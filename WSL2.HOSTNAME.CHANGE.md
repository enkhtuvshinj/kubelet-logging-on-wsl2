## Change a hostname of WSL2 Instance

1. Open `/etc/wsl.conf` or create the same if it does not exist using the nano text editor and save/close:   
`sudo nano /etc/wsl.conf`   
2. Add following lines in `/etc/wsl.conf` and save/close:   
```
[network]
hostname = <new hostname>
generateHosts = false
```   
3. Edit `/etc/hosts` file using the nano editor    
`sudo nano /etc/hosts`   
4. Change all old names found in the `/etc/hosts` by new hostname. Save and close the file.   
5. Shutdown the WSL2 instance and re-launch it from PowerShell terminal   
`wsl -l -v`  
`wsl --terminate <instance name>`  
`wsl -d <instance name>`  

### References

* [How to Change Hostname of the Ubuntu Instance in WSL](https://medium.com/@bonguides25/how-to-change-hostname-of-the-ubuntu-instance-in-wsl-93746998ed26)