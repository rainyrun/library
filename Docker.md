# Docker

## 基本命令

```sh
# 启动docker
systemctl docker start
# 开机自动启动
systemctl docker enable
# 修改守护进程监听端口 或套接字
docker daemon -H tcp://0.0.0.0:2375 [...] # 可绑定多个
docker daemon -H unix://home/docker/docker.sock
# 环境变量 明确docker监听的端口
export DOCKER_HOST="tcp://0.0.0.0:2375"
# 守护进程的调试模式
docker daemon -D

# 启动和停止docker
service docker stop
service docker start

# 查看docker是否正常
docker info
# 创建和启动docker容器
# -i STDIN开启 -t 分配伪tty终端 使用ubuntu基础镜像 并在容器里启动bash。
docker run -i -t ubuntu /bin/bash
# --name 给容器命名，名字要唯一
docker run --name rainy_docker -i -t ubuntu /bin/bash
# -p 在宿主机上随机打开一个端口，连接到容器的80端口上
docker run -d -p 80 仓库名/镜像名 /bin/bash
docker run -d -p [宿主机端口]:[容器端口] 仓库名/镜像名 /bin/bash
docker run -d -p 127.0.0.1::80 仓库名/镜像名 /bin/bash # 绑在宿主机的某ip(127.0.0.1)的随机端口上
docker run -d -P 仓库名/镜像名 /bin/bash # -P 将容器内公开的端口（由EXPOSE命令指定的端口），随机绑到宿主机的端口
docker run -ti -e "WEB_PORT=8080" ubuntu env # -e 设置环境变量，只在运行时有效。 env 查询容器里的环境变量
docker run -d --name website -v $PWD/website:/var/www/html/website:ro # -v 让宿主机的目录作为卷，挂在在容器内 :ro 容器内目录只读 :rw 可读可写
exit # 退出容器
# 获取帮助
docker help
man docker-run
# 查看系统中容器列表
docker ps -a
# 查看正在运行的容器
docker ps
# 查看最后一个容器
docker ps -l
# 显示最后x个容器
docker ps -n x
# 删除容器
docker rm [dockername|dockerID]
# 删除所有容器 -q 只返回ID -a 所有容器
docker rm 'sudo docker ps -a -q'
# 删除镜像（删本地）
docker rmi [仓库名/镜像名 ...]
# 删除所有镜像
docker rmi 'docker images -a -q'
# 重新启动已停止的容器
docker start [dockername|dockerID]
# 附着到正在运行的容器
docker attach [dockername|dockerID]

# 创建守护式容器 -d 容器放在后台运行 在容器里打印hello world
docker run --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
# 查看容器日志，会输出最后几条日志项
docker logs dockername
# 持续输出日志
docker logs -f dockername
# 最后10行日志
docker logs --tail 10 dockername
# 给日志增加时间戳
docker logs -t dockername
# 启动 syslog
docker run --log-driver="syslog" --name daemon_dwayne -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

# 查看容器内的进程
docker top daemon_dave
# 容器统计信息
docker stats [dockername...]
# 在容器内部运行进程
# -d 运行后台进程
docker exec -d daemon_dave touch /etc/new_config_file
# 运行交互命令
docker exec -t -i daemon_dave /bin/bash
# 停止守护式进程
docker stop [dockername|dockerID]

# 自动重启容器
# --restart=always 无条件重启
# --restart=on-failure[:5] 退出代码非0，自动重启[5次]
docker run --restart=always --name daemon_dave -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"

# 容器详细信息
docker inspect dockername
docker inspect --format='{{ .State.Running }} {{.Name}}' [daemon_dave...]

# docker 本地镜像的存储目录
/var/lib/docker
```

## docker 镜像和仓库

docker 镜像由文件系统叠加而成，最底端是一个引导文件系统bootfs。第二层是root文件系统rootfs，永远是只读。

当一个容器启动后，它会被移到内存中，而引导文件系统会被卸载。

Docker利用联合加载技术，可以在rootfs上加载更多文件系统。这样的文件系统成为镜像。一个镜像可以放在另一个镜像的顶部。位于下面的镜像称为父镜像。位于最底部的称为基础镜像。

