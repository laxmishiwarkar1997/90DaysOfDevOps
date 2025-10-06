---
title: "Day 2,3 of AWS journey :"
seoTitle: "aws WAF and S3"
datePublished: Mon Oct 06 2025 20:24:47 GMT+0000 (Coordinated Universal Time)
cuid: cmgfkxr88000002lb7eou829g
slug: day-23-of-aws-journey
tags: aws, cloud-computing, s3, waf, s3-bucket, sheild

---

---

# Securing Your Web Application with AWS WAF: A Hands-On Guide

Web applications are constantly exposed to threats like **SQL injection, cross-site scripting (XSS), and malicious bots**. Protecting your apps is critical, and **AWS WAF (Web Application Firewall)** makes this easy by filtering and monitoring traffic based on customizable rules.

In this blog, I’ll show you how to implement AWS WAF for a sample web application on EC2.

---

## What is AWS WAF?

AWS WAF is a **web application firewall** that lets you:

* Block requests from malicious IP addresses.
    
* Filter traffic containing attack patterns (SQLi, XSS, etc.).
    
* Control access at the HTTP/S level using **WebACLs (Web Access Control Lists)**.
    

---

## Hands-On Project: Implementing AWS WAF

### Step 1: Set Up a Sample Web Application

1. Launch an **EC2 instance**.
    
2. Install a simple web server:
    

```bash
sudo apt update
sudo apt install -y apache2
sudo systemctl start apache2
sudo systemctl enable apache2
```

3. Create a sample HTML page:
    

```bash
echo "<h1>Welcome to My Web App</h1>" | sudo tee /var/www/html/index.html
```

4. Make sure the security group allows **HTTP (port 80)** access.
    
5. Visit your EC2 public IP to verify the web app is running.
    

---

### Step 2: Configure AWS WAF

1. Open the **AWS WAF & Shield** console.
    
2. Create a **WebACL**:
    
    * Choose **Regional** for EC2 applications.
        
    * Add **Rules**:
        
        * **IP Set Rule**: Block specific IP addresses.
            
        * **Managed Rule Groups**: Include AWS Managed rules for SQLi and XSS protection.
            
3. Associate the WebACL with your **EC2 instance or Load Balancer**.
    

> Example: Blocking a malicious IP using CLI:

```bash
aws wafv2 create-ip-set \
    --name "BlockedIPs" \
    --scope REGIONAL \
    --ip-address-version IPV4 \
    --addresses "203.0.113.0/24" \
    --region us-east-1
```

---

### Step 3: Test AWS WAF

* **Malicious Request Test**: Try accessing your app with SQL injection patterns like:
    

```plaintext
http://<EC2-IP>/?id=' OR '1'='1
```

You should see that the request is blocked.

* **Legitimate Request Test**: Normal traffic should still be accessible.
    

> AWS WAF logs can be viewed in **CloudWatch Logs** to confirm which requests were blocked.

---

## Key Learnings

* AWS WAF provides **flexible security controls** to protect web applications.
    
* Testing rules is crucial to **avoid blocking legitimate traffic**.
    
* Understanding attack patterns helps in creating **effective and precise WAF rules**.
    

---

## Conclusion

By implementing AWS WAF for your web applications, you can **proactively secure them against common vulnerabilities**. The combination of WebACLs, IP blocking, and managed rules gives you a strong security layer without complex configuration.

---

**Next Steps:**

* Explore **Rate-based rules** in AWS WAF to block traffic exceeding thresholds.
    
* Integrate WAF with **CloudFront** for global web app protection.
    

---

# Understanding AWS Essentials: S3 Buckets, IAM, and AWS CLI

Amazon Web Services (AWS) is a cloud platform that provides scalable and reliable solutions for businesses of all sizes. In this blog, we’ll explore three essential AWS services — **S3**, **IAM**, and **AWS CLI** — along with some hands-on tasks to help you learn by doing.

---

## **What is an S3 Bucket?**

Amazon **Simple Storage Service (S3)** is a **scalable object storage service**. It allows you to store and retrieve any amount of data from anywhere on the web.

### Common Uses:

* Backup & Restore
    
* Archiving
    
* Content Distribution
    
* Hosting Static Websites
    

---

## **What is IAM in AWS?**

**Identity and Access Management (IAM)** is a service that **secures access to AWS resources**. It enables you to manage **users, groups, roles, and permissions** efficiently.

### Key Components:

* **Users** – Individual accounts for team members
    
* **Groups** – Collections of users with shared permissions
    
* **Roles** – Assignable permissions for services or users
    
* **Policies** – Rules defining access to AWS resources
    

---

## **What is AWS CLI?**

The **AWS Command Line Interface (CLI)** allows you to **interact with AWS services directly from the terminal**, bypassing the AWS Management Console.

---

## **Hands-on AWS Tasks**

### **Task 1: Make a Private S3 Bucket**

* Create a bucket and modify its policy so that **you can access it without making it public**.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1759782118376/ea99b95f-e5de-49f7-ba1c-0c3e4b625ada.png align="center")
    

### **Task 2: Configure AWS CLI on Ubuntu**

### **Task 3: Create an EC2 Instance Using AWS CLI**

* Launch a virtual server via CLI commands.
    

### **Task 4: Setting Up AWS IAM for a New Team Member**

**Scenario:** You’re an IT admin at a company using AWS. Your new colleague Alex needs access to monitor EC2 instances and manage S3 buckets.

**Steps:**

1. Create an IAM user for new.
    
2. Assign permissions:
    
    * **View EC2 Instances:** new can monitor but not modify servers.
        
    * **Create S3 Buckets:** new can manage project storage.
        

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1759781921688/7b4c0d67-abed-4ef2-9426-e635e89df6cd.png align="center")

---

## **Conclusion**

By practicing with **S3 buckets, IAM, and AWS CLI**, you get hands-on experience with AWS storage, security, and management. These skills are essential for anyone looking to grow in cloud computing.