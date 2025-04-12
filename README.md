# k8s-learn
k8s learning
学习教材
https://github.com/guangzhengli/k8s-tutorials?tab=readme-ov-file#%E6%B3%A8%E5%86%8C-docker-hub-%E8%B4%A6%E5%8F%B7%E7%99%BB%E5%BD%95

**1.windows 安装Docker Desktop**

**2.配置docker 环境 https://www.cnblogs.com/jokingremarks/p/18156334**
wsl 可以手动下载
启动出现的问题docker 不启动 https://blog.csdn.net/cplvfx/article/details/138033592  配置虚拟环境
docker 一直加载 https://www.cnblogs.com/jokingremarks/p/18156334 管理员运行最后两部 出现

```
PS C:\Windows\system32> net start com.docker.service
Docker Desktop Service 服务正在启动 .
Docker Desktop Service 服务已经启动成功。

PS C:\Windows\system32> cd "C:\Program Files\Docker\Docker"
PS C:\Program Files\Docker\Docker> .\DockerCli.exe -SwitchDaemon
switching to windows engine: Post "http://ipc/engine/switch": open \\.\pipe\dockerBackendApiServer: The system cannot find the file specified.
```

**3.安装 minikube**
https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download windows 安装方式
minikube start --vm-driver docker --container-runtime=docker
安装过程中无法连接dokcer的镜像源，需要走外网，多试几次
启动后可以在docker 桌面端看到
![image](https://github.com/user-attachments/assets/3d794746-98df-4d06-9b08-036738650cbc)

有意思的是容器和宿主机之间的网络交互：gpt4o给的答案
网络接口：
容器的网络接口是虚拟的，通常命名为 eth0，和宿主机的 eth0 不同，容器通过虚拟接口连接到 Docker 创建的虚拟网络（如 docker0）。
宿主机则使用物理网卡（如 ens33）直接与外部网络通信。
容器的 IP 地址通常是私有的（例如 172.x.x.x），由 Docker 的网络驱动程序分配，属于虚拟子网。
宿主机的 IP 地址是实际网络中的地址（如局域网或公共网络 IP）。
## 容器与宿主机网络的交互
Docker 默认创建一个虚拟网桥（docker0），容器的虚拟网络接口通过这个网桥与宿主机的网络接口相连。
容器通过 NAT 转换使用宿主机的 IP 和端口与外部通信。
端口映射（Port Mapping）：
使用 -p 或 -P 选项将容器的端口映射到宿主机的端口。
例如，docker run -p 8080:80 会将容器的 80 端口映射到宿主机的 8080 端口，外部可以通过 宿主机IP:8080 访问容器。

## 容器与互联网的交互
NAT 转换：
在 bridge 模式下，容器通过宿主机的 NAT 转换访问互联网。
流程：容器发起请求 → 宿主机的虚拟网桥（docker0） → NAT 转换 → 宿主机的物理网卡 → 互联网。反向同理

minikube start --vm-driver docker --container-runtime=docker 这个命令是用docker启动了minikube， 实际执行命令都是转发到docker 中

3.安装kubectl 一个快捷命令工具
https://kubernetes.io/zh-cn/docs/tasks/tools/install-kubectl-windows/
kubectl 版本和集群版本之间的差异必须在一个小版本号内。 例如：v1.32 版本的客户端能与 v1.31、 v1.32 和 v1.33 版本的控制面通信。
安装命令 
```
curl.exe -LO "https://dl.k8s.io/release/v1.34.0/bin/windows/amd64/kubectl.exe"  --奇怪，无法覆盖docker desktop装的 -_- 先暂时忽略
```
**4.使用docker构建容器**
```
docker build . -f Dockerfile.dev -t 908364810/hellok8s:v1
```
出现如下报错，估计是网络原因，发现不是，是镜像源的问题，https://blog.csdn.net/m0_70878103/article/details/144130047 在docker desk中增加镜像源就行
```
ERROR: failed to solve: golang:1.16-buster: failed to resolve source metadata for docker.io/library/golang:1.16-buster: failed to authorize: failed to fetch oauth token: Post "https://auth.docker.io/token": dial tcp 199.59.149.234:443: connectex: A connection attempt failed because the connected party did not properly respond after a period of time, or established connection failed because connected host has failed to respond.
View build details: docker-desktop://dashboard/build/desktop-linux/desktop-linux/ah35lae2w4ztxi17ih3zqi7w
```
启动容器
```
docker run -p 3000:3000 --name hellok8s -d 908364810/hellok8s:v1
```
启动之后有三个地址 可以访问启动的容器资源
以太网适配器 vEthernet (WSL):
   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::ab7:56a2:c4b0:4fbd%49
   IPv4 地址 . . . . . . . . . . . . : 172.23.48.1 --可以访问
   子网掩码  . . . . . . . . . . . . : 255.255.240.0
   默认网关. . . . . . . . . . . . . :
以太网适配器 vEthernet (Default Switch):--可以访问
   连接特定的 DNS 后缀 . . . . . . . :
   本地链接 IPv6 地址. . . . . . . . : fe80::390d:7776:e9fd:7a4d%16
   IPv4 地址 . . . . . . . . . . . . : 172.19.0.1
   子网掩码  . . . . . . . . . . . . : 255.255.240.0
   默认网关. . . . . . . . . . . . .

minikube ip  
192.168.58.2   --无法访问

```
**4.使用k8s 构建nginx-pod**
```
使用 nginx.yaml 构建pod，使用如下命令
```
kubectl apply -f nginx.yaml
```
发现如下报错，Unable to connect to the server: dial tcp 127.0.0.1:6443: connectex: No connection could be made because the target machine actively refused it #12448
可以参考https://stackoverflow.com/questions/50490808/unable-to-connect-to-the-server-dial-tcp-18080-connectex-no-connection-c 这个解决，开启dockerhub 的k8s


