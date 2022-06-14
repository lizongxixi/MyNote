# Docker的基本组成

## Docker用途

比较Docker和虚拟机的不同：

- 传统虚拟机，虚拟出一套硬件，运行一个操作系统，之后再这个系统上安装和运行软件
- 容器内的应用直接运行在宿主机，容器没有自己的内核，也没有虚拟硬件，所以更加轻便
- 每个容器相互隔离，每个容器都有一个属于自己的文件系统，互不影响



DevOps（开发、运维）

**应用更快速的交付和部署**

传统：一堆帮助文档，安装程序

Docker：打包镜像发布测试，一键运行

**更便捷的升级和缩扩容**

使用了Docker之后，部署应用和搭积木一样！

**更简单的系统运维** 

容器化之后，开发和测试环境高度一致

**更高效的计算资源利用**

## Docker的基本组成

**镜像（image）**

docker镜像就是一个模板，可以利用这个模板来创建容器服务，tomcat镜像== 》run ==》 tomcat01容器（提供服务器），通过这个镜像可以创建多个容器（最终服务运行或者项目运行就是在容器中）。

**容器（container）**

docker利用容器技术，独立运行一个或者一个组的应用

基本命令：启动，停止，删除

可以将容器理解为一个简易的linux系统

**仓库（repository）**

仓库就是存放镜像的地方

仓库分私有仓库和公有仓库



## 安装Docker

环境准备

1.linux基础

2.CentOS 7

3.使用Xshell连接远程服务器

环境查看

```shell
[root@lzx /]# uname -r
3.10.0-1160.24.1.el7.x86_64
```

```shell
# 系统版本
[root@lzx /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```

官方文档

https://docs.docker.com/engine/install/centos/

1.如果之前有安装docker，那么需要先卸载

卸载命令

```shell
yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

2.如果是第一次安装Docker，那么需要先设置 Docker仓库，之后可以在仓库里安装和更新Docker

设置仓库的命令为：

```shell
yum install -y yum-utils

yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```



3.安装Docker

ce说明安装的是社区版Docker

```shell
yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
```



4.启动Docker

```shell
systemctl start docker
#查看docker版本，判断是否安装成功
docker version
```



5.测试docker的hello world

```shell
docker run hello-world
```

![image-20220513103925779](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220513103925779.png)

出现了 This message shows that …… correctly 说明已经可以正常工作了



6.查看一下hello world镜像

```shell
[root@lzx /]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
```



7.了解卸载docker

```shell
#卸载
yum remove docker-ce docker-ce-cli containerd.io
#删除对应文件夹
rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```



## 阿里云镜像加速

1.登录阿里云找到容器镜像服务 -》镜像工具 -》镜像加速器

![image-20220514154808062](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220514154808062.png)

2.配置使用

```shell
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://4a0ah74c.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```



## 回顾hello world流程

![image-20220514162748012](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220514162748012.png)



## 工作原理

Docker工作原理

Docker是一个Client-Server结构的系统，Docker的守护进程运行在主机上，通过Socket从客户端访问！

DockerServer接到Docker-Client的指令，就会执行这个命令！

Docker比VM快的原因:

1.Docker有着比虚拟机更少的抽象层

2.Docker利用的是宿主机的内核，VM需要的是Guest OS



# Docker的常用命令

## 帮助命令

```shell
docker version #显示docker的版本信息
docker info   #显示docker更加详细的信息
docker  --help #万能命令
```

### docker info    



效果：  显示docker的系统信息，包括镜像和容器的数量

```shell
Client:
 Context:    default
 Debug Mode: false
 Plugins:
  app: Docker App (Docker Inc., v0.9.1-beta3)
  buildx: Docker Buildx (Docker Inc., v0.8.2-docker)
  compose: Docker Compose (Docker Inc., v2.5.0)
  scan: Docker Scan (Docker Inc., v0.17.0)

Server:
 Containers: 2
  Running: 0
  Paused: 0
  Stopped: 2
 Images: 1
 Server Version: 20.10.16
 Storage Driver: overlay2
  Backing Filesystem: extfs
  Supports d_type: true
  Native Overlay Diff: true
  userxattr: false
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Cgroup Version: 1
 Plugins:
  Volume: local
  Network: bridge host ipvlan macvlan null overlay
  Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: io.containerd.runc.v2 io.containerd.runtime.v1.linux runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: 212e8b6fa2f44b9c21b2798135fc6fb7c53efc16
 runc version: v1.1.1-0-g52de29d
 init version: de40ad0
 Security Options:
  seccomp
   Profile: default
 Kernel Version: 3.10.0-1160.24.1.el7.x86_64
 Operating System: CentOS Linux 7 (Core)
 OSType: linux
 Architecture: x86_64
 CPUs: 1
 Total Memory: 1.795GiB
 Name: lzx
 ID: NG4S:5EXZ:HRPL:LARZ:IDV4:PXRP:RIAH:MYT3:QQJ6:G6LK:CFFM:QLLA
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
  127.0.0.0/8
 Registry Mirrors:
  https://4a0ah74c.mirror.aliyuncs.com/
 Live Restore Enabled: false

```



