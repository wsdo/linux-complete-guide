<!--
 * @Author: starkwang
 * @Contact me: https://shudong.wang/about
 * @Date: 2019-10-18 15:10:08
 * @LastEditors: starkwang
 * @LastEditTime: 2019-10-18 15:10:08
 * @Description: file content
 -->

Linux系统入门

Linux系统管理
	磁盘管理、文件系统管理
	RAID基础原理、LVM2
	网络管理：TCP/IP协议、Linux网络属性配置
	程序包管理：rpm, yum
	进程管理：htop, glance, tsar等
	sed和awk
	Linux系统开机流程
	内核管理基础知识：编译内核、模块
	Linux系统裁剪
		kernel+busybox
	课外作业：LFS

回顾：find、特殊权限、if语句

Linux磁盘管理

	I/O Ports: I/O设备地址；

	一切皆文件：
		open(), read(), write(), close()

		块设备：block，存取单位“块”，磁盘
		字符设备：char，存取单位“字符”，键盘

		设备文件：关联至一个设备驱动程序，进而能够跟与之对应硬件设备进行通信；

			设备号码：
				主设备号：major number, 标识设备类型
				次设备号：minor number, 标识同一类型下的不同设备

			硬盘接口类型：
				并行：
					IDE：133MB/s
					SCSI：640MB/s
				串口：
					SATA：6Gbps
					SAS：6Gbps
					USB：480MB/s

					rpm: rotations per minute


			/dev/DEV_FILE
				磁盘设备的设备文件命名：

				IDE: /dev/hd
				SCSI, SATA, SAS, USB: /dev/sd
					不同设备：a-z
						/dev/sda, /dev/sdb, ...
					同一设备上的不同分区：1,2, ...
						/dev/sda1, /dev/sda5

			机械式硬盘：
				track：磁道
				cylinder: 柱面
				secotr: 扇区
					512bytes

				如何分区：
					按柱面

				0磁道0扇区：512bytes
					MBR: Master Boot Record
						446bytes: boot loader
						64bytes：分区表
							16bytes: 标识一个分区
						2bytes: 55AA

						4个主分区；
							3主分区+1扩展(N个逻辑分区)
								逻辑分区

				问题：UEFI, GPT？

	分区管理工具：fdisk, parted, sfdisk
		fdisk：对于一块硬盘来讲，最多只能管理15分区；

		# fdisk -l [-u] [device...]

		# fdisk device
			子命令：管理功能
				p: print, 显示已有分区；
				n: new, 创建
				d: delete, 删除
				w: write, 写入磁盘并退出
				q: quit, 放弃更新并退出
				m: 获取帮助
				l: 列表所分区id
				t: 调整分区id

		查看内核是否已经识别新的分区：
			# cat /proc/partations

		通知内核重新读取硬盘分区表：
			partx -a /dev/DEVICE
				-n M:N

			kpartx -a /dev/DEVICE
				-f: force

			CentOS 5: 使用partprobe
				partprobe [/dev/DEVICE]


Linux文件系统管理：
	Linux文件系统: ext2, ext3, ext4, xfs, btrfs, reiserfs, jfs, swap
		swap: 交换分区
		光盘：iso9660
	Windows：fat32, ntfs
	Unix: FFS, UFS, JFS2
	网络文件系统：NFS, CIFS
	集群文件系统：GFS2, OCFS2
	分布式文件系统：ceph, 
		moosefs, mogilefs, GlusterFS, Lustre

	根据其是否支持"journal"功能：
		日志型文件系统: ext3, ext4, xfs, ...
		非日志型文件系统: ext2, vfat

	文件系统的组成部分：
		内核中的模块：ext4, xfs, vfat
		用户空间的管理工具：mkfs.ext4, mkfs.xfs, mkfs.vfat

	Linux的虚拟文件系统：VFS

	创建文件系统：
		mkfs命令：
			(1) # mkfs.FS_TYPE /dev/DEVICE
				ext4
				xfs
				btrfs
				vfat
			(2) # mkfs -t FS_TYPE /dev/DEVICE

			-L 'LABEL': 设定卷标

		mke2fs：ext系列文件系统专用管理工具
			-t {ext2|ext3|ext4}
			-b {1024|2048|4096}
			-L 'LABEL'
			-j: 相当于 -t ext3
				mkfs.ext3 = mkfs -t ext3 = mke2fs -j = mke2fs -t ext3
			-i #: 为数据空间中每多少个字节创建一个inode；此大小不应该小于block的大小；
			-N #：为数据空间创建个多少个inode；
			-m #: 为管理人员预留的空间占据的百分比；
			-O FEATURE[,...]：启用指定特性
				-O ^FEATURE：关闭指定特性

		mkswap：创建交换分区
			mkswap [options] device
				-L 'LABEL'

			前提：调整其分区的ID为82；


	其它常用工具：

		blkid：块设备属性信息查看
			blkid [OPTION]... [DEVICE]
				-U UUID: 根据指定的UUID来查找对应的设备
				-L LABEL：根据指定的LABEL来查找对应的设备

		e2label：管理ext系列文件系统的LABEL
			# e2label DEVICE [LABEL]

		tune2fs：重新设定ext系列文件系统可调整参数的值
			-l：查看指定文件系统超级块信息；super block
			-L 'LABEL'：修改卷标
			-m #：修预留给管理员的空间百分比
			-j: 将ext2升级为ext3
			-O: 文件系统属性启用或禁用
			-o: 调整文件系统的默认挂载选项
			-U UUID: 修改UUID号；

		dumpe2fs：
			-h：查看超级块信息

	文件系统检测：
		fsck: File System CheCk
			fsck.FS_TYPE
			fsck -t FS_TYPE
				-a: 自动修复错误
				-r: 交互式修复错误

				Note: FS_TYPE一定要与分区上已经文件类型相同；

		e2fsck：ext系列文件专用的检测修复工具
			-y：自动回答为yes; 
			-f：强制修复；


回顾：
	磁盘接口类型、磁盘分区、fdisk、mkfs、mke2fs, tune2fs, blkid, dumpe2fs, e2label

	vfs: xfs, ext{2|3|4}, btrfs

文件系统管理：
	将额外文件系统与根文件系统某现存的目录建立起关联关系，进而使得此目录做为其它文件访问入口的行为称之为挂载；

	解除此关联关系的过程称之为卸载；

	把设备关联挂载点：Mount Point
		mount

	卸载时：可使用设备，也可以使用挂载点
		umount

	注意：挂载点下原有文件在挂载完成后会被临时隐藏；

	挂载方法：mount DEVICE MOUNT_POINT
		mount：通过查看/etc/mtab文件显示当前系统已挂载的所有设备
		mount [-fnrsvw] [-t vfstype] [-o options] device dir
			device：指明要挂载的设备；
				(1) 设备文件：例如/dev/sda5
				(2) 卷标：-L 'LABEL', 例如 -L 'MYDATA'
				(3) UUID, -U 'UUID'：例如 -U '0c50523c-43f1-45e7-85c0-a126711d406e'
				(4) 伪文件系统名称：proc, sysfs, devtmpfs, configfs
			dir：挂载点
				事先存在；建议使用空目录；
				进程正在使用中的设备无法被卸载；

			常用命令选项：
				-t vsftype：指定要挂载的设备上的文件系统类型；
				-r: readonly，只读挂载；
				-w: read and write, 读写挂载；
				-n: 不更新/etc/mtab； 
				-a：自动挂载所有支持自动挂载的设备；(定义在了/etc/fstab文件中，且挂载选项中有“自动挂载”功能)
				-L 'LABEL': 以卷标指定挂载设备；
				-U 'UUID': 以UUID指定要挂载的设备；
				-B, --bind: 绑定目录到另一个目录上；

			注意：查看内核追踪到的已挂载的所有设备：cat /proc/mounts

			-o options：(挂载文件系统的选项)
				async：异步模式；
				sync：同步模式；
				atime/noatime：包含目录和文件；
				diratime/nodiratime：目录的访问时间戳
				auto/noauto：是否支持自动挂载
				exec/noexec：是否支持将文件系统上应用程序运行为进程
				dev/nodev：是否支持在此文件系统上使用设备文件；
				suid/nosuid：
				remount：重新挂载
				ro：
				rw:
				user/nouser：是否允许普通用户挂载此设备
				acl：启用此文件系统上的acl功能

				注意：上述选项可多个同时使用，彼此使用逗号分隔；
					  默认挂载选项：defaults
					  		rw, suid, dev, exec, auto, nouser, and async

	卸载命令：
		# umount DEVICE
		# umount MOUNT_POINT

		查看正在访问指定文件系统的进程：
			# fuser -v MOUNT_POINT

		终止所有在正访问指定的文件系统的进程：
			# fuser -km MOUNT_POINT

	挂载交换分区：
		启用：swapon
			swapon [OPTION]... [DEVICE]
				-a：激活所有的交换分区；
				-p PRIORITY：指定优先级；
		禁用：swapoff [OPTION]... [DEVICE]

	内存空间使用状态：
		free [OPTION]
			-m: 以MB为单位
			-g: 以GB为单位

	文件系统空间占用等信息的查看工具：
		df: 
			-h: human-readable
			-i：inodes instead of blocks
			-P: 以Posix兼容的格式输出; 

	查看某目录总体空间占用状态：
		du：
			du [OPTION]... DIR
				-h: human-readable
				-s: summary

	命令总结：mount, umount, free, df, du, swapon, swapoff, fuser

文件挂载的配置文件：/etc/fstab

	每行定义一个要挂载的文件系统；

		要挂载的设备或伪文件系统 	挂载点 	文件系统类型  	挂载选项 	转储频率  	自检次序

			要挂载的设备或伪文件系统：
				设备文件、LABEL(LABEL="")、UUID(UUID="")、伪文件系统名称(proc, sysfs)

			挂载选项：
				defaults

			转储频率：
				0：不做备份
				1：每天转储
				2：每隔一天转储

			自检次序：
				0：不自检
				1：首先自检；一般只有rootfs才用1；
				...

文件系统上的其它概念：
	
	Inode: Index Node, 索引节点
		地址指针：
			直接指针：
			间接指针：
			三级指针：

		inode bitmap：对位标识每个inode空闲与否的状态信息；

	链接文件：
		硬链接：
			不能够对目录进行；
			不能跨分区进行；
			指向同一个inode的多个不同路径；创建文件的硬链接即为为inode创建新的引用路径，因此会增加其引用计数；

		符号链接：
			可以对目录进行；
			可以跨分区；
			指向的是另一个文件的路径；其大小为指向的路径字符串的长度；不增加或减少目标文件inode的引用计数；

		ln [-sv] SRC DEST
			-s：symbolic link
			-v: verbose

	文件管理操作对文件的影响：
		文件删除：
		文件复制：
		文件移动：

	练习：
		1、创建一个20G的文件系统，块大小为2048，文件系统ext4，卷标为TEST，要求此分区开机后自动挂载至/testing目录，且默认有acl挂载选项；
			(1) 创建20G分区；
			(2) 格式化：
				mke2fs -t ext4 -b 2048 -L 'TEST' /dev/DEVICE
			(3) 编辑/etc/fstab文件
			LABEL='TEST' 	/testing 	ext4 	defaults,acl 	0 0

		2、创建一个5G的文件系统，卷标HUGE，要求此分区开机自动挂载至/mogdata目录，文件系统类型为ext3；

		3、写一个脚本，完成如下功能：
			(1) 列出当前系统识别到的所有磁盘设备；
			(2) 如磁盘数量为1，则显示其空间使用信息；
				否则，则显示最后一个磁盘上的空间使用信息；
				if [ $disks -eq 1 ]; then 
					fdisk -l /dev/[hs]da
				else 
					fdisk -l $(fdisk -l /dev/[sh]d[a-z] | grep -o "^Disk /dev/[sh]d[a-]" | tail -1 | cut -d' ' -f2)
				fi


bash脚本编程之用户交互：
	read [option]... [name ...]
		-p 'PROMPT'
		-t TIMEOUT

	bash -n /path/to/some_script
		检测脚本中的语法错误

	bash -x /path/to/some_script
		调试执行

	示例：
		#!/bin/bash
		# Version: 0.0.1
		# Author: MageEdu
		# Description: read testing

		read -p "Enter a disk special file: " diskfile
		[ -z "$diskfile" ] && echo "Fool" && exit 1

		if fdisk -l | grep "^Disk $diskfile" &> /dev/null; then
		    fdisk -l $diskfile
		else
		    echo "Wrong disk special file."
		    exit 2
		fi

回顾：
	mount/umount, fstab配置文件、ext文件系统基础原理、read、bash

		/etc/fstab

		ext：super block, GDT, inode table, block bitmap, inode bitmap

		dumpe2fs -h, tune2fs -l

		软链接：l, 

RAID: 
	Redundant Arrays of Inexpensive Disks
	                    Independent

	Berkeley: A case for Redundent Arrays of Inexpensive Disks RAID

		提高IO能力：
			磁盘并行读写；
		提高耐用性；
			磁盘冗余来实现

		级别：多块磁盘组织在一起的工作方式有所不同；
		RAID实现的方式：
			外接式磁盘阵列：通过扩展卡提供适配能力
			内接式RAID：主板集成RAID控制器
			Software RAID：

	级别：level
		RAID-0：0, 条带卷，strip; 
		RAID-1: 1, 镜像卷，mirror;
		RAID-2
		..
		RAID-5：
		RAID-6
		RAID10
		RAID01

		RAID-0: 
			读、写性能提升；
			可用空间：N*min(S1,S2,...)
			无容错能力
			最少磁盘数：2, 2+

		RAID-1：
			读性能提升、写性能略有下降；
			可用空间：1*min(S1,S2,...)
			有冗余能力
			最少磁盘数：2, 2+

		RAID-4：
			1101, 0110, 1011

		RAID-5：
			读、写性能提升
			可用空间：(N-1)*min(S1,S2,...)
			有容错能力：1块磁盘
			最少磁盘数：3, 3+

		RAID-6：
			读、写性能提升
			可用空间：(N-2)*min(S1,S2,...)
			有容错能力：2块磁盘
			最少磁盘数：4, 4+

		
		混合类型
			RAID-10：
				读、写性能提升
				可用空间：N*min(S1,S2,...)/2
				有容错能力：每组镜像最多只能坏一块；
				最少磁盘数：4, 4+
			RAID-01:

			RAID-50、RAID7

			JBOD：Just a Bunch Of Disks
				功能：将多块磁盘的空间合并一个大的连续空间使用；
				可用空间：sum(S1,S2,...)

		常用级别：RAID-0, RAID-1, RAID-5, RAID-10, RAID-50, JBOD

		实现方式：
			硬件实现方式
			软件实现方式 

			CentOS 6上的软件RAID的实现：
				结合内核中的md(multi devices)

				mdadm：模式化的工具
					命令的语法格式：mdadm [mode] <raiddevice> [options] <component-devices>
						支持的RAID级别：LINEAR, RAID0, RAID1, RAID4, RAID5, RAID6, RAID10; 

					模式：
						创建：-C
						装配: -A
						监控: -F
						管理：-f, -r, -a

					<raiddevice>: /dev/md#
					<component-devices>: 任意块设备


					-C: 创建模式
						-n #: 使用#个块设备来创建此RAID；
						-l #：指明要创建的RAID的级别；
						-a {yes|no}：自动创建目标RAID设备的设备文件；
						-c CHUNK_SIZE: 指明块大小；
						-x #: 指明空闲盘的个数；

						例如：创建一个10G可用空间的RAID5；

					-D：显示raid的详细信息；
						mdadm -D /dev/md#

					管理模式：
						-f: 标记指定磁盘为损坏；
						-a: 添加磁盘
						-r: 移除磁盘

					观察md的状态：
						cat /proc/mdstat

					停止md设备：
						mdadm -S /dev/md#

				watch命令：
					-n #: 刷新间隔，单位是秒；

					watch -n# 'COMMAND'

		练习1：创建一个可用空间为10G的RAID1设备，要求其chunk大小为128k，文件系统为ext4，有一个空闲盘，开机可自动挂载至/backup目录；
		练习2：创建一个可用空间为10G的RAID10设备，要求其chunk大小为256k，文件系统为ext4，开机可自动挂载至/mydata目录；

	博客作业：raid各级别特性；

