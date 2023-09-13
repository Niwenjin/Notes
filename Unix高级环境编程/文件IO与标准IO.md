# 文件IO与标准IO
## 文件IO
### 文件描述符
文件描述符fd是一个非负整数，本质上是一个索引值。当打开一个文件时，内核向进程返回一个文件描述符，IO函数对文件进行操作时，将其作为参数传入IO函数，用文件描述符来标识该文件。
### IO函数
#### 基本IO函数
打开文件  
`int open(const char *path, int aflag, .../*mode_t mode*/);`    
`int openat(int fd, const char *path, int aflag, .../*mode_t mode*/);`  
创建文件  
`int creat(const char * pathname, mode_tmode);`  
关闭文件  
`int close(int fd);`  
改变偏移量  
`off_t lseek(int fildes, off_t offset, int whence);`  
读文件  
`ssize_t read(int fd, void * buf , size_t count);`  
写文件  
`ssize_t write (int fd, const void * buf, size_t count);`  
#### 文件共享
复制文件描述符  
`int dup (int oldfd);`  
`int dup2(int odlfd,int newfd);`  
#### IO同步
将修改过的数据块缓冲区放入到设备写队列  
`int sync(void);`  
将fd对应的文件数据写入磁盘  
`int fsync(int fd);`    
`int fdatasync(int fd);        // 不更新文件属性`  
#### 其他函数
改变已经打开文件的属性  
`int fcntl(int fd, int cmd, ...);`     
杂物箱  
`int ioctl(int fd, int request, ...);`
## 文件和目录
### 文件属性
获取文件属性stat  
`int stat(const char *restrict pathname, struct stat *restrict buf);`  
`int fstat(int fd, struct stat *buf);`  
`int lstat(const char *restrict pathname, struct stat *restrict buf);`  
`int fstatat(int fd, const char *restrict pathname, struct stat *restrict buf, int flag);`  
stat结构体定义
```
struct stat {
    mode_t     st_mode;       //文件类型、模式和权限
    ino_t      st_ino;        //i节点编号
    dev_t      st_dev;        //设备号码
    dev_t      st_rdev;       //特殊设备号码
    nlink_t    st_nlink;      //文件的硬链接数
    uid_t      st_uid;        //文件所有者
    gid_t      st_gid;        //文件所有者对应的组
    off_t      st_size;       //文件长度（单位：字节）
    time_t     st_atime;      //文件最后被访问的时间
    time_t     st_mtime;      //文件最后被修改的时间
    time_t     st_ctime;      //i节点状态最后改变时间
    blksize_t  st_blksize;    //文件内容对应的块大小
    blkcnt_t   st_blocks;     //文件内容对应的块数量
};
```
#### 文件类型（包含在st_mode中）
* 普通文件
* 目录文件
* 块特殊文件
* 字符特殊文件
* FIFO
* 套接字
* 符号链接
#### 文件访问权限（包含在st_mode中） 
9个访问权限位
* 用户读、写、执行
* 组读、写、执行
* 其他读、写、执行  

访问权限测试  
`int access(const char *pathname, int mode);`  
`int faccessat(int fd, const char* pathname, int mode, int flag);`  
创建屏蔽字  
`mode_t umask(mode_t cmask);`  
访问权限更改  
`int chmod(const char *pathname, mode_t mode);`  
`int fchmod(int fd, mode_t mode);`  
`int fchmodat(int fd, const char* pathname, mode_t mode, int flag);`
#### 黏着位（包含在st_mode中）
如果对目录设置了黏着位，只有满足下列条件之一的用户，才能删除或重命名此目录下的文件。
* 拥有此文件
* 拥有此目录
* 是超级用户
#### 设备号
st_dev记录文件的主、次设备号信息
* 主设备号标识设备驱动程序
* 次设备号标识特定的子设备
  
