## Table of Contents
1. [Introduction](#introduction)
2. [Basic Configuration](#basic-configuration)
3. [Advanced Configuration](#advanced-configuration)

---

## Introduction

Git is a distributed version control system that allows multiple developers to work on the same project without overwriting each other's changes. This wiki will guide you through setting up and configuring Git from scratch to advanced configurations.

---

## Basic Configuration

### Installing Git
To install Git, follow these steps based on your operating system:

- **Windows**: Download the installer from [git-scm.com](https://git-scm.com/) and run it.
- **macOS**: Use Homebrew by running `brew install git` in the terminal.
- **Linux**: Use your package manager. For example, on Ubuntu, run `sudo apt-get install git`.

### Initial Setup
After installing Git, you need to configure your username and email:

```sh
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

### Creating a Repository
To create a new repository, navigate to your project directory and run:

```sh
git init
```

### Basic Commands
Here are some basic Git commands:
- **Clone a repository**: `git clone <repository-url>`
- **Check status**: `git status`
- **Add changes**: `git add <file>` or `git add .` for all files
- **Commit changes**: `git commit -m "Your commit message"`
- **Push changes**: `git push origin <branch-name>`

---

## Advanced Configuration

### Branching and Merging
Branches allow you to develop features, fix bugs, or safely experiment in a contained area of your repository.

- **Create a new branch**: `git branch <branch-name>`
- **Switch to a branch**: `git checkout <branch-name>` or `git switch <branch-name>`
- **Merge branches**: `git merge <branch-name>`

### Rebasing
Rebasing is an alternative to merging that can create a cleaner project history.

```sh
git rebase <branch-name>
```

### Stashing Changes
Stash changes when you need to switch contexts but don't want to commit your current work.

- **Stash changes**: `git stash`
- **Apply stashed changes**: `git stash apply`

### Git Hooks
Git hooks are scripts that Git executes before or after events such as commit, push, and receive. They can be used for tasks like running tests or enforcing coding standards.

To create a hook, place a script in the `.git/hooks` directory of your repository. For example, to create a pre-commit hook:

```sh
touch .git/hooks/pre-commit
chmod +x .git/hooks/pre-commit
```

### Remote Repositories
Manage remote repositories to collaborate with others.

- **Add a remote**: `git remote add <name> <url>`
- **Fetch changes from a remote**: `git fetch <remote-name>`
- **Pull changes from a remote**: `git pull <remote-name> <branch-name>`
- **Push changes to a remote**: `git push <remote-name> <branch-name>`

### Git Aliases
Create shortcuts for frequently used commands.

```sh
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
```

### Submodules
Submodules allow you to keep a Git repository as a subdirectory of another Git repository.

- **Add a submodule**: `git submodule add <repository-url> <path>`
- **Initialize and update submodules**: `git submodule update --init --recursive`

---

## Conclusion

This wiki covers the basics and advanced configurations for using Git. By following these guidelines, you can effectively manage your projects and collaborate with others.