LVM2:

	LVM: Logical Volume Manager， Version: 2

	dm: device mapper，将一个或多个底层块设备组织成一个逻辑设备的模块；
		/dev/dm-#

	/dev/mapper/VG_NAME-LV_NAME
		/dev/mapper/vol0-root
	/dev/VG_NAME/LV_NAME
		/dev/vol0/root

	pv管理工具：
		pvs：简要pv信息显示
		pvdisplay：显示pv的详细信息

		pvcreate /dev/DEVICE: 创建pv

	vg管理工具：
		vgs
		vgdisplay

		vgcreate  [-s #[kKmMgGtTpPeE]] VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]
		vgextend  VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]
		vgreduce  VolumeGroupName  PhysicalDevicePath [PhysicalDevicePath...]
			先做pvmove

		vgremove

	lv管理工具：
		lvs
		lvdisplay

		lvcreate -L #[mMgGtT] -n NAME VolumeGroup

		lvremove /dev/VG_NAME/LV_NAME

	扩展逻辑卷：
		# lvextend -L [+]#[mMgGtT] /dev/VG_NAME/LV_NAME
		# resize2fs /dev/VG_NAME/LV_NAME

	缩减逻辑卷：
		# umount /dev/VG_NAME/LV_NAME
		# e2fsck -f /dev/VG_NAME/LV_NAME
		# resize2fs /dev/VG_NAME/LV_NAME #[mMgGtT]
		# lvreduce -L [-]#[mMgGtT] /dev/VG_NAME/LV_NAME
		# mount

	快照：snapshot
		lvcreate -L #[mMgGtT] -p r -s -n snapshot_lv_name original_lv_name

	练习1：创建一个至少有两个PV组成的大小为20G的名为testvg的VG；要求PE大小为16MB, 而后在卷组中创建大小为5G的逻辑卷testlv；挂载至/users目录；

	练习2： 新建用户archlinux，要求其家目录为/users/archlinux，而后su切换至archlinux用户，复制/etc/pam.d目录至自己的家目录；

	练习3：扩展testlv至7G，要求archlinux用户的文件不能丢失；

	练习4：收缩testlv至3G，要求archlinux用户的文件不能丢失；

	练习5：对testlv创建快照，并尝试基于快照备份数据，验正快照的功能；


文件系统挂载使用：
	挂载光盘设备：
		光盘设备文件：
			IDE: /dev/hdc
			SATA: /dev/sr0

			符号链接文件：
				/dev/cdrom
				/dev/cdrw
				/dev/dvd
				/dev/dvdrw

		mount -r /dev/cdrom /media/cdrom
		umount /dev/cdrom

	dd命令：convert and copy a file
		用法：
			dd if=/PATH/FROM/SRC of=/PATH/TO/DEST 
				bs=#：block size, 复制单元大小；
				count=#：复制多少个bs；

			磁盘拷贝：
				dd if=/dev/sda of=/dev/sdb

			备份MBR
				dd if=/dev/sda of=/tmp/mbr.bak bs=512 count=1

			破坏MBR中的bootloader：
				dd if=/dev/zero of=/dev/sda bs=256 count=1

		两个特殊设备：
			/dev/null: 数据黑洞；
			/dev/zero：吐零机；

	博客作业：lvm基本应用，扩展及缩减实现；

回顾：lvm2, dd
	lvm: 边界动态扩展或收缩；快照；
		pv --> vg --> lv
			PE:
			LE:

	dd: 复制

btrfs文件系统：
	技术预览版

	Btrfs (B-tree, Butter FS, Better FS), GPL, Oracle, 2007, CoW; 
	ext3/ext4, xfs

	核心特性：
		多物理卷支持：btrfs可由多个底层物理卷组成；支持RAID，以联机“添加”、“移除”，“修改”；
		写时复制更新机制(CoW)：复制、更新及替换指针，而非“就地”更新；
		数据及元数据校验码：checksum
		子卷：sub_volume
		快照：支持快照的快照；
		透明压缩：

	文件系统创建：
		mkfs.btrfs
			-L 'LABEL'
			-d <type>: raid0, raid1, raid5, raid6, raid10, single
			-m <profile>: raid0, raid1, raid5, raid6, raid10, single, dup
			-O <feature>
				-O list-all: 列出支持的所有feature；

		属性查看：
			btrfs filesystem show 

		挂载文件系统：
			mount -t btrfs /dev/sdb MOUNT_POINT

		透明压缩机制：
			mount -o compress={lzo|zlib} DEVICE MOUNT_POINT

	子命令：filesystem, device, balance, subvolume

	博客作业：btrfs管理及应用


压缩、解压缩及归档工具

	compress/uncompress: .Z
	gzip/gunzip: .gz
	bzip2/bunzip2: .bz2
	xz/unxz: .xz
	zip/unzip
	tar, cpio

	1、gzip/gunzip

		gzip [OPTION]... FILE ...
			-d: 解压缩，相当于gunzip
			-c: 将结果输出至标准输出；
			-#：1-9，指定压缩比；

		zcat：不显式展开的前提下查看文本文件内容；

	2、bzip2/bunzip2/bzcat

		bzip2 [OPTION]... FILE ...
			-k: keep, 保留原文件；
			-d：解压缩
			-#：1-9，压缩比，默认为6；

		bzcat：不显式展开的前提下查看文本文件内容；

	3、xz/unxz/xzcat

		bzip2 [OPTION]... FILE ...
			-k: keep, 保留原文件；
			-d：解压缩
			-#：1-9，压缩比，默认为6；

		xzcat: 不显式展开的前提下查看文本文件内容；		

	4、tar
		tar [OPTION]... 

		(1) 创建归档
			tar -c -f /PATH/TO/SOMEFILE.tar FILE...

			tar -cf /PATH/TO/SOMEFILE.tar FILE...

		(2) 查看归档文件中的文件列表
			tar -t -f /PATH/TO/SOMEFILE.tar

		(3) 展开归档
			tar -x -f /PATH/TO/SOMEFILE.tar

			tar -x -f /PATH/TO/SOMEFILE.tar -C /PATH/TO/DIR

		结合压缩工具实现：归档并压缩

			-j: bzip2, -z: gzip, -J: xz

bash脚本编程：
	
	if语句、bash -n、bash -x

		CONDITION:
			bash命令：
				用命令的执行状态结果；
					成功：true
					失败：flase

				成功或失败的意义：取决于用到的命令；

		单分支：
			if CONDITION; then
				if-true
			fi

		双分支：
			if CONDITION; then
				if-true
			else
				if-false
			fi

		多分支：
			if CONDITION1; then
				if-true
			elif CONDITION2; then
				if-ture 
			elif CONDITION3; then
				if-ture 
			...
			esle
				all-false
			fi

			逐条件进行判断，第一次遇为“真”条件时，执行其分支，而后结束；

		示例：用户键入文件路径，脚本来判断文件类型；
			#!/bin/bash
			#

			read -p "Enter a file path: " filename

			if [ -z "$filename" ]; then
			    echo "Usage: Enter a file path."
			    exit 2
			fi

			if [ ! -e $filename ]; then
			    echo "No such file."
			    exit 3
			fi

			if [ -f $filename ]; then
			    echo "A common file."
			elif [ -d $filename ]; then
			    echo "A directory."
			elif [ -L $filename ]; then
			    echo "A symbolic file."
			else
			    echo "Other type."
			fi	
			
		注意：if语句可嵌套；

	循环：for, while, until
		循环体：要执行的代码；可能要执行n遍；
			进入条件：
			退出条件：

		for循环：
			for 变量名  in 列表; do
				循环体
			done

			执行机制：
				依次将列表中的元素赋值给“变量名”; 每次赋值后即执行一次循环体; 直到列表中的元素耗尽，循环结束; 

			示例：添加10个用户, user1-user10；密码同用户名；
				#!/bin/bash
				#

				if [ ! $UID -eq 0 ]; then
				    echo "Only root."
				    exit 1
				fi

				for i in {1..10}; do
				    if id user$i &> /dev/null; then
				  	echo "user$i exists."
				    else
				    	useradd user$i
					if [ $? -eq 0 ]; then
					    echo "user$i" | passwd --stdin user$i &> /dev/null
				            echo "Add user$i finished."
				        fi
				    fi
				done

			列表生成方式：
				(1) 直接给出列表；
				(2) 整数列表：
					(a) {start..end}
					(b) $(seq [start [step]] end) 
				(3) 返回列表的命令；
					$(COMMAND)
				(4) glob
				(b) 变量引用；
					$@, $*


			示例：判断某路径下所有文件的类型
				#!/bin/bash
				#

				for file in $(ls /var); do
				    if [ -f /var/$file ]; then
					echo "Common file."
				    elif [ -L /var/$file ]; then
					echo "Symbolic file."
				    elif [ -d /var/$file ]; then
					echo "Directory."
				    else
					echo "Other type."
				    fi
				done			

			示例：
				#!/bin/bash
				#
				declare -i estab=0
				declare -i listen=0
				declare -i other=0

				for state in $( netstat -tan | grep "^tcp\>" | awk '{print $NF}'); do
				    if [ "$state" == 'ESTABLISHED' ]; then
					let estab++
				    elif [ "$state" == 'LISTEN' ]; then
					let listen++
				    else
					let other++
				    fi
				done

				echo "ESTABLISHED: $estab"
				echo "LISTEN: $listen"
				echo "Unkown: $other"				

	练习1：/etc/rc.d/rc3.d目录下分别有多个以K开头和以S开头的文件；
		分别读取每个文件，以K开头的文件输出为文件加stop，以S开头的文件输出为文件名加start；
			“K34filename stop”
			“S66filename start”

	练习2：写一个脚本，使用ping命令探测172.16.250.1-254之间的主机的在线状态；


回顾：btrfs, compress/uncompress, archive, bash

	btrfs：
		子命令：filesystem, device, balance, subvolume
		管理：创建、挂载、子卷的创建、子卷挂载、向btrfs中添加或移除物理设备、重新均衡数据等；

	压缩/解压缩：
		gzip/gunzip/zcat, bzip2/bunzip2/bzcat, xz/unxz/xzcat, zip/unzip
		tar: -c, -t, -x, -f, -j, -z, -J, -C

	bash：
		多分支if语句；for循环；

