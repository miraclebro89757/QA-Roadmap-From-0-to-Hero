# Version Control with Git

Version control is essential for managing code, test scripts, and automation frameworks. Git is the most popular version control system, enabling teams to collaborate effectively and track changes over time.

## 🎯 What is Version Control?

Version control is a system that tracks changes to files over time, allowing you to:
- **Track Changes:** See what changed, when, and by whom
- **Collaborate:** Work with team members without conflicts
- **Rollback:** Revert to previous versions if needed
- **Branch:** Work on features without affecting main code
- **Merge:** Combine changes from different sources

### **Why Git for QA?**
- **Test Script Management:** Version control your automation code
- **Test Data Tracking:** Manage test data and configuration files
- **Team Collaboration:** Work with developers and other QA engineers
- **CI/CD Integration:** Integrate with automated testing pipelines
- **Documentation:** Track changes to test plans and documentation

---

## 🚀 Git Fundamentals

### **Repository (Repo)**
A repository is a directory that contains your project files and Git metadata.

```bash
# Initialize a new repository
git init

# Clone an existing repository
git clone https://github.com/username/qa-automation-project.git

# Check repository status
git status
```

### **Working Directory, Staging Area, and Repository**
```bash
# Working Directory: Your local files
# Staging Area: Files ready to be committed
# Repository: Committed changes

# Add files to staging area
git add filename.py
git add .  # Add all files

# Commit staged changes
git commit -m "Add login test automation"

# Check what's staged
git diff --cached
```

---

## 📱 Real-World Example: QA Automation Project

### **Project Structure**
```bash
qa-automation-project/
├── tests/
│   ├── test_login.py
│   ├── test_tweet_creation.py
│   └── test_user_profile.py
├── test_data/
│   ├── users.json
│   └── test_config.yaml
├── utils/
│   ├── test_helpers.py
│   └── reporting.py
├── requirements.txt
└── README.md
```

### **Initial Setup**
```bash
# Create project directory
mkdir qa-automation-project
cd qa-automation-project

# Initialize Git repository
git init

# Create initial files
touch README.md
touch requirements.txt
mkdir tests test_data utils

# Add files to Git
git add .
git commit -m "Initial project setup"
```

---

## 🛠️ Essential Git Commands

### **1. Basic Commands**
```bash
# Check Git version
git --version

# Configure Git (one-time setup)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Check configuration
git config --list

# Initialize repository
git init

# Clone repository
git clone <repository-url>
```

### **2. File Management**
```bash
# Check status
git status

# Add files to staging
git add filename.py          # Add specific file
git add tests/               # Add entire directory
git add .                    # Add all changes

# Remove files from staging
git reset filename.py

# Commit changes
git commit -m "Add login test automation"
git commit -am "Update test data"  # Add and commit in one step

# View commit history
git log
git log --oneline            # Compact view
git log --graph              # Visual branch history
```

### **3. Branching and Merging**
```bash
# List branches
git branch

# Create new branch
git branch feature/login-tests

# Switch to branch
git checkout feature/login-tests
git checkout -b feature/login-tests  # Create and switch

# Merge branch
git checkout main
git merge feature/login-tests

# Delete branch
git branch -d feature/login-tests
```

### **4. Remote Repository Operations**
```bash
# Add remote repository
git remote add origin https://github.com/username/qa-automation.git

# Push changes
git push origin main
git push -u origin feature/login-tests  # Set upstream

# Pull changes
git pull origin main

# Fetch changes (without merging)
git fetch origin
```

---

## 🔄 Git Workflow for QA Teams

### **1. Feature Branch Workflow**
```bash
# Start new feature
git checkout main
git pull origin main
git checkout -b feature/tweet-validation-tests

# Make changes
# Edit test files, add new tests

# Commit changes
git add tests/test_tweet_validation.py
git commit -m "Add tweet character limit validation tests"

# Push feature branch
git push -u origin feature/tweet-validation-tests

# Create Pull Request (on GitHub/GitLab)
# Request code review from team members

# After approval, merge to main
git checkout main
git pull origin main
git merge feature/tweet-validation-tests
git push origin main
```

### **2. Hotfix Workflow**
```bash
# Create hotfix branch
git checkout main
git checkout -b hotfix/fix-login-test

# Fix the issue
# Update test code

# Commit fix
git add tests/test_login.py
git commit -m "Fix login test timeout issue"

# Push and merge
git push origin hotfix/fix-login-test
# Create PR, review, merge
```

---

## 📋 Practical Git Examples for QA

### **Example 1: Adding New Test Suite**
```bash
# Create new test suite
git checkout -b feature/user-profile-tests

# Add test files
touch tests/test_user_profile.py
touch test_data/user_profile_data.json

# Write test code
# ... (write your test code)

# Stage and commit
git add tests/test_user_profile.py
git add test_data/user_profile_data.json
git commit -m "Add user profile test suite

- Add test_user_profile.py with profile update tests
- Add test data for user profile scenarios
- Include positive and negative test cases"

# Push to remote
git push -u origin feature/user-profile-tests
```

### **Example 2: Updating Test Data**
```bash
# Update test configuration
git checkout main
git pull origin main

# Edit test data
# Update test_data/users.json with new test users

# Check what changed
git diff test_data/users.json

# Stage and commit
git add test_data/users.json
git commit -m "Update test user data

- Add new test users for edge case testing
- Update user credentials for security
- Add users with different permission levels"

# Push changes
git push origin main
```

