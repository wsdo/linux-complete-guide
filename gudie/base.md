<!--
 * @Author: starkwang
 * @Contact me: https://shudong.wang/about
 * @Date: 2019-10-18 15:05:26
 * @LastEditors: starkwang
 * @LastEditTime: 2019-10-18 15:05:26
 * @Description: file content
 -->


介绍课程：
	中级：
		初级：系统基础
		中级：系统管理、服务安全及服务管理、Shell脚本；
	高级：
		MySQL数据库；
			cache & storage
		集群：
			Cluster

			lb:
				4layer
				7layer
			ha:
		分布式：
			zookeeper
			分布式文件系统
		虚拟化技术：
			xen
			kvm

		Openstack：IAAS云；

		运维工具：
			ansible
			puppet(ruby), saltstack(python)

		监控工具：
			zabbix

		大数据处理：
			hadoop
			spark, storm

			elk: elasticsearch, logstash, kibana

		docker：

		Python：

		一万小时定律：

	认证：
		RedHat:
			RHCSA
			RHCE
			RHCA

	培训：
		加速知识获取过程；
		有人监督，有环境；

纪律：
	1、迟到
		1分钟：2个；

		请假一晌：60个；

	2、教室使用
		全天开放；
			拒绝玩游戏：

	3、上课期间
		手机铃声关闭；
			一次：30个；

作业和考试：
	作业：
		课前提问
		博客作业
			5w1h:
				what, why, when, where, who
				how

	考试：
		机试、笔试
		及格：80分
			一分：5个


上课环境：
	172.16.0.0/16
		Windows: 172.16.250.X
		Linux: 172.16.249.X
			X: 1-254

		网关：172.16.0.1
		DNS: 172.16.0.1

		每位同学：172.16.Y.1-254
				  172.16.100+Y.1-254

	Server: 172.16.0.1, 192.168.0.254, 192.168.1.254
		允许核心转发；
	
		DHCP、ftp、http、cobbler


VMware Workstation的使用：

	现代计算机设备的组成部分：
		运算器、控制器、存储器、输入设备、输出设备
			CPU
			bus：总线
			memory: 编址存储设备
	
			read ahead

		IO: 与外部部件交互
			磁盘：
			网卡：

	虚拟机：虚拟计算机


	CPU：运算器、控制器
		CPU指令，指令集；
			特权指令：OS运行特权指令；
			普通指令：
		程序员：

	OS: Operating System
		软件程序
		通用目的
			硬件驱动
			进程管理
			内存管理
			网络管理
			安全管理

		System Call
			Syscall：系统调用

		编程层次：
			硬件规格：hardware specifiacation
			系统调用：
			库调用：library call

	UI:
		GUI: Graphic User Interface
		CLI: Command Line Interface

	ABI: Application Binary Interface
	API：Application Programming Interface

	CPU架构类型：
		x86
		x64
		arm

		m68000, m68k
		power
		powerpc
		ultrasparc
		alpha
		安腾

	Windows
	Linux：
		Linus --> Linux

		GNU/Linux

	Unix：
		System （Bell Lab）
			AIX （IBM）
			Solaris (SUN)
			HP-UX (HP)
		BSD: （BSRG）Berkeley System Distribution
			NetBSD
			OpenBSD
			FreeBSD

	MIT：Richard Stallman
		GNU: GNU is Not Unix；
			GPL：General Public License

	
计算机的基础知识：
	CPU, memory, I/O

	程序运行模式：
		用户空间：user space, us
		内核空间：system space

	POS: Portable Operating System
		POSIX

		API: 编程接口；

	运行程序格式：
		Windows: EXE, dll(dynamic link library)
		Linux: ELF, so(shared object)

		程序：指令+数据
			指令：只读
			数据：读写

		程序：算法+数据结构

		库调用，系统调用：允许被调用的程序；

		malloc(), free()

		编程语言：
			汇编语言：微码编程
				系统中某些与硬件相关的特有代码、驱动程序开发；
			高级语言C, C++：
				系统级应用、驱动程序
			高级应用java, python, php：
				应用程序

			unix-like

	Linux的发行版：
		slackware:
			suse
				opensuse
		debian:
			ubuntu
				mint
		redhat：
			rhel: redhat enterprise linux
				每18个月发行一个新版本
			CentOS：兼容rhel的格式
			fedora：每6个月发行一个新版本

		ArchLinux
		Gentoo
		LFS: Linux From scratch

		Android: kernel+busybox+java虚拟机

	GNU: GPLv2, GPLv3, LGPL(lesser)
		Apache: apache
		BSD: bsd

		A: 1000, GPL,
		B: 20000, 20

	问题1：CentOS和Linux是什么关系？CentOS和RHEL是什么关系？
	问题2：各种开源协议的具体细节？
		GPL, LGPL, Apache, BSD

	程序包管理器：
		rpm:
			RHEL, Fedora, S.u.S.E, CentOS
		dpt:
			Debian, Ubuntu

	自由软件：
		自由使用；自由学习和修改；自由分发；自由创建衍生版；

	Linux的哲学思想：
		1、一切皆文件；
			把几乎所有资源，包括硬件设备都组织为文件格式；
		2、由众多单一目的小程序组成；一个程序只实现一个功能，而且要做好；
			组合小程序完成复杂任务；
		3、尽量避免跟用户交互；
			目标：实现脚本编程，以自动完成某些功能；
		4、使用纯文本文件保存配置信息；
			目标：一款使用的文本编程器即能完成系统配置工作；

	如何获取CentOS的发行版：
		http://mirrors.aliyun.com
		http://mirrors.sohu.com
		http://mirrors.163.com

	登录：
		root/mageedu

		# startx &

	终端：
		用户与主机交互，必然用到的设备；

		物理终端：直接接入本机的显示器和键盘设备；/dev/console

		虚拟终端：附加在物理终端之上的以软件方式虚拟实现的终端，CentOS 6默认启动6个虚拟终端
			Ctrl+Alt+F#: [1,6]
			图形终端：附加在物理终端之上的以软件方式虚拟实现的终端，但额外会提供桌面环境；

			设备文件路径：/dev/tty#
		模拟终端：
			图形界面下打开的命令行接口，基于ssh协议或telnet协议等远程打开的界面

			设备文件：/dev/pts/#    [0,oo)

		查看当前的终端设备：tty

	交互式接口：启动终端后，在终端设备附加一个交互式应用程序
		GUI：
			X protocol, window manager, desktop

			Desktop:
				GNOME (C, gtk)
				KDE   (C++, qt)
				XFCE  (轻量级桌面)
		CLI：
			shell程序：
				sh (bourn)
				csh 
				tcsh
				ksh (korn)
				bash (bourn again shell), GPL
				zsh 

			显示当前使用的shell：
				# echo ${SHELL}

			显示当前系统使用的所有shell：
				# cat /etc/shells

			命令提示符：prompt
				[root@localhost ~]#
					[root@localhost ~]：PS1
					prompt：
						管理员：#
						普通用户：$

	命令：
		输入命令，回车：
			提请shell程序找到键入命令所对应的可执行程序或代码，并由其分析后提交给内核分配资源将其运行起来；
				表现为一个或多个进程；

			在shell中可执行的命令有两类：
				内建命令：由shell自带的，而且通过某命令形式提供；
				外部命令：在当前系统的某文件系统路径下有对应的可执行程序文件；
					which，whereis

				区别内部或外部命令：
					# type COMMAND

		运行命令：
			命令格式：
				COMMAND [OPTIONS...] [ARGUMENTS...]
					选项：用于启用或关闭命令的某个或某些功能；
						短选项：-c, 例如：-l, -h
							多个短选项可命令使用，例如-l -h, 可写作-lh；
						长选项：--word，例如：--long, --human-readable
					参数：命令的作用对象
						向命令提供数据；

				注意：
					1、多选项，以及多参数和命令之间都应该使用空白字符分隔
					2、取消命令执行：Ctrl+c

		文件系统：
			C:\Program files\office11\word\word.exe

			/etc/sysconfig/network-scripts/ifcfg-eth0

			文件有两类数据：
				元数据：metadata
				数据：data

			1、文件名严格区分字符大小写；file1, File1, FILE1是不同的文件；
			2、文件名可使用除/以外的任意字符，不建议使用特殊字符；
				/: 根目录，路径分隔符；
			3、文件名长度最长不能超过255个字符；
			4、所以.开头的文件，均为隐藏文件；

			路径：
				绝对路径：从根目录起始的路径；
				相对路径：对当前位置起始的路径；
					当前位置的表示方式：
						(1) ./： ./sysconfig/network-scripts
						(2) 省略上述符号: sysconfig/network-scripts

						..：表示当前目录的上一级目录

				当前目录：current directory, 也称作working directory；
					pwd: printing working directory

			LSB: Linux Standard Base

	命令总结：ls, cd, type, tty, cat, which, whereis, pwd, echo

	命令：
		(1) 文件系统相关：
			目录管理
			文件管理
			文件查看
			文件编辑
		(2) 系统管理相关：

