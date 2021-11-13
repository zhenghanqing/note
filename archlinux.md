# 安装

联网

```
iwctl
```

如果无设备，检查网卡是否被阻塞

```
rfkill
rfkill unblock all
```

更新时间

```bash
timedatectl set-ntp true 
```

删除旧分区

```bash
# 查看所有分区,lsblk也可以
fdisk -l 

# 选择硬盘
fdisk /dev/nvme0n1

# d删除分区，w保存退出
```

分区

```bash
cfdisk /dev/nvme0n1
# 3个区
# type:efi system
512M
# type:linux swap 
8G
# type:linux filesystem
剩余所有

# 所有操作完成后write，再quit退出 
```

对各分区格式化

```bash
mkfs.fat -F32 /dev/nvme0n1p1

mkfs.ext4 /dev/nvme0n1p3
# 完成后选择y

mkswap /dev/p2
swapon /dev/nvme0n1p2
```

挂载

```bash
# 挂载到根目录
mount /dev/nvme0n1p3 /mnt 
# 挂载引导
mkdir /mnt/boot
mount /dev/nvme0n1p1 /mnt/boot
```

 修改镜像源

```bash
vim /etc/pacman.d/mirrorlist

# 添加
Server = https://mirrors.tuna.tsinghua.edu.cn/archlinux/$repo/os/$arch 
Server = https://mirrors.zju.edu.cn/archlinux/$repo/os/$arch
```

安装

```
pacstrap /mnt base linux linux-firmware base-devel dhcpcd vim networkmanager 
```

生成fastb文件

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

切换到安装的系统

```bash
arch-chroot /mnt
```

设置时区和同步时间

```bash
ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
hwclock --systohc
```

中文

```bash
vim /etc/locale.gen
# 取消注释
en_US.UTF-8 UTF-8
zh_CN.UTF-8 UTF-8
```

生成locale

```bash
locale-gen 

vim /etc/locale.conf
# 新增
LANG=en_US.UTF-8
```

设置网络

```bash
echo "Lenovo1998" >> /etc/hostname

vim /etc/hosts
# 新增
127.0.0.1	localhost
::1		localhost
127.0.1.1	Lenovo1998.localdomain	Lenovo1998
```

设置root密码

```bash
passwd
```

驱动

```
pacman -S amd-ucode
```

GRUB引导

```bash
pacman -S grub efibootmgr

# 生成grub efi
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB 
grub-mkconfig -o /boot/grub/grub.cfg
```

笔记本单独安装

```
pacman -S iw wpa_supplicant dialog netctl
```

启动服务

```bash
systemctl enable dhcpcd
exit

umount -R /mnt 手动卸载被挂载的分区
```

重启

```bash
systemctl start  NetworkManager
systemctl enable NetworkManager
systemctl start  dhcpcd
systamctl enable dhcpcd
```

创建用户

```bash
# 创建新用户
useradd -m -g users -s /bin/bash zhq

# 为新用户设置密码
passwd zhq

# 为新用户配置sudo权限
#vim /etc/sudoers root下添加zhq..all
```

打开设备

```
rfkill
```

联网

```bash
# 查看网络设备与状态
nmcli device 

# 显示wifi
nmcli device wifi list 

# 联网
nmcli device wifi connect WIFI名 password password
```

解决屏幕亮度问题

```bash
sudo pacman -S acpilight 
sudo gpasswd video -a zhq
sudo gpasswd video -a root

# 获得当前亮度
xbacklight -get
# 设置亮度
xbacklight -set 70
# 增加亮度
xbacklight -inc 10
# 降低亮度
xbacklight -dec 1
```

安装

```bash
# 驱动
pacman -S xf86-video-ati
# 安装X窗口系统
pacman -S xorg
# 安装触摸板驱动 
pacman -S xf86-**input**-synaptics
# 安装常用字体
pacman -S ttf-dejavu wqy-zenhei wqy-microhei    
# 安装plasma元软件
pacman -S plasma
pacman -S kde-applications  
pacman -S sddm sddm-kcm
```

启动服务

```bash
su
systemctl enable NetworkManager      
systemctl enable sddm 
systemctl enable dhcpcd 
```

配置arch源

```bash
vim /etc/pacman.conf 
# 文件末尾添加以下两行
[archlinuxcn] 
Server = https://mirrors.bfsu.edu.cn/archlinuxcn/$arch

# 取消注释
[multilib] 
Include = /etc/pacman.d/mirrorlist

# 生成key
sudo pacman -Syy sudo pacman -S archlinuxcn-keyring  
```

# 软件

yay国内镜像

```
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save
```

蓝牙

