### **AWS Auto Scaling: Creating an Auto Scaling Group (ASG) for EC2 Instances Behind an ALB**

---

### **Overview of AWS Auto Scaling**

AWS Auto Scaling ensures applications maintain **high availability** while optimizing cost efficiency by automatically scaling resources based on demand. It supports scaling for EC2 instances, ECS tasks, DynamoDB tables, and more. Key features include **dynamic scaling**, **scheduled scaling**, **health checks**, and **predictive scaling**.

---

### **Key Components**

1. **Scaling Plans**:  
   - Define scaling triggers based on metrics or schedules.  

2. **Supported Resources**:  
   - EC2 Instances, ECS Tasks, DynamoDB Tables, and Aurora Read Replicas.

3. **Scaling Policies**:  
   - **Target Tracking Scaling**: Adjust resources to meet a metric target.  
   - **Step Scaling**: Scale in increments when thresholds are breached.  
   - **Scheduled Scaling**: Predefine scaling actions for specific times.

4. **Auto Scaling Groups (ASG)**:  
   - Logical groupings of EC2 instances managed by Auto Scaling.

---

### **Step-by-Step Guide: Setting Up an ASG for EC2 Instances Behind an ALB**

#### **Step 1: Create a Launch Template**
A Launch Template defines the configuration for instances launched by the ASG.

1. **Navigate to the EC2 Dashboard**:  
   - Go to **Launch Templates**.  
   - Click **Create Launch Template**.  

2. **Configure the Launch Template**:  
   - **Name**: Provide a descriptive name, e.g., `WebServer-LaunchTemplate`.  
   - **AMI**: Select the Amazon Machine Image (e.g., Amazon Linux 2) used for existing instances.  
   - **Instance Type**: Match the instance type (e.g., `t2.micro`).  
   - **Key Pair**: Use an existing key pair or create a new one for SSH access.  
   - **Network Settings**: Leave blank (ASG will handle VPC and subnet selection).  
   - **Storage**: Define EBS volume settings.  

3. **Add User Data**:  
   Use the following script to install and configure a web server automatically:  

   ```bash
   #!/bin/bash
   yum update -y
   yum install -y httpd
   systemctl start httpd
   systemctl enable httpd
   echo "Hello from $(hostname)" > /var/www/html/index.html
   ```

4. **Security Group**:  
   Use a security group that allows HTTP (port 80) access.  

5. **Create Launch Template**:  
   - Review settings and click **Create Launch Template**.

---

#### **Step 2: Create an Auto Scaling Group (ASG)**

1. **Navigate to Auto Scaling Groups**:  
   - In the EC2 Dashboard, click **Auto Scaling Groups**.  
   - Click **Create Auto Scaling Group**.  

2. **Configure ASG Basics**:  
   - **Name**: Provide a name, e.g., `WebServer-ASG`.  
   - **Launch Template**: Select the template created in Step 1.  

3. **Select VPC and Subnets**:  
   - Choose the VPC of the existing instances.  
   - Select at least **two subnets in different Availability Zones** for fault tolerance.  

4. **Attach Load Balancer**:  
   - Choose **Attach to a Target Group**.  
   - Select the Target Group linked to your Application Load Balancer (ALB).  

5. **Configure Desired Capacity**:  
   - **Desired Capacity**: Set to 2 (matches the existing setup).  
   - **Minimum Capacity**: 1.  
   - **Maximum Capacity**: 3 (or more based on traffic).  

6. **Set Scaling Policies**:  
   - Use **Target Tracking Scaling**:
     - Metric: Average CPU Utilization.  
     - Target value: 50% (scales up or down based on CPU usage).  
   - Optionally, configure **Step Scaling** for finer control.  

7. **Add Notifications**:  
   - Configure notifications via an **SNS Topic** for scaling events.  

8. **Review and Create**:  
   - Verify the settings and click **Create Auto Scaling Group**.

---

#### **Step 3: Test the Setup**

1. **Simulate Scaling Events**:  
   - Adjust the Desired Capacity:
     - Set it to 1 and verify that an instance is terminated.  
     - Increase it to 3 and observe new instances launching.  

2. **Health Check**:  
   - Stop one instance to confirm that ASG launches a replacement.  

3. **Load Balancer Integration**:  
   - Verify that newly launched instances are automatically registered with the ALB.  
   - Test the web application to ensure it serves traffic as expected.

---

### **Benefits of Using Auto Scaling with ALB**
- **High Availability**: Ensures resources are distributed across Availability Zones.  
- **Cost Efficiency**: Scales down during low demand to reduce costs.  
- **Fault Tolerance**: Replaces unhealthy instances automatically.  
- **Optimized Performance**: Responds to demand spikes to maintain performance.  

