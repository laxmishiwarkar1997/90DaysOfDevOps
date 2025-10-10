---
title: "Connecting the Dots in AWS — VPC Peering, Transit Gateway & CloudWatch Setup"
seoTitle: "VPC Peering Transit Gateway CloudWatch "
seoDescription: "VPC peering"
datePublished: Fri Oct 10 2025 16:36:48 GMT+0000 (Coordinated Universal Time)
cuid: cmgl2jzcw000002ii66hc0bi2
slug: connecting-the-dots-in-aws-vpc-peering-transit-gateway-and-cloudwatch-setup
tags: gateway, vpc-peering, vpc-peering-transit-gateway-and-cloudwatch-setup

---

## 1\. Understanding the Basics

Before we jump into configurations, let’s build a quick mental map.

### What is a VPC?

A **Virtual Private Cloud (VPC)** is your isolated network space inside AWS. Think of it as your own **private data center** in the cloud where you define your IP range, subnets, route tables, and gateways.

### What is a Subnet?

Subnets are segments within your VPC — **public subnets** for internet-facing resources, and **private subnets** for internal ones.

### What is an Internet Gateway (IGW)?

An IGW allows resources in your VPC (like EC2 instances) to access the internet. Without it, your public subnet is just a fenced area!

### What is a Route Table?

A Route Table controls **how traffic flows**. It decides whether packets stay inside the VPC, go to another VPC (via peering), or reach the internet (via IGW).

### What is VPC Peering?

**VPC Peering** connects two VPCs so instances can talk to each other **privately** using internal IPs — without the internet.

> Note: VPC peering does **not support transitive routing** (A ↔ B ↔ C won’t work unless all are directly peered).

## 2\. Step-by-Step: Create and Configure VPC Peering

We’ll connect **VPC-A** and **VPC-B** so instances in both can communicate securely.

### Step 1: Create Two VPCs

1. Open **AWS Console → VPC → Create VPC**
    
2. Name them:
    
    * `VPC-A` → CIDR: `10.0.0.0/16`
        
    * `VPC-B` → CIDR: `10.1.0.0/16`
        

### Step 2: Create Subnets

* For each VPC, create **one public subnet** in the same region.
    
    * Example:
        
        * `VPC-A-Subnet` → `10.0.1.0/24`
            
        * `VPC-B-Subnet` → `10.1.1.0/24`
            

### Step 3: Attach Internet Gateways

1. Create and attach an **Internet Gateway (IGW)** to each VPC.
    
2. Update each subnet’s **route table** to send `0.0.0.0/0` traffic to the IGW.
    

### Step 4: Launch EC2 Instances

Launch one EC2 instance in each subnet:

* `EC2-A` inside VPC-A
    
* `EC2-B` inside VPC-B
    

Make sure both have **SSH access (port 22)** allowed in their **security groups**.

### Step 5: Create VPC Peering Connection

1. Go to **VPC Dashboard → Peering Connections → Create Peering Connection**
    
2. Choose:
    
    * **Requester VPC:** VPC-A
        
    * **Accepter VPC:** VPC-B
        
3. Click **Create Peering Connection**.
    
4. Go back and **Accept** it from VPC-B’s side.
    

### Step 6: Update Route Tables

In both VPCs:

* Add a route to the other VPC’s CIDR block pointing to the **VPC Peering Connection**.
    

Example:

* In VPC-A route table → `10.1.0.0/16` → Target: Peering Connection ID
    
* In VPC-B route table → `10.0.0.0/16` → Target: Peering Connection ID
    

### Step 7: Test Connectivity

* SSH into `EC2-A` and ping `EC2-B`’s **private IP**.
    
* You should receive successful responses 🎉
    

---

## 3\. Scaling Up: AWS Transit Gateway

When multiple VPCs exist (like in **ByteConnect Inc.**), peering each pair becomes messy.  
That’s where **Transit Gateway (TGW)** saves the day!

**Transit Gateway** acts like a **central router** — every VPC connects once, and TGW handles the rest.

### Architecture Overview:

```plaintext
        ┌───────────┐
        │ VPC-Sales │
        └─────┬─────┘
              │
        ┌─────▼─────┐
        │ Transit GW │
        └─────▲─────┘
              │
        ┌─────┴─────┐
        │ VPC-HR     │
        └────────────┘
```

### Steps to Implement:

1. Go to **VPC → Transit Gateway → Create TGW**.
    
2. Attach both VPCs (Sales & HR) to TGW using **VPC attachments**.
    
3. Update **route tables** in each VPC to route through TGW.
    
4. Verify connectivity between VPCs — no need for peering!
    

---

## 4\. Monitoring with CloudWatch + NGINX

Now as an AWS Intern at XYZ company, you’ll need to monitor your NGINX server logs using **CloudWatch**.

### Step 1: Launch EC2 and Install NGINX

```bash
sudo apt update -y
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
```

Check via browser:  
`http://<your-ec2-public-ip>`

### Step 2: Install CloudWatch Agent

```bash
sudo yum install -y amazon-cloudwatch-agent
```

or for Ubuntu:

```bash
sudo snap install amazon-cloudwatch-agent --classic
```

### Step 3: Create IAM Role

* Attach **CloudWatchAgentServerPolicy** to your EC2 instance IAM role.
    

### Step 4: Configure the Agent

Run:

```bash
sudo /opt/aws/amazon-cloudwatch-agent/bin/amazon-cloudwatch-agent-config-wizard
```

Follow prompts to choose:

* Metrics: CPU, memory, disk
    
* Log file path: `/var/log/nginx/access.log`
    

Then start the agent:

```bash
sudo systemctl start amazon-cloudwatch-agent
sudo systemctl status amazon-cloudwatch-agent
```

### Step 5: Verify in CloudWatch Console

* Go to **CloudWatch → Logs → Log Groups**
    
* You’ll see `/var/log/nginx/access.log` being streamed in real-time 🚀
    

---

## Key Takeaways

| Concept | Summary |
| --- | --- |
| **VPC Peering** | One-to-one private connectivity between VPCs |
| **Transit Gateway** | Central hub for connecting multiple VPCs |
| **CloudWatch** | AWS service to monitor metrics and logs |
| **Nginx + CloudWatch** | Real-time log tracking and performance monitoring |

* Always check **CIDR overlap** before creating peering.
    
* For large-scale environments, prefer **Transit Gateway** over Peering.
    
* Automate CloudWatch setup using **IAM roles + EC2 UserData** scripts.
    

---

Today’s project helped me connect isolated cloud networks securely and monitor them efficiently. From **VPC Peering** to **Transit Gateway** and **CloudWatch**, I now have a clearer picture of how AWS handles **network isolation and observability**.

---