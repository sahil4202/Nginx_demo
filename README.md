Project Link https://roadmap.sh/projects/static-site-server

2.1 Install Nginx
bash
Copy
Edit
sudo apt update && sudo apt install nginx -y
2.2 Start & Enable Nginx
bash
Copy
Edit
sudo systemctl start nginx
sudo systemctl enable nginx
2.3 Verify Installation
Open http://<EC2-Public-IP> in a browser. You should see the Nginx welcome page.

3️⃣ Deploy a Static Website
3.1 Create the Web Directory
bash
Copy
Edit
sudo mkdir -p /var/www/mysite
sudo chown -R ubuntu:ubuntu /var/www/mysite
3.2 Create a Simple Web Page
Create an HTML file:

bash
Copy
Edit
nano /var/www/mysite/index.html
Add the following content:

html
Copy
Edit
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>My Static Site</title>
    <style>body { text-align: center; font-family: Arial; }</style>
</head>
<body>
    <h1>Welcome to My Static Site!</h1>
    <p>Powered by Nginx on AWS</p>
</body>
</html>
3.3 Configure Nginx
Create a new Nginx config file:

bash
Copy
Edit
sudo nano /etc/nginx/sites-available/mysite
Add:

nginx
Copy
Edit
server {
    listen 80;
    server_name _;
    root /var/www/mysite;
    index index.html;

    location / {
        try_files $uri $uri/ =404;
    }
}
Enable the site and restart Nginx:

bash
Copy
Edit
sudo ln -s /etc/nginx/sites-available/mysite /etc/nginx/sites-enabled/
sudo nginx -t  # Check for errors
sudo systemctl restart nginx
Visit http://<EC2-Public-IP> to see your site.

4️⃣ Automate Deployment with Rsync
4.1 Install Rsync Locally
Ubuntu/Linux:

bash
Copy
Edit
sudo apt install rsync -y
MacOS:

bash
Copy
Edit
brew install rsync
4.2 Create a Deployment Script
On your local machine, create deploy.sh:

bash
Copy
Edit
#!/bin/bash

# Sync files to remote server
rsync -avz -e "ssh -i ~/path/to/aws-key.pem" ./mysite/ ubuntu@<EC2-Public-IP>:/var/www/mysite/

# Restart Nginx on the remote server
ssh -i ~/path/to/aws-key.pem ubuntu@<EC2-Public-IP> "sudo systemctl restart nginx"
Make it executable:

bash
Copy
Edit
chmod +x deploy.sh
Run the script to deploy updates:

bash
Copy
Edit
./deploy.sh
5️⃣ (Optional) Configure a Domain
Buy a domain (e.g., from Namecheap or GoDaddy).

Add an A record pointing to your EC2 public IP.

Edit the Nginx config:

nginx
Copy
Edit
server {
    listen 80;
    server_name mydomain.com www.mydomain.com;
    root /var/www/mysite;
    index index.html;
}
Restart Nginx:

bash
Copy
Edit
sudo systemctl restart nginx
Visit http://mydomain.com.
