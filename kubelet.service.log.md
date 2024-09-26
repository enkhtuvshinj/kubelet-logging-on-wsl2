## Enabling Kubelet logging

Kubernetes tools support verbosity levels (`--v=[1-9]`) for logging. Kubelet runs as a systemd service. Thus, we can enable the logging in its service unit configuration.

### Add a verbose level to Kubelet env file

Before adding the verbose level into Kubelet configuration, `sudo kube join --token <token> --discovery-token-ca-cert-hash <hash>` command that is generated from Kubernetes control plane should be run to generate the kubeadm env file.   

1. Append `/var/lib/kubelet/kubeadm-flags.env` file with `--v=[1-9]`.   
2. After appending the verbose level, `/var/lib/kubelet/kubeadm-flags.env` content looks like:   
`KUBELET_KUBEADM_ARGS=--cgroup-driver=cgroupfs --network-plugin=cni --pod-infra-container-image=k8s.gcr.io/pause:3.1 --v=[1-9]`   
2. Restart Kubelet service   
`sudo systemctl restart kubelet`   

### Add a log file to Kubelet service unit configuration

1. Check a current Kubelet service unit configuration
`sudo systemctl cat kubelet`   
2. Add below lines into `/lib/systemd/system/kubelet.service`
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
StandardOutput=file:/home/<user_name>/<file_name>.log
StandardError=file:/home/<user_name>/<file_name>.log

[Service]
ExecStart=/usr/bin/kubelet
Restart=always
StartLimitInterval=0
RestartSec=10

[Install]
WantedBy=multi-user.target
```   
4. Reload systemd manager configuration and restart the service
```
sudo systemd daemon-reload
sudo systemd restart kubelet
```
5. Check status of the Kubelet service
`sudo systemd status kubelet`   

## References

* [how to enable kubelet logging verbosity](https://stackoverflow.com/questions/55739315/how-to-enable-kubelet-logging-verbosity)   
* [How to redirect output of systemd service to a file](https://stackoverflow.com/questions/37585758/how-to-redirect-output-of-systemd-service-to-a-file)
