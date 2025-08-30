Sure! Here's a properly formatted `README.md` file based on the text you provided:

````markdown
# Jellyfin Movie Server with Nginx Reverse Proxy

This project provides a detailed guide on setting up a **Jellyfin Movie Server** using **Docker** and **Nginx** for reverse proxying. It also explains how to expose Jellyfin to the internet and configure HTTPS using **Certbot**.

## Prerequisites

Before you begin, make sure you have the following:

- A server running **Ubuntu** (or a similar Linux-based OS).
- **Docker** and **Docker Compose** installed on your server.
- A domain (e.g., `bokha.shop`) pointing to your server's IP address (set up DNS).
- **Nginx** installed on your server to serve as a reverse proxy.
- **Certbot** installed for SSL certificate management.

## Steps to Set Up Jellyfin Server

### 1. **Clone the Repository**

Clone this repository to your server:

```bash
git clone https://github.com/yourusername/jellyfin-movie-server.git
cd jellyfin-movie-server
````

### 2. **Start Jellyfin with Docker Compose**

Run the following command to start Jellyfin using Docker Compose:

```bash
docker-compose up -d
```

This will download the necessary Docker image and start the container in detached mode. Once started, Jellyfin will be accessible at `http://localhost:8096`.

### 3. **Configure Nginx as a Reverse Proxy**

#### 3.1 **Install Nginx**

If you don't have Nginx installed, run the following command:

```bash
sudo apt update
sudo apt install nginx
```

#### 3.2 **Create Nginx Configuration File**

Create a new configuration file for your domain in `/etc/nginx/sites-available/example.com`:

```bash
sudo nano /etc/nginx/sites-available/example.com
```

Paste the following code into the file (adjust the domain and paths as needed):

```nginx
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$host$request_uri;  # HTTP to HTTPS redirection
}

server {
    listen 443 ssl;
    server_name example.com www.example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # Managed by Certbot
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # Managed by Certbot
    include /etc/letsencrypt/options-ssl-nginx.conf; # Managed by Certbot
    ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # Managed by Certbot

    location / {
        proxy_pass http://localhost:8096;  # Forward traffic to Jellyfin on port 8096
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    error_log /var/log/nginx/example.com_error.log;
    access_log /var/log/nginx/example.com_access.log;
}
```

#### 3.3 **Enable the Nginx Configuration**

Create a symlink in the `sites-enabled` directory to enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/example.com /etc/nginx/sites-enabled/
```

#### 3.4 **Test Nginx Configuration**

Make sure your Nginx configuration is valid by running:

```bash
sudo nginx -t
```

If everything is good, restart Nginx to apply the changes:

```bash
sudo systemctl restart nginx
```

### 4. **Set Up SSL with Certbot**

To secure your site with HTTPS, use **Certbot** to obtain an SSL certificate:

1. Install Certbot and the Nginx plugin:

   ```bash
   sudo apt install certbot python3-certbot-nginx
   ```

2. Obtain an SSL certificate and configure Nginx for HTTPS:

   ```bash
   sudo certbot --nginx -d example.com -d www.example.com
   ```

3. Follow the prompts and Certbot will automatically configure your Nginx for HTTPS.

### 5. **Access Your Jellyfin Server**

Once everything is set up, you should be able to access your Jellyfin server at `https://example.com`. Nginx will proxy the requests to the Jellyfin Docker container running on port `8096`.

### Notes:

1. **docker-compose.yml**: Make sure the file is included in your repository and properly configured.
2. **Nginx Configuration**: The Nginx configuration is set up to proxy requests to Jellyfin, along with SSL configuration using Certbot.
3. **Customization**: Feel free to adjust paths, domain names, and configurations as needed for your specific environment.

---

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```

### Key Details:
- **Clone the Repository**: Instructions for cloning the GitHub repo and navigating into the project directory.
- **Docker Compose Setup**: How to run Jellyfin using Docker Compose.
- **Nginx Configuration**: Full steps to set up Nginx as a reverse proxy, including SSL setup with Certbot.
- **Customization**: Notes to customize domain and configuration as needed.

Feel free to replace placeholders (like `example.com`) with your actual domain and adjust any paths if necessary!
```
