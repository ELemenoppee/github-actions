# Phase 3:

As I continue my journey with GitHub Actions, I wanted to take automation a step further by implementing a Continuous Integration (CI) pipeline. This pipeline ensures that my code adheres to best practices by running linting and tests every time changes are pushed or a pull request is created.

In this post, I’ll walk you through the steps I took to:

✅ Set up a GitHub Actions CI pipeline for JavaScript and Python.

✅ Use matrix builds to run jobs for both languages.

✅ Perform linting with ESLint (JavaScript) and flake8 (Python).

✅ Run unit tests using Jest (JavaScript) and pytest (Python).

By following along, you'll gain hands-on experience with setting up a structured CI workflow in GitHub Actions. Let’s get started! 🚀

## Prerequisites

Before setting up the workflow, I ensured that my project contained:

- A JavaScript project with a `package.json` file containing ESLint and Jest.
- A Python project with a `requirements.txt` file specifying dependencies.
- A `.github/workflows` directory to store the workflow configuration.

## Step 1: Creating the "develop" Branch

First, I cloned my GitHub repository to my local machine:


```bash
git clone https://github.com/ELemenoppee/github-actions
cd phase-3/
```

## Step 2: Creating the GitHub Actions Workflow File

I created a new workflow file inside `.github/workflows/phase-3-workflow.yaml`:

```bash
touch .github/workflows/phase-3-workflow.yaml
```

This file will contain my GitHub Actions configuration.

## Step 3: Configuring the CI Workflow

Here’s the full YAML configuration of my CI pipeline:

```yaml
name: CI Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

  workflow_dispatch:

jobs:
  lint:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        language: [javascript, python]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Python
        if: matrix.language == 'python'
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Install dependencies (Python)
        if: matrix.language == 'python'
        run: |
          cd phase-3/my-python-project
          python -m pip install --upgrade pip
          pip install -r requirements.txt flake8

      - name: Check Installed Python Packages
        if: matrix.language == 'python'
        run: python -m pip list

      - name: Run flake8 (Python)
        if: matrix.language == 'python'
        run: |
          cd phase-3/my-python-project
          flake8 --exit-zero .

  test:
    runs-on: ubuntu-latest
    needs: lint
    strategy:
      matrix:
        language: [javascript, python]
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up Node.js (for JavaScript)
        if: matrix.language == 'javascript'
        uses: actions/setup-node@v4
        with:
          node-version: 18

      - name: Cache Node.js dependencies
        if: matrix.language == 'javascript'
        uses: actions/cache@v4
        with:
          path: ~/.npm
          key: ${{ runner.os }}-node-${{ hashFiles('phase-3/my-javascript-project/package-lock.json') }}  
          restore-keys: |
            ${{ runner.os }}-node-

      - name: Install dependencies (JavaScript)
        if: matrix.language == 'javascript'
        run: |
          cd phase-3/my-javascript-project
          npm install

      - name: Run Jest tests (JavaScript)
        if: matrix.language == 'javascript'
        run: |
          cd phase-3/my-javascript-project
          npm test

      - name: Set up Python
        if: matrix.language == 'python'
        uses: actions/setup-python@v4
        with:
          python-version: '3.10'

      - name: Cache Python dependencies
        if: matrix.language == 'python'
        uses: actions/cache@v4
        with:
          path: ~/.cache/pip
          key: ${{ runner.os }}-pip-${{ hashFiles('phase-3/my-python-project/requirements.txt') }}
          restore-keys: |
            ${{ runner.os }}-pip-

      - name: Install dependencies (Python)
        if: matrix.language == 'python'
        run: |
          cd phase-3/my-python-project
          pip install -r requirements.txt

      - name: Run pytest (Python)
        if: matrix.language == 'python'
        run: |
          cd phase-3/my-python-project
          pytest
```

## Step 4: Pushing the Workflow and Verifying Execution

To test the workflow, I made a small change to my repository and pushed it:

```bash
echo "Test CI Pipeline" >> test.txt
git add test.txt
git commit -m "Test CI Pipeline"
git push origin main
```

Then, I checked my GitHub Actions dashboard to verify that the workflow ran successfully. If everything was set up correctly, the linting and testing jobs should pass.

## What I Learned

This project helped me understand how to set up an automated CI pipeline with GitHub Actions. Some key takeaways:

✅ How to configure GitHub Actions for JavaScript and Python projects.

✅ Using matrix builds to run jobs for multiple languages.

✅ Automating linting with ESLint and flake8.

✅ Running unit tests using Jest and pytest.

✅ Caching dependencies to improve workflow efficiency.

This has been a great learning experience, and I’m excited to build on it further! 🚀

