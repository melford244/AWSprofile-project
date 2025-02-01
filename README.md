# Project Name


## **Architecture Diagram**

![Image Alt](https://github.com/melford244/AWSprofile-project/blob/0af0cc72f8407de6495e6aeb634865b917225379/AWS%20CLOUD.io%20DIAGRAM.drawio.png)

## **Objectives**
1. **Flexible Infrastructure:**
   - Use AWS services like EC2, Auto Scaling, and ELB to create a scalable and flexible infrastructure.
2. **No Upfront Cost (Pay-as-you-go):**
   - Leverage AWS's pay-as-you-go pricing model to avoid upfront costs.
3. **Modernize Effectively:**
   - Use Infrastructure as Code (IaC) tools like AWS CloudFormation or Terraform to automate deployment.
4. **IaC (Automation):**
   - Automate the deployment process using scripts and AWS services.

---

## **Directory Structure**
Here’s a suggested directory structure for your project:

```
aws-deployment/
├── scripts/                  # Bash scripts for automation
│   ├── user-data.sh          # EC2 user data script
│   ├── deploy-app.sh         # Script to deploy app to EC2
│   └── setup-elb.sh          # Script to configure ELB
├── src/                      # Application source code
│   ├── app/                  # Application code
│   └── build.sh              # Script to build the application
├── terraform/                # Terraform IaC files (optional)
│   ├── main.tf               # Main Terraform configuration
│   ├── variables.tf          # Terraform variables
│   └── outputs.tf            # Terraform outputs
├── artifacts/                # Built application artifacts
└── README.md                 # Project documentation
```

---

## **Detailed Steps for Deployment**

### **Step 1: Login to AWS Account**
1. Go to the [AWS Management Console](https://aws.amazon.com/console/).
2. Log in with your credentials.

---

### **Step 2: Create Key Pairs**
1. Navigate to **EC2 > Key Pairs**.
2. Click **Create Key Pair**.
3. Name the key pair (e.g., `tomcat-key`) and download the `.pem` file.
4. Save the `.pem` file securely (used to SSH into EC2 instances).

---

### **Step 3: Create Security Groups**
1. Navigate to **EC2 > Security Groups**.
2. Create security groups for:
   - **Load Balancer:** Allow HTTP (port 80) and HTTPS (port 443) traffic.
   - **Tomcat Instances:** Allow SSH (port 22) and HTTP (port 8080) traffic.
   - **Backend Services:** Allow traffic from Tomcat instances.

---

### **Step 4: Launch EC2 Instances with User Data**
1. Navigate to **EC2 > Instances**.
2. Click **Launch Instance**.
3. Choose an Amazon Machine Image (AMI) (e.g., Amazon Linux 2).
4. Select an instance type (e.g., `t2.micro`).
5. Configure instance details and add the following **user data** (Bash script):
   ```bash
   #!/bin/bash
   yum update -y
   yum install -y java-1.8.0-openjdk tomcat
   systemctl start tomcat
   systemctl enable tomcat
   ```
6. Attach the security group for Tomcat instances.
7. Launch the instance.

---

### **Step 5: Update IP to Name Mapping in Route 53**
1. Navigate to **Route 53 > Hosted Zones**.
2. Create a new hosted zone or use an existing one.
3. Add an **A record** to map your domain name (e.g., `example.com`) to the public IP of the EC2 instance.

---

### **Step 6: Build Application from Source Code**
1. On your local machine, navigate to the `src/` directory.
2. Run the build script:
   ```bash
   ./build.sh
   ```
   This will generate an artifact (e.g., `app.war`).

---

### **Step 7: Upload Artifact to S3 Bucket**
1. Navigate to **S3 > Buckets**.
2. Create a new S3 bucket (e.g., `my-app-artifacts`).
3. Upload the artifact (`app.war`) to the bucket.

---

### **Step 8: Download Artifact to Tomcat EC2 Instance**
1. SSH into the Tomcat EC2 instance:
   ```bash
   ssh -i tomcat-key.pem ec2-user@<public-ip>
   ```
2. Download the artifact from S3:
   ```bash
   aws s3 cp s3://my-app-artifacts/app.war /var/lib/tomcat/webapps/
   ```

---

### **Step 9: Set Up ELB with HTTPS**
1. Navigate to **EC2 > Load Balancers**.
2. Create an **Application Load Balancer (ALB)**.
3. Configure listeners for HTTP (port 80) and HTTPS (port 443).
4. Attach the security group for the load balancer.
5. Use a certificate from **Amazon Certificate Manager (ACM)** for HTTPS.

---

### **Step 10: Map ELB Endpoint to Domain Name**
1. Go to **Route 53 > Hosted Zones**.
2. Update the **A record** to point to the ALB's DNS name.

---

### **Step 11: Verify the Website**
1. Open a browser and navigate to your domain (e.g., `https://tooplate.com`).
2. Verify that the application is up and running.

---

### **Step 12: Build Auto Scaling Group for Tomcat Instances**
1. Navigate to **EC2 > Auto Scaling Groups**.
2. Create an Auto Scaling Group:
   - Use the Tomcat instance as a template.
   - Set minimum, maximum, and desired capacity.
   - Attach the Auto Scaling Group to the ALB.
3. Configure scaling policies (e.g., scale based on CPU utilization).


