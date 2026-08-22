# Beacon Innovation Hub - CI/CD Pipeline Setup Guide

## Overview

This guide explains how to set up and use the centralized CI/CD workflows for BIH repositories.

## Centralized Reusable Workflows

All workflows are stored in `.github/workflows/` and designed to be called by other repositories using the `uses` keyword.

### Available Workflows

#### 1. **Lint & Format** (`lint-and-format.yml`)
Validates code quality and formatting standards.

**Supports:**
- Node.js projects (ESLint, Prettier)
- Python projects (flake8, pylint, black, isort)

**Inputs:**
```yaml
inputs:
  working-directory: '.'
  node-version: '18'
  python-version: '3.11'
  use-node: false
  use-python: false
```

---

#### 2. **Test** (`test.yml`)
Runs test suites and generates coverage reports.

**Supports:**
- Node.js (npm test, Jest, Vitest)
- Python (pytest)
- Code coverage reporting (Codecov)

**Inputs:**
```yaml
inputs:
  working-directory: '.'
  node-version: '18'
  python-version: '3.11'
  use-node: false
  use-python: false
  test-command: 'npm test'
```

---

#### 3. **Build** (`build.yml`)
Builds projects and uploads artifacts.

**Supports:**
- Node.js build pipelines
- Custom build commands
- Artifact retention

**Inputs:**
```yaml
inputs:
  working-directory: '.'
  node-version: '18'
  build-command: 'npm run build'
  use-node: false
```

---

#### 4. **PR Validation** (`pr-validation.yml`)
Validates pull request quality.

**Checks:**
- Conventional commit format
- Large file detection
- JSON file validation

---

#### 5. **Security Scan** (`security-scan.yml`)
Performs security scanning and vulnerability detection.

**Tools:**
- Trivy (filesystem scanning)
- TruffleHog (secrets detection)
- npm audit / pip safety (dependency scanning)

---

## How to Use in Your Repository

### Step 1: Create a Workflow File

In your repository, create `.github/workflows/ci.yml`:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  lint:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-node: true
      node-version: '18'

  test:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    needs: lint
    with:
      use-node: true
      test-command: 'npm test'

  build:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/build.yml@main
    needs: test
    with:
      use-node: true
      build-command: 'npm run build'

  security:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
    needs: lint
```

### Step 2: For Python Projects

```yaml
name: Python CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-python: true
      python-version: '3.11'

  test:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    needs: lint
    with:
      use-python: true
      test-command: 'pytest'

  security:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

### Step 3: For Pull Request Validation

```yaml
name: PR Validation

on:
  pull_request:
    branches: [ main ]

jobs:
  validate:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/pr-validation.yml@main
```

---

## Repository Checklist

Before adding workflows to your repo, ensure you have:

### For Node.js Projects
- ✅ `package.json`
- ✅ `.eslintrc` or `eslint` config in `package.json`
- ✅ `.prettierrc` or `prettier` config
- ✅ Test script defined in `package.json`
- ✅ `build` script (if using Build workflow)

### For Python Projects
- ✅ `requirements.txt` or `pyproject.toml`
- ✅ `.flake8` or similar linting config
- ✅ `pytest.ini` or similar test config
- ✅ Tests in a `tests/` directory or following `test_*.py` pattern

### For All Projects
- ✅ `.gitignore` file
- ✅ README with setup instructions
- ✅ License file (recommended)

---

## Workflow Examples

### Example 1: LEARNING-RESOURCES (Markdown/Documentation)

Since this is a learning resources repo, use minimal CI:

```yaml
name: Documentation Validation

on:
  pull_request:
    branches: [ main ]

jobs:
  validate:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/pr-validation.yml@main

  security:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

### Example 2: demo-repository (HTML/JavaScript)

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-node: true
      node-version: '18'

  test:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    needs: lint
    with:
      use-node: true
      test-command: 'npm test'

  build:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/build.yml@main
    needs: test
    with:
      use-node: true
      build-command: 'npm run build'

  security:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

### Example 3: Competence-Tests (Full CI/CD)

```yaml
name: Full CI/CD Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  validate-pr:
    if: github.event_name == 'pull_request'
    uses: Beacon-Innovation-Hub/.github/.github/workflows/pr-validation.yml@main

  lint:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-python: true
      python-version: '3.11'

  test:
    needs: lint
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    with:
      use-python: true
      test-command: 'pytest -v'

  security:
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

---

## Branch Protection Rules

Recommended branch protection settings:

1. **Require status checks to pass before merging:**
   - ✅ CI Pipeline (all jobs)

2. **Require branches to be up to date before merging:**
   - ✅ Enabled

3. **Require code reviews before merging:**
   - ✅ 1 approval required
   - ✅ Dismiss stale pull request approvals when new commits are pushed
   - ✅ Require review from code owners

4. **Restrict who can push to matching branches:**
   - ✅ Enforce all configured restrictions for administrators

---

## Secrets & Environment Variables

If your workflows need secrets (API keys, credentials, etc.):

1. Go to **Repository Settings** → **Secrets and variables** → **Actions**
2. Click **New repository secret**
3. Add your secrets (e.g., `CODECOV_TOKEN`, `DEPLOY_KEY`)

**Organization-level secrets** (optional):
- Can be shared across all repos
- Manage at **Organization Settings** → **Secrets and variables** → **Actions**

---

## Monitoring & Debugging

### View Workflow Runs
1. Go to your repository
2. Click **Actions** tab
3. Select the workflow run to see detailed logs

### Common Issues

| Issue | Solution |
|-------|----------|
| Workflow not triggering | Check branch name and trigger conditions in `on:` |
| Permission denied | Ensure GitHub Actions is enabled in repo settings |
| Dependency installation fails | Verify `package.json` or `requirements.txt` exists |
| Linting fails locally but passes CI | Check Node/Python versions match |
| Tests timeout | Increase timeout or optimize slow tests |

---

## Next Steps

1. **Choose your repositories** - Which BIH repos need CI/CD?
2. **Create workflow files** - Use the examples above
3. **Test locally** - Ensure scripts work before committing
4. **Set branch protections** - Enforce quality gates
5. **Monitor results** - Review Actions tab for success/failures

---

## Support & Contribution

For questions or improvements to these workflows:
1. Create an issue in `.github` repository
2. Submit a PR with enhancements
3. Document your changes in this guide

---

**Last Updated:** August 22, 2026
