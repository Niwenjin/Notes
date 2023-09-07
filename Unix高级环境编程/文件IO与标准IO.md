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