DevOps Infrastructure project 

Machines and environment configuration. 

Kubernetes 

  1. Ubuntu Master VM  
    - Updates : "sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
    - update host files : sudo vim /etc/hosts 

///////// host files ///////////

192.168.1.11   kubernetesmaster
192.168.1.14   kubernetesslave4
192.168.1.15   kubernetesslave5
192.168.1.13   kubernetesslave3
192.168.1.12   kubernetesslave2
192.168.1.10   kubernetesslave1

/////////////////////////////////

    - disable swap : 

      sudo swapoff -a
      sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

    - kernel parameters :

      /////////////////
      sudo tee /etc/modules-load.d/containerd.conf <<EOF
      overlay
      br_netfilter
      EOF
      /////////////////

      sudo modprobe overlay
      sudo modprobe br_netfilter

      ////////////////////////////////////////
      sudo tee /etc/sysctl.d/kube.conf <<EOT
      net.bridge.bridge-nf-call-ip6tables = 1
      net.bridge.bridge-nf-call-iptables = 1
      net.ipv4.ip_forward = 1
      EOT
      ////////////////////////////////////////

      sudo sysctl --system

    - Install Containerd

         sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
         sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
         sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
         sudo apt update
         sudo apt install -y containerd.io
         containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
         sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
         sudo systemctl restart containerd
         sudo systemctl enable containerd

    - Add repository for kubernetes

          curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
          echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
          sudo apt-get update
          sudo apt-get install -y kubelet kubeadm kubectl
            sudo apt-mark hold kubelet kubeadm kubectl

    
   

///////// uninstall and purge of kubernetes ///////////

kubeadm reset
sudo apt-get purge kubeadm kubectl kubelet kubernetes-cni kube*   
sudo apt-get autoremove  
sudo rm -rf ~/.kube


              sudo kubeadm init

              After this command you will get an output that you will run on the worker nodes to join them to the cluster and will look this bellow one.    
              
                 sudo kubeadm join 192.168.1.11:6443 --token ska119.fpzfbyq85jwn9jy9 \
        --discovery-token-ca-cert-hash sha256:347555c5e86f060d85ba289dc37da166f84f9f5d97f0501d74aeac726ff4f184


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

                   Disable Linux Swap

                   sudo swapoff -a && sudo sed -i '/swap/d' /etc/fstab

             Run the command to join this node to the cluster 

                  
                   
