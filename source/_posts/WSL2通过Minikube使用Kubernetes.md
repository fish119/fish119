title: WSL2通过Minikube使用Kubernetes
tags:
  - Kubernetes
  - Minikube
  - WSL
categories:
  - WSL
date: 2021-12-26 19:39:07
---
为了方便在笔记本上学习和使用Kubernetes，尝试在WSL2下通过Minikube搭建Kubernetes环境。

<!--more-->

# 前置条件
- WSL2安装Ubuntu 18.04
- 安装Docker
- 安装kubectl

# 安装 Minikube
[参考：官方文档](https://minikube.sigs.k8s.io/docs/start/)
```bash
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube
```
或
```bash
# Download the latest version of Minikube
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
# Make the binary executable
chmod +x ./minikube
# Move the binary to your executable path
sudo mv ./minikube /usr/local/bin/
```
# 安装所需依赖

## 安装 conntrack
```
# Install the conntrack package
sudo apt install -y conntrack
```

## 启用Systemd
为了在WSL2使Systemd，我们将使用由丹尼尔·卢埃林提供的脚本。

- 安装脚本
```bash
# Install the needed packages
sudo apt install -yqq daemonize dbus-user-session fontconfig
```

- 创建并编辑 /usr/sbin/start-systemd-namespace 文件

```bash
# Create the start-systemd-namespace script
sudo vi /usr/sbin/start-systemd-namespace
```

文件内容如下：

```bash
#!/bin/bash
 
SYSTEMD_PID=$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')
if [ -z "$SYSTEMD_PID" ] || [ "$SYSTEMD_PID" != "1" ]; then
    export PRE_NAMESPACE_PATH="$PATH"
    (set -o posix; set) | \
        grep -v "^BASH" | \
        grep -v "^DIRSTACK=" | \
        grep -v "^EUID=" | \
        grep -v "^GROUPS=" | \
        grep -v "^HOME=" | \
        grep -v "^HOSTNAME=" | \
        grep -v "^HOSTTYPE=" | \
        grep -v "^IFS='.*"$'\n'"'" | \
        grep -v "^LANG=" | \
        grep -v "^LOGNAME=" | \
        grep -v "^MACHTYPE=" | \
        grep -v "^NAME=" | \
        grep -v "^OPTERR=" | \
        grep -v "^OPTIND=" | \
        grep -v "^OSTYPE=" | \
        grep -v "^PIPESTATUS=" | \
        grep -v "^POSIXLY_CORRECT=" | \
        grep -v "^PPID=" | \
        grep -v "^PS1=" | \
        grep -v "^PS4=" | \
        grep -v "^SHELL=" | \
        grep -v "^SHELLOPTS=" | \
        grep -v "^SHLVL=" | \
        grep -v "^SYSTEMD_PID=" | \
        grep -v "^UID=" | \
        grep -v "^USER=" | \
        grep -v "^_=" | \
        cat - > "$HOME/.systemd-env"
    echo "PATH='$PATH'" >> "$HOME/.systemd-env"
    exec sudo /usr/sbin/enter-systemd-namespace "$BASH_EXECUTION_STRING"
fi
if [ -n "$PRE_NAMESPACE_PATH" ]; then
    export PATH="$PRE_NAMESPACE_PATH"
fi
```

- 创建并编辑 /usr/sbin/enter-systemd-namespace 文件

```bash
# Create the enter-systemd-namespace
sudo vi /usr/sbin/enter-systemd-namespace
```

文件内容如下：

```bash
#!/bin/bash
 
if [ "$UID" != 0 ]; then
    echo "You need to run $0 through sudo"
    exit 1
fi
 
SYSTEMD_PID="$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')"
if [ -z "$SYSTEMD_PID" ]; then
    /usr/sbin/daemonize /usr/bin/unshare --fork --pid --mount-proc /lib/systemd/systemd --system-unit=basic.target
    while [ -z "$SYSTEMD_PID" ]; do
        SYSTEMD_PID="$(ps -ef | grep '/lib/systemd/systemd --system-unit=basic.target$' | grep -v unshare | awk '{print $2}')"
    done
fi
 
if [ -n "$SYSTEMD_PID" ] && [ "$SYSTEMD_PID" != "1" ]; then
    if [ -n "$1" ] && [ "$1" != "bash --login" ] && [ "$1" != "/bin/bash --login" ]; then
        exec /usr/bin/nsenter -t "$SYSTEMD_PID" -a \
            /usr/bin/sudo -H -u "$SUDO_USER" \
            /bin/bash -c 'set -a; source "$HOME/.systemd-env"; set +a; exec bash -c '"$(printf "%q" "$@")"
    else
        exec /usr/bin/nsenter -t "$SYSTEMD_PID" -a \
            /bin/login -p -f "$SUDO_USER" \
            $(/bin/cat "$HOME/.systemd-env" | grep -v "^PATH=")
    fi
    echo "Existential crisis"
fi
```

- Edit the permissions of the enter-systemd-namespace script
```bash
sudo chmod +x /usr/sbin/enter-systemd-namespace
```
- Edit the bash.bashrc file
```bash
sudo sed -i 2a"# Start or enter a PID namespace in WSL2\nsource /usr/sbin/start-systemd-namespace\n" /etc/bash.bashrc
```
- 退出并重新启动WSL2会话

# Minikube：单节点kubernetes集群
```bash
# Check if the KUBECONFIG is not set
echo $KUBECONFIG
# Check if the .kube directory is created > if not, no need to create it
ls $HOME/.kube
# Check if the .minikube directory is created > if yes, delete it
ls $HOME/.minikube
# Create the cluster with sudo
minikube start --driver=none --image-repository=registry.cn-hangzhou.aliyuncs.com/google_containers
```
# 设置非root用户能够使用kubectl
```bash

# Change the owner of the .kube and .minikube directories
sudo chown -R $USER $HOME/.kube $HOME/.minikube
# Check the access and if the cluster is running
kubectl cluster-info
# Check the resources created
kubectl get all --all-namespaces
```

# 集群创建成功
查看Minikube状态
```bash
minikube status
```
访问 https://localhost:8443 或 https://wsl2ip:8443 可访问Kubernetes API

# Minikube：Kubernetes Dashbord
## 开启 Kubernetes Dashbord
Minikube内置 [Kubernetes Dashbord](https://github.com/kubernetes/dashboard)，可以非常方便的使用：
```bash
# Enable the Dashboard service
sudo minikube dashboard
# Access the Dashboard from a browser on Windows side
```
输出的地址即为Kubernetes Dashbord的访问地址，或使用如下命令查看地址：
```bash
minikube dashboard --url
```
## 通过LoadBalancer访问Dashboard
- 编辑Dashboard服务，并将其类型更改为LoadBalancer：
```bash
# Edit the Dashoard service
kubectl edit service/kubernetes-dashboard --namespace kubernetes-dashboard
# Go to the very end and remove the last 2 lines
status:
  loadBalancer: {}
# Change the type from ClusterIO to LoadBalancer
  type: LoadBalancer
# Save the file
```
- 再次检查Dashboard服务
```bash
# Get all the services from the dashboard namespace
kubectl get all --namespace kubernetes-dashboard
# Access the Dashboard from a browser on Windows side with the URL: localhost:<port exposed>
```

# 测试：简单部署 nginx
## 创建一个部署和服务
```bash
kubectl create deployment nginx --image=nginx
kubectl create service nodeport nginx --tcp 80:80
kubectl get pods,svc
```
当pod status是running 时，查看nginx的PORT(S)列，可见其外部访问端口
通过Windows宿主机浏览器访问loalhost:端口即可访问

# 参考资料
[minikube start](https://minikube.sigs.k8s.io/docs/start/)
[wsl-docker-kubernetes-on-the-windows-desktop](https://kubernetes.io/blog/2020/05/21/wsl-docker-kubernetes-on-the-windows-desktop/)
[单核小鸡上的Minikube实践](https://segmentfault.com/a/1190000022685244)

