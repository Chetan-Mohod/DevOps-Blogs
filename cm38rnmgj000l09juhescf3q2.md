---
title: "Step-by-Step Guide to Setting Up GitHub Actions for CI/CD: Building Your Own CI/CD Pipeline Easily"
datePublished: Fri Nov 08 2024 13:22:18 GMT+0000 (Coordinated Universal Time)
cuid: cm38rnmgj000l09juhescf3q2
slug: step-by-step-guide-to-setting-up-github-actions-for-cicd-building-your-own-cicd-pipeline-easily
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1731072062412/b43fbb00-49e2-4c9a-815a-02fd0faf99a9.webp
tags: github, github-actions, github-actions-1, github-actions-tutorial

---

### Introduction: Why GitHub Actions?

If you're working on a project, you've probably faced the challenge of keeping your code organized and bug-free. Every time you make changes, there’s a risk of breaking something. GitHub Actions is here to help. It automates tasks, runs tests, and ensures everything works smoothly before changes go live. In simple words, it’s a tool that helps you catch errors before they reach users.

Imagine you’re building a web app with a team. Everyone’s working on different features. As soon as you push a new feature, GitHub Actions can test it automatically to make sure nothing broke. If there’s an issue, you’re notified immediately, allowing for a faster fix.

### What is CI/CD, and Why Should You Care?

CI/CD stands for Continuous Integration and Continuous Deployment/Delivery. In simple terms:

* **Continuous Integration (CI)** is about automatically testing and integrating code changes.
    
* **Continuous Deployment (CD)** means deploying those changes to a live environment when tests pass.
    

Think of CI/CD as your quality-control assistant. It ensures that every change to your project is stable and ready for release.

---

### Real-Life Example: Let’s Set Up a Simple CI/CD Workflow

Let's say you’re working on a Node.js app, and you want to set up GitHub Actions to automatically test your code whenever you make changes. This example will cover the basics of creating a workflow that runs tests on every push to your repository.

---

### Step 1: Creating a GitHub Repository

First, if you don’t have a repository, create one on GitHub:

1. Go to GitHub and click **New Repository**.
    
2. Name your repository and make it public or private as needed.
    
3. Once created, you can add your code or simply initialize it with a [**README.md**](http://README.md) file.
    

---

### Step 2: Adding a GitHub Actions Workflow

Now, let’s set up GitHub Actions for this project. GitHub Actions uses YAML files to define workflows.

1. In your GitHub repository, go to the **Actions** tab.
    
2. GitHub will show some suggested workflows. For our Node.js app, click **Set up this workflow** under “Node.js.”
    

GitHub will create a YAML file (**.github/workflows/main.yml**) with some default steps. This file tells GitHub what to do every time you make changes.

---

### Step 3: Configuring the Workflow File

In the **main.yml** file, you’ll see a few sections. Let’s break it down:

1. **Name**: This is the name of your workflow. You can name it **CI Pipeline** or anything you like.
    
2. **On**: This section specifies when the workflow should run. In our case, we want it to run on every push. It looks like this:
    
    * **on:**
        
        * **push:**
            
            * **branches:**
                
                * **main**
                    
3. **Jobs**: Here’s where the actual tasks go. We’ll create a job named **build** that will:
    
    * Set up a Node.js environment.
        
    * Install dependencies.
        
    * Run tests.
        
    
    Here’s what that section might look like:
    
    * **jobs:**
        
        * **build:**
            
            * **runs-on: ubuntu-latest**
                
            * **steps:**
                
                * **name: Checkout code**
                    
                    * **uses: actions/checkout@v2**
                        
                * **name: Set up Node.js**
                    
                    * **uses: actions/setup-node@v2**
                        
                    * **with:**
                        
                        * **node-version: '14'**
                            
                * **name: Install dependencies**
                    
                    * **run: npm install**
                        
                * **name: Run tests**
                    
                    * **run: npm test**
                        

---

### Step 4: Commit and Push Your Workflow

Once you’ve set up your YAML file, commit it to your repository:

1. In GitHub, click **Start commit**, add a commit message like **Set up CI pipeline**, and click **Commit new file**.
    
2. As soon as you push this, GitHub Actions will trigger the workflow and start testing your code.
    

### Step 5: Check Your Workflow Results

1. Go back to the **Actions** tab on GitHub.
    
2. You’ll see a new workflow run. Click on it to view the steps. If everything worked, you’ll see green checkmarks next to each step.
    

If something failed, GitHub will show an error message, helping you fix the issue before it goes live.

---

### Going Further: Adding CD to Your Pipeline

To automatically deploy code when tests pass, add a **deployment job**. This could involve deploying to a server, cloud platform, or anywhere you host your app.

Here's an example addition to your YAML file:

* **deploy:**
    
    * **needs: build**
        
    * **runs-on: ubuntu-latest**
        
    * **steps:**
        
        * **name: Deploy to server**
            
            * **run: echo "Deploying to server..."**
                

This example only echoes **Deploying to server...**, but in a real-world scenario, you’d include actual deployment commands here.

---

### Conclusion: Benefits of GitHub Actions in CI/CD

By now, you’ve set up a basic CI/CD pipeline using GitHub Actions. Here’s a recap of why this matters:

* **Saves Time**: Automates repetitive tasks like testing and deployment.
    
* **Reduces Errors**: Catches bugs early by testing with each change.
    
* **Streamlines Workflow**: Makes collaboration easier, especially in teams.
    

GitHub Actions is a powerful tool that can make your development process faster and more reliable. Start experimenting with it to discover more workflows and possibilities for your projects!