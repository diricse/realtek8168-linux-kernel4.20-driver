# realtek8168-linux-kernel4.20-driver
realtek8168 driver for linux kernel4.20<br>

linux kernel4.20已经发布 centos7的内核还是3.10的版本<br>

如何将centos7的内核升级到最新的linux kernel4.20呢？<br>

最简单的方法是使用yum升级内核<br>
可是升级后发现网卡不能工作，无法联网。原来是自带的r8169网卡驱动在4.20下无法正确工作。<br>


于是，折腾开始，google很久也无法解决<br>

后来看到realtek提供有针对4.15后的内核的更新驱动程序，但是无法直接下载要通过邮件发送下载链接<br>

是不是很麻烦？<br>

为了方便广大信徒，我将该下载放到这里，方便大家下载，顺便告诉各位我的内核升级办法<br>

#### 1、创建文件夹<br>
mkdir /data

#### 2、下载内核源码<br>
cd /data 
wget https://cdn.kernel.org/pub/linux/kernel/v4.x/linux-4.20.12.tar.xz <br>

#### 3、解压源码<br>

tar xvf linux-4.20.12.tar.xz <br>

#### 4、准备配置文件<br>

cp /boot/config-3.10.0-862.el7.x86_64  .config

#### 5、配置内核选项 如果不熟悉就使用默认，跳过此步<br>

make menuconfig <br>

#### 6、安装编译依赖软件<br>
yum -y install gcc gcc-c++ glibc glibc-devel pcre pcre-devel openssl openssl-devel systemd-devel zlib-devel vim lrzsz tree screen  lsoftcpdump wget ntpdate net-tools iotop bc zip unzip ncurses-devel elfutils-libelf-devel bison flex <br>

#### 7、编译 我的老机器只有2核所以是 -j 2 如果你土豪金是16核那么可以 -j16,编译耗时非常长，建议慢慢的喝茶等待<br>

make -j 2 && echo -e '\a'

#### 8、安装<br>

make modules_install&&make install

菩萨保佑，一切顺利，安装成功后，在/boot下有个vmlinuz-4.20.12文件

#### 9、更新启动菜单<br>

在centos7下的update-grub命令是：
grub2-mkconfig -o /boot/grub2/grub.cfg 

#### 10、重启机器，选则编译的内核进入系统<br>
reboot

#### 11、编译安装r8168网卡驱动<br>

重启后网卡已经无法工作了，所有只有在本地登录操作<br>

解压网卡驱动<br>
tar xjvf r8168.tar.bz2<br>
cd r8168-8.046.0<br>
./autorun.sh<br>

驱动自动编译安装好了<br>
ping网关通了<br>
可以远程登录了<br>

如果你不嫌弃这个自己编译的内核没有在yum管理下，那么就此ok<br>

如果你觉得这个不在yum管理下的内核很不爽，那么请你接着向下读<br>

#### 12、在CentOS 7上启用 ELRepo 仓库<br>
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org <br>
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-3.el7.elrepo.noarch.rpm<br>

#### 13、列出可用的内核相关包：<br>
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available<br>

#### 14、，安装最新的主线稳定内核：<br>

yum --enablerepo=elrepo-kernel install kernel-ml kernel-ml-devel kernel-ml-headers kernel-ml-tools<br>

安装后请不要删除其它版本内核<br>

#### 15、更新一下启动菜单<br>

grub2-mkconfig -o /boot/grub2/grub.cfg<br>

#### 16、重启进入rpm安装的内核系统<br>

reboot<br>

又无法上网了，所以只有在本机登录操作<br>

#### 17、安装r8168网卡驱动<br>

cd r8168-8.046.0<br>
./autorun.sh<br>

命令成功执行，网络连接又好了<br>

#### 18、列出kernel<br>

rpm -qa | grep kernel<br>

#### 19、删除不要的低版本kernel与自己编译的kernel<br>

rpm -e kernel***    这里*请输入具体的列出的kernel后的内容<br>
rm  vmlinuz-4.20.12 <br>

#### 20、再次更新下启动菜单<br>
grub2-mkconfig -o /boot/grub2/grub.cfg<br>

#### 21、重启进入rpm安装的4.20内核，大功告成！


本人做码农将近20年了，非常喜欢交朋友<br>
我的QQ：2456568249  回答:github <br>
my twitter: https://twitter.com/QinBerlin





