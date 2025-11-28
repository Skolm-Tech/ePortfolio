# ePortfolio

This is our team's ePortfolio

# Git Essentials

## Configure git locally

Configure Git with your user details.

1. **Set your email:**
   ```bash
   git config --global user.email "you@example.com"
   ```

2. **Set your name:**
   ```bash
   git config --global user.name "Your Name"
   ```

## Pull code for first time on new machine

To pull both the `main` and `testing` branch files on a new machine, follow these steps:

1. **Initialize Git:**
    ```bash
    git init
    ```

2. **Add the remote repository:**
    ```bash
    git remote add origin https://github.com/Skolm-Tech/ePortfolio.git
    ```

3. **Fetch all branches from the remote:**

    ```bash
    git fetch
    ```

4. **Check out the `main` branch and set it to track the remote:**

    ```bash
    git checkout -b main origin/main
    git branch --set-upstream-to=origin/main main
    ```

6. **(Optional) Return to the main branch:**

    ```bash
    git checkout main
    ```    

## Pulling new changes

To receive the newest version of the code:

1. **Fetch the latest changes:**
   ```bash
   git fetch
   ```

2. **Pull the changes:**
   ```bash
   git merge
   ```

    OR simply run

    ```shell
    git pull
    ```

## Pushing New Changes To `main`

To push your code changes back to the repository:

1. **Add all changes:**
   ```bash
   git add .
   ```

2. **Commit with a message:**
   ```bash
   git commit -am "code comments"
   ```

3. **Push the changes (first time only):**
   ```bash
   git push --set-upstream origin main
   ```

    OR

   ```
   git push origin HEAD:main
   ```


4. **Push changes**
   ```shell
   git push
   ```

## Reverting Changes
### 1. Revert Unstaged Changes (Changes Not Yet Added)

If you have made changes to files but haven't staged them yet, you can revert those changes using:

```bash
git checkout -- <file>
```

To revert all unstaged changes in your working directory:

```bash
git checkout -- .
```

### 2. Revert Staged Changes (Changes Already Added)

If you have staged changes (added to the index with `git add`), but want to revert them before committing, you can unstage them with:

```bash
git reset <file>
```

To unstage all files:

```bash
git reset
```

This will keep your changes in the working directory, allowing you to edit or discard them later.

### 3. Revert Committed Changes (Undo the Last Commit)

If you want to undo the last commit and move the changes back to the working directory (so you can modify or discard them):

```bash
git reset --soft HEAD~1
```

If you want to completely discard the last commit and the changes, use:

```bash
git reset --hard HEAD~1
```

### 4. Revert Specific Commits

If you want to undo a specific commit without altering history, use:

```bash
git revert <commit_hash>
```

This will create a new commit that undoes the changes made in the specified commit.

## Check difference between local copy and remote branch

```
git diff main origin/main
```