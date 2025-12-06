# 🚀 Deploying a Website on AWS EC2 Using Amazon Linux & Nginx

**A Complete Setup Guide With Real-World Troubleshooting**

This project documents the full process of deploying a production-ready website on an AWS EC2 instance using **Amazon Linux** and **Nginx**.
It also includes the **exact errors I encountered during deployment and how I fixed them**, making this repository valuable for anyone new to cloud deployment.

---

## 📌 Project Overview

* **Platform:** AWS EC2
* **Operating System:** Amazon Linux
* **Web Server:** Nginx
* **Purpose:** Learn, deploy, troubleshoot, and optimise a Linux-powered web server
* **Audience:** Beginners and intermediate cloud engineers

---

## 🧰 Technologies Used

* AWS EC2
* Amazon Linux 2
* Nginx
* Bash / Linux CLI
* Git
* Systemd

---

## 📂 Project Structure

```
/
├── index.html           # Website home page
├── /etc/nginx/nginx.conf   # Main Nginx config (system file)
└── README.md            # Documentation
```

---

# 🛠️ Step-by-Step Deployment Guide

## 1️⃣ Launch an EC2 Instance

Use:

* Amazon Linux 2 AMI
* Instance type: t3.micro
* Security Group: Open ports **22 (SSH)** and **80 (HTTP)**

SSH into your instance:

```bash
ssh -i your-key.pem ec2-user@your-public-ip
```

---

## 2️⃣ Install Nginx

```bash
sudo yum update -y
sudo yum install -y nginx
```

Start and enable the service:

```bash
sudo systemctl start nginx
sudo systemctl enable nginx
```

Check status:

```bash
systemctl status nginx
```

---

## 3️⃣ Configure Firewall (If using Firewalld)

Amazon Linux does **not** include `firewalld` by default.
When running:

```
sudo firewall-cmd --permanent --add-service=http
sudo: firewall-cmd: command not found
```

### ❗ ERROR #

**`sudo: firewall-cmd: command not found`**

### ✔ FIX

Amazon Linux uses **security groups**, not firewalld.
So the correct fix is:

* Open port **80** in the EC2 **Security Group**, not in the OS firewall.

Go to:
**AWS Console → EC2 → Security Groups → Inbound rules → Add rule → HTTP (80)**

---

## 4️⃣ Deploy Your Website Files

Create the default directory:

```bash
cd /usr/share/nginx/html
sudo nano index.html
```

Add your HTML and save.

Restart Nginx:

```bash
sudo systemctl restart nginx
```

Visit in browser:

```
http://your-ec2-public-ip
```


![EC2 Instance Running](Website on AWS EC2 Using Amazon Linux & Nginx screenshots/Screenshot (454).png)
![Nginx Active](./screenshots/nginx-active.png)
![Website Output](./screenshots/webpage-output.png)
---

# 🔥 Troubleshooting & Errors Encountered

This is the real value of the project — the actual errors you encountered and the exact fixes.

---

## ❗ ERROR #1

### **Nginx failed to start**

```
Job for nginx.service failed because the control process exited with error code.
See "systemctl status nginx.service" and "journalctl -xeu nginx.service".
```

### ✔ FIX

Run diagnostics:

```bash
sudo systemctl status nginx.service
sudo journalctl -xeu nginx.service
```

Common underlying issues and fixes:

### **a. Nginx configuration syntax error**

You can test config BEFORE restarting:

```bash
sudo nginx -t
```

If you see errors, correct the indicated line.

---

## ❗ ERROR #2

### **Port 80 already in use**

(Occurs if another service is running on port 80)

Check what is using port 80:

```bash
sudo lsof -i :80
```

Stop the conflicting service:

```bash
sudo systemctl stop httpd
```

Then start Nginx:

```bash
sudo systemctl start nginx
```

---

## ❗ ERROR #3

### **firewall-cmd not found**

```
sudo: firewall-cmd: command not found
```

### ✔ FIX

Amazon Linux does **not** use firewalld.
Correct action is to edit the EC2 **Security Group** to allow HTTP (port 80).

---

# 🌐 Final Deployment Result

Once the corrections were applied:

* Nginx started successfully
* The EC2 Security Group allowed public traffic
* The website loaded correctly on:

```
http://your-ec2-public-ip
```

---

# 🎯 Lessons Learned

* Always check Nginx syntax using `nginx -t` before restarting
* Amazon Linux does not use `firewalld`; use Security Groups instead
* Most failed-to-start Nginx issues come from missing configs or port conflicts
* Logs are your friend:

  * `systemctl status nginx`
  * `journalctl -xeu nginx.service`

---

# 🤝 Contributing

Feel free to fork this repo and contribute improvements.

Just tell me!
