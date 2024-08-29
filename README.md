DevOps Infrastructure project 

Machines and environment configuration. 

**Kubernetes **

**Master Node**

Updates : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
Update host files : 

sudo vim /etc/hosts 

///////// host files ///////////

192.168.1.11   kubernetesmaster
192.168.1.14   kubernetesslave4
192.168.1.15   kubernetesslave5
192.168.1.13   kubernetesslave3
192.168.1.12   kubernetesslave2
192.168.1.10   kubernetesslave1

/////////////////////////////////

Disable swap : 

sudo vim /etc/fstab 

Comment the line with swapp

Kernel parameters :

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

Install Containerd :

sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd

Add repository for kubernetes:

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

///////////////////////////////////////////////////////


sudo kubeadm init

After this command you will get an output that you will run on the worker nodes to join them to the cluster and will look this bellow one.

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

              
                 sudo kubeadm join 192.168.1.11:6443 --token ska119.fpzfbyq85jwn9jy9 \
        --discovery-token-ca-cert-hash sha256:347555c5e86f060d85ba289dc37da166f84f9f5d97f0501d74aeac726ff4f184

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Making home folders after the nodes are joined

////////////////////////////////////////////////////////

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

////////////////////////////////////////////////////////

Applying networking for nodes

kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
                   

**Slaves Nodes**

Updates : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
Update host files : 

sudo vim /etc/hosts 

///////// host files ///////////

192.168.1.11   kubernetesmaster
192.168.1.14   kubernetesslave4
192.168.1.15   kubernetesslave5
192.168.1.13   kubernetesslave3
192.168.1.12   kubernetesslave2
192.168.1.10   kubernetesslave1

/////////////////////////////////

Disable swap : 

sudo vim /etc/fstab 

Comment the line with swapp

Kernel parameters :

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

Install Containerd :

sudo apt install -y curl gnupg2 software-properties-common apt-transport-https ca-certificates
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmour -o /etc/apt/trusted.gpg.d/docker.gpg
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install -y containerd.io
containerd config default | sudo tee /etc/containerd/config.toml >/dev/null 2>&1
sudo sed -i 's/SystemdCgroup \= false/SystemdCgroup \= true/g' /etc/containerd/config.toml
sudo systemctl restart containerd
sudo systemctl enable containerd

Add repository for kubernetes:

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

///////////////////////////////////////////////////////

Run the join command : 

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

              
                 sudo kubeadm join 192.168.1.11:6443 --token ska119.fpzfbyq85jwn9jy9 \
        --discovery-token-ca-cert-hash sha256:347555c5e86f060d85ba289dc37da166f84f9f5d97f0501d74aeac726ff4f184

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


<><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><><>

**SonarQube**

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Updates : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
Docker : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/dockersetup.sh)"
Sonarqube : sudo docker run -d --name sonar -p 9000:9000 sonarqube:lts-community --restart=always

SonarQube is accessible ip:9000

User : admin
Pass: admin

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

**Nexus**

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Updates : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
Docker : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/dockersetup.sh)"
Nexus : sudo docker run -d --name nexus -p 8081:8081 sonatype/nexus3:latest --restart=always

SonarQube is accessible ip:8081

User :admin 

For the password : 

////////////////////////////////////////
docker ps
docker exec -it <container_ID> /bin/bash
cat admin.password
exit

////////////////////////////////////////

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////


**Jenkins**

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

Updates : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/serverupdate.sh)"
Docker : sudo sh -c "$(curl -fsSL https://raw.githubusercontent.com/RzvST/mediaserver/main/scripts/dockersetup.sh)"
Jenkins

# Install OpenJDK 17 JRE Headless
sudo apt install openjdk-17-jre-headless -y

# Download Jenkins GPG key
sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
  https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key

# Add Jenkins repository to package manager sources
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
  https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
  /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package manager repositories
sudo apt-get update

# Install Jenkins
sudo apt-get install jenkins -y

////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////                  
                   
