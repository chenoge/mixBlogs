---
title: linux-开机自启动服务
date: 2019-05-27 21:07:31
tags: [linux,chkconfig]
---

##### 创建脚本测试

```shell
[root@anuo ~]# vim anuo.sh  --创建个脚本
```

```shell
# !/bin/bash
# chkconfig: 35 53 88  --35是指定3和5级别启动、53是启动的顺序、88是关闭的顺序
# description: is anuo  --可以随便说点啥， 最好的说明这个脚本的用途啥的。
echo Anuo Come on  --脚本的内容
```

```shell
[root@anuo ~]# mv anuo.sh /etc/init.d/  --必须将脚本放到/etc/init.d/目录下
[root@anuo ~]# chmod +x /etc/init.d/anuo.sh  --别忘记给脚本加执行权限
[root@anuo ~]# chkconfig --add anuo.sh  --添加开机自启动
[root@anuo ~]# chkconfig --list | grep anuo.sh  --可以看到开启级别的启动
```

```
anuo.sh   0:关闭    1:关闭    2:关闭    3:启用    4:关闭    5:启用    6:关闭
```

<!--more-->

<br/>



##### 小测试

```shell
[root@anuo ~]# ll /etc/rc.d/rc3.d/|grep anuo.sh
lrwxrwxrwx  1 root root 17 5月  12 19:10 S53anuo.sh -> ../init.d/anuo.sh
```

查看到3级别启动的文件里一个S53anuo.sh的链接文件

（S表示开启，53也就对应了配置文件里的53的启动的顺序）

<br/>



```shell
[root@anuo ~]# chkconfig anuo.sh off --设置开机不启动
[root@anuo ~]# ll /etc/rc.d/rc3.d/|grep anuo.sh     
lrwxrwxrwx  1 root root 17 5月  12 19:30 K88anuo.sh -> ../init.d/anuo.sh
```

再次查看发现S53anuo.sh的链接文件没有了，却多了个K88anuo.sh的链接文件

（K表示不开启 88对应的是配置文件里的88关闭的顺序）

<br/>



小结：

- 要把脚本放到`/etc/init.d/`目录下并给执行权限。

- 当`chkconfig`设置开机自启动时候会在相应的启动级别的文件里创建**S开头**的链接文件，同时会删除以**K开头**的对应的链接文件，反之也亦然。

<br/>



##### [nginx开机启动脚本](<https://www.nginx.com/resources/wiki/start/topics/examples/redhatnginxinit/>)

```shell
#!/bin/sh
#
# nginx - this script starts and stops the nginx daemon
#
# chkconfig:   - 85 15
# description:  NGINX is an HTTP(S) server, HTTP(S) reverse \
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
   user=`$nginx -V 2>&1 | grep "configure arguments:.*--user=" | sed 's/[^*]*--user=\([^ ]*\).*/\1/g' -`
   if [ -n "$user" ]; then
      if [ -z "`grep $user /etc/passwd`" ]; then
         useradd -M -s /bin/nologin $user
      fi
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
    fi
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
```

