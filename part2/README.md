This is following the creation of the server in Assignment 3.1
*Note: I accidentally updated by README.md from part one when uploading this. 

You can check your ufw firewall to ensure it is allowing ssh and http connections. 
```
sudo ufw status
```

It will show something like: 

To                         Action      From
--                         ------      ----
22                         LIMIT       Anywhere
22                         ALLOW       203.0.113.0/24
80                         ALLOW       Anywhere
22 (v6)                    LIMIT       Anywhere (v6)
80 (v6)                    ALLOW       Anywhere (v6)

And you can add or delete rules as necessary.

Steps to set up reverse proxy with the hello-server binary file: 

1. Upload hello-server using sftp
```
sftp <ip-address> /path/to/hello-server-file
```

2. Move hello-server to $HOME/bin directory. This is where a user's executable files usually are located. 
```
mv source/location $HOME/bin
```

3. Give executable permissions to the hello-server file
```
sudo chmod +x $HOME/bin/hello-server
```

4. Make the service file for hello-server.
```
sudo vim /etc/systemd/system/hello-server.service
```
Paste this in: 
```
[Unit]
Description=hello-server backend service
AssertPathExists=/home/karen/bin/hello-server

[Service]
Type=simple
ExecStart=/home/karen/bin/hello-server

[Install]
WantedBy=multi-user.target
```
Notes: 
"ExecStart=" gives the full path of the file to be executed to start the process. 


5. Enable the service
```
sudo systemctl enable hello-server.service
```

6. Start the service
```
sudo systemctl start hello-server.service
```

7. Check status of the service
```
systemctl status hello-server.service
```
It should say it is active and enabled. 

8. Configure the server
```
sudo vim /etc/nginx/sites-available/nginx-2420
```
Paste this in: 
```
server {
    listen 80;
    listen [::]:80;

    root /web/html/nginx-2420;

    index index.html;

    server_name _;

    location / {
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        try_files $uri $uri/ =404;
    }

    location /hey {
        # Define the reverse proxy settings
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;

    }

    location /echo {
        # Define the reverse proxy settings
        proxy_pass http://127.0.0.1:8080;
        proxy_http_version 1.1;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```
The hello-server file has 2 endpoints: /hey and /echo
Therefore, there should be 2 endpoints in this configuration file. 

9. Check configuration syntax
```
sudo nginx -t
```

10. Restart systemctl to use the new configuration:
```
sudo systemctl restart nginx
```

Then you can check by going to your browser and paste in your ip-address. It should show the home page. Then go to <ip-address>/hey and see the endpoint message. Use POSTMAN with post <ip-address>/echo to see the /echo endpoint. 