---
title: "Deep Dive into Linux Server Management"
seoTitle: "Linux DevOps Deep Dive: Users, Logs, Volumes & Automation"
seoDescription: "Explore the essentials of Linux server administration in Week 2 of #90DaysOfDevOps. "
datePublished: Sat May 31 2025 16:36:19 GMT+0000 (Coordinated Universal Time)
cuid: cmbcgewtg000107jm2rb5guiz
slug: deep-dive-into-linux-server-management
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1748709288100/3732f2da-c393-4310-84ba-e815d1229235.png
tags: linux, devops, shell-scripting, 90daysofdevops, system-administration

---

This week, I plunged into the core of Linux server management, tackling essential tasks that are critical for any DevOps engineer. From user and permission management to log analysis, volume handling, process monitoring, and even automation, this week's challenges were all about building a solid foundation for managing production-grade Linux systems.

Let's break down what I learned and implemented.

### 1\. User & Group Management: The Foundation of Security

Understanding Linux users, groups, and permissions is paramount for secure and efficient server operations. I started by exploring `/etc/passwd` and `/etc/group` to grasp how user and group information is stored.

**My Task 1:**

* **Created a user** `devops_user` and added them to a group `devops_team`: This simulates setting up a new team member with appropriate access. Bash
    
    ```plaintext
    sudo groupadd devops_team
    sudo useradd -m -g devops_team devops_user
    ```
    
