# 如何使用虚拟机挂载共享文件
```
## [参考](https://jingyan.baidu.com/article/a3761b2b87d2331577f9aa7e.html)
第一步：

1）VMbox里面编辑共享文件夹路径D:\DongDemo\VM\Ubuntu18\Share 取名Share【随便自己取】第二步：

2）自动挂载+固定分配

第二步：

1）VMboxlinux系统的窗口【不是linux系统里面】：设备-安装增强工具-》安装VBox_GAs_6.0.6

第三步：

1）Ctrl+Alt+T：打开终端

2）linux创建共享文件夹，	

	CMD：	sudo mkdir /mnt/shared

3）linux挂载虚拟机的文件夹到Linux的shared中，

	CMD：	sudo mount -t vboxsf Share /mnt/shared

4) 挂载：
手动挂载：输入

sudo mount -t vboxsf Share /mnt/shared进行手动挂载

自动挂载：/etc/fstab的文件最后写入如下：

Share /mnt/shared vboxsf rw,gid=dong,uid=dong,auto 0 0


5）复制文件：CMD: 

sudo cp -Rf ./VBox_GAs_6.0.6 /mnt/shared/

6）卸载共享文件夹：

sudo umount -f /mnt/shared
```

# 如何安装sublime
官宣：https://www.sublimetext.com/docs/3/linux_repositories.html#apt
https://www.jianshu.com/p/55ad5b07ec0e
```
## 方法一-失败：pga问题
tar -xvvf sublime_text_3_build_3126_x64.tar.bz
sudo add-apt-repository ppa:webupd8team/sublime-text-3 
error：解决：pga问题
sudo apt-get update 更新一下软件包
sudo apt-get install sublime-text 

## 方法二：
apt
1）Install the GPG key:
wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | sudo apt-key add -

2）Ensure apt is set up to work with https sources:
sudo apt-get install apt-transport-https

3）Select the channel to use:
Stable
echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
Dev
echo "deb https://download.sublimetext.com/ apt/dev/" | sudo tee /etc/apt/sources.list.d/sublime-text.list
Update apt sources and install Sublime Text

4）sudo apt-get update

5）sudo apt-get install sublime-text

6) 最后一步：https://blog.csdn.net/chenyulancn/article/details/80566555
--- BEGIN LICENSE -----
ZYNGA INC.
50 User License
EA7E-811825
927BA117 84C9300F 4A0CCBC4 34A56B44
985E4562 59F2B63B CCCFF92F 0E646B83
0FD6487D 1507AE29 9CC4F9F5 0A6F32E3
0343D868 C18E2CD5 27641A71 25475648
309705B3 E468DDC4 1B766A18 7952D28C
E627DDBA 960A2153 69A2D98A C87C0607
45DC6049 8C04EC29 D18DFA40 442C680B
1342224D 44D90641 33A3B9F2 46AADB8F
------ END LICENSE ------
```
## ubuntu 文件夹功能说明
```
### / 
这就是根目录，一台电脑有且只有一个根目录，所有的文件都是从这里开始的。举个例子：当你在终端里输入“/home”，你其实是在告诉电脑，先从/（根目录）开始，再进入到home目录。 
### /root 
系统管理员（root user）的目录。至于系统管理员的权限都多大我这里就不在废话了。因此，请小心使用root帐号。 
### /boot 
系统启动文件，所有与系统启动有关的文件都保存在这里 。 
### /bin 
这里是存放系统的程序。 
### /etc 
主要存放了系统配置方面的文件。 
### /dev 
这里主要存放与设备（包括外设）有关的文件（unix和linux系统均把设备当成文件）。想连线打印机吗？系统就是从这个目录开始工作的。另外还有一些包括磁盘驱动、USB驱动等都放在这个目录。 
### /home 
这里主要存放你的个人数据。具体每个用户的设置文件，用户的桌面文件夹，还有用户的数据都放在这里。每个用户都有自己的用户目录，位置为：/home/用户名。当然，root用户除外。 
### /tmp 
这是临时目录。对于某些程序来说，有些文件被用了一次两次之后，就不会再被用到，像这样的文件就放在这里。因此，千万不要把重要的数据放在这里哦。 
### /usr 
在这个目录下，你可以找到那些不适合放在/bin或/etc目录下的额外的工具。比如像游戏阿，一些打印工具等等。/usr目录包含了许多子目录： /usr/bin目录用于存放程序；/usr/share用于存放一些共享的数据，比如音乐文件或者图标等等；/usr/lib目录用于存放那些不能直接 运行的，但却是许多程序运行所必需的一些函数库文件。你的软件包管理器会自动帮你管理好/usr目录的。 
### /opt 
这里主要存放一些可选的程序。如你想尝试最新的firefox测试版吗？那就装到/opt目录下吧，这样，当你尝试完，想删掉firefox的时候，你就 可以直接删除它，而不影响系统其他任何设置。安装到/opt目录下的程序，它所有的数据、库文件等等都是放在同个目录下面。 
### usr/local 
这里主要存放那些手动安装的软件，即不是通过“新立得”或apt-get安装的软件。它和/usr目录具有相类似的目录结构。让软件包管理器来管理/usr目录，而把自定义的脚本（scripts）放到/usr/local目录下面。 
### /media 
这个目录是用来挂载那些usb接口的移动硬盘（包括U盘）、CD/DVD驱动器等等。
```
# ubuntu opencv 安装opencv
```
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev

cd ~/opencv-3.4.3  # 进入opencv文件夹
mkdir build # 创建build文件夹
cd build # 进入build文件夹

#cmake指令，如果没有特殊要求建议就选择默认的就可以
#注意，后面的两个点千万不能省，代表了上级目录
cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local ..  
make -j7 # 多线程执行make任务

# 最后一步，安装库文件
sudo make install

# 更新库
sudo ldconfig
```