Linux命令帮助的获取

	外部命令：都有一个可执行程序，位于文件系统某目录下；
		which, whereis

		shell程序搜寻可执行程序文件的路径定义在PATH环境变量中；
			# echo $PATH

			注意：自左至右

	格式：COMMAND [OPTIONS...] [ARGUMENTS...]

	内部命令：
		# help COMMAND
	外部命令：
		(1) # COMMAND --help
			# COMMAND -h
		(2) 使用手册(manual)
			# man COMMAND
		(3) 信息页
			# info COMMAND
		(4) 程序自身的帮助文档
			README
			INSTALL
			ChangeLog
		(5) 程序官方文档
			官方站点：Documentation
		(6) 发行版的官方文档
		(7) Google

	内部命令帮助：
		# help COMMAND

			hash命令：
				shell搜寻到的外部命令的路径结果会缓存至kv(key-value)存储中；

			history命令：
				作用是什么？相关的环境变量是什么？有用的选项？

				管理命令历史；
					登录shell时，会读取命令历史文件中记录下的命令：~/.bash_history
					登录进shell后新执行的命令只会记录在缓存中；这些命令会用户退出时“追加”至命令历史文件中；

					history:
						-a: 追加本次会话新执行的命令历史列表至历史文件中；
						-d: 删除历史中指定的命令；
						-c: 清空命令历史；

				快捷操作：
					!#: 调用历史中第#条命令；
					!string：调用历史中最近一个以string开头的命令；
					!!: 上一条命令

	外部命令帮助获取：
		(2) man COMMAND
			手册页：/usr/share/man
				man1 ... man8
					man1: 用户命令
					man2: 系统调用
					man3: C库调用
					man4: 设备文件及特殊文件
					man5: 配置文件格式
					man6: 游戏
					man7: 杂项
					man8: 管理类的命令

			注意：有些关键在不止一个章节中存在帮助手册；
				要查看指定章节中的手册：man # COMMAND

			man命令的配置文件：/etc/man.config
				MANPATH /PATH/TO/SOMEWHERE: 指明新的手册文件搜索位置

				# man -M /PATH/TO/SOMEWHERE COMMAND: 到指定位置下搜索COMMAND命令的手册页并显示之；

			帮助手册中的段落说明：
				NAME
				SYNOPSIS
				DESCRIPTION
				OPTIONS
				EXAMPLES
				AUTHOR
				REPORTING BUGS
				SEE ALSO

			SYNOPSIS：
				[]：可选内容
				<>: 必选内容
				a|b：二选一
				...: 同一内容可出现多次

			man命令的操作方法：
				Space, ^V, ^f, ^F: 向文件尾翻屏；
				b, ^B: 向文件首部翻屏；
				d, ^D: 向文件尾部翻半屏；
				u, ^U: 向文件首部翻半屏；
				RETURN, ^N, e, ^E or j or ^J: 向文件尾部翻一行；
				y or ^Y or ^P or k or ^K：向文件首部翻一行；
				q: 退出；

				#：跳转至第#行；
				1G: 回到文件首部
				G：翻至文件尾部

			文本搜索：
				/KEYWORD: 
					以KEYWORD指定的字符串为关键字，从当前位置向文件尾部搜索；不区分字符大小写；
						n: 下一个
						N：上一个
				?KEYWORD:
					以KEYWORD指定的字符串为关键字，从当前位置向文件首部搜索；不区分字符大小写；
						n: 跟搜索命令同方向，下一个
						N：跟搜索命令反方向，上一个

			练习：date命令
				显示今天为周几？
				设定当前系统时间？
				显示今天的日期，形如：08/15/15？
				显示当前的时间，形如： 16:01:21？

		(3) info
			info COMMAND

		(4) 程序自身的帮助文档
			/usr/share/doc/COMMAND-VERSION

		(5) 程序官方文档

		(6) 发行版的官方文档
			http://www.redhat.com/docs

		(7) Google

		(8) slideshare
			http://www.slideshare.net/

基础命令：
	date：
		date [OPTION]... [+FORMAT]：显示
			FORMAT: 格式符号
				%D
				%F
				%T
       	date [MMDDhhmm[[CC]YY][.ss]]：设置
       		MM: 月分
       		DD：几号
       		hh: 小时
       		mm: 分钟
       		YY: 两位年份
       		CCYY: 四位年份
       		.ss: 秒钟

	    Linux的两种时钟：
	    	系统时钟：由Linux内核通过CPU的工作频率进行的计时；
	    	硬件时钟：

	    	hwclock: 显示硬件时钟
	    		-s, --hctosys
	    		-w, --systohc

	    	cal：日历

	目录相关的命令：
		当前目录或工作目录
		主目录，家目录：HOME
			root: /root
			普通用户：/home/USERNAME
				/home/tom

			~：用户的主目录

		cd: 
			cd 或 cd ~: 回当前用户的主目录
			cd ~USERNAME: 切换至指定用户的主目录
			cd -: 在上一个目录和当前目录之间来回切换

			.
			..

			相关的环境变量：
				PWD：保存了当前目录路径；
				OLDPWD：上一次所在目录路径；

		pwd: 显示当前目录

		ls: list
			显示指定路径下的文件列表；

			ls [OPTION]... [DIR]...
				-a, --all: 显示所有文件，包括隐藏文件；
				-l: 长格式
					-rw-r--r-- 1 root root 44800 Aug 14 14:32 install.log
						-rw-r--r--:
							最左侧的第一位：文件类型
								-, d, l, b, c, p, s
							后面的9位：访问权限，perm
						数字：文件被硬链接的次数；
						左root: 文件的owner
						右root: 文件的group
						44800：文件的size
						Aug 14 14:32 : 文件的最近一次被修改的时间
						install.log： 文件名
				-h, --human-readable：单位换算
				-d: 显示目录自身的相关属性；通常要与-l一起使用；
				-r, --reverse: 逆序显示
				-R, --recursive: 递归



		stat /PATH/TO/SOMEFILE：获取指定文件的元数据

		文件查看命令：cat, tac
			cat [OPTION]... [FILE]...
				-E: 显示行结束符$
				-n: 对显示出的每一行进行编号

		文件内容类型查看命令：file
			file /PATH/TO/SOMEWHERE

		回显命令：echo
			-n: 禁止自动添加换行符号；
			-e: 允许使用转义符；
				\n: 换行
				\t: 制表符

			echo "$VAR_NAME": 变量会替换，双引号表弱引用
			echo '$VAR_NAME': 变量不会替换，强引用

		which：显示命令对应的程序文件路径
			which [OPTION] COMMAND
				--skip-alias：禁止显示别名

		whatis：
			使用mkwhatis命令可将当前系统上所有的帮助手册及与之对应的关键字创建为一个数据库；

	系统管理类命令：
		关机：
			halt, poweroff, shutdown, init 0
		重启：
			reboot, shutdown, init 6
		跟用户登录相关：
			who, whoami, w


Linux的文件系统：
	根文件系统(rootfs)：
		root filesystem

	LSB, FHS: (FileSystem Heirache Standard)
		/etc, /usr, /var, /root, /home, /dev

		/boot：引导文件存放目录，内核文件(vmlinuz)、引导加载器(bootloader, grub)都存放于此目录；
		/bin：供所有用户使用的基本命令；不能关联至独立分区，OS启动即会用到的程序；
		/sbin：管理类的基本命令；不能关联至独立分区，OS启动即会用到的程序；
		/lib：基本共享库文件，以及内核模块文件(/lib/modules)；
		/lib64：专用于x86_64系统上的辅助共享库文件存放位置；
		/etc：配置文件目录(纯文本文件)；
		/home/USERNAME：普通用户家目录；
		/root：管理员的家目录；
		/media：便携式移动设备挂载点；
			cdrom
			usb
		/mnt：临时文件系统挂载点；
		/dev：设备文件及特殊文件存储位置；
			b: block device，随机访问
			c: character device，线性访问
		/opt：第三方应用程序的安装位置；
		/srv：系统上运行的服务用到的数据；
		/tmp：临时文件存储位置；
		/usr: universal shared, read-only data；
			bin: 保证系统拥有完整功能而提供的应用程序；
			sbin:
			lib：
			lib64：
			include: C程序的头文件(header files)；
			share：结构化独立的数据，例如doc, man等；
			local：第三方应用程序的安装位置；
				bin, sbin, lib, lib64, etc, share

		/var: variable data files
			cache: 应用程序缓存数据目录；
			lib: 应用程序状态信息数据；
			local：专用于为/usr/local下的应用程序存储可变数据；
			lock: 锁文件
			log: 日志目录及文件；
			opt: 专用于为/opt下的应用程序存储可变数据；
			run: 运行中的进程相关的数据；通常用于存储进程的pid文件；
			spool: 应用程序数据池；
			tmp: 保存系统两次重启之间产生的临时数据；

		/proc: 用于输出内核与进程信息相关的虚拟文件系统；
		/sys：用于输出当前系统上硬件设备相关信息的虚拟文件系统；
		/selinux: security enhanced Linux，selinux相关的安全策略等信息的存储位置；

	Linux上的应用程序的组成部分：
		二进制程序：/bin, /sbin, /usr/bin, /usr/sbin, /usr/local/bin, /usr/local/sbin
		库文件：/lib, /lib64, /usr/lib, /usr/lib64, /usr/local/lib, /usr/local/lib64
		配置文件：/etc, /etc/DIRECTORY, /usr/local/etc
		帮助文件：/usr/share/man, /usr/share/doc, /usr/local/share/man, /usr/local/share/doc

	Linux下的文件类型：
		- (f)：普通文件；
		d: 目录文件；
		b: 块设备；
		c: 字符设备；
		l: 符号链接文件；
		p: 管道文件；
		s: 套接字文件；socket; 