![Docker文件系统层](images/Docker/Docker文件系统层.png)

```sh
# 列出本地镜像
docker images [仓库名]
# 拉远端镜像 ubuntu是仓库 12.04是tag 一个镜像可以有多个标签
docker pull ubuntu:12.04
docker pull ubuntu # 等价于 docker pull ubuntu:latest

# 在Docker Hub 查找带有puppet的镜像
docker search puppet

# 构建镜像（基于已有镜像）
# 在容器内做更新后，提交该更新。类似git的提交一个commit
docker commit [容器ID] 仓库名/镜像名
# -m 指定提交信息 -a 指定作者信息
docker commit -m"A new custom image" -a"James" [容器ID] 仓库名/镜像名:标签名

# 使用dockerfile构建镜像，会在本地目录找Dockerfile文件
docker build -t="[仓库名/镜像名:标签名]"
# 从git仓库找Dockerfile
docker build -t="[仓库名/镜像名]" git@github.com:用户名/仓库
# 略过缓存
docker build --no-cache -t="仓库名/镜像名]"
# 查看镜像的构建过程
docker history [镜像ID]
# 查看容器的映射端口
docker port [镜像ID|容器名] 80


# 登录到Docker hub
docker login
# 退出登录
docker logout


```

用 Dockerfile 构建镜像

```sh
# 命令大写 第一行都是FROM 构建基础镜像
FROM ubuntu:14.04
# 告知作者
MAINTAINER rainy "rainy@email.com"
# RUN 在当前镜像中运行指定的命令，默认在Shell里，使用命令包装器 /bin/sh -c .每条RUN命令都会创建一个新的镜像层。
RUN echo 'Hi, docker' > /usr/share/nginx/html/index.html
# rainy: 这个环境变量之后的命令都执行吗？没太懂
ENV REFRESHED_AT 2021-10-06
# exec 格式的RUN命令
RUN ["apt-get", "install", "-y", "nginx"]
# EXPOSE 容器内的应用程序将会使用容器指定的端口
EXPOSE 80
# CMD 命令指定容器被启动时要运行的命令，只能有一条。会被docker run指定的命令覆盖
CMD ["/bin/bash", "-l"]
# ENTRYPOINT 不会被 docker run 覆盖，docker run的指令会变成参数，传给 ENTRYPOINT
ENTRYPOINT ["/usr/sbin/nginx"]
# 设置工作目录
WORKDIR /opt/webapp/db
# 设置环境变量 使用变量 $RVM_PATH 会被保存在从镜像创建的任何容器中
ENV RVM_PATH /home/rvm/
# 设置运行时的用户
USER dbadmin
# 给容器加卷，卷可以在容器间共享。为镜像创建一个名为/opt/project的挂载点。提交或创建镜像时，卷不包含在镜像里。
VOLUME ["/opt/project"]
# 将构建环境下的文件和目录复制到镜像中 以/结尾，表示是目录；添加压缩文件，会解压
ADD software.lic(当前目录) /opt/application/software.lic(镜像中的目录)
# 和ADD类似，但只复制，不解压
COPY conf.d/ /etc/apache2/
```

## Docker 内部连网

在安装Docker时，会创建一个新的网络接口，名字是Docker0。每个Docker容器都会在这个接口上分配一个ip地址。

接口 docker0 是一个虚拟的以太网桥。Docker 每创建一个容器，就会创建一组互联的网络接口。这组接口其中一端作为容器的eth0接口，另一端统一命名为类似vethec6a，作为宿主机的一个端口。通过把每个 `veth*` 接口帮到docker0网桥，Docker创建了一个虚拟子网，子网内有宿主机和所有Docker容器。

```sh
# 在宿主机上执行，可以看到docker容器的网关ip
ip a show docker0
# 在容器上执行，可以看到容器的ip
ip a show eth0
traceroute google.com # 查看途径的路由器，可以看到网关ip是宿主机上查的ip

# Docker 的 iptables 和 NAT 配置
iptables -t nat -L -n
```




