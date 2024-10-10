# Arch Linux

## 包管理器

Paru是yaourt的一个现代化替代品，旨在提供更快、更稳定且功能丰富的AUR包管理体验。它基于yay进行改进，增加了许多新特性和优化，使得管理Arch Linux系统中的自定义软件库变得更加方便。

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

-   zsh -autosuggestions  
    zsh-autosuggestions 是一个命令提示插件，当你输入命令时，会自动推测你可能需要输入的命令，按下右键可以快速采用建议。
    `git clone https://github.com/zsh-users/zsh-autosuggestions ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions`
-   zsh-completions
    `git clone https://github.com/zsh-users/zsh-completions ${ZSH_CUSTOM:-${ZSH:-~/.oh-my-zsh}/custom}/plugins/zsh-completions`
-   zsh-syntax-highlighting
    zsh-syntax-highlighting 是一个命令语法校验插件，在输入命令的过程中，若指令不合法，则指令显示为红色，若指令合法就会显示为绿色。
    `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting`
-   z
    oh-my-zsh 内置了 z 插件。z 是一个文件夹快捷跳转插件，对于曾经跳转过的目录，只需要输入最终目标文件夹名称，就可以快速跳转，避免再输入长串路径，提高切换文件夹的效率。
-   extract
    oh-my-zsh 内置了 extract 插件。extract 用于解压任何压缩文件，不必根据压缩文件的后缀名来记忆压缩软件。使用 x 命令即可解压文件。
-   web-search
    oh-my-zsh 内置了 web-search 插件。web-search 能让我们在命令行中使用搜索引擎进行搜索。使用搜索引擎关键字+搜索内容 即可自动打开浏览器进行搜索。

启用插件

```sh
# 修改~/.zshrc
plugins=(git zsh-autosuggestions zsh-completions zsh-syntax-highlighting z extract web-search)
```

## 数据备份和恢复

rsync 可用于备份数据。

用 rsync 备份系统到 USB 驱动器：

```sh
$ sudo rsync -aAXv --delete --exclude=/dev/ --exclude=/proc/ --exclude=/sys/ --exclude=/tmp/ --exclude=/run/ --exclude=/mnt/ --exclude=/media/ --exclude="swapfile" --exclude="lost+found" --exclude=".cache" --exclude="Downloads" --exclude="Documents" --exclude="miniconda3" --exclude="Proj" --exclude="Pictures" --exclude="Music" --exclude=".VirtualBoxVMs"--exclude=".ecryptfs" / /run/media/younis/younisx
```

选项：

```sh
-A 是指示操作系统保留访问控制列表的选项，

-X标志用于保持安全、系统、可信和用户属性，

而 -v 是您用来获取备份进度的标志。

–A、-a 和 –X 标志一起通过维护文件的属性来保护文件的完整性。

然后是–delete选项，它指示仅备份目标中尚未存在的那些文件（在我们的例子中是USB）。–delete 在使用时应采取充分的预防措施，因为更新版本源中的文件替换（覆盖）目标中的旧版本。

–dry-run 选项将所有这些保留在模拟中。

-exclude 标志用于忽略一些要备份的文件夹。在上面的命令中，我省略了 /dev/、/proc/、/proc/ /sys/ /tmp/ /run/ /mnt/ 和 /media 文件夹。这只是为了证明，它们的排除（/mnt/ 除外）是不必要的，因为它们的内容不会由 rsync 自动备份。

/- 指定我们要备份的内容

/run/media/younis/younisx 是您要备份到的目录。
```

备份完成后，可以从移动硬盘恢复系统。

首先，我们将从 Live ISO 和插件启动系统并安装备份驱动器。然后我们将登录并为备份驱动器上的内容创建一个文件夹，并为硬盘上的内容创建另一个文件夹。

```sh
$mkdir /mnt/system /mnt/usb
```

然后查找互连设备的名称：

```sh
$ lsblk
```

通过运行以下命令挂载文件系统和备份：

```sh
$ mount /dev/sda1 /mnt/system

$ mount /dev/sdb1 /mnt/usb
```

然后使用以下命令恢复备份：

```sh
$ rsync -aAXv --delete --exclude="lost+found" /mnt/usb/ /mnt/system/
```
