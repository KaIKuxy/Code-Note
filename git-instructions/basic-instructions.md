# Basic Instructions

### What is Git

Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency.

Git **is not** Github

### Git's Procedure

![](../.gitbook/assets/index1-2x.png)

Staging area is an intermediate area where commits can be formatted and reviewed before completing the commit.

It is also possible ignore the staging area and commit directly using:

```bash
git commit -a
```

### Basic Commands

* Init \(Tell Git to monitor the current directory for changes\)

```bash
git init
```

Upon execution, Git will create a Hidden Folder named /.git in the current workspace and use it to track all changes to your workspace. \(To view hidden folders in terminal, use ls -a\)

* Status

```bash
git status
```

Git will provide a status report, including current branch, changes to be committed \(which means added  into staging area\) and changes not staged for commit, for the current workspace like:

```bash
On branch master
Your branch is up to date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   app.js

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   routes/index.js
```

* Add \(Add a file to the staging area. To add all of the modified files in the workspace, use **.** as the filename\)

```bash
git add <file_name>
```

* Commit \(Save all the changes in the staging area into a new checkpoint in the current branch.\)

```bash
git commit -m "If a message is needed, type -m and and message with double quotes like this"
```

* Log \(Displays a log of all the commits made to the project till current checkpoint\)
* Checkout \(Change to a different commit checkpoint or change to a different branch\)

```bash
git checkout <commit_id>
git checkout -b <branch_name>
git checkout master
```

* Merge \(Merge two branches into one, we can merge a stable feature branch into the main branch\)

```bash
git merge <branch_name>
```

* Revert \(Revert back a previous commit\)

```bash
git revert --no-commit <commit_id>..HEAD
git commit -m "Explanatory Message"
```

### [Diff between checkout and revert](https://www.atlassian.com/git/tutorials/resetting-checking-out-and-reverting)

A checkout is an operation that moves the **HEAD** ref pointer to a specified commit. 

A revert is an operation that takes a specified commit and creates a new commit which inverses the specified commit. git revert can only be run at a commit level scope and has no file level functionality.

A reset is an operation that takes a specified commit and resets the "three trees" to match the state of the repository at that specified commit. A reset can be invoked in three different modes which correspond to the three trees.

Checkout and reset are generally used for making local or private 'undos'. They modify the history of a repository that can cause conflicts when pushing to remote shared repositories. Revert is considered a safe operation for 'public undos' as it creates new history which can be shared remotely and doesn't overwrite history remote team members may be dependent on.

