[//title]:(docker-cookbook)
[//englishTitle]:(docker-cookbook)
[//category]:(docker,tutorial)
[//tags]:(docker)
[//createTime]:(20190301)
[//lastUpdateTime]:(20200312)
## 安装docker
### mac版本
直接去官方下载dmg安装文件 https://hub.docker.com/editions/community/docker-ce-desktop-mac，下载前会要求你注册一个docker.com的账号
### ubuntu版本
ubuntu安装docker[官方指引](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)：  
第一步：将docker添加到apt-get库
``` bash
sudo apt-get update
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

第二步：安装docker ce
``` bash
sudo apt-get update
sudo apt-get install docker-ce
```

第三步：验证安装
``` bash
sudo docker run hello-world
```

如果出现以下返回则安装成功，这个输出还简单解释了docker的工作原理：
```
root@MNG-BC ➜  ~ docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
1b930d010525: Pull complete
Digest: sha256:2557e3c07ed1e38f26e389462d03ed943586f744621577a99efb77324b0fe535
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/
```

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

命令中`--publish 80:80`的作用就是将容器的80端口映射到宿主端口80上，简写为`-p`，格式为：主机(宿主)端口:容器端口  

## docker命令自动补全
安装docker后默认是没有docker相关命令行自动补全的，需要自己安装，可以参考[官方说明](https://docs.docker.com/compose/completion/)
以zsh为例，如果使用的是oh-my-zsh配置，则只需要在~/.zshrc开启插件：
``` vim
plugins=(... docker docker-compose
)
```

然后使用`source ~/.zshrc`使配置生效即可

自动补全效果举例：
敲"docker con" + tab键，出现提示
``` zsh
shiming@pro ➜  ~ docker con
config     -- Manage Docker configs
container  -- Manage containers
```

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
容器的名字不指定的时候是自动生成的，但是可以在`docker run`的时候用`--name`指定  
``` zsh
shiming@pro ➜  ~ docker container run --publish 80:80 --detach --name webhost nginx
ce47f01488e85bad69357c1c3a26bb05bdbad8a63ac4ae314c3b4892c3792368
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
ce47f01488e8        nginx               "nginx -g 'daemon of…"   10 seconds ago      Up 10 seconds       0.0.0.0:80->80/tcp   webhost
```

## 给容器重命名
如果`docker run`的时候没有为容器命名，容器会得到一个随机名字，后续可以通过`docker container reanme <old_name> <new_name>`来给容器重命名(简写`docker rename <old_name> <new_name>`)

容器的随机名字为infallible_saha:
``` zsh
shiming@pro ➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                NAMES
b3c2b3a65b95        alpine              "sh"                     2 days ago          Exited (0) 2 days ago                        infallible_saha
```

通过`rename`修改名字为alpine：
``` zsh
shiming@pro ➜  ~ docker container rename infallible_saha alpine
shiming@pro ➜  ~ docker container ls -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                  PORTS                NAMES
b3c2b3a65b95        alpine              "sh"                     2 days ago          Exited (0) 2 days ago                        alpine
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
## docker run命令详解
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
docker container run -d --name mysql -e MYSQL_RANDOM_ROOT_PASSWORD=true -p 3307:3306 mysql   # 3307是主机端口，3306docker端口，将3306映射到主机3307
```

如此指定之后mysql容器运行后，可以用`docker container logs mysql`查看生成的随机root密码  
![](https://github.com/huahuayu/img/blob/master/20190302073127.png?raw=true)  

也可以通过`MYSQL_ROOT_PASSWORD`直接指定mysql root密码  
```
docker container run -d --name mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3307:3306 mysql   # 3307是主机端口，3306docker端口，将3306映射到主机3307
```

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

## 在启动的容器中执行命令
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

建议拉取指定版本，而不是直接拉取latest版，latest是相对的  
```
docker pull sonarqube:6.7.6-community   #冒号后面就是版本号
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

## 查看image history
`docker image history <image>`  可以查看image的创建历史，只有最上层的image layer有image id，底层layer不需要id。  
```
shiming@pro ➜  ~ docker image history nginx
IMAGE               CREATED             CREATED BY                                      SIZE                COMMENT
8c9ca4d17702        3 months ago        /bin/sh -c #(nop)  CMD ["nginx" "-g" "daemon…   0B
<missing>           3 months ago        /bin/sh -c #(nop)  STOPSIGNAL SIGTERM           0B
<missing>           3 months ago        /bin/sh -c #(nop)  EXPOSE 80                    0B
<missing>           3 months ago        /bin/sh -c ln -sf /dev/stdout /var/log/nginx…   22B
<missing>           3 months ago        /bin/sh -c set -x  && apt-get update  && apt…   54MB
<missing>           3 months ago        /bin/sh -c #(nop)  ENV NJS_VERSION=1.15.9.0.…   0B
<missing>           3 months ago        /bin/sh -c #(nop)  ENV NGINX_VERSION=1.15.9-…   0B
<missing>           3 months ago        /bin/sh -c #(nop)  LABEL maintainer=NGINX Do…   0B
<missing>           3 months ago        /bin/sh -c #(nop)  CMD ["bash"]                 0B
<missing>           3 months ago        /bin/sh -c #(nop) ADD file:5a6d066ba71fb0a47…   55.3MB
```
## 查看image详细信息
`docker image inspect <image>` 可以查看image的详细信息(元数据)  

```
shiming@pro ➜  ~ docker image inspect nginx
[
    {
        "Id": "sha256:8c9ca4d17702c354fa41432be278d8ce6c0761b1302608034fa3ad49c6da43f9",
        "RepoTags": [
            "nginx:latest"
        ],
        "RepoDigests": [
            "nginx@sha256:20bc75f3622d55ede8cd8fbbc808a4495c7a6f629293d96676f25eee709de5a7"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2019-02-27T23:24:03.506817648Z",
        "Container": "2110c8a2d4615018cf5d614fde710c869b537340588e312552c92f3364be539d",
        "ContainerConfig": {
            "Hostname": "2110c8a2d461",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.15.9-1~stretch",
                "NJS_VERSION=1.15.9.0.2.8-1~stretch"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"nginx\" \"-g\" \"daemon off;\"]"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:dc31fe1787f6c5a7c474c35abf044f0818b66a952901e4ee3cc19406b370bcf1",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGTERM"
        },
        "DockerVersion": "18.06.1-ce",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.15.9-1~stretch",
                "NJS_VERSION=1.15.9.0.2.8-1~stretch"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "ArgsEscaped": true,
            "Image": "sha256:dc31fe1787f6c5a7c474c35abf044f0818b66a952901e4ee3cc19406b370bcf1",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGTERM"
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 109269900,
        "VirtualSize": 109269900,
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/1ca8abebad83c651db8de36664e0c08bea69a79c50ad2a6b82fd299741d23724/diff:/var/lib/docker/overlay2/56fc92713ecb4834f857835338ced9ad0a239866534b17969e5e03a0d6d17a99/diff",
                "MergedDir": "/var/lib/docker/overlay2/05b6635d8cf9284cb2d453f1cd78addc5f6e37b390bccf0f18a54de958ea3add/merged",
                "UpperDir": "/var/lib/docker/overlay2/05b6635d8cf9284cb2d453f1cd78addc5f6e37b390bccf0f18a54de958ea3add/diff",
                "WorkDir": "/var/lib/docker/overlay2/05b6635d8cf9284cb2d453f1cd78addc5f6e37b390bccf0f18a54de958ea3add/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:0a07e81f5da36e4cd6c89d9bc3af643345e56bb2ed74cc8772e42ec0d393aee3",
                "sha256:55028c39c191b1bbc24b2041f72bf66bbf9d5dccf1cf79be52b303e1bfdf9da7",
                "sha256:0b9e07febf57e72a62e4746216a343af152e5a6c4ec0de0753ba28da01b711a6"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```

## 给image tag重命名
`docker image tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]` 会新建一个image，以前的image不会被删除，是会新建一个image  
```
shiming@pro ➜  ~ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
jboss/jbpm-workbench-showcase     latest              ee46864cbe36        12 days ago         1GB
jboss/jbpm-workbench              latest              84d668d97441        12 days ago         1GB
debian                            latest              8d31923452f8        4 weeks ago         101MB
shiming@pro ➜  ~ docker image ls
REPOSITORY                        TAG                 IMAGE ID            CREATED             SIZE
jboss/jbpm-workbench-showcase     latest              ee46864cbe36        12 days ago         1GB
jboss/jbpm-workbench              latest              84d668d97441        12 days ago         1GB
huahuayu/debian                   latest              8d31923452f8        4 weeks ago         101MB
debian                            latest              8d31923452f8        4 weeks ago         101MB
```

## 登录docker hub
`docker login`可以登录docker hub，登录了docker hub之后可以push image到自己的docker hub，相应的`docker logout`是登出  
```
shiming@pro ➜  ~ docker login
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: huahuayu
Password:
Login Succeeded
shiming@pro ➜  ~ docker logout
Removing login credentials for https://index.docker.io/v1/
```

## docker push
`docker push`和`git push`类似，可以将本地的image push到docker hub上（push前先用`docker login`登录）。push后可以在https://cloud.docker.com/repository/list查看push上来的image。  
```
shiming@pro ➜  ~ docker push huahuayu/alpine
The push refers to repository [docker.io/huahuayu/alpine]
503e53e365f3: Mounted from library/alpine
latest: digest: sha256:25b4d910f4b76a63a3b45d0f69a57c34157500faf6087236581eca221c62d214 size: 528
```

## 在容器中中运行alpine
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

- `docker container run -it`   交互式启动容器
- `docker container start -at` 启动已exit的容器
- `docker exec -it <container> <command>` 在已启动的容器中执行额外的命令

第三条最常用，如`docker exec -it 0b867efe19ae bash`

## 查看容器的启动命令
在容器内部可以用`ps -ef`查看，第1号进程就是    
在容器外部可以用`docker inspect <container>`查看  

## 查看容器端口
怎么查看在运行的容器使用的端口以及宿主主机的映射端口？  
`docker ps`可以查看, PORTS这栏就是端口情况，左边是主机的端口，右边是容器的端口，箭头代表流量转发，tcp是使用的传输协议  
``` zsh
shiming@pro ➜  ~ docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                NAMES
cbecc2967e2e        nginx               "nginx -g 'daemon of…"   2 days ago          Up 14 hours         0.0.0.0:80->80/tcp   webhost
```

另外一种方法是`docker container port <container>`，可以直接查看 
``` zsh
shiming@pro ➜  ~ docker container port webhost
80/tcp -> 0.0.0.0:80
```

## 查看容器的ip地址
上面介绍了如何查看容器的端口，那么如何查看容器的ip地址呢？有两种方法：

第一种：使用`inspect` + `--format`参数来直接获取元数据json的IPAddress字段
``` zsh
shiming@pro ➜  ~ docker container inspect --format '{{.NetworkSettings.IPAddress}}' webhost
172.17.0.2
```

第二种：使用grep查找，查找结果会有些不精确，但是也可以看出来
``` zsh
shiming@pro ➜  ~ docker container inspect webhost | grep IPAddress
            "SecondaryIPAddresses": null,
            "IPAddress": "172.17.0.2",
                    "IPAddress": "172.17.0.2",
```

## 容器网络
在上一节【查看容器的ip地址】可以看到容器的ip地址为172.17.0.2，而宿主的ip可以通过`ifconfig en0`查看，为192.168.199.171，不在一个网段上，因为容器的网络实际上是另一个虚拟网络。

``` zsh
shiming@pro ➜  ~ ifconfig en0
en0: flags=8863<UP,BROADCAST,SMART,RUNNING,SIMPLEX,MULTICAST> mtu 1500
	ether f0:18:98:5e:fc:ae
	inet6 fe80::1481:8049:6101:16aa%en0 prefixlen 64 secured scopeid 0xa
	inet 192.168.199.171 netmask 0xffffff00 broadcast 192.168.199.255
	nd6 options=201<PERFORMNUD,DAD>
	media: autoselect
	status: active
```

为了使容器通过主机相互通信以及与外界进行通信，必须涉及一个网络层。 Docker支持不同类型的网络，每种网络都适合某些场景。

使用同一个虚拟网络的两个容器可以相互通信，无需对外暴露端口（如图），不同虚拟网络的容器要借助宿主主机相互通信。
![](https://raw.githubusercontent.com/huahuayu/img/master/20190303001649.png)

## 查看网络列表
`docker network ls`可以查看容器的网络列表：
``` zsh
shiming@pro ➜  ~ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
dbfad6703d34        bridge              bridge              local
be8589833b5d        host                host                local
2ae02f9d17f7        none                null                local
```
其中第一个name为bridge的是默认的容器网络

其他的容器网络相关命令如下：
``` zsh
shiming@pro ➜  ~ docker network --help

Usage:	docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
```
## 默认网络
在docker安装后，默认的网络`bridge`(docker0)就被创建了，新建的容器默认都位于这个网络中，除非另行指定。除了bridge，另外其他两个网络也被创建了，一个叫`host`，一个叫`none`。

host网络和宿主网络相同，他们之间没有任何隔离，提高了网络性能，但是有一定的安全隐患；none网络中，容器只能访问localhost。



## 查看网络详情
`docker network inspect <network>`可以查看网络详情:
``` zsh
shiming@pro ➜  ~ docker network inspect bridge
[
    {
        "Name": "bridge",
        "Id": "dbfad6703d342033f44ed5bff69b2a39fc1a097b01b837cab2491b2c66077b44",
        "Created": "2019-02-28T07:39:08.181791852Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.17.0.0/16",
                    "Gateway": "172.17.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "918a8cf31d1f9f0343d24c8c42119dca9c2db8b1b182791cffcb45286e9c321d": {
                "Name": "mongo",
                "EndpointID": "3cc3d215ea9cd268d278ac39699919f89b2a8ec3595b592727263ee7eb836e3c",
                "MacAddress": "02:42:ac:11:00:03",
                "IPv4Address": "172.17.0.3/16",
                "IPv6Address": ""
            },
            "b3c2b3a65b95c9b2e89f1f4ca10f4f4cf6e39eecef3e2a839e5c6e6b18faa4d6": {
                "Name": "alpine",
                "EndpointID": "49e3dde817273ece30c45c21df0c1f9d181879f61f5c4efb35c5083c4d1fdf54",
                "MacAddress": "02:42:ac:11:00:04",
                "IPv4Address": "172.17.0.4/16",
                "IPv6Address": ""
            },
            "cbecc2967e2ee730457e09144a16ba5295b55980c6cc2d81c72dbe3f2ae7105e": {
                "Name": "webhost",
                "EndpointID": "4f6154cc1678cbc78f1788f9814ef9b55181ad6712e4aea0492843e93c522bf5",
                "MacAddress": "02:42:ac:11:00:02",
                "IPv4Address": "172.17.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```
在Containers数组中可以看到，mongo、alpine、webhost都运行在这个默认网络上，印证了在[默认网络](#默认网络)中提到的。

## 新建网络
`docker network create <network_name>`可以新建自定义网络
``` zsh
shiming@pro ➜  ~ docker network create my_app_net
e2b8dcbe4827b3e4c011c8e9f5f2298611cbff328948cede94d5cbb7a76729cf
shiming@pro ➜  ~ docker network ls
NETWORK ID          NAME                DRIVER              SCOPE
dbfad6703d34        bridge              bridge              local
be8589833b5d        host                host                local
e2b8dcbe4827        my_app_net          bridge              local
2ae02f9d17f7        none                null                local
```

以上新建了一个网络叫my_app_net，网络驱动（driver）默认为bridge，网络驱动是内置的或者第三方的，能提供虚拟网络服务的程序。

新建网络其实还有很多的选项可以使用
``` zsh
shiming@pro ➜  ~ docker network create --help

Usage:	docker network create [OPTIONS] NETWORK

Create a network

Options:
      --attachable           Enable manual container attachment
      --aux-address map      Auxiliary IPv4 or IPv6 addresses used by Network driver (default map[])
      --config-from string   The network from which copying the configuration
      --config-only          Create a configuration only network
  -d, --driver string        Driver to manage the Network (default "bridge")
      --gateway strings      IPv4 or IPv6 Gateway for the master subnet
      --ingress              Create swarm routing-mesh network
      --internal             Restrict external access to the network
      --ip-range strings     Allocate container ip from a sub-range
      --ipam-driver string   IP Address Management Driver (default "default")
      --ipam-opt map         Set IPAM driver specific options (default map[])
      --ipv6                 Enable IPv6 networking
      --label list           Set metadata on a network
  -o, --opt map              Set driver specific options (default map[])
      --scope string         Control the network's scope
      --subnet strings       Subnet in CIDR format that represents a network segment
```

## 指定容器网络
`docker run --network <network_name> <image_name>`可以指定容器运行在特定的网络上
``` zsh
shiming@pro ➜  ~ docker container run -d --name new_nginx --network my_app_net nginx
1de774242804433c78cadf58083f2432b0f05b49850682d2ce91fc18ce4761b6
```

检查my_app_net网络，Containers栏位中有new_nginx成功运行了
``` zsh
shiming@pro ➜  ~ docker network inspect my_app_net
[
    {
        "Name": "my_app_net",
        "Id": "e2b8dcbe4827b3e4c011c8e9f5f2298611cbff328948cede94d5cbb7a76729cf",
        "Created": "2019-03-03T14:12:14.9603493Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1de774242804433c78cadf58083f2432b0f05b49850682d2ce91fc18ce4761b6": {
                "Name": "new_nginx",
                "EndpointID": "c7925a18b2036885d908f1ce32f9e74165f7ecdc6cfc123379f1cc7863efbefb",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```
## 容器链接网络
`docker network connect <network> <container>`可以将容器链接到指定网络。

输入`docker network connect` 然后按tab键会出现输入建议，按多次tab可以选择（如果不会出现输入建议请参考本文【docker命令自动补全】章节）
``` zsh
shiming@pro ➜  ~ docker network connect e2b8dcbe4827
2ae02f9d17f7  none                              --    null, local
be8589833b5d  host                              --    host, local
e2b8dcbe4827  dbfad6703d34  my_app_net  bridge  --  bridge, local
```

将webhost容器连接上my_app_net
``` zsh
shiming@pro ➜  ~ docker network connect my_app_net webhost
```

查看webhost容器，Networks中有了两个网络了：bridge和my_app_net
``` zsh
shiming@pro ➜  ~ docker container inspect webhost
[
    {
            ...
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "NetworkID": "dbfad6703d342033f44ed5bff69b2a39fc1a097b01b837cab2491b2c66077b44",
                    "EndpointID": "4f6154cc1678cbc78f1788f9814ef9b55181ad6712e4aea0492843e93c522bf5",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null
                },
                "my_app_net": {
                    "IPAMConfig": {},
                    "Links": null,
                    "Aliases": [
                        "cbecc2967e2e"
                    ],
                    "NetworkID": "e2b8dcbe4827b3e4c011c8e9f5f2298611cbff328948cede94d5cbb7a76729cf",
                    "EndpointID": "c0d901d89e15870be9ef28efc50cfa31d1fb8c354410e2f8983dcac919b47904",
                    "Gateway": "172.18.0.1",
                    "IPAddress": "172.18.0.3",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "MacAddress": "02:42:ac:12:00:03",
                    "DriverOpts": null
                }
            }
        }
    }
]
```

相应的，检查my_app_net也可以看到多了一个webhost容器
``` zsh
shiming@pro ➜  ~ docker network inspect my_app_net
[
    {
        "Name": "my_app_net",
        "Id": "e2b8dcbe4827b3e4c011c8e9f5f2298611cbff328948cede94d5cbb7a76729cf",
        "Created": "2019-03-03T14:12:14.9603493Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1de774242804433c78cadf58083f2432b0f05b49850682d2ce91fc18ce4761b6": {
                "Name": "new_nginx",
                "EndpointID": "c7925a18b2036885d908f1ce32f9e74165f7ecdc6cfc123379f1cc7863efbefb",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "cbecc2967e2ee730457e09144a16ba5295b55980c6cc2d81c72dbe3f2ae7105e": {
                "Name": "webhost",
                "EndpointID": "c0d901d89e15870be9ef28efc50cfa31d1fb8c354410e2f8983dcac919b47904",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

## 断开网络
`docker network disconnect <network_name> <container_name>`可以将容器从指定网络断开
``` zsh
shiming@pro ➜  ~ docker network disconnect my_app_net webhost
shiming@pro ➜  ~ docker network inspect my_app_net
[
    {
        "Name": "my_app_net",
        "Id": "e2b8dcbe4827b3e4c011c8e9f5f2298611cbff328948cede94d5cbb7a76729cf",
        "Created": "2019-03-03T14:12:14.9603493Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "1de774242804433c78cadf58083f2432b0f05b49850682d2ce91fc18ce4761b6": {
                "Name": "new_nginx",
                "EndpointID": "c7925a18b2036885d908f1ce32f9e74165f7ecdc6cfc123379f1cc7863efbefb",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

## 网络安全
容器依赖主机进行网络连接，只和主机进行网络交互
容器默认不对外暴露，除非显示的使用`-p`对主机发布
前端程序可以和后端程序放在同一个网络中


## 容器发现（DNS）
容器间的通讯不应该依赖ip地址，特别是在生产系统上，因为容器的创建和删除是动态的ip地址并不可靠。应该依赖容器的名字或id，这就涉及到DNS。同一个虚拟网络中的容器是可以相互发现的。

进入容器bash并安装ping命令
``` zsh
shiming@pro ➜  ~ docker container exec -it new_nginx bash
root@1de774242804:/# apt-get update
root@1de774242804:/# apt-get install iputils-ping
```

从容器内部ping同网络的另一个容器，发现可以ping通
``` zsh
root@1de774242804:/# ping webhost
PING webhost (172.18.0.3) 56(84) bytes of data.
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=1 ttl=64 time=5.28 ms
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=2 ttl=64 time=0.190 ms
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=3 ttl=64 time=0.195 ms
```

这样ping也可以
``` zsh
shiming@pro ➜  ~ docker container exec new_nginx ping webhost
PING webhost (172.18.0.3) 56(84) bytes of data.
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=1 ttl=64 time=0.715 ms
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=2 ttl=64 time=0.326 ms
64 bytes from webhost.my_app_net (172.18.0.3): icmp_seq=3 ttl=64 time=0.195 ms
```

附加学习：
[DNS: Why It’s Important and How It Works](https://dyn.com/blog/dns-why-its-important-how-it-works/)


## docker file
Step 1 : Create a file named Dockerfile

Step 2 : Add instructions in Dockerfile

Step 3 : Build dockerfile to create image

Step 4 : Run image to create container



COMMANDS
: docker build 
: docker build -t ImageName:Tag directoryOfDocekrfile

: docker run image

## 搜索image
`docker search <image>`
```
root@df ➜  ~ docker search drools
NAME                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
jboss/drools-workbench-showcase           Drools Workbench Showcase                       67                                      [OK]
jboss/drools-workbench                    Drools Workbench                                49                                      [OK]
chtijbug/drools-platform-docker           Drools Platform Container                       2                                       [OK]
......
```

## 常用软件
### jenkins
```
docker run -p 8080:8080 -p 50000:50000 -d --name jenkins <jenkins_image_id>
```

## 教程推荐
- [docker 101](https://www.aquasec.com/wiki/display/containers/Docker+Containers)
