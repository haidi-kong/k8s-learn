# k8s-learn
k8s learning
学习教材
https://github.com/guangzhengli/k8s-tutorials?tab=readme-ov-file#%E6%B3%A8%E5%86%8C-docker-hub-%E8%B4%A6%E5%8F%B7%E7%99%BB%E5%BD%95

1.windows 安装Docker Desktop

2.配置docker 环境 https://www.cnblogs.com/jokingremarks/p/18156334
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

3.安装 minikube
https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download windows 安装方式




