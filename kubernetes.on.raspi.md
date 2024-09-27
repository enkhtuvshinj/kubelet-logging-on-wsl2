## Installation of Kubernetes on Raspberry Pi

Assume that Ubuntu server or Raspberry light OS has been already installed on the Pi.

1. Update the package lists for upgrades.   
```
sudo apt-get update
```   
2. Add cgroup flags
Add `cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1` to the end of the `/boot/firmware/cmdline.txt` file.
```
echo " cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1" | sudo tee -a /boot/firmware/cmdline.txt
```
3. Reboot `sudo reboot`.   
4. Disable and uninstall swap
```
sudo dphys-swapfile swapoff
sudo dphys-swapfile uninstall
sudo apt purge -y dphys-swapfile
sudo apt autoremove -y
```
or
```
sudo swapoff -a
```
5. Check if swap memory is disabled using `free -m` command. The swap should be equal to zero for all columns.   
6. Install all dependencies.   
```
sudo apt install socat
sudo apt install conntrack
sudo apt install -y apt-transport-https
sudo apt install curl
```
7. Install docker engine and start it.   
```
sudo apt install docker.io
sudo systemctl start docker
sudo systemctl enable docker
```
8. Create a docker group and add the user to that group.
```
sudo groupadd docker
sudo usermod -aG docker $(whoami)
sudo service docker start
```   
9. Update apt repository and include the Kubernetes repository.
```
curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.31/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.31/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
```   
10. Install Kubernetes tools from package lists
```
sudo apt install kubeadm kubelet kubectl kubernetes-cni -y
```   
11. Check whether Kubelet service configuration is created.   
```
sudo cat /lib/systemd/system/kubelet.service
```   
12. Check whether Kubernetes applications were copied.   
```
ls -la /usr/bin/kube*
```  

### Handy commands

* Please note that Kubernetes control plane should have a Container Network Interface installed. For example, you can run the following to install Flannel. 
```
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```
Otherwise, when worker nodes join the cluster, they faces an error `Container runtime network not ready: NetworkReady=false reason:NetworkPluginNotReady message:docker: network plugin is not ready: cni config uninitialized`.   
* Run the following to disable swap.   
```
sudo apt-get install dphys-swapfile
sudo dphys-swapfile swapoff
sudo dphys-swapfile uninstall
sudo systemctl disable dphys-swapfile
```

### References

* [A Guide to Building a Kubernetes Cluster with Raspberry Pi’s](https://alexsniffin.medium.com/a-guide-to-building-a-kubernetes-cluster-with-raspberry-pis-23fa4938d420)
* [Using Kubelet Client to Attack the Kubernetes Cluster](https://www.cyberark.com/resources/threat-research-blog/using-kubelet-client-to-attack-the-kubernetes-cluster)
* [How to install Kubernetes on Raspberry PI](https://medium.com/karlmax-berlin/how-to-install-kubernetes-on-raspberry-pi-53b4ce300b58)
* [How to Install Kubernetes Cluster on Ubuntu 22.04](https://www.linuxtechi.com/install-kubernetes-on-ubuntu-22-04/?utm_content=cmp-true)
* [Kubernetes on Raspbian](https://github.com/alexellis/k8s-on-raspbian)
