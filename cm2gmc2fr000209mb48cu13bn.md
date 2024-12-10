---
title: "The Ultimate GitHub Command Cheat Sheet"
datePublished: Sat Oct 19 2024 20:35:48 GMT+0000 (Coordinated Universal Time)
cuid: cm2gmc2fr000209mb48cu13bn
slug: the-ultimate-github-command-cheat-sheet
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/wX2L8L-fGeA/upload/85ac9b2e38fa76318600e8dfefdd8dc6.jpeg
tags: github, version-control, gitlab, version-control-systems

---

GitHub is a must-have tool for developers. Mastering its commands can boost your productivity. Whether a beginner or a pro, this cheat sheet will help you navigate GitHub like a boss. Let’s dive in!

## 1\. **git init**

**Initialize a new Git repository**

*Example:* You’re starting a new project called **AwesomeApp**. Navigate to your project directory and initialize it:

**cd AwesomeApp  
git init**

## 2\. **git clone**

**Clone an existing repository**

*Example:* You found an interesting project on GitHub and want to contribute. Clone the repository to your local machine:

**git clone https://github.com/username/project\_path.git**

## 3\. **git add**

**Add files to the staging area**

*Example:* You’ve made changes to the test.txt and want to stage it for commit:

**git add test.txt**

To add all changes:

**git add .**

## 4\. **git commit**

**Commit changes to the repository**

*Example:* You’ve staged your changes and are ready to commit them with a meaningful message:

**git commit -m “Add new feature to homepage“**

## 5\. **git status**

**Check the status of your repository**

*Example:* Before committing, you want to see which files are staged, modified, or untracked:

**git status**

## 6\. **git push**

**Push changes to a remote repository**

*Example:* You’ve committed your changes and want to push them to the main/master branch on GitHub:

**git push origin main**

## 7\. **git pull**

**Fetch and merge changes from a remote repository**

*Example:* Before starting your work, you want to ensure your local repository is up-to-date with the remote repository:

**git pull origin main**

## 8\. **git branch**

**List, create, or delete branches**

*Example:* You want to create a new branch for a feature you’re working on:

**git branch feature-branch**

To switch to the new branch:

**git checkout feature-branch**

## 9\. **git merge**

**Merge branches**

*Example:* You’ve finished working on feature-branch and want to merge it into **main**:

**git checkout main**

**git merge feature-branch**

## 10\. **git log**

**View commit history**

*Example:* You want to see the history of commits in your repository:

**git log**

---

Happy coding! 🚀