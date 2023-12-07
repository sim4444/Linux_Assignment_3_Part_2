# as3p2-starter-f23

You will have to edit some of these files to get your web servers working.

The included backend server runs on port 8080, 127.0.0.1:8080

## Included material

- backend binary, hello-server
- frontend, index.html
- nginx configuration file, hello.conf
- service file for backend, hello-server.service
- config for setting up servers, cloud-config.yml
- example curl commands for testing your server, curl.md
# linux_part2_assignment_3

To begin with, I created 2 droplets(named - web 1 and web2) with regular user(web) on digital ocean. We will setup these servers which are made by creating droplets web1 and web2. The regular user is able to connect to the server via ssh, have bash set as their login shell and is member of the sudo group. Below are the steps to augment my web server by incorporating various components such as a backend and a load balancer: 

- I cloned the provided files into directory of my host machine(my laptop).

## Setting up a server with a cloud-config script:
- When I edited file "cloud-config.yaml", I made name and primary_group as "web", added my public SSH key, shell is /bin/bash to make sure that regular user "web' has bash set as their login shell. Groups is sudo to make web user a member of the sudo group.

The below command in cloud-config.yaml with PermitRootLogin no in the /etc/ssh/sshd_config file disables root access via SSH:
``` bash
    sed -i -e '/^PermitRootLogin/s/^.*$/PermitRootLogin no/' /etc/ssh/sshd_config
```

The below command in cloud-config.yaml restarts the SSH service and apply any modifications made to the configuration:
``` bash
    systemctl restart ssh
```

The below command in cloud-config.yaml restarts the systemd-journald service to manage system logs.
``` bash
    systemctl restart systemd-journald
```
## Firewall, UFW(uncomplicated firewall):
-  I uploaded the required files to the web droplets(or servers here) web1 and web2 using sftp(Secure File Transfer Protocol) in my laptop's terminal.
-  I installed nginx and ufw with commands below:
``` bash
    sudo apt update
    sudo apt install nginx
    sudo apt install ufw
```
- I ran below commands to enable firewall on both droplets web1 and web2. Firstly SSh is allowed and then ufw is enabled and then checked status for confirmation. These below commands should be run in order:
``` bash
    sudo ufw allow SSH
    sudo ufw allow http
    sudo ufw enable
    sudo ufw status verbose
```
## Creating a reverse proxy server with nginx:
