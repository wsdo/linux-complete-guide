<!--
 * @Author: starkwang
 * @Contact me: https://shudong.wang/about
 * @Date: 2019-10-18 15:10:38
 * @LastEditors: starkwang
 * @LastEditTime: 2019-10-18 15:10:38
 * @Description: file content
 -->

传统上基于进程或线程模型架构的web服务通过每进程或每线程处理并发连接请求，这势必会在网络和I/O操作时产生阻塞，其另一个必然结果则是对内存或CPU的利用率低下。生成一个新的进程/线程需要事先备好其运行时环境，这包括为其分配堆内存和栈内存，以及为其创建新的执行上下文等。这些操作都需要占用CPU，而且过多的进程/线程还会带来线程抖动或频繁的上下文切换，系统性能也会由此进一步下降。

在设计的最初阶段，nginx的主要着眼点就是其高性能以及对物理计算资源的高密度利用，因此其采用了不同的架构模型。受启发于多种操作系统设计中基于“事件”的高级处理机制，nginx采用了模块化、事件驱动、异步、单线程及非阻塞的架构，并大量采用了多路复用及事件通知机制。在nginx中，连接请求由为数不多的几个仅包含一个线程的进程worker以高效的回环(run-loop)机制进行处理，而每个worker可以并行处理数千个的并发连接及请求。

如果负载以CPU密集型应用为主，如SSL或压缩应用，则worker数应与CPU数相同；如果负载以IO密集型为主，如响应大量内容给客户端，则worker数应该为CPU个数的1.5或2倍。

Nginx会按需同时运行多个进程：一个主进程(master)和几个工作进程(worker)，配置了缓存时还会有缓存加载器进程(cache loader)和缓存管理器进程(cache manager)等。所有进程均是仅含有一个线程，并主要通过“共享内存”的机制实现进程间通信。主进程以root用户身份运行，而worker、cache loader和cache manager均应以非特权用户身份运行。

主进程主要完成如下工作：
1. 读取并验正配置信息；
2. 创建、绑定及关闭套接字；
3. 启动、终止及维护worker进程的个数；
4. 无须中止服务而重新配置工作特性；
5. 控制非中断式程序升级，启用新的二进制程序并在需要时回滚至老版本；
6. 重新打开日志文件，实现日志滚动；
7. 编译嵌入式perl脚本；

worker进程主要完成的任务包括：
1. 接收、传入并处理来自客户端的连接；
2. 提供反向代理及过滤功能；
3. nginx任何能完成的其它任务；

cache loader进程主要完成的任务包括：
1. 检查缓存存储中的缓存对象；
2. 使用缓存元数据建立内存数据库；

cache manager进程的主要任务：
1. 缓存的失效及过期检验；

Nginx的配置有着几个不同的上下文：main、http、server、upstream和location(还有实现邮件服务反向代理的mail)。配置语法的格式和定义方式遵循所谓的C风格，因此支持嵌套，还有着逻辑清晰并易于创建、阅读和维护等优势。


Nginx的代码是由一个核心和一系列的模块组成, 核心主要用于提供Web Server的基本功能，以及Web和Mail反向代理的功能；还用于启用网络协议，创建必要的运行时环境以及确保不同的模块之间平滑地进行交互。不过，大多跟协议相关的功能和某应用特有的功能都是由nginx的模块实现的。这些功能模块大致可以分为事件模块、阶段性处理器、输出过滤器、变量处理器、协议、upstream和负载均衡几个类别，这些共同组成了nginx的http功能。事件模块主要用于提供OS独立的(不同操作系统的事件机制有所不同)事件通知机制如kqueue或epoll等。协议模块则负责实现nginx通过http、tls/ssl、smtp、pop3以及imap与对应的客户端建立会话。

在nginx内部，进程间的通信是通过模块的pipeline或chain实现的；换句话说，每一个功能或操作都由一个模块来实现。例如，压缩、通过FastCGI或uwsgi协议与upstream服务器通信，以及与memcached建立会话等。

一、安装Nginx：

1、解决依赖关系

编译安装nginx需要事先需要安装开发包组"Development Tools"和 "Development Libraries"。同时，还需要专门安装pcre-devel包：
# yum -y install pcre-devel

2、安装

首先添加用户nginx，实现以之运行nginx服务进程：
# groupadd -r nginx
# useradd -r -g nginx nginx

