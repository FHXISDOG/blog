---
title: "Arch安装记录"
date: 2020-04-10T10:26:30+08:00
draft: false
categories: ["linux"]
tags: ["Arch","linux"]
---

>  本文记录本人安装Arch的过程以及后续配置,不做为教程,只作为备忘使用,仅供参考

## 前期准备

- [下载镜像](https://www.archlinux.org/download/)
- 下载[Rufus](https://rufus.ie/)
- 由于我的电脑支持`efi`启动,制作`efi/gpt`格式的启动盘

## 系统安装

### 开机U盘启动

不同品牌型号的电脑,进入U盘启动的方式不一样,我这里的是`F12`选择U盘启动

### 启动安装环境

`MBR`:

![](/img/ArchInstall/MBR_START.gif)

`UEFI`:

![](/img/ArchInstall/UEFI_START.gif)

当屏幕上出现命令行提示符及闪烁的光标时即启动完毕。

![](/img/ArchInstall/start_orver.gif)

### 检查引导方式

```shell
ls /sys/firmware/efi/efivars
```

如果提示:

```shell
如果提示
```

表明你是以`BIOS`方式引导，否则为以`EFI`方式引导

### 联网

我这里是插入网线了,所以执行命令:

```sheell
dhcpcd
```

如果你是wifi,请执行:

```shell
wifi-menu
```

### 更新系统时间

```shell
timedatectl set-ntp true
```

### 分区与格式化

> 警告：请谨慎操作以防数据丢失

**必要**的分区如下：

- Arch Linux 要求至少一个分区分配给根目录 /。

- 在 UEFI 系统上，需要一个 UEFI 系统分区。

#### 查看分区:

```shell
fdisk -l
```

输出:

```shell
DISK /dev/nvme0n1    xxxxxxxxxxxxx
/dev/nvme0n1p1   xxx
/dev/nvme0n1p2   xxx
```

输出像“Disk /dev/nvme0n1 xx GiB ...”这种的，xx也就是该存储设备的总容量，然后你判断哪个设备是有多余空间的。假如该设备是`/dev/sda`，这时候你还可以通过如下命令查看该存储设备下已有的分区情况：

```shell
fdisk -l /dev/nvme0n1
```

#### 格式化硬盘:(我硬盘没有重要数据所以格式化,如果你硬盘有数据,不要执行此命令!!!!!!!!!!!!)

输入:

```shell
fdisk /dev/nvme0n1

d    #这里省略输出,有几个分区执行几次,如果显示错误,提示硬盘已没有分区证明完成

y

w    #写入格式化结果,也可以不写入,下一步不执行fdisk /dev/nvme0n1就行,然后和新的分区结果一起写入
```

#### 分区

##### 如果你是`MBR`,只需要一个分区用于系统安装的分区:

1. 创建根分区:

```shell
fdisk /dev/nvme0n1
```

2. o #输入o来创建一个全新的MBR分区表。

3. n #输入n创建一个新的分区，首先会让你选择起始扇区，一般直接回车使用默认数值即可，然后可以输入结束扇区或是分区大小，如果我们想要使创建的分区完全占满空闲的空间，可以直接回车使用默认结束扇区。
   
   > 这里我的输入 `p`查看,我的引导分区名是:`/dev/nvme0n1p1`,所以我的引导分区是`/dev/nvme0n1p1`

4. 输入`w`来将之前所有的操作写入磁盘生效，在这之前可以输入`p`来确认自己的分区表没有错误。

5. 输入以下命令来格式化刚刚创建的根分区：
   
   ```shell
   mkfs.ext4 /dev/nvme0n1p1     #请将的sdxY替换为刚创建的分区,例如我的是 mkfs.ext4 /dev/nvme0n1p1
   ```

##### 如果你是EFI模式，需要分两个分区：

- 第一个分区用于系统引导（512M）
- 第二个分区用于系统安装（49G）

操作步骤:

###### 创建引导分区

1.执行命令:

```shell
fdisk /dev/nvme0n1 #自己的磁盘根据情况变化,名称可能不一样
```

2.输入`n`创建一个新的分区，首先会让你选择起始扇区，一般直接回车使用默认数值即可，然后可以输入结束扇区或是分区大小，这里我们输入`+512M`来创建一个512M的引导分区。

```shell
Command (? for help): n
Partition number (1-128, default 1):    ##直接回车

First sector (34-10485726, default = 2048) or {+-}size{KMGTP}:  #直接回车

Last sector (2048-10485726, default = 10485726) or {+-}size{KMGTP}: +512M

Current type is 'Linux filesystem'
Hex code or GUID (L to show codes, Enter = 8300): ef00
Changed type of partition to 'EFI System'
```

3.这时我们可以输入`p`来查看新创建的分区。

4.输入`t`并选择新创建的分区序号来更改分区的类型，输入`l`可以查看所有支持的类型，输入`ef`更改分区的类型为`EFI`。(这里我输入的是1)

> 这里我的输入 `p`查看,我的引导分区名是:`/dev/nvme0n1p1`,所以我的引导分区是`/dev/nvme0n1p1`

###### 创建根分区:

1. 输入n

2. 一路回车

> 这里我的输入 `p`查看,我的引导分区名是:`/dev/nvme0n1p2`,所以我的分区是`/dev/nvme0n1p2`

###### 写入:

输入`w`写入,退出`fdisk`

###### 格式化分区:

```shell
mkfs.fat -F32 /dev/nvme0n1p1 #请将nvme0n1p1替换为刚创建的引导分区 这里我的分区名是 /dev/nvme0n1p1


mkfs.ext4 /dev/nvme0n1p2     #请将的nvme0n1p2替换为刚创建的分区 这里我的分区名是`/dev/nvme0n1p2`
```

#### 挂载分区

- 如果你是`MBR`格式启动,只需挂在根分区即可:

```shell
mount /dev/nvme0n1p1 /mnt #这里是MBR的根分区,注意别搞混了,上面的efi中 /dev/nvme0n1p1是引导分区
```

- 如果你是`EFI`启动,需要挂载启动分区和根分区

```shell
mount /dev/nvme0n1p2 /mnt #挂在根分区
mkdir /mnt/boot
mount /dev/sdxY /mnt/boot #上面的efi中 /dev/nvme0n1p1是引导分区
```

#### 选择镜像源

编辑` /etc/pacman.d/mirrorlist`,变更为:

```shell
Server = http://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch
Server = http://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
```

#### 安装基本包

下面就要安装最基本的`ArchLinux`包到磁盘上了。这是一个联网下载并安装的过程。

执行以下命令：

```shell
pacstrap /mnt base base-devel linux linux-firmware dhcpcd
```

根据下载速度的不同在这里需要等待一段时间，当命令提示符重新出现的时候就可以进行下一步操作了。

#### 配置Fstab

生成自动挂载分区的`fstab`文件，执行以下命令：

```shell
genfstab -L /mnt >> /mnt/etc/fstab
```

由于这步比较重要，所以我们需要输出生成的文件来检查是否正确，执行以下命令：

```shell
cat /mnt/etc/fstab
```

#### Chroot

`Chroot`意为`Change root`，相当于把操纵权交给我们新安装（或已经存在）的`Linux`系统，**执行了这步以后，我们的操作都相当于在磁盘上新装的系统中进行**。

执行如下命令：

```shell
arch-chroot /mnt
```

这里顺便说一下，如果以后我们的系统出现了问题，只要插入U盘并启动， 将我们的系统根分区挂载到了`/mnt`下（如果有`efi`分区也要挂载到`/mnt/boot`下），再通过这条命令就可以进入我们的系统进行修复操作。

#### 设置时区

依次执行如下命令设置我们的时区为上海并生成相关文件：

```shell
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

#### 提前安装必须软件包

因为我们现在已经`Chroot`到了新的系统中，只有一些最基本的包（组件），这时候我们就需要自己安装新的包了，下面就要介绍一下`ArchLinux`下非常强大的包管理工具`pacman`，大部分情况下，一行命令就可以搞定包与依赖的问题。

安装包的命令格式为`pacman -S 包名`，`pacman`会自动检查这个包所需要的其他包（即为依赖）并一起装上。下面我们就通过`pacman`来安装一些包，这些包在之后会用上，在这里先提前装好。

执行如下命令（注意大小写，大小写错误会导致包无法找到）：

```shell
pacman -S vim dialog wpa_supplicant  networkmanager
```

#### 设置Locale

设置我们使用的语言选项，编辑`/etc/locale.gen`文件,在文件中找到`zh_CN.UTF-8 UTF-8`  `zh_HK.UTF-8 UTF-8`  `zh_TW.UTF-8 UTF-8`  `en_US.UTF-8 UTF-8`这四行，去掉行首的#号.

然后执行：

```shell
locale-gen
```

打开（不存在时会创建）`/etc/locale.conf`文件：\

```shell
vim /etc/locale.conf
```

在文件的第一行加入以下内容：

```shell
LANG=en_US.UTF-8
```

#### 设置主机名

打开（不存在时会创建）`/etc/hostname`文件：

```shell
vim /etc/hostname
```

在文件的第一行输入你自己设定的一个`myhostname`,我这里输入的是`Arch`

编辑`/etc/hosts`文件：

```shell
vim /etc/hosts
```

在文件末添加如下内容（将`Arch`替换成你自己设定的主机名）:

```shell
127.0.0.1    localhost
::1        localhost
127.0.1.1    Arch.localdomain    Arch
```

#### 设置Root密码

`Root`是`Linux`中具有最高权限帐户，有些敏感的操作必须通过`Root`用户进行，比如使用`pacman`，我们之前进行所有的操作也都是以`Root`用户进行的，也正是因为`Root`的权限过高，如果使用不当会造成安全问题，所以我们之后会新建一个普通用户来进行日常的操作。在这里我们需要为`Root`帐户设置一个密码：

执行如下命令：

```shell
passwd
```

按提示设置并确认就可以了。

#### 安装`Intel-ucode`（非`Intel`CPU可以跳过此步骤）

```shell
pacman -S intel-ucode
```

#### 安装`Bootloader`

##### `systemd bootctl`方式(仅`efi`引导方式可用):

1. 安装`bootctl`:

```shell
 bootctl install
```

2. 编辑`/boot/loader/loader.conf`(没有则新建):

```shell
default 
archtimeout 4
```

3. `vim`编辑`/boot/loader/entries/arch.conf`(没有则新建):

```shell
title   Arch Linux
linux   /vmlinuz-linux
initrd  /initramfs-linux.img
options root=
```

棘手的一步来了，最优解来自新加坡程序员Kai Hendry：

**esc键退出insert模式，打 :r !blkid**

将blkid读取到 你文件里，**找到/dev/nvmen01p2(就是你根分区)里面的PARTUUID, 然后将其拼凑出如下的格式。**:

```text
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=PARTUUID=12345-67890-1234-123456-7890 rw
```

##### `grub`方式

###### 如果为BIOS/MBR引导方式：

- 安装`grub`包：

```shell
pacman -S grub
```

- 部署`grub`：

```shell
grub-install --target=i386-pc /dev/nvme0n1 （将sdx换成你安装的硬盘）
```

注意这里的`sdx`应该为硬盘（例如`/dev/sda`），**而不是**形如`/dev/sda1`这样的分区。

- 生成配置文件：

- ```shell
  grub-mkconfig -o /boot/grub/grub.cfg
  ```

**如果你没有看到成功的提示信息，请仔细检查是否正确完成上面的过程。常见问题如下：**

1.如果报`warning failed to connect to lvmetad，falling back to device scanning.`错误。参照[wiki](https://wiki.archlinux.org/index.php/Install_from_existing_Linux)中搜索关键词`use_lvmetad`所在位置，简单的方法是编辑`/etc/lvm/lvm.conf`这个文件，找到`use_lvmetad = 1`将`1`修改为`0`，保存，重新配置grub。

###### 如果为EFI/GPT引导方式：

- 安装`grub`与`efibootmgr`两个包：

```shell
pacman -S grub efibootmgr
```

- 部署`grub`：

```shell
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=grub
```

- 生成配置文件：

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

**提示信息应与上面的类似，如果你发现错误，请仔细检查是否正确完成上面的过程。**

如果报`warning failed to connect to lvmetad，falling back to device scanning.`错误。参照[这篇文章](https://www.pckr.co.uk/arch-grub-mkconfig-lvmetad-failures-inside-chroot-install/)，简单的方法是编辑`/etc/lvm/lvm.conf`这个文件，找到`use_lvmetad = 1`将`1`修改为`0`，保存，重新配置grub

如果报`grub-probe: error: cannot find a GRUB drive for /dev/sdb1, check your device.map`类似错误，并且`sdb1`这个地方是你的u盘，这是u盘`uefi`分区造成的错误，对我们的正常安装没有影响，可以不用理会这条错误。

##### 安装后检查

**如果你是多系统，请注意上面一节中对`os-prober`这个包的安装。**

**强烈建议使用如下命令检查是否成功生成各系统的入口，如果没有正常生成会出现开机没有系统入口的情况：**

```shell
vim /boot/grub/grub.cfg
```

检查接近末尾的`menuentry`部分是否有`windows`或其他系统名入口。下图例子中是`Arch Linux`入口与检测到的`windows10`入口（安装在`/dev/sda1`)

**如果你没有看到`Arch Linux`系统入口或者该文件不存在**，请先检查`/boot`目录是否正确部署`linux`内核：

```shell
cd /boot
ls
```

查看是否有`initramfs-linux-fallback.img initramfs-linux.img intel-ucode.img vmlinuz-linux`这几个文件，如果都没有，说明`linux`内核没有被正确部署，很有可能是`/boot`目录没有被正确挂载导致的，确认`/boot`目录无误后，可以重新部署`linux`内核：

```shell
pacman -S linux
```

再重新生成配置文件，就可以找到系统入口。

**如果你已经安装`os-prober`包并生成配置文件后还是没有生成其他系统的入口**：

**你目前处的U盘安装环境下有可能无法检测到其他系统的入口，请在下一步中重启登陆之后重新运行**：

```shell
grub-mkconfig -o /boot/grub/grub.cfg
```

#### 重启

接下来，你需要进行重启来启动已经安装好的系统，执行如下命令：

```shell
exit
```

**如果挂载了`/mnt/boot`，先`umount /mnt/boot`，再`umount /mnt`，否则直接`umount /mnt`：**

```shell
umount /mnt/bootumount /mntreboot
```

注意这个时候你可能会卡在有两行提示的地方无法正常关机，长按电源键强制关机即可，没有影响。

## 安装后基本配置

### 连接网络

现在我们是在新安装的系统上进行操作，所以我们要重新联网，我们在之前安装系统时已经提前装好了相关的包。所以现在只要跟之前一样：

- 如果你是有线网并且路由器支持DHCP的话插上网线后先执行以下命令获取IP地址：

```shell
dhcpcd
```

- 无线网:

```shell
wifi-menu
```

### 创建交换文件

交换文件可以在物理内存不足的时候将部分内存暂存到交换文件中，避免系统由于内存不足而完全停止工作。

之前我们通常采用单独一个分区的方式作为交换分区，现在更推荐采用交换文件的方式，更便于我们的管理。

分配一块空间用于交换文件，执行：

```shell
fallocate -l 512M /swapfile （请将512M换成需要的大小，只能以M或G为单位）
```

交换文件的大小可以自己决定，推荐4G以下的物理内存，交换文件与物理内存一致，4G以上的物理内存，交换文件4-8G。

更改权限，执行：

```shell
chmod 600 /swapfile
```

设置交换文件，执行：

```shell
mkswap /swapfile
```

启用交换文件，执行：

```shell
swapon /swapfile
```

最后我们需要编辑`/etc/fstab`为交换文件设置一个入口，使用`vim`打开文件：

```shell
vim /etc/fstab
```

**注意编辑`fstab`文件的时候要格外注意不要修改之前的内容，直接在最后新起一行加入以下内容**：

```shell
/swapfile none swap defaults 0 0
```

### 新建用户

在这之前所有操作都是以`root`用户的身份进行的，由于`root`的权限过高，日常使用`root`用户是不安全的。`Linux`为我们提供了强大的用户与组的权限管理，提高了整个系统的安全性。这里我们就来新建一个用户。

执行以下命令来创建一个名为`username`的用户（请自行替换`username`为你的用户名）：

```shell
useradd -m -G wheel username （请自行替换username为你的用户名）
```

在这里稍微解释一下各参数的含义：

`-m`：在创建时同时在`/home`目录下创建一个与用户名同名的文件夹，这个目录就是你的**家目录**啦！家目录有一个别名是`~`，你可以在任何地方使用`~`来代替家目录路径。这个神奇的目录将会用于存放你所有的个人资料、配置文件等所有跟系统本身无关的资料。这种设定带来了诸多优点：

- 只要家目录不变，你重装系统后只需要重新安装一下软件包（它们一般不存放在家目录），然后所有的配置都会从家目录中读取，完全不用重新设置软件着。
- 你可以在家目录不变的情况下更换你的发行版而不用重新配置你的环境。
- 切换用户后所有的设置会从新的用户的家目录中读取，将不同用户的资料与软件设置等完全隔离。
- 有些著名的配置文件比如`vim`的配置文件`~/.vimrc`，只要根据自己的使用习惯配置一次， 在另一个`Linux`系统下（例如你的服务器）把这个文件复制到家目录下，就可以完全恢复你的配置。

`-G wheel`：`-G`代表把用户加入一个组，对用户与组的概念感兴趣的同学可以自行查找有关资料学习。后面跟着的`wheel`就是加入的组名，至于为什么要加入这个组，后面会提到。

当然记得为新用户设置一个密码，执行如下命令：

```shell
passwd username （请自行替换username为你的用户名）  
```

根据提示输入两次密码就可以了，注意，这是你的用户密码，推荐与之前设置的`root`用户的密码不同。

### 配置sudo

我们已经创建好了一个新的用户，以后我们将会使用这个用户来登录，那么如果我们需要执行一些只有`root`用户才能执行的命令（例如修改系统文件、安装软件包）怎么办？当然我们可以通过:

```shell
su
```

命令来切换到`root`用户执行命令后再通过:

```shell
exit
```

返回普通用户。

但是`sudo`为我们提供了一个更快捷的办法，使用`sudo`，我们只要在需要`root`权权限执行的命令之前加上`sudo`就可以了，例如安装软件包：

```shell
sudo pacman -S something
```

下面我们就来安装并配置`sudo`。

`sudo`本身也是一个软件包，所以我们需要通过`pacman`来安装：

```shell
pacman -S sudo
```

接下来我们需要用专门的`visudo`命令来编辑`sudo`的配置文件：

```shell
visudo
```

实际上就是`vim`的操作，使用它是为了对编辑后的文件进行检查防止格式的错误。

找到:

```shell
# %wheel ALL=(ALL)ALL
```

这行，去掉之前的`#`注释符，保存并退出就可以了。

这里的`%wheel`就是代表`wheel`组，意味着`wheel`组中的所有用户都可以使用`sudo`命令。

当然为了安全使用`sudo`命令还是需要输入**当前用户**的密码的。

配置好`sudo`以后，我们进行一次重启，执行：

```shell
reboot
```

来重启你的电脑。

重启以后输入你**刚创建的用户名与密码**来登录。注意登录后要重新进行联网操作。

### 图形界面的安装

#### 安装集显:

```shell
sudo pacman -S xf86-video-intel
```

#### 安装`Xorg`,桌面环境(我这里安装的是`KDE`),`SDDM`

```shell
sudo pacman -S xorg plasma kde-applications sddm
```

#### 设置开机启动服务和网络

```shell
sudo systemctl enable sddm #设置开机启动sddm

sudo systemctl disable netctl #配置网络

sudo systemctl enable NetworkManager #注意大小写
sudo pacman -S network-manager-applet #安装工具栏工具来显示网络设置图标
```

重启后即可进入桌面环境

#### 设置中国源

升级系统到最新  

```html
sudo pacman -Syyu
```

编辑`/etc/pacman.conf`配置源:

```textile
[multilib]

Include = /etc/pacman.d/mirrorlist

[archlinuxcn]

Server = https://mirrors.ustc.edu.cn/archlinuxcn/$arch


#或者：

[archlinuxcn]
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinuxcn/$arch
```

安装 archlinuxcn-keyring 导入 GPG key:

```shell
sudo pacman -S archlinuxcn-keyring 直接这样会出错
sudo pacman -Syu haveged
sudo systemctl start haveged
sudo systemctl enable haveged
sudo rm -fr /etc/pacman.d/gnupg
sudo pacman-key --init
sudo pacman-key --populate archlinux
sudo pacman-key --populate archlinuxcn
sudo pacman -S archlinuxcn-keyring
sudo pacman -Syu
```

#### 安装中文字体

```shell
sudo pacman -S adobe-source-han-sans-cn-fonts (思源黑体)

sudo pacman -S ttf-dejavu

sudo pacman -S wqy-zenhei

sudo pacman -S wqy-microhei
```

编辑 `~/.xprofile`或`~/.xinitrc`:

```shell
export LANG=zh_CN.UTF-8
export LANGUAGE=zh_CN:en_US
```

重启即可

## 参考连接:

> [Archlinux从安装到基本配置]([https://www.joxrays.com/archlinux-configure/](https://www.joxrays.com/archlinux-configure/))
> 
> [以官方Wiki的方式安装ArchLinux]([https://www.viseator.com/2017/05/17/arch_install/](https://www.viseator.com/2017/05/17/arch_install/))
> 
> [ArchLinux安装后的必须配置与图形界面安装教程]([https://www.viseator.com/2017/05/19/arch_setup/](https://www.viseator.com/2017/05/19/arch_setup/))
> 
> [Arch Linux 安装指南[2019.12.01]](https://bbs.archlinuxcn.org/viewtopic.php?id=1037)
> 
> [女生程序员教你15分钟安装Arch Linux](https://zhuanlan.zhihu.com/p/113615452)