帮助文档、或者菜鸟教程查看命令的意思



## 镜像命令

### docker images  查看所有镜像

效果：查看本地主机上的所有镜像

```shell
[root@lzx ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
```

解释：

REPOSITORY	镜像的仓库源

TAG					镜像的标签

IMAGE ID			镜像的ID

CREATED			镜像的创建时间

SIZE					镜像的大小

### docker image inspect  镜像名:版本号 查看镜像的详细信息

docker image inspect tomcat:9.0

### docker search 搜索镜像

效果：搜索镜像

```shell
[root@lzx ~]# docker search mysql
NAME                           DESCRIPTION                                     STARS     OFFICIAL   AUTOMATED
mysql                          MySQL is a widely used, open-source relation…   12596     [OK]       
mariadb                        MariaDB Server is a high performing open sou…   4839      [OK]       
percona                        Percona Server is a fork of the MySQL relati…   576       [OK]       
phpmyadmin                     phpMyAdmin - A web interface for MySQL and M…   540       [OK]       
bitnami/mysql                  Bitnami MySQL Docker Image                      71                   [OK]
linuxserver/mysql-workbench                                                    36                   
linuxserver/mysql              A Mysql container, brought to you by LinuxSe…   35                   
ubuntu/mysql                   MySQL open source fast, stable, multi-thread…   33                   
circleci/mysql                 MySQL is a widely used, open-source relation…   25                   
google/mysql                   MySQL server for Google Compute Engine          21                   [OK]
rapidfort/mysql                RapidFort optimized, hardened image for mysql   12                   
bitnami/mysqld-exporter                                                        3                    
ibmcom/mysql-s390x             Docker image for mysql-s390x                    2                    
nasqueron/mysql                                                                1                    [OK]
vitess/mysqlctld               vitess/mysqlctld                                1                    [OK]
newrelic/mysql-plugin          New Relic Plugin for monitoring MySQL databa…   1                    [OK]
silintl/mysql-backup-restore   Simple docker image to perform mysql backups…   0                    [OK]
cimg/mysql                                                                     0                    
mirantis/mysql                                                                 0                    
drud/mysql-local-57            ddev mysql local container                      0                    
drud/mysql                                                                     0                    
drud/mysql-docker-local-57     This repo has been deprecated, new tags are …   0                    
drud/mysql-docker-local        docker containers for local womysql rk          0                    [OK]
docksal/mysql                  MySQL service images for Docksal - https://d…   0                    
drud/mysqld-exporter                                                           0        
```



### docker pull 下载镜像

```shell
[root@lzx ~]# docker pull mysql
Using default tag: latest        #如果不写tag，默认下载最新
latest: Pulling from library/mysql
72a69066d2fe: Pull complete 
93619dbc5b36: Pull complete 
99da31dd6142: Pull complete 
626033c43d70: Pull complete 
37d5d7efb64e: Pull complete 
ac563158d721: Pull complete 
d2ba16033dad: Pull complete 
688ba7d5c01a: Pull complete 
00e060b6d11d: Pull complete 
1c04857f594f: Pull complete 
4d7cfa90e6ea: Pull complete 
e0431212d27d: Pull complete 
Digest: sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest  #真实地址

#下载指定版本的mysql 镜像
[root@lzx ~]# docker pull mysql:5.7
5.7: Pulling from library/mysql
72a69066d2fe: Already exists    #这部分显示了docker的分层结构
93619dbc5b36: Already exists 
99da31dd6142: Already exists 
626033c43d70: Already exists 
37d5d7efb64e: Already exists 
ac563158d721: Already exists 
d2ba16033dad: Already exists 
0ceb82207cd7: Pull complete 
37f2405cae96: Pull complete 
e2482e017e53: Pull complete 
70deed891d42: Pull complete 
Digest: sha256:f2ad209efe9c67104167fc609cca6973c8422939491c9345270175a300419f94
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7

```

