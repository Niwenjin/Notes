# 标准IO
## 流和FILE对象
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
## 读和写流
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
## 缓冲
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
## 定位流
ftell和fseek
```
#include <stdio.h>
long ftell(FILE *fp);     // 返回当前文件位置
int fseek(FILE *fp, long offset, int whence);
/* whence: 
SEEK_SET： 文件开头
SEEK_CUR： 当前位置
SEEK_END： 文件结尾 */

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
## 格式化IO
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
## 临时文件
创建一个临时文件
```
#include <stdio.h>
char *tmpnam(char *ptr);    // 返回指向唯一路径名的指针
FILE *tmpfile(void);        // 返回匿名文件指针
```
```
#include <stdlib.h>
char *mkdtemp(char *template);  // 创建一个临时目录
int mkstemp(char *template);    // 创建一个临时文件
```
## 内存流
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