系统管理类命令：
		关机：
			halt, poweroff, shutdown, init 0
		重启：
			reboot, shutdown, init 6
		跟用户登录相关：
			who, whoami, w

	关机或重启：
		halt, poweroff
		reboot

			-f: 强制，不调用shutdown
			-p: 切断电源

	shutdown：
		shutdown [OPTION]...  TIME [MESSAGE]

			-r: reboot
			-h: halt

			-c：cancel

			TIME: 
				now: 立刻
				+m: 相对时间表示法，从命令提交开始多久之后；例如 +3；
				hh:mm: 绝对时间表示，指明具体时间；

	用户登录信息查看命令：
		whoami: 显示当前登录有效用户；
		who: 系统当前所有的登录会话；
		w: 系统当前所有的登录会话及所做的操作；

bash的基础特性(1)：
	
	(1) 命令历史
		history
			环境变量：
				HISTSIZE：命令历史记录的条数；
				HISTFILE：~/.bash_history；
				HISTFILESIZE：命令历史文件记录历史的条数；

			history -d OFFSET
			-c
			history #: 显示历史中最近的#条命令；
			-a: 手动追加当前会话缓冲区的命令历史至历史文件中；

		调用历史中的命令：
			!#: 重复执行第#条指令；
			!!: 
			!string

		调用上一条命令的最后一个参数：
			!$: 
			ESC, .
			Alt+.

		控制命令历史的记录方式：
			环境变量：HISTCONTROL
				ignoredups：忽略重复的命令；连续且相同方为“重复”；
				ignorespace：忽略所有以空白开头的命令；
				ignoreboth：ignoredups, ignorespace；

			修改环境变量值的方式：export 变量名="值"
				变量赋值：把赋值符号后面的数据存储于变量名指向内存空间；

	(2) 命令补全
		bash执行命令：
			内部命令：
			外部命令：bash根据PATH环境变量定义的路径，自左而右在每个路径搜寻以给定命令名命名的文件，第一次找到即为要执行的命令；

				直接补全：Tab，用户给定的字符串只有一条惟一对应的命令；
				以用户给定的字符串为开头对应的命令不惟一，则再次Tab会给出列表；

	(3) 路径补全
		把用户给出的字符串当做路径开头，并在其指定上级目录下搜索以指定的字符串开头的文件名；
			如果惟一：则直接补全；
			否则：再次Tab，给出列表；

	(4) 命令行展开
		~: 展开为用户的主目录
		~USERNAME：展开为指定用户的主目录
		{}：可承载一个以逗号分隔的列表，并将其展开为多个路径
			/tmp/{a,b} = /tmp/a, /tmp/b
			/tmp/{tom,jerry}/hi = /tmp/tom/hi, /tmp/jerry/hi

	(5) 命令的执行结果状态
		成功
		失败

		bash使用特殊变量$?保存最近一条命令的执行状态结果：
			0：成功
			1-255：失败

		程序执行有两类结果：
			程序的返回值；
			程序的执行状态结果；


目录管理类命令：
	cd, pwd, ls

	mkdir, rmdir, tree

	mkdir [options] /path/to/somewhere
		-p: 存在于不报错，且可自动创建所需的各目录；
		-v: 显示详细信息
		-m MODE: 创建目录时直接指定权限；

	tree: 
		-d: 只显示目录
		-L level：指定显示的层级数目
		-P pattern: 只显示由指定pattern匹配到的路径；

	rmdir：删除空目录
		rmdir [OPTION]... DIRECTORY...
			-v: 显示过程；

	练习：
		(1) 如何创建/tmp/x/y1, /tmp/x/y2, /tmp/x/y1/a, /tmp/x/y1/b, /tmp/x/y2/a, /tmp/x/y2/b
			mkdir /tmp/x/{y1,y2}/{a,b}		

		(2) 如何创建x_m, y_m, x_n, y_n
			mkdir {x,y}_{m,n}

		(3) 如何创建/tmp/bin, /tmp/sbin, /tmp/usr, /tmp/usr/bin, /tmp/usr/sbin
			mkdir -p /tmp/{bin,sbin,usr/{bin,sbin}}

文本文件查看类命令：
	cat, tac
	more, less, tail, head

	more
		more [OPTIONS...] FILE...
			-d: 显示翻页及退出提示

	less
		less [OPTIONS...] FILE...

	head
		head [OPTION]... [FILE]...
			-c #: 指定获取前#字节
			-n #: 指定获取前#行
				-#：

	tail 
		tail [OPTION]... [FILE]...
			-c #: 指定获取后#字节
			-n #: 指定获取后#行
				-#：
			-f: 跟踪显示文件新追加的内容；

文件的时间戳管理工具：
	touch

	文件：metadata, data
		查看文件状态：stat

		三个时间戳：
			access time：访问时间，简写为atime，读取文件内容
			modify time: 修改时间, mtime，改变文件内容（数据）
			change time: 改变时间, ctime，元数据发生改变

	touch命令：
		touch [OPTION]... FILE...
			-a: only atime
			-m: only mtime
			-t STAMP:
				 [[CC]YY]MMDDhhmm[.ss]
			-c: 如果文件不存，则不予创建

回顾：
	文件系统、bash的基础特性、目录管理及文件查看

		文件系统：
			boot, bin, sbin, lib, lib64, dev, home, root, mnt, media, etc, proc, sys
			usr
			var

		bash的基础特性：
			命令补全
			路径补全
			命令历史
			命令行展开：
				~, {}
			命令的状态结果
				成功：0
				失败：1-255

		目录管理：mkdir, rmdir

		文件查看：more, less, tail, head

		时间戳管理：touch
			-a, -m, -t
			-c

文件管理：
	cp, mv, rm

	复制命令：cp
		cp [OPTION]... [-T] SOURCE DEST
        cp [OPTION]... SOURCE... DIRECTORY
        cp [OPTION]... -t DIRECTORY SOURCE...

        cp SRC DEST
        	SRC是文件：
        		如果目标不存在：新建DEST，并将SRC中内容填充至DEST中；
        		如果目录存在：
        			如果DEST是文件：将SRC中的内容覆盖至DEST中；
        				此时建议为cp命令使用-i选项；
        			如果DEST是目录：在DEST下新建与原文件同名的文件，并将SRC中内容填充至新文件中；

        cp SRC... DEST
        	SRC...：多个文件
        		DEST必须存在，且为目录，其它情形均会出错；

        cp SRC DEST
        	SRC是目录：
        		此时使用选项：-r

        		如果DEST不存在：则创建指定目录，复制SRC目录中所有文件至DEST中；
        		如果DEST存在：
        			如果DEST是文件：报错
        			如果DEST是目录：

        常用选项：
        	-i：交互式
        	-r, -R: 递归复制目录及内部的所有内容；
        	-a: 归档，相当于-dR --preserv=all
        		-d：--no-dereference --preserv=links
        		--preserv[=ATTR_LIST]
        			mode: 权限
        			ownership: 属主属组
        			timestamp: 
        			links
        			xattr
        			context
        			all
        	-p: --preserv=mode,ownership,timestamp
        	-v: --verbose
        	-f: --force

    mv: move，移动文件
       mv [OPTION]... [-T] SOURCE DEST
       mv [OPTION]... SOURCE... DIRECTORY
       mv [OPTION]... -t DIRECTORY SOURCE...
       
       常用选项：
       		-i: 交互式
       		-f: 强制

    rm: remove，删除
    	rm [OPTION]... FILE...

    	常用选项：
    		-i: 交互式
    		-f: 强制删除
    		-r: 递归

    		rm -rf 

    文本编辑器：nano
    	全屏编辑器

