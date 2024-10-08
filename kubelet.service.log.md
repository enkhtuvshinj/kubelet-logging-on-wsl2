## Enabling Kubelet logging

Kubernetes tools support verbosity levels (`--v=[1-9]`) for logging. Kubelet runs as a systemd service. Thus, we can enable the logging in its service unit configuration.

### Add a verbose level to Kubelet env file

Before adding the verbose level into Kubelet configuration, `sudo kube join --token <token> --discovery-token-ca-cert-hash <hash>` command that is generated from Kubernetes control plane should be run to generate the kubeadm env file.   

1. Append `/var/lib/kubelet/kubeadm-flags.env` file with `--v=[1-9]`.   
2. After appending the verbose level, `/var/lib/kubelet/kubeadm-flags.env` content looks like:   
```
KUBELET_KUBEADM_ARGS=--cgroup-driver=cgroupfs --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --v=[1-9]
```   
2. Restart Kubelet service and check its status.   
```
sudo systemctl restart kubelet
sudo systemd status kubelet
```   

### Add a log file to Kubelet service unit configuration

1. Check a current Kubelet service unit configuration   
```
sudo systemctl cat kubelet
```   
2. Add below lines to the bottom of `[Service]` section in `/lib/systemd/system/kubelet.service` file. PLease note that this will create a new file and does not append to the existing file.
```
StandardOutput=file:/home/<user_name>/<file_name>.log
StandardError=file:/home/<user_name>/<file_name>.log
```
3. After adding the below lines, `/lib/systemd/system/kubelet.service` content looks like:   
```
[Unit]
Description=kubelet: The Kubernetes Node Agent
Documentation=https://kubernetes.io/docs/
Wants=network-online.target
After=network-online.target

[Service]
ExecStart=/usr/bin/kubelet
Restart=always
StartLimitInterval=0
RestartSec=10
StandardOutput=file:/home/<user_name>/<file_name>.log
StandardError=file:/home/<user_name>/<file_name>.log

[Install]
WantedBy=multi-user.target
```   
4. Create the log file.   
```
touch  /home/<user_name>/<file_name>.log
```   
5. Reload systemd manager configuration and restart the service.   
```
sudo systemd daemon-reload
sudo systemd restart kubelet
```
6. Check status of the Kubelet service.   
```
sudo systemd status kubelet
```   

## References

* [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
* [System Logs](https://kubernetes.io/docs/concepts/cluster-administration/system-logs/)
* [how to enable kubelet logging verbosity](https://stackoverflow.com/questions/55739315/how-to-enable-kubelet-logging-verbosity)   
* [How to redirect output of systemd service to a file](https://stackoverflow.com/questions/37585758/how-to-redirect-output-of-systemd-service-to-a-file)
* [How to run Kubernetes without TLS for Debugging](https://eviatargerzi.medium.com/how-to-run-kubernetes-without-tls-for-debugging-eaf1baea572b)
