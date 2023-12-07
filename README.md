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

To begin with, I created 2 servers(named - web 1 and web2) with regular user(web) on digital ocean. The regular user is able to connect to the server via ssh, have bash set as their login shell and is member of the sudo group. 

When I edited file "cloud-config.yaml", I made name and primary_group as "web", added my public SSH key, shell is /bin/bash making sure that regular user "web' has bash set as their login shell. The 2nd command with PermitRootLogin no in the /etc/ssh/sshd_config file disables root access via SSH.



I cloned the provided files into directory of my host machine(my laptop). First thing I did is to upload those files to the web servers.
