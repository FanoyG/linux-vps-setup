# **Cloud VPS Setup & Configuration Documentation**

## **1️⃣ Cloud Server Overview**
This document provides a step-by-step guide for setting up a **Cloud VPS** for hosting a small business website.

- **Cloud Provider**: AWS / Google Cloud / DigitalOcean / Linode / Vultr (Choose One)
- **OS**: Ubuntu 22.04 / CentOS 8
- **Web Server**: Apache/Nginx
- **Database**: MySQL/MariaDB
- **Security**: Firewall (UFW/Firewalld), SSH hardening, automated backups, Fail2Ban
- **Domain & SSL**: Let's Encrypt SSL for HTTPS security
- **Monitoring**: Uptime monitoring, resource monitoring

---

## **2️⃣ Server Deployment & Access**

### **🌍 Cloud VPS Deployment**
1. Log into your cloud provider.
2. Choose **Ubuntu 22.04 LTS** (or CentOS 8).
3. Select **1 vCPU, 2GB RAM (Recommended for small websites, adjust based on needs)**.
4. Choose a **Data Center Region** close to your users.
5. Add an **SSH Key for secure access**.
6. Enable **automatic updates**.
7. Deploy the server.

### **🔑 SSH Access**
- **Connect to your server:**
  ```bash
  ssh -i ~/.ssh/my_key.pem user@your_server_ip
  ```
- **Security Enhancements:**
  - **Disable root login**
  - **Enable key-based authentication**
  - **Change default SSH port to a non-standard port (e.g., 2222)**
  - **Install Fail2Ban to prevent brute-force attacks**

### **🔑 Secure SSH Access Setup**

1️⃣ **Generate an SSH Key (if not already created):**  
   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
   ```

2️⃣ **Copy Public Key to the Server:**  
   ```bash
   ssh-copy-id -i ~/.ssh/id_rsa.pub username@your_server_ip
   ```

3️⃣ **Disable Password Authentication & Root Login:**  
   ```bash
   sudo nano /etc/ssh/sshd_config
   ```
   Change:
   ```bash
   PermitRootLogin no
   PasswordAuthentication no
   ```
   Restart SSH:
   ```bash
   sudo systemctl restart sshd
   ```

---

## **3️⃣ Web Server Configuration (Apache/Nginx)**

### **🛠️ Install Web Server**
- **For Apache:**
  ```bash
  sudo apt update && sudo apt install apache2 -y
  sudo systemctl enable --now apache2
  ```
- **For Nginx:**
  ```bash
  sudo apt update && sudo apt install nginx -y
  sudo systemctl enable --now nginx
  ```

### **🌐 Configure Virtual Host**
- **For Apache:**
  ```bash
  sudo nano /etc/apache2/sites-available/mywebsite.conf
  ```
  ```apache
  <VirtualHost *:80>
      ServerName yourdomain.com
      DocumentRoot /var/www/html
      ErrorLog ${APACHE_LOG_DIR}/error.log
      CustomLog ${APACHE_LOG_DIR}/access.log combined
  </VirtualHost>
  ```
  ```bash
  sudo a2ensite mywebsite.conf
  sudo systemctl restart apache2
  ```
- **For Nginx:**
  ```bash
  sudo nano /etc/nginx/sites-available/mywebsite
  ```
  ```nginx
  server {
      listen 80;
      server_name yourdomain.com;
      root /var/www/html;
      index index.html;
  }
  ```
  ```bash
  sudo ln -s /etc/nginx/sites-available/mywebsite /etc/nginx/sites-enabled/
  sudo systemctl restart nginx
  ```

---

## **4️⃣ Secure the Server**

### **🔒 Firewall & Fail2Ban**
- **Enable UFW and allow necessary ports:**
  ```bash
  sudo ufw allow 2222/tcp    # SSH (Custom Port)
  sudo ufw allow 80/tcp      # HTTP
  sudo ufw allow 443/tcp     # HTTPS
  sudo ufw enable
  ```
- **Install Fail2Ban:**
  ```bash
  sudo apt install fail2ban -y
  ```
  Configure Fail2Ban for SSH brute-force prevention:
  ```bash
  sudo nano /etc/fail2ban/jail.local
  ```
  Add:
  ```
  [sshd]
  enabled = true
  port = 2222
  maxretry = 5
  findtime = 600
  bantime = 3600
  ```
  Restart Fail2Ban:
  ```bash
  sudo systemctl restart fail2ban
  ```

### **🔐 Install SSL (Let's Encrypt)**
```bash
sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx -d yourdomain.com
```

---

## **5️⃣ Database Setup (MySQL/MariaDB)**

### **🗄️ Install & Secure MySQL**
- **Install MySQL/MariaDB:**
  ```bash
  sudo apt install mysql-server -y
  sudo systemctl enable --now mysql
  ```
- **Secure MySQL:**
  ```bash
  sudo mysql_secure_installation
  ```

### **📌 Create a New Database**
```bash
sudo mysql -u root -p
CREATE DATABASE mydatabase;
CREATE USER 'myuser'@'localhost' IDENTIFIED BY 'mypassword';
GRANT ALL PRIVILEGES ON mydatabase.* TO 'myuser'@'localhost';
FLUSH PRIVILEGES;
EXIT;
```

---

## **6️⃣ Backup & Monitoring**

### **📂 Automated Backups**
- **Setup Cron Job for Weekly Backup:**
  ```bash
  sudo crontab -e
  ```
  Add:
  ```
  0 3 * * 1 mysqldump -u root -p mydatabase > /backup/db_$(date +\%F).sql
  ```

### **📊 Server Monitoring**
- **Install and configure monitoring tools:**
  ```bash
  sudo apt install htop
  ```
- **Check Server Load & Resources:**
  ```bash
  top
  free -m
  df -h
  ```
- **View Logs for Debugging:**
  ```bash
  sudo journalctl -xe
  sudo tail -f /var/log/apache2/error.log  # Apache
  sudo tail -f /var/log/nginx/error.log    # Nginx
  ```

---

## **7️⃣ Final Checks**
- [x] SSH secured ✅
- [x] Firewall enabled ✅
- [x] Web server configured ✅
- [x] Database running ✅
- [x] Backups scheduled ✅
- [x] SSL installed ✅
- [x] Monitoring enabled ✅
- [x] Reboot test passed ✅

**🎉 Your Cloud VPS is fully set up, secured, and optimized! 🎉**

