# ACIT 2420 Assignment 3 Part 1

This is a tutorial that would take a reader from a fresh Arch Linux server running on DigitalOcean to serving the demo document.


## Installation of Vim and Nginx

1. Update your system before installing vim and nginx:

```bash
pacman -Syu
```

2. Install vim and nginx:

```bash
pacman -S vim nginx
```

### Configuration of nginx

1. Create a directory that will act as you project root to store your website document:

```bash
mkdir -p /web/html/nginx-2420
```

2. Go to the directory where nginx configuration file is located:

```bash
cd /etc/nginx
```

3. Create new directories to store a new server block configuration file for this project and to use for a symbolic link:

```bash
mkdir sites-available & mkdir sites-enabled

```

4. Create the new server block in cofiguration file which should be stored in the sites-available directory and open the file using vim:

```bash
vim sites-available/nginx-2420.conf
```

5. Add the server block:

```
server {     
    listen 80;     
    server_name your_server_ip;      
    root /web/html/nginx-2420;     
    index index.html;      
    location / { try_files $uri $uri/ =404; 
    } 
}
```
Don't forget using the `:wq` command to save what you wrote!!! 

6. Create a symbolic link to enable the server block:

The symbolic link will allow nginx to include the new server block you created at step 5 for your project 

```bash
ln -s /etc/nginx/sites-available/nginx-2420 /etc/nginx/sites-enabled/
```

#### Systemd components 

1. Test the configuration file:

```bash
sudo nginx -t
```
If there is no error. you should reload nginx to apply changes which are from the configuration.

2. Reload nginx 

```bash
sudo systemctl reload nginx 
```

3. Check the status of nginx

```bash
sudo systemctl status nginx
```

The nginx service should be active at this moment if you follow the tutorial.

#### Demo Document

1. Create a html file for the demo page

```bash
vim /web/html/nginx-2420/index.html
```

2. Write a html document and save it

Sample demo html document
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>2420</title>
    <style>
        * {
            color: #db4b4b;
            background: #16161e;
        }
        body {
            display: flex;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
        }
        h1 {
            text-align: center;
            font-family: sans-serif;
        }
    </style>
</head>
<body>
    <h1>All your base are belong to us</h1>
</body>
</html>
```

3. Check the demo page by entering your-server-ip that you wrote in the configuration file.

Everything is done!!