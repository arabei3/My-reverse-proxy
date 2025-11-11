# 🚀 AWS Reverse Proxy Infrastructure using Terraform

## 🧭 Overview
This project builds a **production-grade AWS infrastructure** using Terraform.  
It provisions an environment with **Nginx reverse proxies** in public subnets and **Flask backend servers** in private subnets —  
connected through **public** and **internal load balancers**, all organized using **modular Terraform**.

---

## 🧱 Architecture

Internet → Public ALB → Nginx Reverse Proxies (Public Subnets)
↓
Internal ALB → Flask Backends (Private Subnets)



---

## 🧩 Main Components

- **VPC** — Custom (CIDR: `10.0.0.0/16`)
- **2 Public Subnets** — Host Nginx proxies
- **2 Private Subnets** — Host Flask backends
- **NAT Gateway** — Allows private instances to reach the internet
- **Public ALB** — Routes external traffic to Nginx
- **Internal ALB** — Routes internal traffic from Nginx to Flask
- **EC2 Instances** — 2 proxies + 2 backend servers
- **Terraform Modules** — Clean, reusable infrastructure code

---

## ⚙️ Prerequisites

- AWS account with sufficient permissions  
- Terraform v1.0 or higher installed  
- AWS CLI configured (`aws configure`)  
- Existing EC2 Key Pair in AWS  
- S3 bucket for Terraform remote state  
- DynamoDB table for state locking  

---

## 📂 Project Structure

terraform-aws-reverse-proxy/
├── main.tf # Root orchestrator
├── variables.tf # Global variables
├── outputs.tf # Global outputs
├── providers.tf # AWS provider setup
├── backend.tf # Remote backend config (S3 + DynamoDB)
├── terraform.tfvars # Variable values
│
├── modules/
│ ├── network/ # VPC, Subnets, NAT, IGW, Route Tables
│ ├── security/ # Security Groups
│ ├── compute/ # EC2 Instances + Provisioners
│ └── loadbalancing/ # ALBs, Target Groups, Listeners
│
├── provisioners/
│ └── app/
│ ├── app.py # Flask Application
│ └── requirements.txt # Python dependencies
│
└── all-ips.txt # Generated file listing all EC2 IPs



---

## 🧰 Backend Setup

Before running Terraform, create your backend resources:

### Create S3 bucket
<<<<<<< Updated upstream
```bash
=======

>>>>>>> Stashed changes
aws s3api create-bucket \
  --bucket reverse-proxy-terraform-state-rabei \
  --region us-east-1
Enable versioning

aws s3api put-bucket-versioning \
  --bucket reverse-proxy-terraform-state-rabei \
  --versioning-configuration Status=Enabled
Create DynamoDB table for state locking

aws dynamodb create-table \
  --table-name terraform-state-lock \
  --attribute-definitions AttributeName=LockID,AttributeType=S \
  --key-schema AttributeName=LockID,KeyType=HASH \
  --billing-mode PAY_PER_REQUEST \
  --region us-east-1
<<<<<<< Updated upstream
🪄 Deployment Steps
=======
## Deployment Steps
>>>>>>> Stashed changes
1️⃣ Initialize Terraform

terraform init
2️⃣ Configure Variables
Edit terraform.tfvars with your values:


key_name         = "rabei-key"
private_key_path = "~/.ssh/rabei-key.pem"
region           = "us-east-1"
3️⃣ Create a Workspace

terraform workspace new dev
terraform workspace select dev
4️⃣ Plan & Apply

terraform plan
terraform apply -auto-approve
<<<<<<< Updated upstream
🧪 Testing
=======
##🧪 Testing
>>>>>>> Stashed changes
🔹 Show Outputs

terraform output public_alb_url
🔹 Test Public ALB

curl $(terraform output -raw public_alb_url)
Expected Response:


{
  "status": "healthy",
  "message": "Backend server is running!",
  "hostname": "ip-10-0-1-10",
  "ip": "10.0.1.10"
}
🔹 SSH to Public Proxy

ssh -i ~/.ssh/rabei-key.pem ec2-user@<proxy-public-ip>
🔹 SSH to Private Backend (via Proxy)

ssh -i ~/.ssh/rabei-key.pem -J ec2-user@<proxy-public-ip> ec2-user@<backend-private-ip>
<<<<<<< Updated upstream
🧠 Provisioners Overview
=======
##🧠 Provisioners Overview
>>>>>>> Stashed changes
Type	Description
remote-exec (Proxy)	Installs and configures Nginx as a reverse proxy
remote-exec (Backend)	Installs Python3 & Flask, starts backend service
file	Copies Flask app files to backend instance
local-exec	Generates all-ips.txt containing all instance IPs

Example of the generated file:


proxy-ip1 54.23.11.22
proxy-ip2 54.23.12.33
backend-ip1 10.0.1.10
backend-ip2 10.0.3.20
<<<<<<< Updated upstream
🔐 Security Best Practices
=======
##🔐 Security Best Practices
>>>>>>> Stashed changes
Restrict SSH access to your IP only

Use IAM Roles instead of static credentials

Enable MFA on your AWS account

Rotate SSH keys regularly

Enable VPC Flow Logs for auditing

Use HTTPS in production

<<<<<<< Updated upstream
🧹 Cleanup
=======
##🧹 Cleanup
>>>>>>> Stashed changes

terraform destroy -auto-approve
terraform workspace select default
terraform workspace delete dev
<<<<<<< Updated upstream
💰 Cost Estimate (per month)
=======
##💰 Cost Estimate (per month)
>>>>>>> Stashed changes
Resource	Estimated Cost
4× EC2 (t3.micro)	~$30
1× NAT Gateway	~$32
2× ALB	~$32
Total Estimate	≈ $95/month

🧩 Module Summary
Module	Purpose
Network	Creates VPC, Subnets, Routes, NAT, IGW
Security	Manages all Security Groups
Compute	Creates EC2s and runs provisioners
Load Balancing	Sets up ALBs, Listeners, and Target Groups

📊 Technical Notes
Uses aws_ami data source for the latest Amazon Linux 2 AMI

Stores remote state in S3 with DynamoDB locking

Nginx servers act as bastion hosts for private EC2 access

Internal ALB uses health checks on / endpoint

Each environment can have its own Terraform workspace
