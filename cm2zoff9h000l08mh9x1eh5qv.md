---
title: "Git vs. GitHub: Understanding the Difference with Simple Examples"
datePublished: Sat Nov 02 2024 04:42:01 GMT+0000 (Coordinated Universal Time)
cuid: cm2zoff9h000l08mh9x1eh5qv
slug: git-vs-github-understanding-the-difference-with-simple-examples
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1730522394574/71e418c9-ede8-499c-a286-4ffb986907b4.jpeg
tags: github, git, devops, gitlab, github-actions-1, gitcommands, 90daysofdevops, 90daysofdevops-chanllenge, 90daysofdevops-devops-projectdevelopment-nonitbackground-github-docker-cloudplatforms-ec2-aws-elasticbeanstalk-lambdafunctions-devopspipelines-terraform-jenkins-docker-devsecops-scm-git-gitlab-bitbucket-buildtools-griddle-maven-ant-msbuild-monitoringtools-prometheus-grafana-ansible-ai-chatgpt-valueaddition-realworldproblems, 90daysofdevopschallenge

---

When starting software development, you’ll often hear the terms **Git** and **GitHub**. They’re two of the most important tools in coding, especially when working with others, but they can be confusing at first. Let’s break it down in a way that’s easy to understand even if you’re new to programming.

### What is Git?

Imagine you’re working on a big art project, maybe a digital drawing. Every few minutes, you save a version of your work so that you can go back if you mess up or want to experiment without losing your progress. Now, let’s add a twist: what if you’re working with a friend on this project? You’d want to save different versions of your work so you can see what changes each person made, right? That’s where **Git** comes in.

Git is like a powerful “save” system for code. It keeps track of all the changes in your project, from start to finish. It lets you create different “branches” to try new things, and if you like your changes, you can “merge” them back into the main project. Git does all this locally, meaning it works on your computer, without needing an internet connection.

#### Example

Let’s say you’re building a website. You make a branch in Git called “new-feature” and start coding a login page. But then you realize you need to fix a bug on the homepage. You can simply switch back to the main branch, make the fix, and then go back to your “new-feature” branch without losing any of your work. Git makes all this possible by storing versions safely.

### What is GitHub?

So now you have Git, which helps you track and save different versions of your code. But what if you’re working with a team, maybe some friends or colleagues from other places? You’d need a way to share your code so that everyone can work together. That’s where **GitHub** comes in.

GitHub is like a social media site for code, but instead of sharing photos or updates, you’re sharing code. It stores Git repositories online (a repository, or “repo,” is where all your project files and versions are kept). GitHub makes it easy for your whole team to access, review, and add to the project from anywhere.

#### Example

Imagine you’re working on that website project with three friends. You all save your code in a GitHub repository. You can see each other’s updates, make suggestions, or even work on the same files without overwriting each other’s work. When you’re done with a new feature, you can create a **pull request** (a way to suggest changes), and your friends can review your work before adding it to the main project. GitHub helps keep everything organized and lets everyone contribute easily.

### Why Do Developers Use Both?

Git and GitHub go hand-in-hand, like peanut butter and jelly. Git is for local version control on your computer, while GitHub lets you share and manage code online. Developers use Git to keep track of their work, make changes, and test ideas. Then, they use GitHub to collaborate, allowing teammates to review and merge their changes into one main project.

### Quick Comparison of Git and GitHub

| **Feature** | **Git** | **GitHub** |
| --- | --- | --- |
| **What it is** | Local version control tool | Online platform for sharing Git repositories |
| **Where it works** | On your computer (no internet needed) | Online, using the internet |
| **Main purpose** | Save versions of code, track changes locally | Share code, collaborate, and review contributions |
| **Who uses it** | Individuals working alone or in teams | Teams working together remotely |

### How Do You Use Git and GitHub Together?

Think of Git as your personal workspace and GitHub as the library where you publish your work so others can read or add to it. Here’s how you’d typically use both:

1. **Initialize Git**: You start by setting up Git on your computer to track your project.
    
2. **Create a GitHub Repository**: You create a place on GitHub to store your project files and code versions online.
    
3. **Commit Changes with Git**: Each time you make a change (like adding a new feature), you save it with a **commit** in Git, which is like a “checkpoint.”
    
4. **Push to GitHub**: When you’re ready to share your changes, you **push** them from your local Git to your GitHub repository.
    
5. **Collaborate**: Teammates can then pull your changes from GitHub to their local machines, make their updates, and push them back.
    

### Real-Life Example: Writing a Book with Friends

Let’s put it into another context that doesn’t involve coding. Say you’re co-writing a book with friends. Here’s how you’d use Git and GitHub:

* **Using Git**: Each of you writes your part on your computer and uses Git to track changes in your chapters. If you rewrite a paragraph, Git saves the new version but also keeps the old one in case you want to go back.
    
* **Using GitHub**: Once you’re happy with your chapter, you upload it to GitHub. Now, all your co-authors can see your work, suggest edits, or add their own chapters. When someone suggests a major edit, they can create a pull request, and the team can discuss and approve it before adding it to the final draft.
    

### Key Terms in Simple Words

* **Repository (Repo)**: Your project folder that Git and GitHub keep track of.
    
* **Branch**: A separate version of your project to try new things.
    
* **Commit**: Saving a specific change or update to your project.
    
* **Push**: Sending your local changes to GitHub.
    
* **Pull Request**: Asking to add your changes to the main project (great for team reviews).
    

### In Summary

* **Git** is for local version control, keeping your work organized and safe on your own machine.
    
* **GitHub** is for sharing and managing code with others online, making teamwork easy and organized.
    

Together, they’re powerful tools for individual coders and teams alike, helping you save, experiment, and share your work smoothly. So whether you’re coding solo or with a team, Git and GitHub are two tools you’ll rely on a lot!