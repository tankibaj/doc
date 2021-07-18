AutoSSH is a great tool for Linux administrators. SSH is a must-have – we are using it on daily basis. In many cases, it is just enough. The well-designed tool which is doing its job. From time to time, however, we need something more.



AutoSSH is a tool to monitor and restart SSH sessions. It is checking the health of the connection and restarts SSH session if necessary. It is useful for non-reliable internet connections such as GSM or in cases when the connection parameters are changing from time to time (for example ISP is assigning new IP to the server from). We are using it to maintain reliable tunnel between our remote servers and main monitoring server.

Remote servers are located in the places where no static IPs are assigned and there is no possibility to open ports on the router. In this case, the connection has to be initiated from the remote host. We are simply creating the tunnel which is forwarding all connections to the port NNNN of monitoring host to the port 22 (SSH) of the remote host. In some cases, we are doing the same for the port 80 (HTTP) of the remote host if we want to forward web server data.





## Getting Started

<br/>

- #### Generate SSH key on both host

  ```bash
  ssh-keygen -t rsa -b 4096 -C 'hostname'
  ```

  ```bash
  cat .ssh/id_rsa.pub
  ```

  

- #### Change SSH config on VPS

  ```
  sudo vim /etc/ssh/sshd_config
  ```

  ```
  AllowTcpForwarding yes
  GatewayPorts yes
  X11Forwarding yes
  AllowAgentForwarding yes
  ```

  

- #### Install AutoSSH Package

  ```bash
  sudo apt install autossh
  ```

  

- #### AutoSSH remote/reverse port forwarding

  ```bash
  autossh -N -R 8881:127.0.0.1:22 naim@tunnel.naim.run
  ```

  **Explanation:**

  `-f` - Requests autossh to go to background just before command execution.

  `-N` - Do not execute a remote command.  This is useful for just forwarding ports.

  `-R` - Remote port forwarding.

  `8881` - Remote host port.

  `127.0.0.1:22` - Local host IP:Port.

  `naim@192.168.0.100` - Remote host User@IP.



- #### Test SSH Tunnel

  ```bash
  ssh naim@192.168.0.100 -p 8881
  ```

  Or

  ```bash
  ssh naim@localhost -p 8881
  ```



- #### Autossh Tunnel Daemon

  Nowadays the most convenient way to run autossh on boot time is to use **systemd** service. Let’s create the file named **autossh-tunnel.service** in **/etc/systemd/system/**

  ```bash
  sudo touch /etc/systemd/system/autossh-tunnel.service
  ```

  Paste this code into it:

  ```bash
  [Unit]
  Description=AutoSSH tunnel service remote port 8881 to local 22
  After=network.target network-online.target sshd.service
  
  [Service]
  User=naim
  Environment="AUTOSSH_GATETIME=0"
  ExecStart=/usr/bin/autossh -o "ServerAliveInterval 10" -o "ServerAliveCountMax 3" -NR 8881:127.0.0.1:22 naim@tunnel.naim.run
  RestartSec=5
  Restart=always
  ExecStop=killall -s KILL autossh
  
  [Install]
  WantedBy=multi-user.target
  ```

  **Explanation:**

  `After` - This daemon will start after following service `network.target` `network-online.target` `sshd.service`.

  `User` - Specify the user who has SSH key  to authenticate remote host.

  `Environment variable ` `AUTOSSH_GATETIME` - Systemd does not support **&** or **-f** option to run the service in the background. The environmental variable tells autossh not to wait for the first connection to succeed, but immediately go to background and restart even if this first connection will fail.

  `ServerAliveInterval` - Tells SSH to test the connection every 10 seconds.

  `ServerAliveCountMax` - Assume failure after 3 consecutive failed messages. Such configuration ensures a quick recovery after the connection failure.

  

  Once we have the service file created, we should let systemd know that we changed something, we can start the service and we can enable it to run during the boot time:

  ```bash
  sudo systemctl daemon-reload
  sudo systemctl start autossh-tunnel.service
  sudo systemctl enable autossh-tunnel.service
  sudo systemctl status autossh-tunnel.service
  ```

  

- #### What else can I do?

  You can also create the tunnel which is working the other way. In some cases, we are using it to forward remote port 80 to the local port 8080 to forward some API calls. The only change you have to do is to change the part of the configuration. Such tunnel can be created like so:

  ```bash
  remote.host$ autossh -N -R 8883:localhost:80 naim@tunnel.naim.run
  ```

  Now, on the remote host, you can access monitoring.com HTTP by opening http://localhost:80/ OR http://192.168.0.100:80/ the request will be forwarded through the tunnel to the monitoring host.