Linux程序包管理：
	
	API：Application Programming Interface
		POSIX：Portable OS

		程序源代码 --> 预处理 --> 编译 --> 汇编 --> 链接 
			静态编译：
			共享编译：.so

	ABI：Application Binary Interface
		Windows与Linux不兼容
		库级别的虚拟化：
			Linux: WINE
			Windows: Cywin

	系统级开发
		C
		C++
	应用级开发
		java
		Python
		php
		perl
		ruby

	二进制应用程序的组成部分：
		二进制文件、库文件、配置文件、帮助文件

		程序包管理器：
			debian：deb, dpt
			redhat: rpm, rpm
				rpm: Redhat Package Manager
					RPM is Package Manager

			Gentoo
			Archlinux

	
	源代码：name-VERSION.tar.gz
		VERSION: major.minor.release
	rpm包命名方式：
		name-VERSION-release.arch.rpm
			VERSION: major.minor.release
			release.arch：
				release：release.OS

			zlib-1.2.7-13.el7.i686.rpm

			常见的arch：
				x86: i386, i486, i586, i686
				x86_64: x64, x86_64, amd64
				powerpc: ppc
				跟平台无关：noarch

		testapp: 拆包
			testapp-VERSION-ARCH.rpm: 主包
			testapp-devel-VERSION-ARCH.rpm：支包
			testapp-testing-VERSION-ARHC.rpm

		包之间：存在依赖关系
			X, Y, Z

			yum：rpm包管理器的前端工具；
			apt-get：deb包管理器前端工具;
			zypper: suse上的rpm前端管理工具；
			dnf: Fedora 22+ rpm包管理器前端管理工具；

	查看二进制程序所依赖的库文件：
		ldd /PATH/TO/BINARY_FILE

	管理及查看本机装载的库文件：
		ldconfig 
			/sbin/ldconfig -p: 显示本机已经缓存的所有可用库文件名及文件路径映射关系；

			配置文件为：/etc/ld.so.conf, /etc/ld.so.conf.d/*.conf
			缓存文件：/etc/ld.so.cache

	程序包管理：
		功能：将编译好的应用程序的各组成文件打包一个或几个程序包文件，从而方便快捷地实现程序包的安装、卸载、查询、升级和校验等管理操作；

		1、程序的组成组成清单 (每个包独有)
			文件清单
			安装或卸载时运行的脚本
		2、数据库(公共)
			程序包名称及版本
			依赖关系；
			功能说明；
			安装生成的各文件的文件路径及校验码信息；

	管理程序包的方式：
		使用包管理器：rpm
		使用前端工具：yum, dnf

	获取程序包的途径：
		(1) 系统发版的光盘或官方的服务器；
			CentOS镜像：
				http://mirrors.aliyun.com
				http://mirrors.sohu.com
				http://mirrors.163.com

		(2) 项目官方站点
		(3) 第三方组织：
			Fedora-EPEL
			搜索引擎：
				http://pkgs.org
				http://rpmfind.net
				http://rpm.pbone.net
		(4) 自己制作

	建议：检查其合法性
		来源合法性；
		程序包的完整性；


CentOS系统上rpm命令管理程序包：
	安装、卸载、升级、查询、校验、数据库维护

	安装：
		rpm {-i|--install} [install-options] PACKAGE_FILE ...
			-v: verbose
			-vv: 
			-h: 以#显示程序包管理执行进度；每个#表示2%的进度

			rpm -ivh PACKAGE_FILE ...

				[install-options]
					--test: 测试安装，但不真正执行安装过程；dry run模式；
					--nodeps：忽略依赖关系；
					--replacepkgs: 重新安装；

					--nosignature: 不检查来源合法性；
					--nodigest：不检查包完整性；

					--noscipts：不执行程序包脚本片断；
						%pre: 安装前脚本； --nopre
						%post: 安装后脚本； --nopost
						%preun: 卸载前脚本； --nopreun
						%postun: 卸载后脚本；  --nopostun

	升级：
		rpm {-U|--upgrade} [install-options] PACKAGE_FILE ...
		rpm {-F|--freshen} [install-options] PACKAGE_FILE ...

			upgrage：安装有旧版程序包，则“升级”；如果不存在旧版程序包，则“安装”;
			freeshen：安装有旧版程序包，则“升级”；如果不存在旧版程序包，则不执行升级操作；

			rpm -Uvh PACKAGE_FILE ...
			rpm -Fvh PACKAGE_FILE ...

			--oldpackage：降级；
			--force: 强行升级；

		注意：(1) 不要对内核做升级操作；Linux支持多内核版本并存，因此，对直接安装新版本内核；
			  (2) 如果原程序包的配置文件安装后曾被修改，长级时，新版本的提供的同一个配置文件并不会直接覆盖老版本的配置文件，而把新版本的文件重命名(FILENAME.rpmnew)后保留；

	查询：
		rpm {-q|--query} [select-options] [query-options]

		[select-options]
			-a: 所有包
			-f: 查看指定的文件由哪个程序包安装生成

			-p /PATH/TO/PACKAGE_FILE：针对尚未安装的程序包文件做查询操作；

			--whatprovides CAPABILITY：查询指定的CAPABILITY由哪个包所提供；
			--whatrequires CAPABILITY：查询指定的CAPABILITY被哪个包所依赖；

		[query-options]
			--changelog：查询rpm包的changlog
			-c: 查询程序的配置文件
			-d: 查询程序的文档
			-i: information
			-l: 查看指定的程序包安装后生成的所有文件；
			--scripts：程序包自带的脚本片断
			-R: 查询指定的程序包所依赖的CAPABILITY；
			--provides: 列出指定程序包所提供的CAPABILITY；

		用法：
			-qi PACKAGE, -qf FILE, -qc PACKAGE, -ql PACKAGE, -qd PACKAGE
			-qpi PACKAGE_FILE, -qpl PACKAGE_FILE, ...
			-qa

	卸载：
		rpm {-e|--erase} [--allmatches] [--nodeps] [--noscripts]
           [--notriggers] [--test] PACKAGE_NAME ...


    校验：
    	rpm {-V|--verify} [select-options] [verify-options]

	       S file Size differs
	       M Mode differs (includes permissions and file type)
	       5 digest (formerly MD5 sum) differs
	       D Device major/minor number mismatch
	       L readLink(2) path mismatch
	       U User ownership differs
	       G Group ownership differs
	       T mTime differs
	       P caPabilities differ

	包来源合法性验正及完整性验正：
		完整性验正：SHA256
		来源合法性验正：RSA


		公钥加密：
			对称加密：加密、解密使用同一密钥；
			非对称加密：密钥是成对儿的，
				public key: 公钥，公开所有人
				secret key: 私钥, 不能公开


		导入所需要公钥：
			rpm --import /PATH/FROM/GPG-PUBKEY-FILE

			CentOS 7发行版光盘提供的密钥文件：RPM-GPG-KEY-CentOS-7

	数据库重建：
		rpm {--initdb|--rebuilddb}
			initdb: 初始化
				如果事先不存在数据库，则新建之；否则，不执行任何操作；

			rebuilddb：重建
				无论当前存在与否，直接重新创建数据库；

回顾：Linux程序包管理的实现、rpm包管理器

	rpm命令实现程序管理：
		安装：-ivh, --nodeps, --replacepkgs
		卸载：-e, --nodeps
		升级：-Uvh, -Fvh, --nodeps, --oldpackage
		查询：-q, -qa, -qf, -qi, -qd, -qc, -q --scripts, -q --changlog, -q --provides, -q --requires
		校验：-V

		导入GPG密钥：--import, -K, --nodigest, --nosignature
		数据库重建：--initdb, --rebuilddb

Linux程序包管理(2)

	CentOS: yum, dnf

	URL: ftp://172.16.0.1/pub/	

	YUM: yellow dog, Yellowdog Update Modifier

	yum repository: yum repo
		存储了众多rpm包，以及包的相关的元数据文件（放置于特定目录下：repodata）；

		文件服务器：
			ftp://
			http://
			nfs://
			file:///

	yum客户端：
		配置文件：
			/etc/yum.conf：为所有仓库提供公共配置
			/etc/yum.repos.d/*.repo：为仓库的指向提供配置

		仓库指向的定义：
		[repositoryID]
		name=Some name for this repository
		baseurl=url://path/to/repository/
		enabled={1|0}
		gpgcheck={1|0}
		gpgkey=URL
		enablegroups={1|0}
		failovermethod={roundrobin|priority}
			默认为：roundrobin，意为随机挑选；
		cost=
			默认为1000


		教室里的yum源：http://172.16.0.1/cobbler/ks_mirror/CentOS-6.6-x86_64/
		CentOS 6.6 X84_64 epel: http://172.16.0.1/fedora-epel/6/x86_64/

	yum命令的用法：
		yum [options] [command] [package ...]

       command is one of:
        * install package1 [package2] [...]
        * update [package1] [package2] [...]
        * update-to [package1] [package2] [...]
        * check-update
        * upgrade [package1] [package2] [...]
        * upgrade-to [package1] [package2] [...]
        * distribution-synchronization [package1] [package2] [...]
        * remove | erase package1 [package2] [...]
        * list [...]
        * info [...]
        * provides | whatprovides feature1 [feature2] [...]
        * clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]
        * makecache
        * groupinstall group1 [group2] [...]
        * groupupdate group1 [group2] [...]
        * grouplist [hidden] [groupwildcard] [...]
        * groupremove group1 [group2] [...]
        * groupinfo group1 [...]
        * search string1 [string2] [...]
        * shell [filename]
        * resolvedep dep1 [dep2] [...]
        * localinstall rpmfile1 [rpmfile2] [...]
           (maintained for legacy reasons only - use install)
        * localupdate rpmfile1 [rpmfile2] [...]
           (maintained for legacy reasons only - use update)
        * reinstall package1 [package2] [...]
        * downgrade package1 [package2] [...]
        * deplist package1 [package2] [...]
        * repolist [all|enabled|disabled]
        * version [ all | installed | available | group-* | nogroups* | grouplist | groupinfo ]
        * history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]
        * check
        * help [command]

    显示仓库列表：
    	repolist [all|enabled|disabled]

    显示程序包：
    	list
    		# yum list [all | glob_exp1] [glob_exp2] [...]
    		# yum list {available|installed|updates} [glob_exp1] [...]

    安装程序包：
    	install package1 [package2] [...]

    	reinstall package1 [package2] [...]  (重新安装)

    升级程序包：
    	update [package1] [package2] [...]

    	downgrade package1 [package2] [...] (降级)

    检查可用升级：
    	check-update

    卸载程序包：
    	remove | erase package1 [package2] [...]

    查看程序包information：
    	info [...]

    查看指定的特性(可以是某文件)是由哪个程序包所提供：
    	provides | whatprovides feature1 [feature2] [...]

    清理本地缓存：
    	clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]

    构建缓存：
    	makecache

    搜索：
    	search string1 [string2] [...]

    	以指定的关键字搜索程序包名及summary信息；

    查看指定包所依赖的capabilities：
    	deplist package1 [package2] [...]

    查看yum事务历史：
    	history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]

    安装及升级本地程序包：
		* localinstall rpmfile1 [rpmfile2] [...]
           (maintained for legacy reasons only - use install)
        * localupdate rpmfile1 [rpmfile2] [...]
           (maintained for legacy reasons only - use update)

    包组管理的相关命令：
        * groupinstall group1 [group2] [...]
        * groupupdate group1 [group2] [...]
        * grouplist [hidden] [groupwildcard] [...]
        * groupremove group1 [group2] [...]
        * groupinfo group1 [...]

    如何使用光盘当作本地yum仓库：
    	(1) 挂载光盘至某目录，例如/media/cdrom
    		# mount -r -t iso9660 /dev/cdrom /media/cdrom
    	(2) 创建配置文件
    	[CentOS7]
    	name=
    	baseurl=
    	gpgcheck=
    	enabled=

    yum的命令行选项：
    	--nogpgcheck：禁止进行gpg check；
    	-y: 自动回答为“yes”；
    	-q：静默模式；
    	--disablerepo=repoidglob：临时禁用此处指定的repo；
    	--enablerepo=repoidglob：临时启用此处指定的repo；
    	--noplugins：禁用所有插件；

    yum的repo配置文件中可用的变量：
    	$releasever: 当前OS的发行版的主版本号；
    	$arch: 平台；
    	$basearch：基础平台；
    	$YUM0-$YUM9

    	http://mirrors.magedu.com/centos/$releasever/$basearch/os

    创建yum仓库：
    	createrepo [options] <directory>

    程序包编译安装：
    	testapp-VERSION-release.src.rpm --> 安装后，使用rpmbuild命令制作成二进制格式的rpm包，而后再安装；

    	源代码 --> 预处理 --> 编译(gcc) --> 汇编 --> 链接 --> 执行

    	源代码组织格式：
    		多文件：文件中的代码之间，很可能存在跨文件依赖关系；

    		C、C++： make (configure --> Makefile.in --> makefile)
    		java: maven


    		C代码编译安装三步骤：
    			./configure：
    				(1) 通过选项传递参数，指定启用特性、安装路径等；执行时会参考用户的指定以及Makefile.in文件生成makefile；
    				(2) 检查依赖到的外部环境；
    			make：
    				根据makefile文件，构建应用程序；
    			make install

    		开发工具：
    			autoconf: 生成configure脚本
    			automake：生成Makefile.in

    		建议：安装前查看INSTALL，README

    	开源程序源代码的获取：
    		官方自建站点：
    			apache.org (ASF)
    			mariadb.org
    			...
    		代码托管：
    			SourceForge
    			Github.com
    			code.google.com

    	c/c++: gcc (GNU C Complier)

    	编译C源代码：
    		前提：提供开发工具及开发环境
    			开发工具：make, gcc等
    			开发环境：开发库，头文件
    				glibc：标准库

    			通过“包组”提供开发组件
    				CentOS 6: "Development Tools", "Server Platform Development",

    		第一步：configure脚本
    			选项：指定安装位置、指定启用的特性

    			--help: 获取其支持使用的选项
    				选项分类：
    					安装路径设定：
    						--prefix=/PATH/TO/SOMEWHERE: 指定默认安装位置；默认为/usr/local/
    						--sysconfdir=/PATH/TO/SOMEWHERE：配置文件安装位置；

    					System types:

    					Optional Features: 可选特性
    						--disable-FEATURE
    						--enable-FEATURE[=ARG]

    					Optional Packages: 可选包
    						--with-PACKAGE[=ARG]
    						--without-PACKAGE

    		第二步：make

    		第三步：make install

    	安装后的配置：
    		(1) 导出二进制程序目录至PATH环境变量中；
    			编辑文件/etc/profile.d/NAME.sh
    				export PATH=/PATH/TO/BIN:$PATH

    		(2) 导出库文件路径
    			编辑/etc/ld.so.conf.d/NAME.conf
    				添加新的库文件所在目录至此文件中；

    			让系统重新生成缓存：
    				ldconfig [-v]

    		(3) 导出头文件
    			基于链接的方式实现：
    				ln -sv 

    		(4) 导出帮助手册
    			编辑/etc/man.config文件
    				添加一个MANPATH

    练习：
    	1、yum的配置和使用；包括yum repository的创建；
    	2、编译安装apache 2.2; 启动此服务；

    博客作业：程序包管理：rpm/yum/编译

 回顾：yum、源代码编译安装

 	yum [OPTION]... COMMAND [args]...
 		install, list, remove, update, groupinstall, grouplist, clean, makecache

 	源代码编译：
 		前提：开发环境
 			./configure
 			make
 			make install

 Linux网络属性管理

 	局域网：以太网，令牌环网

 		Ethernet: CSMA/CD
 			冲突域
 			广播域  

 			MAC：Media Access Control
 				48bits: 
 					24bits:
 					24bits:

 			IP: Internet Protocol
 				Routing protocol
 				Routed protocol

 	OSI, TCP/IP
 		tcp/ip分层：
 			application layer
 			transport layer
 			internet layer
 			datalink layer
 			pysical layer

 		传输层协议：
 			tcp, udp, sctp

 		网络层协议：
 			ip

 		ip协议：

 			IPv4 地址分类：
 				点分十进制：0-255
 				0000 0000 - 1111 1111

 				0.0.0.0-255.255.255.255

 				A类：
 					0 000 0000 - 0 111 1111: 1-127
 					网络数：126, 127
 					每个网络中的主机数：2^24-2
 					默认子网掩码：255.0.0.0
 					私网地址：10.0.0.0/8

 				B类：
 					10 00 0000 - 10 11 1111：128-191
 					网络数：2^14
 					每个网络中的主机数：2^16-2
 					默认子网掩码：255.255.0.0
 					私网地址：172.16.0.0/16-172.31.0.0/16

 				C类：
 					110 0 0000 - 110 1 1111: 192-223
 					网络数：2^21
 					每个网络中的主机数：2^8-2
 					默认子网掩码：255.255.255.0
 					私网地址：192.168.0.0/24-192.168.255.0/24

 				D类：组播
 					1110 0000 - 1110 1111: 224-239

 				E类：
 					240-255

 			子网掩码：
 				172.16.100.100/255.255.0.0, 172.17.1.1

 				跨网络通信：路由
 					主机路由
 					网络路由
 					默认路由

	将Linux主机接入到网络中：
		IP/mask
		路由：默认网关
		DNS服务器
			主DNS服务器
			次DNS服务器
			第三DNS服务器

		配置方式：
			静态指定:
				ifcfg: ifconfig, route, netstat
				ip: object {link, addr, route}, ss, tc
				配置文件
					system-config-network-tui (setup)
				CentOS 7:
					nmcli, nmtui
			动态分配：
				DHCP: Dynamic Host Configuration Protocol

		配置网络接口：
			接口命名方式：
				CentOS 6: 
					以太网：eth[0,1,2,...]
					ppp：ppp[0,1,2,...]

			ifconfig命令
				ifconfig [interface]
					# ifconfig -a
					# ifconfig IFACE [up|down]
       			ifconfig interface [aftype] options | address ...
       				# ifconfig IFACE IP/mask [up]
       				# ifconfig IFACE IP netmask MASK

       				注意：立即生效；

       				启用混杂模式：[-]promisc

       		route命令
       			路由管理命令
       				查看：route -n
       				添加：route add
       					route add  [-net|-host]  target [netmask Nm] [gw Gw] [[dev] If]

       						目标：192.168.1.3  网关：172.16.0.1
       						~]# route add -host 192.168.1.3 gw 172.16.0.1 dev eth0

       						目标：192.168.0.0 网关：172.16.0.1
       						~]# route add -net 192.168.0.0 netmask 255.255.255.0 gw 172.16.0.1 dev eth0
       						~]# route add -net 192.168.0.0/24 gw 172.16.0.1 dev eth0

       						默认路由，网关：172.16.0.1
       						~]# route add -net 0.0.0.0 netmask 0.0.0.0 gw 172.16.0.1
       						~]# route add default gw 172.16.0.1

       				删除：route del
       					route del [-net|-host] target [gw Gw] [netmask Nm] [[dev] If]

       						目标：192.168.1.3  网关：172.16.0.1
       						~]# route del -host 192.168.1.3

       						目标：192.168.0.0 网关：172.16.0.1
       						~]# route del -net 192.168.0.0 netmask 255.255.255.0

       			DNS服务器指定
       				/etc/resolv.conf
       					nameserver DNS_SERVER_IP1
       					nameserver DNS_SERVER_IP2
       					nameserver DNS_SERVER_IP3

       				正解：FQDN-->IP
       					# dig -t A FQDN
       					# host -t A FQDN
       				反解：IP-->FQDN
       					# dig -x IP
       					# host -t PTR IP
       					
       					FQDN: www.magedu.com.

       			netstat命令：
       				netstat - Print network connections, routing tables, interface statistics, masquerade connections, and multicast memberships

       				显示网络连接：
       					netstat [--tcp|-t] [--udp|-u] [--raw|-w] [--listening|-l] [--all|-a] [--numeric|-n] [--extend|-e[--extend|-e]]  [--program|-p]
       						-t: tcp协议相关
       						-u: udp协议相关
       						-w: raw socket相关
       						-l: 处于监听状态
       						-a: 所有状态
       						-n: 以数字显示IP和端口；
       						-e：扩展格式
       						-p: 显示相关进程及PID

       						常用组合：
       							-tan, -uan, -tnl, -unl

       				显示路由表：
       					netstat  {--route|-r} [--numeric|-n]
       						-r: 显示内核路由表
       						-n: 数字格式

       				显示接口统计数据：
       					netstat  {--interfaces|-I|-i} [iface] [--all|-a] [--extend|-e] [--program|-p] [--numeric|-n] 

       						# netstat -i
       						# netstat -I IFACE  

       		总结：ifcfg家庭命令配置
       			ifconfig/route/netstat
       			ifup/ifdown

Linux网络配置(2)
	
	配置Linux网络属性：ip命令

		ip命令：
			ip - show / manipulate routing, devices, policy routing and tunnels

			ip [ OPTIONS ] OBJECT { COMMAND | help }

				OBJECT := { link | addr | route }

			link OBJECT:
				ip link - network device configuration

					set
						dev IFACE
						可设置属性：
							up and down：激活或禁用指定接口；

					show
						[dev IFACE]：指定接口
						[up]：仅显示处于激活状态的接口

				ip address - protocol address management

					ip addr { add | del } IFADDR dev STRING
						[label LABEL]：添加地址时指明网卡别名
						[scope {global|link|host}]：指明作用域
							global: 全局可用；
							link: 仅链接可用；
							host: 本机可用；
						[broadcast ADDRESS]：指明广播地址

					ip address show - look at protocol addresses
						[dev DEVICE]
						[label PATTERN]
						[primary and secondary]

					ip address flush - flush protocol addresses
						使用格式同show

				ip route - routing table management

					ip route add
						添加路由：ip route add TARGET via GW dev IFACE src SOURCE_IP
							TARGET:
								主机路由：IP
								网络路由：NETWORK/MASK

							添加网关：ip route add defalt via GW dev IFACE

					ip route delete
						删除路由：ip route del TARGET 

					ip route show
					ip route flush
						[dev IFACE]
						[via PREFIX]

		ss命令：
			格式：ss [OPTION]... [FILTER]
				选项：
					-t: tcp协议相关
					-u: udp协议相关
					-w: 裸套接字相关
					-x：unix sock相关
					-l: listen状态的连接
					-a: 所有
					-n: 数字格式
					-p: 相关的程序及PID
					-e: 扩展的信息
					-m：内存用量
					-o：计时器信息

					FILTER := [ state TCP-STATE ] [ EXPRESSION ]

			TCP的常见状态：
				tcp finite state machine:
					LISTEN: 监听
					ESTABLISHED：已建立的连接
					FIN_WAIT_1
					FIN_WAIT_2
					SYN_SENT
					SYN_RECV
					CLOSED

				EXPRESSION:
					dport = 
					sport = 
					示例：’( dport = :ssh or sport = :ssh )’

			常用组合：
				-tan, -tanl, -tanlp, -uan

	Linux网络属性配置(3): 修改配置文件

		IP、MASK、GW、DNS相关配置文件：/etc/sysconfig/network-scripts/ifcfg-IFACE
		路由相关的配置文件：/etc/sysconfig/network-scripts/route-IFACE

		/etc/sysconfig/network-scripts/ifcfg-IFACE：
			DEVICE：此配置文件应用到的设备；
			HWADDR：对应的设备的MAC地址；
			BOOTPROTO：激活此设备时使用的地址配置协议，常用的dhcp, static, none, bootp；
			NM_CONTROLLED：NM是NetworkManager的简写；此网卡是否接受NM控制；CentOS6建议为“no”；
			ONBOOT：在系统引导时是否激活此设备；
			TYPE：接口类型；常见有的Ethernet, Bridge；
			UUID：设备的惟一标识；

			IPADDR：指明IP地址；
			NETMASK：子网掩码；
			GATEWAY: 默认网关；
			DNS1：第一个DNS服务器指向；
			DNS2：第二个DNS服务器指向；

			USERCTL：普通用户是否可控制此设备；
			PEERDNS：如果BOOTPROTO的值为“dhcp”，是否允许dhcp server分配的dns服务器指向信息直接覆盖至/etc/resolv.conf文件中；

		/etc/sysconfig/network-scripts/route-IFACE
			两种风格：
				(1) TARGET via GW

				(2) 每三行定义一条路由
					ADDRESS#=TARGET
					NETMASK#=mask
					GATEWAY#=GW

		给网卡配置多地址：
			ifconfig:
				ifconfig IFACE_ALIAS 
			ip
				ip addr add 
			配置文件：
				ifcfg-IFACE_ALIAS
					DEVICE=IFACE_ALIAS

			注意：网关别名不能使用dhcp协议引导；

	Linux网络属性配置的tui(text user interface)：
		system-config-network-tui

		也可以使用setup找到；

		注意：记得重启网络服务方能生效；

	配置当前主机的主机名：
		hostname [HOSTNAME]

		/etc/sysconfig/network
		HOSTNAME=

	网络接口识别并命名相关的udev配置文件：
		/etc/udev/rules.d/70-persistent-net.rules

		卸载网卡驱动：
			modprobe -r e1000

		装载网卡驱动：
			modprobe e1000


	CentOS 7网络属性配置

		传统命名：以太网eth[0,1,2,...], wlan[0,1,2,...]

		可预测功能

			udev支持多种不同的命名方案：
				Firmware, 拓扑结构

		(1) 网卡命名机制
			systemd对网络设备的命名方式：
				(a) 如果Firmware或BIOS为主板上集成的设备提供的索引信息可用，且可预测则根据此索引进行命名，例如eno1；
				(b) 如果Firmware或BIOS为PCI-E扩展槽所提供的索引信息可用，且可预测，则根据此索引进行命名，例如ens1; 
				(c) 如果硬件接口的物理位置信息可用，则根据此信息进行命名，例如enp2s0；
				(d) 如果用户显式启动，也可根据MAC地址进行命名，enx2387a1dc56; 
				(e) 上述均不可用时，则使用传统命名机制；

				上述命名机制中，有的需要biosdevname程序的参与；

		(2) 名称组成格式
			en: ethernet
			wl: wlan
			ww: wwan

			名称类型：
				o<index>: 集成设备的设备索引号；
				s<slot>: 扩展槽的索引号；
				x<MAC>: 基于MAC地址的命名；
				p<bus>s<slot>: enp2s1

		网卡设备的命名过程：
			第一步：
				udev, 辅助工具程序/lib/udev/rename_device, /usr/lib/udev/rules.d/60-net.rules

			第二步：
				biosdevname 会根据/usr/lib/udev/rules.d/71-biosdevname.rules

			第三步：
				通过检测网络接口设备，根据/usr/lib/udev/rules.d/75-net-description
					ID_NET_NAME_ONBOARD, ID_NET_NAME_SLOT, ID_NET_NAME_PATH

		回归传统命名方式：
			(1) 编辑/etc/default/grub配置文件
				GRUB_CMDLINE_LINUX="net.ifnames=0 rhgb quiet"

			(2) 为grub2生成其配置文件
				grub2-mkconfig -o /etc/grub2.cfg

			(3) 重启系统

		地址配置工具：nmcli
			nmcli  [ OPTIONS ] OBJECT { COMMAND | help }

				device - show and manage network interfaces

				connection - start, stop, and manage network connections

			如何修改IP地址等属性：
				#nmcli connection modify IFACE [+|-]setting.property value
					 setting.property:
					 	ipv4.addresses
					 	ipv4.gateway
					 	ipv4.dns1
					 	ipv4.method
					 		manual

		网络接口配置tui工具：nmtui

		主机名称配置工具：hostnamectl
			status
			set-hostname

	博客作业：上述所有内容；
		ifcfg, ip/ss, 配置文件, nmcli

	参考资料：http://www.redhat.com/hdocs

	课外作业：nmap, ncat, tcpdump工具

	网络客户端工具：
		lftp, ftp, lftpget, wget

			# lftp [-p port] [-u user[,password]] SERVER
				子命令：
					get
					mget
					ls
					help

			# lftpget URL

			# ftp

			# wget
				wget [option]... [URL]...
					-q: 静默模式
					-c: 续传
					-O: 保存位置
					--limit-rates=: 指定传输速率

			
回顾：ip命令，ss命令；配置文件；CentOS 7

	ifcfg、ip、netstat、ss
	配置文件：
		/etc/sysconfig/network-scripts/
			ifcfg-IFNAME
			route-IFNAME
	CentOS 7: nmcli, nmtui

Linux进程及作业管理

	内核的功用：进程管理、文件系统、网络功能、内存管理、驱动程序、安全功能

	Process: 运行中的程序的一个副本；
		存在生命周期

	Linux内核存储进程信息的固定格式：task struct
		多个任务的的task struct组件的链表：task list

	进程创建：
		init
			父子关系
			进程：都由其父进程创建
				fork(), clone()

		进程优先级：
			0-139：
				1-99：实时优先级；
				100-139：静态优先级；
					数字越小，优先级越高；

				Nice值：
					-20,19

			Big O
				O(1), O(logn), O(n), O(n^2), O(2^n)

		进程内存：
			Page Frame: 页框，用存储页面数据
				存储Page

				MMU：Memory Management Unit

		IPC: Inter Process Communication
			同一主机上：
				signal
				shm: shared memory
				semerphor

			不同主机上：
				rpc: remote procecure call
				socket: 

	Linux内核：抢占式多任务

		进程类型：
			守护进程: 在系统引导过程中启动的进程，跟终端无关的进程；
			前台进程：跟终端相关，通过终端启动的进程
				注意：也可把在前台启动的进程送往后台，以守护模式运行；

		进程状态：
			运行态：running
			就绪态：ready
			睡眠态：
				可中断：interruptable
				不可中断：uninterruptable
			停止态：暂停于内存中，但不会被调度，除非手动启动之；stopped
			僵死态：zombie

		进程的分类：
			CPU-Bound
			IO-Bound

		《Linux内核设计与实现》，《深入理解Linux内核》

	Linux进程查看及管理的工具：pstree, ps, pidof, pgrep, top, htop, glance, pmap, vmstat, dstat, kill, pkill, job, bg, fg, nohup

		pstree命令：
		 	pstree - display a tree of processes

		ps: process state
			ps - report a snapshot of the current processes

			Linux系统各进程的相关信息均保存在/proc/PID目录下的各文件中；

			ps [OPTION]...
				选项：支持两种风格

				常用组合：aux
					u: 以用户为中心组织进程状态信息显示
					a: 与终端相关的进程；
					x: 与终端无关的进程；

				 	~]# ps aux
					USER        PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND

						VSZ: Virtual memory SiZe，虚拟内存集
						RSS: ReSident Size, 常驻内存集
						STAT：进程状态
							R：running
							S: interruptable sleeping
							D: uninterruptable sleeping
							T: stopped
							Z: zombie

							+: 前台进程
							l: 多线程进程
							N：低优先级进程
							<: 高优先级进程
							s: session leader

				常用组合：-ef
					-e: 显示所有进程
					-f: 显示完整格式程序信息

				常用组合：-eFH
					-F: 显示完整格式的进程信息
					-H: 以进程层级格式显示进程相关信息

				常用组合：-eo, axo
					-eo pid,tid,class,rtprio,ni,pri,psr,pcpu,stat,comm
					axo stat,euid,ruid,tty,tpgid,sess,pgrp,ppid,pid,pcpu,comm

						ni: nice值
						pri: priority，优先级
						psr: processor, CPU
						rtprio: 实时优先级

		pgrep, pkill：
				pgrep [options] pattern
       			pkill [options] pattern

       				-u uid: effective user
       				-U uid: real user
       				-t terminal: 与指定终端相关的进程
       				-l: 显示进程名
       				-a: 显示完整格式的进程名
       				-P pid: 显示其父进程为此处指定的进程的进程列表

       	pidof：
       		根据进程名获取其PID；

       	top：
       		有许多内置命令：
       			排序：
       				P：以占据的CPU百分比；
       				M：占据内存百分比；
       				T：累积占据CPU时长；

       			首部信息显示：
       				uptime信息：l命令
       				tasks及cpu信息：t命令
       					cpu分别显示：1 (数字)
       				memory信息：m命令

       			退出命令：q
       			修改刷新时间间隔：s
       			终止指定进程：k

       		选项：
       			-d #: 指定刷新时间间隔，默认为3秒；
       			-b: 以批次方式；
       			-n #: 显示多少批次；

       	htop命令：
       		选项：
       			-d #: 指定延迟时间；
       			-u UserName: 仅显示指定用户的进程；
       			-s COLOMN: 以指定字段进行排序；
       		命令：
	       		s: 跟踪选定进程的系统调用；
	       		l: 显示选定进程打开的文件列表；
	       		a：将选定的进程绑定至某指定CPU核心；
	       		t: 显示进程树

	       	注意：Fedora-EPEL源

回顾：
	Linux基础:
		CPU: timeslice
		Memory: 线性地址空间
		I/O：
			分时复用
	进程查看工具：pstree, ps, pgrep, pidof, top, htop

Linux进程查看及管理(2)
	
	Linux进程查看及管理的工具：pstree, ps, pidof, pgrep, top, htop, glances, pmap, vmstat, dstat, kill, pkill, job, bg, fg, nohup

	vmstat命令：
		vmstat [options] [delay [count]]	
			procs:
				r：等待运行的进程的个数；
				b：处于不可中断睡眠态的进程个数；(被阻塞的队列的长度)；
			memory：
				swpd: 交换内存的使用总量； 
				free：空闲物理内存总量；
				buffer：用于buffer的内存总量；
				cache：用于cache的内存总量；
			swap:
				si：数据进入swap中的数据速率(kb/s)
				so：数据离开swap中的数据速率(kb/s)
			io：
				bi：从块设备读入数据到系统的速率；(kb/s)
				bo: 保存数据至块设备的速率；
			system：
				in: interrupts, 中断速率；
				cs: context switch, 进程切换速率；
			cpu：
				us
				sy
				id
				wa
				st

		选项：
			-s: 显示内存的统计数据

	pmap命令：
		pmap - report memory map of a process

			pmap [options] pid [...]
				-x: 显示详细格式的信息；

			另外一种实现：
				# cat /proc/PID/maps

	glances命令：

		glances [-bdehmnrsvyz1] [-B bind] [-c server] [-C conffile] [-p port] [-P password] [--password] [-t refresh] [-f file] [-o output]


		内建命令：

			  a  Sort processes automatically     l  Show/hide logs
			  c  Sort processes by CPU%           b  Bytes or bits for network I/O
			  m  Sort processes by MEM%           w  Delete warning logs
			  p  Sort processes by name           x  Delete warning and critical logs
			  i  Sort processes by I/O rate       1  Global CPU or per-CPU stats
			  d  Show/hide disk I/O stats         h  Show/hide this help screen
			  f  Show/hide file system stats      t  View network I/O as combination
			  n  Show/hide network stats          u  View cumulative network I/O
			  s  Show/hide sensors stats          q  Quit (Esc and Ctrl-C also work)
			  y  Show/hide hddtemp stats

		常用选项：
			-b: 以Byte为单位显示网卡数据速率；
			-d: 关闭磁盘I/O模块；
			-f /path/to/somefile: 设定输入文件位置；
			-o {HTML|CSV}：输出格式；
			-m: 禁用mount模块
			-n: 禁用网络模块
			-t #: 延迟时间间隔
			-1：每个CPU的相关数据单独显示；

		C/S模式下运行glances命令：
			服务模式：
				glances -s -B IPADDR

				IPADDR: 指明监听于本机哪个地址

			客户端模式：
				glances -c IPADDR

				IPADDR：要连入的服务器端地址

	dstat命令：
		dstat [-afv] [options..] [delay [count]]

			-c: 显示cpu相关信息；
				-C #,#,...,total
			-d: 显示disk相关信息；
				-D total,sda,sdb,...
			-g：显示page相关统计数据；
			-m: 显示memory相关统计数据；
			-n: 显示network相关统计数据；
			-p: 显示process相关统计数据；
			-r: 显示io请求相关的统计数据；
			-s: 显示swapped相关的统计数据；

			--tcp
			--udp
			--unix
			--raw
			--socket 

			--ipc

			--top-cpu：显示最占用CPU的进程；
			--top-io: 显示最占用io的进程；
			--top-mem: 显示最占用内存的进程；
			--top-lantency: 显示延迟最大的进程；

	kill命令：

		向进程发送控制信号，以实现对进程管理

		显示当前系统可用信号：
			# kill -l
			# man 7 signal

			常用信号：
				1) SIGHUP: 无须关闭进程而让其重读配置文件；
				2) SIGINT: 中止正在运行的进程；相当于Ctrl+c；
				9) SIGKILL: 杀死正在运行的进程；
				15) SIGTERM：终止正在运行的进程；
				18) SIGCONT：
				19) SIGSTOP：

			指定信号的方法：
				(1) 信号的数字标识；1, 2, 9
				(2) 信号完整名称；SIGHUP
				(3) 信号的简写名称；HUP

		向进程发信号：
			kill [-SIGNAL] PID...

		终止“名称”之下的所有进程：
			killall [-SIGNAL] Program

	Linux的作业控制

		前台作业：通过终端启动，且启动后一直占据终端；
		后台作业：可以通过终端启动，但启动后即转入后台运行（释放终端）；

		如何让作业运行于后台？
			(1) 运行中的作业
				Ctrl+z
			(2) 尚未启动的作业
				# COMMAND &

			此类作业虽然被送往后台运行，但其依然与终端相关；如果希望送往后台后，剥离与终端的关系：
				# nohup COMMAND &

			查看所有作业：
				# jobs

			作业控制：
				# fg [[%]JOB_NUM]：把指定的后台作业调回前台；
				# bg [[%]JOB_NUM]：让送往后台的作业在后台继续运行；
				# kill [%JOB_NUM]：终止指定的作业；

	进程优先级调整：
		静态优先级：100-139

		进程默认启动时的nice值为0，优先级为120;

		nice命令：
			nice [OPTION] [COMMAND [ARG]...]

		renice命令：
			renice [-n] priority pid...

		查看：
			ps axo pid,comm,ni

	未涉及到的命令：sar, tsar, iostat, iftop

	博客作业：进程管理工具top/htop/glances/dstat的使用；


Linux任务计划、周期性任务执行

	未来的某时间点执行一次任务：at, batch
	周期性运行某任务: cron

	电子邮件服务：
		smtp: simple mail transmission protocol, 用于传送邮件；
		pop3: Post Office Protocol
		imap4：Internet Mail Access Protocol

		mailx - send and receive Internet mail

			MUA：Mail User Agent

			mailx [-s 'SUBJECT'] username[@hostname] 
				邮件正文的生成：
					(1) 直接给出，Ctrl+d; 
					(2) 输入重定向；
					(3) 通过管道；
						echo -e "How are you?\nHow old are you?" | mail

			mailx

	at命令：

		at [option] TIME

			TIME:
				HH:MM [YYYY-mm-dd]
				noon, midnight, teatime
				tomorrow
				now+#{minutes,hours,days, OR weeks}

			常用选项：
				-q QUEUE: 
				-l: 列出指定队列中等待运行的作业；相当于atq
				-d: 删除指定的作业；相当于atrm
				-c: 查看具体作业任务；
				-f /path/from/somefile：从指定的文件中读取任务；

			注意：作业的执行结果以邮件通知给相关用户；

	batch命令：
		让系统自行选择空闲时间去执行此处指定的任务；

	周期性任务计划：cron
		相关的程序包：
			cronie: 主程序包，提供了crond守护进程及相关辅助工具；
			cronie-anacron：cronie的补充程序；用于监控cronie任务执行状况；如cronie中的任务在过去该运行的时间点未能正常运行，则anacron会随后启动一次此任务；
			crontabs：包含CentOS提供系统维护任务；

			确保crond守护处于运行状态：
				CentOS 7:
					systemctl status crond
						...running...
				CentOS 6:
					service crond status

		计划要周期性执行的任务提交给crond，由其来实现到点运行。
			系统cron任务：系统维护作业
				/etc/crontab
			用户cron任务：
				crontab命令

			系统cron任务
				# Example of job definition:
				# .---------------- minute (0 - 59)
				# |  .------------- hour (0 - 23)
				# |  |  .---------- day of month (1 - 31)
				# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
				# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
				# |  |  |  |  |
				# *  *  *  *  * user-name  command to be executed	

				例如：晚上9点10分运行echo命令；
					10 21 * * *	 gentoo /bin/echo "Howdy!"

				时间表示法：
					(1) 特定值；
						给定时间点有效取值范围内的值；
					(2) *
						给定时间点上有效取值范围内的所有值；
						表示“每...”；
					(3) 离散取值：,
						#,#,#
					(4) 连续取值：-
						#-#
					(5) 在指定时间范围上，定义步长：
						/#: #即为步长

				例如：每3小时echo命令；
					0 */3 * * * gentoo /bin/echo "howdy!"

			用户cron：
				crontab命令定义，每个用户都有专用的cron任务文件：/var/spool/cron/USERNAME

				crontab命令：
					crontab [-u user] [-l | -r | -e] [-i] 
						-l: 列出所有任务；
						-e: 编辑任务；
						-r: 移除所有任务；
						-i：同-r一同使用，以交互式模式让用户有选择地移除指定任务；

						-u user: 仅root可运行，代为为指定用户管理cron任务；

			注意：运行结果以邮件通知给相关用户；
				(1) COMMAND > /dev/null 
				(2) COMMAND &> /dev/null

				对于cron任务来讲，%有特殊用途；如果在命令中要使用%，则需要转义；不过，如果把%放置于单引号中，也可以不用转义；

			思考：
				(1) 如何在秒级别运行任务？
					* * * * * for min in 0 1 2; do echo "hi"; sleep 20; done
				(2) 如何实现每7分钟运行一次任务?

				sleep命令：
					sleep NUMBER[SUFFIX]...

						SUFFIX: 
							s: 秒, 默认
							m: 分
							h: 小时
							d: 天

		练习：
			1、每4小时备份一次/etc目录至/backup目录中，保存的文件名称格式为“etc-yyyy-mm-dd-HH.tar.xz”；

			2、每周2, 4, 7备份/var/log/messages文件至/logs目录中，文件名形如“messages-yyyymmdd”；

			3、每两小时取出当前系统/proc/meminfo文件中以S或M开头的信息追加至/tmp/meminfo.txt文件中；

			4、工作日时间内，每小执行一次“ip addr show”命令；

	