bash的基础特性(2):
	
	1、命令别名(alias)
		通过alias命令实现：
			(1) alias
				显示当前shell进程所有可用的命令别名；
			(2) alias NAME='VALUE'
				定义别名NAME，其相当于执行命令VALUE；

			注意：在命令行中定义的别名，仅对当前shell进程有效；如果想永久有效，要定义在配置文件中；
				仅对当前用户：~/.bashrc
				对所有用户有效：/etc/bashrc

				Note: 编辑配置给出的新配置不会立即生效；

				bash进程重新读取配置文件：
					source /path/to/config_file
					.  /path/to/config_file

		撤消别名：unalias
			unalias [-a] name [name ...]

		Note: 如果别名同原命令的名称，则如果要执行原命令，可使用"\COMMAND"；

	2、glob (globbing)
		bash中用于实现文件名“通配”

			通配符：*, ?, []

			(1) *
				任意长度的任意字符
					a*b: 
						aab, ab, a123b, 
						abc
			(2) ?
				任意单个字符
					a?b:
						aab
						ab, a12b, abc

			(3) []
				匹配指定范围内的任意单个字符
					[0-9]
					[a-z]：不区分字符大小写

			(4) [^]
				匹配指定范围外的任意单个字符
					[^0-9]

			专用字符集合：
				[:digit:]：任意数字，相当于0-9
				[:lower:]：任意小写字母
				[:upper:]: 任意大写字母
				[:alpha:]: 任意大小写字母
				[:alnum:]：任意数字或字母
				[:space:]：
				[:punct:]：标点符号

		练习：
			1、显示/var目录下所有以l开头，以一个小写字母结尾，且中间出现至少一位数字的文件或目录；
				# ls -d /var/l*[0-9]*[[:lower:]]

			2、显示/etc目录下，以任意一位数字开头，且以非数字结尾的文件或目录；
				# ls -d /etc/[0-9]*[^0-9]

			3、显示/etc/目录下，以非字母开头，后面跟了一个字母及其它任意长度任意字符的文件或目录；
				# ls /etc/[^[:alpha:]][[:alpha:]]*

			4、复制/etc目录下，所有以m开头，以非数字结尾的文件或目录至/tmp/mageedu目录中；
				# cp -a /etc/m*[^0-9] /tmp/mageedu

			5、复制/etc目录下，所有以.d结尾的文件或目录至/tmp/magedu.com目录中；
				# cp -a /etc/*.d /tmp/magedu.com

			6、复制/etc目录下，所以有.conf结尾，且以m,n,r,p开头的文件或目录至/tmp/mageedu.com目录中；
				# cp -a /etc/[mnrp]*.conf  /tmp/mageedu.com

	3、bash的快捷键

		Ctrl+l: 清屏，相当于clear命令；

		Ctrl+a：跳转至命令开始处；
		Ctrl+e：跳转至命令结尾处；

		Ctrl+c：取消命令的执行；

		Ctrl+u: 删除命令行首至光村所在处的所有内容；
		Ctrl+k: 删除光标所在处至命令行尾部的所有内容；

	4、bash的的I/O重定向及管道

		程序：指令+数据
			读入数据：Input
			输出数据：Output

		打开的文件都有一个fd: file descriptor (文件描述符)

		标准输入：keyborad, 0
		标准输出：monitor, 1
		标准错误输出：monitor, 2

		I/O重定向：改变标准位置

		输出重定向：COMMAND > NEW_POS, COMMAND >> NEW_POS
			>：覆盖重定向，目标文件中的原有内容会被清除；
			>>: 追加重定向，新内容会追加至目标文件尾部；

			# set -C: 禁止将内容覆盖输出至已有文件中；
				强制覆盖：>|
			# set +C: 

			2>: 覆盖重定向错误输出数据流；
			2>>: 追加重定向错误输出数据流；

			标准输出和错误输出各自定向至不同位置：
				COMMAND > /path/to/file.out 2> /path/to/error.out

			合并标准输出和错误输出为同一个数据流进行重定向：
				&>：覆盖重定向
				&>>：追加重定向

				COMMAND > /path/to/file.out 2> &1
				COMMAND >> /path/to/file.out 2>> &1

		输入重定向：<

			tr命令：转换或删除字符
				tr [OPTION]... SET1 [SET2]

		HERE Documentation：<<
			# cat << EOF
			# cat > /path/to/somefile << EOF

	管道：
		COMMAND1 | COMMAND2 | COMMAND3 |...

		Note：最后一个命令会在当前shell进程的子shell进程中执行；

		tee命令：
			tee [OPTION]... [FILE]...

	练习：
		1、将/etc/passwd文件中的前5行内容转换为大写后保存至/tmp/passwd.out文件中；

		2、将登录至将前系统上用户信息中的后3行的信息转换为大写后保存至/tmp/who.out文件中；
			# who | tail -n 3 | tr 'a-z' 'A-Z' > /tmp/who.out

文本处理工具：wc, cut, sort, uniq

	wc命令：
		wc [OPTION]... [FILE]...
			-l: lines
			-w: words
			-c: characters

	cut命令：
		cut [OPTION]... [FILE]...
			-d DELIMITER: 指明分隔符
			-f FILEDS:
				#: 第#个字段
				#,#[,#]：离散的多个字段，例如1,3,6
				#-#：连续的多个字段, 例如1-6

				混合使用：1-3,7
			--output-delimiter=STRING

	sort命令：
		sort [OPTION]... [FILE]...
			-f: 忽略字符大小写
			-r: 逆序
			-t DELIMITER: 字段分隔符
			-k #：以指定字段为标准排序
			-n: 以数值大小进行排序
			-u: uniq，排序后去重

	uniq命令：
		uniq [OPTION]... [FILE]...
			-c: 显示每行重复出现的次数；
			-d: 仅显示重复过的行；
			-u: 仅显示不曾重复的行；

		Note: 连续且完全相同方为重复

	练习：以冒号分隔，取出/etc/passwd文件的第6至第10行，并将这些信息按第3个字段的数值大小进行排序；最后仅显示的各自的第1个字段；

回顾：
	文件管理命令、bash的基础特性、文件本管理命令

	文件管理命令：cp, mv, rm

	bash基础特性：alias/unalias, glob (*, ?, [], [^]), 快捷键

	IO重定向、管道：
		标准输出重定向：>, >>
		标准错误重定向：2>, 2>>

			&>, &>>
			COMMAND > /path/to/somefile.out 2> &1

		输入重定向：<
			显式使用：tr
			隐式使用：...

		管道：COMMAND1 | COMMAND2 | ...

	文本处理命令：wc, cut, sort, uniq


用户和组管理
	资源分派：
		Authentication：认证
		Authorization：授权
		Accouting：审计
			Audition

		token, identity(username/password)	

	Linux用户：Username/UID
		管理员：root, 0
		普通用户：1-65535
			系统用户：1-499, 1-999
				对守护进程获取资源进行权限分配；
			登录用户:500+, 1000+
				交互式登录；

	Linux组：Groupname/GID
		管理员组：root, 0
		普通组：
			系统组：1-499, 1-999
			普通组：500+, 1000+

	Linux安全上下文：
		运行中的程序：进程 (process)

			以进程发起者的身份运行：
				root: cat
				tom: cat

			进程所能够访问的所有资源的权限取决于进程的发起者的身份；

	Linux组的类别：
		用户的基本组(主组)：
			组名同用户名，且仅包含一个用户：私有组
		用户的附加组(额外组)：

	Linux用户和组相关的配置文件：
		/etc/passwd：用户及其属性信息(名称、UID、基本组ID等等)；
		/etc/group：组及其属性信息；
		/etc/shadow：用户密码及其相关属性；
		/etc/gshadow：组密码及其相关属性；

	/etc/passwd：
		name:password:UID:GID:GECOS:directory:shell

		用户名:密码:UID:GID:GECOS:主目录:默认shell

	/etc/group：
		group_name:password:GID:user_list

		组名:组密码:GID:以当前组为附加组的用户列表(分隔符为逗号)

	/etc/shadow
		用户名:加密了的密码:最近一次更改密码的日期:密码的最小使用期限:最大密码使用期限:密码警告时间段:密码禁用期:账户过期日期:保留字段

			加密机制：
				加密：明文--> 密文
				解密：密文--> 明文
				
				单向加密：提取数据指纹
					md5: message digest, 128bits
					sha1: secure hash algorithm, 160bits
					sha224: 224bits
					sha256: 256bits
					sha384: 384bits
					sha512: 512bits

					雪崩效应：初始的条件的微小改变，将会引起结果的巨大改变；
					定长输出：

			密码的复杂性策略：
				1、使用数字、大写字母、小写字母及特殊字符中至少3种；
				2、足够长；
				3、使用随机密码；
				4、定期更换；不要使用最近曾经使用过的密码；

	用户和组相关的管理命令：
		用户创建：useradd
			useradd [options] LOGIN
				-u UID: [UID_MIN, UID_MAX], 定义在/etc/login.defs
				-g GID：指明用户所属基本组，可为组名，也可以GID；
				-c "COMMENT"：用户的注释信息；
				-d /PATH/TO/HOME_DIR: 以指定的路径为家目录；
				-s SHELL: 指明用户的默认shell程序，可用列表在/etc/shells文件中；
				-G GROUP1[,GROUP2,...[,GROUPN]]]：为用户指明附加组；组必须事先存在；
				-r: 创建系统用户
					CentOS 6: ID<500
					CentOS 7: ID<1000

			默认值设定：/etc/default/useradd文件中
				useradd -D 
					-s SHELL

		练习：创建用户gentoo，附加组为distro和linux，默认shell为/bin/csh，注释信息为"Gentoo Distribution"; 

	组创建：groupadd
		groupadd [OPTION]... group_name

			-g GID: 指明GID号；[GID_MIN, GID_MAX]
			-r: 创建系统组；
				CentOS 6: ID<500
				CentOS 7: ID<1000

	查看用户相关的ID信息：id
		id [OPTION]... [USER]
			-u: UID
			-g: GID
			-G: Groups
			-n: Name

	切换用户或以其他用户身份执行命令：su
		su [options...] [-] [user [args...]]

			切换用户的方式：
				su UserName：非登录式切换，即不会读取目标用户的配置文件；
				su - UserName：登录式切换，会读取目标用户的配置文件；完全切换；

				Note：root su至其他用户无须密码；非root用户切换时需要密码；

			换个身份执行命令：
				su [-] UserName -c 'COMMAND'

			选项：
				-l：“su -l UserName”相当于“su - UserName”

	用户属性修改：usermod
		usermod [OPTION] login

			-u UID: 新UID
			-g GID: 新基本组
			-G GROUP1[,GROUP2,...[,GROUPN]]]：新附加组，原来的附加组将会被覆盖；若保留原有，则要同时使用-a选项，表示append；
			-s SHELL：新的默认SHELL；
			-c 'COMMENT'：新的注释信息；
			-d HOME: 新的家目录；原有家目录中的文件不会同时移动至新的家目录；若要移动，则同时使用-m选项；
			-l login_name: 新的名字；

			-L: lock指定用户
			-U: unlock指定用户

			-e YYYY-MM-DD: 指明用户账号过期日期；
			-f INACTIVE: 设定非活动期限；

	给用户添加密码：passwd
		passwd [OPTIONS] UserName: 修改指定用户的密码，仅root用户权限
		passwd: 修改自己的密码；

			常用选项：
				-l: 锁定指定用户
				-u: 解锁指定用户

				-n mindays: 指定最短使用期限
				-x maxdays：最大使用期限
				-w warndays：提前多少天开始警告
				-i inactivedays：非活动期限；

				--stdin：从标准输入接收用户密码；
					echo "PASSWORD" | passwd --stdin USERNAME

				Note: /dev/null, bit buckets
				      /dev/zero, 

	删除用户：userdel
		userdel [OPTION]... login
			-r: 删除用户家目录；

	组属性修改：groupmod
		groupmod [OPTION]... group
			-n group_name: 新名字
			-g GID: 新的GID；

	组删除：groupdel
		groupdel GROUP

	组密码：gpasswd
		gpasswd [OPTION] GROUP

			-a user: 将user添加至指定组中；
			-d user: 删除用户user的以当前组为组名的附加组

			-A user1,user2,...: 设置有管理权限的用户列表

		newgrp命令：临时切换基本组；
			如果用户本不属于此组，则需要组密码；

	修改用户属性：chage
		chage [OPTION]... LOGIN
			-d LAST_DAY
			-E, --expiredate EXPIRE_DATE
			-I, --inactive INACTIVE
			-m, --mindays MIN_DAYS
			-M, --maxdays MAX_DAYS
			-W, --warndays WARN_DAYS

	其它命令：chfn, chsh, finger

	命令总结：useradd, groupadd, su, id, usermod, userdel, groupmod, groupdel, passwd, newgrp, pwck, gpasswd, chage, chsh, chfn, finger

