# 🌐 Subdomain Setup for Testing (errors.vivekreddy.dev)

This guide explains how we configured a dedicated **subdomain on GCP with Nginx + SSL (Certbot)**  
for testing HTTP/HTTPS traffic and custom error responses as part of our project.

---

## ⚙️ Quick Setup Commands

### 🧩 1. Install & Start Nginx
```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
