# 文件IO
## 文件描述符
文件描述符fd是一个非负整数，本质上是一个索引值。当打开一个文件时，内核向进程返回一个文件描述符，IO函数对文件进行操作时，将其作为参数传入IO函数，用文件描述符来标识该文件。
## IO函数
### 基本IO函数
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
### 文件共享
复制文件描述符  
`int dup (int oldfd);`  
`int dup2(int odlfd,int newfd);`  
### IO同步
将修改过的数据块缓冲区放入到设备写队列  
`int sync(void);`  
将fd对应的文件数据写入磁盘  
`int fsync(int fd);`    
`int fdatasync(int fd);        // 不更新文件属性`  
### 其他函数
改变已经打开文件的属性  
`int fcntl(int fd, int cmd, ...);`     
杂物箱  
`int ioctl(int fd, int request, ...);`