st_rdev记录字符特殊文件和块特殊文件的设备号
#### 用户ID、组ID（st_uid，st_gid）  
更改文件的用户ID和组ID  
`int chown(const char *pathname, uid_t owner, gid_t group);`  
`int fchown(int fd, uid_t owner, gid_t group);`  
`int fchownat(int fd, const char *pathname, uid_t   owner, gid_t group, int flag);`  
`int lchown(const char *pathname, uid_t owner, gid_t group);`
#### 文件长度
打开文件时将长度截断为0（指定O_TRUNC）  
`int fd = open(const char *path, O_TRUNC);`   
指定长度截断文件  
`int truncate(const char *pathname, off_t length);`  
`int ftruncate(int fd, off_t length);`
#### 文件中的空洞
当设置的偏移量超过文件尾端，并写入数据后，造成文件空洞。  
空洞使文件长度增加，但是不占用磁盘空间。  
#### 文件时间
更改文件时间  
`int futimens(int fd, const struct timespec times[2]);`  
`int utimensat(int fd, const char *path, const struct timespec times[2], int flag);`  
`int utimes(const char *pathname, const struct timeval time[2]);`
### 文件系统
#### 硬链接
创建硬链接（目录项）  
`int link(const char *existingpath, const char *newpath);`  
`int linkat(int efd, const char *existingpath, int nfd, const char *newpath, int flag);`  
删除目录项  
`int unlink(const char *pathname);`  
`int unlinkat(int fd, const char *pathname, int flag);`  
重命名文件或目录  
`int rename(const char *oldname, const char *newname);`  
`int rename(int oldfd, const char *oldname, int newfd, const char *newname);`  
#### 符号链接
优点：可跨分区建立，可对目录建立  
创建符号链接  
`int symlink(const char *actualpath, const char *sympath);`  
`int symlinkat(const char *actualpath, int fd, const char *sympath);`  
读取符号链接  
`ssize_t readlink(const char *restrict pathname, char *restrict buf, size_t bufsize);`  
`ssize_t readlinkat(int fd, const char *restrict pathname, char *restrict buf, size_t bufsize);`  
### 目录
创建目录  
`int mkdir(const char *pathname, mode_t mode);`  
`int mkdirat(int fd, const char *pathname, mode_t mode);`  
删除空目录  
`int rmdir(const char *pathname);`  
改变当前工作目录  
`int chdir(const char *pathname);`  
`int fchdir(int fd);`  
获取工作目录的绝对路径  
`char *getcwd(char *buf, size_t size);`  
打开目录流  
`DIR *opendir(const char *pathname);`  
`DIR *fdopendir(int fd);`  
读取目录流  
`struct dirent *readdir(DIR *dp);`  
重置目录流读取位置  
`void rewinddir(DIR *dp);`  
获取目录流位置  
`long telldir(DIR *dp);`  
设置目录流位置  
`void seekdir(DIR *dp,long loc);`  
关闭目录流  
`int closedir(DIR *dp);`
## 标准IO
### 流和FILE对象
流的定向  
```
int fwide(FILE *fp, int mode);
// mode为正，指定流为宽定向
// mode为负，指定流为字节定向
// mode为0，不指定流定向，返回该流定向的值
```
标准输入、标准输出和标准错误  
```
// 头文件<stdio.h>预定义了3个流
// 通过文件指针stdin, stdout, stderr加以引用
```
打开流
```
#include <stdio.h>
FILE *fopen(const char *restrict pathname, const char *restrict type);
// 打开路径为pathname的文件
FILE *freopen(const char *restrict pathname, const char *restrict type, FILE *restrict fp);
// 在一个指定的流上打开一个文件
FILE *fdopen(int fd, const char *type);
// 取已有的描述符，使一个标准IO流与其相结合
```
获取文件描述符
```
int fileno(FILE *fp);
```
### 读和写流
输入函数
```
#include <stdio.h>
int getc(FILE *fp);
int fgetc(FILE *fp);   // getc可被实现为宏，fgetc不能
int getchar(void);     // getchar等于getc(stdin)

char *fgets(char *restrict buf, int n, FILE *restrict fp); // 读入一行到缓冲区
char *gets(char *buf)  // 弃用
```
输入结束
```
#include <stdio.h>
int ferror(FILE *fp);    // 判断出错
int feof(FILE *fp);      // 判断到达尾端
void clearerr(FILE *fp); // 清除错误和EOF标志
```
输出函数
```
#include <stdio.h>
int putc(int c, FILE *fp);
int fputc(int c, FILE *fp);
int putchar(int c);     // 与输入函数相同

int fputs(const char *restrict str, FILE *restrict fp); // 将一个null字节终止的字符串写入到指定的流
int puts(const char *str); // 将字符串写入到标准输出
```
二进制IO
```
#include <stdio.h>
size_t fread(void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp);
size_t fwrite(const void *restrict ptr, size_t size, size_t nobj, FILE *restrict fp);
// size为每个数组元素的长度，nobj为读或写的元素个数
```
### 缓冲
缓冲类型
* 全缓冲：在填满标准IO缓冲区后才进行实际IO操作
* 行缓冲：遇到换行符时才进行IO操作
* 不带缓冲：立即进行IO操作

