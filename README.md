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