```bash
sudo pacman -S bluez
sudo pacman -S bluez-utils
sudo systemctl start bluetooth.service
sudo systemctl enable bluetooth.service
sudo pacman -S pulseaudio-bluetooth
sudo pacman -S pavucontrol
# 若音箱没有播放，执行pavucontrol选择设备。

# 如果还是不行
sudo vim /etc/bluetooth/main.conf
在 [General] 下添加Enable = Source,Sink,Media,Socket
```

通用

```shell
# 安装yay
sudo pacman -S yay

# 安装vim
sudo pacman -S vim

# 安装网易云音乐
yay -S netease-cloud-music

# v2ray
sudo pacman -Syy qv2ray
sudo pacman -Syy  v2ray

# wps
yay -S wps-office-cn
yay -S wps-office-mui-zh-cn ttf-wps-fonts

# 网盘
yay -S baidunetdisk
# 备用
yay -S baidunetdisk-electron

# latte-dock
yay -S latte-dock

# chrome
yay -S google-chrome

# VSCode
yay -S visual-studio-code-bin
```

zsh

```bash
yay -S zsh
chsh -s /bin/zsh
yay -S oh-my-zsh-git
cp /usr/share/oh-my-zsh/zshrc ~/.zshrc
yay -S autojump
yay -S zsh-syntax-highlighting zsh-autosuggestions

# 设置软链接
sudo ln -s /usr/share/zsh/plugins/zsh-syntax-highlighting /usr/share/oh-my-zsh/custom/plugins/
sudo ln -s /usr/share/zsh/plugins/zsh-autosuggestions /usr/share/oh-my-zsh/custom/plugins/

# vim ～/.zshrc
# 主题
ZSH_THEME="agnoster"

# 插件
plugins=(
git
autojump
zsh-syntax-highlighting
zsh-autosuggestions
)
```

vmware

```bash
# 确定内核
uname -r
yay linux-headers
sudo pacman -S fuse2 gtkmm linux-headers pcsclite libcanberra
yay -S ncurses5-compat-libs
yay -S vmware-workstation

# 启动服务
sudo systemctl start vmware-networks.service  vmware-usbarbitrator.service vmware-hostd.service    
sudo systemctl enable vmware-networks.service  vmware-usbarbitrator.service vmware-hostd.service                   
sudo modprobe -a vmw_vmci vmmon
sudo modprobe vmnet
sudo vmware-networks --start
```

jdk

```bash
# 下载tar.gz包：https://repo.huaweicloud.com/java/jdk/
sudo mkdir /usr/local/java
sudo mv xxx /usr/local/java
tar -zxvf xxx.tar.gz
sudo vim /etc/profile 

# 新增
export JAVA_HOME=/usr/local/java/jdk1.8.0_201
export JRE_HOME=${JAVA_HOME}/jre
export CLASSPATH=.:${JAVA_HOME}/lib:${JRE_HOME}/lib:$CLASSPATH
export JAVA_PATH=${JAVA_HOME}/bin:${JRE_HOME}/bin
export PATH=$PATH:${JAVA_PATH}:$MAVEN_HOME/bin
export MAVEN_HOME=/opt/maven/apache-maven-3.5.4

# 启用 
source /etc/profile
```

maven

```bash
# 下载tar.gz包
mkdir /usr/local/java/maven
tar -zxvf xxx-bin.tar.gz
vim /etc/profile

# 修改本地仓库配置
vim settings.xml

## 1、本地仓库
<localRepository>/usr/local/java/maven/myStorage</localRepository>

## 2、阿里云镜像
<mirror>
      <id>alimaven</id>
      <name>aliyun maven</name>
      <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
      <mirrorOf>central</mirrorOf>        
</mirror>

# 启用 
source /etc/profile
```

intellij IDE

```bash
# 下载tar.gz包和授权
sudo mkdir /usr/local/intellij
sudo tar -zxvf xxx.tar.gz -C /usr/local/intellij
sudo chmod a=+rx bin/idea.sh

# 启动安装
bin/idea.sh
# 注：勾选Create a desktop entry for integration with system application menu 和 for all users

# -javaagent:/usr/intellij/jetbrains-agent.jar
```

docker

```bash
sudo pacman -Ss docker
sudo systemctl enable docker.service
sudo systemctl start docker.service
sudo gpasswd -a $USER docker
reboot

vim /etc/docker/daemon.json
# 新增docker镜像加速
{
    "registry-mirrors": ["https://docker.mirrors.ustc.edu.cn"]
}

sudo systemctl daemon-reload
sudo systemctl restart docker.service

# 验证安装是否成功
docker info
```

向日葵

```
yay -S sunloginclient
systemctl start runsunloginclient
```

xmind

```
yay -S xmind-2020
```



docker

```
docker pull elasticsearch:7.12.0
docker pull mobz/elasticsearch-head:5
docker pull kibana:7.12.0
```