### docker rmi 删除镜像

```shell
[root@lzx ~]# docker images
REPOSITORY    TAG       IMAGE ID       CREATED        SIZE
mysql         5.7       c20987f18b13   4 months ago   448MB
mysql         latest    3218b38490ce   4 months ago   516MB
hello-world   latest    feb5d9fea6a5   7 months ago   13.3kB
[root@lzx ~]# docker rmi 321
Untagged: mysql:latest
Untagged: mysql@sha256:e9027fe4d91c0153429607251656806cc784e914937271037f7738bd5b8e7709
Deleted: sha256:3218b38490cec8d31976a40b92e09d61377359eab878db49f025e5d464367f3b
Deleted: sha256:aa81ca46575069829fe1b3c654d9e8feb43b4373932159fe2cad1ac13524a2f5
Deleted: sha256:0558823b9fbe967ea6d7174999be3cc9250b3423036370dc1a6888168cbd224d
Deleted: sha256:a46013db1d31231a0e1bac7eeda5ad4786dea0b1773927b45f92ea352a6d7ff9
Deleted: sha256:af161a47bb22852e9e3caf39f1dcd590b64bb8fae54315f9c2e7dc35b025e4e3
Deleted: sha256:feff1495e6982a7e91edc59b96ea74fd80e03674d92c7ec8a502b417268822ff

```

docker rmi -f $(docker images -aq)   #删除所有镜像

docker rmi –f 容器id1 容器id2 	#删除多个容器

可以通过IMAGE ID删除镜像（用前几位能区分镜像id的即可）



## 容器命令

**有了镜像才可以创建容器**    相当于类与实例的关系

这里下载一个centos镜像进行测试

```
docker pull centos
```

### docker run [可选参数] image  新建容器并启动

```shell
docker run [可选参数] image
#参数说明
--name="Name" 		#容器名字 tomcat01 tomcat02 用来区分容器
-d					#后台方式运行
-it					#使用交互方式运行，进入容器查看内容
-p					#指定容器的端口 -p 8080:8080
#-p 主机端口：容器端口
#-p ip：主机端口：容器端口
-P					#随机指定端口
```

```shell
[root@lzx ~]# docker run -it centos /bin/bash
#现在的主机名就是容器id，查看容器内的centos，基础版本，很多命令都不完善
[root@c3c440e3da2a /]# ls
bin  etc   lib	  lost+found  mnt  proc  run   srv  tmp  var
dev  home  lib64  media       opt  root  sbin  sys  usr
#退出容器使用exit命令
```

### 新建容器并在后台运行 docker run -d 镜像名

例：docker run -d centos

问题出现：docker ps，发现centos停止了

常见的坑：docker容器在后台运行，必须要有一个进程对其进行调用，如果容器没有被调用就会被自动停止

### 退出容器

#### exit 停止并退出

#### ctrl + P + Q 退出但是容器还在运行

### docker ps    #列出当前正在运行的容器

```shell
docker ps    #列出当前正在运行的容器
docker ps -a	 #列出历史运行过的容器
docker ps -q	#只显示容器的编号
[root@lzx ~]# docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED          STATUS                       PORTS     NAMES
c3c440e3da2a   centos        "/bin/bash"   51 minutes ago   Exited (127) 5 minutes ago             hungry_hopper
3f11adcbe684   hello-world   "/hello"      6 days ago       Exited (0) 6 days ago                  interesting_khayyam
17484a94e33a   hello-world   "/hello"      6 days ago       Exited (0) 6 days ago                  crazy_wilbur

```

### docker rm 删除容器

docker rm 容器id										#删除指定容器，不能删除正在运行的容器，如果要强制删除rm -f

docker rm -f $(docker ps -aq)

启动和停止容器的操作



### 启动和停止容器的操作

docker start 容器id 		#启动容器

docker restart 容器id 	#重启容器

docker stop 容器id 		#停止当前正在运行的容器

docker kill 容器id			#强制停止当前容器



## 常用其他命令

### docker logs查看日志

docker logs —details 容器名 			#查看容器的日志



