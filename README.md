# docker安装
yum install -y yum-utils
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y --setopt=obsoletes=0 \
   docker-ce-17.03.2.ce-1.el7.centos.x86_64 \
   docker-ce-selinux-17.03.2.ce-1.el7.centos.noarch
# docker 配置
yum install *rhsm*

wget http://mirror.centos.org/centos/7/os/x86_64/Packages/python-rhsm-certificates-1.19.10-1.el7_4.x86_64.rpm
rpm2cpio python-rhsm-certificates-1.19.10-1.el7_4.x86_64.rpm | cpio -iv --to-stdout ./etc/rhsm/ca/redhat-uep.pem | tee /etc/rhsm/ca/redhat-uep.pem

# docker 加速器
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://y1oasprj.mirror.aliyuncs.com"]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker

# minikube
curl -Lo minikube http://storage.googleapis.com/minikube/releases/v0.28.2/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
curl -Lo kubectl http://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

export MINIKUBE_WANTUPDATENOTIFICATION=false
export MINIKUBE_WANTREPORTERRORPROMPT=false
export MINIKUBE_HOME=$HOME
export CHANGE_MINIKUBE_NONE_USER=true
mkdir -p $HOME/.kube
touch $HOME/.kube/config

export KUBECONFIG=$HOME/.kube/config

mkdir -p $HOME/.minikube/cache/v1.9.0/
curl -Lo kubeadm http://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubeadm && chmod +x kubeadm && sudo mv kubeadm $HOME/.minikube/cache/v1.9.0/
curl -Lo kubelet http://storage.googleapis.com/kubernetes-release/release/v1.9.0/bin/linux/amd64/kubelet && chmod +x kubelet && sudo mv kubelet $HOME/.minikube/cache/v1.9.0/




# minikube相关核心镜像0.9
docker pull registry.cn-qingdao.aliyuncs.com/kang123/kube-apiserver-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kube-apiserver-amd64:1.0 gcr.io/google_containers/kube-apiserver-amd64:v1.9.0

docker pull registry.cn-qingdao.aliyuncs.com/kang123/kube-controller-manager-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kube-controller-manager-amd64:1.0 gcr.io/google_containers/kube-controller-manager-amd64:v1.9.0

docker pull registry.cn-qingdao.aliyuncs.com/kang123/kube-scheduler-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kube-scheduler-amd64:1.0 gcr.io/google_containers/kube-scheduler-amd64:v1.9.0

docker pull registry.cn-qingdao.aliyuncs.com/kang123/etcd-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/etcd-amd64:1.0 gcr.io/google_containers/etcd-amd64:3.1.10

docker pull registry.cn-qingdao.aliyuncs.com/kang123/kube-proxy-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kube-proxy-amd64:1.0 gcr.io/google_containers/kube-proxy-amd64:v1.9.0

# 下面两个镜像需要但是不知道为啥需要
docker pull registry.cn-qingdao.aliyuncs.com/kang123/pause-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/pause-amd64:1.0 gcr.io/google_containers/pause-amd64:3.0

# 插件镜像
docker pull registry.cn-qingdao.aliyuncs.com/kang123/storage-provisioner:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/storage-provisioner:1.0 gcr.io/k8s-minikube/storage-provisioner:v1.8.1

docker pull registry.cn-qingdao.aliyuncs.com/kang123/kube-addon-manager:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kube-addon-manager:1.0 k8s.gcr.io/kube-addon-manager:v8.6

docker pull registry.cn-qingdao.aliyuncs.com/kang123/defaultbackend:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/defaultbackend:1.0 k8s.gcr.io/defaultbackend:1.4
docker pull registry.cn-qingdao.aliyuncs.com/kang123/nginx-ingress-controller:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/nginx-ingress-controller:1.0 quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.16.2

docker pull registry.cn-qingdao.aliyuncs.com/kang123/sidecar:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/sidecar:1.0 gcr.io/google_containers/k8s-dns-sidecar-amd64:1.14.7
docker pull registry.cn-qingdao.aliyuncs.com/kang123/dnsmasq:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/dnsmasq:1.0 gcr.io/google_containers/k8s-dns-dnsmasq-nanny-amd64:1.14.7
docker pull registry.cn-qingdao.aliyuncs.com/kang123/kubedns:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kubedns:1.0 gcr.io/google_containers/k8s-dns-kube-dns-amd64:1.14.7

docker pull registry.cn-qingdao.aliyuncs.com/kang123/kubernetes-dashboard-amd64:1.0
docker tag registry.cn-qingdao.aliyuncs.com/kang123/kubernetes-dashboard-amd64:1.0 k8s.gcr.io/kubernetes-dashboard-amd64:v1.8.1


minikube start --vm-driver=none --kubernetes-version=v1.9.0