权限管理：
	
	文件的权限主要针对三类对象进行定义：
		owner: 属主, u
		group: 属组, g
		other: 其他, o

	每个文件针对每类访问者都定义了三种权限：
		r: Readable
		w: Writable
		x: eXcutable

		文件：
			r: 可使用文件查看类工具获取其内容；
			w: 可修改其内容；
			x: 可以把此文件提请内核启动为一个进程；

		目录：
			r: 可以使用ls查看此目录中文件列表；
			w: 可在此目录中创建文件，也可删除此目录中的文件；
			x: 可以使用ls -l查看此目录中文件列表，可以cd进入此目录；

		--- 000 0
		--x 001 1
		-w- 010 2
		-wx 011 3
		r-- 100 4
		r-x 101 5
		rw- 110 6
		rwx 111 7

		例如：
			640: rw-r-----
			rwxr-xr-x: 755

	修改文件权限：chmod
		chmod [OPTION]... OCTAL-MODE FILE...

			-R: 递归修改权限

		chmod [OPTION]... MODE[,MODE]... FILE...
			MODE:
				修改一类用户的所有权限：
					u=
					g=
					o=
					ug=
					a=
					u=,g=

				修改一类用户某位或某些位权限
					u+
					u-

		chmod [OPTION]... --reference=RFILE FILE...
			参考RFILE文件的权限，将FILE的修改为同RFILE；

	修改文件的属主和属组：
		仅root可用；

		修改文件的属主：chown
			chown [OPTION]... [OWNER][:[GROUP]] FILE...

				用法：
					OWNER
					OWNER:GROUP
					:GROUP

					Note: 命令中的冒号可用.替换；

				-R: 递归

			chown [OPTION]... --reference=RFILE FILE...

		修改文件的属组：chgrp
 			chgrp [OPTION]... GROUP FILE...
       		chgrp [OPTION]... --reference=RFILE FILE...

       		-R

    文件或目录创建时的遮罩码：umask
    	FILE: 666-umask
    		Note: 如果某类的用户的权限减得的结果中存在x权限，则将其权限+1
    	DIR: 777-umask	

    	umask: 查看
    	umask #: 设定

    命令总结：chmod, chown, chgrp, umask

回顾：
	文件管理、用户管理、权限管理、bash的基础特性
		文件管理：cp, mv, rm
		用户管理：
			useradd, usermod, chsh, chfn, chage, userdel
			groupad, groupmod, groupdel
			passwd, gpasswd, pwck
			id, finger, who, whoami, w
			su
			配置文件：/etc/passwd, /etc/shadow, /etc/group, /etc/gshadow 
		权限管理：
			perm: mode(rwxrwxrwx), ownership 
				chmod
				chown
				chgrp
					-R, --reference=RFILE

				FILE: 666-umask
				DIR: 777-umask

				umask [UMASK]

	bash基础特性：
		命令行展开：~, {}
		命令别名：alias/unalias
		命令历史：history
		命令和路径补全：$PATH
		glob: *, ?, [], [^]
		快捷键：Ctrl+{a,e,l,c,u,k}
		命令hash：hash

bash的基础特性(3)
	1、提供了编程环境

		程序：指令+数据

		程序编程风格：
			过程式：以指令为中心，数据服务于指令
			对象式：以数据为中心，指令服务于数据

		shell程序：提供了编程能力，解释执行

		程序的执行方式：
			计算机：运行二进制指令；
			编程语言：
				低级：汇编
				高级：
					编译：高级语言-->编译器-->目标代码
						C、C++, java
					解释：高级语言-->解释器-->机器代码
						shell, perl, python

		过程式编程：
			顺序执行
			循环执行
			选择执行

			shell编程：过程式、解释执行
				编程语言的基本结构：
					数据存储：变量、数组
					表达式
					语句

	shell脚本：文本文件
		shebang：
			#!/bin/bash
			#!/usr/bin/python
			#!/usr/bin/perl		

		magic number: 魔数

		运行脚本：
			1、给予执行权限，通过具体的文件路径指定文件执行；
			2、直接运行解释器，将脚本作为解释器程序的参数运行；

	变量：
		命名的内存空间；

		数据存储方式：ASCII
			字符：110：24
			数值：110 --> 8
				整型
				浮点型

		变量：变量类型
			作用：
				1、数据存储格式；
				2、参与的运算；
				3、表示的数据范围；

			类型：
				字符
				数值：
					整型
					浮点型

			编程程序语言：
				强类型：
				弱类型：bash
					把所有要存储的数据统统当作字符进行
					不支持浮点数；

			逻辑运算：
				true, false
				1, 0

				与：
					1 && 1 = 1
					1 && 0 = 0
					0 && 1 = 0
					0 && 0 = 0
				或:
					1 || 1 = 1
					1 || 0 = 1
					0 || 1 = 1
					0 || 0 = 0
				非：
					! 1 = 0
					! 0 = 1
				
			短路运算：
				与：
					第一个为0，结果必定为0；
					第一个为1，第二个必须要参与运算；
				或：
					第一个为1，结果必定为1；
					第一个为0，第二个必须要参与运算；

grep：
	Linux上文本处理三剑客
		grep：文本过滤(模式：pattern)工具; 
			grep, egrep, fgrep
		sed：stream editor，文本编辑工具；
		awk：Linux上的实现gawk，文本报告生成器；

	grep: Global search REgular expression and Print out the line.
		作用：文本搜索工具，根据用户指定的“模式”对目标文本逐行进行匹配检查；打印匹配到的行；
			模式：由正则表达式字符及文本字符所编写的过滤条件；
			REGEXP：由一类特殊字符及文本字符所编写的模式，其中有些字符不表示字符字面意义，而表示控制或通配的功能；
				分两类：
					基本正则表达式：BRE
					扩展正则表达式：ERE
						grep -E, egrep

				正则表达式引擎

				grep [OPTIONS] PATTERN [FILE...]

						选项：
							--color=auto: 对匹配到的文本着色显示；
							-v: 显示不能够被pattern匹配到的行；
							-i: 忽略字符大小写；
							-o: 仅显示匹配到的字符串；
							-q: 静默模式，不输出任何信息；
							-A #：after, 后#行
							-B #: before, 前#行
							-C #：context, 前后各#行

							-E：使用ERE；

		基本正则表达式元字符：
			字符匹配:
				.：  匹配任意单个字符；
				[]： 匹配指定范围内的任意单个字符
				[^]：匹配指定范围外的任意单个字符
					[:digit:]、[:lower:]、[:upper:]、[:alpha:]、[:alnum:]、[:punct:]、[:space:]
			
			匹配次数：用在要指定次数的字符后面，用于指定前面的字符要出现的次数；
				*：匹配前面的字符任意次；
					例如: grep "x*y" 
						abxy
						xay
						xxxxxxy

					贪婪模式
				.*：任意长度的任意字符；
				\?：匹配其前面的字符0或1次；即前面的可有可无；
				\+：匹配其前面的字符至少1次；
				\{m\}：匹配前面的字符m次；
				\{m,n\}：匹配前面的字符至少m次，至多n次；
					\{0,n\}：匹配前面的字符至多n次；
					\{m,\}：匹配前面的字符至少m次；

			位置锚定：
				^：行首锚定；用于模式的最左侧；
				$：行尾锚定；用于模式的最右侧；
				^PATTERN$: 用于模式匹配整行；
					^$: 空行；
					^[[:space:]]*$

				\< 或 \b：词首锚定；用于单词模式的左侧；
				\> 或 \b：词尾锚定；用于单词模式的右侧；
				\<PATTERN\>：匹配整个单词；

			分组：
				\(\)：将一个或多个字符捆绑在一起，当作一个整体进行处理；
					\(xy\)*ab

				Note: 分组括号中的模式匹配到的内容会被正则表达式引擎记录于内部的变量中，这些变量的命名方式为: \1, \2, \3, ...
					\1: 从左侧起，第一个左括号以及与之匹配右括号之间的模式所匹配到的字符；
						\(ab\+\(xy\)*\)：
							\1: ab\+\(xy\)*
							\2: xy

				后向引用：引用前面的分组括号中的模式所匹配字符，(而非模式本身)

	练习：
		1、显示/proc/meminfo文件中以大小s开头的行；(要求：使用两种方式)


		2、显示/etc/passwd文件中不以/bin/bash结尾的行；


		3、显示/etc/passwd文件中ID号最大的用户的用户名；


		4、如果用户root存在，显示其默认的shell程序；
		# id root &> /dev/null && grep "^root\>" /etc/passwd | cut -d: -f7

		5、找出/etc/passwd中的两位或三位数；
		# grep "\<[0-9]\{2,3\}\>" /etc/passwd

		6、显示/etc/rc.d/rc.sysinit文件中，至少以一个空白字符开头的且后面存非空白字符的行；
		# grep "^[[:space:]]\+[^[:space:]]" /etc/grub2.cfg

		7、找出"netstat -tan"命令的结果中以'LISTEN'后跟0、1或多个空白字符结尾的行；
		# netstat -tan | grep "LISTEN[[:space:]]*$"

		8、添加用户bash、testbash、basher以及nologin(其shell为/sbin/nologin)；而后找出/etc/passwd文件中用户名同shell名的行；
		# grep "^\([[:alnum:]]\+\>\).*\1$" /etc/passwd


	练习：
		1、写一个脚本，实现如下功能
			如果user1用户存在，就显示其存在，否则添加之；
			显示添加的用户的id号等信息；
				#!/bin/bash
				id user1 &> /dev/null && echo "user1 exists." || useradd user1
				id user1

		2、写一个脚本，完成如下功能
			如果root用户登录了当前系统，就显示root用户在线；否则说明其未登录；