* **Set a password and granted** `sudo` access:  
    Option A: Granting `sudo` access carefully is crucial for administrative tasks. Bash
    
    ```plaintext
    sudo passwd devops_user
    sudo usermod -aG sudo devops_user
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748694938268/03748159-9903-4662-a3a7-3ea89d59e92c.png align="center")
    
    Option B: Give explicit sudo access (works on all distros)
    
    ```plaintext
    bashCopyEdit# Open the sudoers file safely
    sudo visudo
    ```
    
    Add the following line:
    
    ```plaintext
    textCopyEditdevops_user ALL=(ALL) NOPASSWD:ALL
    ```
    
    > ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748695009728/7990b984-d6a8-4bb0-9835-8b3e613055a2.png align="center")
    
* **Restricted SSH login for certain users:** This is a vital security measure to prevent unauthorized access. I edited `/etc/ssh/sshd_config` to disallow SSH login for specific users. Bash
    
    ```plaintext
    # Example in /etc/ssh/sshd_config
    # DenyUsers rogue_user
    sudo systemctl restart sshd
    ```
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748695185079/8741b393-aab0-4504-9767-c8a5f447dfd1.png align="center")
    
    ### 2\. File & Directory Permissions: The Gatekeepers of Data
    
    Understanding and correctly applying file and directory permissions is crucial for security and collaboration on any Linux system. This task demonstrates how to set specific access levels for owners, groups, and others.
    
    **My Task 2:**
    
    1. **Create** `/devops_workspace` directory: First, we need a dedicated workspace. Since `/devops_workspace` is at the root level, we'll need `sudo` privileges to create it.
        
        Bash
        
        ```plaintext
        sudo mkdir /devops_workspace
        ```
        
        *Explanation:* The `mkdir` command creates a new directory. `sudo` is used because we're creating it outside of a user's home directory.
        
    2. **Create a file** `project_notes.txt` inside the workspace: Now, let's create the file within our newly made workspace.
        
        Bash
        
        ```plaintext
        sudo touch /devops_workspace/project_notes.txt
        ```
        
        *Explanation:* The `touch` command creates an empty file if it doesn't exist. Again, `sudo` is likely needed if your current user doesn't own `/devops_workspace`.
        
    3. **Set permissions: Owner can edit, group can read, others have no access.** This is where the `chmod` command comes in. We'll use octal notation for precision.
        
        * **Owner can edit:** This means the owner should have read (`4`), write (`2`) permissions. So, `4 + 2 = 6`.
            
        * **Group can read:** This means the group should only have read (`4`) permission.
            
        * **Others have no access:** This means others have no (`0`) permissions.
            
        
        Combining these, we get the octal permission `640`.
        
        Bash
        
        ```plaintext
        sudo chmod 640 /devops_workspace/project_notes.txt
        ```
        
        *Explanation:* `chmod` (change mode) is used to modify file and directory permissions. `640` is the octal representation of the desired permissions. `sudo` is used to ensure we have the necessary rights to change permissions on a file owned by root (or whatever user created it with `sudo`).
        
    4. **Use** `ls -l` to verify permissions: To confirm our permissions are set correctly, we use the `ls -l` command, which provides a detailed listing including permissions.
        
        Bash
        
        ```plaintext
        ls -l /devops_workspace/project_notes.txt
        ```
        
        *Expected Output:* You should see something similar to this (the owner and group might vary depending on who created the file, but the permission string is key):
        
        ```plaintext
        -rw-r----- 1 root root 0 May 31 18:30 /devops_workspace/project_notes.txt
        ```
        
        *Explanation of the permission string* `-rw-r-----`:
        
        * The first character `-` indicates it's a regular file (d for directory, l for symbolic link, etc.).
            
        * `rw-`: These are the permissions for the **owner**. `r` means read, `w` means write. The hyphen `-` means no execute permission. So, the owner can read and write (edit).
            
        * `r--`: These are the permissions for the **group**. `r` means read. The hyphens mean no write or execute permission. So, the group can only read.
            
        * `---`: These are the permissions for **others**. All hyphens mean no read, write, or execute permission. So, others have no access.
            
    5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748695419746/40e68fca-9537-4324-b9ab-df25cd59336d.png align="center")
        
        ### 3\. Log File Analysis with AWK, Grep & Sed: Uncovering Insights
        
        Log files are indispensable in the world of DevOps, providing a detailed narrative of system events, application behavior, and potential issues. This task focuses on leveraging powerful Linux command-line tools—`grep`, `awk`, and `sed`—to extract meaningful insights and manipulate log data, using the `Linux_2k.log` file from the LogHub repository.
        
        **My Task 3: Breakdown and Solutions:**
        
        1. **Download the log file from the repository:** The `Linux_2k.log` file can be directly downloaded using `wget`.
            
            Bash
            
            ```plaintext
            wget https://raw.githubusercontent.com/logpai/loghub/refs/heads/master/Linux/Linux_2k.log
            ```
            
            *Explanation:* `wget` is a command-line utility for retrieving content from web servers. This command downloads the `Linux_2k.log` file into your current directory.
            
        2. **Extract insights using commands:**
            
            * **Use** `grep` to find all occurrences of the word "error": `grep` is perfect for searching text patterns within files. The `-i` option makes the search case-insensitive, which is generally good practice for log analysis.
                
                Bash
                
                ```plaintext
                grep -i "error" Linux_2k.log
                ```
                
                *Explanation:* This command will output every line from `Linux_2k.log` that contains the word "error", regardless of its capitalization (e.g., "error", "Error", "ERROR").
                
            * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748695727578/faa07628-80b6-4ab3-bcbe-35f2eba969fb.png align="center")
                
            * **Use** `awk` to extract timestamps and log levels: `awk` is a versatile tool for text processing, particularly useful for extracting specific fields from structured text. The format of `Linux_2k.log` typically has the timestamp and log level at the beginning of each line. You may need to inspect the file to confirm the exact column numbers for your specific log format. For example, if the timestamp is in the first two fields and the log level in the third:
                
                Bash
                
                ```plaintext
                awk '{print $1, $2, $3}' Linux_2k.log
                ```
                
                *Explanation:* `awk` processes the file line by line. `$1`, `$2`, and `$3` refer to the first, second, and third fields (columns) of each line, respectively, delimited by whitespace. This command will print these selected fields for every log entry.
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748695981046/dc30ba5c-0dd7-4782-8561-8d53f8b3b1ba.png align="center")
                
                  
                
            * **Use** `sed` to replace all IP addresses with `[REDACTED]` for security: `sed` is excellent for stream editing, allowing you to perform find-and-replace operations. We'll use a regular expression to match common IPv4 address patterns and replace them.
                
                Bash
                
                ```plaintext
                sed -E 's/\b([0-9]{1,3}\.){3}[0-9]{1,3}\b/[REDACTED]/g' Linux_2k.log > Linux_2k_redacted.log
                ```
                
                *Explanation:*
                
                * `-E`: Enables extended regular expressions.
                    
                * `s/pattern/replacement/g`: The `s` command stands for substitute. `pattern` is the regular expression to search for, `replacement` is what to replace it with, and `g` means global replacement (replace all occurrences in a line, not just the first).
                    
                * `\b([0-9]{1,3}\.){3}[0-9]{1,3}\b`: This is the regular expression for an IPv4 address.
                    
                    * `\b`: Word boundary (ensures we match whole IP addresses).
                        
                    * `([0-9]{1,3}\.){3}`: Matches one to three digits followed by a dot, repeated three times (e.g., "192.", "168.", "1. ").
                        
                    * `[0-9]{1,3}`: Matches the last segment of the IP address (one to three digits).
                        
                * `[REDACTED]`: The string used for replacement.
                    
                * `> Linux_2k_redacted.log`: Redirects the output to a new file, `Linux_2k_redacted.log`, so the original file remains unchanged.
                    
        3. **Bonus: Find the most frequent log entry using** `awk` or `sort | uniq -c | sort -nr | head -10`: This combines several commands to count and display the most common log messages. The `awk` part helps normalize the log entries by removing variable elements like timestamps and process IDs, making it easier to find identical message patterns.
            
            Bash
            
            ```plaintext
            awk '{$1=$2=$3=""; print $0}' Linux_2k.log | sort | uniq -c | sort -nr | head -10
            ```
            
            *Explanation:*
            
            * `awk '{$1=$2=$3=""; print $0}' Linux_2k.log`: This `awk` command attempts to remove the first few fields (assuming they contain timestamps and potentially other unique identifiers) from each line, then prints the rest of the line. This helps in grouping similar log messages. You might need to adjust `$1=$2=$3=""` based on the actual structure of your `Linux_2k.log` to effectively isolate the message content.
                
            * `sort`: Sorts the modified log entries alphabetically.
                
            * `uniq -c`: Counts consecutive identical lines and prefixes each with the count.
                
            * `sort -nr`: Sorts the output numerically (`-n`) in reverse order (`-r`), placing the most frequent entries at the top.
                
            * `head -10`: Displays only the top 10 most frequent log entries.
                
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748696308068/43288641-b235-4865-9081-1d40d6d44911.png align="center")
        
        ---
        
        ### 4\. Volume Management & Disk Usage: Mastering Storage
        
        Effective management of disk volumes is a fundamental skill in DevOps. It ensures that applications have sufficient storage, prevents disk space exhaustion, and allows for flexible scaling. For this task, we'll simulate mounting a new volume using a *loop device*, which is a common and safe way to practice disk management locally without needing an actual physical disk.
        
        **My Task 4:**  
        **Breakdown and Solutions:**
        
        1. **Create a directory** `/mnt/devops_data`: This directory will serve as our "mount point" – the location where our new volume will be accessible within the file system.
            
            Bash
            
            ```plaintext
            sudo mkdir /mnt/devops_data
            ```
            
            *Explanation:* The `mkdir` command creates a new directory. We use `sudo` because `/mnt` is a system directory, and creating a new directory there requires root privileges.
            
            ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748707226946/1a60a4d7-e617-4c1c-ba60-58a332dc6e1d.png align="center")
            
              
            
        2. **Mount a new volume (or loop device for local practice):** Since we're doing this for practice, a loop device is perfect. This involves creating a file, formatting it as a filesystem, and then mounting that file as if it were a separate disk.
            
            * **Create a dummy file:** We'll create a 100MB file that will act as our "disk."
                
                Bash
                
                ```plaintext
                sudo dd if=/dev/zero of=/root/devops_volume.img bs=1M count=100
                ```
                
                *Explanation:*
                
                * `dd`: A command-line utility for converting and copying files.
                    
                * `if=/dev/zero`: Reads from the special `/dev/zero` device, which provides null characters.
                    
                * `of=/root/devops_volume.img`: Specifies the output file (our disk image). I'm placing it in `/root` for simplicity, but you could choose another location.
                    
                * `bs=1M`: Sets the block size to 1 Megabyte.
                    
                * `count=100`: Creates a file of 100 blocks, resulting in a 100MB file.
                    
            * **Create a filesystem on the dummy file:** We need to format our "disk" so Linux can use it. `ext4` is a common Linux filesystem.
                
                Bash
                
                ```plaintext
                sudo mkfs.ext4 /root/devops_volume.img
                # When prompted, type 'y' and press Enter to proceed
                ```
                
                *Explanation:* `mkfs.ext4` creates an `ext4` filesystem. This command effectively formats our `devops_volume.img` file as if it were a blank disk.
                
            * **Mount the loop device:** Now, we'll attach our formatted image file to our `/mnt/devops_data` directory.
                
                Bash
                
                ```plaintext
                sudo mount -o loop /root/devops_volume.img /mnt/devops_data
                ```
                
                *Explanation:*
                
                * `mount`: The command used to attach a filesystem to a directory.
                    
                * `-o loop`: This option tells `mount` to use a loop device, treating the file `/root/devops_volume.img` as a block device.
                    
                * `/root/devops_volume.img`: The path to our disk image file.
                    
                * `/mnt/devops_data`: The mount point where the content of `devops_volume.img` will be accessible.
                    
        3. **Verify using** `df -h` and `mount | grep devops_data`: It's crucial to verify that the volume has been mounted correctly and is visible to the system.
            
            * **Verify with** `df -h`: This command shows disk space usage in a human-readable format.
                
                Bash
                
                ```plaintext
                df -h /mnt/devops_data
                ```
                
                *Expected Output:* You should see a line showing `/root/devops_volume.img` mounted on `/mnt/devops_data` with its size and usage.
                
                ```plaintext
                Filesystem      Size  Used Avail Use% Mounted on
                /dev/loop0       93M  2.6M   84M   3% /mnt/devops_data
                ```
                
                *(Note: The* `Filesystem` column might show `/dev/loop0` or similar, indicating it's a loop device.)
                
            * **Verify with** `mount | grep devops_data`: This specifically checks the `mount` command's output for our mount point.
                
                Bash
                
                ```plaintext
                mount | grep devops_data
                ```
                
                *Expected Output:*
                
                ```plaintext
                /root/devops_volume.img on /mnt/devops_data type ext4 (rw,relatime,seclabel,loop)
                ```
                
                *Explanation:* This output confirms that `devops_volume.img` is mounted on `/mnt/devops_data` with the `ext4` filesystem type and that it's a loop device.
                
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748707374055/0ad64001-a29d-4ae5-b180-c82d09fe8464.png align="center")
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748707478350/ebedaa80-4396-4163-a1ae-7c61a66c77e9.png align="center")
        
        Mount is not permanent, we can make it permanent by making entry in /etc/fstab.
        
        Great work on volume management! Now, let's dive into **Process Management & Monitoring**, another fundamental skill for maintaining healthy and efficient Linux servers. Understanding how to monitor and control processes is crucial for troubleshooting performance issues, managing resources, and ensuring system stability.
        
        ---
        
        ### 5\. Process Management & Monitoring: Keeping an Eye on Operations
        
        In a Linux environment, everything running on your system is a process. Being able to start, monitor, and gracefully terminate processes is a vital part of a DevOps engineer's toolkit. This task will walk through these essential steps using common command-line tools.
        
        **My Task 5:**  
        **Breakdown and Solutions:**
        
        1. **Start a background process (**`ping` [`google.com`](http://google.com) `> ping_test.log &`): We'll start a continuous `ping` command to [`google.com`](http://google.com) and redirect its output to a log file. The `&` symbol at the end is key, sending the process to the background so you can continue using your terminal.
            
            Bash
            
            ```plaintext
            ping google.com > ping_test.log &
            ```
            
            *Explanation: We need to provide elevated privileges for the file creation .*
            
            * `ping` [`google.com`](http://google.com): This command sends network packets to [`google.com`](http://google.com) and waits for replies, measuring latency.
                
            * `> ping_test.log`: This redirects the standard output of the `ping` command to a file named `ping_test.log`. This file will continuously grow with ping results.
                
            * `&`: This crucial symbol sends the `ping` process to the background. This means your terminal prompt will immediately return, and you can execute other commands while `ping` runs.
                
        2. **Use** `ps`, `top`, and `htop` to monitor it: Now that our background process is running, we'll use different tools to observe its status.
            
            * `ps` (Process Status): Provides a snapshot of currently running processes.
                
                Bash
                
                ```plaintext
                ps aux | grep "ping google.com"
                # You might also grep for "ping_test.log" if the above doesn't work well
                ps aux | grep "ping_test.log"
                ```
                
                *Expected Output (example):*
                
                ```plaintext
                your_user   1234  0.0  0.0  12345  6789 pts/0    S    10:30   0:00 ping google.com
                your_user   5678  0.0  0.0  98765  4321 pts/0    S+   10:30   0:00 grep --color=auto ping google.com
                ```
                
                *Explanation:* You're looking for the line that shows `ping` [`google.com`](http://google.com) (not the `grep` command itself). The second column (e.g., `1234` in the example) is the **Process ID (PID)**, which you'll need to kill the process later. `ps aux` shows all processes for all users, including those without a controlling terminal, with full format.
                
            * ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748707927703/240d3819-c0a7-4f2f-b989-60de75e9b36e.png align="center")
                
            * `top`: Provides a dynamic, real-time view of running processes, sorted by CPU usage.
                
                Bash
                
                ```plaintext
                top
                ```
                
                *Explanation:* After running `top`, you'll see a constantly updating list. Look for the `ping` process in the list. You might need to scroll down or press `P` to sort by CPU usage if it's consuming very little. Press `q` to exit `top`.
                
            * `htop`: A more user-friendly and interactive process viewer than `top`, often providing better visual cues and easier navigation. (You might need to install it first: `sudo apt install htop` or `sudo yum install htop`)
                
                Bash
                
                ```plaintext
                htop
                ```
                
                *Explanation:* Once `htop` is running, you'll see a colorful, interactive display. You can use your arrow keys to navigate and find the `ping` process. It provides real-time CPU, memory, and load averages. Press `F10` or `q` to exit `htop`.
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748708105324/b77d7cfd-a4e0-4333-ac29-fa3bd3e554c4.png align="center")
                
                  
                
        3. **Kill the process and verify it's gone:** Once you've identified the PID of your `ping` process, you can terminate it.
            
            * **Kill the process:**
                
                Bash
                
                ```plaintext
                kill <PID_of_ping_process>
                # Replace <PID_of_ping_process> with the actual PID you found using ps, top, or htop.
                # For example, if the PID was 1234:
                # kill 1234
                ```
                
                *Explanation:* The `kill` command sends a signal to a process. By default, it sends `SIGTERM` (signal 15), which is a polite request for the process to terminate. Most well-behaved processes will stop cleanly. If a process is stubborn, you might need `kill -9 <PID>` (SIGKILL), which forcibly terminates it, but use this as a last resort.
                
            * **Verify it's gone:** Check again using `ps` to confirm the process is no longer running.
                
                Bash
                
                ```plaintext
                ps aux | grep "ping google.com"
                # or
                ps aux | grep "ping_test.log"
                ```
                
                *Expected Output:* You should only see the `grep` command itself, not the `ping` process. This indicates the `ping` process has been successfully terminated.
                
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748708149925/475c5d00-def5-47a9-89c2-2da52bde2114.png align="center")
        
        Great job on the process management! You're now at the final task for Week 2, and it's a big one: **Automating Backups with Shell Scripting**. This task combines several crucial DevOps skills: shell scripting for automation, understanding file archiving (`tar`), date formatting, and scheduling with `cron`. This is where the power of automation truly shines!
        
        ---
        
        ### 6\. Automate Backups with Shell Scripting: The Power of Automation
        
        Automation is a cornerstone of DevOps, enabling repeatable, reliable, and efficient operations. Backups are a prime example of where automation is not just convenient but absolutely critical for disaster recovery and business continuity. This task will guide you through creating a simple, yet effective, backup script and scheduling it to run automatically.
        
        **My Task 6:**  
        **Breakdown and Solutions:**
        
        1. **Write a shell script to back up** `/devops_workspace` as `backup_$(date +%F).tar.gz`: We'll create a script that uses the `tar` command to archive and compress our `/devops_workspace` directory. The backup file name will include the current date.
            
            * **Create the backup directory:** First, ensure the `/backups` directory exists.
                
                Bash
                
                ```plaintext
                sudo mkdir -p /backups
                # The -p flag creates parent directories if they don't exist
                ```
                
            * **Create the script file:** Let's create `devops_`[`backup.sh`](http://backup.sh) inside `/backups`.
                
                Bash
                
                ```plaintext
                sudo vim /backups/devops_backup.sh
                # Or use vi, nano, or your preferred text editor
                ```
                
            * **Add the following content to the script:**
                
                Bash
                
                ```plaintext
                #!/bin/bash
                
                # Define variables
                BACKUP_DIR="/backups"
                SOURCE_DIR="/devops_workspace"
                # Get current date in YYYY-MM-DD format
                TIMESTAMP=$(date +%F)
                BACKUP_FILE="${BACKUP_DIR}/backup_${TIMESTAMP}.tar.gz"
                
                # Ensure the backup directory exists (though we created it manually earlier,
                # it's good practice to have this in the script for robustness)
                mkdir -p "$BACKUP_DIR"
                
                echo "Starting backup of ${SOURCE_DIR}..."
                
                # Create the tar.gz archive
                # -c: create archive
                # -z: filter archive through gzip (compress)
                # -f: use archive file or device
                tar -czf "$BACKUP_FILE" "$SOURCE_DIR"
                
                # Check if the tar command was successful
                if [ $? -eq 0 ]; then
                  # Display success message in green text
                  echo -e "\e[32mBackup of ${SOURCE_DIR} to ${BACKUP_FILE} created successfully!\e[0m"
                else
                  # Display error message in red text
                  echo -e "\e[31mError: Backup of ${SOURCE_DIR} failed!\e[0m"
                fi
                ```
                
                *Explanation of the script:*
                
                * `#!/bin/bash`: Shebang line, specifies the interpreter for the script.
                    
                * `BACKUP_DIR`, `SOURCE_DIR`, `TIMESTAMP`, `BACKUP_FILE`: Variables for easy management.
                    
                * `date +%F`: Formats the date as YYYY-MM-DD (e.g., 2025-05-31).
                    
                * `mkdir -p "$BACKUP_DIR"`: Ensures the backup destination exists.
                    
                * `tar -czf "$BACKUP_FILE" "$SOURCE_DIR"`: This is the core backup command. It creates a compressed archive of `/devops_workspace` with the specified filename.
                    
                * `if [ $? -eq 0 ]; then ... else ... fi`: This checks the exit status of the previous command (`tar`). If it's `0`, the command succeeded; otherwise, it failed.
                    
                * `echo -e "\e[32m... \e[0m"`: This uses ANSI escape codes to print the success message in green. `\e[32m` sets the color to green, and `\e[0m` resets the text color to default.
                    
            * **Make the script executable:**
                
                Bash
                
                ```plaintext
                sudo chmod +x /backups/devops_backup.sh
                ```
                
                *Explanation:* `chmod +x` grants execute permissions to the script, allowing it to be run like a program.
                
        2. **Schedule it using** `cron`: `cron` is a time-based job scheduler in Unix-like operating systems. We'll use `crontab` to add an entry that runs our backup script automatically.
            
            * **Edit your crontab:**
                
                Bash
                
                ```plaintext
                crontab -e
                ```
                
                *Explanation:* This command opens your user's crontab file in a text editor (usually `nano` or `vi`). If it's your first time, it might ask you to choose an editor.
                
            * **Add the cron job entry:** Add the following line at the end of the file. This example schedules the backup to run daily at 2:00 AM.
                
                Code snippet
                
                ```plaintext
                0 2 * * * /backups/devops_backup.sh >> /var/log/devops_backup.log 2>&1
                ```
                
                *Explanation of the cron entry:*
                
                * `0 2 * * *`: This is the cron schedule.
                    
                    * `0`: Minute (0-59) - at minute 0.
                        
                    * `2`: Hour (0-23) - at 2 AM.
                        
                    * `*`: Day of month (1-31) - every day.
                        
                    * `*`: Month (1-12) - every month.
                        
                    * `*`: Day of week (0-7, 0 or 7 is Sunday) - every day of the week.
                        
                * `/backups/devops_`[`backup.sh`](http://backup.sh): The full path to your script.
                    
                * `>> /var/log/devops_backup.log 2>&1`: This redirects both standard output (`1`) and standard error (`2`) to a log file named `devops_backup.log` in `/var/log`. The `>>` appends to the file, creating it if it doesn't exist. This is crucial for debugging cron jobs, as they run in the background without a direct terminal.
                    
            * **Save and exit the crontab file:** (e.g., `Ctrl+X`, then `Y`, then `Enter` for nano). The cron daemon will automatically pick up the changes.
                
            * **Verification (Optional but Recommended):** To quickly test your script without waiting for cron, you can run it manually:
                
                Bash
                
                ```plaintext
                sudo /backups/devops_backup.sh
                ```
                
                Then check the `/backups` directory for the `backup_YYYY-MM-DD.tar.gz` file and inspect `/var/log/devops_backup.log` for the success message.
                
                ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748708701946/bff87436-992a-4ae5-8466-c8fe8c497490.png align="center")
                
        
        ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1748708826970/863275a7-d852-4841-b19b-32a1aefcb800.png align="center")
        
        Week 2 of the #90DaysOfDevOps challenge has been an incredibly insightful journey into the practicalities of **Linux server management**. I've moved beyond theoretical concepts to execute real-world tasks that are fundamental to any DevOps engineer's toolkit. From setting up secure user environments and meticulously managing file permissions, to delving deep into log analysis with powerful command-line utilities like `grep`, `awk`, and `sed`, each task reinforced the critical importance of attention to detail and efficiency.
        
        Tackling volume and process management provided crucial hands-on experience in maintaining system health and resource allocation. Finally, automating backups with shell scripting and `cron` truly brought the DevOps philosophy of "automate everything" to life, highlighting how scripting can ensure reliability and free up valuable time.
        
        I'm leaving Week 2 with significantly enhanced confidence in navigating, controlling, and troubleshooting Linux systems. This foundational knowledge is invaluable, and I'm eager to build upon it as I continue my #90DaysOfDevOps journey. The path to becoming a proficient DevOps engineer is challenging, but these hands-on experiences are making every step worthwhile!