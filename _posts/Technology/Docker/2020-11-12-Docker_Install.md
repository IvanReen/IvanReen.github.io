---
layout: post
title: Docker 安装
category: 技术
tags: Docker
keywords: Docker
---

```
yum install -y yum-utils device-mapper-persistent-data lvm2
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum makecache fast
yum -y install docker-ce
service docker start
docker version

mkdir -p /etc/docker
tee /etc/docker/daemon.json <<-'EOF'
{
"registry-mirrors": ["https://nonerzup.mirror.aliyuncs.com"],
"graph":"/data/docker"
}
EOF

systemctl daemon-reload
systemctl restart docker

# 修改数据卷"graph":"/data/docker"

# 打包镜像

docker save -o images.tar postgres:latest mongo:latest

# 加载镜像

docker load -i images.tar

# 打包容器

docker export -o postgres-export.tar postgres

# 加载成一个镜像

docker import postgres-export.tar postgres:latest
```

daemon.json 变量意义

```
{
"exec-root": "", # 执行状态的文件的根目录
"experimental": false, # 是否开启试验性特性
"live-restore": true, # dockerd 挂掉是否保活容器（避免了 docker 服务异常而造成容器退出）
"log-driver": "", # 容器日志的驱动器
"mtu": 0, # 设置容器网络 MTU（最大传输单元）
"pidfile": "", # daemon PID 文件的位置
"cluster-store": "", # 集群存储系统的 URL
"cluster-advertise": "", # 对外的地址名称
"max-concurrent-downloads": 3, # 设置每个 pull 进程的最大并发
"max-concurrent-uploads": 5, # 设置每个 push 进程的最大并发
"default-shm-size": "64M", # 设置默认共享内存的大小
"shutdown-timeout": 15, # 设置关闭的超时时限(who?)
"debug": true, # 开启调试模式
"log-level": "", # 日志级别
"tls": true, # 开启传输层安全协议 TLS
"tlsverify": true, # 开启输层安全协议并验证远程地址
"tlscacert": "", # CA 签名文件路径
"tlscert": "", # TLS 证书文件路径
"tlskey": "", # TLS 密钥文件路径
"swarm-default-advertise-addr": "", # swarm 对外地址
"api-cors-header": "", # 设置 CORS（跨域资源共享-Cross-origin resource sharing）头
"selinux-enabled": false, # 开启 selinux(用户、进程、应用、文件的强制访问控制)
"userns-remap": "", # 给用户命名空间设置 用户/组
"group": "", # docker 所在组
"cgroup-parent": "", # 设置所有容器的 cgroup 的父类(?)
"init": false, # 容器执行初始化，来转发信号或控制(reap)进程
"init-path": "/usr/libexec/docker-init", # docker-init 文件的路径
"ipv6": false, # 开启 IPV6 网络
"iptables": false, # 开启防火墙规则
"ip-forward": false, # 开启 net.ipv4.ip_forward
"ip-masq": false, # 开启 ip 掩蔽(IP 封包通过路由器或防火墙时重写源 IP 地址或目的 IP 地址的技术)
"userland-proxy": false, # 用户空间代理
"userland-proxy-path": "/usr/libexec/docker-proxy", # 用户空间代理路径
"ip": "0.0.0.0", # 默认 IP
"bridge": "", # 将容器依附(attach)到桥接网络上的桥标识
"fixed-cidr": "", # (ipv4)子网划分，即限制 ip 地址分配范围，用以控制容器所属网段实现容器间(同一主机或不同主机间)的网络访问
"fixed-cidr-v6": "", # （ipv6）子网划分
"default-gateway": "", # 默认网关
"default-gateway-v6": "", # 默认 ipv6 网关
"icc": false, # 容器间通信
"raw-logs": false, # 原始日志(无颜色、全时间戳)
"allow-nondistributable-artifacts": [], # 不对外分发的产品提交的 registry 仓库
"seccomp-profile": "", # seccomp 配置文件
"no-new-privileges": false, # 禁止新优先级(??)
"default-runtime": "runc", # OCI 联盟(The Open Container Initiative)默认运行时环境
"node-generic-resources": ["NVIDIA-GPU=UUID1", "NVIDIA-GPU=UUID2"], # 对外公布的资源节点
"runtimes": { # 运行时
"cc-runtime": {"path": "/usr/bin/cc-runtime"},
"custom": {"path": "/usr/local/bin/my-runc-replacement", "runtimeArgs": ["--debug"]},
},
"api-cors-header": "", # 在引擎 API 中设置 CORS 标头
"authorization-plugins": [], # 要加载的授权插件
"bridge": "", # 将容器附加到网桥
"cgroup-parent": "", # 为所有容器设置父 cgroup
"cluster-store": "", # 分布式存储后端的 URL
"cluster-store-opts": {}, # 设置集群存储选项（默认 map []）
"cluster-advertise": "", # 要通告的地址或接口名称
"debug": true, # 启用调试模式，启用后，可以看到很多的启动信息。默认 false
"default-gateway": "", # 容器默认网关 IPv4 地址
"default-gateway-v6": "", # 容器默认网关 IPv6 地址
"default-runtime": "runc", # 容器的默认 OCI 运行时（默认为" runc"）
"default-ulimits": {}, # 容器的默认 ulimit（默认[]）
"dns": ["192.168.1.1"], # 设定容器 DNS 的地址，在容器的 /etc/resolv.conf 文件中可查看。
"dns-opts": [], # 容器 /etc/resolv.conf 文件，其他设置
"dns-search": [], # 设定容器的搜索域，当设定搜索域为 .example.com 时，在搜索一个名为 host 的 主机时，DNS 不仅搜索 host，还会搜索 host.example.com 。 注意：如果不设置， Docker 会默认用主机上的 /etc/resolv.conf 来配置容器。
"exec-opts": [], # 运行时执行选项
"exec-root": "", # 执行状态文件的根目录（默认为’/var/run/docker‘）
"fixed-cidr": "", # 固定 IP 的 IPv4 子网
"fixed-cidr-v6": "", # 固定 IP 的 IPv6 子网
"data-root": "/var/lib/docker", # Docker 运行时使用的根路径，默认/var/lib/docker
"group": "", # UNIX 套接字的组（默认为"docker"）
"hosts": [], # 设置容器 hosts
"icc": false, # 启用容器间通信（默认为 true）
"ip": "0.0.0.0", # 绑定容器端口时的默认 IP（默认 0.0.0.0）
"iPTAbles": false, # 启用 iptables 规则添加（默认为 true）
"ipv6": false, # 启用 IPv6 网络
"ip-forward": false, # 默认 true, # 启用 net.ipv4.ip_forward ,进入容器后使用 sysctl -a | grepnet.ipv4.ip_forward 查看
"ip-masq": false, # 启用 IP 伪装（默认为 true）
"labels": ["nodeName=node-121"], # docker 主机的标签，很实用的功能,例如定义：–label nodeName=host-121
"live-restore": true, # 在容器仍在运行时启用 docker 的实时还原
"log-driver": "", # 容器日志的默认驱动程序（默认为" json-file"）
"log-level": "", # 设置日志记录级别（"调试"，"信息"，"警告"，"错误"，"致命"）（默认为"信息"）
"max-concurrent-downloads": 3, # 设置每个请求的最大并发下载量（默认为 3）
"max-concurrent-uploads": 5, # 设置每次推送的最大同时上传数（默认为 5）
"mtu": 0, # 设置容器网络 MTU
"oom-score-adjust": -500, # 设置守护程序的 oom_score_adj（默认值为-500）
"pidfile": "", # Docker 守护进程的 PID 文件
"raw-logs": false, # 全时间戳机制
"selinux-enabled": false, # 默认 false，启用 selinux 支持
"swarm-default-advertise-addr": "", # 设置默认地址或群集广告地址的接口
"tls": true, # 默认 false, # 启动 TLS 认证开关
"tlscacert": "", # 默认 ~/.docker/ca.pem，通过 CA 认证过的的 certificate 文件路径
"tlscert": "", # 默认 ~/.docker/cert.pem ，TLS 的 certificate 文件路径
"tlskey": "", # 默认~/.docker/key.pem，TLS 的 key 文件路径
"tlsverify": true, # 默认 false，使用 TLS 并做后台进程与客户端通讯的验证
"userland-proxy": false, # 使用 userland 代理进行环回流量（默认为 true）
"userns-remap": "", # 用户名称空间的用户/组设置
"bip": "192.168.88.0/22", # 指定网桥 IP
"registry-mirrors": ["https://192.498.89.232:89"], # 设置镜像加速
"insecure-registries": ["120.123.122.123:12312"], # 设置私有仓库地址可以设为 http
"storage-driver": "", # 要使用的存储驱动程序
"storage-opts": ["overlay2.override_kernel_check=true", "overlay2.size=15G"], # 存储驱动程序选项, 根据驱动程序变化
"log-opts": {"max-file": "3", "max-size": "10m"}, # 容器默认日志驱动程序选项
"iptables": false, # 启用 iptables 规则添加（默认为 true）
}
```

后期修改 graph，采用软连接方式最佳

```
docker info | grep "Docker Root Dir"

service docker stop

mv /var/lib/docker /root/data/docker

ln -s /root/data/docker /var/lib/docker
```
