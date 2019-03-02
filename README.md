# yum docker cookbook
docker烹饪书、docker命令详解

## 查看版本
`docker version`命令可以检查docker版本:
``` zsh
shiming@pro ➜  ~ docker version
Client: Docker Engine - Community
 Version:           18.09.2
 API version:       1.39
 Go version:        go1.10.8
 Git commit:        6247962
 Built:             Sun Feb 10 04:12:39 2019
 OS/Arch:           darwin/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          18.09.2
  API version:      1.39 (minimum version 1.12)
  Go version:       go1.10.6
  Git commit:       6247962
  Built:            Sun Feb 10 04:13:06 2019
  OS/Arch:          linux/amd64
  Experimental:     true
```
一般server和client版本要一致，检查版本以保证cli和engine可以通讯

## 查看参数
`docker info`可以查看大部分的docker engine参数
``` zsh
shiming@pro ➜  ~ docker info
Containers: 1
 Running: 0
 Paused: 0
 Stopped: 1
Images: 1
Server Version: 18.09.2
Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
Logging Driver: json-file
...
```

## 查看帮助
`docker`或`docker --help`可以查看docker帮助  
``` zsh
shiming@pro ➜  ~ docker --help

Usage:	docker [OPTIONS] COMMAND

A self-sufficient runtime for containers

Options:
      --config string      Location of client config files (default "/Users/shiming/.docker")
  -D, --debug              Enable debug mode
  -H, --host list          Daemon socket(s) to connect to
  -l, --log-level string   Set the logging level ("debug"|"info"|"warn"|"error"|"fatal") (default "info")
...
```

## 查看特定命令帮助
`docker <command> --help`
``` zsh
shiming@pro ➜  ~ docker exec --help

Usage:	docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Run a command in a running container

Options:
  -d, --detach               Detached mode: run command in the background
      --detach-keys string   Override the key sequence for detaching a container
  -e, --env list             Set environment variables
  -i, --interactive          Keep STDIN open even if not attached
      --privileged           Give extended privileges to the command
  -t, --tty                  Allocate a pseudo-TTY
  -u, --user string          Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir string       Working directory inside the container
```

## 命令格式
最初docker命令格式为：  
`docker <command> (options)`  

因为命令太多，所以有management command格式：  
`docker <command> <sub-command> (option)`  

两种命令格式等价

## hello docker
在docker中运行第一个容器，看看效果：  
`docker container run --publish 80:80 nginx` 
这条命令所做的事情：
1. 从docker hub上下载nginx
2. 在一个新容器上运行nginx
3. 在本机开启80端口
4. 将80端口的请求转发到容器80端口上
5. 如果本机端口被占用则会报错，可以改一个端口如8000:80，本机通过localhost:8000访问

``` zsh
shiming@pro ➜  ~ docker container run --publish 80:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
6ae821421a7d: Pull complete
58702d4af197: Pull complete
b165f42e8fd4: Pull complete
Digest: sha256:20bc75f3622d55ede8cd8fbbc808a4495c7a6f629293d96676f25eee709de5a7
Status: Downloaded newer image for nginx:latest
```

