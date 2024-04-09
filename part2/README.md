1. Upload hello-server using sftp
```
sftp <ip-address> /path/to/hello-server
```

2. Move hello-server to $HOME/bin directory
```
mv source $HOME/bin
```

3. Give executable permissions to the hello-server file
```
sudo chmod +x $HOME/bin/hello-server
```
4. Make the service file for hello-server 
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

9. Check configuration syntax
```
sudo nginx -t
```

10. Restart systemctl to use the new configuration:
```
sudo systemctl restart nginx
```
