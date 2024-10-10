# Windows 配置

## WSL

### WSL 的安装和迁移

```powershell
# 查看可用的发行版
wsl --list --online

# 安装
wsl.exe --install <Distro>

# 导出
wsl --export Ubuntu D:\Linux\Ubuntu\Ubuntu.tar

# 注销
wsl --unregister Ubuntu

# 导入
wsl --import Ubuntu D:\Linux\Ubuntu\ D:\Linux\Ubuntu\Ubuntu.tar --version 2

# 修改默认用户
# 如 Ubuntu-22.04 的可执行文件名为 Ubuntu2204.exe
ubuntu.exe config --default-user niwenjin

# 若没有对应可执行程序，在/etc/wsl.conf文件中添加：
[user]
default = niwenjin
```

### 设置免 sudo 密码

`sudo visudo`打开 sudo 文件

在底下添加一行`niwenjin ALL=(ALL) NOPASSWD:ALL`

## WMWare

### 虚拟机的安装

略。

### SSH 连接到虚拟机

**VMWare 虚拟网卡配置**

在 VMWare Workstation 中：

右键虚拟机 → 设置 → 网络适配器 → 选择 NAT 模式

**安装 openssh**

在虚拟机中执行：

```bash
sudo apt install net-tools openssh-client openssh-server
sudo /etc/init.d/ssh restart
```

**查询虚拟机 IP**

在虚拟机中执行：

```bash
ifconfig
```

找到 eth33 下的 IPv4 地址。

**设置端口转发**

在 VMWare Workstation 中：

菜单栏 → 编辑 → 虚拟网络编辑器 → VMNet8 → NAT 设置 → 添加端口转发

填写：

-   主机端口：22
-   IP 地址：上一步查到的
-   虚拟机端口：22

**开放防火墙**

在虚拟机中执行：

```bash
sudo ufw allow ssh
```

**密钥对的生成和传递**

在宿主机（Windows）执行：

```power shell
ssh-keygen  # 已有公钥可跳过此步
cat ~/.ssh/id_rsa.pub | ssh 虚拟机用户名@虚拟机IP "cat >> ~/.ssh/authorized_keys"
```

_注意：如果连接失败，查看`C:\Users\Jim Ni\.ssh\known_host`文件，删除对应 IP 地址的那一行。_

## oh-my-posh

[oh-my-posh](https://ohmyposh.dev/docs/)是 Windows PowerShell 的一款插件，提供了终端美化、提示等功能。

安装 oh-my-posh

```sh
winget install JanDeDobbeleer.OhMyPosh -s winget
```

查看配置文件路径：

```sh
$PROFILE
> C:\Users\13199\Documents\WindowsPowerShell\Microsoft.PowerShell_profile.ps1
```

在上述路径创建对应的配置文件，输入自定义配置：

```txt
C:\\Users\\13199\\AppData\\Local\\Programs\\oh-my-posh\\bin\\oh-my-posh.exe init pwsh --config $env:POSH_THEMES_PATH\M365Princess.omp.json | Invoke-Expression
```

其中，主题文件位置在`C:\Users\13199\AppData\Local\Programs\oh-my-posh\themes`中，可以在官方文档中预览。