接着开始编译和安装：
# ./configure \
  --prefix=/usr/local/nginx \
  --sbin-path=/usr/local/nginx/sbin/nginx \
  --conf-path=/etc/nginx/nginx.conf \
  --error-log-path=/var/log/nginx/error.log \
  --http-log-path=/var/log/nginx/access.log \
  --pid-path=/var/run/nginx/nginx.pid  \
  --lock-path=/var/lock/nginx.lock \
  --user=nginx \
  --group=nginx \
  --with-http_ssl_module \
  --with-http_flv_module \
  --with-http_stub_status_module \
  --with-http_gzip_static_module \
  --http-client-body-temp-path=/var/tmp/nginx/client/ \
  --http-proxy-temp-path=/var/tmp/nginx/proxy/ \
  --http-fastcgi-temp-path=/var/tmp/nginx/fcgi/ \
  --http-uwsgi-temp-path=/var/tmp/nginx/uwsgi \
  --http-scgi-temp-path=/var/tmp/nginx/scgi \
  --with-pcre
# make && make install

说明：如果想使用nginx的perl模块，可以通过为configure脚本添加--with-http_perl_module选项来实现，但目前此模块仍处于实验性使用阶段，可能会在运行中出现意外，因此，其实现方式这里不再介绍。如果想使用基于nginx的cgi功能，也可以基于FCGI来实现，具体实现方法请参照网上的文档。

3、为nginx提供SysV init脚本:

新建文件/etc/rc.d/init.d/nginx，内容如下：
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15 
# description:  Nginx is an HTTP(S) server, HTTP(S) reverse \
#               proxy and IMAP/POP3 proxy server
# processname: nginx
# config:      /etc/nginx/nginx.conf
# config:      /etc/sysconfig/nginx
# pidfile:     /var/run/nginx.pid
 
# Source function library.
. /etc/rc.d/init.d/functions
 
# Source networking configuration.
. /etc/sysconfig/network
 
# Check that networking is up.
[ "$NETWORKING" = "no" ] && exit 0
 
nginx="/usr/sbin/nginx"
prog=$(basename $nginx)
 
NGINX_CONF_FILE="/etc/nginx/nginx.conf"
 
[ -f /etc/sysconfig/nginx ] && . /etc/sysconfig/nginx
 
lockfile=/var/lock/subsys/nginx
 
