# Setup Ubuntu Server with Nginx, Node.js, PM2, and Stress

This guide provides a step-by-step setup of Nginx, Node.js, PM2, and Stress on an Ubuntu server.

## 1. Update and Upgrade the System

```bash
sudo apt update && sudo apt upgrade -y

## 2. Install and Setup Nginx

sudo apt-get install nginx -y

Enable Nginx to start on boot and check its status:

sudo systemctl enable nginx
sudo systemctl status nginx

## 3. Configure Nginx
Navigate to the Nginx sites-available directory:
cd /etc/nginx/sites-available/

Copy the default configuration:
sudo cp default server

Edit the server file:

sudo vi server

Delete all the contents of the file:

:%d

Then, add the following configuration:

server {
    listen 80;
    listen [::]:80;
    root /home/azureuser/apps/FE/build;

    index index.html index.htm index.nginx-debian.html;
    server_name 172.191.96.129;

    location / {
        try_files $uri /index.html;
    }

    location /api {
        proxy_pass http://172.191.96.129:3001;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Save and exit.

Create a symbolic link to enable the new configuration:

Save and exit.

Create a symbolic link to enable the new configuration:

sudo ln -s /etc/nginx/sites-available/server /etc/nginx/sites-enabled/

Restart Nginx to apply the changes:

sudo service nginx restart
sudo systemctl status nginx

## 4. Install Node Version Manager (NVM) and Node.js

Install NVM:

curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.3/install.sh | bash

Load NVM:

export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
[ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"

nvm install 14.17

## 5. Install NPM and PM2

npm install -g npm@6.14.18

5. Install NPM and PM2

pm2 start server.js
pm2 startup
pm2 save


8. Backend Express Server Code Example
Here is an example of the server.js for the backend:

import express from "express";
import bodyParser from "body-parser";
import viewEngine from "./config/viewEngine";
import initWebRoutes from "./route/web";
import connectDB from "./config/connectDB";
import cors from "cors";
require("dotenv").config();

let app = express();
app.use(cors());

// Add headers before the routes are defined
app.use(function (req, res, next) {
    res.setHeader("Access-Control-Allow-Origin", process.env.URL_REACT);
    res.setHeader("Access-Control-Allow-Methods", "GET, POST, OPTIONS, PUT, PATCH, DELETE");
    res.setHeader("Access-Control-Allow-Headers", "X-Requested-With,content-type");
    res.setHeader("Access-Control-Allow-Credentials", true);
    next();
});

// Config app
app.use(bodyParser.json({ limit: "50mb" }));
app.use(bodyParser.urlencoded({ limit: "50mb", extended: true }));

viewEngine(app);
initWebRoutes(app);
connectDB();

let port = process.env.PORT || 3001;
app.listen(port, () => {
    console.log("Backend Nodejs is running on the port : " + port);
});

9. Set Permissions for the Frontend Directory

Set the correct permissions for the frontend files:

sudo chown -R www-data:www-data /home/azureuser/apps/FE/build
sudo chmod -R 755 /home/azureuser/apps/FE/build

Set the correct permissions for the user's home directory:
sudo chmod 755 /home/azureuser

## 10. Install and Run Stress for Load Testing

Install the stress tool:

sudo apt-get install stress -y

Run a stress test with 30 CPU workers for 10 minutes:

stress --cpu 30 --timeout 600

## 11. Restart the Backend with PM2

Start the backend again using PM2 and save the process state:

pm2 start server.js
pm2 startup
pm2 save


