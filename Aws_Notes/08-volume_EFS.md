**AWS Elastic File System (EFS) and NFS Overview**

### **What is AWS Elastic File System (EFS)?**
EFS is a file-level storage service provided by AWS that enables you to store and share data across multiple EC2 instances concurrently. It is a fully managed service, which means AWS takes care of scaling, high availability, and durability. Unlike EBS (Elastic Block Storage), EFS is file-based storage that can be accessed over the network and is optimized for file-based workloads.

### **How Does EFS Work?**
- **Scalability and Availability**: EFS spans multiple availability zones within a region, allowing it to provide high availability and durability. This makes it ideal for file systems that require sharing data across multiple EC2 instances.
- **Performance Tuning**: You can choose the level of I/O operations (IOPS) you need based on your application workload.
- **Data Sharing**: EFS can be used to share data between multiple EC2 instances simultaneously.
- **Access Control**: You can configure security groups (SG) and access permissions on the file system to control who can read, write, and access the data.

### **Use Cases for EFS**:
1. **Secured File Sharing**: EFS can be used to share data between EC2 instances, enabling collaboration between applications.
2. **Web Hosting**: Hosting multiple web servers that need to access shared data. EFS scales automatically as data volumes increase.
3. **Modern Application Development**: Sharing data between AWS ECS, EKS, and serverless applications like Lambda.
4. **Machine Learning and AI Workloads**: EFS is well-suited for large datasets and collaborative environments where multiple containers or instances need access to the same data.

### **NFS Protocol**:
- **NFS (Network File System)** is a protocol used to enable distributed file sharing between servers and clients across networks. It defines how data in the form of files is stored and retrieved from storage devices.
- **NFS Version 4 (NFSv4)**: The latest version of NFS, documented in RFC 7530, uses Remote Procedure Calls (RPCs) to route requests between clients and servers.
- **Port Used**: Both EFS and NFS use port 2049 for communication.

### **LAB Instructions**:
**Objective**: To configure EFS between two EC2 instances in different Availability Zones (AZs) within the same VPC.

#### 1. **Create Two EC2 Instances**:
- **EC2-1**:
  - Network: Default
  - Availability Zone: 1a
  - Security Group: SSH
  - Storage: Default
  - Launch

- **EC2-2**:
  - Network: Default
  - Availability Zone: 1b
  - Security Group: SSH
  - Storage: Default
  - Launch

#### 2. **Create EFS (Elastic File System)**:
- Go to the **EFS Console**.
- Select **Create EFS**.
- **Name**: `EFS`
- **VPC**: Default
- **Click Create EFS**.

#### 3. **Configure Security Group for EFS**:
- **Go to VPC Console**.
- **Security Groups**.
- **Create a Security Group**:
  - Name: `EFS-Port-2049`
- **Configure Rule**:
  - **Type**: Custom TCP Rule
  - **Port Range**: 2049
  - **Source**: The security group of your EC2 instances (e.g., `SSH` security group).
- **Attach the Security Group** to the EFS file system in the EFS Console under the **Network** section.

#### 4. **Attach EFS to EC2 Instances**:
- **Go to the EFS Console**.
- **Select your EFS file system**.
- **Go to the Network section** and click **Manage Security Groups**.
- **Add** the previously created Security Group (`EFS-Port-2049`).
- **Save changes**.

Now, your EFS is configured and accessible from both EC2 instances in the same VPC.

#### 5. **Mount EFS on EC2 Instances**:
1. **SSH into EC2-1 (in AZ 1a)**:
   ```bash
   sudo mkdir /mnt/efs
   sudo mount -t nfs4 <EFS-DNS>: /mnt/efs
   ```

2. **SSH into EC2-2 (in AZ 1b)**:
   ```bash
   sudo mkdir /mnt/efs
   sudo mount -t nfs4 <EFS-DNS>: /mnt/efs
   ```
   - **Replace `<EFS-DNS>` with the DNS name of your EFS file system**.
   - After mounting, both EC2 instances can access the same data and perform operations on the EFS volume simultaneously.

Now you have configured EFS between two EC2 instances in different availability zones using the NFS protocol (port 2049). This setup enables you to share data across EC2 instances effectively, even in a highly available and durable environment.