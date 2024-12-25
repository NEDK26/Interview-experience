## Docker

docker pull centos

启动容器并进入

docker run -it centos /bin/bash 



**查看运行过的所有容器**

```shell
docker ps
docker ps -a 
exit #退出
Ctrl + P + Q # 退出但不停止
```

**删除容器**

```shell
docker rm -f containerId
docker rm -f $(docker ps -aq)
docker ps -aq|xargs docker rm
```

**启动和停止容器**

```shell
docker start containerId
docker restart contrainId
docker stop containerId
docker kill contrainerId
```

**后台启动容器**

```shell
docker run -d centos
# docker 容器使用后台运行，必须要有一个前台进程
# nginx ，容器启动后，发现没有提供服务，就会立刻停止
```

**查看日志**

```
docker logs --help

```

**查看容器中进程id**

```
docker top containerId
```

查看镜像元数据

```shell
docker inspect contrainerId
```

进入当前正在运行的容器

```shell
docker exec -it containerId bash/sh  
# 测试
minda66@DESKTOP-7GIR5EL:~$ docker exec -it 8cc3e791f9b4 /bin/bash
[root@8cc3e791f9b4 /]# docker ps
bash: docker: command not found
[root@8cc3e791f9b4 /]# ls
bin  etc   lib    lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
[root@8cc3e791f9b4 /]# ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 11:48 ?        00:00:00 /bin/sh -c while true;do ech
root       853     0  0 12:03 pts/0    00:00:00 /bin/bash
root       902     1  0 12:03 ?        00:00:00 /usr/bin/coreutils --coreuti
root       903   853  0 12:03 pts/0    00:00:00 



docker attach # 进入正在运行的终端
```

**拷贝到主机**

docker cp containerId：容器内路径  目的的主机路径



### Commit镜像

``` do
docker commit

docker commit -m"
```

