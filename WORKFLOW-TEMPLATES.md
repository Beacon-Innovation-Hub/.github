# Quick Copy-Paste Workflow Templates

## 1. Node.js Full Stack Project

Copy this to `.github/workflows/ci.yml` in your repository:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  lint:
    name: Lint & Format
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-node: true
      node-version: '18'

  test:
    name: Run Tests
    needs: lint
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    with:
      use-node: true
      test-command: 'npm test'

  build:
    name: Build Project
    needs: test
    uses: Beacon-Innovation-Hub/.github/.github/workflows/build.yml@main
    with:
      use-node: true
      build-command: 'npm run build'

  security:
    name: Security Scan
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

---

## 2. Python Project

Copy this to `.github/workflows/ci.yml` in your repository:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  lint:
    name: Lint & Format
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-python: true
      python-version: '3.11'

  test:
    name: Run Tests
    needs: lint
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    with:
      use-python: true
      test-command: 'pytest -v --cov'

  security:
    name: Security Scan
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

---

## 3. Full Stack (Node.js + Python)

Copy this to `.github/workflows/ci.yml` in your repository:

```yaml
name: CI Pipeline

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  lint-js:
    name: Lint JavaScript
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-node: true
      node-version: '18'
      working-directory: 'frontend'

  lint-py:
    name: Lint Python
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-python: true
      python-version: '3.11'
      working-directory: 'backend'

  test-js:
    name: Test Frontend
    needs: lint-js
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    with:
      use-node: true
      test-command: 'npm test'
      working-directory: 'frontend'

  test-py:
    name: Test Backend
    needs: lint-py
    uses: Beacon-Innovation-Hub/.github/.github/workflows/test.yml@main
    with:
      use-python: true
      test-command: 'pytest'
      working-directory: 'backend'

  build:
    name: Build Project
    needs: [ test-js, test-py ]
    uses: Beacon-Innovation-Hub/.github/.github/workflows/build.yml@main
    with:
      use-node: true
      build-command: 'npm run build'
      working-directory: 'frontend'

  security:
    name: Security Scan
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

---

## 4. Documentation Only (Markdown)

Copy this to `.github/workflows/ci.yml` in your repository:

```yaml
name: Documentation Validation

on:
  pull_request:
    branches: [ main ]
    paths:
      - '**.md'
      - '.github/workflows/ci.yml'

jobs:
  validate:
    name: Validate PR
    uses: Beacon-Innovation-Hub/.github/.github/workflows/pr-validation.yml@main
```

---

## 5. Pull Request Quality Gate

Copy this to `.github/workflows/pr-check.yml` in your repository:

```yaml
name: PR Quality Gate

on:
  pull_request:
    types: [ opened, synchronize, reopened ]
    branches: [ main ]

jobs:
  validate:
    name: Validate PR Quality
    uses: Beacon-Innovation-Hub/.github/.github/workflows/pr-validation.yml@main

  lint:
    name: Code Quality
    uses: Beacon-Innovation-Hub/.github/.github/workflows/lint-and-format.yml@main
    with:
      use-node: true  # Change to use-python: true for Python projects

  security:
    name: Security Check
    uses: Beacon-Innovation-Hub/.github/.github/workflows/security-scan.yml@main
```

---

## 6. Custom Multi-Environment Testing

Copy this to `.github/workflows/matrix-test.yml` in your repository:

```yaml
name: Matrix Tests

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main, develop ]

jobs:
  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ ubuntu-latest, macos-latest, windows-latest ]
        node-version: [ '16', '18', '20' ]
    
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: ${{ matrix.node-version }}
          cache: 'npm'
      - run: npm ci
      - run: npm test
```

---

## Usage Instructions

1. **Choose the template** that matches your project type
2. **Copy the YAML code**
3. **Create file** in your repository: `.github/workflows/ci.yml`
4. **Paste the code** and customize:
   - Replace `main` branch with your default branch if needed
   - Update Python/Node versions to match your project
   - Modify `working-directory` if using monorepos
   - Change `test-command` to your actual test script
5. **Commit and push**
6. **Check Actions tab** to see workflow run

---

## Customization Tips

### Change Node Version
```yaml
node-version: '20'  # Update to your required version
```

### Change Python Version
```yaml
python-version: '3.12'  # Update to your required version
```

### Add Custom Environment Variables
```yaml
env:
  NODE_ENV: production
  DEBUG: false
```

### Run Only on Specific Paths
```yaml
on:
  push:
    paths:
      - 'src/**'
      - 'package.json'
      - '.github/workflows/**'
```

### Skip CI for Certain Commits
```
Commit message: [skip ci] Update README
```

---

**For more details, see [CI-CD-SETUP.md](CI-CD-SETUP.md)**
