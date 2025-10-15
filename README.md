# 🌐 Subdomain Setup for Testing

This guide documents how we configured a **dedicated subdomain on Google Cloud (GCP)** using **Nginx + SSL (Certbot)** for our project.  
It serves as a controlled environment to simulate HTTP errors and verify secure (HTTPS) traffic handling.

---

## ⚙️ Setup Instructions

🧩 1. Install & Start Nginx
```bash
sudo apt update
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
sudo systemctl status nginx
```

☁️ 2. Open Firewall (HTTP + HTTPS)
```
GCP Console → VPC Network → Firewall → Create Rule
Name: allow-http-https | Direction: Ingress | Action: Allow | Source: 0.0.0.0/0 | Ports: tcp:80,44
```

🌎 3. Create Subdomain in Cloudflare
Type	Name	Content	Proxy
A	errors	<GCP_VM_EXTERNAL_IP>	DNS Only (Gray Cloud)

🧱 4. Setup HTML Directory
```
sudo mkdir -p /var/www/html/errors
sudo nano /var/www/html/errors/index.html
```
```
<h1>HTTP Error Simulation</h1>
<ul>
  <li><a href="/404">404</a></li>
  <li><a href="/500">500</a></li>
  <li><a href="/403">403</a></li>
  <li><a href="/400">400</a></li>
  <li><a href="/502">502</a></li>
</ul>
```

⚙️ 5. Configure Nginx
```
sudo nano /etc/nginx/sites-available/errors.conf
```
```
server {
    listen 80;
    server_name your_domain_name;
    root /var/www/html/errors;
    index index.html;
    include /etc/nginx/mime.types;
    default_type text/html;
    location / { try_files $uri $uri/ /index.html; }
    location /404 { return 404 "<h1>404 Not Found</h1>"; }
    location /500 { return 500 "<h1>500 Internal Server Error</h1>"; }
    location /403 { return 403 "<h1>403 Forbidden</h1>"; }
    location /400 { return 400 "<h1>400 Bad Request</h1>"; }
    location /502 { return 502 "<h1>502 Bad Gateway</h1>"; }
}
```

```
sudo ln -s /etc/nginx/sites-available/errors.conf /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

🔐 6. Install SSL Certificate (Certbot)
```
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d errors.vivekreddy.dev
sudo nginx -t
sudo systemctl restart nginx
```

✅ 8. Test
```
https://your_domain_name → Loads main simulation page
/404, /500, /403, /400, /502 → Return correct HTTP codes
```

## 🎬 Demo Video

🎥 Click Here to Watch the Setup & Working Demo

## 📘 Summary

A complete subdomain testing environment for simulating HTTP response codes, validating HTTPS security,
and analyzing web traffic logs for our network monitoring project.

## 👨‍💻 Contributors

| Name | GitHub | Role |
|------|---------|------|
| **Avinash. M** | [@avinash](https://github.com/avinash) | Documentation, Project Coordination |
| **Vivek Reddy** | [@vivekreddy9036](https://github.com/vivekreddy9036) | Server Setup, SSL Integration, Nginx Configuration, DNS Configuration|
| **Teja** | — | Testing, Debugging, and Demo Video Production |
