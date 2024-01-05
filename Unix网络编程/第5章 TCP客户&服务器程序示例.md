# 第5章 TCP客户&服务器程序示例
## TCP回射服务器程序
```
#include	"unp.h"

int
main(int argc, char **argv)
{
	int					listenfd, connfd;
	pid_t				childpid;
	socklen_t			clilen;
	struct sockaddr_in	cliaddr, servaddr;

	// 创建一个TCP套接字，绑定服务器的端口
    listenfd = Socket(AF_INET, SOCK_STREAM, 0);

	bzero(&servaddr, sizeof(servaddr));
	servaddr.sin_family      = AF_INET;
	servaddr.sin_addr.s_addr = htonl(INADDR_ANY);
	servaddr.sin_port        = htons(SERV_PORT);

	Bind(listenfd, (SA *) &servaddr, sizeof(servaddr));

	Listen(listenfd, LISTENQ);

	for ( ; ; ) {
		// 服务阻塞于accept调用，等待客户完成连接
        clilen = sizeof(cliaddr);
		connfd = Accept(listenfd, (SA *) &cliaddr, &clilen);

		// 并发服务器，为每个客户派生一个子进程
        if ( (childpid = Fork()) == 0) {	/* child process */
			Close(listenfd);	/* close listening socket */
			// 回射函数
            str_echo(connfd);	/* process the request */
			exit(0);
		}
		Close(connfd);			/* parent closes connected socket */
	}
}

// 读入缓冲区并回射其中内容
void
str_echo(int sockfd)
{
	ssize_t		n;
	char		buf[MAXLINE];

again:
	// read函数从套接字读入数据，writen函数把内容回射给客户
    while ( (n = read(sockfd, buf, MAXLINE)) > 0)
		Writen(sockfd, buf, n);

	// 如果客户关闭连接，返回0，函数退出；否则循环
    if (n < 0 && errno == EINTR)
		goto again;
	else if (n < 0)
		err_sys("str_echo: read error");
}
```
## TCP回射客户程序
```
#include	"unp.h"

int
main(int argc, char **argv)
{
	int					sockfd;
	struct sockaddr_in	servaddr;

	if (argc != 2)
		err_quit("usage: tcpcli <IPaddress>");

	// 创建TCP套接字，用服务器IP地址和端口号装填网际网套接字地址结构
    sockfd = Socket(AF_INET, SOCK_STREAM, 0);

	bzero(&servaddr, sizeof(servaddr));
	servaddr.sin_family = AF_INET;
	servaddr.sin_port = htons(SERV_PORT);
	Inet_pton(AF_INET, argv[1], &servaddr.sin_addr);

	// 连接到服务器
    Connect(sockfd, (SA *) &servaddr, sizeof(servaddr));

	// 处理回射工作
    str_cli(stdin, sockfd);		/* do it all */

	exit(0);
}

// TCP回射函数
void
str_cli(FILE *fp, int sockfd)
{
	char	sendline[MAXLINE], recvline[MAXLINE];

	// 每次读入一行，直到遇到文件结束符
    while (Fgets(sendline, MAXLINE, fp) != NULL) {

		// 发送给服务器
        Writen(sockfd, sendline, strlen(sendline));

		// 从服务器读到回射行，并写到标准输出
        if (Readline(sockfd, recvline, MAXLINE) == 0)
			err_quit("str_cli: server terminated prematurely");

		Fputs(recvline, stdout);
	}
}
```
## 处理僵死进程
注意事项
1. 当fork子进程时，必须捕获SIGCHLD信号
2. 当捕获信号时，必须处理被中断的系统调用
3. SIGCHLD的信号处理函数必须正确编写，应使用waitpid函数以免留下僵死进程