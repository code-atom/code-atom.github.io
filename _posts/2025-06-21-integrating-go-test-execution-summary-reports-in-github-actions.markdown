---
title: Integrating Go Test Execution Summary Reports in GitHub Actions
date: 2025-06-21 11:31:00 +05:30
layout: post
---

GitHub Actions is a powerful Continuous Integration/Continuous Deployment (CI/CD) tool that enables developers to automate workflows directly within their repositories. When working with Go projects, generating and publishing test execution summary reports can provide valuable insights into test results, simplifying debugging and tracking. This article outlines how to integrate Go test execution summary reports into your GitHub Actions workflow using only the `go-ctrf-json-reporter`.

## **Prerequisites**

Before you begin, ensure that you have the following:

1. Your repository contains Go tests.

2. You have a GitHub Actions workflow file set up.

3. The essential tool `go-ctrf-json-reporter` is installed in your workflow.

## **Setting Up GitHub Action for Go Tests with CTRF JSON Reporter**

To define a GitHub Action for running Go tests with the go-ctrf-json-reporter, you can create a workflow file (e.g., .github/workflows/execute-tests.yml) in your repository. Below is an example of how to set up the workflow:

    name: Go Test with CTRF Reporter
    on:
      push:
        branches:
          - main
      workflow_dispatch:
    jobs:
      test:
        runs-on: ubuntu-latest
        steps:
          - name: Checkout code
            uses: actions/checkout@v2
          - name: Set up Go
            uses: actions/setup-go@v2
            with:
              go-version: '1.16'# Specify the Go version you want to use
          - name: Install Dependencies
            run: go mod tidy
          - name: Execute tests
            run: |
              cd tests
              go test -json -p=4 -timeout 3600s 2>&1 | tee ./gotest.json || true
          - name: Generate test reports
            if: always()
            run: |
              go-ctrf-json-reporter -output ctrf-report.json < gotest.json
          - name: Publish Test Summary Results
            if: always()
            run: npx github-actions-ctrf test_output/ctrf-report.json
          - name: Upload Test Results
            if: always()
            uses: actions/upload-artifact@v2
            with:
              name: test-results
              path: ./gotest.json

### **Explanation of the Workflow Steps:**

1. **Triggering Events**: The workflow is triggered on `push` events to the `main` branch or can be initiated manually.

2. **Checkout Code**: The `actions/checkout` step checks out your repository code.

3. **Set up Go**: The `actions/setup-go` step sets up the specified version of Go.

4. **Install Dependencies**: This step runs `go mod tidy` to ensure all dependencies are installed.

5. **Execute Tests**: Go tests are executed with the `json` flag, and the output is saved to `gotest.json`.

6. **Generate Test Reports**: The `go-ctrf-json-reporter` tool processes the JSON output and generates a CTRF-compliant report.

7. **Publish Test Summary Results**: The generated CTRF report is published using `github-actions-ctrf`.

8. **Upload Test Results**: Finally, the test results are uploaded as artifacts for future reference.

## **Benefits of Adding Test Summary Reports**

This workflow will help automate the testing process for your Go projects and provide valuable insights through the generated reports.Benefits of Adding Test Summary Reports

* **Improved Visibility**:

  * Test summaries provide a quick overview of test results directly in the GitHub Actions interface.

* **Enhanced Debugging**:

  * Detailed reports assist in identifying failing tests and their underlying causes.

* **Integration with External Tools**:

  * Reports can be processed and integrated with tools like Jira and Xray for better tracking and management.

## **Conclusion**

Integrating Go test execution summary reports into your GitHub Actions workflow using only `go-ctrf-json-reporter` can streamline your CI/CD pipeline and enhance the visibility of test results. The provided workflow illustrates how to execute tests, generate reports, and publish them effectively. Feel free to adapt this workflow to meet your project's specific needs and leverage automated reporting in your development process.d reporting in your development process.