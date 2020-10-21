# 第3章 文件I/O

## 习题

### 3.1
``` C    
#include "apue.h"
struct Node{
	int fd;
	struct Node *next;
};
void del_fd_my(struct Node *head) {
	while(NULL != head){
		close(head->fd);
		printf("close file description %d\n", head->fd);
		struct Node *temp = head->next;
		free(head);
		head = temp;
	}
}
int my_dup2(int fd1, int fd2) {
	if(fd1 == fd2){
		return fd2;
	}
	close(fd2);
	struct Node *dummyHead = (struct Node *) malloc(sizeof(struct Node));
	int fd_temp;
	while((fd_temp = dup(fd1)) != fd2) {
		if(-1 == fd_temp){
			del_fd_my(dummyHead->next);
			return -1;
		}
		printf("create file description %d\n", fd_temp);
		struct Node *temp = (struct Node *) malloc(sizeof(struct Node));
		temp->fd = fd_temp;
		temp->next = dummyHead->next;
		dummyHead->next = temp;
	}
	del_fd_my(dummyHead->next);
	return fd2;
}
int main() {
	int fd2 = my_dup2(STDOUT_FILENO, 15);	
	write(15, "Hello World!\n", 13);
	exit(0);
}
```
