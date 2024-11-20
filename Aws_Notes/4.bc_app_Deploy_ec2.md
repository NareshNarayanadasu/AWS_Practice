### **Deploy an Application on EC2 Using HTTPd as a Web Server**

This guide walks through deploying a web application on an EC2 instance using Apache HTTPd as the web server.

---

### **What is HTTPd?**  
HTTPd (Hypertext Transfer Protocol Daemon) is an open-source web server software used to serve web content over the internet. Commonly used implementation: **Apache HTTP Server**.

#### **Key HTTPd Configuration Directories:**
1. **Port**: 80 (default for HTTP traffic).  
2. **Server Root**: `/etc/httpd` (location of server configuration files).  
3. **Config Path**: `/etc/httpd/conf/httpd.conf` (main configuration file).  
4. **Document Root**: `/var/www/html` (default directory to serve web files).

---

### **Steps to Deploy Your Application**

#### **Step 1: Create an EC2 Instance**
1. Go to the **AWS Management Console** and create an EC2 instance:
   - **AMI**: Choose Linux, CentOS, or Ubuntu.  
   - **Instance Type**: `t2.micro` (free-tier eligible).  
   - **Security Group Rules**:
     - Allow **SSH** (Port 22).
     - Allow **HTTP** (Port 80).  
   - **Storage**: Use default or adjust as needed.  
2. Launch the instance and download the private key file (`.pem`) for SSH access.

---

#### **Step 2: Connect to the EC2 Instance**
1. Open a terminal and connect via SSH:
   ```bash
   ssh -i /path/to/pem-file ec2-user@<public-ip>
   ```
   Replace `/path/to/pem-file` with your key file and `<public-ip>` with the instance's public IP.

---

#### **Step 3: Install and Configure HTTPd**
1. **Update the System**:
   ```bash
   sudo yum -y update
   ```

2. **Install HTTPd**:
   ```bash
   sudo yum -y install httpd
   ```

3. **Start and Enable HTTPd**:
   ```bash
   sudo systemctl start httpd
   sudo systemctl enable httpd
   ```

4. **Verify HTTPd Installation**:
   - Open a web browser and navigate to `http://<public-ip>`.  
   - You should see the default Apache HTTPd welcome page.

---

#### **Step 4: Install Git and Clone Application Code**
1. **Install Git**:
   ```bash
   sudo yum -y install git
   ```

2. **Clone the Repository**:
   ```bash
   sudo git clone http://github.com/ai-technov/ecomm.git /var/www/html
   ```

3. **Set Permissions**:
   ```bash
   sudo chown -R apache:apache /var/www/html
   sudo chmod -R 755 /var/www/html
   ```

---

#### **Step 5: Access the Web Application**
- Open a browser and go to `http://<public-ip>`.  
- The web application cloned from GitHub should now be live.

---

### **Key Commands for Troubleshooting**

1. **Check HTTPd Status**:
   ```bash
   sudo systemctl status httpd
   ```

2. **Restart HTTPd**:
   ```bash
   sudo systemctl restart httpd
   ```

3. **Check Logs**:
   - Error Log: `/var/log/httpd/error_log`
   - Access Log: `/var/log/httpd/access_log`

4. **Verify Firewall Rules**:
   Ensure HTTP (port 80) is allowed in the EC2 Security Group.

---

### **Summary**
You have successfully deployed a web application on an EC2 instance using HTTPd as the web server. The application is accessible via the public IP of the instance. Let me know if you need further assistance!