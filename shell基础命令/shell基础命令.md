# shell基础命令  
## bash基本语法
> `$ command [-options] [FILE/section/...] {destination}`
## powershell常用命令
> * ping
> > `$ ping "host"` 验证与主机间的连接
> * ssh
> > `$ ssh "host"` 登录远程主机  
> > `$ ssh -l "user" "host"` 指定用户登录主机  
> > `$ ssh -T "host"` 测试连接
> * scp
> > `$ scp "source" "destination"` 复制文件到远程主机
> * ipconfig
> > `$ ipconfig ` 显示ip配置
## coreutils命令  
> * arch 
> > `$ arch` 显示主机架构
> * b2sum
> > `$ b2sum "filename"` 计算并检查BLAKE2校验和
> * base32
> > `$ base32 [-d] "filename"` 编码或解码到Base32并输出
> * base64
> > `$ base64 [-d] "filename"` 编码或解码到base64并输出
> * basename
> > `$ basename "filepath/filename"` 获取末尾目录名或文件名
> * basenc
> > `$ basenc [-option] "filename"`以指定选项编码或解码文件并输出
> * cat
> > `$ cat "filename"` 连接并显示文本文件  
> > `$ cat >> "filename"` 输入保存到文本文件
> * chcon
> > `$ chcon -R -t "user" "filename"` 将文件向用户共享
> * chgrp
> > `$ chgrp "group" "filename"` 修改文件或目录的所属群组
> * chmod
> > `$ chmod u+x "filename"` 文件所有者增加执行权限  
> > `$ chmod 755 "filename"` 改变文件权限
> > > 三个数字代表所有者、同组用户、其他用户的权限数值相加  
> > > 执行1；写2；读4
> * chown
> > `$ chown "owner:group" "filename"` 改变文件或目录的所有者和群组
> * chroot
> > `$ chroot "rootpath" {command}` 改变根目录并在新根目录下执行命令
> * cksum
> > `$ cksum "filename"` 检查文件的CRC校验和
> * comm
> > `$ comm "file1" "file2"` 比较两个文件的内容
> * cp
> > `$ cp [-option] "source" "destination"` 复制文件或目录到目标地址
> > > `-a`=`-dpr`  
> > > `-s` 符号连接  
> > > `-l` 硬连接  
> > > `-p` 保留源文件或目录和属性   
> > > `-r` 递归目录下子目录和文件
> * csplit
> > `$ csplit "FILE" "PATTEN"` 将文件分割并输出到xx00、xx01...中
> > > `$ csplit a 3 5` 将文件a分割为三个部分，第二部分从第3行开始，第三部分从第5行开始
> * cut
> > `$cut [-option] "FILE"` 从一个文本文件或者文本流中提取文本列
> > > `-b` 以字节为单位分割  
> > > `-c` 以字符为单位分割  
> > > `-d` 设置分割字符  
> > > `-f` 分割后取出第几段  
> * date
> > `$ date` 显示日期时间
> * dd
> > `$ dd if="input" of="output" bs="块大小" conv="关键字"` 读取并转换文件
> * df
> > `$ df` 显示文件系统的磁盘使用情况
> * dir
> > `$ dir` 显示目录下文件和目录
> > > ls展示目录时，目录和可执行文件会用颜色区分，dir则不会
> * dircolors
> > `$ dircolors [色彩配置文件]` 设置ls在显示目录或文件时所用的色彩
> * dirname
> > `$ dirname "dirpath"` 除去最后一级路径
> * du
> > `$ du "dirname/filename"` 显示文件或目录占用的空间 
> * echo
> > `$ echo -e "text\n" > "filename"` 输出文字到控制台/文件...
> * env
> > `$ env` 显示环境变量  
> > `$ env -u var` 删除指定的环境变量  
> > `$ env var=""` 设置环境变量
> * expand
> > `$ expand "FILE"` 将文件的制表符转换为空格，并输出到标准输出
> * expr
> > `$ expr 10 + 10` 整数运算  
> > `$ expr s1 = s2` 判断数值或字符串是否相等，若相等返回1
> * factor
> > `$ factor n` 分解质因数
> * false
> > `$ false` 无事发生
> * fmt
> > `$ fmt -w 50 "FILE"` 按指定格式重排文本
> * fold
> > `$ flod -b 100 "FILE"` 按列宽重排文本
> * groups
> > `$ groups root` 显示用户所在的群组
> * head
> > `$ head -n 'x' "FILE"` 显示文本的前x行
> * hostid
> > `$ hostid` 显示当前主机的16进制标识
> * hostname
> > `$ hostid` 显示当前主机的主机名
> * id
> > `$ id` 显示用户和群组的id
> * install
> > `$ install -g "group" -o "user" "source" "dest"` 复制文件并指定它的群组和用户
> * join
> > `$ join FILE1 FILE2` 将两个文件中指定字段的内容相同的行连接起来
> * kill
> > `$ kill 12345` 结束指定编号的进程
> * link
> > `$ link "FILE" "filename"` 为文件创建一个硬链接
> * ln
> > `$ ln "FILE" "filename"` 为文件建立一个硬链接  
> > `$ ln -s "FILE" "filename"` 为文件建立一个符号链接
> * logname
> > `$ logname"` 显示用户的登录名
> * ls
> > `$ ls` 查看当前目录下文件  
> > `$ ls -l"` 查看完整的文件信息  
> > `$ ln -a"` 查看隐藏文件
> * md5sum
> > `$ md5sum FILE` 查看文件的MD5加密和
> * mkdir
> > `$ mkdir "dirname"` 建立目录  
> > `$ mkdir -p "dir1/dir2/dir3"` 建立多级目录
> * mkfifo
> > `$ mkfifo "pipe"` 创建命名管道
> * mknod
> > `$ mknod "name"` 创建块设备或字符设备文件
> * mktemp
> > `$ mktemp -d "dir" tmp.XXX` 在指定目录下创建临时文件
> * mv
> > `$ mv "source" "dest"` 重命名或移动文件  
> > `$ mv -b "source" "dest"` 若覆盖文件，则进行备份
> * nice
> > `$ nice` 查看进程优先级  
> > `$ nice -n -20 command &` 设置后台程序运行优先级
> > > 优先级为-20~19，-20最高，19最低
> * nl
> > `$ nl FILE` 列出文件行号  
> > `$ nl -b a FILE` 列出文件行号，包括空行
> * nohup
> > `$ nohup command &` 不挂起运行程序，输出将写入nohup.out
> * nproc
> > `$ nproc` 显示可用的处理器数目
> * numfmt
> > `$ numfmt --to=iec 2048` 格式化数字并输出
> * od
> > `$ od -tx FILE` 以十六进制输出文件
> * paste
> > `$ paste FILE1 FILE2` 将多个文件合并并输出  
> > `$ paste -s FILE` 将一个文件的多行合并为一行
> * pathchk
> > `$ pathchk "path"` 检查路径是否可移植
> * pinky
> > `$ pinky` 输出登录用户
> * pr
> > `$ pr FILE` 将文件转换为打印格式
> > > `$ -h "title"` 设置标题  
> > > `$ -l n` 设置每页的行数
> * printenv
> > `$ printenv` 显示所有环境变量
> * printf
> > `$ printf %s\n a b c...` 格式化输出文本
> * ptx
> > `$ ptx FILE` 生成序列改变的索引
> * pwd
> > `$ pwd` 显示当前目录路径
> * readlink
> > `$ readlink -f FILE` 显示符号链接指向的位置
> * realpath
> > `$ realpath FILE` 显示文件的绝对路径
> * rm
> > `$ rm FILE` 删除文件  
> > `$ rm -r "dir"` 删除目录及子文件
> * rmdir
> > `$ rmdir -p dir1/dir2` 删除多级空目录
> * runcon
> > `$ runcon CONTEXT COMMAND` 以给定的上下文执行命令
> * seq
> > `$ seq n` 顺序输出一列数字
> * sha1sum
> > `$sha1sum FILE` 生成sha1sum校验和  
> > `$sha1sum -c FILE.sha1` 检验校验和
> * sha224sum 
> > 同上 
> * sha256sum
> > 同上
> * sha384sum
> > 同上
> * sha512sum
> > 同上
> * shred
> > `$ shred FILE` 覆写文件（彻底删除）
> * shuf
> > `$ shuf -i 0-10 -n 4 -o FILE` 生成4个0~10的伪随机数输出到文件
> * sleep
> > `$ sleep 120` 睡眠120秒
> * sort
> > `$ sort [-option] FILE` 对文件按行升序排序
> > > `-u` 去除重复行  
> > > `-r` 降序排序  
> > > `-o FILE` 将排序结果保存到文件  
> > > `-n` 按数值排序  
> > > `-t ':'` 设置分隔符:  
> > > `-k 'x'` 按第x列排序
> * split
> > `$ split [-option] FILE` 分割文件为多个小文件
> > > `-b` 设置子文件大小（字节）  
> > > `-C` 子文件单行最大字节数  
> > > `-l` 指定多少行分割成一个小文件
> * stat
> > `$ stat FILE` 显示文件状态
> * stdbuf
> > `$ stdbuf [-option] COMMAND` 调整标准缓冲区并执行命令
> > > `-i` 调整输入流缓冲区  
> > > `-o` 调整输出流缓冲区  
> > > `-e` 调整错误流缓冲区
> * stty
> > `$ stty -a` 打印所有终端设置
> * sum
> > `$ sum FILE` 计算文件的校验和和占用磁盘块数
> * sync
> > `$ sync FILE` 将缓存数据写入文件
> * tac
> > `$ tac FILE` 按行反向输出文件
> * tail
> > `$ tail -n 'x' FILE` 显示文本的后x行
> * tee
> > `$ echo "hello" | tee FILE` 读取标准输入写入标准输出
> * test
> > `$ test 表达式` 检查条件是否成立
> * timeout
> > `$ timeout 'time' COMMAND` 在指定时间后终止命令
> * touch
> > `$ touch -a FILE` 改变文件的存取时间
> * tr
> > `$ tr -c -d -s "string1" "string2" FILE` 替换文件中的字符串string1为string2
> > > `-c` 使用string1的补集替换  
> > > `-d` 删除string1输入字符  
> > > `-s` 删除重复字符串
> * true
> > `$ true` 无事发生
> * truncate
> > `$ truncate -s 'size' FILE` 设置文件为指定的大小（如果设置字节数小于文件大小，数据会丢失）
> * tsort
> > `$ tsort FILE` 对文件中的数据进行拓扑排序
> * tty
> > `$ tty` 显示标准输入设备的文件路径和名称
> * uname
> > `$ uname -a` 显示系统信息
> * unexpand
> > `$ unexpand FILE` 将空格转换为制表符
> * uniq
> > `$ uniq -c FILE` 删除文件中重复的行，并在行首添加重复次数
> > > 重复的行不相邻时，uniq不起作用，通常先使用sort
> * unlink
> > `$ unlink FILE` 删除文件
> * uptime
> >`$ uptime` 显示系统运行的时间
> * users
> > `$ users` 显示最近登录主机的用户名
> * vdir
> > `$ vidr` 类似 `$ls -l`
> * wc
> > `$ wc [-option] FILE` 计算文件的长度  
> > > `-c` 字节数  
> > > `-l` 行数  
> > > `-w` 单词数  
> > > `-m` 字符数
> * who
> > `$ who` 显示登录的用户和登录时间
> * whoami
> > `$ whoami` 显示当前用户名
> * yes
> > `$ yes` 输出一串y直到中断
## 常用命令
> * grep
> * gawk
> > `$ gawk '{print $n}' FILE` 输出文本每行的第n个字段
> > > `-F` 指定分隔符
> * find
> * sed
> * Rust替代命令
> > * ripgrep
> > * fd-find