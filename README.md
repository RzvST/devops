DevOps Infrastructure project 

Machines and environment configuration. 

Kubernetes 

  1. Ubuntu Master VM  
    - Updates : "sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
    - Docker : "sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/dockersetup.sh)"
    - Kubernetes :  --->>> https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl
                   sudo apt-get update
                   sudo apt-get install -y apt-transport-https ca-certificates curl gpg
                   sudo mkdir -p -m 755 /etc/apt/keyrings
                   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
                   echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
                   sudo apt update
                   sudo apt-get install -y kubelet kubeadm kubectl
                   sudo apt-mark hold kubelet kubeadm kubectl

                   --->>>> https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/create-cluster-kubeadm/

                   sudo kubeadm init --pod-network-cidr=10.244.0.0/16
              After this command you will get an output that you will run on the worker nodes to join them to the cluster and will look this bellow one.    
              



                   mkdir -p $HOME/.kube
                   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
                   sudo chown $(id -u):$(id -g) $HOME/.kube/config

                   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
                   kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v0.49.0/deploy/static/provider/baremetal/deploy.yaml

     

  3. Ubuntu Slaves VM
    - Updates : "sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
    - Docker : "sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/dockersetup.sh)"
    - Kubernetes :  --->>> https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/#installing-kubeadm-kubelet-and-kubectl
                   sudo apt-get update
                   sudo apt-get install -y apt-transport-https ca-certificates curl gpg
                   sudo mkdir -p -m 755 /etc/apt/keyrings
                   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
                   echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
                   sudo apt update
                   sudo apt-get install -y kubelet kubeadm kubectl
                   sudo apt-mark hold kubelet kubeadm kubectl

             Run the command to join this node to the cluster 

                  
                   