回顾：进程管理工具、任务计划

	进程管理工具：pmap, glances, vmstat, dstat, kill, jobs, bg, fg, nice, renice, pidof
	任务计划：
		一次性执行某任务：at, batch
		周期性执行某任务：crond --> anacron
			* * * * * COMMAND

CentOS 5和6的启动流程

	Linux: kernel+rootfs

		kernel: 进程管理、内存管理、网络管理、驱动程序、文件系统、安全功能
		rootfs:
			glibc

		库：函数集合, function, 调用接口
			过程调用：procedure
			函数调用：function

		程序

		内核设计流派：
			单内核设计：Linux
				把所有功能集成于同一个程序；
			微内核设计：Windows, Solaris
				每种功能使用一个单独子系统实现；

		Linux内核特点：
			支持模块化：.ko
			支持模块的动态装载和卸载；

			组成部分：
				核心文件：/boot/vmlinuz-VERSION-release
					ramdisk：
						CentOS 5: /boot/initrd-VERSION-release.img
						CentOS 6: /boot/initramfs-VERSION-release.img
				模块文件：/lib/modules/VERSION-release

	CentOS 系统启动流程：

		POST：加电自检；
			ROM：CMOS
				BIOS：Basic Input and Output System

				ROM+RAM

		BOOT Sequence: 
			按次序查找各引导设备，第一个有引导程序的设备即为本次启动用到设备；

			bootloader: 引导加载器，程序
				windows: ntloader
				Linux：
					LILO：LInux LOader
					GRUB: GRand Uniform Bootloader
						GRUB 0.X: GRUB Legacy
						GRUB 1.x: GRUB2

				功能：提供一个菜单，允许用户选择要启动系统或不同的内核版本；把用户选定的内核装载到内存中的特定空间中，解压、展开，并把系统控制权移交给内核；

			MBR:
				446: bootloader
				64: fat
				2: 55AA

			GRUB: 
				bootloader: 1st stage
				disk: 2nd stage

		kernel：
			自身初始化：
				探测可识别到的所有硬件设备；
				加载硬件驱动程序；（有可能会借助于ramdisk加载驱动）
				以只读方式挂载根文件系统；
				运行用户空间的第一个应用程序：/sbin/init

				init程序的类型：
					SysV: init, CentOS 5
						配置文件：/etc/inittab

					Upstart: init, CentOS 6
						配置文件：/etc/inittab, /etc/init/*.conf

					Systemd：systemd, CentOS 7
						配置文件：/usr/lib/systemd/system, /etc/systemd/system

				ramdisk：

					内核中的特性之一：使用缓冲和缓存来回事对磁盘上的文件访问；

						ramdisk --> ramfs

						CentOS 5: initrd,  工具程序：mkinitrd
						CentOS 6: initramfs， 工具程序：mkinitrd, dracut

			系统初始化：
				POST --> BootSequence (BIOS) --> Bootloader(MBR) --> kernel(ramdisk) --> rootfs(只读) --> init

	
		/sbin/init

			CentOS 5:

				运行级别：为了系统的运行或维护等应用目的而设定；

					0-6：7个级别
						0：关机
						1：单用户模式(root, 无须登录), single, 维护模式；
						2: 多用户模式，会启动网络功能，但不会启动NFS；维护模式；
						3：多用户模式，正常模式；文本界面；
						4：预留级别；可同3级别；
						5：多用户模式，正常模式；图形界面；
						6：重启

					默认级别：
						3, 5

					切换级别：
						init #

					查看级别：
						runlevel
						who -r

			配置文件：/etc/inittab

				每一行定义一种action以及与之对应的process
					id:runlevel:action:process
						action:
							wait: 切换至此级别运行一次；
							respawn：此process终止，就重新启动之；
							initdefault：设定默认运行级别；process省略；
							sysinit：设定系统初始化方式，此处一般为指定/etc/rc.d/rc.sysinit；
							...

					id:3:initdefault:
					si::sysinit:/etc/rc.d/rc.sysinit

					l0:0:wait:/etc/rc.d/rc 0
					l1:1:wait:/etc/rc.d/rc 1
					...
					l6:6:wait:/etc/rc.d/rc 6

						说明：rc 0 --> 意味着读取/etc/rc.d/rc0.d/
							K*: K##*：##运行次序；数字越小，越先运行；数字越小的服务，通常为依赖到别的服务；
							S*: S##*：##运行次序；数字越小，越先运行；数字越小的服务，通常为被依赖到的服务；

							for srv in /etc/rc.d/rc0.d/K*; do
								$srv stop
							done

							for srv in /etc/rc.d/rc0.d/S*; do
								$srv start
							done

						chkconfig命令
							查看服务在所有级别的启动或关闭设定情形：
								chkconfig [--list] [name]

							添加：
								SysV的服务脚本放置于/etc/rc.d/init.d (/etc/init.d)

								chkconfig --add name

									#!/bin/bash
									#
									# chkconfig: LLLL nn nn

							删除：
								chkconfig --del name

							修改指定的链接类型
								chkconfig [--level levels] name <on|off|reset>
									--level LLLL: 指定要设置的级别；省略时表示2345；

						注意：正常级别下，最后启动一个服务S99local没有链接至/etc/rc.d/init.d一个服务脚本，而是指向了/etc/rc.d/rc.local脚本；因此，不便或不需写为服务脚本放置于/etc/rc.d/init.d/目录，且又想开机时自动运行的命令，可直接放置于/etc/rc.d/rc.local文件中；

					tty1:2345:respawn:/usr/sbin/mingetty tty1
					tty2:2345:respawn:/usr/sbin/mingetty tty2
					...
					tty6:2345:respawn:/usr/sbin/mingetty tty6

						mingetty会调用login程序

				/etc/rc.d/rc.sysinit: 系统初始化脚本
					(1) 设置主机名；
					(2) 设置欢迎信息；
					(3) 激活udev和selinux; 
					(4) 挂载/etc/fstab文件中定义的文件系统；
					(5) 检测根文件系统，并以读写方式重新挂载根文件系统；
					(6) 设置系统时钟；
					(7) 激活swap设备；
					(8) 根据/etc/sysctl.conf文件设置内核参数；
					(9) 激活lvm及software raid设备；
					(10) 加载额外设备的驱动程序；
					(11) 清理操作；

			总结：/sbin/init --> (/etc/inittab) --> 设置默认运行级别 --> 运行系统初始脚本、完成系统初始化 --> 关闭对应下需要关闭的服务，启动需要启动服务 --> 设置登录终端 


		CentOS 6:

			init程序为: upstart, 其配置文件：
				/etc/inittab, /etc/init/*.conf

				注意：/etc/init/*.conf文件语法 遵循 upstart配置文件语法格式；

		博客作业：系统启动流程；

		启动系统时，设置其运行级别1：


回顾：
	
	CentOS 6启动流程：
		POST --> Boot Sequence(BIOS) --> Boot Loader (MBR) --> Kernel(ramdisk) --> rootfs --> switchroot --> /sbin/init -->(/etc/inittab, /etc/init/*.conf) --> 设定默认运行级别 --> 系统初始化脚本 --> 关闭或启动对应级别下的服务 --> 启动终端

GRUB(Boot Loader)：

	grub: GRand Unified Bootloader
		grub 0.x: grub legacy
		grub 1.x: grub2

	grub legacy:
		stage1: mbr
		stage1_5: mbr之后的扇区，让stage1中的bootloader能识别stage2所在的分区上的文件系统；
		stage2：磁盘分区(/boot/grub/)

		配置文件：/boot/grub/grub.conf <-- /etc/grub.conf

		stage2及内核等通常放置于一个基本磁盘分区；
			功用：
				(1) 提供菜单、并提供交互式接口
					e: 编辑模式，用于编辑菜单；
					c: 命令模式，交互式接口；
				(2) 加载用户选择的内核或操作系统
					允许传递参数给内核
					可隐藏此菜单
				(3) 为菜单提供了保护机制
					为编辑菜单进行认证
					为启用内核或操作系统进行认证

		如何识别设备：
			(hd#,#)
				hd#: 磁盘编号，用数字表示；从0开始编号
				#: 分区编号，用数字表示; 从0开始编号

				(hd0,0)

		grub的命令行接口
			help: 获取帮助列表
			help KEYWORD: 详细帮助信息
			find (hd#,#)/PATH/TO/SOMEFILE：
			root (hd#,#)
			kernel /PATH/TO/KERNEL_FILE: 设定本次启动时用到的内核文件；额外还可以添加许多内核支持使用的cmdline参数；
				例如：init=/path/to/init, selinux=0
			initrd /PATH/TO/INITRAMFS_FILE: 设定为选定的内核提供额外文件的ramdisk；
			boot: 引导启动选定的内核；

			手动在grub命令行接口启动系统：
				grub> root (hd#,#)
				grub> kernel /vmlinuz-VERSION-RELEASE ro root=/dev/DEVICE 
				grub> initrd /initramfs-VERSION-RELEASE.img
				grub> boot

		配置文件：/boot/grub/grub.conf
			配置项：
				default=#: 设定默认启动的菜单项；落单项(title)编号从0开始；
				timeout=#：指定菜单项等待选项选择的时长；
				splashimage=(hd#,#)/PATH/TO/XPM_PIC_FILE：指明菜单背景图片文件路径；
				hiddenmenu：隐藏菜单；
				password [--md5] STRING: 菜单编辑认证；
				title TITLE：定义菜单项“标题”, 可出现多次；
					root (hd#,#)：grub查找stage2及kernel文件所在设备分区；为grub的“根”; 
					kernel /PATH/TO/VMLINUZ_FILE [PARAMETERS]：启动的内核
					initrd /PATH/TO/INITRAMFS_FILE: 内核匹配的ramfs文件；
					password [--md5] STRING: 启动选定的内核或操作系统时进行认证；


			grub-md5-crypt命令

		进入单用户模式：
			(1) 编辑grub菜单(选定要编辑的title，而后使用e命令); 
			(2) 在选定的kernel后附加
				1, s, S或single都可以；
			(3) 在kernel所在行，键入“b”命令；

		安装grub：
			(1) grub-install
				grub-install --root-directory=ROOT /dev/DISK
			
			(2) grub
				grub> root (hd#,#)
				grub> setup (hd#)

		练习：
			1、新加硬盘，提供直接单独运行bash系统；
			2、破坏本机grub stage1，而后在救援模式下修复之；
			3、为grub设备保护功能；

	博客作业：grub应用；

Linux Kernel：

	单内核体系设计、但充分借鉴了微内核设计体系的优点，为内核引入模块化机制。
		内核组成部分：
			kernel: 内核核心，一般为bzImage，通常在/boot目录下，名称为vmlinuz-VERSION-RELEASE；
			kernel object: 内核对象，一般放置于/lib/modules/VERSION-RELEASE/
				[ ]: N
				[M]: M
				[*]: Y

			辅助文件：ramdisk
				initrd
				initramfs

	运行中的内核：

		uname命令：
			uname - print system information
			uname [OPTION]...
				-n: 显示节点名称；
				-r: 显示VERSION-RELEASE; 

		模块：
			lsmod命令：
				显示由核心已经装载的内核模块

				显示的内容来自于: /proc/modules文件

			modinfo命令：
				显示模块的详细描述信息

				modinfo [ -k kernel ]  [ modulename|filename... ]
					-n: 只显示模块文件路径
					-p: 显示模块参数
					-a: author
					-d: description
					-l: license

			modprobe命令：
				装载或卸载内核模块

				modprobe [ -C config-file ]  [ modulename ]  [ module parame-ters... ]
					配置文件：/etc/modprobe.conf, /etc/modprobe.d/*.conf

      			modprobe [ -r ] modulename...

      		depmod命令：
      			内核模块依赖关系文件及系统信息映射文件的生成工具；

      		装载或卸载内核模块：
      			insmod命令：
      				insmod [ filename ]  [ module options... ]

      			rmmod
      				rmmod [ modulename ]

    /proc目录：
    	内核把自己内部状态信息及统计信息，以及可配置参数通过proc伪文件系统加以输出；

    	参数：
    		只读：输出信息
    		可写：可接受用户指定“新值”来实现对内核某功能或特性的配置
    			/proc/sys

    			(1) sysctl命令用于查看或设定此目录中诸多参数；
    				sysctl -w path.to.parameter=VALUE

    				~]# sysctl -w kernel.hostname=mail.magedu.com

    			(2) echo命令通过重定向的方式也可以修改大多数参数的值；
    				echo "VALUE" > /proc/sys/path/to/parameter

    				~]# echo "www.magedu.com" > /proc/sys/kernel/hostname

    		sysctl命令：
    			默认配置文件：/etc/sysctl.conf
    				(1) 设置某参数
    					sysctl -w parameter=VALUE
    				(2) 通过读取配置文件设置参数
    					sysctl -p [/path/to/conf_file]

    		内核中的路由转发：
    			/proc/sys/net/ipv4/ip_forward

    			常用的几个参数：
    				net.ipv4.ip_forward
    				vm.drop_caches
    				kernel.hostname

    /sys目录：

    	sysfs：输出内核识别出的各硬件设备的相关属性信息，也有内核对硬件特性的设定信息；有些参数是可以修改的，用于调整硬件工作特性。

    	udev通过此路径下输出的信息动态为各设备创建所需要设备文件；udev是运行用户空间程序；专用工具：udevadmin, hotplug；

    	udev为设备创建设备文件时，会读取其事先定义好的规则文件，一般在/etc/udev/rules.d及/usr/lib/udev/rules.d目录下；

    ramdisk文件的制作：

    	(1) mkinitrd命令
    		为当前正在使用的内核重新制作ramdisk文件
    			~] # mkinitrd /boot/initramfs-$(uname -r).img $(uname -r)

    	(2) dracut命令 
    		为当前正在使用的内核重新制作ramdisk文件
    			~] # dracut /boot/initramfs-$(uname -r).img $(uname -r) 

    编译内核：
    	前提：
    		(1) 准备好开发环境；
    		(2) 获取目标主机上硬件设备的相关信息；
    		(3) 获取到目标主机系统功能的相关信息，例如要启用的文件系统；
    		(4) 获取内核源代码包；
    			www.kernel.org

    	准备好开发环境：
    		包组(CentOS 6)：
    			Server Platform Development
    			Development Tools

    	目标主机硬件设备相关信息：
    		CPU：
    			~]# cat /proc/cpuinfo
    			~]# x86info -a
    			~]# lscpu

    		PCI设备：
    			~]# lspci
    				-v
    				-vv

    			~]# lsusb
    				-v
    				-vv

    			~]# lsblk

    		了解全部硬件设备信息
    			~]# hal-device

    	简单依据模板文件的制作过程：
    		~]# tar xf linux-3.10.67.tar.xz -C /usr/src
    		~]# cd /usr/src
    		~]# ln -sv linux-3.10.67 linux
    		~]# cd linux
    		~]# cp /boot/config-$(uname -r) ./.config

    		~]# make menuconfig
    		~]# screen
    		~]# make -j #

    		~]# make modules_install
    		~]# make install

    		重启系统，并测试使用新内核；

    	练习：编译好，并启用之；

回顾：内核组成部分、内核编译

	内核组成部分：
		核心、模块
		核心：/boot/vmlinuz-VERSION-RELEASE
		模块：/lib/modules/VERSION-RELEASE/

		模块管理的相关命令：
			lsmod, modinfo, modprobe [-r], insmod, rmmod, depmod

	内核编译：
		[ ]
		[*]
		[M]

		步骤：
			make menuconfig：配置内核选项
				.config：文本文件
			make [-j #]
			make modules_install
			make install
				安装bzImage为/boot/vmlinuz-VERSION-RELEASE
				生成initramfs文件
				编辑grub的配置文件

Linux内核编译(2)

	编译内核的步骤：
		(1) 配置内核选项
			支持“更新”模式进行配置：
				(a) make config：基于命令行以遍历的方式去配置内核中可配置的每个选项；
				(b) make menuconfig：基于curses的文本窗口界面；
				(c) make gconfig：基于GTK开发环境的窗口界面；
				(d) make xconfig：基于Qt开发环境的窗口界面；
			支持“全新配置”模式进行配置：
				(a) make defconfig：基于内核为目标平台提供的“默认”配置进行配置；
				(b) make allnoconfig: 所有选项均回答为"no"；

		(2) 编译
			make [-j #]

			如何只编译内核中的一部分功能：
				(a) 只编译某子目录中的相关代码：
					# cd /usr/src/linux
					# make dir/

				(b) 只编译一个特定的模块：
					# cd /usr/src/linux
					# make dir/file.ko

					例如：只为e1000编译驱动：
						# make drivers/net/ethernet/intel/e1000/e1000.ko

			如何交叉编译内核：
				编译的目标平台与当前平台不相同；

				# make ARCH=arch_name

				要获取特定目标平台的使用帮助
				# make ARCH=arch_name help

	如何在已经执行过编译操作的内核源码树做重新编译：
		事先清理操作：
			# make clean：清理大多数编译生成的文件，但会保留config文件等；
			# make mrproper: 清理所有编译生成的文件、config及某些备份文件；
			# make distclean：mrproper、patches以及编辑器备份文件；

	screen命令：
		打开新的screen:
			# screen
		退出并关闭screen:
			# exit
		剥离当前screen:
			Ctrl+a,d
		显示所有已经打开的screen:
			screen -ls
		恢复某screen
			screen -r [SESSION]

CentOS系统安装

	bootloader-->kernel(initramfs)-->rootfs-->/sbin/init

	anaconda: 安装程序
		tui: 基于curses的文本窗口；
		gui：图形窗口；

	CentOS的安装程序启动过程：
		MBR：boot.cat
		stage2: isolinux/isolinux.bin
			配置文件：isolinux/isolinux.cfg

			每个对应的菜单选项：
				加载内核：isolinuz/vmlinuz
				向内核传递参数：append initrd=initrd.img ...

			装载根文件系统，并启动anaconda

			默认启动GUI接口
			若是显式指定使用TUI接口：
				向内核传递“text”参数即可；
					boot: linux text

		注意：上述内容一般应位于引导设备；而后续的anaconda及其安装用到的程序包等有几种方式可用：
			本地光盘
			本地硬盘
			ftp server: yum repository
			http server: yum repostory
			nfs server

			如果想手动指定安装源：
				boot: linux method

	anaconda应用的工作过程：
		安装前配置阶段
			安装过程使用的语言
			键盘类型
			安装目标存储设备
				Basic Storage：本地磁盘
				特种设备：iSCSI
			设定主机名
			配置网络接口
			时区
			管理员密码
			设定分区方式及MBR的安装位置					
			创建一个普通用户			
			选定要安装的程序包
		安装阶段
			在目标磁盘创建分区，执行格式化操作等
			将选定的程序包安装至目标位置
			安装bootloader
		首次启动
			iptables
			selinux
			core dump

	anaconda的配置方式：
		(1) 交互式配置方式；
		(2) 通过读取事先给定的配置文件自动完成配置；
			按特定语法给出的配置选项；
				kickstart文件；

	安装引导选项：
		boot: 
			text: 文本安装方式
			method: 手动指定使用的安装方法
			与网络相关的引导选项：
				ip=IPADDR
				netmask=MASK
				gateway=GW
				dns=DNS_SERVER_IP
				ifname=NAME:MAC_ADDR
			与远程访问功能相关的引导选项：
				vnc
				vncpassword='PASSWORD'
			指明kickstart文件的位置
				ks=
					DVD drive: ks=cdrom:/PATH/TO/KICKSTART_FILE
					Hard drive: ks=hd:/device/drectory/KICKSTART_FILE
					HTTP server: ks=http://host:port/path/to/KICKSTART_FILE
					FTP server: ks=ftp://host:port/path/to/KICKSTART_FILE
					HTTPS server: ks=https://host:port/path/to/KICKSTART_FILE
			启动紧急救援模式：
				rescue

			官方文档：《Installation Guide》

	kickstart文件的格式：
		命令段：指明各种安装前配置，如键盘类型等；
		程序包段：指明要安装的程序包组或程序包，不安装的程序包等；
			%packages
			@group_name
			package
			-package
			%end
		脚本段：
			%pre: 安装前脚本
				运行环境：运行于安装介质上的微型Linux环境

			%post: 安装后脚本
				运行环境：安装完成的系统；


		命令段中的命令：
			必备命令
				authconfig: 认证方式配置
					authconfig --useshadow  --passalgo=sha512
				bootloader：bootloader的安装位置及相关配置
					bootloader --location=mbr --driveorder=sda --append="crashkernel=auto crashkernel=auto rhgb rhgb quiet quiet"
				keyboard: 设定键盘类型
				lang: 语言类型
				part: 创建分区
				rootpw: 指明root的密码
				timezone: 时区



			可选命令
				install OR upgrade
				text: 文本安装界面
				network
				firewall
				selinux
				halt
				poweroff
				reboot
				repo
				user：安装完成后为系统创建新用户
				url: 指明安装源

		创建kickstart文件的方式：
			(1) 直接手动编辑；
				依据某模板修改；
			(2) 可使用创建工具：system-config-kickstart (CentOS 6)
				依据某模板修改并生成新配置；

			http://172.16.0.1/centos6.x86_64.cfg

		检查ks文件的语法错误：ksvalidator
			# ksvalidator /PATH/TO/KICKSTART_FILE

		创建引导光盘：
			# mkisofs -R -J -T -v --no-emul-boot --boot-load-size 4 --boot-info-table -V "CentOS 6.6 x86_64 boot" -b isolinux/isolinux.bin -c isolinux/boot.cat -o /root/boot.iso myiso/

回顾：CentOS系统安装

	kickstart文件：
		命令段
			必备：authconfig, bootloader, ...
			可选：firewall, selinux, reboot, ...
		程序包段
			%packages
			@group_name
			package
			-package
			%end
		脚本段
			%pre
			...
			%end

			%post
			...
			%end

	创建工具：system-config-kickstart
	语法检查：ksvalidator

	安装过程如何获取kickstart文件：
		DVD: ks=cdrom:/PATH/TO/KS_FILE
		HTTP: ks=http://HOST:PORT/PATH/TO/KS_FILE

SELinux：
	
	SELinux: Secure Enhanced Linux，工作于Linux内核中

	DAC：自主访问控制；
	MAC：强制访问控制；

	SELinux有两种工作级别：
		strict: 每个进程都受到selinux的控制；
		targeted: 仅有限个进程受到selinux控制；
			只监控容易被入侵的进程；

	sandbox：

		subject operation object

			subject: 进程
			object: 进程，文件，
				文件：open, read, write, close, chown, chmod

			subject: domain
			object: type

		SELinux为每个文件提供了安全标签，也为进程提供了安全标签；
			user:role:type
				user: SELinux的user; 
				role: 角色；
				type: 类型；

		SELinux规则库：
			规则：哪种域能访问哪种或哪些种类型内文件；

	配置SELinux:
		SELinux是否启用；
		给文件重新打标；
		设定某些布型特性；

		SELinux的状态：
			enforcing: 强制，每个受限的进程都必然受限；
			permissive: 启用，每个受限的进程违规操作不会被禁止，但会被记录于审计日志；
			disabled: 关闭；

			相关命令：
				getenforce: 获取selinux当前状态；
				setenforce 0|1
					0: 设置为permissive
					1: 设置为enforcing

				此设定：重启系统后无效；

				配置文件：/etc/sysconfig/selinux, /etc/selinux/config
					SELINUX={disabled|enforcing|permissive}

		给文件重新打标：
			chcon
		       chcon [OPTION]... CONTEXT FILE...
		       chcon [OPTION]... [-u USER] [-r ROLE] [-t TYPE] FILE...
		       chcon [OPTION]... --reference=RFILE FILE...

		    	-R：递归打标；

		还原文件的默认标签：
			restorecon [-R] /path/to/somewhere

		布尔型规则：
			getsebool
			setsebool

			getsebool命令：
				getsebool [-a] [boolean]

			setsebool命令：
				setsebool [ -P] boolean value | bool1=val1 bool2=val2 ...


bash脚本编程：

	编程语言：
		数据结构

		顺序执行
		选择执行
			条件测试
				运行命令或[[ EXPRESSION ]]
					执行状态返回值；
			if
			case
		循环执行
			将某代码段重复运行多次；
			重复运行多少次？
				循环次数事先已知：
				循环次数事先未知；

				必须有进入条件和退出条件：

			for, while, until

		函数：结构化编程及代码重用；
			function

	for循环语法:
		for NAME in LIST; do
			循环体
		done

		列表生成方式：
			(1) 整数列表
				{start..end}
				$(seq start [[step]end])
			(2) glob
				/etc/rc.d/rc3.d/K*
			(3) 命令

		通过ping命令探测172.16.250.1-254范围内的所有主机的在线状态；
			#!/bin/bash
			#
			net='172.16.250'
			uphosts=0
			downhosts=0

			for i in {1..20}; do
			    ping -c 1 -w 1 ${net}.${i} &> /dev/null
			    if [ $? -eq 0 ]; then
				echo "${net}.${i} is up."
			        let uphosts++
			    else
				echo "${net}.${i} is down."
			        let downhosts++
			    fi
			done
			    
			echo "Up hosts: $uphosts."
			echo "Down hosts: $downhosts."			

	while循环：
		while CONDITION; do
			循环体
		done

		CONDITION：循环控制条件；进入循环之前，先做一次判断；每一次循环之后会再次做判断；
			条件为“true”，则执行一次循环；直到条件测试状态为“false”终止循环；

			因此：CONDTION一般应该有循环控制变量；而此变量的值会在循环体不断地被修正；

		示例：求100以内所有正整数之和；
			#!/bin/bash
			#
			declare -i sum=0
			declare -i i=1

			while [ $i -le 100 ]; do
			    let sum+=$i
			    let i++
			done

			echo "$i"
			echo "Summary: $sum."

		练习：添加10个用户
			user1-user10

			#!/bin/bash
			#
			declare -i i=1
			declare -i users=0

			while [ $i -le 10 ]; do
			    if ! id user$i &> /dev/null; then
				useradd user$i
			  	echo "Add user: user$i."
			        let users++
			    fi
			    let i++
			done

			echo "Add $users users."			

		练习：通过ping命令探测172.16.250.1-254范围内的所有主机的在线状态；(用while循环)
			#!/bin/bash
			#
			declare -i i=1
			declare -i uphosts=0
			declare -i downhosts=0
			net='172.16.250'

			while [ $i -le 20 ]; do
			    if ping -c 1 -w 1 $net.$i &> /dev/null; then
					echo "$net.$i is up."
					let uphosts++
			    else
					echo "$net.$i is down."
					let downhosts++
			    fi
			    let i++
			done

			echo "Up hosts: $uphosts."
			echo "Down hosts: $downhosts."

		练习：打印九九乘法表；(分别使用for和while循环实现)
			#!/bin/bash
			#

			for j in {1..9}; do
			    for i in $(seq 1 $j); do
					echo -e -n "${i}X${j}=$[$i*$j]\t"
			    done
			    echo
			done			

			#!/bin/bash
			#
			declare -i i=1
			declare -i j=1

			while [ $j -le 9 ]; do
			    while [ $i -le $j ]; do
					echo -e -n "${i}X${j}=$[$i*$j]\t"
					let i++
			    done

			    echo
			    let i=1
			    let j++
			done

		练习：利用RANDOM生成10个随机数字，输出这个10数字，并显示其中的最大者和最小者；

			#!/bin/bash
			#
			declare -i max=0
			declare -i min=0
			declare -i i=1


			while [ $i -le 9 ]; do
			    rand=$RANDOM
			    echo $rand

			    if [ $i -eq 1 ]; then
					max=$rand
					min=$rand
			    fi

			    if [ $rand -gt $max ]; then
					max=$rand
			    fi
			    if [ $rand -lt $min ]; then
					min=$rand
			    fi
			    let i++
			done

			echo "MAX: $max."
			echo "MIN: $min."

回顾：selinux, while

	selinux: 内核，安全加强；
		开启：
			/etc/sysconfig/selinux, /etc/selinux/config
			# setenforce
			# getenforce

		打标：
			chcon [-t TYPE]
				-R

		布尔型：
			getsebool [-a]
			setsebool [-P]

	while循环：
		while CONDITION; do
			循环体
		done

sed：编辑器

	sed: Stream EDitor, 行编辑器；

	用法：
		sed [option]... 'script' inputfile...

			script: 
				'地址命令'

			常用选项：
				-n：不输出模式中的内容至屏幕；
				-e: 多点编辑；
				-f /PATH/TO/SCRIPT_FILE: 从指定文件中读取编辑脚本；
				-r: 支持使用扩展正则表达式；
				-i: 原处编辑；

			地址定界：
				(1) 不给地址：对全文进行处理；
				(2) 单地址：
					#: 指定的行；
					/pattern/：被此处模式所能够匹配到的每一行；
				(3) 地址范围：
					#,#
					#,+#
					/pat1/,/pat2/
					#,/pat1/
				(4) ~：步进
					1~2
					2~2

			编辑命令：
				d: 删除
				p: 显示模式空间中的内容
				a \text：在行后面追加文本；支持使用\n实现多行追加；
				i \text：在行前面插入文本；支持使用\n实现多行插入；
				c \text：替换行为单行或多行文本；
				w /path/to/somefile: 保存模式空间匹配到的行至指定文件中；
				r /path/from/somefile：读取指定文件的文本流至模式空间中匹配到的行的行后；
				=: 为模式空间中的行打印行号；
				!: 取反条件; 
				s///：支持使用其它分隔符，s@@@，s###；
					替换标记：
						g: 行内全局替换；
						p: 显示替换成功的行；
						w /PATH/TO/SOMEFILE：将替换成功的结果保存至指定文件中；

					练习1：删除/boot/grub/grub.conf文件中所有以空白开头的行行首的空白字符；
						~]# sed 's@^[[:space:]]\+@@' /etc/grub2.cfg

					练习2：删除/etc/fstab文件中所有以#开头，后面至少跟一个空白字符的行的行首的#和空白字符；
						~]# sed 's@^#[[:space:]]\+@@' /etc/fstab

					练习3：echo一个绝对路径给sed命令，取出其基名；取出其目录名；
						~]# echo "/etc/sysconfig/" | sed 's@[^/]\+/\?$@@'

				高级编辑命令：
					h: 把模式空间中的内容覆盖至保持空间中；
					H：把模式空间中的内容追加至保持空间中；
					g: 从保持空间取出数据覆盖至模式空间；
					G：从保持空间取出内容追加至模式空间；
					x: 把模式空间中的内容与保持空间中的内容进行互换；
					n: 读取匹配到的行的下一行至模式空间；
					N：追加匹配到的行的下一行至模式空间；
					d: 删除模式空间中的行；
					D：删除多行模式空间中的所有行；

					sed -n 'n;p' FILE：显示偶数行
					sed '1!G;h;$!d' FILE：逆向显示文件内容
					sed '$!N;$!D' FILE: 取出文件后两行；
					sed '$!d' FILE：取出文件最后一行；
					sed 'G' FILE: 
					sed '/^$/d;G' FILE: 
					sed 'n;d' FILE: 显示奇数行；
					sed -n '1!G;h;$p' FILE: 逆向显示文件中的每一行；

bash脚本编程

	while CONDITION; do
		循环体
	done

	进入条件：CONDITION为true；
	退出条件：false

	until CONDITION; do
		循环体
	done

	进入条件：false
	退出条件：true

		示例：求100以内所正整数之和；
			#!/bin/bash
			#
			declare -i i=1
			declare -i sum=0

			until [ $i -gt 100 ]; do
			    let sum+=$i
			    let i++
			done

			echo "Sum: $sum"	

		示例：打印九九乘法表
			#!/bin/bash
			#
			declare -i j=1
			declare -i i=1

			until [ $j -gt 9 ]; do
			    until [ $i -gt $j ]; do
					echo -n -e "${i}X${j}=$[$i*$j]\t"
			        let i++
			    done
			    echo
			    let i=1
			    let j++
			done

	循环控制语句（用于循环体中）：
		continue [N]：提前结束第N层的本轮循环，而直接进入下一轮判断；
			while CONDTIITON1; do
				CMD1
				...
				if CONDITION2; then
					continue
				fi
				CMDn
				...
			done

		break [N]：提前结束循环；					
			while CONDTIITON1; do
				CMD1
				...
				if CONDITION2; then
					break
				fi
				CMDn
				...
			done

		示例1：求100以内所有偶数之和；要求循环遍历100以内的所正整数；
			#!/bin/bash
			#
			declare -i i=0
			declare -i sum=0

			until [ $i -gt 100 ]; do
			    let i++
			    if [ $[$i%2] -eq 1 ]; then
					continue
			    fi
			    let sum+=$i
			done

			echo "Even sum: $sum"

	创建死循环：
		while true; do
			循环体
		done

		until false; do
			循环体
		done

		示例2：每隔3秒钟到系统上获取已经登录的用户的信息；如果docker登录了，则记录于日志中，并退出；
			#!/bin/bash
			#
			read -p "Enter a user name: " username

			while true; do
			    if who | grep "^$username" &> /dev/null; then
					break
			    fi
			    sleep 3
			done

			echo "$username logged on." >> /tmp/user.log 					

			第二种实现：
			#!/bin/bash
			#
			read -p "Enter a user name: " username

			until who | grep "^$username" &> /dev/null; do
			    sleep 3
			done

			echo "$username logged on." >> /tmp/user.log

	while循环的特殊用法（遍历文件的每一行）：

		while read line; do
			循环体
		done < /PATH/FROM/SOMEFILE

		依次读取/PATH/FROM/SOMEFILE文件中的每一行，且将行赋值给变量line: 

		示例：找出其ID号为偶数的所有用户，显示其用户名及ID号；
			#!/bin/bash

			while read line;do
			        if [ $[`echo $line | cut -d: -f3` % 2] -eq 0 ];then
			                echo -e -n "username: `echo $line | cut -d: -f1`\t"
			                echo "uid: `echo $line | cut -d: -f3 `"
			        fi
			done < /etc/passwd		

	for循环的特殊格式：
		for ((控制变量初始化;条件判断表达式;控制变量的修正表达式)); do
			循环体
		done

		控制变量初始化：仅在运行到循环代码段时执行一次；
		控制变量的修正表达式：每轮循环结束会先进行控制变量修正运算，而后再做条件判断；

		示例：求100以内所正整数之和；
			#!/bin/bash
			#
			declare -i sum=0

			for ((i=1;i<=100;i++)); do
			    let sum+=$i
			done

			echo "Sum: $sum."

		示例2：打印九九乘法表；
			#!/bin/bash
			#

			for((j=1;j<=9;j++));do
			        for((i=1;i<=j;i++))do
			            echo -e -n "${i}X${j}=$[$i*$j]\t"
			        done
			        echo
			done

		练习：写一个脚本，完成如下任务
			(1) 显示一个如下菜单：
				cpu) show cpu information;
				mem) show memory information;
				disk) show disk information;
				quit) quit
			(2) 提示用户选择选项；
			(3) 显示用户选择的内容；

				#!/bin/bash
				#
				cat << EOF
				cpu) show cpu information;
				mem) show memory information;
				disk) show disk information;
				quit) quit
				============================
				EOF

				read -p "Enter a option: " option
				while [ "$option" != 'cpu' -a "$option" != 'mem' -a "$option" != 'disk' -a "$option" != 'quit' ]; do
				    read -p "Wrong option, Enter again: " option
				done

				if [ "$option" == 'cpu' ]; then
				    lscpu
				elif [ "$option" == 'mem' ]; then
				    cat /proc/meminfo
				elif [ "$option" == 'disk' ]; then
				    fdisk -l
				else
				    echo "Quit"
				    exit 0
				fi

			进一步地：
				用户选择，并显示完成后不退出脚本；而是提示用户继续选择显示其它内容；直到使用quit方始退出；

	条件判断：case语句
		case 变量引用 in
		PAT1)
			分支1
			;;
		PAT2)
			分支2
			;;
		...
		*)
			默认分支
			;;
		esac

		示例：

			#!/bin/bash
			#
			cat << EOF
			cpu) show cpu information;
			mem) show memory information;
			disk) show disk information;
			quit) quit
			============================
			EOF

			read -p "Enter a option: " option
			while [ "$option" != 'cpu' -a "$option" != 'mem' -a "$option" != 'disk' -a "$option" != 'quit' ]; do
			    read -p "Wrong option, Enter again: " option
			done

			case "$option" in
			cpu)
				lscpu 
				;;
			mem)
				cat /proc/meminfo
				;;
			disk)
				fdisk -l
				;;
			*)
				echo "Quit..."
				exit 0
				;;
			esac

		练习：写一个脚本，完成如下要求
			(1) 脚本可接受参数：start, stop, restart, status; 
			(2) 如果参数非此四者之一，提示使用格式后报错退出；
			(3) 如果是start：则创建/var/lock/subsys/SCRIPT_NAME, 并显示“启动成功”；
				考虑：如果事先已经启动过一次，该如何处理？
			(4) 如果是stop：则删除/var/lock/subsys/SCRIPT_NAME, 并显示“停止完成”；
				考虑：如果事先已然停止过了，该如何处理？
			(5) 如果是restart，则先stop, 再start；
				考虑：如果本来没有start，如何处理？
			(6) 如果是status, 则
				如果/var/lock/subsys/SCRIPT_NAME文件存在，则显示“SCRIPT_NAME is running...”；
				如果/var/lock/subsys/SCRIPT_NAME文件不存在，则显示“SCRIPT_NAME is stopped...”；

			其中：SCRIPT_NAME为当前脚本名；

	总结：until, while, for, case

回顾：sed命令、bash脚本编程
	
	sed命令：
		sed [options] 'SCRIPT' FILE...

			编辑命令：d, p, w, r, a, i, c, s, =, 
				n, N, h, H, g, G, p, P, x, D

	bash脚本编程：
		while, for, case, until

bash脚本编程：

	case语句：
		case 变量引用 in
		PAT1)
			分支1
			;;
		PAT2)
			分支2
			;;
		...
		*)
			分支n
			;;
		esac

		case支持glob风格的通配符：
			*: 任意长度任意字符；
			?: 任意单个字符；
			[]：指定范围内的任意单个字符；
			a|b: a或b

	function：函数
		过程式编程：代码重用
			模块化编程
			结构化编程

		语法一：
			function f_name {
				...函数体...
			} 

		语法二：
			f_name() {
				...函数体...
			}

		调用：函数只有被调用才会执行；
			调用：给定函数名
				函数名出现的地方，会被自动替换为函数代码；

			函数的生命周期：被调用时创建，返回时终止；
				return命令返回自定义状态结果；
					0：成功
					1-255：失败

			#!/bin/bash
			#
			function adduser {
			   if id $username &> /dev/null; then
			        echo "$username exists."
			        return 1
			   else
			        useradd $username
			        [ $? -eq 0 ] && echo "Add $username finished." && return 0
			   fi
			}

			for i in {1..10}; do
			    username=myuser$i
			    adduser
			done

			示例：服务脚本
				#!/bin/bash
				#
				# chkconfig: - 88 12
				# description: test service script
				#
				prog=$(basename $0)
				lockfile=/var/lock/subsys/$prog

				start() {
				    if [ -e $lockfile ]; then
					echo "$prog is aleady running."
					return 0
				    else
					touch $lockfile
					[ $? -eq 0 ] && echo "Starting $prog finished."
				    fi
				}

				stop() {
				    if [ -e $lockfile ]; then
					rm -f $lockfile && echo "Stop $prog ok."
				    else
					echo "$prog is stopped yet."
				    fi
				}

				status() {
				    if [ -e $lockfile ]; then
					echo "$prog is running."
				    else
					echo "$prog is stopped."
				    fi
				}

				usage() {
				    echo "Usage: $prog {start|stop|restart|status}"
				}

				if [ $# -lt 1 ]; then
				    usage
				    exit 1
				fi    

				case $1 in
				start)
					start
					;;
				stop)
					stop
					;;
				restart)
					stop
					start
					;;
				status)
					status
					;;
				*)
					usage
				esac	
			
			练习：打印九九乘法表，利用函数实现；

		函数返回值：
			函数的执行结果返回值：
				(1) 使用echo或print命令进行输出；
				(2) 函数体中调用命令的执行结果；
			函数的退出状态码：
				(1) 默认取决于函数体中执行的最后一条命令的退出状态码；
				(2) 自定义退出状态码：
					return

		函数可以接受参数：
			传递参数给函数：调用函数时，在函数名后面以空白分隔给定参数列表即可；例如“testfunc arg1 arg2 ...”

			在函数体中当中，可使用$1, $2, ...调用这些参数；还可以使用$@, $*, $#等特殊变量；

			示例：添加10个用户		
				#!/bin/bash
				#
				function adduser {
				   if [ $# -lt 1 ]; then
					return 2
					# 2: no arguments
				   fi

				   if id $1 &> /dev/null; then
					echo "$1 exists."
					return 1
				   else	
					useradd $1
					[ $? -eq 0 ] && echo "Add $1 finished." && return 0
				   fi
				}

				for i in {1..10}; do
				    adduser myuser$i
				done

			练习：打印NN乘法表，使用函数实现；

		变量作用域：
			本地变量：当前shell进程；为了执行脚本会启动专用的shell进程；因此，本地变量的作用范围是当前shell脚本程序文件；
			局部变量：函数的生命周期；函数结束时变量被自动销毁；
				如果函数中有局部变量，其名称同本地变量；

			在函数中定义局部变量的方法：
				local NAME=VALUE

		函数递归：
			函数直接或间接调用自身；

			N!=N(n-1)(n-2)...1
				n(n-1)! = n(n-1)(n-2)!
			
				#!/bin/bash
				#
				fact() {
				    if [ $1 -eq 0 -o $1 -eq 1 ]; then
						echo 1
				    else
						echo $[$1*$(fact $[$1-1])]
				    fi
				}

				fact 5	

			练习：求n阶斐波那契数列；
				#!/bin/bash
				#
				fab() {
				    if [ $1 -eq 1 ]; then
					echo 1
				    elif [ $1 -eq 2 ]; then
					echo 1
				    else
					echo $[$(fab $[$1-1])+$(fab $[$1-2])]
				    fi
				}

				fab 7	



Systemd：
	
	POST --> Boot Sequence --> Bootloader --> kernel + initramfs(initrd) --> rootfs --> /sbin/init
		init:
			CentOS 5: SysV init
			CentOS 6: Upstart
			CentOS 7: Systemd

	Systemd新特性：
		系统引导时实现服务并行启动；
		按需激活进程；
		系统状态快照；
		基于依赖关系定义服务控制逻辑；

	核心概念：unit
		配置文件进行标识和配置；文件中主要包含了系统服务、监听socket、保存的系统快照以及其它与init相关的信息；
		保存至：
			/usr/lib/systemd/system
			/run/systemd/system
			/etc/systemd/system

	Unit的类型：
		Service unit: 文件扩展名为.service, 用于定义系统服务；
		Target unit: 文件扩展名为.target，用于模拟实现“运行级别”；
		Device unit: .device, 用于定义内核识别的设备；
		Mount unit: .mount, 定义文件系统挂载点；
		Socket unit: .socket, 用于标识进程间通信用的socket文件；
		Snapshot unit: .snapshot, 管理系统快照；
		Swap unit: .swap, 用于标识swap设备；
		Automount unit: .automount，文件系统的自动挂载点；
		Path unit: .path，用于定义文件系统中的一个文件或目录；

	关键特性：
		基于socket的激活机制：socket与服务程序分离；
		基于bus的激活机制：
		基于device的激活机制：
		基于path的激活机制：
		系统快照：保存各unit的当前状态信息于持久存储设备中；
		向后兼容sysv init脚本；

	不兼容：
		systemctl命令固定不变
		非由systemd启动的服务，systemctl无法与之通信

	管理系统服务：
		CentOS 7: service unit
			注意：能兼容早期的服务脚本

			命令：systemctl COMMAND name.service

		启动：service name start ==> systemctl start name.service
		停止：service name stop ==> systemctl stop name.service
		重启：service name restart ==> systemctl restart name.service
		状态：service name status ==> systemctl status name.service
		条件式重启：service name condrestart ==> systemctl try-restart name.service
		重载或重启服务：systemctl reload-or-restart name.service
		重载或条件式重启服务：systemctl reload-or-try-restart name.service
		禁止设定为开机自启：systemctl mask name.service
		取消禁止设定为开机自启：systemctl unmask name.service


		查看某服务当前激活与否的状态：systemctl is-active name.service
		查看所有已经激活的服务：
			systemctl list-units --type service 
		查看所有服务：
			systemctl list-units --type service --all

		chkconfig命令的对应关系：
			设定某服务开机自启：chkconfig name on ==> systemctl enable name.service
			禁止：chkconfig name off ==> systemctl disable name.service
			查看所有服务的开机自启状态：
				chkconfig --list ==> systemctl list-unit-files --type service 
			查看服务是否开机自启：systemctl is-enabled name.service

		其它命令：
			查看服务的依赖关系：systemctl list-dependencies name.service


	target units：
		unit配置文件：.target

		运行级别：
			0  ==> runlevel0.target, poweroff.target
			1  ==> runlevel1.target, rescue.target
			2  ==> runlevel2.target, multi-user.target
			3  ==> runlevel3.target, multi-user.target
			4  ==> runlevel4.target, multi-user.target
			5  ==> runlevel5.target, graphical.target
			6  ==> runlevel6.target, reboot.target

		级别切换：
			init N ==> systemctl isolate name.target

		查看级别：
			runlevel ==> systemctl list-units --type target

		获取默认运行级别：
			/etc/inittab ==> systemctl get-default

		修改默认级别：
			/etc/inittab ==> systemctl set-default name.target

		切换至紧急救援模式：
			systemctl rescue

		切换至emergency模式：
			systemctl emergency

	其它常用命令：
		关机：systemctl halt、systemctl poweroff
		重启：systemctl reboot
		挂起：systemctl suspend
		快照：systemctl hibernate
		快照并挂起：systemctl hybrid-sleep


回顾: bash脚本编程、systemd

	函数：模块化编程
		function f_name {
			...函数体...
		}

		f_name() {
			...函数体...
		}

		return命令；
		参数：
			函数体中调用参数：$1, $2, ...
			$*, $@, $#
		向函数传递参数：
			函数名 参数列表

	systemd：系统及服务
		unit: 
			类型：service, target
				.service, .target
		systemctl 

bash脚本编程：

	数组：
		变量：存储单个元素的内存空间；
		数组：存储多个元素的连续的内存空间；
			数组名
			索引：编号从0开始，属于数值索引；
				注意：索引也可支持使用自定义的格式，而不仅仅是数值格式；
					  bash的数组支持稀疏格式；

			引用数组中的元素：${ARRAY_NAME[INDEX]}

		声明数组：
			declare -a ARRAY_NAME
			declare -A ARRAY_NAME: 关联数组；

		数组元素的赋值：
			(1) 一次只赋值一个元素；
				ARRAY_NAME[INDEX]=VALUE
					weekdays[0]="Sunday"
					weekdays[4]="Thursday"
			(2) 一次赋值全部元素：
				ARRAY_NAME=("VAL1" "VAL2" "VAL3" ...)
			(3) 只赋值特定元素：
				ARRAY_NAME=([0]="VAL1" [3]="VAL2" ...)
			(4) read -a ARRAY

		引用数组元素：${ARRAY_NAME[INDEX]}
			注意：省略[INDEX]表示引用下标为0的元素；

		数组的长度(数组中元素的个数)：${#ARRAY_NAME[*]}, ${#ARRAY_NAME[@]}

			示例：生成10个随机数保存于数组中，并找出其最大值和最小值；
				#!/bin/bash
				#
				declare -a rand
				declare -i max=0

				for i in {0..9}; do
				    rand[$i]=$RANDOM
				    echo ${rand[$i]}
				    [ ${rand[$i]} -gt $max ] && max=${rand[$i]}
				done

				echo "Max: $max"

			练习：写一个脚本
				定义一个数组，数组中的元素是/var/log目录下所有以.log结尾的文件；要统计其下标为偶数的文件中的行数之和；

					#!/bin/bash
					#
					declare -a files
					files=(/var/log/*.log)
					declare -i lines=0

					for i in $(seq 0 $[${#files[*]}-1]); do
					    if [ $[$i%2] -eq 0 ];then
						let lines+=$(wc -l ${files[$i]} | cut -d' ' -f1) 
					    fi
					done

					echo "Lines: $lines."	

		引用数组中的元素：
			所有元素：${ARRAY[@]}, ${ARRAY[*]}

			数组切片：${ARRAY[@]:offset:number}
				offset: 要跳过的元素个数
				number: 要取出的元素个数，取偏移量之后的所有元素：${ARRAY[@]:offset}；

		向数组中追加元素：
			ARRAY[${#ARRAY[*]}]

		删除数组中的某元素：
			unset ARRAY[INDEX]

		关联数组：
			declare -A ARRAY_NAME
			ARRAY_NAME=([index_name1]='val1' [index_name2]='val2' ...)

		练习：生成10个随机数，升序或降序排序；

	bash的字符串处理工具：
		字符串切片：
			${var:offset:number}
			取字符串的最右侧几个字符：${var: -lengh}
				注意：冒号后必须有一空白字符；

		基于模式取子串：
			${var#*word}：其中word可以是指定的任意字符；功能：自左而右，查找var变量所存储的字符串中，第一次出现的word, 删除字符串开头至第一次出现word字符之间的所有字符；
			${var##*word}：同上，不过，删除的是字符串开头至最后一次由word指定的字符之间的所有内容；
				file="/var/log/messages"
				${file##*/}: messages

			${var%word*}：其中word可以是指定的任意字符；功能：自右而左，查找var变量所存储的字符串中，第一次出现的word, 删除字符串最后一个字符向左至第一次出现word字符之间的所有字符；
				file="/var/log/messages"
				${file%/*}: /var/log

			${var%%word*}：同上，只不过删除字符串最右侧的字符向左至最后一次出现word字符之间的所有字符；

			示例：url=http://www.magedu.com:80
				${url##*:}
				${url%%:*}

		查找替换：
			${var/pattern/substi}：查找var所表示的字符串中，第一次被pattern所匹配到的字符串，以substi替换之；
			${var//pattern/substi}: 查找var所表示的字符串中，所有能被pattern所匹配到的字符串，以substi替换之；

			${var/#pattern/substi}：查找var所表示的字符串中，行首被pattern所匹配到的字符串，以substi替换之；
			${var/%pattern/substi}：查找var所表示的字符串中，行尾被pattern所匹配到的字符串，以substi替换之；

		查找并删除：
			${var/pattern}：查找var所表示的字符串中，删除第一次被pattern所匹配到的字符串
			${var//pattern}：
			${var/#pattern}：
			${var/%pattern}：

		字符大小写转换：
			${var^^}：把var中的所有小写字母转换为大写；
			${var,,}：把var中的所有大写字母转换为小写；

		变量赋值：
			${var:-value}：如果var为空或未设置，那么返回value；否则，则返回var的值；
			${var:=value}：如果var为空或未设置，那么返回value，并将value赋值给var；否则，则返回var的值；

			${var:+value}：如果var不空，则返回value；
			${var:?error_info}：如果var为空或未设置，那么返回error_info；否则，则返回var的值；

	为脚本程序使用配置文件：
		(1) 定义文本文件，每行定义“name=value”
		(2) 在脚本中source此文件即可

	命令：
		mktemp命令：
			mktemp [OPTION]... [TEMPLATE]
				TEMPLATE: filename.XXX
					XXX至少要出现三个；
				OPTION：
					-d: 创建临时目录；
					--tmpdir=/PATH/TO/SOMEDIR：指明临时文件目录位置；

		install命令：
	       install [OPTION]... [-T] SOURCE DEST
	       install [OPTION]... SOURCE... DIRECTORY
	       install [OPTION]... -t DIRECTORY SOURCE...
	       install [OPTION]... -d DIRECTORY...
	       		选项：
	       			-m MODE
	       			-o OWNER
	       			-g GROUP

	练习：写一个脚本
		(1) 提示用户输入一个可执行命令名称；
		(2) 获取此命令所依赖到的所有库文件列表；
		(3) 复制命令至某目标目录(例如/mnt/sysroot)下的对应路径下；
			/bin/bash ==> /mnt/sysroot/bin/bash
			/usr/bin/passwd ==> /mnt/sysroot/usr/bin/passwd
		(4) 复制此命令依赖到的所有库文件至目标目录下的对应路径下：
			/lib64/ld-linux-x86-64.so.2 ==> /mnt/sysroot/lib64/ld-linux-x86-64.so.2

		进一步地：
			每次复制完成一个命令后，不要退出，而是提示用户键入新的要复制的命令，并重复完成上述功能；直到用户输入quit退出；


回顾：bash脚本编程数组

	数组，字符串处理

	数组：
		数组：declare -a 
			index: 0-
		关联数组: declare -A

	字符串处理：
		切片、查找替换、查找删除、变量赋值

GNU awk：
	
	文本处理三工具：grep, sed, awk
		grep, egrep, fgrep：文本过滤工具；pattern
		sed: 行编辑器
			模式空间、保持空间
		awk：报告生成器，格式化文本输出；

		AWK: Aho, Weinberger, Kernighan --> New AWK, NAWK

		GNU awk, gawk

	gawk - pattern scanning and processing language

		基本用法：gawk [options] 'program' FILE ...
			program: PATTERN{ACTION STATEMENTS}
				语句之间用分号分隔

				print, printf

			选项：
				-F：指明输入时用到的字段分隔符；
				-v var=value: 自定义变量；

		1、print

			print item1, item2, ...

			要点：
				(1) 逗号分隔符；
				(2) 输出的各item可以字符串，也可以是数值；当前记录的字段、变量或awk的表达式；
				(3) 如省略item，相当于print $0; 

		2、变量
			2.1 内建变量
				FS：input field seperator，默认为空白字符；
				OFS：output field seperator，默认为空白字符；
				RS：input record seperator，输入时的换行符；
				ORS：output record seperator，输出时的换行符；

				NF：number of field，字段数量
					{print NF}, {print $NF}
				NR：number of record, 行数；
				FNR：各文件分别计数；行数；

				FILENAME：当前文件名；

				ARGC：命令行参数的个数；
				ARGV：数组，保存的是命令行所给定的各参数；

			2.2 自定义变量
				(1) -v var=value

					变量名区分字符大小写；

				(2) 在program中直接定义

		3、printf命令

			格式化输出：printf FORMAT, item1, item2, ...

				(1) FORMAT必须给出; 
				(2) 不会自动换行，需要显式给出换行控制符，\n
				(3) FORMAT中需要分别为后面的每个item指定一个格式化符号；

				格式符：
					%c: 显示字符的ASCII码；
					%d, %i: 显示十进制整数；
					%e, %E: 科学计数法数值显示；
					%f：显示为浮点数；
					%g, %G：以科学计数法或浮点形式显示数值；
					%s：显示字符串；
					%u：无符号整数；
					%%: 显示%自身；

				修饰符：
					#[.#]：第一个数字控制显示的宽度；第二个#表示小数点后的精度；
						%3.1f
					-: 左对齐
					+：显示数值的符号

		4、操作符

			算术操作符：
				x+y, x-y, x*y, x/y, x^y, x%y
				-x
				+x: 转换为数值；

			字符串操作符：没有符号的操作符，字符串连接

			赋值操作符：
				=, +=, -=, *=, /=, %=, ^=
				++, --

			比较操作符：
				>, >=, <, <=, !=, ==

			模式匹配符：
				~：是否匹配
				!~：是否不匹配

			逻辑操作符：
				&&
				||
				!

			函数调用：
				function_name(argu1, argu2, ...)

			条件表达式：
				selector?if-true-expression:if-false-expression

				# awk -F: '{$3>=1000?usertype="Common User":usertype="Sysadmin or SysUser";printf "%15s:%-s\n",$1,usertype}' /etc/passwd

		5、PATTERN

			(1) empty：空模式，匹配每一行；
			(2) /regular expression/：仅处理能够被此处的模式匹配到的行；
			(3) relational expression: 关系表达式；结果有“真”有“假”；结果为“真”才会被处理；
				真：结果为非0值，非空字符串；
			(4) line ranges：行范围，
				startline,endline：/pat1/,/pat2/

				注意： 不支持直接给出数字的格式
				~]# awk -F: '(NR>=2&&NR<=10){print $1}' /etc/passwd
			(5) BEGIN/END模式
				BEGIN{}: 仅在开始处理文件中的文本之前执行一次；
				END{}：仅在文本处理完成之后执行一次；

		6、常用的action

			(1) Expressions
			(2) Control statements：if, while等；
			(3) Compound statements：组合语句；
			(4) input statements
			(5) output statements

		7、控制语句

			if(condition) {statments} 
			if(condition) {statments} else {statements}
			while(conditon) {statments}
			do {statements} while(condition)
			for(expr1;expr2;expr3) {statements}
			break
			continue
			delete array[index]
			delete array
			exit 
			{ statements }

			7.1 if-else

				语法：if(condition) statement [else statement]

				~]# awk -F: '{if($3>=1000) {printf "Common user: %s\n",$1} else {printf "root or Sysuser: %s\n",$1}}' /etc/passwd

				~]# awk -F: '{if($NF=="/bin/bash") print $1}' /etc/passwd

				~]# awk '{if(NF>5) print $0}' /etc/fstab

				~]# df -h | awk -F[%] '/^\/dev/{print $1}' | awk '{if($NF>=20) print $1}'

				使用场景：对awk取得的整行或某个字段做条件判断；

			7.2 while循环
				语法：while(condition) statement
					条件“真”，进入循环；条件“假”，退出循环；

				使用场景：对一行内的多个字段逐一类似处理时使用；对数组中的各元素逐一处理时使用；

				~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF) {print $i,length($i); i++}}' /etc/grub2.cfg

				~]# awk '/^[[:space:]]*linux16/{i=1;while(i<=NF) {if(length($i)>=7) {print $i,length($i)}; i++}}' /etc/grub2.cfg

			7.3 do-while循环
				语法：do statement while(condition)
					意义：至少执行一次循环体

			7.4 for循环
				语法：for(expr1;expr2;expr3) statement

					for(variable assignment;condition;iteration process) {for-body}

				~]# awk '/^[[:space:]]*linux16/{for(i=1;i<=NF;i++) {print $i,length($i)}}' /etc/grub2.cfg

				特殊用法：
					能够遍历数组中的元素；
						语法：for(var in array) {for-body}

			7.5 switch语句
				语法：switch(expression) {case VALUE1 or /REGEXP/: statement; case VALUE2 or /REGEXP2/: statement; ...; default: statement}

			7.6 break和continue
				break [n]
				continue

			7.7 next

				提前结束对本行的处理而直接进入下一行；

				~]# awk -F: '{if($3%2!=0) next; print $1,$3}' /etc/passwd

		8、array

			关联数组：array[index-expression]

				index-expression:
					(1) 可使用任意字符串；字符串要使用双引号；
					(2) 如果某数组元素事先不存在，在引用时，awk会自动创建此元素，并将其值初始化为“空串”；

					若要判断数组中是否存在某元素，要使用"index in array"格式进行；

					weekdays[mon]="Monday"

				若要遍历数组中的每个元素，要使用for循环；
					for(var in array) {for-body}

					~]# awk 'BEGIN{weekdays["mon"]="Monday";weekdays["tue"]="Tuesday";for(i in weekdays) {print weekdays[i]}}'

					注意：var会遍历array的每个索引；
					state["LISTEN"]++
					state["ESTABLISHED"]++

					~]# netstat -tan | awk '/^tcp\>/{state[$NF]++}END{for(i in state) { print i,state[i]}}'

					~]# awk '{ip[$1]++}END{for(i in ip) {print i,ip[i]}}' /var/log/httpd/access_log

					练习1：统计/etc/fstab文件中每个文件系统类型出现的次数；
					~]# awk '/^UUID/{fs[$3]++}END{for(i in fs) {print i,fs[i]}}' /etc/fstab

					练习2：统计指定文件中每个单词出现的次数；
					~]# awk '{for(i=1;i<=NF;i++){count[$i]++}}END{for(i in count) {print i,count[i]}}' /etc/fstab

		9、函数

			9.1 内置函数
				数值处理：
					rand()：返回0和1之间一个随机数；

				字符串处理：
					length([s])：返回指定字符串的长度；
					sub(r,s,[t])：以r表示的模式来查找t所表示的字符中的匹配的内容，并将其第一次出现替换为s所表示的内容；
					gsub(r,s,[t])：以r表示的模式来查找t所表示的字符中的匹配的内容，并将其所有出现均替换为s所表示的内容；

					split(s,a[,r])：以r为分隔符切割字符s，并将切割后的结果保存至a所表示的数组中；

					~]# netstat -tan | awk '/^tcp\>/{split($5,ip,":");count[ip[1]]++}END{for (i in count) {print i,count[i]}}'

			9.2 自定义函数

				《sed和awk》

		

























		



























































    			




















				







	










		

























































































































































		

DNF新一代的RPM软件包管理器。他首先出现在 Fedora 18 这个发行版中。而最近，他取代了YUM，正式成为 Fedora 22 的包管理器。

DNF包管理器克服了YUM包管理器的一些瓶颈，提升了包括用户体验，内存占用，依赖分析，运行速度等多方面的内容。

DNF使用 RPM, libsolv 和 hawkey 库进行包管理操作。尽管它没有预装在 CentOS 和 RHEL 7 中，但你可以在使用 YUM 的同时使用 DNF 。

DNF 的最新稳定发行版版本号是 1.0，发行日期是2015年5月11日。 这一版本的额 DNF 包管理器（包括在他之前的所有版本） 都大部分采用 Python 编写，发行许可为GPL v2.


Dependency resolution of YUM is a nightmare and was resolved in DNF with SUSE library ‘libsolv’ and Python wrapper along with C Hawkey.
YUM don’t have a documented API.
Building new features are difficult.
No support for extensions other than Python.
Lower memory reduction and less automatic synchronization of metadata – a time taking process.
































