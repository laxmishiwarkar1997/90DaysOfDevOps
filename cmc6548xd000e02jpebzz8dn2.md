---
title: "Mastering Git and GitHub: A Deep Dive into Week 4"
seoTitle: "Mastering Git & GitHub: Real-World SRE Lessons from Week 4 of 90 Days "
seoDescription: "Completed Week 4 of the #90DaysOfDevOps challenge with hands-on Git & GitHub tasks. Faced real-world issues like GitHub push protection, secret leaks, and S"
datePublished: Sat Jun 21 2025 11:13:11 GMT+0000 (Coordinated Universal Time)
cuid: cmc6548xd000e02jpebzz8dn2
slug: mastering-git-and-github-a-deep-dive-into-week-4
tags: linux, github, version-control, 90daysofdevops, configurationmanagement

---

## Challenge 1

Welcome to the latest installment of my #90DaysOfDevOps journey! This past week, we tackled a comprehensive Git and GitHub challenge that pushed our understanding of version control, collaboration, and advanced Git concepts. It was an incredibly insightful experience, and I'm excited to share my journey and learnings with you.

This challenge, guided by the excellent lessons from Shubham Bhaiya, focused on solidifying our understanding of fundamental Git commands, repository management, branching strategies, and authentication methods. Let's break down each task and explore the "why" behind the "how."

### Task 1: Fork and Clone the Repository – The Foundation

Every collaborative project on GitHub starts with a fork. For those new to it, forking a repository creates a personal copy of the original project on your GitHub account. This allows you to experiment, make changes, and contribute back without directly altering the main project.

**My Steps:**

1. **Forked the** `90DaysOfDevOps` repository: I navigated to the [original repository](https://www.google.com/search?q=https://github.com/your-username/90DaysOfDevOps) (replace with the actual repo link if available) and clicked the "Fork" button. This created a copy under my GitHub profile.
    
2. **Cloned my fork locally:** With the fork in place, I brought it down to my local machine using the `git clone` command. It's crucial to clone *your forked repository*, not the original, as you'll be pushing changes to your personal copy.
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750498257455/e3c58671-a9d8-4363-a3e0-c6ae78fdf8c9.png align="center")
    
3. *Why this is important:* Forking provides a safe sandbox for your contributions, and cloning brings the project files to your local environment, enabling you to work on them.
    

### Task 2: Initialize a Local Repository and Create a File – Your First Contribution

This task focused on setting up our local working environment and making our first meaningful commit.

**My Steps:**

1. **Created a dedicated challenge directory:** To keep things organized within the larger `90DaysOfDevOps` structure, I created a new directory.
    
2. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750498320778/6d18d204-5081-4824-9b38-863017fdfa96.png align="center")
    
3. **Initialized a Git repository:** Even though we cloned a repository, `git init` in a subdirectory is useful for starting a new, isolated Git project within a larger one, or for tracking changes in a directory that wasn't initially a Git repo. In this specific challenge context, it allows us to track changes *within* our `week-4-challenge` directory independently if needed, though typically, a single Git repo manages the entire cloned project.
    
4. **Created and committed my first file:** I created `info.txt`, added some introductory content, staged it, and then committed it.
    
    *Why this is important:* `git init` marks a directory as a Git repository, `git add` stages changes for the next commit, and `git commit` records those changes with a descriptive message in your local history. These are the atomic operations of version control.
    
5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750498549246/8a657dd8-9d2a-48b4-98ed-9ba8c447ff87.png align="center")
    

### Task 3: Configure Remote URL with PAT and Push/Pull – Connecting to the Cloud

Pushing and pulling changes from a remote repository is where collaboration truly begins. This task highlighted the use of Personal Access Tokens (PATs) for authentication.

**My Steps:**

1. **Configured Remote URL with PAT:** This step involved updating the remote URL to embed my PAT. While convenient for this exercise, it's crucial to remember that this is **not recommended for production environments due to security risks.**
    
    ```bash
    git remote set-url origin https://<your-username>:<your-PAT>@github.com/<your-username>/90DaysOfDevOps.git
    ```
    
    *Note: If* `origin` didn't exist, `git remote add origin ...` would be used instead.
    
2. **Pushed my commit:**
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750499564379/1162aba2-4d23-48b8-a5dd-4024f85020c0.png align="center")
    
3. The `-u origin main` flag sets the upstream branch, so subsequent `git push` and `git pull` commands don't require specifying `origin main`.
    
4. **(Optional) Pulled remote changes:**
    
    ```bash
    git pull origin main
    ```
    
    *Why this is important:* Connecting your local repository to a remote one (like GitHub) allows you to share your changes with others and receive their updates. PATs provide a secure way to authenticate without exposing your GitHub password.
    

### Task 4: Explore Your Commit History – Understanding Your Journey

The `git log` command is your best friend for understanding the history of your project.

**My Steps:**

1. **Viewed the Git Log:**
    
2. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750499884486/d57d5552-f717-413f-bf75-c5960e9251fe.png align="center")
    
    This command displayed a chronological list of commits, each with a unique hash, author information, date, and commit message.
    
    *Why this is important:* `git log` helps you trace changes, understand who did what and when, and even revert to previous states if necessary. It's essential for debugging and maintaining project integrity.
    

### Task 5: Advanced Branching and Switching – The Power of Parallel Development

Branching is arguably one of Git's most powerful features, enabling parallel development and isolating new features or bug fixes.

**My Steps:**

1. **Created a new branch** `feature-update`:
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750499941486/0c2037b9-bd9b-421f-b830-f5f06b9d4bfa.png align="center")
    
      
    
2. **Switched to the new branch:**
    
3. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750499951556/b311dbb1-d419-4b00-945b-3f53fdb5c89a.png align="center")
    
4. **Modified** `info.txt` and committed changes:
    
5. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750500005055/d05b396d-c917-4159-81b4-d7c3a2770176.png align="center")
    
    *Why this is important:* Branching allows developers to work on new features or bug fixes independently without affecting the stable `main` branch. This prevents regressions and facilitates concurrent development. Once a feature is complete and reviewed, it can be merged back into `main`.
    
    **The Merge via Pull Request:** After pushing my `feature-update` branch, I created a Pull Request (PR) on GitHub from `feature-update` to `main`. This is the standard collaborative workflow: changes are proposed, reviewed by teammates, and then merged, ensuring code quality and consistency.
    
6. ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750500433950/2a87eac2-2e9c-4e23-9377-440406df677d.png align="center")
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1750500676196/daf815fd-a6d2-482d-8b08-48d55a8224b6.png align="center")
    

### Task 6: Explain Branching Strategies – The Collaborative Advantage

This was a critical thinking task, requiring me to articulate the importance of branching strategies. My [`solution.md`](http://solution.md) file documented all the commands and provided the following explanation:

**Why Branching Strategies are Important in Collaborative Development:**

Branching strategies are crucial for several reasons in team environments:

1. **Isolating Features and Bug Fixes:** Each new feature or bug fix can be developed on its own dedicated branch. This isolation ensures that ongoing work doesn't interfere with the stability of the main codebase, preventing incomplete or buggy code from reaching production.
    
2. **Facilitating Parallel Development:** Multiple developers can work on different features concurrently on their respective branches. This significantly speeds up development cycles as teams aren't waiting for one task to finish before starting another.
    
3. **Reducing Merge Conflicts:** While branching doesn't eliminate merge conflicts, well-defined strategies (like Git Flow or GitHub Flow) aim to minimize them by keeping branches short-lived and merging frequently. When conflicts do arise, they are typically smaller and easier to resolve.
    
4. **Enabling Effective Code Reviews:** Pull Requests, which are built upon branching, provide a structured mechanism for code review. Developers can review changes on a feature branch before they are merged into the main line, ensuring code quality, catching bugs early, and sharing knowledge. This collaborative review process leads to more robust and maintainable code.
    

### Bonus Task: Explore SSH Authentication – Enhanced Security and Convenience

The bonus task introduced SSH authentication, a more secure and often more convenient alternative to HTTPS with PATs for frequent interactions.

**My Steps:**

1. **Generated an SSH Key:**
    
    ```bash
    ssh-keygen
    ```
    
    This command generated a public and private key pair. The public key is shared with GitHub, while the private key remains secure on my machine.
    
2. **Added Public Key to GitHub:** I copied the contents of `~/.ssh/id_`[`ed25519.pub`](http://ed25519.pub) and added it to my GitHub account under "SSH and GPG keys" settings.
    
3. **Switched Remote URL to SSH:**
    
    ```bash
    git remote set-url origin git@github.com:<your-username>/90DaysOfDevOps.git
    ```
    
4. **Pushed using SSH:**
    
    ```bash
    git push origin feature-update
    ```
    
    *Why this is important:* SSH provides a more secure and password-less way to interact with GitHub after initial setup. Instead of repeatedly entering a PAT (or password with HTTPS), SSH uses cryptographic keys for authentication, offering a smoother and more secure workflow.
    

### Conclusion and Submission

This challenge was a fantastic way to solidify my Git and GitHub skills. From the basics of cloning and committing to the nuances of branching strategies and SSH authentication, every step reinforced crucial concepts.

**My Submission Process:**

1. **Pushed my final work:** Ensured my `feature-update` branch, along with the updated [`solution.md`](http://solution.md) file, was pushed to my fork.
    
2. **Created a Pull Request (PR):** Opened a PR from my `feature-update` branch to the `main` branch of the original `90DaysOfDevOps` repository. My PR title was descriptive: "Week 4 Challenge - DevOps Batch 9: Git & GitHub Advanced Challenge," and the description summarized my process and listed the commands used.
    

I'm incredibly grateful for these challenges, as they provide hands-on experience that theoretical knowledge alone cannot. Onto Week 4 Challenge 2!

---

---