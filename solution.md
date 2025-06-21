# ✅ Week 4 Challenge: Git & GitHub Basics – Solution

## 👤 Author: Laxmi Shiwarkar
> Part of the #90DaysOfDevOps series by Shubham Bhaiya  
> Folder: `2025/git/01_Git_and_Github_Basics/week-4-challenge`

---

## 📚 Tasks 1–4: Git Commands Used

### 🔹 Task 1: Fork and Clone the Repository

```bash
# Clone forked repo using HTTPS
git clone https://github.com/laxmishiwarkar1997/90DaysOfDevOps.git

# Navigate into the appropriate folder
cd 2025/git/01_Git_and_Github_Basics
# Create directory for the challenge
mkdir week-4-challenge
cd week-4-challenge

# Initialize a new Git repository
git init

# Create a file
echo "Hi, I'm Laxmi! DevOps enthusiast 🚀" > info.txt

# Stage and commit
git add info.txt
git commit -m "Initial commit: Add info.txt with introductory content"

