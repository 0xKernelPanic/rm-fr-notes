---
title: GIT
tags:
  - git
  - version_control
created: 2025-02-19
---

# GIT

## Description
`Git` is a **distributed version control system (DVCS)** used for tracking changes in source code during software development. It allows multiple developers to work on a project simultaneously, manage versions, and collaborate efficiently.

### GIT Concepts
| GIT Concept    | Description                                                            |
| -------------- | ---------------------------------------------------------------------- |
| `Repository`   | A directory containing all files, history, and commits.                |
| `Commit`       | A snapshot of changes saved in the repository.                         |
| `Branch`       | A separate line of development.                                        |
| `Merge`        | Combining changes from different branches.                             |
| `Remote`       | A reference to a repository stored on a server (e.g., GitHub, GitLab). |
| `Pull`         | Fetching and merging changes from a remote repository.                 |
| `Push`         | Sending local commits to a remote repository.                          |
| `Staging Area` | Where changes are prepared before committing.                          |
| `HEAD`         | Pointer to the current commit.                                         |

## Use Cases
- Tracking file changes
- Reverting changes

## Commands
### Git setup
```bash
# Configure user details
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"

# Check configuration
git config --list

```

### Initializing a repository
To initialize a new repository in the current folder type the following command
```bash
git init
```
This will create `.git file` that contains all the information about the repository
### Clone an existing repository
```bash
git clone <repo_url>
```
### Working with changes
```bash
# Check status
git status

# Add files to staging area
git add <file>    # Add a specific file
git add .         # Add all changes

# Commit changes
git commit -m "Commit message"
```

### Commit history
```bash
git log --oneline --graph --all
```

###  Run SSH agent and add private key 
```bash
eval $(ssh-agent -s)
ssh-add ~/.ssh/key
```
## Sources
- https://www.youtube.com/watch?v=USjZcfj8yxE



