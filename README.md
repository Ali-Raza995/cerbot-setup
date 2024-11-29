# Certbot SSL Setup for Node.js Application

This repository contains the setup and documentation for configuring SSL certificates using Certbot for a Node.js application. Certbot automates the process of obtaining and renewing SSL certificates from Let’s Encrypt, ensuring that your application is securely accessible over HTTPS.

## Contents
- [Certbot SSL Setup Overview](#certbot-ssl-setup-overview)
- [Setup Steps](#setup-steps)
- [Configuring Certbot](#configuring-certbot)
- [Automating Certificate Renewal](#automating-certificate-renewal)
- [Deploying to Production](#deploying-to-production)
- [Testing SSL Configuration](#testing-ssl-configuration)

## Certbot SSL Setup Overview
Certbot is a tool that automatically helps you configure SSL certificates from Let’s Encrypt, a free certificate authority. With Certbot, you can ensure that your application runs over HTTPS with a valid, secure SSL certificate.

This setup includes:

1. **Installing Certbot**: Installing Certbot on the server where your Node.js application is hosted.
2. **Generating SSL Certificates**: Generating SSL certificates using Certbot and Let’s Encrypt.
3. **Renewal Setup**: Setting up automatic renewal of certificates so they remain valid without manual intervention.

## Setup Steps
Follow these steps to set up SSL certificates for your Node.js application using Certbot:

### 1. **Install Certbot on your server**:
   Ensure that your server has Certbot installed. This can typically be done with a package manager (e.g., `apt` for Ubuntu).
   
   ```bash
   sudo apt update
   sudo apt install certbot
2. Install Nginx Plugin for Certbot:
If you're using Nginx to serve your Node.js application, you’ll also need the Certbot Nginx plugin to automate the SSL configuration.

bash
Copy code
sudo apt install python3-certbot-nginx
3. Obtain SSL Certificate for Your Domain:
Run the following command to request an SSL certificate for your domain. Replace your-domain.com with your actual domain name.

bash
Copy code
sudo certbot --nginx -d your-domain.com -d www.your-domain.com
This command will:

Generate a free SSL certificate from Let’s Encrypt.
Automatically configure your Nginx server to use HTTPS.
4. Verify HTTPS Access:
After obtaining the certificate, verify that your Node.js application is accessible securely via HTTPS by visiting https://your-domain.com in a browser.

5. Test Nginx Configuration:
Ensure your Nginx configuration is correct after Certbot’s changes:

bash
Copy code
sudo nginx -t
6. Reload Nginx:
Reload the Nginx service to apply changes:

bash
Copy code
sudo systemctl reload nginx
Configuring Certbot
Certbot works by generating and renewing SSL certificates for your domain. Here are a few essential configurations:

1. HTTP to HTTPS Redirection:
Certbot automatically configures your Nginx to redirect all HTTP traffic to HTTPS. Ensure that your Nginx server has the following HTTP configuration:

nginx
Copy code
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    
    # Redirect all HTTP traffic to HTTPS
    return 301 https://$host$request_uri;
}
2. SSL Configuration:
After running Certbot, your Nginx configuration will include SSL settings pointing to the certificate files created by Certbot:

nginx
Copy code
server {
    listen 443 ssl;
    server_name your-domain.com www.your-domain.com;

    # SSL certificate paths
    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # Other SSL configurations
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';

    root /var/www/html;
    index index.html;
}
3. Other Nginx Configurations:
You may also include other configurations like a www redirection to ensure traffic to www.your-domain.com is properly handled. Here's an example:

nginx
Copy code
server {
    listen 443 ssl;
    server_name www.your-domain.com;
    
    # SSL certificate paths
    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # Redirect www to non-www
    return 301 https://your-domain.com$request_uri;
}
Automating Certificate Renewal
Let’s Encrypt certificates expire every 90 days. Certbot includes a built-in mechanism to automatically renew certificates before they expire.

1. Setting Up Auto-Renewal:
To automate the renewal process, create a cron job that runs the certbot renew command periodically:

bash
Copy code
sudo crontab -e
Add the following cron job to check and renew the certificate twice a day:

bash
Copy code
0 0,12 * * * certbot renew --quiet && systemctl reload nginx
This will:

Run certbot renew at midnight and noon every day.
Reload Nginx if a certificate renewal occurs.
2. Verifying Auto-Renewal:
To verify that the auto-renewal process works, you can perform a dry-run of the renewal process:

bash
Copy code
sudo certbot renew --dry-run
If there are no errors, your certificate will be renewed automatically.

Deploying to Production
Once the SSL setup is complete, ensure that your application is correctly deployed to production:

Verify Server Ports: Ensure that Nginx is listening on ports 80 and 443.
Firewall Configuration: Make sure that your server's firewall allows traffic on ports 80 and 443.
