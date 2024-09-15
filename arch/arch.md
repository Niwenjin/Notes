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