make_dirs() {
   # make required directories
   user=`nginx -V 2>&1 | grep "configure arguments:" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   options=`$nginx -V 2>&1 | grep 'configure arguments:'`
   for opt in $options; do
       if [ `echo $opt | grep '.*-temp-path'` ]; then
           value=`echo $opt | cut -d "=" -f 2`
           if [ ! -d "$value" ]; then
               # echo "creating" $value
               mkdir -p $value && chown -R $user $value
           fi
       fi
   done
}
 
start() {
    [ -x $nginx ] || exit 5
    [ -f $NGINX_CONF_FILE ] || exit 6
    make_dirs
    echo -n $"Starting $prog: "
    daemon $nginx -c $NGINX_CONF_FILE
    retval=$?
    echo
    [ $retval -eq 0 ] && touch $lockfile
    return $retval
}
 
stop() {
    echo -n $"Stopping $prog: "
    killproc $prog -QUIT
    retval=$?
    echo
    [ $retval -eq 0 ] && rm -f $lockfile
    return $retval
}
 
restart() {
    configtest || return $?
    stop
    sleep 1
    start
}
 
reload() {
    configtest || return $?
    echo -n $"Reloading $prog: "
    killproc $nginx -HUP
    RETVAL=$?
    echo
}
 
force_reload() {
    restart
}
 
configtest() {
  $nginx -t -c $NGINX_CONF_FILE
}
 
rh_status() {
    status $prog
}
 
rh_status_q() {
    rh_status >/dev/null 2>&1
}
 
case "$1" in
    start)
        rh_status_q && exit 0
        $1
        ;;
    stop)
        rh_status_q || exit 0
        $1
        ;;
    restart|configtest)
        $1
        ;;
    reload)
        rh_status_q || exit 7
        $1
        ;;
    force-reload)
        force_reload
        ;;
    status)
        rh_status
        ;;
    condrestart|try-restart)
        rh_status_q || exit 0
            ;;
    *)
        echo $"Usage: $0 {start|stop|status|restart|condrestart|try-restart|reload|force-reload|configtest}"
        exit 2
esac

而后为此脚本赋予执行权限：
# chmod +x /etc/rc.d/init.d/nginx

添加至服务管理列表，并让其开机自动启动：
# chkconfig --add nginx
# chkconfig nginx on

而后就可以启动服务并测试了：
# service nginx start


二、安装mysql-5.5.28

1、准备数据存放的文件系统

新建一个逻辑卷，并将其挂载至特定目录即可。这里不再给出过程。

这里假设其逻辑卷的挂载目录为/mydata，而后需要创建/mydata/data目录做为mysql数据的存放目录。

2、新建用户以安全方式运行进程：

# groupadd -r mysql
# useradd -g mysql -r -s /sbin/nologin -M -d /mydata/data mysql
# chown -R mysql:mysql /mydata/data

3、安装并初始化mysql-5.5.28

首先下载平台对应的mysql版本至本地，这里是32位平台，因此，选择的为mysql-5.5.28-linux2.6-i686.tar.gz，其下载位置为ftp://172.16.0.1/pub/Sources/mysql-5.5。

# tar xf mysql-5.5.28-linux2.6-i686.tar.gz -C /usr/local
# cd /usr/local/
# ln -sv mysql-5.5.24-linux2.6-i686  mysql
# cd mysql 

# chown -R mysql:mysql  .
# scripts/mysql_install_db --user=mysql --datadir=/mydata/data
# chown -R root  .

4、为mysql提供主配置文件：

# cd /usr/local/mysql
# cp support-files/my-large.cnf  /etc/my.cnf

并修改此文件中thread_concurrency的值为你的CPU个数乘以2，比如这里使用如下行：
thread_concurrency = 2

另外还需要添加如下行指定mysql数据文件的存放位置：
datadir = /mydata/data


5、为mysql提供sysv服务脚本：

# cd /usr/local/mysql
# cp support-files/mysql.server  /etc/rc.d/init.d/mysqld

添加至服务列表：
# chkconfig --add mysqld
# chkconfig mysqld on

而后就可以启动服务测试使用了。


为了使用mysql的安装符合系统使用规范，并将其开发组件导出给系统使用，这里还需要进行如下步骤：

6、输出mysql的man手册至man命令的查找路径：

编辑/etc/man.config，添加如下行即可：
MANPATH  /usr/local/mysql/man

7、输出mysql的头文件至系统头文件路径/usr/include：

这可以通过简单的创建链接实现：
# ln -sv /usr/local/mysql/include  /usr/include/mysql

8、输出mysql的库文件给系统库查找路径：

# echo '/usr/local/mysql/lib' > /etc/ld.so.conf.d/mysql.conf

而后让系统重新载入系统库：
# ldconfig

9、修改PATH环境变量，让系统可以直接使用mysql的相关命令。具体实现过程这里不再给出。


三、编译安装php-5.4.4

1、解决依赖关系：

请配置好yum源（可以是本地系统光盘）后执行如下命令：
# yum -y groupinstall "X Software Development" 

如果想让编译的php支持mcrypt、mhash扩展和libevent，此处还需要下载ftp://172.16.0.1/pub/Sources/ngnix目录中的如下几个rpm包并安装之：
libmcrypt-2.5.8-4.el5.centos.i386.rpm
libmcrypt-devel-2.5.8-4.el5.centos.i386.rpm
mhash-0.9.9-1.el5.centos.i386.rpm
mhash-devel-0.9.9-1.el5.centos.i386.rpm
mcrypt-2.6.8-1.el5.i386.rpm

最好使用升级的方式安装上面的rpm包，命令格式如下：
# rpm -Uvh 


另外，也可以根据需要安装libevent，系统一般会自带libevent，但版本有些低。因此可以升级安装之，它包含如下两个rpm包。
libevent-2.0.17-2.i386.rpm
libevent-devel-2.0.17-2.i386.rpm

说明：libevent是一个异步事件通知库文件，其API提供了在某文件描述上发生某事件时或其超时时执行回调函数的机制，它主要用来替换事件驱动的网络服务器上的event loop机制。目前来说， libevent支持/dev/poll、kqueue、select、poll、epoll及Solaris的event ports。

2、编译安装php-5.4.4

首先下载源码包至本地目录，下载位置ftp://172.16.0.1/pub/Sources/new_lamp。

# tar xf php-5.4.4.tar.bz2
# cd php-5.4.4
#  ./configure --prefix=/usr/local/php --with-mysql=/usr/local/mysql --with-openssl --enable-fpm --enable-sockets --enable-sysvshm  --with-mysqli=/usr/local/mysql/bin/mysql_config --enable-mbstring --with-freetype-dir --with-jpeg-dir --with-png-dir --with-zlib-dir --with-libxml-dir=/usr --enable-xml  --with-mhash --with-mcrypt  --with-config-file-path=/etc --with-config-file-scan-dir=/etc/php.d --with-bz2 --with-curl 

说明：如果前面第1步解决依赖关系时安装mcrypt相关的两个rpm包，此./configure命令还可以带上--with-mcrypt选项以让php支持mycrpt扩展。--with-snmp选项则用于实现php的SNMP扩展，但此功能要求提前安装net-snmp相关软件包。

# make
# make test
# make intall

为php提供配置文件：
# cp php.ini-production /etc/php.ini

为php-fpm提供Sysv init脚本，并将其添加至服务列表：
# cp sapi/fpm/init.d.php-fpm  /etc/rc.d/init.d/php-fpm
# chmod +x /etc/rc.d/init.d/php-fpm
# chkconfig --add php-fpm
# chkconfig php-fpm on

为php-fpm提供配置文件：
# cp /usr/local/php/etc/php-fpm.conf.default /usr/local/php/etc/php-fpm.conf 

编辑php-fpm的配置文件：
# vim /usr/local/php/etc/php-fpm.conf
配置fpm的相关选项为你所需要的值，并启用pid文件（如下最后一行）：
pm.max_children = 150
pm.start_servers = 8
pm.min_spare_servers = 5
pm.max_spare_servers = 10
pid = /usr/local/php/var/run/php-fpm.pid 

接下来就可以启动php-fpm了：
# service php-fpm start

使用如下命令来验正（如果此命令输出有中几个php-fpm进程就说明启动成功了）：
# ps aux | grep php-fpm


四、整合nginx和php5

1、编辑/etc/nginx/nginx.conf，启用如下选项：
location ~ \.php$ {
            root           html;
            fastcgi_pass   127.0.0.1:9000;
            fastcgi_index  index.php;
            fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
            include        fastcgi_params;
        }

2、编辑/etc/nginx/fastcgi_params，将其内容更改为如下内容：
fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx;
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;
fastcgi_param  SCRIPT_FILENAME    $document_root$fastcgi_script_name;
fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;

并在所支持的主页面格式中添加php格式的主页，类似如下：
location / {
            root   html;
            index  index.php index.html index.htm;
        }
        
而后重新载入nginx的配置文件：
# service nginx reload

3、在/usr/html新建index.php的测试页面，测试php是否能正常工作：
# cat > /usr/html/index.php << EOF
<?php
phpinfo();
?>

接着就可以通过浏览器访问此测试页面了。


五、安装xcache，为php加速：

1、安装
# tar xf xcache-2.0.0.tar.gz
# cd xcache-2.0.0
# /usr/local/php/bin/phpize
# ./configure --enable-xcache --with-php-config=/usr/local/php/bin/php-config
# make && make install

安装结束时，会出现类似如下行：
Installing shared extensions:     /usr/local/php/lib/php/extensions/no-debug-zts-20100525/

2、编辑php.ini，整合php和xcache：

首先将xcache提供的样例配置导入php.ini
# mkdir /etc/php.d
# cp xcache.ini /etc/php.d

说明：xcache.ini文件在xcache的源码目录中。

接下来编辑/etc/php.d/xcache.ini，找到zend_extension开头的行，修改为如下行：
zend_extension = /usr/local/php/lib/php/extensions/no-debug-zts-20100525/xcache.so

注意：如果php.ini文件中有多条zend_extension指令行，要确保此新增的行排在第一位。

3、重新启动php-fpm
# service php-fpm restart


六、补充说明

如果要在SSL中使用php，需要在php的location中添加此选项：

fastcgi_param HTTPS on;




补充阅读材料：

Events is one of paradigms to achieve asynchronous execution. But not all asynchronous systems use events. That is about semantic meaning of these two - one is super-entity of another.

epoll and aio use different metaphors:

epoll is a blocking operation (epoll_wait()) - you block the thread until some event happens and then you dispatch the event to different procedures/functions/branches in your code.

In AIO you pass address of you callback function (completion routine) to the system and the system calls your function when something happens.

Problem with AIO is that your callback function code runs from system thread and so on top of system stack. A few problems with that as you can imagine.