### **Example 3: Fixing Broken Tests**
```bash
# Identify broken test
git log --oneline -10  # Check recent commits

# Create fix branch
git checkout -b fix/broken-login-test

# Fix the test
# Update test code to handle new UI changes

# Test the fix
python -m pytest tests/test_login.py -v

# Commit fix
git add tests/test_login.py
git commit -m "Fix login test for new UI changes

- Update element selectors for new login form
- Add wait conditions for dynamic content
- Fix assertion for new success message"

# Push fix
git push origin fix/broken-login-test
```

---

## 🔧 Advanced Git Features

### **1. Stashing Changes**
```bash
# Save work in progress
git stash
git stash push -m "WIP: working on tweet validation"

# List stashes
git stash list

# Apply stash
git stash pop
git stash apply stash@{0}

# Drop stash
git stash drop stash@{0}
```

### **2. Rebasing**
```bash
# Rebase feature branch on main
git checkout feature/login-tests
git rebase main

# Interactive rebase
git rebase -i HEAD~3  # Rebase last 3 commits
```

### **3. Cherry-picking**
```bash
# Apply specific commit to current branch
git cherry-pick abc1234

# Cherry-pick multiple commits
git cherry-pick abc1234 def5678
```

### **4. Git Tags**
```bash
# Create tag for release
git tag v1.0.0
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tags
git push origin v1.0.0
git push origin --tags

# List tags
git tag
```

---

## 🎯 Git Best Practices for QA

### **1. Commit Messages**
```bash
# Good commit messages
git commit -m "Add login test automation

- Implement login test with valid credentials
- Add test for invalid login scenarios
- Include proper error handling and reporting"

# Bad commit messages
git commit -m "fix stuff"
git commit -m "update"
```

### **2. Branch Naming**
```bash
# Good branch names
feature/login-automation
bugfix/fix-timeout-issue
hotfix/critical-test-fix
chore/update-dependencies

# Bad branch names
new-feature
fix
test
```

### **3. File Organization**
```bash
# Recommended structure
tests/
├── unit/
├── integration/
└── e2e/
test_data/
├── production/
└── staging/
docs/
├── test_plans/
└── automation_guides/
```

### **4. .gitignore for QA Projects**
```bash
# Create .gitignore file
touch .gitignore

# Add common patterns
*.log
*.pyc
__pycache__/
.pytest_cache/
test_reports/
screenshots/
videos/
.env
config/local.yaml
```

---

## 🔄 Collaboration with Development Teams

### **1. Code Review Process**
```bash
# Create pull request
git push origin feature/new-tests

# On GitHub/GitLab:
# 1. Create Pull Request
# 2. Add description of changes
# 3. Request reviewers
# 4. Address feedback
# 5. Merge after approval
```

### **2. Working with Shared Branches**
```bash
# Keep main branch clean
git checkout main
git pull origin main

# Create feature branch from main
git checkout -b feature/shared-tests

# Regular updates from main
git checkout main
git pull origin main
git checkout feature/shared-tests
git rebase main
```

### **3. Resolving Conflicts**
```bash
# When conflicts occur
git merge feature/conflicting-branch

# Git will show conflict markers
# Edit files to resolve conflicts
# Remove conflict markers

# After resolving
git add .
git commit -m "Resolve merge conflicts"
```

---

## 💡 Tips for Effective Git Usage

### **1. Regular Commits**
- Commit frequently with meaningful messages
- Keep commits focused on single changes
- Use descriptive commit messages

### **2. Branch Management**
- Use feature branches for new work
- Keep branches short-lived
- Delete merged branches

### **3. Communication**
- Communicate with team about branch usage
- Use pull requests for code reviews
- Document complex changes

### **4. Backup and Safety**
- Push changes regularly
- Use remote repositories for backup
- Test changes before pushing to main

### **5. Learning Resources**
- Practice with personal projects
- Use Git GUI tools for visualization
- Read Git documentation and tutorials

---

## 🔄 Git vs Other Version Control Systems

| Feature | Git | SVN | Mercurial |
|---------|-----|-----|-----------|
| **Distributed** | ✅ | ❌ | ✅ |
| **Branching** | ✅ | ⚠️ | ✅ |
| **Performance** | ✅ | ⚠️ | ✅ |
| **Learning Curve** | Medium | Easy | Medium |
| **Community** | Large | Medium | Small |
| **Integration** | Excellent | Good | Good |

---

## 💡 Common Git Commands Cheat Sheet

```bash
# Setup
git init                    # Initialize repository
git clone <url>            # Clone repository
git config --global user.name "Name"

# Basic Workflow
git status                 # Check status
git add <file>             # Stage files
git commit -m "message"    # Commit changes
git push                   # Push to remote
git pull                   # Pull from remote

# Branching
git branch                 # List branches
git checkout <branch>      # Switch branch
git checkout -b <branch>   # Create and switch
git merge <branch>         # Merge branch

# History
git log                    # View history
git log --oneline          # Compact history
git diff                   # View changes

# Undo
git reset HEAD <file>      # Unstage file
git checkout -- <file>     # Discard changes
git revert <commit>        # Revert commit
```

**Remember:** Git is a powerful tool that becomes more valuable as you use it. Start with basic commands and gradually learn advanced features. The key is to practice regularly and integrate Git into your daily QA workflow.
