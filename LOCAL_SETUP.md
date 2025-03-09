# **Linux Server Setup & Configuration Documentation**

## **1️⃣ Server Overview**
This document provides a step-by-step guide to managing your newly configured Linux VPS for hosting your small business website.

- **OS**: Ubuntu/CentOS (as per client's choice)
- **Web Server**: Apache/Nginx
- **Database**: MySQL/MariaDB
- **Firewall**: UFW/Firewalld
- **SSH Security**: Root login disabled, key-based authentication enabled, custom SSH port set
- **Backup System**: Automated weekly backups

---

## **2️⃣ Server Access & Management**

### **🔑 SSH Access**
- **Command to connect to server:**
  ```bash
  ssh -p <custom_port> user@<your_server_ip>
  ```
- **SSH Security Measures:**
  - Root login disabled
  - Key-based authentication enabled
  - Custom SSH port: `<custom_port>`

### **🌐 Web Server (Apache/Nginx) Management**
- **Check server status:**
  ```bash
  systemctl status apache2   # For Apache
  systemctl status nginx     # For Nginx
  ```
- **Restart server:**
  ```bash
  systemctl restart apache2   # For Apache
  systemctl restart nginx     # For Nginx
  ```

### **🛡️ Firewall Rules (UFW/Firewalld)**
- **Check firewall status:**
  ```bash
  sudo ufw status      # For UFW
  sudo firewall-cmd --list-all   # For Firewalld
  ```
- **To allow new ports:**
  ```bash
  sudo ufw allow 80/tcp   # Allow HTTP
  sudo ufw allow 443/tcp  # Allow HTTPS
  ```

### **🗄️ Database (MySQL/MariaDB) Management**
- **Login to MySQL/MariaDB:**
  ```bash
  sudo mysql -u root -p
  ```
- **Check database status:**
  ```bash
  systemctl status mariadb   # For MariaDB
  systemctl status mysql     # For MySQL
  ```
- **Secure database:**
  ```bash
  sudo mysql_secure_installation
  ```

### **📂 Backup & Restore**
- **Backup location:** `<backup_directory>`
- **Automatic backup schedule:** Every midnight (`0 0 * * *` cron job)
- **Manually run backup:**
  ```bash
  sudo <backup_directory>/backup_script.sh
  ```
- **Restore backup:**
  ```bash
  mysql -u root -p < <backup_directory>/db_backup.sql
  ```

### **🔄 Reboot & Post-Reboot Check**
- **Reboot server:**
  ```bash
  sudo reboot
  ```
- **Check all services post-reboot:**
  ```bash
  systemctl status apache2   # Check Web Server
  systemctl status mariadb   # Check Database
  sudo ufw status            # Check Firewall (if using UFW)
  ```

---

## **3️⃣ Troubleshooting Guide**

### **🛑 Web Server Not Running?**
- Check logs:
  ```bash
  sudo journalctl -xe
  sudo tail -f /var/log/apache2/error.log  # Apache
  sudo tail -f /var/log/nginx/error.log    # Nginx
  ```
- Restart service:
  ```bash
  sudo systemctl restart apache2/nginx
  ```

### **🚫 Cannot SSH into Server?**
- Ensure correct SSH port is used (`<custom_port>`)
- Check SSH service:
  ```bash
  sudo systemctl status sshd
  ```
- Verify firewall rules:
  ```bash
  sudo ufw status
  ```

### **🔒 Database Access Denied?**
- Reset root password:
  ```bash
  sudo mysql
  ALTER USER 'root'@'localhost' IDENTIFIED BY '<NewPassword>';
  FLUSH PRIVILEGES;
  EXIT;
  ```
  **⚠️ Warning:** Ensure you replace `<NewPassword>` with a secure password and do not hardcode it in public documentation.

---

## **4️⃣ Additional Notes**
- Keep your server **updated** regularly:
  ```bash
  sudo apt update && sudo apt upgrade -y    # Ubuntu/Debian
  sudo yum update -y                        # CentOS
  ```
- Monitor disk space:
  ```bash
  df -h
  ```
- Log file locations:
  - Web server logs: `/var/log/apache2/` or `/var/log/nginx/`
  - Database logs: `/var/log/mysql/`
  - Firewall logs: `/var/log/ufw.log`

---

## **✅ Final Confirmation**
- [x] Web server is running ✅
- [x] Firewall is configured ✅
- [x] SSH security is implemented ✅
- [x] Database is set up ✅
- [x] Backup system is active ✅
- [x] Reboot test passed ✅

**🎉 Your server is fully set up and secured! 🎉**