### docker top 容器ID 查看容器中的进程信息

### docker inspect 容器ID 查看容器的详细信息

```shell
[root@lzx ~]# docker inspect c3c
[
    {
        "Id": "c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7",
        "Created": "2022-05-19T07:16:23.351826358Z",
        "Path": "/bin/bash",
        "Args": [],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 16159,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2022-05-19T12:12:43.570839246Z",
            "FinishedAt": "2022-05-19T08:02:50.745788294Z"
        },
        "Image": "sha256:5d0da3dc976460b72c77d94c8a1ad043720b0416bfc16c52c45d4847e53fadb6",
        "ResolvConfPath": "/var/lib/docker/containers/c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7/hostname",
        "HostsPath": "/var/lib/docker/containers/c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7/hosts",
        "LogPath": "/var/lib/docker/containers/c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7/c3c440e3da2a8834fafb33259084c9232b9a8ed90b309527588e9eac236ac6d7-json.log",
        "Name": "/hungry_hopper",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "default",
            "PortBindings": {},
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "host",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "ConsoleSize": [
                0,
                0
            ],
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": null,
            "BlkioDeviceWriteBps": null,
            "BlkioDeviceReadIOps": null,
            "BlkioDeviceWriteIOps": null,
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "KernelMemory": 0,
            "KernelMemoryTCP": 0,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": false,
            "PidsLimit": null,
            "Ulimits": null,
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/24ad42d088f04c3829a7e62069b525e7b55bf0f9035efad050e2ee2b432a97b9-init/diff:/var/lib/docker/overlay2/07bc71c6dccc5b1a19eaa81f4b2adca4b9a4a1e3c9b16fd45325952b79c756d8/diff",
                "MergedDir": "/var/lib/docker/overlay2/24ad42d088f04c3829a7e62069b525e7b55bf0f9035efad050e2ee2b432a97b9/merged",
                "UpperDir": "/var/lib/docker/overlay2/24ad42d088f04c3829a7e62069b525e7b55bf0f9035efad050e2ee2b432a97b9/diff",
                "WorkDir": "/var/lib/docker/overlay2/24ad42d088f04c3829a7e62069b525e7b55bf0f9035efad050e2ee2b432a97b9/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "c3c440e3da2a",
            "Domainname": "",
            "User": "",
            "AttachStdin": true,
            "AttachStdout": true,
            "AttachStderr": true,
            "Tty": true,
            "OpenStdin": true,
            "StdinOnce": true,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "centos",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.label-schema.build-date": "20210915",
                "org.label-schema.license": "GPLv2",
                "org.label-schema.name": "CentOS Base Image",
                "org.label-schema.schema-version": "1.0",
                "org.label-schema.vendor": "CentOS"
            }
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "f5b0a861c60f17df8ed5a6827e92caa9cd54f09b374e5322922b26204053df2e",
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "Ports": {},
            "SandboxKey": "/var/run/docker/netns/f5b0a861c60f",
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "05587b8d741d7fca3ff9f4e523cb19678771fbf83c7dbefcaf83c63d1e87ac3c",
            "Gateway": "172.18.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.18.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:12:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "219184462c26bdc598ddc23a449e3798f7c7977563c715fc6b348077e6967b80",
                    "EndpointID": "05587b8d741d7fca3ff9f4e523cb19678771fbf83c7dbefcaf83c63d1e87ac3c",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:02",
                    "DriverOpts": null
                }
            }
        }
    }
]

```



### docker exec -it 容器ID /bin/bash 进入当前正在运行的容器

### docker exec  进入容器后开启一个新的终端，可以在里面操作

### docker attach	进入容器正在执行的终端，不会启动新的进程

### docker cp 从容器内拷贝数据到宿主主机

例子：将id为c3c的容器中home目录下的test.java文件拷贝到宿主主机/home目录下

```shell
docker cp c3c:/home/test.java /home/
```

![docker](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/docker.jpeg)



# 作业练习

## 作业1 安装Nginx

docker search nginx 搜索镜像

docker pull nginx 下载镜像

docker images 查看镜像是否下载成功

创建容器并将其命名为nginx01同时将容器的80端口映射到宿主主机的3344端口

docker run -d  --name nginx01 -p 3344:80 nginx

测试本机的3344端口

```shell
[root@lzx home]# curl localhost:3344
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

```



## 作业2 安装tomcat

