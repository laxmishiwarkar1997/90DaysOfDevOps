---
title: "Automating User Account Management with Bash | 90 Days of DevOps – Week 3 Challenge 1"
seoTitle: "Automating User Management and Backups with Bash | 90 Days of DevOps "
seoDescription: "Managing user accounts is one of the foundational tasks for any Linux system administrator. For Week 3, Challenge 1 of the 90 Days of DevOps program, I buil"
datePublished: Mon Jun 09 2025 11:14:42 GMT+0000 (Coordinated Universal Time)
cuid: cmbozvza4000502jie75v5yn7
slug: automating-user-account-management-with-bash-90-days-of-devops-week-3-challenge-1
tags: bash-scripting-devops-linux-automation-shell-scripting-cicd-continuous-integration-continuous-deployment-scripting-best-practices-devops-tools-linux-automation-shell-script-for-devops-devops-workflow-devops-automation-infrastructure-as-code-bash-scripts-for-cicd-linux-scripting-cicd-pipeline-scripting-in-devops-automation-with-bash-bash-for-devops-engineers

---

Managing user accounts is one of the foundational tasks for any Linux system administrator. For **Week 3, Challenge 1** of the [90 Days of DevOps](https://90daysofdevops.com/) program, I built a versatile bash script that simplifies this process through a command-line interface.

This script, named `user_management.sh`, provides options to **create, delete, reset passwords**, and **list user accounts**—while also including a usage/help section for convenience.

---

## 🧠 Challenge Objective

The task required creating a bash script to manage Linux user accounts using various flags for specific functions:

* `-c` or `--create`: Create a new user
    
* `-d` or `--delete`: Delete an existing user
    
* `-r` or `--reset`: Reset a user password
    
* `-l` or `--list`: List all system users with their UIDs
    
* `-h` or `--help`: Show script usage instructions
    

Additionally, the script should:

* Prompt for inputs securely
    
* Handle edge cases like existing users, mismatched passwords, or empty fields
    
* Be readable and properly commented
    

---

## 🔧 The Script in Action

Here's a breakdown of each feature implemented in `user_management.sh`:

### ✅ Part 1: Create User Account (`-c` / `--create`)

This section:

* Prompts the admin to enter a new username and password
    
* Validates input and checks if the username already exists
    
* If valid, creates the account and sets the password using `useradd` and `chpasswd`
    
* Provides feedback at every step
    

🧩 Example output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749313964594/05abcf99-d517-408e-836b-e628f88b6ba9.png align="center")

### ❌ Part 2: Delete User Account (`-d` / `--delete`)

This function:

* Prompts for a username to delete
    
* Validates the existence of the user
    
* Deletes the user account using `userdel -r` (removes home directory too)
    
* Notifies the admin of success or failure
    

🔐 Example output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749313994720/993f89bf-4cb1-45c4-a278-a74e061ffef5.png align="center")

### 🔑 Part 3: Reset Password (`-r` / `--reset`)

Here, the script:

* Prompts for the username and new password
    
* Checks for existing user
    
* Ensures password confirmation matches
    
* Resets the password securely using `chpasswd`
    

⚠️ Example:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749314033123/bbdb8bd5-6009-49ee-af8d-821d37c6ccff.png align="center")

### 📋 Part 4: List All User Accounts (`-l` / `--list`)

This feature extracts and displays all usernames and their corresponding UIDs from `/etc/passwd` using `awk`.

💡 Sample output:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749314055654/bb432b5d-afdd-4f21-8e8c-c71217aa8a13.png align="center")

### 📘 Part 5: Help & Usage (`-h` / `--help`)

If the script is run with no or incorrect arguments, it displays clear usage instructions and exits gracefully.

🔎 Example

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749314120175/4f7b0a79-1146-475b-b706-37ca84838777.png align="center")

:

---

## ✨ Features and Enhancements

* Fully interactive prompts with validation
    
* Secure password input using `read -s`
    
* Color-coded feedback can be added for better UX (omitted here for simplicity)
    
* Graceful error handling for missing/invalid inputs
    
* Modular function-based structure
    

### Bonus Ideas (Future Enhancements):

* Display more account metadata (home directory, shell, last login)
    
* Add functionality to lock/unlock accounts
    
* Integrate logs for actions performed
    
