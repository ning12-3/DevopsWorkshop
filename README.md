# DevOps Workshop: Introduction to CI with GitHub Actions

Welcome! In this hands-on workshop you will set up a **Continuous Integration (CI) pipeline** using GitHub Actions. By the end you will understand how automated testing works in a real development workflow.

---

## Prerequisites

- A GitHub account
- Git installed on your machine
- Python 3.10+ installed
- A code editor (VS Code recommended)

---

## Step 1 — Fork & Clone the Repository

1. Click **Fork** in the top-right corner of this repo on GitHub.
2. Clone your fork locally:

```bash
git clone https://github.com/<your-username>/DevopsWorkshop.git
cd DevopsWorkshop
```

3. Install the test dependency:

```bash
pip install -r requirements.txt
```

---

## Step 2 — Explore the Codebase

Take a few minutes to look through the project. Here is the structure:

```
DevopsWorkshop/
├── app/
│   ├── calculator.py      # Basic math functions
│   └── string_utils.py    # String helper functions
├── tests/
│   ├── test_calculator.py # Tests for calculator
│   └── test_string_utils.py # Tests for string utils
├── requirements.txt       # Python dependencies
└── README.md              # You are here!
```

**Tasks:**

- [ ] Open `app/calculator.py` and read through the functions.
- [ ] Open `app/string_utils.py` and read through the functions.
- [ ] Open the test files in `tests/` and see what is being tested.

> **Question for the group:** What do you think the tests are checking? Can you spot anything that might be wrong in the code?

---

## Step 3 — Run the Tests Locally

Before setting up CI, run the tests on your own machine:

```bash
python3 -m pytest tests/ -v
```

**You should see output like this:**

```
tests/test_calculator.py::test_add PASSED
tests/test_calculator.py::test_subtract FAILED
tests/test_calculator.py::test_multiply PASSED
tests/test_calculator.py::test_divide PASSED
tests/test_calculator.py::test_divide_by_zero PASSED
tests/test_calculator.py::test_percentage PASSED
tests/test_string_utils.py::test_reverse_string FAILED
tests/test_string_utils.py::test_capitalize_words PASSED
tests/test_string_utils.py::test_count_vowels FAILED
tests/test_string_utils.py::test_is_palindrome PASSED
```

**Tasks:**

- [ ] Run the tests. How many pass? How many fail?
- [ ] Read the failure messages carefully. Which functions have bugs?

---

## Step 4 — Create a GitHub Actions Workflow

Now let's automate these tests so they run every time code is pushed.

### 4a. Create the workflow directory

```bash
mkdir -p .github/workflows
```

### 4b. Create the workflow file

Create a new file at `.github/workflows/ci.yml` and paste the following:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v5
        with:
          python-version: "3.12"

      - name: Install dependencies
        run: pip install -r requirements.txt

      - name: Run tests
        run: python3 -m pytest tests/ -v
```

**Tasks:**

- [ ] Create the `.github/workflows/` directory.
- [ ] Create `ci.yml` with the content above.
- [ ] Read through the YAML. Can you identify what each section does?

> **Discussion:**
>
> - `on:` — When does this workflow run?
> - `runs-on:` — Where does it run?
> - `steps:` — What does each step do?

---

## Step 5 — Push and Watch the Pipeline Run

Commit your workflow file and push it:

```bash
git add .github/workflows/ci.yml
git commit -m "Add CI workflow"
git push origin main
```

**Tasks:**

- [ ] Go to your repository on GitHub.
- [ ] Click the **Actions** tab.
- [ ] Watch the workflow run. Does it pass or fail?

You should see the pipeline **fail** — because there are three bugs: `count_vowels` and `reverse_string` in `string_utils.py`, and `subtract` in `calculator.py`. This is CI doing its job: catching bugs automatically!

---

## Step 6 — Fix the Bug and See the Pipeline Go Green

Time to fix the bugs! There are three to find:

### Bug 1 — `count_vowels` in `app/string_utils.py`

1. Find the `count_vowels` function.
2. Look at the vowel list — what is missing?

<details>
<summary>💡 Hint (click to reveal)</summary>

The vowel string `"aeio"` is missing `"u"`. It should be `"aeiou"`.

</details>

### Bug 2 — `reverse_string` in `app/string_utils.py`

1. Find the `reverse_string` function.
2. Look at the slice notation — is it actually reversing the string?

<details>
<summary>💡 Hint (click to reveal)</summary>

The slice `s[::1]` just returns the string as-is. It should be `s[::-1]` (note the negative step) to reverse it.

</details>

### Bug 3 — `subtract` in `app/calculator.py`

1. Find the `subtract` function.
2. Look at the operator — is it subtracting?

<details>
<summary>💡 Hint (click to reveal)</summary>

The function uses `+` instead of `-`. It should be `return a - b`.

</details>

5. Run the tests locally to confirm they all pass:

```bash
python3 -m pytest tests/ -v
```

6. Commit and push the fix:

```bash
git add app/string_utils.py app/calculator.py
git commit -m "Fix bugs in subtract, reverse_string, and count_vowels"
git push origin main
```

**Tasks:**

- [ ] Fix the bugs in `count_vowels`, `reverse_string`, and `subtract`.
- [ ] Verify all 10 tests pass locally.
- [ ] Push the fix and watch the Actions tab go green!

---

## Bonus Challenges

If you finish early, try these:

### Bonus 1 — Add a New Function and Test

Add a new function to `app/calculator.py` (e.g., `power(base, exp)`) and write a test for it in `tests/test_calculator.py`. Push and watch CI run your new test.

### Bonus 2 — Create a Pull Request Workflow

Instead of pushing directly to `main`:

1. Create a new branch: `git checkout -b add-power-function`
2. Make your changes and push the branch.
3. Open a Pull Request on GitHub.
4. See how CI runs automatically on the PR before merging!

### Bonus 3 — Add a Linter Step

Add a linting step to your workflow using `flake8`:

```yaml
- name: Install linter
  run: pip install flake8

- name: Run linter
  run: flake8 app/ tests/ --max-line-length=100
```

---

## Key Takeaways

- **CI (Continuous Integration)** automatically runs tests when code changes.
- **GitHub Actions** lets you define CI pipelines as YAML files in your repo.
- A failing pipeline tells you something is wrong **before** it reaches production.
- Fixing a bug and seeing the pipeline go green is the core CI feedback loop.

---

## Quick Reference

| Command                       | What it does                      |
| ----------------------------- | --------------------------------- |
| `python3 -m pytest tests/ -v` | Run all tests with verbose output |
| `git add <file>`              | Stage a file for commit           |
| `git commit -m "message"`     | Create a commit                   |
| `git push origin main`        | Push to GitHub                    |
| `git checkout -b <branch>`    | Create and switch to a new branch |

---