```shell
# 官方的使用
$ docker run -it --rm tomcat:9.0
# 官方的这种办法可以用来测试，用完即删


#所以先docker pull 下载
docker pull tomcat:9.0
#启动运行
docker run -d -p 3355:8080 --name tomcat01 tomcat
# 测试访问没有问题，但是tomcat是阉割版，所以运行时和平时在window下运行不太一样
#原因：1、linux命令少了 2、没有webapps。阿里云镜像的原因。默认时最小的镜像，所以剔除了不必要的部分
#可以进到容器看一下里面的目录
docker exec -it tomcat01 bash
```

思考问题：之后部署项目，每次进入容器十分麻烦，可以在容器外部提供一个映射路径，webapps，在外面放置项目，自动同步到内部。



## 作业3  部署ES+Kibana



# 可视化

- portainer 先用这个

```shell
# docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
```

访问：47.100.197.170:8088

![image-20220530153855016](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220530153855016.png)

可视化面板平时不用

- Rancher CI/CD再用



# Docker镜像讲解

## 镜像是什么

镜像时一种轻量级、可执行的独立软件包，用来打包软件运行环境和基于运行环境开发的软件，包含运行某个软件所需的所有内容，包括代码、运行时库、环境变量和配置文件。

## 镜像的来源

- 从远程仓库下载
- 拷贝他人镜像
- 自己制作一个镜像DockerFile

## Docker镜像加载原理

**UnionFS（联合文件系统）**

下载的时候看到的一层层结构就是这个

UnionFS（联合文件系统）：Union文件系统是一种分层、轻量级并且高性能的文件系统，支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下。联合文件系统是Docker镜像的基础。镜像可以进行继承，基于基础镜像可以制作各种具体的应用镜像。

特性：一次同时加载多个文件系统，但从外面看起来，只能看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终稿的文件系统会包含所有底层的文件和目录。

**Docker镜像加载原理**

docker镜像实际上是由一层层的文件系统组成，这种层级的文件系统就是UnionFS

bootfs（boot file system）主要包含bootloader和kernel，bootloader主要是引导加载kernel，Linux刚启动时会加载bootfs文件系统，在Docker镜像的最底层是bootfs。这和典型的Linux/Unix系统是一样的，包含boot加载器和内核。当boot加载完成后整个内核都在内存中了，此时内存的使用权已由bootfs转交给内核，此时系统也会卸载bootfs。

rootfs(root file system)在bootfs之上。包含的就是典型Linux系统中的/dev，/bin，/etc 等标准目录和文件。rootfs就是各种不同的操作系统的发行版，比如redhat、centos、ubuntu等

![image-20220611195352352](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220611195352352.png)

对于一个精简的os，rootfs可以很小，只需要包含最基本的命令，工具和程序库就可以了，因为底层直接使用Host和kernel，自己只需要提供rootfs就可以了。所以说对于不同的linux发行版，bootfs基本是一致的，rootfs会有差别。

## 分层理解

Docker 镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部！

这一层就是我们常说的容器层，容器之下都叫镜像层！



理解：

所有的Docker镜像都起始于一个基础镜像层，当进行修改或者增加新的内容的时候，就会在当前的镜像层之上，创建新的镜像层。

举一个简单的例子，假如基于Ubuntu Linux 16.04创建一个新的镜像，这就是新镜像的第一层；如果在该镜像中添加Python包，那么就会在基础镜像层之上创建第二个镜像层；如果继续添加一个安全补丁，就会创建第三个镜像层。

当前的镜像包含三个镜像层，如下图所示。

![image-20220611204214378](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220611204214378.png)

在添加额外的镜像层的同时，镜像始终保持是当前所有镜像的组合，下图举了一个简单的例子，每个镜像层包含3个文件，而镜像包含了来自两层镜像层的6个文件。

![image-20220611204503430](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220611204503430.png)

下图展示了稍微复杂的三层镜像，外部看来只有6个文件，这是因为最上层中的文件7是文件5的一个更新版本。

![image-20220611205316532](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220611205316532.png)

这种情况下，上层镜像层中的文件覆盖了底层镜像层中的文件，这就使得文件的更新版本作为一个新镜像层添加到镜像中。

Docker通过存储引擎（新版本采用快照机制）的方式来实现镜像层对战，并保证多镜像层对外展示为统一的文件系统。

下面展示了和上面系统显示相同的三层镜像。所有镜像层堆叠并合并，对外提供统一的视图。

