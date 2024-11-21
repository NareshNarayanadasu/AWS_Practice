### **Launch Multiple Servers with Similar Configuration Using EC2 User Data**

Using **User Data** in Amazon EC2 allows you to automate the configuration of servers during their launch. This is especially useful when deploying multiple instances with similar setups, such as web servers, application servers, or database servers.

---

### **What is EC2 User Data?**
- **Definition**: User Data is a script or commands that are executed when an EC2 instance launches.  
- **Purpose**: Automates instance initialization tasks such as software installation, service configuration, or file deployment.  
- **Supported Formats**: Shell scripts (`#!/bin/bash`) or cloud-init directives.

---

### **Steps to Launch Multiple Servers Using User Data**

#### **Step 1: Write the User Data Script**
Prepare the script that will configure the instances upon launch. For example, to set up a web server:

```bash
#!/bin/bash
sudo yum -y install git
sudo yum -y install httpd
sudo systemctl start httpd
sudo systemctl enable httpd
sudo git clone <URL> /var/www/html/
```

Replace `<URL>` with the GitHub repository URL.

---

#### **Step 2: Launch EC2 Instances**
1. **Go to AWS Console**: Navigate to the EC2 Dashboard.
2. **Create a New Instance**:
   - **AMI**: Select Linux-based AMI (e.g., Amazon Linux 2).  
   - **Instance Type**: Choose the desired type (e.g., `t2.micro` for free-tier eligible).  
   - **Number of Instances**: Specify the count for how many servers you want to launch.  
3. **Configure Security Group**:
   - Allow **SSH** (Port 22).
   - Allow **HTTP** (Port 80).  
4. **Advanced Details**:
   - Scroll down to **Advanced Details**.
   - Paste the User Data script in the **User Data** field.

---

#### **Step 3: Verify Server Setup**
1. Once the instances are running, connect to one of the instances via SSH to verify:
   ```bash
   ssh -i /path/to/pem-file ec2-user@<public-ip>
   ```
2. Check if HTTPd is running:
   ```bash
   sudo systemctl status httpd
   ```
3. Open a browser and navigate to the public IP of the instance (`http://<public-ip>`). You should see the application from the cloned GitHub repository.

---

### **Key Notes**

- **User Data Script Execution**:  
  - Scripts in the **User Data** field execute only once during the first boot.  
  - To re-run the script, you can manually execute it or restart the instance with the script preloaded.  

- **Scaling with Auto Scaling Groups**:  
  If you need a larger, scalable fleet of servers, consider using **Auto Scaling Groups**. Include the same User Data script in the launch configuration.

- **Preloading Instances with Same Configurations**:  
  If you frequently launch instances with the same setup, consider creating a custom AMI after configuring one instance.

---

### **Summary**
Using EC2 User Data scripts is a powerful way to automate instance initialization tasks. By launching multiple instances with a predefined User Data script, you can quickly deploy web servers or other infrastructure. Let me know if you'd like to explore advanced options like scaling or automation!