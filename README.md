# APUE3
## 环境配置 Linux安装apue.3e(基于Ubuntu20.04)
1. 安装静态链接库
``` 
sudo apt-get install libbsd-dev
```
2. 下载无编译错误的apue(参考的是 https://blog.csdn.net/ninesnow_c/article/details/107428736 这篇博客）
```
git clone https://github.com/hangxin001/my_study_tools.git
cd my_study_tools/apue.3e
```
3. make
```
make
```
4. 在编译成功的基础上，我们进行安装apue.h文件及其对应的静态链接库libapue.a（这一步参考的 https://www.cnblogs.com/lesroad/p/9738020.html 这篇博客）
```
cp ./include/apue.h /usr/include/
cp ./lib/libapue.a /usr/local/lib/
```

5. 创建apueerror.h文件
``` C
/**************
 *
 *apueerror.h
 *
 *************/
#include <apue.h>
#include <stdio.h>
#include <errno.h> /* for definition of errno */
 
#include <stdarg.h> /* ISO C variable aruments */
 
static void err_doit(int, int, const char *, va_list);
/*
 * Nonfatal error related to a system call.
 * Print a message and return.
 */
void err_ret(const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
}
 
 
/*
 * Fatal error related to a system call.
 * Print a message and terminate.
 */
void err_sys(const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
    exit(1);
}
 
 
/*
 * Fatal error unrelated to a system call.
 * Error code passed as explict parameter.
 * Print a message and terminate.
 */
void err_exit(int error, const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(1, error, fmt, ap);
    va_end(ap);
    exit(1);
}
 
 
/*
 * Fatal error related to a system call.
 * Print a message, dump core, and terminate.
 */
void err_dump(const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(1, errno, fmt, ap);
    va_end(ap);
    abort(); /* dump core and terminate */
    exit(1); /* shouldn't get here */
}
 
 
/*
 * Nonfatal error unrelated to a system call.
 * Print a message and return.
 */
void err_msg(const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(0, 0, fmt, ap);
    va_end(ap);
}
 
 
/*
 * Fatal error unrelated to a system call.
 * Print a message and terminate.
 */
void err_quit(const char *fmt, ...)
{
    va_list ap;
 
    va_start(ap, fmt);
    err_doit(0, 0, fmt, ap);
    va_end(ap);
    exit(1);
}
 
 
/*
 * Print a message and return to caller.
 * Caller specifies "errnoflag".
 */
static void err_doit(int errnoflag, int error, const char *fmt, va_list ap)
{
    char buf[MAXLINE];
   vsnprintf(buf, MAXLINE, fmt, ap);
   if (errnoflag)
       snprintf(buf+strlen(buf), MAXLINE-strlen(buf), ": %s",
         strerror(error));
   strcat(buf, "\n");
   fflush(stdout); /* in case stdout and stderr are the same */
   fputs(buf, stderr);
   fflush(NULL); /* flushes all stdio output streams */
}
```
6. 把创建的apueerror.h文件粘贴到/usr/include下
```
cp apueerror.h /usr/include
```

7. 之后我们写的代码中，`#include "apue.h"` 的下一行要增加 `#include "apueerror.h"`
