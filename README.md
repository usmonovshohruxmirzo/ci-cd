# 🏆 CI/CD Learning Roadmap (GitHub Actions)

## ⚙️ Core Concepts Before Starting

* **`on:`** → Defines the event that triggers the workflow.
  Examples:

  * `on: push` → run when you push code.
  * `on: pull_request` → run when PR is opened/updated.
  * `on: workflow_dispatch` → manual trigger.

* **`jobs:`** → A workflow is made of one or more jobs.

  * Each job runs on a **runner** (like `ubuntu-latest`).
  * Jobs can run **in parallel** or depend on each other (`needs:`).

* **`steps:`** → Each job has steps (a sequence of commands/actions).

* **`uses:`** → Run a prebuilt GitHub Action (from Marketplace). Example: `actions/checkout@v3`.

* **`run:`** → Run custom shell commands (`bash` on Linux runners).

* **`needs:`** → Makes jobs depend on other jobs (controls order).

* **`with:`** → Pass input values to actions.

---

## 🔹 Stage 1: Hello World (Basics)

👉 Goal: Understand how workflows run.

📌 Workflow: `.github/workflows/01-hello.yml`

```yaml
name: Hello World CI   # Workflow name (shown in Actions tab)

on:
  push:                # Trigger event
    branches: [ "main" ]

jobs:
  hello:               # Job ID
    runs-on: ubuntu-latest   # Runner machine
    steps:
      - name: Checkout repo
        uses: actions/checkout@v3   # Action to fetch code

      - name: Print message
        run: echo "🚀 Hello CI/CD World!"   # Run shell command
```

✅ Push → check **Actions tab** → see logs.

---

## 🔹 Stage 2: Run Jobs (Node.js or Python)

👉 Goal: Run scripts/tests.

📌 Workflow: `.github/workflows/02-tests.yml`

```yaml
name: Run Tests

on: [push]    # Short syntax (same as "on: push")

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18    # Specify Node.js version

      - run: npm install
      - run: npm test
```

✅ Add a simple `npm test` script in `package.json`.

---

## 🔹 Stage 3: Matrix Builds

👉 Goal: Test across multiple versions.

📌 `.github/workflows/03-matrix.yml`

```yaml
name: Matrix Build

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    strategy:              # Define test matrix
      matrix:
        node: [16, 18, 20] # Node.js versions
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
        with:
          node-version: ${{ matrix.node }}
      - run: npm install
      - run: npm test
```

✅ Runs tests on Node.js 16, 18, 20 simultaneously.

---

## 🔹 Stage 4: Lint + Test + Build (Pipeline Stages)

👉 Goal: Create real CI pipeline.

📌 `.github/workflows/04-pipeline.yml`

```yaml
name: CI Pipeline

on: [push]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm run lint

  test:
    runs-on: ubuntu-latest
    needs: lint     # Run only after lint succeeds
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm test

  build:
    runs-on: ubuntu-latest
    needs: test     # Run only after test succeeds
    steps:
      - uses: actions/checkout@v3
      - run: npm install
      - run: npm run build
```

✅ Enforces **Lint → Test → Build order**.

---

## 🔹 Stage 5: Cache Dependencies

👉 Goal: Optimize workflow speed.

📌 `.github/workflows/05-cache.yml`

```yaml
name: CI with Cache

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3

      - name: Cache node_modules
        uses: actions/cache@v3
        with:
          path: node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
          restore-keys: |
            ${{ runner.os }}-node-

      - run: npm install
      - run: npm test
```

✅ Dependencies won’t reinstall every time.

---

## 🔹 Stage 6: Deploy (GitHub Pages Example)

👉 Goal: Continuous Deployment.

📌 `.github/workflows/06-deploy.yml`

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [ "main" ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - name: Setup Node.js
        uses: actions/setup-node@v3
        with:
          node-version: 18
      - run: npm install
      - run: npm run build
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./dist
```

✅ Every push → deploys site to GitHub Pages.

---

# 🎯 Your Mastery Checklist

* [ ] Understand triggers (`on:`)
* [ ] Understand jobs and steps
* [ ] Run Hello World workflow
* [ ] Run tests on push
* [ ] Use matrix builds (multi-versions)
* [ ] Pipeline with stages (Lint → Test → Build)
* [ ] Use caching for speed
* [ ] Deploy automatically

---

⚡ Once you complete all 6 stages, you’ll **fully master GitHub Actions CI/CD basics + deployment**.
