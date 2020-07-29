Install Docker: https://kubernetes.io/docs/setup/production-environment/container-runtimes/

Install Kubeadm: https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

Create Network: https://kubernetes.io/docs/concepts/cluster-administration/networking/#how-to-implement-the-kubernetes-networking-model
		https://docs.projectcalico.org/getting-started/kubernetes/quickstart

Create Deployments: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/



Master:

   1  yum update
    2  yum install -y yum-utils device-mapper-persistent-data lvm2
    3  yum-config-manager --add-repo   https://download.docker.com/linux/centos/docker-ce.repo
    4  yum update -y && yum install -y   containerd.io-1.2.13   docker-ce-19.03.11   docker-ce-cli-19.03.11
    5  mkdir /etc/docker
    6  cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

    7  mkdir -p /etc/systemd/system/docker.service.d
    8  systemctl daemon-reload
    9  systemctl restart docker
   10  systemctl enable docker
   11  docker --version
   12  docker ps
   13  docker images
   14  cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

   15  sudo sysctl --system
   16  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

   17  sudo setenforce 0
   18  sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
   19  sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
   20  sudo systemctl enable --now kubelet
   21  kubeadm version
   22  kubectl version
   23  kubelet --version
   24  kubeadm init --pod-network-cidr=192.168.0.0/16
   25   mkdir -p $HOME/.kube
   26    sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   27    sudo chown $(id -u):$(id -g) $HOME/.kube/config
   28  kubectl create -f https://docs.projectcalico.org/manifests/tigera-operator.yaml
   29  kubectl create -f https://docs.projectcalico.org/manifests/custom-resources.yaml
   30  kubectl get pods
   31  kubectl get pods --all-namespaces
   32  kubectl get nodes
   33  kubectl get nodes -o wide
   34  kubectl get pods
   35  kubectl get pods --all-namespace
   36  kubectl get pods --all-namespaces
   37  docker ps
   38  clear
   39  kubectl get deployments
   40  kubectl get deployments -all-namespaces
   41  kubectl get deployments --all-namespaces
   42  kubectl get deployments
   43  kubectl create deployment skillrary --image=skillrary/uk101tomcat
   44  kubectl get deployments
   45  kubectl create deployment nginx --image=ngnix:1.7.9
   46  kubectl get deployments
   47  kubectl get nodes
   48  kubectl get po --all-namespaces
   49  kubectl delete deployment nginx
   50  kubectl create deployment nginx --image=ngnix:1.18
   51  kubectl get deployments
   52  kubectl get po --all-namespaces
   53  kubectl describe po nginx-5c595fb8f5-lvdnb
   54  kubectl delete deployment nginx
   55  kubectl create deployment nginx --image=nginx:1.7.9
   56  kubectl get deployments
   57  kubectl get po
   58  kubectl describe po skillrary-67447cfbf4-9mc8n
   59  kubectl get deployments
   60  kubectl apply -f https://k8s.io/examples/controllers/nginx-deployment.yaml
   61  kubectl get deployments
   62  kubectl rollout status deployment.v1.apps/nginx-deployment
   63  kubectl get deployments
   64  history







Worker:

    1  yum update
    2  yum install -y yum-utils device-mapper-persistent-data lvm2
    3  yum-config-manager --add-repo   https://download.docker.com/linux/centos/docker-ce.repo
    4  yum update -y && yum install -y   containerd.io-1.2.13   docker-ce-19.03.11   docker-ce-cli-19.03.11
    5  mkdir /etc/docker
    6  cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

    7  mkdir -p /etc/systemd/system/docker.service.d
    8  systemctl daemon-reload
    9  systemctl restart docker
   10  systemctl enable docker
   11  docker --version
   12  docker ps
   13  docker images
   14  cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF

   15  sudo sysctl --system
   16  cat <<EOF | sudo tee /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=https://packages.cloud.google.com/yum/repos/kubernetes-el7-\$basearch
enabled=1
gpgcheck=1
repo_gpgcheck=1
gpgkey=https://packages.cloud.google.com/yum/doc/yum-key.gpg https://packages.cloud.google.com/yum/doc/rpm-package-key.gpg
exclude=kubelet kubeadm kubectl
EOF

   17  sudo setenforce 0
   18  sudo sed -i 's/^SELINUX=enforcing$/SELINUX=permissive/' /etc/selinux/config
   19  sudo yum install -y kubelet kubeadm kubectl --disableexcludes=kubernetes
   20  sudo systemctl enable --now kubelet
   21  kubeadm join 172.31.46.137:6443 --token 2fg0th.mhwbq9149r5d2y1a     --discovery-token-ca-cert-hash sha256:e44a544894986fdcf37dc68d959601918db5c4                                                                              33b624162b6981de9c3bce9bb1
   22  docker ps
   23  kubeadm version
   24  kubeadm join 172.31.46.137:6443 --token 2fg0th.mhwbq9149r5d2y1a     --discovery-token-ca-cert-hash sha256:e44a544894986fdcf37dc68d959601918db5c4                                                                              33b624162b6981de9c3bce9bb1
   25  kubeadm join 172.31.46.137:6443 --token 2fg0th.mhwbq9149r5d2y1a --discovery-token-ca-cert-hash sha256:e44a544894986fdcf37dc68d959601918db5c433b624162b6981de9c3bce9bb1
   26  docker ps
   27  docker ps -a
   28  clear
   29  docker ps
   30  history