打开或关闭缓冲
```
#include <stdio.h>

void setbuf(FILE *restrict fp, char *restrict buf);
// 打开时，buf指向缓冲区；关闭时，buf设置为NULL

int setvbuf(FILE *restrict fp, char *restrict buf, int mode, size_t size);
// mode参数设置缓冲类型：
    _IOFBF     全缓冲
    _IOLBF     行缓冲
    _IONBF     不带缓冲
```
强制冲洗流
```
int fflush(FILE *fp);
// 该函数使流中未写的数据被传送至内核
// 若fp设置为NULL，则冲洗所有输出流
```
### 定位流
ftell和fseek
```
#include <stdio.h>
long ftell(FILE *fp);     // 返回当前文件位置
int fseek(FILE *fp, long offset, int whence);
void rewind(FILE *fp);    // 将流设置至起始位置 
```
ftello和fseeko
```
#include <stdio.h>
off_t ftello(FILE *fp);
int fseeko(FILE *fp, off_t offset, int whence);
// 用off_t代替了长整型
```
fgetpos和fsetpos
```
// 移植到非Unix系统的程序应使用fgetpos和fsetpos
int fgetpos(FILE *restrict fp, fpos_t *restrict pos);
int fsetpos(FILE *fp, const fpos_t pos); 
```
### 格式化IO
格式化输出
```
#include <stdio.h>
int printf(const char *restrict format, ...);
int fprintf(FILE *restrict fp, const char *restrict format, ...);
int dprintf(int fd, const char *restrict format, ...);
int sprintf(char *restrict buf, const char *restrict format, ...);
int snprintf(char *restrict buf, size_t n, const char *restrict format, ...);
```
格式化输入
```
#include <stdio.h>
int scanf(const char *restrict format, ...);
int fscanf(FILE *restrict fp, const char *restrict format, ...);
int sscanf(const char *restrict buf, const char *restrict format, ...);
```
### 临时文件
创建一个临时文件
```
#include <stdio.h>
char *tmpnam(char *ptr);    // 返回指向唯一路径名的指针
FILE *tmpfile(void);        // 返回文件指针
```
```
#include <stdlib.h>
char *mkdtemp(char *template);  // 创建一个临时目录
int mkstemp(char *template);    // 创建一个临时文件
```
### 内存流
创建内存流
```
#include <stdio.h>
FILE *fmemopen(void *restrict buf, size_t size, const char *restrict type);
```
```
#include <stdio.h>
FILE *open_memstream(char **bufp, size_t *sizep);
```
```
#include <wchar.h>
FILE *open_wmemstream(wchar_t **bufp, size_t *sizep);
```
## 系统数据文件和信息
### 口令文件
口令文件位置  
`/etc/passwd`  
口令结构
```
struct passwd {
	char   *pw_name;       /* username */
	char   *pw_passwd;     /* user password */
	uid_t   pw_uid;        /* user ID */
	gid_t   pw_gid;        /* group ID */
	char   *pw_gecos;      /* real name */
	char   *pw_dir;        /* home directory */
	char   *pw_shell;      /* shell program */
};
```
查看口令文件项
```
#include <pwd.h>
// 通过uid或用户名查看对应记录项
struct passwd *getpwuid(uid_t uid);
struct passwd *getpwnam(const char *name);
```
查看整个口令文件
```
#include <pwd.h>
// 每次调用查看下一条记录项
struct passwd *getpwent(void);
void setpwent(void);    // 反绕文件
void endpwent(void);    // 关闭口令文件
```
阴影口令
```
// 加密口令存放在阴影口令文件/etc/shadow
struct spwd {
    char *sp_namp;       /* 用户登录名 */
    char *sp_pwdp;       /* 加密口令 */
    long int sp_lstchg;  /* 上次更改口令以来的时间 */
    long int sp_min;     /* 进过多少天后可以更改 */
    long int sp_max;     /* 要求更改的剩余天数 */
    long int sp_warn;    /* 到期警告的天数 */
    long int sp_inact;   /* 账户不活动之前尚余天数 */
    long int sp_expire;  /* 账户到期天数 */
    unsigned long int sp_flag; /* 保留 */
};
```
访问阴影口令
```
#include <shadow.h>
// 与访问口令函数类似
struct spwd *getspnam(const char *name);
struct spwd *getspent(void);
void setspent(void);
void endspent(void);
```
### 组文件
group结构
```
#include <sys/types.h>
#include <grp.h>
struct group
{
  char *gr_name;  				/* 组名 */
  char *gr_passwd;  			/* 密码 */
  __gid_t gr_gid;  				/* 组ID */
  char **gr_mem;  				/* 组成员名单 */
}
```
访问组结构
```
#include <grp.h>
struct group *getgrgid(gid_t gid);
struct group *getgrnam(const char *name);

struct group *getgrent(void);
void setgrent(void);
void endgrent(void);
```
获取组ID
```
#include <unistd.h>
int getgroups(int getgidsize, gid_t grouplist[]);
// 将进程所属用户各附属组ID填入grouplist中
```
设置组ID
```
#include <grp.h>
int setgroups(int ngroups, const gid_t grouplist[]);
int initgroups(const char *username, gid_t basegid);
```
### 登录记录
utmp文件记录当前登录系统的各个用户
```
struct utmp
{
  char ut_line[8];  			/* 设备名 */
  char ut_name[8];  			/* 用户名 */
  long ut_time;
}
```
### 系统标识
返回主机和操作系统有关信息
```
#include <sys/utsname.h>
int uname(struct utsname *name);
```
utsname结构
```
struct utsname
{
    char sysname[];        //当前操作系统名
    char nodename[];       //网络上的名称
    char release[];        //当前发布级别
    char version[];        //当前发布版本
    char machine[];        //当前硬件体系类型
}
```
### 时间和日期
获取当前时间
```
#include <time.h>
time_t time(time_t *calptr);
```
获取指定时钟的时间
```
#include <sys/time.h>
int clock_gettime(clockid_t clock_id, struct timespec *tsp);
int clock_getres(clockid_t clock_id, struct timespec *tsp);  // 将timespec结构初始化为clock_id对应的精度
```
对特定的时钟设定时间
```
#include <sys/time.h>
int clock_settime(clockid_t clock_id, struct timespec *tsp);
```
分解时间格式tm
```
struct tm {
    int tm_sec;       /* 秒 – 取值区间为[0,59] */
    int tm_min;       /* 分 - 取值区间为[0,59] */
    int tm_hour;      /* 时 - 取值区间为[0,23] */
    int tm_mday;      /* 一个月中的日期 */
    int tm_mon;       /* 月份（0代表一月）  */
    int tm_year;      /* 年份，实际年份减去1900 */
    int tm_wday;      /* 星期 – 取值区间为[0,6] */
    int tm_yday;      /* 从每年的1月1日开始的天数 */
    int tm_isdst;     /* 夏令时标识符 */
};
```
日历时间和分解时间的转换
```
#include <time.h>
struct tm *gmtime(const time_t *calptr);
struct tm *localtime(const time_t *calptr);
time_t mktime(struct tm *tmptr);
```
格式化输出时间
```
#include <time.h>
size_t strftime(char *str, size_t maxsize, const char *format, const struct tm *timeptr)
size_t strftime_l(char *strDest, size_t maxsize, const char *format, const struct tm *timeptr,  locale_t locale);
```