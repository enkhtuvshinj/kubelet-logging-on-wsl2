## Installation of Kubernetes on Raspberry Pi

Assume that Ubuntu server or Raspberry light OS has been already installed on the Pi.

1. Update the package lists for upgrades.   
`sudo apt-get update`   
2. Install all dependencies.   
```
sudo apt install socat
sudo apt install conntrack
sudo apt install -y apt-transport-https
sudo apt install curl
```
3. Install docker engine and start it.   
```
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```
4. Create a docker group and add the user to that group.
```
sudo groupadd docker
sudo usermod -aG docker $(whoami)
sudo service docker start
```   
5. Update apt repository and include the Kubernetes repository.
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
```   
6. Install Kubernetes tools from package lists
`sudo apt install kubeadm kubelet kubectl kubernetes-cni -y`   
7. Switch a memory swap off.
`sudo swapoff -a`
8. Or run the following to disable swap.   
```
sudo apt-get install dphys-swapfile
sudo dphys-swapfile swapoff
sudo dphys-swapfile uninstall
sudo systemctl disable dphys-swapfile
```
9. Check whether Kubelet service configuration is created.   
`sudo cat /lib/systemd/system/kubelet.service`   
10. Check whether Kubernetes applications were copied.   
`ls -la /usr/bin/kube*`  