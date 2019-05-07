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
