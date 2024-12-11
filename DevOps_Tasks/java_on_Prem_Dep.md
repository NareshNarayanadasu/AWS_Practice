# **Deploying BarCode Generator Application on Amazon Linux 2**

This guide covers all the necessary steps to set up and deploy the BarCode Generator application on an EC2 instance running Amazon Linux 2. 

---

## **1. Setting Up the EC2 Instance**

### **Create EC2 Instance**
- Instance type: `t2.micro`
- Use a key pair (e.g., `barcode_app.pem`) for SSH access.

### **Connect to EC2 Instance**
Run the following command from your local machine:
```bash
ssh -i "barcode_app.pem" ec2-user@ec2-15-168-238-31.ap-northeast-3.compute.amazonaws.com
```

### **Update the System**
```bash
sudo yum update -y
```

---

## **2. Install Git**

Install Git to clone the application's repository:
```bash
sudo yum install git -y
```

### **Clone the Repository**
Ensure you have SSH access to the private repository. Clone it using:
```bash
git clone git@github.com:NareshNarayanadasu/BarCodeGenerator.git
```

If successful, the repository will be downloaded into your instance.

---

## **3. Install JDK 17**

The application requires JDK 17. Amazon Linux 2 does not always include JDK 17 in its default repositories. Here's how to install it:

### **Install Amazon Corretto 17**
1. Enable Amazon Corretto 17:
   ```bash
   sudo amazon-linux-extras enable corretto17
   ```

2. Install the JDK:
   ```bash
   sudo yum install -y java-17-amazon-corretto-devel
   ```

3. Verify the installation:
   ```bash
   java -version
   ```
   Example output:
   ```
   openjdk version "17.0.x" 2023-xx-xx
   OpenJDK Runtime Environment Corretto-17.x.x.x
   ```

---

## **4. Install Maven**

The project requires Maven for building and dependency management:
```bash
sudo yum install maven -y
```

Verify Maven installation:
```bash
mvn -v
```

---

## **5. Install MySQL**

### **Install MySQL 8.0**
1. Add the MySQL repository:
   ```bash
   sudo yum install -y https://dev.mysql.com/get/mysql80-community-release-el7-5.noarch.rpm
   ```

2. Install MySQL Server:
   ```bash
   sudo yum install -y mysql-community-server
   ```

3. Start MySQL:
   ```bash
   sudo systemctl start mysqld
   sudo systemctl enable mysqld
   ```

4. Secure the installation:
   ```bash
   sudo mysql_secure_installation
   ```

5. Log in and create a database for the application:
   ```bash
   mysql -u root -p
   ```
   ```sql
   CREATE DATABASE BarCodeDB;
   ```

---

## **6. Install Apache Tomcat**

### **Download and Install Tomcat**
1. Download the latest Tomcat version:
   ```bash
   wget https://dlcdn.apache.org/tomcat/tomcat-10/v10.1.13/bin/apache-tomcat-10.1.13.tar.gz
   ```

2. Extract and move Tomcat:
   ```bash
   tar -xvf apache-tomcat-10.1.13.tar.gz
   sudo mv apache-tomcat-10.1.13 /opt/tomcat
   ```

3. Set execute permissions for Tomcat scripts:
   ```bash
   sudo chmod +x /opt/tomcat/bin/*.sh
   ```

4. Start Tomcat:
   ```bash
   /opt/tomcat/bin/startup.sh
   ```

5. Verify Tomcat is running:
   Open a browser and navigate to:
   ```
   http://<EC2_PUBLIC_IP>:8080
   ```
   Ensure port **8080** is open in the EC2 instance's security group.

---

## **7. Configure the Environment**

### **Set JAVA_HOME for Tomcat**
Create the file `/opt/tomcat/bin/setenv.sh` if it doesn't exist:
```bash
sudo nano /opt/tomcat/bin/setenv.sh
```

Add the following line:
```bash
export JAVA_HOME=/usr/lib/jvm/java-17-openjdk
```

Save and exit the file.

---

## **8. Deploy the Application**

### **Build the Application**
Navigate to the project directory and build the application using Maven:
```bash
cd BarCodeGenerator
mvn clean package
```

### **Deploy the WAR File**
Copy the generated `.war` file to Tomcat's `webapps` directory:
```bash
cp target/*.war /opt/tomcat/webapps/
```

### **Restart Tomcat**
Restart Tomcat to deploy the application:
```bash
/opt/tomcat/bin/shutdown.sh
/opt/tomcat/bin/startup.sh
```

---

## **9. Access the Application**

Visit the application in your browser:
```
http://<EC2_PUBLIC_IP>:8080/BarCodeGenerator
```

---

## **Additional Notes**

- Ensure security groups allow inbound traffic on ports **8080** (Tomcat) and **3306** (MySQL), as required.
- Regularly check logs for troubleshooting:
  - **Tomcat Logs**: `/opt/tomcat/logs`
  - **MySQL Logs**: `/var/log/mysqld.log`

This setup ensures your BarCode Generator application is fully operational on Amazon Linux 2. 🎉