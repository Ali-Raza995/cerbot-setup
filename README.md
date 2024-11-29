<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

    <h1>Certbot SSL Setup</h1>

    <div class="section">
        <h2>Contents</h2>
        <ul>
            <li><a href="#installation-guide">Installation and Configuration Guide</a></li>
            <li><a href="#nginx-configuration">Example Nginx Configuration</a></li>
            <li><a href="#auto-renewal">Setting Up Auto-Renewal</a></li>
            <li><a href="#troubleshooting">Troubleshooting</a></li>
        </ul>
    </div>

    <div class="section" id="installation-guide">
        <h2>Installation and Configuration Guide</h2>
        <p>To set up SSL certificates for your domain, follow the steps below:</p>

        <h3>Step 1: Install Certbot</h3>
        <pre><code>sudo apt update
sudo apt install certbot python3-certbot-nginx</code></pre>

        <h3>Step 2: Obtain SSL Certificate for Your Domain</h3>
        <p>Run the following command to obtain an SSL certificate and automatically configure Nginx:</p>
        <pre><code>sudo certbot --nginx -d your-domain.com -d www.your-domain.com</code></pre>
        <p>This will:</p>
        <ul>
            <li>Obtain an SSL certificate.</li>
            <li>Automatically configure Nginx to serve content over HTTPS.</li>
        </ul>

        <h3>Step 3: Verify HTTPS Access</h3>
        <p>After the installation, navigate to <code>https://your-domain.com</code> to verify that the site is being served over HTTPS.</p>

        <h3>Step 4: Test Nginx Configuration</h3>
        <pre><code>sudo nginx -t</code></pre>

        <h3>Step 5: Reload Nginx</h3>
        <pre><code>sudo systemctl reload nginx</code></pre>
    </div>

    <div class="section" id="nginx-configuration">
        <h2>Example Nginx Configuration</h2>
        <h3>HTTP Configuration (default.conf)</h3>
        <p>The <code>default.conf</code> file should handle HTTP traffic on port 80, redirecting users to HTTPS:</p>
        <pre><code>server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    # Redirect all HTTP traffic to HTTPS
    return 301 https://$host$request_uri;
}</code></pre>

        <h3>SSL Configuration (ssl.conf)</h3>
        <p>The <code>ssl.conf</code> file should be used to handle HTTPS traffic on port 443:</p>
        <pre><code>server {
    listen 443 ssl;
    server_name your-domain.com www.your-domain.com;

    # SSL certificate paths
    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # Additional SSL settings (can be customized)
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';

    # Define the root directory
    root /var/www/html;
    index index.html;
}</code></pre>
    </div>

    <div class="section" id="auto-renewal">
        <h2>Setting Up Auto-Renewal</h2>
        <p>Certbot certificates are valid for 90 days. To ensure that your SSL certificate is automatically renewed, set up a cron job:</p>

        <h3>Step 1: Open Crontab</h3>
        <pre><code>sudo crontab -e</code></pre>

        <h3>Step 2: Add the Renewal Command</h3>
        <p>Add the following cron job to check for certificate expiration twice a day and renew if necessary:</p>
        <pre><code>0 0,12 * * * certbot renew --quiet && systemctl reload nginx</code></pre>
        <p>This will:</p>
        <ul>
            <li>Run <code>certbot renew</code> at midnight and noon every day.</li>
            <li>Reload Nginx if a renewal occurs.</li>
        </ul>

        <h3>Step 3: Verify the Cron Job</h3>
        <p>To verify that the cron job is running as expected, you can check the system logs or manually renew the certificate:</p>
        <pre><code>sudo certbot renew --dry-run</code></pre>
    </div>

    <div class="section" id="troubleshooting">
        <h2>Troubleshooting</h2>
        <h3>Common Issues</h3>
        <div class="note">
            <p><strong>Certbot fails to obtain a certificate:</strong> Ensure that your DNS is pointing to the correct server, and your firewall allows traffic on ports 80 and 443.</p>
        </div>

        <div class="note">
            <p><strong>Nginx not serving HTTPS:</strong> Double-check your Nginx configurations for SSL settings and correct file paths for your certificates.</p>
        </div>
    </div>

    <div class="section">
        <h2>Usage</h2>
        <p>Clone this repository to use the provided Nginx configuration examples and guides:</p>
        <pre><code>git clone https://github.com/your-username/certbot-ssl.git
cd certbot-ssl</code></pre>

        <p>For further details, refer to the official Certbot documentation at <a href="https://certbot.eff.org" target="_blank">https://certbot.eff.org</a>.</p>
    </div>

</body>
</html>