![image-20220611210625694](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220611210625694.png)

Docker镜像都是只读的，当容器启动时，一个新的可写层会被加载到镜像的顶部。

这一层就是我们常说的容器层，容器层之下的都叫镜像层。



如何提交自己的一个镜像？

## commit镜像

```shell
docker commit 提交容器成为一个新的副本

docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名：[Tag]
```

实战测试

```shell
# 启动一个默认的tomcat
docker run  -t -p 8080:8080 tomcat:latest
docker exec -it 192 bash #192是容器id的前三位
# 发现这个默认的tomcat是没有webapps应用的，镜像的原因，官方的镜像默认webapps下是没有文件的！
root@1925ed5e03f0:/usr/local/tomcat# cd webapps
root@1925ed5e03f0:/usr/local/tomcat/webapps# ls
# 自己向里面拷贝了一些基本文件
root@1925ed5e03f0:/usr/local/tomcat# cp -r webapps.dist/* webapps
root@1925ed5e03f0:/usr/local/tomcat# cd webapps
root@1925ed5e03f0:/usr/local/tomcat/webapps# ls
ROOT  docs  examples  host-manager  manager
root@1925ed5e03f0:/usr/local/tomcat/webapps# exit
exit
[root@lzx ~]# docker ps
CONTAINER ID   IMAGE           COMMAND                  CREATED          STATUS          PORTS                                       NAMES
1925ed5e03f0   tomcat:latest   "catalina.sh run"        11 minutes ago   Up 11 minutes   0.0.0.0:8080->8080/tcp, :::8080->8080/tcp   charming_montalcini
dd67f96682db   nginx           "/docker-entrypoint.…"   3 weeks ago      Up 3 weeks      0.0.0.0:3344->80/tcp, :::3344->80/tcp       nginx01
c3c440e3da2a   centos          "/bin/bash"              3 weeks ago      Up 3 weeks
# 复制文件夹时记得添加参数 -r 递归的进行复制


#生成新的镜像，192是容器的id前三位，tomcat02:1.0是我们生成的新的镜像的名字
[root@lzx ~]# docker commit -a="lizongxi" -m="add webapps app" 192 tomcat02:1.0
sha256:57090981172c66ec2109eaf0fb7c2cca28fd1bbd7e74e3df92337824d47036a6
[root@lzx ~]# docker images
REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
tomcat02              1.0       57090981172c   8 seconds ago   684MB
nginx                 latest    605c77e624dd   5 months ago    141MB
tomcat                9.0       b8e65a4d736d   5 months ago    680MB
tomcat                latest    fb5657adc892   5 months ago    680MB
hello-world           latest    feb5d9fea6a5   8 months ago    13.3kB
centos                latest    5d0da3dc9764   8 months ago    231MB
portainer/portainer   latest    580c0e4e98b0   14 months ago   79.1MB

```

如果想保存当前容器的状态，可以使用commit来提交，获得一个镜像，就像之前学习虚拟机的时候，快照！

# 容器数据卷

## docker理念回顾

将应用和环境打包成一个镜像

数据？如果数据都在容器中，那么如果把容器删除，那么数据就会丢失！ ==需求：数据可以持久化==

容器之间可以有一个数据共享的技术！Docker容器中产生的数据，可以同步到本地！

卷技术：目录的挂载，可以把容器内的目录，挂载到Linux上面

![image-20220612105033880](C:\Users\lixueting\AppData\Roaming\Typora\typora-user-images\image-20220612105033880.png)

总结一句话：容器的持久化和同步操作！容器间是可以进行数据共享的！

## 使用数据卷

方式一：直接使用命令来挂载

```shell
docker run -it -v 主机目录 容器内端口
[root@lzx home]# docker run -it -v /home/ceshi:/home centos bash

#启动起来之后可以在宿主机（也就是服务器中）通过docker inspect 容器id查看是否挂载成功
[root@lzx home]#  docker inspect 2e7

```

![image-20220612112652563](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220612112652563.png)

容器和宿主使用同一份数据，就是文件的挂载

![image-20220612141000756](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220612141000756.png)



## 实战：安装MySQL

思考：MySQL的数据持久化的问题！

