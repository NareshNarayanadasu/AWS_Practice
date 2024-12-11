# Setting Up Your EC2 Instance

## Create an EC2 Instance:
1. Log in to the AWS Console.
2. Launch a new EC2 instance (preferably Ubuntu or Amazon Linux 2).
3. Choose the appropriate instance type.
4. Create and assign a security group allowing SSH (port 22) and HTTP/HTTPS (ports 80/443).
5. Download the `.pem` key pair for SSH access.

## Connect to the EC2 Instance:
Use the following command to SSH into the EC2 instance:
```bash
ssh -i "barcode_app.pem" ubuntu@ec2-15-168-12-61.ap-northeast-3.compute.amazonaws.com
```

![EC2 Instance Setup](./images/ec2.png)

# Installing Java JDK 21 on Ubuntu

### **Step 1: Update Package List**
Before installing any package, update the package list to ensure you have the latest repository information:
```bash
sudo apt update
```

### **Step 2: Install OpenJDK 21**
To install OpenJDK 21, run the following command:
```bash
sudo apt install openjdk-21-jdk -y
```
This will install **OpenJDK 21** along with the necessary development tools.

### **Step 3: Verify the Installation**
After installation, verify that Java 21 is installed correctly:

![JDK Installation](./images/jdk_install.png)

# Installing Maven

### **Step 1: Update Package List**
Start by updating the package list:
```bash
sudo apt update
```

### **Step 2: Install Maven**
Install Maven directly from the Ubuntu repositories:
```bash
sudo apt install maven -y
```
This will install the latest stable version of Maven from the official Ubuntu package repository.

![Maven Installation](./images/maven_install.png)

# Clone the Repository
Clone the repository for the Train Ticket Reservation System:
```bash
git clone https://github.com/NareshNarayanadasu/Train-Ticket-Reservation-System.git
```

# Installing MySQL

### **Step 1: Update the Package List**
Update the package list to fetch the latest MySQL packages:
```bash
sudo apt update
```

### **Step 2: Install MySQL Server**
Install MySQL Server:
```bash
sudo apt install mysql-server -y
```
This will install MySQL 8.2 (or the latest available version in the MySQL 8.x series).

### **Step 3: Verify the Installation**
Check the MySQL version:
```bash
mysql --version
```

### **Start MySQL**
Start and enable MySQL to run on boot:
```bash
sudo systemctl start mysql
sudo systemctl enable mysql
sudo systemctl status mysql
```

![MySQL Installation](./images/SQL_INTSLATION.png)

### **Log in to MySQL**
Log in to the MySQL server:
```bash
sudo mysql -u root
```

### **Create a User**
Create a new MySQL user:
```sql
CREATE USER 'root1'@'localhost' IDENTIFIED BY 'Password@123';
GRANT ALL PRIVILEGES ON `traindb`.* TO 'root1'@'localhost';
```

### **Create a Database**
Set up the database configuration in `application.properties`:
```properties
# MySQL Database Configuration
spring.datasource.url=jdbc:mysql://localhost:3306/traindb?serverTimezone=UTC
spring.datasource.username=RESERVATION
spring.datasource.password=MANAGER
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
```

# Build the Project
Navigate to the `Train-Ticket-Reservation-System` directory:
```bash
cd Train-Ticket-Reservation-System
```

Run specific Maven goals (clean and install):
```bash
mvn clean install
```

# Installing JBoss (WildFly) on Ubuntu

## Prerequisites

### **Install Java Development Kit (JDK)**
Ensure you have a JDK installed:
```bash
sudo apt install default-jdk
```

### **Create a Dedicated User Account**
Create a user account to run WildFly:
```bash
sudo groupadd wildfly
sudo useradd -r -g wildfly -d /opt/wildfly -s /sbin/nologin wildfly
```

## Download and Extract WildFly
Download the WildFly `.tar.gz` file:
```bash
wget https://github.com/wildfly/wildfly/releases/download/34.0.1.Final/wildfly-34.0.1.Final.tar.gz
```

Extract the downloaded archive:
```bash
sudo tar -xzvf wildfly-34.0.1.Final.tar.gz -C /opt/
```

Set the appropriate permissions:
```bash
sudo chown -R wildfly:wildfly /opt/wildfly-34.0.1.Final
```

## Configure Systemd Service
Create a systemd service file for WildFly:
```bash
sudo nano /etc/systemd/system/wildfly.service
```

Add the following content to the file:
```ini
[Unit]
Description=WildFly Application Server
After=network.target

[Service]
User=wildfly
Group=wildfly
WorkingDirectory=/opt/wildfly-34.0.1.Final/bin
ExecStart=/opt/wildfly-34.0.1.Final/bin/standalone.sh -b 0.0.0.0
ExecStop=/opt/wildfly-34.0.1.Final/bin/standalone.sh -S

[Install]
WantedBy=multi-user.target
```

## Start and Enable WildFly
Start and enable WildFly:
```bash
sudo systemctl start wildfly
sudo systemctl enable wildfly
```

Check the status of WildFly:
```bash
sudo systemctl status wildfly
```

![WildFly Status](./images/wildify.png)

## Access the WildFly Management Console
Open a web browser and navigate to:
```
http://localhost:8080/
```
Log in with the default credentials:
- **Username**: admin
- **Password**: admin

## Additional Considerations
- **Customization**: Customize WildFly configuration in `/opt/wildfly/standalone/configuration`.
- **Security**: Use a strong password and enable SSL/TLS.
- **Monitoring**: Use tools like JMX or Prometheus for monitoring WildFly.

# Deploying the WAR File

### **Prepare Your WAR File**
Ensure your application is packaged as a standard WAR file.

### **Identify the Deployment Directory**
- **Default Directory**: `/opt/wildfly/standalone/deployments`
- **Custom Directory**: Configure WildFly to monitor other directories or use the management console.

### **Deploy the WAR File**

#### **Method 1: Direct Deployment**
1. Copy the WAR file to the deployment directory.
2. Monitor the server log for deployment messages.

#### **Method 2: Using the Management Console**
1. Access the management console at `http://localhost:9990`.
2. Log in with the credentials (admin/admin).
3. Navigate to the "Deployments" section.
4. Click on "Add," select the WAR file, and deploy it.

### **Verify Deployment**
- **Check the Server Log**: Look for deployment messages.
- **Access the Application**: Use the appropriate URL, e.g., `http://localhost:8080/your-app-name`.