打开浏览器，访问`localhost`，boom!  
![](https://github.com/huahuayu/img/blob/master/20190302070414.png?raw=true)

刷新网页，再返回terminal界面查看，会打印请求日志出来
``` zsh
172.17.0.1 - - [01/Mar/2019:23:48:16 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
172.17.0.1 - - [01/Mar/2019:23:48:21 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
```

目前是交互式运行的，所以`ctrl + c`可以终止运行  
如果要放在后台运行可以执行`docker container run --publish 80:80 --detach nginx`
``` zsh
shiming@pro ➜  ~ docker container run --publish 80:80 --detach nginx
b46fee7c3a8434d1ba6e7bb9b1e5f3161188319a981f117c99cf6698eb1db29b
```
返回的是唯一的container id

## 查看运行的容器列表
`docker container ls` 或 `docker ps`
``` zsh
shiming@pro ➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
b46fee7c3a84        nginx               "nginx -g 'daemon of…"   3 minutes ago       Up 3 minutes        0.0.0.0:80->80/tcp   practical_cray
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
b46fee7c3a84        nginx               "nginx -g 'daemon of…"   6 minutes ago       Up 6 minutes        0.0.0.0:80->80/tcp   practical_cray
```

`docker container ls -a` 可以查看所有的容器（包括已经停止的）  
有趣的一点：可以注意到NAMES这列，是系统生成的，下划线前面是一些开源项目名，下划线后面是一些黑客的名字
``` zsh
shiming@pro ➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                                              NAMES
ae5aba478456        nginx               "nginx -g 'daemon of…"   10 seconds ago      Up 9 seconds                   0.0.0.0:80->80/tcp                                 zen_bassi
b46fee7c3a84        nginx               "nginx -g 'daemon of…"   About an hour ago   Exited (0) 43 minutes ago                                                         practical_cray
79c83674b996        nginx               "nginx -g 'daemon of…"   About an hour ago   Exited (0) About an hour ago                                                      wonderful_lamport
328fe8f35e0d        eosio/eos:v1.4.2    "/bin/bash -c 'keosd…"   3 months ago        Exited (255) 2 months ago      127.0.0.1:5555->5555/tcp, 0.0.0.0:7777->7777/tcp   eosio
```

## 停止容器运行
`docker container stop <container>`
只需要敲几个首字母能区分container即可，stop后再用`docker container ls`来看，进程已经没有了
``` zsh
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
b46fee7c3a84        nginx               "nginx -g 'daemon of…"   6 minutes ago       Up 6 minutes        0.0.0.0:80->80/tcp   practical_cray
shiming@pro ➜  ~ docker container stop b46
b46
shiming@pro ➜  ~ docker container ls
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

## 指定容器名字
容器的名字不指定的时候是自动生成的，但是可以用`--name`指定  
``` zsh
shiming@pro ➜  ~ docker container run --publish 80:80 --detach --name webhost nginx
ce47f01488e85bad69357c1c3a26bb05bdbad8a63ac4ae314c3b4892c3792368
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
ce47f01488e8        nginx               "nginx -g 'daemon of…"   10 seconds ago      Up 10 seconds       0.0.0.0:80->80/tcp   webhost
```

## 查看日志
使用 `--detach`后台运行的容器，如何查看日志呢？可以使用`docker container logs <container_name>` 或者 `docker container logs <container_id>`  

简写`docker logs <container>`效果一样
``` zsh
shiming@pro ➜  ~ docker container logs webhost
172.17.0.1 - - [28/Feb/2019:10:26:26 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
172.17.0.1 - - [28/Feb/2019:10:26:31 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
shiming@pro ➜  ~
shiming@pro ➜  ~ docker container logs ce47
172.17.0.1 - - [28/Feb/2019:10:26:26 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
172.17.0.1 - - [28/Feb/2019:10:26:31 +0000] "GET / HTTP/1.1" 304 0 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.119 Safari/537.36" "-"
```

## 删除容器
`docker container rm <container1> <container2>...` 或者 `docker rm` rm只能删除已经停止运行的容器，加上option `-f`可以强行删除
``` zsh
shiming@pro ➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                                              NAMES
ce47f01488e8        nginx               "nginx -g 'daemon of…"   13 minutes ago      Up 13 minutes                  0.0.0.0:80->80/tcp                                 webhost
ae5aba478456        nginx               "nginx -g 'daemon of…"   About an hour ago   Exited (0) About an hour ago                                                      zen_bassi
b46fee7c3a84        nginx               "nginx -g 'daemon of…"   2 hours ago         Exited (0) 2 hours ago                                                            practical_cray
79c83674b996        nginx               "nginx -g 'daemon of…"   3 hours ago         Exited (0) 2 hours ago                                                            wonderful_lamport
328fe8f35e0d        eosio/eos:v1.4.2    "/bin/bash -c 'keosd…"   3 months ago        Exited (255) 2 months ago      127.0.0.1:5555->5555/tcp, 0.0.0.0:7777->7777/tcp   eosio
shiming@pro ➜  ~ docker container rm ae5 b46 79c 328 ce4
ae5
b46
79c
328
Error response from daemon: You cannot remove a running container ce47f01488e85bad69357c1c3a26bb05bdbad8a63ac4ae314c3b4892c3792368. Stop the container before attempting removal or force remove
shiming@pro ➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
ce47f01488e8        nginx               "nginx -g 'daemon of…"   14 minutes ago      Up 14 minutes       0.0.0.0:80->80/tcp   webhost
shiming@pro ➜  ~ docker container rm -f ce4
ce4
```
## docker container run命令背后
1. 在本地image缓存中搜索这个image，如果找不到
2. 在远程image库寻找（默认是Docker Hub）
3. 默认下载最新版image(nginx:latest), 但是如果指定了版本则下载指定版本
4. 基于image创建一个新容器，并准备启动
5. 在docker engine中为容器分配一个虚拟内网ip
6. 在本机开启80端口并转发本机80端口的请求到容器
7. 使用image中Dockerfile的命令启动container 

`docker container run`命令可以指定不少参数：    
![](https://github.com/huahuayu/img/blob/master/20190302072704.png?raw=true)

## 查看容器中的进程
`docker top <container>`可以查看容器中的进程
``` zsh
shiming@pro ➜  ~ docker run --name mongo -d mongo
Unable to find image 'mongo:latest' locally
latest: Pulling from library/mongo
7b722c1070cd: Pull complete
5fbf74db61f1: Pull complete
ed41cb72e5c9: Pull complete
7ea47a67709e: Pull complete
778aebe6fb26: Pull complete
3b4b1e0b80ed: Pull complete
844ccc42fe76: Pull complete
eab01fe8ebf8: Pull complete
e5758d5381b1: Pull complete
dc553720c5c3: Pull complete
67750c781aa2: Pull complete
b00b8942c827: Pull complete
32201bb8ca69: Pull complete
Digest: sha256:002fda672a0d196325a30736d4c80d04adf6f39dd28db41e6799f42844cab7b8
Status: Downloaded newer image for mongo:latest
918a8cf31d1f9f0343d24c8c42119dca9c2db8b1b182791cffcb45286e9c321d
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
918a8cf31d1f        mongo               "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes        27017/tcp           mongo
shiming@pro ➜  ~ docker top mongo
PID                 USER                TIME                COMMAND
3600                999                 0:01                mongod --bind_ip_all
```

## 容器和虚拟机的区别
容器中的进程实际上是运行在本机的，他只是宿主机上的一个进程，只需要最小化的资源运行，进展一停止，容器就停止了  
![](https://raw.githubusercontent.com/huahuayu/img/master/2019-03-02_07-27-40.png?token=ABpShOoWxL4Y8hpQteqkcZquumepmozOks5cecBcwA%3D%3D)

虚拟机虚拟出了一个操作系统，消耗更多资源  
![](https://github.com/huahuayu/img/blob/master/20190302072856.png?raw=true)


## docker create/start/run区别
`Create` adds a writeable container on top of your image and sets it up for running whatever command you specified in your CMD. The container ID is reported back but it’s not started.

`Start` will start any stopped containers. This includes freshly created containers.

`Run` is a combination of create and start. It creates the container and starts it.

## 查看容器元数据
`docker container inspect <container>`或者`docker inspect <container>`可以查看**单个容器**的元数据（metadata），包括容器信息，配置、网络、磁盘等  
``` zsh
shiming@pro ➜  ~ docker container inspect mongo
[
    {
        "Id": "918a8cf31d1f9f0343d24c8c42119dca9c2db8b1b182791cffcb45286e9c321d",
        "Created": "2019-02-28T12:10:25.3036055Z",
        "Path": "docker-entrypoint.sh",
        "Args": [
            "mongod"
        ],
.....  # 下面还有非常多字段
```


## 设置环境变量
`-e`可以在容器运行时指定容器的环境变量，如以下命令即指定环境变量`MYSQL_RANDOM_ROOT_PASSWORD`为true：  
```
docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true -p 3307:3306 mysql
```

如此指定之后mysql容器运行后，可以用`docker container logs mysql`查看生成的随机root密码  
![](https://github.com/huahuayu/img/blob/master/20190302073127.png?raw=true)

## 查看所有容器运行状态
`docker container stats`或`docker stats`可以查看所有容器实时运行情况  
``` zsh
shiming@pro ➜  ~ docker stats
CONTAINER ID        NAME                CPU %               MEM USAGE / LIMIT     MEM %               NET I/O             BLOCK I/O           PIDS
cbecc2967e2e        webhost             0.00%               1.996MiB / 1.952GiB   0.10%               688B / 0B           647kB / 0B          2
```


## 运行容器的同时启动bash
`-it`参数代表交互式运行容器，跟在nginx后的`bash`代表运行在容器中的bash shell
`docker run -it --name proxy nginx bash`
``` zsh
shiming@pro ➜  ~ docker run -it --name proxy nginx bash
root@22b66467708d:/# ls -al
total 72
drwxr-xr-x   1 root root 4096 Feb 28 13:40 .
drwxr-xr-x   1 root root 4096 Feb 28 13:40 ..
-rwxr-xr-x   1 root root    0 Feb 28 13:40 .dockerenv
drwxr-xr-x   2 root root 4096 Feb  4 00:00 bin
drwxr-xr-x   2 root root 4096 Jan 22 13:47 boot
drwxr-xr-x   5 root root  360 Feb 28 13:40 dev
drwxr-xr-x   1 root root 4096 Feb 28 13:40 etc
drwxr-xr-x   2 root root 4096 Jan 22 13:47 home
drwxr-xr-x   1 root root 4096 Feb  4 00:00 lib
drwxr-xr-x   2 root root 4096 Feb  4 00:00 lib64
drwxr-xr-x   2 root root 4096 Feb  4 00:00 media
drwxr-xr-x   2 root root 4096 Feb  4 00:00 mnt
drwxr-xr-x   2 root root 4096 Feb  4 00:00 opt
dr-xr-xr-x 220 root root    0 Feb 28 13:40 proc
drwx------   2 root root 4096 Feb  4 00:00 root
drwxr-xr-x   3 root root 4096 Feb  4 00:00 run
drwxr-xr-x   2 root root 4096 Feb  4 00:00 sbin
drwxr-xr-x   2 root root 4096 Feb  4 00:00 srv
dr-xr-xr-x  13 root root    0 Feb 28 13:40 sys
drwxrwxrwt   1 root root 4096 Feb 27 23:23 tmp
drwxr-xr-x   1 root root 4096 Feb  4 00:00 usr
drwxr-xr-x   1 root root 4096 Feb  4 00:00 var
```

## 在容器中运行ubuntu
在容器中运行的linux发行版通常都非常精简，有需要用到的软件可以自己用`apt-get`安装  
用`docker container run -it --name ubuntu ubuntu` 创建并start容器，进入交互模式
exit退出交互bash时容器也停止了  
可以用`docker container start -ai ubuntu` start容器并重新进入交互模式  
``` zsh
shiming@pro ➜  ~ docker container run -it --name ubuntu ubuntu
Unable to find image 'ubuntu:latest' locally
latest: Pulling from library/ubuntu
6cf436f81810: Pull complete
987088a85b96: Pull complete
b4624b3efe06: Pull complete
d42beb8ded59: Pull complete
Digest: sha256:7a47ccc3bbe8a451b500d2b53104868b46d60ee8f5b35a24b41a86077c650210
Status: Downloaded newer image for ubuntu:latest
root@384472f0ecc1:/# exit      # 退出ubuntu的bash后容器就停止了
exit
shiming@pro ➜  ~ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                         PORTS                NAMES
384472f0ecc1        ubuntu              "/bin/bash"              2 minutes ago       Exited (0) 8 seconds ago                            ubuntu  # 容器停止了
cbecc2967e2e        nginx               "nginx -g 'daemon of…"   About an hour ago   Up About an hour               0.0.0.0:80->80/tcp   webhost
ba243bdd6c41        mysql               "docker-entrypoint.s…"   2 hours ago         Exited (0) About an hour ago                        mysql
918a8cf31d1f        mongo               "docker-entrypoint.s…"   2 hours ago         Exited (0) About an hour ago                        mongo
shiming@pro ➜  ~ docker container start --help

Usage:	docker container start [OPTIONS] CONTAINER [CONTAINER...]

Start one or more stopped containers

Options:
  -a, --attach                  Attach STDOUT/STDERR and forward signals
      --checkpoint string       Restore from this checkpoint
      --checkpoint-dir string   Use a custom checkpoint storage directory
      --detach-keys string      Override the key sequence for detaching a container
  -i, --interactive             Attach container's STDIN
shiming@pro ➜  ~ docker container start -ai ubuntu   # 用交互模式start容器
root@384472f0ecc1:/#
```

## 容器启动后执行命令
`docker exec`的作用是Run a command in a running container即在运行中的容器中执行命令，`docker exec -it <container> <command>`即可以交互式执行command，比如bash：  
``` zsh
shiming@pro ➜  ~ docker exec -it ubuntu bash
root@384472f0ecc1:/#
```

## 将image拉到本地
`docker pull <image>`可以将image拉到本地
``` zsh
shiming@pro ➜  ~ docker pull --help

Usage:	docker pull [OPTIONS] NAME[:TAG|@DIGEST]

Pull an image or a repository from a registry

Options:
  -a, --all-tags                Download all tagged images in the repository
      --disable-content-trust   Skip image verification (default true)
      --platform string         Set platform if server is multi-platform capable
```

alpine是一个非常精简的linux发行版，它的docker image只有5M大小，把它拉到本地看下  
``` zsh
shiming@pro ➜  ~ docker pull alpine
Using default tag: latest
latest: Pulling from library/alpine
6c40cc604d8e: Pull complete
Digest: sha256:b3dbf31b77fd99d9c08f780ce6f5282aba076d70a513a8be859d8d3a4d0c92b8
Status: Downloaded newer image for alpine:latest
```


## 查看image列表
`docker image ls`可以查看image大小
``` zsh
shiming@pro ➜  ~ docker image ls
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
nginx               latest              8c9ca4d17702        15 hours ago        109MB
mysql               latest              81f094a7e4cc        3 weeks ago         477MB
ubuntu              latest              47b19964fb50        3 weeks ago         88.1MB
mongo               latest              0da05d84b1fe        3 weeks ago         394MB
alpine              latest              caf27325b298        4 weeks ago         5.53MB
eosio/eos           v1.4.2              9e4bb39a8e25        4 months ago        250MB
```

## docker中运行alpine
alpine太精简了，连bash都没有，只有`sh`所以不能用`docker container run -it alpine bash`运行  
``` zsh
shiming@pro ➜  ~ docker container run -it alpine sh
/ #
```

alpine的包管理工具是`apk`，可以用`apk add`去安装bash
``` sh
/ # apk add bash
fetch http://dl-cdn.alpinelinux.org/alpine/v3.9/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.9/community/x86_64/APKINDEX.tar.gz
(1/5) Installing ncurses-terminfo-base (6.1_p20190105-r0)
(2/5) Installing ncurses-terminfo (6.1_p20190105-r0)
(3/5) Installing ncurses-libs (6.1_p20190105-r0)
(4/5) Installing readline (7.0.003-r1)
(5/5) Installing bash (4.4.19-r1)
Executing bash-4.4.19-r1.post-install
Executing busybox-1.29.3-r10.trigger
OK: 14 MiB in 19 packages
/ # bash
bash-4.4#
```

## 进入容器的方法总结

* `docker container run -it`   交互式启动容器
* `docker container start -at` 启动已exit的容器
* `docker container exec -it`  在已启动的容器中执行额外的命令

## 