```shell
# 获取镜像
[root@lzx ceshi]# docker pull mysql:5.7

#运行容器的时候需要进行数据挂载！ 安装启动mysql的时候，需要配置密码
#官方测试 $ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag


#实际上启动MySQL用的命令
-d 后台运行
-p 端口映射
-v 数据卷挂载
-e 环境配置
--name 容器名字
[root@lzx home]# docker run -d -p 3310:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
4f6607a931208ee7445cf28aa4174eff57a316194f36d89b8969247a130a9efa

# 启动成功之后，本地使用navicat来测试一下
#navicat连接到服务器的3310端口 3310端口和容器内的3306端口进行映射
# 在本地测试创建一个数据库，查看我们的映射路径是否ok
```

假设将容器删除，会发现容器挂载到服务器的数据卷依旧没有消失

## 具名挂载和匿名挂载

```shell
# 匿名挂载
-v 容器内的路径
# 例子
docker run -d -P --name nginx02 -v /etc/nginx nginx
#查看所有volume的情况
[root@lzx conf]# docker volume ls
DRIVER    VOLUME NAME
local     0029415bd06cc04bd38be231738bfb87f1b3605428980230e4add0403ff692ee
local     f48058e532f70a12d1b6626e1c1009952110ff299090bbaa48be9f66751b25d6
# 这种就是匿名挂载，在-v的时候只写了容器内的路径，没有写容器外的路径

# 具名挂载
[root@lzx conf]# docker run -d -P --name nginx03 -v have-name:/etc/nginx nginx
0f81c2ed9cc4b0f7c5a2b2cc2b0d3edf89006ec082bdeef408e778492ea7e812
[root@lzx conf]# docker volume ls
DRIVER    VOLUME NAME
local     have-name
# 使用docker inspect 查看一下这个卷的具体信息
```

![image-20220612162637276](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220612162637276.png)

所有docker容器中的卷，没有指定目录的情况下都是在 /var/lib/docker/volumes/xxx   里面

![image-20220612162922053](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220612162922053.png)

通过具名挂载可以很方便的找到我们的一个卷，所以在大多数情况下使用的是具名挂载

```shell
# 几种挂载类型的总结
-v 容器内路径 # 匿名挂载
-v 卷名：容器内路径 # 具名挂载
-v /宿主机路径：容器内路径 #指定路径挂载
```

拓展：

```shell
# 通过 -v 容器内的路径:ro rw 改变读写权限
ro read only #只读
rw read write #可读可写
[root@lzx conf]# docker run -d -P --name nginx03 -v have-name:/etc/nginx:ro nginx

```



# 初始DockerFile

Dockerfile是用来构建docker镜像的构建文件！脚本命令！

通过这个脚本可以用来生成镜像，镜像是一层一层的，脚本就是一个个的命令，每个命令就是一层。

```shell
# 进行简单的测试
1 [root@lzx home]# mkdir docker-test-volume
# 创建了一个名为dockerfile1的文件，文件内容如下
2 [root@lzx docker-test-volume]# vim dockerfile1
FROM centos

VOLUME ["volume01","volume02"]

CMD echo "---end---"
CMD bash
3 [root@lzx docker-test-volume]# docker build -f dockerfile1 -t lizongxi/centos .

注意：镜像名前面不能有/   也就/lizongxi/centos

```

![image-20220613204133337](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220613204133337.png)

从图中可以发现每个命令就是镜像的一层



同时发现已经生成了我们自己的镜像

![image-20220613210157935](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220613210157935.png)

启动生成的镜像

```shell
docker run -it 6fe
```

查看镜像的目录，发现了我们挂载的目录volume01 和 volume02

![image-20220613210304952](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220613210304952.png)



而外部（宿主主机）一定有与volume01和volume02同步的目录，上面是一个匿名挂载。

在宿主主机查看当前容器的详细信息！！！注意不是在容器中

![image-20220613221254375](https://raw.githubusercontent.com/lizongxixi/cloudImg/main/img/image-20220613221254375.png)

发现了挂载在宿主主机的目录

未来大多使用这种方式，因为我们会构建自己的镜像

如果在构建镜像的时候没有挂载卷，那么需要手动挂载使用 -v 卷名：容器内的路径

# DockerFile

dockerfile 是用来构建docker镜像的文件！命令参数脚本！

构建步骤：

1 编写一个dockerfile 文件

2 docker build 构建成为一个镜像

3 docker run 运行镜像

4 docker push 发布镜像（DockerHub、阿里云镜像仓库）



# Docker网络
