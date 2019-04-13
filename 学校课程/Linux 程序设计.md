# Linux 程序设计

### 分区理论

- 主分区：最多四个，或者3主分区+1扩展分区
- 逻辑分区：扩展分区可以分出无数个逻辑分区，Linux最多59个
- 主引导记录 MBR：记录硬盘各个分区的大小和位置信息
  - 446字节程序代码（用于引导操作系统）
  - 64=4×16字节的磁盘分区表（DPT）
  - 2字节的结束标志（55 AA）
- GUID分区表 GPT：第一个扇区用作MBR，后面是分区表头部和分区表项，硬盘的最后保存了一份副本

### 文件系统

操作系统中负责存取和管理文件的部分，有ext2、ext3、fat32、ntfs等

#### 硬盘分区

- 根目录 /

- 交换分区 swap：和内存大小相同

- 引导分区 /boot：16MB

- 其他分区

  默认分区工具：fdisk

#### 文件类型

- 普通文件
- 字符文件
- 块文件 /dev 中
- socket
- 动态链接文件
- 目录

#### 文件权限管理

查看：`ls -l`文件类型-权限 链接数目 拥有者 组 大小 修改时间 文件名

修改：`chmod <who(ugoa) operator(+-=) what(rwx)> filename`

​	rwx 分别为421，相加即为对应二进制码

默认：文件`-rw-r--r--`；目录`drwxr-xr-x`

### Linux 启动流程

启动电源-BIOS-boot loader-Linux 内核-初始化-系统就绪

#### BIOS

检查内存和硬件、从不可擦除内存加载选项、检查引导设备、加载引导设备中的MBR并执行

#### Boot loader

加载并开始运行Linux内核，通常在 /dev/hda中配置

- LILO：Linux Loader /etc/lilo.conf
- GRUB：Grand Unified Bootloader /boot/grub/grub.conf

