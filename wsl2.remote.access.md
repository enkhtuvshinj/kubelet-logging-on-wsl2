## Connecting to WSL2 from devices in LAN

Generally, all connections from WSL2 instance to any devices in same network are open. However, connections from devices in the same LAN is blocked by default. 

1. WSL2 has a virtualized ethernet adapter with its own unique IP address that is not in IP range of the LAN. In order to assign IP address from IP range of the LAN, enable the mirrored mode networking for Linux instance on WSL2 by adding `networkingMode=mirrored` into `.wslconfig` file that exists in `C:\Users\<user name>` folder. If this file doesn't exist, create it and add `networkingMode=mirrored`.  
2. Shutdown the WSL2 instance and re-launch it from PowerShell terminal   
`wsl -l -v`  
`wsl --terminate <instance name>`  
`wsl -d <instance name>`  
3. Now we should find out what port the application that you want to open a remote access runs on. Run the following command in terminal of Linux instance on WSL2 and note down the port numbers for the intended application.   
`sudo lsof -i -P -n | grep LISTEN`   
4. Enable Windows port forwarding from PowerShell terminal   
`netsh interface portproxy add v4tov4 listenaddress=<windows_local_ip> listenport=<listen_port_on_windows> connectaddress=<wsl_internal_ip>   connectport=<service_port>`   
5. Enable Inbound rules for the given port in Windows firewall   
`netsh advfirewall firewall add rule name="<Any name>" dir=in action=allow protocol=TCP localport=<listen_port_on_windows>`   

### Handy netsh commands
* Show all configured portproxy interfaces:   
`netsh interface portproxy show all`   
* Delete the existing portproxy interface:   
`netsh interface portproxy delete v4tov4 listenport=<listenning_port> listenaddress=<listenning_ip>`   

### References

* [Accessing a WSL 2 distribution from your local area network (LAN)](https://learn.microsoft.com/en-us/windows/wsl/networking#accessing-a-wsl-2-distribution-from-your-local-area-network-lan)
* [WSL 2 Setup for SSH Remote Access](https://medium.com/@wuzhenquan/windows-and-wsl-2-setup-for-ssh-remote-access-013955b2f421)   
* [Netsh interface portproxy commands](https://learn.microsoft.com/en-us/windows-server/networking/technologies/netsh/netsh-interface-portproxy)