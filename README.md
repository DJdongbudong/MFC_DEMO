# 如何使用虚拟机挂载共享文件
## 步骤
第一步：

1）VMbox里面编辑共享文件夹路径D:\DongDemo\VM\Ubuntu18\Share 取名Share【随便自己取】第二步：

2）自动挂载+固定分配

第二步：

1）VMboxlinux系统的窗口【不是linux系统里面】：设备-安装增强工具-》安装VBox_GAs_6.0.6

第三步：

1）Ctrl+Alt+T：打开终端

2）linux创建共享文件夹，	
```
	CMD：	sudo mkdir /mnt/shared
```
3）linux挂载虚拟机的文件夹到Linux的shared中，
```
	CMD：	sudo mount -t vboxsf Share /mnt/shared
```
4) 挂载：
手动挂载：输入
```
sudo mount -t vboxsf Share /mnt/shared进行手动挂载
```
自动挂载：/etc/fstab的文件最后写入如下：
```
Share /mnt/shared vboxsf rw,gid=dong,uid=dong,auto 0 0
```

5）复制文件：CMD: 
```
sudo cp -Rf ./VBox_GAs_6.0.6 /mnt/shared/
```
6）卸载共享文件夹：
```
sudo umount -f /mnt/shared
```

## [参考](https://jingyan.baidu.com/article/a3761b2b87d2331577f9aa7e.html)

# 如何安装sublime
官宣：https://www.sublimetext.com/docs/3/linux_repositories.html#apt
https://www.jianshu.com/p/55ad5b07ec0e

# 如何安装sublime
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
