## 1. 编译和安装 Hello OS

### 1.1. 编译
#### 1.1.1. 安装工具
```
# ubuntu
$ sudo apt install make
$ sudo apt install nasm
$ sudo apt install gcc

# centos
$ sudo yum install make
$ sudo yum install nasm
$ sudo yum install gcc
```

- `make` 是一个工具程序，通过读取 `makefile` 文件编写好的构建规则自动的构建软件.
- `nasm` 是 x86 体系结构的汇编语言编译器.
- `gcc` 是 C 语言的编译器.

#### 1.1.2. 编译过程
- make 编译：
```
$ cd hello.os
$ make
```
- 编译过程：

![编译过程](https://os-qingdao.oss-cn-qingdao.aliyuncs.com/cosmos-learning/hello.os/Hello%20OS%20%E7%BC%96%E8%AF%91%E8%BF%87%E7%A8%8B.jpg)

- 编译结果：

![编译结果](https://os-qingdao.oss-cn-qingdao.aliyuncs.com/cosmos-learning/hello.os/Hello%20OS%20%E7%BC%96%E8%AF%91%E7%BB%93%E6%9E%9C.png)

#### 1.1.3. 安装配置

##### 1. `HelloOS.bin` 文件复制到 `/boot` 目录下
```
$ sudo cp HelloOS.bin /boot
```
##### 2. 查找 boot 目录挂载的分区
在 Linux 系统的终端下输入命令：`df /boot/`，就会得到如下结果：

```
$ df /boot/
# 其中的“sda4”就是硬盘的第四个分区，但是 GRUB 的 menuentry 中不能写 sda4，
# 而是要写“hd0,msdos4”，这是 GRUB 的命名方式，hd0 表示第一块硬盘，
# 结合起来就是第一块硬盘的第四个分区。
文件系统          1K-块    已用     可用      已用% 挂载点
/dev/sda1      48752308 8087584 38158536   18%    /
```

##### 3. 配置 GRUB

##### Ubuntu 配置 GRUB
###### 1. 修改 `/boot/grub/grub.cfg` 文件的权限;
```
$ sudo chmod 777 /boot/grub/grub.cfg
```
###### 2. 编辑 `/boot/grub/grub.cfg` 文件，添加如下内容：
```
$ sudo gedit /boot/grub/grub.cfg

### BEGIN /etc/grub.d/40_custom ###
# This file provides an easy way to add custom menu entries.  Simply type the
# menu entries you want to add after this comment.  Be careful not to change
# the 'exec tail' line above.
# 追加内容
menuentry 'HelloOS' {
    insmod part_msdos
    insmod ext2
    set root='hd0,msdos1'
    multiboot2 /boot/HelloOS.bin
    boot
}
### END /etc/grub.d/40_custom ###

```

###### 3. 设置 grub
```
$ sudo gedit /etc/default/grub
- 注释 # grub_timeout_style=hidden
- 修改 GRUB_TIMEOUT=10
- 修改 grub_cmdline_linux_default = “text”
# 更新 grub 配置
$ sudo update-grub 
```
###### 4. 重启出现 `HelloOS` 的系统选项则表示成功

##### Centos 配置 GRUB
###### 1. 在 /etc/grub.d/40_custom 文件中，添加 HelloOS 在 grub2 中的配置
```
#!/bin/sh
exec tail -n +3 $0
# This file provides an easy way to add custom menu entries. Simply type the
# menu entries you want to add after this comment. Be careful not to change
# the 'exec tail' line above.
#以下是添加内容
menuentry 'HelloOS' {
     insmod part_msdos #GRUB加载分区模块识别分区
     insmod ext2 #GRUB加载ext文件系统模块识别ext文件系统
     set root='hd0,msdos1' #注意boot目录挂载的分区，这是我机器上的情况
     multiboot2 /HelloOS.bin #GRUB以multiboot2协议加载HelloOS.bin
     boot #GRUB启动HelloOS.bin
}
```

###### 2. 重新生成 `/boot/grub2/grub.cfg` 文件
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```

###### 3. 重启出现 `HelloOS` 的系统选项则表示成功

##### 资料
- [Grub 配置详解](https://blog.csdn.net/gatieme/article/details/52722955)
