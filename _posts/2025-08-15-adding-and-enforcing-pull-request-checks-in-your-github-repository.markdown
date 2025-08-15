---
title: Adding and Enforcing Pull Request Checks in Your GitHub Repository
date: 2025-08-15 23:52:00 +05:30
categories:
- go
tags:
- CI\CD
- devops
- github-actions
layout: post
---

Recently, I have been working on a project where developers merge code into the main branch without verifying if the changes function correctly. As a result, every two to three days, we encounter issues where nothing works. We need to address this.

To solve this problem, we suggest implementing pull request checks. These checks ensure that every contribution to our repository meets required standards, passes tests, and adheres to best practices. In this blog, we’ll explore how to set up a pull request check using GitHub Actions and enforce it as a mandatory step before merging.

### **Why Use Pull Request Checks?**

Pull request checks help:

- **Automate Quality Assurance**: Automatically run tests and linting to catch issues early.
- **Enforce Standards**: Ensure all contributions meet coding and security standards.
- **Streamline Collaboration**: Provide immediate feedback to contributors, reducing review cycles.

### **Step 1: Setting Up a Pull Request Check Workflow**

GitHub Actions makes it easy to define workflows for pull request checks. Below is an example workflow file (pull-request-check.yml) that:

- Cleans the runner environment.
- Checks out the latest code.
- Sets up Go and installs dependencies.
- Runs tests for changed files.
- Optionally, runs linting.

### **Example Workflow File**

```
name: Pull Request Check
on:
  pull_request:
    branches:
      - main
jobs:
  check:
    steps:
      - name: Checkout latest code
        uses: actions/checkout@v4
        with:
          fetch-depth: 0
          ref: ${{ github.ref }}
          fetch-tags: true
      - name: Setup Go
        uses: actions/setup-go@v4.1.0
        with:
          go-version: "1.24"
          cache: false
      - name: Install Dependencies
        run: |
          sudo apt install -y jq
          go install github.com/playwright-community/playwright-go/cmd/playwright@latest
          playwright install --with-deps chromium
      - name: Execute tests
        env:
          HEADLESS_BROWSER: ${{ vars.HEADLESS_BROWSER }}
        run: go test -v ./..

```

---

### **Step 2: Commit and Push the Workflow File**

Save the workflow file as pull-request-check.yml in your repository. Then, commit and push it to the `main` branch:

```
git add .github/workflows/pull-request-check.yml
git commit -m "Add pull request check workflow"
git push origin main
```

---

### **Step 3: Enforce the Pull Request Check**

To make the pull request check mandatory, configure branch protection rules in your GitHub repository.

### **Steps to Enforce the Check:**

1. **Navigate to Repository Settings**:
    - Go to your repository on GitHub.
    - Click on the `Settings` tab.
2. **Set Up Branch Protection Rules**:
    - Under the `Code and automation` section, click on `Branches`.
    - Click the `Add branch ruleset` button.
3. **Define the Rule**:
    - In the `Branch name pattern` field, enter `main` (or the branch you want to protect).
    - Check the `Require status checks to pass before merging` option.
    - Under `Status checks that are required`, select the workflow name (e.g., `Pull Request Check`).
4. **Save the Rule**:
    - Click the `Save changes` button.

---

### **Step 4: Verify the Setup**

1. Create a new pull request targeting the `main` branch.
2. The pull request check will automatically run.
3. The pull request cannot be merged until the check passes.