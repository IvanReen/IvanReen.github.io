---
layout: post
title: Docker 使用
category: 技术
tags: Docker
keywords: Docker
---

安装及学习网址: [https://dev.aliyun.com/](https://dev.aliyun.com/)

## 搜索镜像

```
docker search
Options：
    -f, --filter filter    根据提供的条件过滤输出
        --format string    用Go模版打印漂亮的输出
        --limit  int       搜索结果最大数量（默认25）
        --no-trunc         不要截断输出
eg:
docker search centos --limit 2
```

## 获取镜像

```
docker pull centos
```

## 查看本地的镜像

```
docker images
或
docker image ls
```

REPOSITORY   镜像名称

TAG           最新版本

IMAGE_ID     镜像的 ID

## 修改 TAG

```
docker tag IMAGEID 镜像名称：新标签
eg：
docker  tag  4842 centos7:1.0
```

或者

```
docker tag 镜像名：tag 新镜像名称：新标签
eg：
docker tag hello-world:latest hello-world:1.1
```

## 查看本地镜像的 IMAGE ID

```
docker images -q
```

## 查看镜像制作历程

```
docker history centos
```

## 备份本地仓库的镜像

### 使用 save 子命令

```json
将本地仓库的镜像保存在nginx.img.tar当前目录下
docker save -o nginx.img.tar nginx
```

### 本地镜像导入本地 docker 库

```json
docker load -i nging.img.tar
```

或者

```
docker load < nginx.img.tar
```

## 删除镜像

```
docker rmi 镜像名：标签
docker rmi hello-world:1.1
```

# 容器管理

## 运行容器

```
docker run 参数 镜像名称：tag 执行的命令
参数：
    -i          保持容器存活（默认退出）
    -t          输出一个tty
    -d          后台运行
    --rm        执行完命令就销毁
    --name      自定义名称
    --restatr   重启策略
        no      默认不重启
        on-failure:int    非0码退出时重启，可指定次数
        always            重启
    --cap-add   运行使用的功能或命令
eg：
    # 运行一个容器，并获取一个tty
    docker run -it ubuntu
    # 运行一个容器，更改容器网络端口的状态
    docker run --cap-add=NET_ADMIN ubuntu sh -C "ip link eth0 down"
    # 运行一个容器，禁止使用chown命令
    docker run -it --cap-drop=CHOWN ubuntu
```

## 查看正在运行的容器

```
docker ps
```

CONTAINER ID   容器器 ID

IMAGE           容器器依赖的镜像

COMMAND         启动容器器时执⾏行行的命令或程序 CREATED 容器器启动时到现在的相隔时间

STATUS         容器器状态

PORTS           宿主机到容器器的端⼝口映射

## 查看本地所有的容器

```
docker ps -a
```

## 查看最近一次启动的容器

```
docker ps -l
```

## 获取正在运行的容器的 ID

```
docker ps -q
```

## 获取所有容器的 ID

```
docker ps -aq
```

## 查看容器元数据

```
docker inspect <容器ID | 容器名>
```

## 查看容器 IP

```
docker inspect --format='{{.NetworkSettings.IPAddress}}' 容器ID
```

## 容器重命名

```
docker rename OLD_NAME NEW_NAME
eg:
    docker rename ubuntu linux
```

## 重新启动一个处于停止的容器

```
# 后台运行
docker start 容器名/容器ID
# 启动容器并获取一个终端
docker start -i 容器名/容器ID
```

## 重启一个容器

```
docker restart 容器/容器ID
```

## 停止一个容器

```
docker stop 容器ID
```

## 关闭或删除容器

```
docker rm 容器ID
注意：容器名是不好使的
```

## 在外部执行运行中容器的命令

```
docker exec 容器名/容器ID （不是镜像名/镜像ID）执行的命令 [参数]
eg：
    docker exec ubuntu cat /etc/hosts
```

## 进入运行中的容器

### attach 命令

```
docker attach 容器ID
```

退出时，容器也会停止运行。

解决办法：先按[Ctrl]+p 键，接着再按[Ctrl]+q 键

### nsenter 工具

需要软件包支持：util-linux

```
docker ps     查看容器运行状况
# 使用容器ID或容器名获取PID
PID=$(docker inspect -f "{{ .State.Pid }}" 78d)
78d     容器ID
# 连接容器
nsenter --target $PID --mount --uts --ipc --net --pid
```

# 监控容器

## 查看日志

```
docker logs
参数：
    --tail     查看最后几条日志
    -t         指定时间戳
    --until      某时间之前的日志，eg：2018-01-01 | 45m
eg:
    docker logs --until 2018-01-01 78d
```

## 查看进程

```
docker top 78d
```

## 本地和容器交换文件

```
语法：
    docker cp [OPTIONS] CONTAINER:PATH LOCALPATH
    docker cp [OPTIONS] LOCALPATH|- CONTAINER:PATH
eg：
    # 将容器的文件复制到本地
    docker cp ubuntu:/root/a.txt .
    # 修改
    echo "update" > a.txt
    # 重新传回容器
    docker cp ./a.txt ubuntu:/root/a.txt
```

## 查看容器内发生改变的文件

```
# 进入容器，修改文件
nsenter --target $PID -- mount --uts --ipc --net --pid
mkdir change_dir
touch change_dir/file.txt
# 外部查看不同
docker diff 78d
```

A 表示在原来的基础上，做了了添加

C 表示在原来的基础上，做了了改变

## 实时输出 docker 服务端的事件

```
# 先监听
docker events
# 触发事件
docker start -i myhell-docker
# 输出结果
docker events
```

包括了创建，启动，关闭等

## 创建容器，不启用

```
docker images
docker create --name "myhello-docker" hello-world:latest
docker ps -a
```

## 把容器制作成镜像

```
# 导出容器
docker export 容器ID > tar包
# tar包制成镜像，- 表示把cat的内容作为import 的标准输入
cat tar包 | docker import -  REPOSITORY：TAG
```

**docker import 和 docker load 的区别:**

load 是用来导入镜像存储文件到本地镜像库的，镜像存储文件是用 save 从本地镜像库保存到本地硬盘的镜像备份文件，一般容量相对容器的快照⽂件较大，保存的是完整的记录，导入时，不能重新指定标签(tag)等元数据信息；而 import 导入的是容器的快照文件，容器的快照文件体积小，它丢弃了历史记录和元数据信息，仅保存容器当时的快照状态。
