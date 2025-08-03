---
title: "Week 4: Git & GitHub Advanced Challenge : A Deep Dive into Real-World Version Control"
seoTitle: "Git & GitHub Advanced Challenge – A Deep Dive into Real-World Version"
seoDescription: "Git & GitHub Advanced Challenge – A Deep Dive into Real-World Version Control"
datePublished: Mon Jul 07 2025 10:13:42 GMT+0000 (Coordinated Universal Time)
cuid: cmcsy1ddk000i02l83zu3dfau
slug: week-4-git-and-github-advanced-challenge-a-deep-dive-into-real-world-version-control

---

# Challenge 2

---

## Introduction

This week’s challenge focused on **advanced Git concepts** essential for any DevOps professional. Whether working solo or in a team, mastering Git’s powerful features like **pull requests, stashing, resetting, and branching strategies** helps keep workflows efficient and collaborative.

Here’s what I worked on in Week 4, and how it deepened my understanding of Git in real-world DevOps environments.

---

## Task 1: Pull Requests – Collaborating Like a Pro

### Scenario

Created a new feature, committed it to a branch, and opened a **Pull Request (PR)** to merge into the main branch.

### Steps I Followed:

Created PR on GitHub, requested a review, and merged after approval.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751881491575/0bfaf8ad-30d0-4771-bfa9-ee7ca6514cdf.png align="center")

### Key Takeaways:

* Always write clear PR descriptions.
    
* Use checklists to verify readiness.
    
* Respect code review comments — they improve your code and understanding.
    

---

## Task 2: Undoing Changes – Reset & Revert

### Scenario

Accidentally committed the wrong code and needed to undo it using different Git reset modes.

### Commands Used:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751881757937/b3e95972-bff6-4509-b5cc-24f08157f6b0.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751881909332/a2485aa3-29d3-4b03-b38b-394911066ad3.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751882051255/65eae938-4f75-49b5-8117-b4823830f47f.png align="center")

```plaintext
bashCopyEditgit reset --soft HEAD~1     # Keeps changes staged
git reset --mixed HEAD~1    # Unstages changes but keeps files
git reset --hard HEAD~1     # Discards everything
git revert HEAD             # Safely creates a new commit that undoes the previous one
```

### Learning:

* Use **revert** in public branches to avoid rewriting history.
    
* Use **reset** in local/private branches when safe to discard/rewrite.
    

---

## Task 3: Stashing – Saving Work Temporarily

### Scenario

Needed to switch branches but didn’t want to commit incomplete changes.

### Commands Used:

```plaintext
bashCopyEditgit stash
git checkout main
git stash pop  # Also explored git stash apply
```

### Notes:

* `git stash pop` removes the stash after applying.
    
* `git stash apply` keeps the stash intact for reuse.
    
* Great for mid-task interruptions or quick hotfixes!
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751882221127/b1a95c7a-02c8-47f6-b1cd-01f6fbdc1672.png align="center")
    

---

## Task 4: Cherry-Picking – Applying Specific Commits

### Scenario

Needed a bug fix from another branch without merging all its changes.

### Commands Used:

```plaintext
git log --oneline
git cherry-pick <commit-hash>
```

Resolved conflicts and used `git cherry-pick --continue` to finish.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751882359451/512f4f87-142f-46b2-890b-64fb9dbcaa13.png align="center")

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751882637343/f06c865a-5b80-4b3b-9761-645cfa8c311d.png align="center")

Conflict resolved manully

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751883045092/69a149d8-5b78-41d0-923f-1d42d4f862b8.png align="center")

:

### Risks:

* Cherry-picking can introduce **duplicate commits**.
    
* Best for isolated fixes, not large features.
    

---

## Task 5: Rebasing – Keeping a Clean History

### Scenario

My branch was behind `main` and needed updates without merge commits.

### Commands Used:

```plaintext
git fetch origin main
git rebase origin/main
git rebase --continue
```

### Key Difference:

* `merge` keeps all commits but adds a merge commit.
    
* `rebase` rewrites history to make it linear and clean.
    

### Best Practices:

* Only rebase local changes.
    
* Avoid rebasing shared branches without coordination.
    
* ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1751883146121/ffafb020-adba-448f-8f2f-3bab310b6925.png align="center")
    

---

## Task 6: Industry Branching Strategies

I explored and simulated these workflows using Git branches:

1. **Git Flow** – Feature, Release, Hotfix branches
    
2. **GitHub Flow** – Main + Feature branches
    
3. **Trunk-Based Development** – CI/CD + short-lived feature branches
    

### Which One’s Best?

| Strategy | Pros | Cons |
| --- | --- | --- |
| Git Flow | Structured, good for releases | Can be heavy for fast-paced CI/CD |
| GitHub Flow | Simple, ideal for GitHub projects | Lacks release branch support |
| Trunk-Based Dev | Great for CI/CD | Requires automation & discipline |

---

---

## Bonus: Screenshot from My Solution

> “Used `git reset --soft HEAD~1` to undo a commit while keeping changes staged. Super handy during experimentation!”  
> “Rebased my feature branch with `origin/main` to maintain a linear commit history. Less clutter, more clarity!”

---

## Conclusion

This week gave me practical, hands-on exposure to Git’s advanced capabilities. I now feel more confident contributing to collaborative projects and managing real-world version control challenges.

Thanks to #90DaysOfDevOps for pushing us to go depth of the concepts.

---

---

#90DaysOfDevOps #Git #GitHub #VersionControl #DevOps #Rebase #CherryPick #PullRequest #CI\_CD #TechBlog