回顾：shell的基础特性、grep

	shell:
		#!/bin/bash

		过程式：以指令为中心
		对象式：以数据为中心

	grep：文本过滤器
		PATTERN

		REGEXP：
			BRE、ERE

		BRE：
			字符匹配：., [], [^]
			次数匹配：*, \?, \+, \{m\}, \{m,n\}
			位置锚定：^, $, \<, \b, \>, \b
			分组：\(\)
				后向引用：\1, \2, ...

		vim, sed, awk, nginx, 

egrep及扩展的正则表达式

	egrep = grep -E

	egrep [OPTIONS] PATTERN [FILE...]

	扩展正则表达式的元字符：
		字符匹配：
			.
			[]
			[^]
		次数匹配：
			*
			?: 0或1次；
			+：1次或多次；
			{m}：匹配m次；
			{m,n}：至少m，至多n次；
		锚定：
			^
			$
			\<, \b
			\>, \b
		分组：
			()

			后向引用：\1, \2, ...
		或者：
			a|b
				C|cat: C或cat

	练习：
		1、显示当前系统root、centos或user1用户的默认shell和UID；
		# grep -E '^(root|centos|user1)\>' /etc/passwd | cut -d: -f1,3,7

		2、找出/etc/rc.d/init.d/functions文件(centos6)中某单词后面跟一个小括号的行；
		# grep -E -o "^[_[:alpha:]]+\(\)" /etc/rc.d/init.d/functions

		3、使用echo输出一绝对路径，使用egrep取出其基名；
		# echo "/mnt/sdc" | grep -E -o "[^/]+/?$" | cut -d"/" -f1

		进一步地：使用egrep取出路径的目录名，类似于dirname命令的结果；

		4、找出ifconfig命令结果中1-255之间的数值；

		5、找出ifconfig命令结果中的IP地址；

	fgrep：不支持正则表达式搜索；



