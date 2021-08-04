# Установка и настройка сервера VNC

### Установка VNC-сервера

```

# yum install tigervnc-server tigervnc-server-module
```
### Настройка пароля VNC для пользователей

```

Смените пользователя на того пользователя, которого хотите использовать для VNC:
# su - vncuser1

Задайте пароль VNC для пользователя:
$ vncpasswd

```

### Отредактируйте /lib/systemd/system/vncserver@.service в вашем предпочтительном редакторе

необходимо прописать имя пользователя

```

# vi /lib/systemd/system/vncserver@.service

# The vncserver service unit file
#
# Quick HowTo:
# 1. Copy this file to /etc/systemd/system/vncserver@.service
# 2. Replace <USER> with the actual user name and edit vncserver
#    parameters in the wrapper script located in /usr/bin/vncserver_wrapper
# 3. Run `systemctl daemon-reload`
# 4. Run `systemctl enable vncserver@:<display>.service`
#
# DO NOT RUN THIS SERVICE if your local area network is
# untrusted!  For a secure way of using VNC, you should
# limit connections to the local host and then tunnel from
# the machine you want to view VNC on (host A) to the machine
# whose VNC output you want to view (host B)
#
# [user@hostA ~]$ ssh -v -C -L 590N:localhost:590M hostB
#
# this will open a connection on port 590N of your hostA to hostB's port 590M
# (in fact, it ssh-connects to hostB and then connects to localhost (on hostB).
# See the ssh man page for details on port forwarding)
#
# You can then point a VNC client on hostA at vncdisplay N of localhost and with
# the help of ssh, you end up seeing what hostB makes available on port 590M
#
# Use "-nolisten tcp" to prevent X connections to your VNC server via TCP.
#
# Use "-localhost" to prevent remote VNC clients connecting except when
# doing so through a secure tunnel.  See the "-via" option in the
# `man vncviewer' manual page.
[Unit]
Description=Remote desktop service (VNC)
After=syslog.target network.target

[Service]
Type=simple

# Clean any existing files in /tmp/.X11-unix environment
ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'
ExecStart=/usr/bin/vncserver_wrapper <USER> %i                                   #ИМЯ ПОЛЬЗОВАТЕЛЯ VNC
ExecStop=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :'

[Install]
WantedBy=multi-user.target

```
##### в зависимости от версии и дистрибутива конфиг файл может быть :

```

/etc/systemd/system/vncserver@.service
 со следующим содержимым

 [Unit] 
Description=Remote desktop service (VNC) 
After=syslog.target network.target 

[Service] 
Type=forking 
WorkingDirectory=/home/tecmint 
User=tecmint 
Group=tecmint 

PIDFile=/home/tecmint/.vnc/%H%i.pid 

ExecStartPre=/bin/sh -c '/usr/bin/vncserver -kill %i > /dev/null 2>&1 || :' 
ExecStart=/usr/bin/vncserver -autokill %i 
ExecStop=/usr/bin/vncserver -kill %i 

[Install] 
WantedBy=multi-user.target
```

### Запуск демона VNC

#####Чтобы запустить службу VNC, вам нужно отключить SELinux

```

# setenforce 0
```

#### Теперь перезагрузите системный менеджер, чтобы применить последние изменения, а затем запустите службу VNC, включите ее автоматический запуск во время загрузки системы и проверьте, работает ли он, используя следующие команды systemctl.

```

# systemctl daemon-reload
# systemctl start vncserver@:1
# systemctl status vncserver@:1
# systemctl enable vncserver@:1
```


#### На этом этапе служба VNC запущена и работает, убедитесь, что VNC-сервер прослушивает TCP-порт 5901, используя команду netstat.

```
netstat -tlnp
```

#### настройка брендмаузера

```

# firewall-cmd --permanent --add-port=5901/tcp
# firewall-cmd --reload

```

