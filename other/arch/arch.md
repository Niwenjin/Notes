# Arch Linux

## 安装指南

[安装指南](https://wiki.archlinuxcn.org/wiki/%E5%AE%89%E8%A3%85%E6%8C%87%E5%8D%97)

1. 下载 iso 镜像，制作安装介质（U 盘），启动到 live 环境（禁用安全启动）。

2. 选择 _Arch Linux install medium_ 并按`Enter` 进入安装环境。

3. 连接到互联网，可以使用`ip link`检查。联网后会自动更新系统时间。

4. `fdisk -l`查看硬盘驱动器，创建并格式化硬盘分区。

   ```sh
   cfdisk /dev/sda  # 进入磁盘分区
   # 在图形化界面创建EFI分区、swap分区和root分区
   # /dev/sda1 256M EFI System
   # /dev/sda2 4G swap
   # /dev/sda3 Linux filesystem
   mkfs.fat -F 32 /dev/sda1  # EFI分区格式化为fat32
   mkfs.ext4 /dev/sda3  # root分区格式化为ext4（可以选择其他格式）
   mkswap /dev/sda2  # 创建swap分区
   ```

5. 挂载分区。

   ```sh
   mount /dev/sda3 /mnt
   mkdir -p /mnt/boot/efi
   mount /dev/sda1 /mnt/boot/efi
   swapon /dev/sda2
   ```

6. 安装内核和其他软件。

   ```sh
   pacstrap -K /mnt linux-zen linux-firmware linux-zen-headers base base-devel vim git man-db man-pages texinfo
   ```

7. 生成 fstab。

   ```sh
   genfstab -U /mnt >> /mnt/etc/fstab
   ```

8. 进入系统。

   ```
   arch-chroot /mnt
   ```

9. 设置时区和语言。

   ```sh
   ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
   hwclock --systohc

   vim /etc/locale.gen
   # 将以下两行取消注释
   en_US.UTF-8 UTF-8
   zh_CN.UTF-8 UTF-8
   # 保存并退出
   locale-gen
   echo "LANG=en_US.UTF-8" > /etc/locale.conf  # 设置环境变量
   ```

10. 设置主机名和用户名。

    ```sh
    vim /etc/hostname
    # 设置主机名
    archlinux
    # 设置root密码
    passwd
    # 添加用户
    useradd --create-home jim
    passwd jim  # 设置用户密码
    # 设置用户组
    usermod -aG wheel,users,storage,power,lp,adm,optical jim
    # 设置sudo免密码
    visudo
    # 取消注释以下行
    %wheel ALL=(ALL) ALL
    ```

11. 安装并配置 grub。

    ```sh
    pacman -S grub efibootmgr efivar networkmanager intel-ucode/amd-ucode
    grub-install /dev/sda
    grub-mkconfig -o /boot/grub/grub.cfg
    ```

12. 安装 kde 桌面环境。

    ```sh
    pacman -S plasma sddm konsole dolphin kate ark
    ```

13. 启用程序。

    ```sh
    systemctl enable NetworkManager sddm
    ```

14. 重启进入系统。

    ```sh
    exit
    reboot
    ```

## 包管理器

Paru 是 yaourt 的一个现代化替代品，旨在提供更快、更稳定且功能丰富的 AUR 包管理体验。它基于 yay 进行改进，增加了许多新特性和优化，使得管理 Arch Linux 系统中的自定义软件库变得更加方便。

```sh
$ paru
# 更新所有软件包，等效于 paru -Syu
$ paru <packagename>
# 查找软件包
$ paru -S <packagename>
# 安装指定的包
$ paru -c/--clean
# 清理不被需要的包
$ paru -P/--show
# 打印相关选项，常用选项 -s 打印包统计信息和系统运行状况
$ paru -R
# 清除缓存数据，常用选项 -s 移除相关依赖
$ paru -G <packagename>
# 获取包的 PKGBUILD
$ paru --help
# 获取命令行帮助
```

## zsh

安装并设置默认终端

```sh
sudo apt install zsh
# 如果使用vscode集成终端，在vscode中设置
# chsh -s /bin/zsh
```

安装 oh-my-zsh

[oh-my-zsh 官网](http://ohmyz.sh/)

使用 oh-my-zsh 的模板替换~/.zshrc

```sh
cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
```

安装并配置 powerlevel10k 主题

```sh
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
# 在~/.zshrc中修改
ZSH_THEME="powerlevel10k/powerlevel10k"
# 重启终端，会启动引导程序配置p10k
# 或者，手动启动配置程序
p10k configure
```

安装并配置插件

插件推荐：

- zsh -autosuggestions  
  zsh-autosuggestions 是一个命令提示插件，当你输入命令时，会自动推测你可能需要输入的命令，按下右键可以快速采用建议。
  `git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
- zsh-completions
  `git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions`
- zsh-syntax-highlighting
  zsh-syntax-highlighting 是一个命令语法校验插件，在输入命令的过程中，若指令不合法，则指令显示为红色，若指令合法就会显示为绿色。
  `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`
- z
  oh-my-zsh 内置了 z 插件。z 是一个文件夹快捷跳转插件，对于曾经跳转过的目录，只需要输入最终目标文件夹名称，就可以快速跳转，避免再输入长串路径，提高切换文件夹的效率。
- extract
  oh-my-zsh 内置了 extract 插件。extract 用于解压任何压缩文件，不必根据压缩文件的后缀名来记忆压缩软件。使用 x 命令即可解压文件。
- web-search
  oh-my-zsh 内置了 web-search 插件。web-search 能让我们在命令行中使用搜索引擎进行搜索。使用搜索引擎关键字+搜索内容 即可自动打开浏览器进行搜索。

启用插件

```sh
# 修改~/.zshrc
plugins=(git zsh-autosuggestions zsh-completions zsh-syntax-highlighting z extract web-search)
```

## 数据备份和恢复

rsync 可用于备份数据。

用 rsync 备份系统到目标目录：

```sh
$ sudo rsync -aAXHv --delete --exclude='/dev/*' --exclude='/proc/*' --exclude='/sys/*' --exclude='/tmp/*' --exclude='/run/*' --exclude='/mnt/*' --exclude='/media/*' --exclude='/lost+found/' --exclude='Desktop' --exclude='Downloads' --exclude='Music' --exclude='Pictures' --exclude='Public' --exclude='Templates' --exclude='tigo' --exclude='Videos' --exclude='.cache' --exclude='miniconda3' / /path/to/backup
```

选项：

```sh
-A 是指示操作系统保留访问控制列表的选项，

-X标志用于保持安全、系统、可信和用户属性，

而 -v 是您用来获取备份进度的标志。

–A、-a 和 –X 标志一起通过维护文件的属性来保护文件的完整性。

--delete 替换（覆盖）目标中的旧版本。

–dry-run 选项将所有这些保留在模拟中。

-exclude 标志用于忽略一些要备份的文件夹。在上述命令中，/dev、/proc、/sys、/tmp和/run 等目录被包括在内，但这些目录的内容被排除在外。这是因为它们在系统启动时才会被填入内容，但这些目录本身不会被创建。/lost+found 是针对文件系统的。如果计划将系统备份到 /mnt 或 /media 以外的其他位置，请不要忘记将其添加到排除的模式列表中，以避免无限循环。

/- 指定我们要备份的内容

/run/media/younis/younisx 是要备份到的目录。
```

备份完成后，可以从移动硬盘恢复系统。

首先，我们将从 Live ISO 和插件启动系统并安装备份驱动器。然后我们将登录并为备份驱动器上的内容创建一个文件夹，并为硬盘上的内容创建另一个文件夹。

```sh
$mkdir /mnt/root /mnt/backup
```

然后查找互连设备的名称：

```sh
$ lsblk
```

通过运行以下命令挂载文件系统和备份：

```sh
$ mount /dev/sda1 /mnt/root

$ mount /dev/sdb1 /mnt/backup
```

然后使用以下命令恢复备份：

```sh
$ rsync -aAXv --delete --exclude="lost+found" /mnt/backup/ /mnt/root/
```