bash的基础特性(4)

	变量类型：
		数据存储格式、存储空间大小、参与运算种类；

		字符型
		数值型：
			整型
			浮点型

		强类型：定义变量时必须指定类型、参与运算必须符合类型要求；调用未声明变量会产生错误；
		弱类型：无须指定类型，默认均为字符型；参与运算会自动进行隐式类型转换；变量无须事先定义可直接调用；
			bash

		bash中的变量的种类：
			根据变量的生效范围等标准：
				本地变量：生效范围为当前shell进程；对当前shell之外的其它shell进程，包括当前shell的子shell进程均无效；
				环境变量：生效范围为当前shell进程及其子进程；
				局部变量：生效范围为当前shell进程中某代码片断(通常指函数)；
				位置变量：$1, $2, ...来表示，用于让脚本在脚本代码中调用通过命令行传递给它的参数；
				特殊变量：$?, $0, $*, $@, $#

			本地变量：
				变量赋值：name='value'
					可以使用引用：
						value:
							(1) 可以是直接字串; name="username"
							(2) 变量引用：name="$username"
							(3) 命令引用：name=`COMMAND`, name=$(COMMAND)
				变量引用：${name}, $name
					""：弱引用，其中的变量引用会被替换为变量值；
					''：强引用，其中的变量引用不会被替换为变量值，而保持原字符串；
				显示已定义的所有变量
					set
				销毁变量：
					unset name

			环境变量：
				变量声明、赋值：
					export name=VALUE
					declare -x name=VALUE
				变量引用：$name, ${name}
				显示所有环境变量：
					export
					env
					printenv
				销毁：
					unset name

				bash有许多内建的环境变量：PATH, SHELL, UID, HISTSIZE, HOME, PWD, OLD, HISTFILE, PS1

			变量命名法则：
				1、不能使程序中的保留字：例如if, for; 
				2、只能使用数字、字母及下划线，且不能以数字开头；
				3、见名知义，

			只读变量：
				readonly name
				declare -r name

			位置变量：
				在脚本代码中调用通过命令行传递给脚本的参数；
					$1, $2, ...：对应调用第1、第2等参数；

						shift [n]

					$0: 命令本身；

					$*: 传递给脚本的所有参数；
					$@: 传递给脚本的所有参数；
					$#: 传递给脚本的参数的个数；

				示例：判断给出的文件的行数
					#!/bin/bash
					linecount="$(wc -l $1| cut -d' ' -f1)"
					echo "$1 has $linecount lines."			

	bash的配置文件：

		按生效范围划分，存在两类：
			全局配置：
				/etc/profile
					/etc/profile.d/*.sh
				/etc/bashrc
			个人配置：
				~/.bash_profile
				~/.bashrc

		按功能划分，存在两类：
			profile类：为交互式登录的shell提供配置
				全局：/etc/profile, /etc/profile.d/*.sh
				个人：~/.bash_profile
				功用：
					(1) 用于定义环境变量；
					(2) 运行命令或脚本；

			bashrc类：为非交互式登录的shell提供配置
				全局：/etc/bashrc
				个人：~/.bashrc

				功用：
					(1) 定义命令别名；
					(2) 定义本地变量；

		shell登录：
			交互式登录：
				直接通过终端输入账号密码登录；
				使用“su - UserName”或“su -l UserName”切换的用户

				/etc/profile --> /etc/profile.d/*.sh --> ~/.bash_profile --> ~/.bashrc --> /etc/bashrc

			非交互式登录：
				su UserName
				图形界面下打开的终端
				执行脚本

				~/.bashrc --> /etc/bashrc --> /etc/profile.d/*.sh

		编辑配置文件定义的新配置的生效方式：
			(1) 重新启动shell进程；
			(2) 使用source或.命令进程；

		问题：
			1、定义对所有用户都生效的别名？
			2、让用户的PATH环境变量的值多出一个路径，例如多如/usr/local/apache2/bin
				管理员？
				所有？

	bash中的算术运算
		+, -, *, /, %, **

		实现算术运算：
			(1) let var=算术表达式
			(2) var=$[算术表达式]
			(3) var=$((算术表达式))
			(4) var=$(expr arg1 arg2 arg3 ...)

			乘法符号有些场景中需要转义；

			bash有内建的随机数生成器：$RANDOM

		增强型赋值：
			+=, -=, *=, /=, %=

			let varOPERvalue
				例如:let count+=1

		自增，自减：
			let var+=1
				let var++

			let var-=1
				let var--

		练习1：写一个脚本
			计算/etc/passwd文件中的第10个用户和第20用户的ID之和；

				#!/bin/bash
				userid1=$(head -n 10 /etc/passwd | tail -n 1 | cut -d: -f3)
				userid2=$(head -n 20 /etc/passwd | tail -n 1 | cut -d: -f3)

				useridsum=$[$userid1+$userid2]

				echo "uid sum: $useridsum"

		练习2：写一个脚本
			传递两个文件路径作为参数给脚本，计算这两个文件中所有空白行之和；

				#!/bin/bash

				spaceline1=$(grep "^[[:space:]]*$" $1 |	wc -l)
				spaceline2=$(grep "^[[:space:]]*$" $2 |	wc -l)

				echo "The sum of space line: $[$spaceline1+$spaceline2]"

		练习3：统计/etc, /var, /usr目录共有多少个一级子目录和文件；


	条件测试：
		判断某需求是否满足，需要由测试机制来实现；

		Note: 专用的测试表达式需要由测试命令辅助完成测试过程；

		测试命令：
			test EXPRESSION
			[ EXPRESSION ]
			[[ EXPRESSION ]]

			Note: EXPRESSION前后必须有空白字符；

		bash的测试类型：
			数值测试：
				-gt: 是否大于；
				-ge: 是否大于等于；
				-eq: 是否等于；
				-ne: 是否不等于；
				-lt: 是否小于；
				-le: 是否小于等于；
			字符串测试：
				==：是否等于；
				>: 是否大于；
				<: 是否小于；
				!=: 是否不等于；
				=~: 左侧字符串是否能够被右侧的PATTERN所匹配；
					Note: 此表达式一般用于[[  ]]中；
				-z "STRING"：测试字符串是否为空，空则为真，不空则为假；
				-n "STRING"：测试字符串是否不空，不空则为真，空则为假；

				Note：用于字符串比较时的用到的操作数都应该使用引号；

			文件测试

	bash自定义退出状态码
		exit [n]：自定义退出状态码；
			注意：脚本中一旦遇到exit命令，脚本会立即终止；终止退出状态取决于exit命令后面的数字；

		注意：如果未给脚本指定退出状态码，整个脚本的退出状态码取决于脚本中执行的最后一条命令的状态码；

		练习：写一个脚本
			接受一个文件路径作为参数；
				如果参数个数小于1，则提示用户“至少应该给一个参数”，并立即退出；
				如果参数个数不小于1，则显示第一个参数所指向的文件中的空白行数；

回顾：egrep, 扩展的正则表达式,  变量, 配置文件, 算术运算, 条件测试, 退出状态码

	ERE:
		字符匹配：., [], [^]
		次数匹配：*, ?, +, {m,n}
		位置锚定：^, $, \<, \>, \b
		分组：(), \1, \2, ..
		或者: a|b

	变量：
		本地变量
		环境变量
		局部变量
		位置变量
		特殊变量

		不能使用保留字；

		命令：unset, export, declare -x, set, env, printenv, readonly

	配置文件：
		profile, bashrc

	算术运算：
		let, $[], $(()), expr

		+=, -=, *=, /=

		++, --

	条件测试：
		test, [], [[]]

		三种：
			数值：-lt, -le, -gt, -ge, -ne, -eq
			字符串：==, !=, >, <, =~, -z, -n
			文件		

	自定义退出码：
		exit [n]

		[ $# -lt 1 ] && echo "At least one argument." && exit 1

vim编辑器

	简介
		vi: Visual Interface，文本编辑器

		文本：ASCII, Unicode

		文本编辑种类：
			行编辑器: sed
			全屏编辑器：nano, vi

		VIM - Vi IMproved

	使用
		vim：模式化的编辑

			基本模式：
				编辑模式，命令模式
				输入模式
				末行模式：
					内置的命令行接口

			打开文件：
				# vim [OPTION]... FILE...
					+#: 打开文件后，直接让光标处于第#行的行首；
					+/PATTERN：打开文件后，直接让光标处于第一个被PATTERN匹配到的行的行首；

			模式转换：
				编辑模式 --> 输入模式
					i: insert, 在光标所在处输入；
					a: append, 在光标所在处后面输入；
					o: 在当前光标所在行的下方打开一个新行；
					I：在当前光标所在行的行首输入；
					A：在当前光标所在行的行尾输入；
					O：在当前光标所在行的上方打开一个新行；
					c
					C

				输入模式 --> 编辑模式
					ESC

				编辑模式 --> 末行模式
					:

				末行模式 --> 编辑模式
					ESC

			关闭文件：
				:q 退出
				:q! 强制退出，丢弃做出的修改；
				:wq 保存退出
				:x 保存退出
				:w /PATH/TO/SOMEWHERE

				ZZ: 保存退出；

		光标跳转：
			
			字符间跳转：
				h, j, k, l
					h: 左
					l: 右
					j: 下
					k: 上

				#COMMAND：跳转由#指定的个数的字符；

			单词间跳转：
				w：下一个单词的词首
				e：当前或下一单词的词尾
				b：当前或前一个单词的词首

				#COMMAND：由#指定一次跳转的单词数

			行首行尾跳转：
				^: 跳转至行首的第一个非空白字符；
				0: 跳转至行首；
				$: 跳转至行尾；

			行间移动：
				#G：跳转至由#指定行；
				G：最后一行；
				1G, gg: 第一行；

			句间移动：
				)
				(

			段落间移动：
				}
				{

	vim的编辑命令：

		字符编辑：
			x: 删除光标处的字符；
			#x: 删除光标处起始的#个字符；

			xp: 交换光标所在处的字符及其后面字符的位置；

		替换命令(r, replace)
			r: 替换光标所在处的字符

		删除命令：
			d: 删除命令，可结合光标跳转字符，实现范围删除；
				d$: 
				d^:
				d0:

				dw
				de
				db

					#COMMAND

				dd: 删除光标所在的行；
					#dd：多行删除；

		粘贴命令(p, put, paste)：
			p：缓冲区存的如果为整行，则粘贴当前光标所在行的下方；否则，则粘贴至当前光标所在处的后面；
			P：缓冲区存的如果为整行，则粘贴当前光标所在行的上方；否则，则粘贴至当前光标所在处的前面；

		复制命令(y, yank)：
			y: 复制，工作行为相似于d命令；
				y$
				y0
				y^

				ye
				yw
				yb

					#COMMAND

				yy：复制行
					#yy: 复制多行；

		改变命令(c, change)
			c: 修改
				编辑模式 --> 输入模式

				c$
				c^
				c0

				cb
				ce
				cw
					#COMMAND

				cc：删除并输入新内容
				#cc: 

		其它编辑操作

			可视化模式：
				v: 按字符选定
				V：按行行定

				Note：经常结合编辑命令；
					d, c, y

			撤消此前的编辑：
				u(undo)：撤消此前的操作；
					#u: 撤消指定次数的操作；

			撤消此前的撤消：
				Ctrl+r

			重复前一个编辑操作：
				.

		翻屏操作：
			Ctrl+f: 向文件尾部翻一屏；
			Ctrl+b: 向文件首部翻一屏；

			Ctrl+d: 向文件尾部翻半屏；
			Ctrl+u：向文件首部翻半屏；

		vim自带的练习教程：
			vimtutor

	vim中的末行模式：
		内建的命令行接口

		(1) 地址定界
			:start_pos,end_pos
				#: 具体第#行，例如2表示第2行；
				#,#: 从左侧#表示行起始，到右侧#表示行结尾；
				#,+#: 从左侧#表示的行起始，加上右侧#表示的行数；
				.: 当前行
				$: 最后一行
					.,$-1
				%：全文, 相当于1,$

				/pat1/,/pat2/：
					从第一次被pat1模式匹配到的行开始，一直到第一次被pat2匹配到的行结束；
					#,/pat/
					/pat/,$

			使用方式：
				后跟一个编辑命令
					d
					y
					w /PATH/TO/SOMEWHERE: 将范围内的行另存至指定文件中；
					r /PATH/FROM/SOMEFILE：在指定位置插入指定文件中的所有内容；

		(2) 查找
			/PATTERN：从当前光标所在处向文件尾部查找；
			?PATTERN：从当前光标所在处向文件首部查找；
				n：与命令同方向；
				N：与命令反方向；

		(3) 查找并替换
			s: 在末行模式下完成查找替换操作
				s/要查找的内容/替换为的内容/修饰符
					要查找的内容：可使用模式
					替换为的内容：不能使用模式，但可以使用\1, \2, ...等后向引用符号；还可以使用“&”引用前面查找时查找到的整个内容；
					修饰符：
						i: 忽略大小写
						g: 全局替换；默认情况下，每一行只替换第一次出现；

				查找替换中的分隔符/可替换为其它字符，例如
					s@@@
					s###

			练习：
				1、复制/etc/grub2.cfg至/tmp/目录，用查找替换命令删除/tmp/grub2.cfg文件中的行首的空白字符；
					%s/^[[:space:]]\+//g

				2、复制/etc/rc.d/init.d/functions文件至/tmp目录，用查找替换命令为/tmp/functions的每行开头为空白字符的行的行首添加一个#号；
					:%s/^[[:space:]]/#&/

		多文件模式：
			vim FILE1 FILE2 FILE3 ...
				:next 下一个
				:prev 前一个
				:first 第一个
				:last 最后一个

				:wall 保存所有
				:qall 退出所有

		窗口分隔模式：
			vim -o|-O FILE1 FILE2 ...
				-o: 水平分割
				-O: 垂直分割

				在窗口间切换：Ctrl+w, Arrow

		单文件窗口分割：
			Ctrl+w,s: split, 水平分割
			Ctrl+w,v: vertical, 垂直分割

		定制vim的工作特性：
			配置文件：永久有效
				全局：/etc/vimrc
				个人：~/.vimrc

			末行：当前vim进程有效

			(1) 行号
				显示：set number, 简写为set nu
				取消显示：set nonumber, 简写为set nonu
			(2) 括号匹配
				匹配：set showmatch, 简写为set sm
				取消：set nosm
			(3) 自动缩进
				启用：set ai
				禁用：set noai
			(4) 高亮搜索
				启用：set hlsearch
				禁用：set nohlsearch
			(5) 语法高亮
				启用：syntax on
				禁用：syntax off
			(6) 忽略字符的大小写
				启用：set ic
				不忽略：set noic

			获取帮助：
				:help 
				:help subject

		问题：如何设置tab缩进为4个字符？

		练习：
			1、复制/etc/rc.d/init.d/functions文件至/tmp目录；替换/tmp/functions文件中的/etc/sysconfig/init为/var/log；
			2、删除/tmp/functions文件中所有以#开头，且#后面至少有一个空白字符的行的行首的#号；

bash条件测试：
	
	测试方式：
		test EXPRESSION
		[ EXPRESSION ]
		[[ EXPRESSION ]]

	测试表达式的类别：
		数值比较
		字符串测试
		文件测试：
			存在性测试
				-a FILE
				-e FILE: 文件存在性测试，存在为真，否则为假；
			存在性及类别测试
				-b FILE：是否存在且为块设备文件；
				-c FILE：是否存在且为字符设备文件；
				-d FILE：是否存在且为目录文件；
				-f FILE：是否存在且为普通文件；
				-h FILE 或 -L FILE：存在且为符号链接文件；
				-p FILE：是否存在且为命名管道文件；
				-S FILE：是否存在且为套接字文件；
			文件权限测试：
				-r FILE：是否存在且可读
				-w FILE: 是否存在且可写
				-x FILE: 是否存在且可执行
			文件特殊权限测试：
				-g FILE：是否存在且拥有sgid权限；
				-u FILE：是否存在且拥有suid权限；
				-k FILE：是否存在且拥有sticky权限；
			文件大小测试：
				-s FILE: 是否存且非空；
			文件是否打开：
				-t fd: fd表示文件描述符是否已经打开且与某终端相关


				-N FILE：文件自动上一次被读取之后是否被修改过；
				-O FILE：当前有效用户是否为文件属主；
				-G FILE：当前有效用户是否为文件属组；

			双目测试：
				FILE1 -ef FILE2: FILE1与FILE2是否指向同一个设备上的相同inode

				FILE1 -nt FILE2: FILE1是否新于FILE2；
				FILE1 -ot FILE2: FILE1是否旧于FILE2；

	组合测试条件：
		逻辑运算：
			第一种方式：
				COMMAND1 && COMMAND2
				COMMAND1 || COMMAND2
				! COMMAND

				[ -e FILE ] && [ -r FILE ]

			第二种方式：
				EXPRESSION1 -a EXPRESSION2
				EXPRESSION1 -o EXPRESSION2
				! EXPRESSION

				必须使用测试命令进行；

			# [ -z "$hostName" -o "$hostName"=="localhost.localdomain" ] && hostname www.magedu.com
			# [ -f /bin/cat -a -x /bin/cat ] && cat /etc/fstab

回顾：
	vim编辑器、条件测试之文件测试、组合测试条件

	vim：
		编辑模式、输入模式、末行模式、可视化模式

		跳转：h,j,k,l,w,b,e,),(,},{, G, ^, 0, $
		编辑：x, r, c, d, y, p, u, ctrl+r
		查找: /, ?, n, N
		查找替换: s
			s///
				g:
				i:
		末行定界：
			#
			m,n
			m,+#
			.
			$
			/pat1/
			/pat1/,/pat2/
			%

	文件测试：
		单目：-e, -f, -d, -b, -c, -L, -P, -S, -r, -w, -x, -s
		双目：-nt, -ot

	组合测试：-a, -o, !


文件查找：
	在文件系统上查找符合条件的文件；

	文件查找：locate, find
		非实时查找(数据库查找)：locate
		实时查找：find

	locate：
		依赖于事先构建的索引；索引的构建是在系统较为空闲时自动进行(周期性任务)；手动更新数据库(updatedb)；

		索引构建过程需要遍历整个根文件系统，极消耗资源；

		工作特点:
			查找速度快；
			模糊查找；
			非实时查找；

		locate KEYWORD

	find：
		实时查找工具，通过遍历指定路径下的文件系统完成文件查找；

		工作特点：
			查找速度略慢；
			精确查找；
			实时查找；

		语法：
			find [OPTION]... [查找路径] [查找条件] [处理动作]
				查找路径：指定具体目标路径；默认为当前目录；
				查找条件：指定的查找标准，可以文件名、大小、类型、权限等标准进行；默认为找出指定路径下的所有文件；
				处理动作：对符合条件的文件做什么操作；默认输出至屏幕；

				查找条件：
					根据文件名查找：
						-name "文件名称"：支持使用glob
							*, ?, [], [^]
						-iname "文件名称"：不区分字母大小写

						-regex "PATTERN"：以PATTERN匹配整个文件路径字符串，而不仅仅是文件名称；

					根据属主、属组查找：
						-user USERNAME：查找属主为指定用户的文件；
						group GRPNAME: 查找属组为指定组的文件；

						-uid UserID：查找属主为指定的UID号的文件；
						-gid GroupID：查找属组为指定的GID号的文件；

						-nouser：查找没有属主的文件；
						-nogroup：查找没有属组的文件；

					根据文件类型查找：
						-type TYPE:
							f: 普通文件
							d: 目录文件
							l: 符号链接文件
							s：套接字文件
							b: 块设备文件
							c: 字符设备文件
							p: 管道文件

					组合条件：
						与：-a
						或：-o
						非：-not, !

						!A -a !B = !(A -o B)
						!A -o !B = !(A -a B)

						找出/tmp目录下，属主不是root，且文件名不是fstab的文件；
							find /tmp \( -not -user root -a -not -name 'fstab' \) -ls
							find /tmp -not \( -user root -o -name 'fstab' \) -ls

					根据文件大小来查找：
						-size [+|-]#UNIT
							常用单位：k, M, G

							#UNIT: (#-1, #]
							-#UNIT：[0,#-1]
							+#UNIT：(#,oo)

					根据时间戳：
						以“天”为单位；
							-atime [+|-]#,  
								#: [#,#+1)
								+#: [#+1,oo]
								-#: [0,#)
							-mtime
							-ctime

						以“分钟”为单位：
							-amin
							-mmin
							-cmin

					根据权限查找：
						-perm [/|-]MODE
							MODE: 精确权限匹配
							/MODE：任何一类(u,g,o)对象的权限中只要能一位匹配即可；
							-MODE：每一类对象都必须同时拥有为其指定的权限标准；

				处理动作：
					-print：默认的处理动作，显示至屏幕；
					-ls：类似于对查找到的文件执行“ls -l”命令；
					-delete：删除查找到的文件；
					-fls /path/to/somefile：查找到的所有文件的长格式信息保存至指定文件中；
					-ok COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令；
						对于每个文件执行命令之前，都会交互式要求用户确认；
					-exec COMMAND {} \; 对查找到的每个文件执行由COMMAND指定的命令; 
						{}: 用于引用查找到的文件名称自身；

					注意：find传递查找到的文件至后面指定的命令时，查找到所有符合条件的文件一次性传递给后面的命令；
					有些命令不能接受过多参数，此时命令执行可能会失败；另一种方式可规避此问题：
						find | xargs COMMAND 

		练习：
			1、查找/var目录下属主为root，且属组为mail的所有文件或目录；
				# find /var -user root -group mail

			2、查找/usr目录下不属于root、bin或hadoop的所有文件或目录；
				# find /usr -not -user root -a -not -user bin -a -not -user hadoop
				# find /usr -not \( -user root -o -user bin -o -user hadoop \)

			3、查找/etc目录下最周一周内其内容修改过，同时属主不为root，也不是hadoop的文件或目录；
				# find /etc -mtime -7 -a -not -user root -a -not -user hadoop
				# find /etc/ -mtime -7 -a -not \( -user root -o -user hadoop \)

			4、查找当前系统上没有属主或属组，且最近一个周内曾被访问过的文件或目录；
				# find / -nouser -a -nogroup -a -atime -7

			5、查找/etc目录下大于1M且类型为普通文件的所有文件；
				# find /etc -size +1M -type f


			6、查找/etc目录下所有用户都没有写权限的文件；
			# find /etc -not -perm /222

			7、查找/etc目录下至少有一类用户没有执行权限的文件；
			# find /etc -not -perm -111

			8、查找/etc/init.d目录下，所有用户都有执行权限，且其它用户有写权限的文件；
			# find /etc/init.d -perm -113

Linux文件系统上的特殊权限

	SUID, SGID, Sticky

	1 权限
		r, w, x

		user, group, other

	2 安全上下文
		前提：进程有属主和属组；文件有属主和属组；
		(1) 任何一个可执行程序文件能不能启动为进程：取决发起者对程序文件是否拥有执行权限；
		(2) 启动为进程之后，其进程的属主为发起者；进程的属组为发起者所属的组；
		(3) 进程访问文件时的权限，取决于进程的发起者：
			(a) 进程的发起者，同文件的属主：则应用文件属主权限；
			(b) 进程的发起者，属于文件的属组；则应用文件属组权限；
			(c) 应用文件“其它”权限；

	3 SUID
		(1) 任何一个可执行程序文件能不能启动为进程：取决发起者对程序文件是否拥有执行权限；
		(2) 启动为进程之后，其进程的属主为原程序文件的属主；

		权限设定：
			chmod u+s FILE... 
			chmod u-s FILE...

	4 SGID
		默认情况下，用户创建文件时，其属组为此用户所属的基本组；
		一旦某目录被设定了SGID，则对此目录有写权限的用户在此目录中创建的文件所属的组为此目录的属组；

		权限设定：
			chmod g+s DIR...
			chmod g-s DIR...

	5 Sticky
		对于一个多人可写的目录，如果设置了sticky，则每个用户仅能删除自己的文件；

		权限设定：
			chmod o+t DIR...
			chmod o-t DIR...

		SUID SGID STICKY
			000 0
			001 1
			010 2
			011 3
			100 4
			101 5
			110 6
			111 7

			chmod 4777 /tmp/a.txt

		几个权限位映射：
			SUID: user, 占据属主的执行权限位；
				s: 属主拥有x权限
				S：属主没有x权限
			SGID: group,  占据group的执行权限位；
				s: group拥有x权限
				S：group没有x权限			
			Sticky: other, 占据ohter的执行权限位；
				t: other拥有x权限
				T：other没有x权限


bash脚本编程：
	过程式编程语言：
		顺序执行
		选择执行
		循环执行

	选择执行：
		if 判断条件
		then
			条件为真的分支代码
		fi

		if 判断条件; then
			条件为真的分支代码
		else
			条件为假的分支代码
		fi



































































					




















































   	

























































	
























			




