* Add support for bulk user creation from a CSV
    

---

## 📁 Full Script

You can find the full `user_management.sh` script [on my GitHub here](#) (link to your repo or Gist).

---

## What I Learned

* How to modularize shell scripts using functions
    
* Effective use of `id`, `useradd`, `userdel`, `chpasswd`, and `awk`
    
* Prompting user inputs securely in bash
    
* How command-line tools can be combined to automate sysadmin tasks
    
* How to build scripts that are user-friendly, reusable, and production-ready
    

# Week 3: Challenge 2

## 🔄 Automated Backup & Recovery using Cron and Bash

In Day 2 of the Bash Scripting Challenge for Week 3 of the **#90DaysOfDevOps**, we take a practical dive into **automating backups** using **cron jobs** and **bash scripting**. This challenge taught me how to build a robust backup strategy with **timestamped snapshots**, **rotation logic**, and **minimal manual intervention**.

---

## 🔧 Challenge Overview

**Goal:**  
Write a bash script named `backup_with_`[`rotation.sh`](http://rotation.sh) that:

* Accepts a directory path as a command-line argument.
    
* Creates a timestamped backup folder containing copies of all files from the given directory.
    
* Retains **only the last 3 backup folders**, deleting older ones automatically.
    

This solution is ideal for lightweight backup systems or automation tasks in environments where you don't want to install full-scale backup software.

---

## 📁 Challenge Breakdown

### 🎯 Key Features to Implement:

* **Directory validation**
    
* **Timestamped backups**
    
* **Backup rotation (keeping last 3 only)**
    
* **Logging or echoing progress to the terminal**
    

### 🖥️ Example Usage:

Output

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749466834820/93478251-77e1-4bd2-ab80-ec7abfe3066d.png align="center")

:

After several executions, the directory should contain only the 3 most recent `backup_YYYY-MM-DD_HH-MM-SS` folders along with the original files.

---

## 📜 The Script: `backup_with_`[`rotation.sh`](http://rotation.sh)

```plaintext
#!/bin/bash

: <<'info'
This script is for backup rotation with a limit of 3 backups.
Usage:
 ./backup_with_rotation.sh /home/user/documents
info

# Function to display usage
display() {
    echo "This script performs backup rotation for 3 days."
    echo "Usage: ./backup_with_rotation.sh <path>"
}

# Check if no argument is passed
if [ "$#" -eq 0 ]; then
    display
    exit 1
fi

TARGET_DIR="$1"
TIMESTAMP=$(date +"%Y-%m-%d_%H-%M-%S")
BACKUP_DIR="$TARGET_DIR/backup_$TIMESTAMP"

# Ensure target directory exists
if [ ! -d "$TARGET_DIR" ]; then
    echo "Error: Directory $TARGET_DIR does not exist."
    exit 1
fi

# Create backup directory
mkdir "$BACKUP_DIR"

# Change to the target directory
cd "$TARGET_DIR" || exit 1

# Copy items excluding existing backups
for item in *; do
    # Skip backup directories
    if echo "$item" | grep -q "^backup_"; then
        continue
    fi
    cp -r "$item" "$BACKUP_DIR"
done

echo "Backup created: $BACKUP_DIR"

# Rotation: Keep only the 3 most recent backups
BACKUP_DIRS=$(ls -dt "$TARGET_DIR"/backup_* 2>/dev/null)
COUNT=0

for dir in $BACKUP_DIRS; do
    COUNT=$((COUNT + 1))
    if [ "$COUNT" -gt 3 ]; then
        rm -rf "$dir"
        echo "Old backup removed: $dir"
    fi
done
```

---

---

## ✅ Output Example

### After 4 script runs:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1749467118340/6594f304-6a72-4613-9ff7-53e644d9802d.png align="center")

Only the latest 3 backups are preserved—older ones are automatically deleted.

---

## 💡 What I Learned

* The importance of automation in DevOps for repeatable tasks.
    
* Handling dynamic file naming using timestamps.
    
* Implementing rotation logic using bash arrays.
    
* Clean scripting practices with validation and user-friendly messaging.
    

---

This challenge reinforced the importance of **small scripts** in solving **big operational problems**. Automating even simple tasks like backups can save hours of manual effort and ensure consistent disaster recovery readiness.