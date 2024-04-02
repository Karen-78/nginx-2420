Start your Arch Linux Droplet. 

1. Install Vim. This will be the text editor used for this project. 
``` 
sudo pacman -S vim
```

2. Install Nginx.
``` 
sudo pacman -S nginx
```

3. Start nginx service. Installing it does not mean it is running. 
``` 
sudo systemctl start nginx
```

4. Check nginx status to see if it's running. If it's active and running, you should see "active (running)" next to the "Active" status. 
```
systemctl status nginx
```

5. Create the directory /web/html/nginx-2420 for this project. This will be where your website documents will be stored. There is no strict default for where this website documents are stored, so this is where we will store it for this project. Make sure to use sudo because it's in the root directory. 
```
sudo mkdir -p /web/html/nginx-2420
```

6. Create the HTML file for your web page inside this directory. Make sure to use sudo vim so you can edit the file. 
```
sudo vim /web/html/nginx-2420/index.html
```

7. Paste this HTML code into that index.html. The use :wq to write to and quit the vim file. 

```
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

8. Setup a separate server block "nginx-2420" for your new server. It should not be part of the nginx.conf file because you want to decouple the nginx configuration and your server configuration. Create these directories first: 
```
sudo mkdir -p /etc/nginx/sites-available
```
and
```
sudo mkdir -p /etc/nginx/sites-enabled
```
The sites-available and sites-enabled directories should be created inside /etc/nginx directory so nginx is able to locate it, and it will follow conventions and best-practices. The sites-available directory will contain the configuration for all the sites you have on your system. The sites-enabled directory will contain symbolic links to files in your sites-available directory that you actually want to enable and turn on. 


Then create a separate server block "nginx-2420" by using:
``` 
sudo vim /etc/nginx/sites-available/nginx-2420.conf
```
And paste this into it: 
```
server {
    listen 80; 
    listen localhost;
    
    location / {
        root /web/html/nginx-2420;
        index index.html;
    }
}
```
The "root /web/html/nginx-2420" is telling nginx which directory to find HTML pages. The "index index.html" is telling nginx which HTML file to use for the index page. 

9. Check your nginx configuration file syntax with: 
```
sudo nginx -t
```
It will let you know if there are errors. 

10. To enable a site (make it actually available), create a symbolic link to the sites-enabled directory from the sites-available directory. Make sure to use sudo because this is dealing with root directories. 
```
sudo ln -s /etc/nginx/sites-available/nginx-2420.conf /etc/nginx/sites-enabled/nginx-2420.conf
```

11. Edit nginx configuration file. 
```
sudo vim /etc/nginx/nginx.conf
```

At the end of the http block, append: include sites-enabled/*;
```
http {
    ...
    include sites-enabled/*;
}
```


12. Restart nginx to load the new configuration.
```
sudo systemctl restart nginx
```

13. Check your site by entering your DigitalOcean Droplet's IP address into your web browser. There is a screenshot of what your screen should look like. This is the rendered version of the HTML file in sudo vim /web/html/nginx-2420/index.html