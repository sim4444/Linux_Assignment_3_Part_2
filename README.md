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
-  I uploaded the required files to the web servers web1 and web2 using sftp(Secure File Transfer Protocol) in my laptop's terminal.
-  I installed nginx and ufw with commands below:
``` bash
    sudo apt update
    sudo apt install nginx
    sudo apt install ufw
```
- I ran below commands to enable firewall on both servers web1 and web2. Firstly SSh is allowed and then ufw is enabled and then checked status for confirmation. These below commands should be run in order:
``` bash
    sudo ufw allow SSH
    sudo ufw allow http
    sudo ufw enable
    sudo ufw status verbose
```
## Creating frontend Html and Creating a reverse proxy server(for backend) with nginx:
- For frontend; I made directory my_site in directory /var/www. Inside directory path /var/www/my_site/ ; I create file  index.html (frontend). I added below hmtl content in this file:
``` html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
        }
    </style>
</head>
<body>
    <h1>Hello, Galaxy!</h1>
</body>
</html>
```
- I made index.html executable using command:
``` bash
    sudo chmod +x index.html
```
- For backend; I created a proxy server with nginx by adding a new location block and a 'proxy_pass' directive to specify where the traffic is being served from. I edited the configuration file named hello.conf as below:
``` bash
    server {
    listen 80;
    listen [::]:80;

    root /var/www/my_site;

    index index.html;

    server_name _;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /hey {
        # Define the reverse proxy settings
        proxy_pass http://127.0.0.8080/hey;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /echo {
        # Define the reverse proxy settings
        proxy_pass http://127.0.0.8080/echo;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
```
- I had copied the configuration file hello.conf from home directory to /etc/nginx/sites-available/ and made a symbolic link of hello.conf in /etc/nginx/sites-enabled/ using below commands:
``` bash
    sudo cp ./hello.conf /etc/nginx/sites-available/
    sudo ln /etc/nginx/sites-available/hello.conf /etc/nginx/sites-enabled/
```

- I copied the binary file hello-server from home directory into /var/www using command:
``` bash
    sudo cp ./hello-server /var/www
```
-I made directory named hello-server in /var/log to store backend logs here:
```
    mkdir hello-server
```

