# Jellyfin Movie Server with Nginx Reverse Proxy

This project provides a detailed guide on setting up a **Jellyfin Movie Server** using **Docker** and **Nginx** for reverse proxying. It also explains how to expose Jellyfin to the internet and configure HTTPS using **Certbot**.


<img width="1919" height="1007" alt="Screenshot From 2025-08-30 12-43-45" src="https://github.com/user-attachments/assets/d0a142db-798f-4064-955e-b7f29f7dfe01" />

## Prerequisites

* **Ubuntu 20.04/22.04/24.04/latest** or any other similar Linux-based OS.
* **Docker** & **Docker Compose** installed.
* **Nginx** installed for reverse proxying.
* A registered domain (e.g., `movies.example.com`) pointing to your server's public IP.
* Optionally, **Certbot** for SSL certificate management.

## Steps to Set Up Jellyfin Server

### 1. **Clone the Repository**

Clone this repository to your server:

```bash
git clone https://github.com/devopspradeepyadav/MovieServer.git
cd MovieServer
````

Install docker and docker compose :
```bash
curl https://get.docker.com | bash
sudo usermod -aG docker $USER
newgrp docker
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

Create a new Nginx configuration file for your domain:

```bash
sudo nano /etc/nginx/sites-available/movies.example.com
```

Add the following configuration:

#### **For HTTP Only (No SSL)**

```nginx
server {
    listen 80;
    server_name movies.example.com www.movies.example.com;

    location / {
        proxy_pass http://localhost:8096;  # Forward traffic to Jellyfin on port 8096
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    error_log /var/log/nginx/movies.example.com_error.log;
    access_log /var/log/nginx/movies.example.com_access.log;
}
```

#### **For HTTPS with SSL (Secure Setup)**

```nginx
server {
    listen 80;
    server_name movies.example.com www.movies.example.com;
    return 301 https://$host$request_uri;  # HTTP to HTTPS redirection
}

server {
    listen 443 ssl;
    server_name movies.example.com www.movies.example.com;

    ssl_certificate /etc/nginx/ssl/movies.example.com.crt;
    ssl_certificate_key /etc/nginx/ssl/movies.example.com.key;

    location / {
        proxy_pass http://localhost:8096;  # Forward traffic to Jellyfin on port 8096
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    error_log /var/log/nginx/movies.example.com_error.log;
    access_log /var/log/nginx/movies.example.com_access.log;
}
```

**Notes:**

* Replace `movies.example.com` with your actual domain.
* The `ssl_certificate` and `ssl_certificate_key` should point to your SSL certificate and private key files.
* Make sure to replace `localhost` with the internal Docker IP or `localhost` if you’re directly forwarding from the host.

#### 3.3 **Enable the Nginx Configuration**

Create a symlink in the `sites-enabled` directory to enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/yourmovieserver.com /etc/nginx/sites-enabled/
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
   sudo certbot --nginx -d yourmovieserver.com -d www.yourmovieserver.com
   ```

3. Follow the prompts and Certbot will automatically configure your Nginx for HTTPS.


## **4. Automating SSL Certificate Renewal (If Using Let's Encrypt)**

SSL certificates provided by Let's Encrypt are valid for 90 days. To ensure they automatically renew, set up a cron job:

```bash
sudo crontab -e
```

Add the following line to renew the certificates:

```bash
0 0,12 * * * certbot renew --quiet && systemctl reload nginx
```

This will check for renewals twice daily and reload Nginx to apply any updated certificates.

---

## **5. Testing and Monitoring**

### **Test the Movie Server**

To confirm the server is working:

1. Open a web browser.
2. Navigate to `http://movies.example.com` or `https://movies.example.com` (depending on whether SSL is configured).
3. Log in to Jellyfin, and check that your media libraries and other configurations are intact.
4. Keep the movies under the folder `/home/ubuntu/movies` or the respective directory
<img width="1920" height="928" alt="image" src="https://github.com/user-attachments/assets/fc8fb631-315c-44b4-a9b5-752e51cb38e2" />
 

### **Monitor the Server**

Use monitoring tools like **Prometheus**, **Grafana**, or **Datadog** to keep an eye on server metrics like CPU usage, memory, and network traffic. This is especially important for production systems.

Additionally, check logs periodically:

* **Jellyfin logs**: Located in the Docker container or mounted volume at `./config/logs`.
* **Nginx logs**: Located at `/var/log/nginx/`.

## **6. Setting up DNS**

### **Setup DNS record for pointing the website to your Domain Name**

``` Type A
Name @
Data <your-server-ip>
TTL 3600/1 Hour

for ex- 

Type Name Data              TTL
A	 @	  210.79.129.50	    1 Hour		
```


## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

```

### Key Details:
- **Clone the Repository**: Instructions for cloning the GitHub repo and navigating into the project directory.
- **Docker Compose Setup**: How to run Jellyfin using Docker Compose.
- **Nginx Configuration**: Full steps to set up Nginx as a reverse proxy, including SSL setup with Certbot.
- **Customization**: Notes to customize domain and configuration as needed.

Feel free to replace placeholders (like `yourmovieserver.com`) with your actual domain and adjust any paths if necessary!
```
