# ACIT2420 Assignment3 Part 2

This is a tutorial I'll guide you through the process of adding a firewall using UFW (Uncomplicated Firewall) and a reverse proxy server using Nginx to your backend application. This tutorial assumes you have already set up your backend application and have root access to your server.

## Installation of UFW

UFW is a program that makes it a little easier to manage a [netfilter](https://netfilter.org/) firewall. UFW actually manages nftables or iptables, which make use of netfilter which is part of the Linux kernel.

Before install, update your system
```bash
    sudo pacman -Syu
```
To install:
```bash
    sudo pacman -S ufw
```
To start and enable the service:
```bash
    sudo systemctl enable --now ufw.service
```

## Configuration of UFW

I will configure the UFW rules to set the default behavior for incoming and outgoing traffic. SSH and HTTP traffics are allowed to enable remote access to the server and web traffic. 

To check the status of UFW before configuration:
```bash
    sudo ufw status verbose
```

To set the default ufw:
```bash
    sudo ufw default deny incoming
    sudo ufw default allow outgoing
```

To configure ufw we can use _allow_, _deny_ and _limit_ commands:
```bash
    sudo ufw allow <ip address>         
    sudo ufw deny  <Ip address>

    sudo ufw allow ssh                # allow 22 Our SSH
    sudo ufw deny ssh

    sudo ufw allow <port number>      # allow 8080 Our server 
    sudo ufwd deny <port number>

    sudo ufw allow http
    sudo ufw deny http

    sudo ufw limit <any method>
```
Note: you can choose the method to allow, deny, and limit for the server.

To turn on the firewall:
```bash
    sudo ufw enable 
```
### Backned Setup

1. Place Backend Binary
You will use a backend binary file to the server using sftp by trasfering the file.

To transfer the backend binary file to the server:
```bash
    sftp ssh_name
    or
    sftp username@your_ip_address
```

To navigate to the directory where you want to transfer the binary file:
```bash
    put /path/filename             #usr/local/bin   
```
Note: the path /usr/local/bin can be good directory to store the binary file.

2. Create Systemd UFW Service File
A systemd service file is created to define how the backend application should be managed as a service by systemd. It specifies the command to start the backend, defines restart behavior, and sets dependencies.

To create a new systemd service file for the backend:
```bash
    sudo vim /etc/systemd/system/backend.service
```

Now add some content for the configuration file.
```vim
    [Unit]
    Description=Backend Service
    After=network.target

    [Service]
    ExecStart=/opt/backend
    Restart=always
    RestartSec=3

    [Install]
    WantedBy=multi-user.target
```

3. Start and Enable the UFW Service

To start and enable the service:
```bash
    sudo systemctl deamon-reload
    sudo systemctl start backend
    sudo systemctl enable backend
```

### Configuration a reverse proxy server with nginx

The [proxy_pass](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_pass) directive is used to set the address of a proxied server in an nginx server block.

1. Edit nginx Server Block 

To open nginx server block configuration:
```bash
    sudo vim /etc/nginx/sites-available/nginx-2420.conf
```

2. Add configuration content in the server block
```vim
server {
    listen 80;
    server_name your_domain.com;
    root your_root_html_file;           # /web/html/nginx-2420

    location / {
        try_files $uri $uri/ =404;
        index index.html index.htm;
    }

    location /hey {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /ech {
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### Testing 

1. Test nginx configuration and restart it
```bash
    sudo nginx -t
    sudo systemctl restart nginx
```

2. Test connection to the Backend

To test the backend, you can use curl commands or a tool like postman, httpie, etc

If you use curl commands:

This will request for getting the HTTP.
```bash
    curl http://<ip address> 
```

This will request for getting the HTTP with the endpoint.
```bash
    curl http://<ip address>/<endpoint>
```

This will request for posting the HTTP with the meassage to the path which is the endpoint of the server.
```bash
    curl -X POST -H "Content-Type: application/json" -d '{"message": ""}' http://<ip address>/<path>
```

