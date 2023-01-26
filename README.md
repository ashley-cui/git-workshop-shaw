# Git Workshop

## Intro
### What is git?
Git is a command line version control tool. Essentinally, it tracks changes you make in a folder that you tell it to, and takes snapshots of those changes. Git is also particularly powerful for collaboration.

### Install git
#### MacOS
Git is installed by default on MacOS, if you have command-line-tools installed. Run `git --version`, and if it returns a version, you are all set. Otherwise, follow the instructions that appear on the screen after running `git --version`.
```
git --version
```
#### Windows
Download the Git for Windows here:

https://git-scm.com/download/win

We will use the git bash application.

### Set up Git
Let's tell git who you are. When making git commits, git needs to credit an author (more on this later).

```
git config --global user.name "Ashley Cui"
git config user.email "acui@redhat.com"
```

## Local git
We're going to start off with playing around with git on your local machine. That is, everything that happens in this section only exists on your computer, and nowhere else.

### Create a repository
#### What is a git repository?
A git repository is essentially a "project". Within a repository, git tracks changes to all files and folders inside the repository. Basically, a repository is a folder that git tracks all changes inside that folder.
#### Create your first Repository
Create a new folder that you want to designate as your git repo, and tell git to start tracking inside that folder.
```
mkdir myrepo
cd  myrepo
git init
```
#### Check the status of your repository
Let's take a look at what's going on in our repo by running `git status`.
```
$ git status

On branch main

No commits yet

nothing to commit (create/copy files and use "git add" to track)
```

Okay, looks like we're on our `main` branch, we have no commits, and there's nothing to commit. Running `git status` before doing any git operation is a good habit, as it ensures you have the right things in place.

#### Make your first commit
Okay, so let's create a file inside your repo. You can do this via your gui, your favorite text editor, or via the command line.

Let's create a file named "newfile" and fill it with "some content"

```
echo "some content" > newfile
```


### Commit your Changes
#### What is a commit?
Commits are the backbone of how git works. Essentially, a commit as a "snapshot" of every single file in your repository at the specfic time you created the commit. Commits are powerful because they act like an anchor point in your project history, where you can easily look at what the code looked like at that time. You could even go back in time to that point. The best way to see this is to create a commit!

#### Create your commit
Now that you've made some changes in your repo, let's run `git status` to see what git sees.

```
$ git status

On branch main

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)
	newfile

nothing added to commit but untracked files present (use "git add" to track)
```

Looks like git picked up the fact that you added a new file to your repo, but it says its untracked. Let's commit this change!

First, we need to tell git which files we want to commit. `git add` does this! Git add also can take `.` as an argument that means "add all files in this directory"
```
git add newfile
```
Let's see what's going on now:

```
$ git status
On branch main

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)
	new file:   newfile
```

Git status tells us that we're on the main branch (again, don't worry about this yet), we have not commited anything, and now, our newfile is "to be committed". This means that once we run `git commit`, the changes in newfile would be "snapshotted" in the commit. This state is called "staging" - we would say that newfile has been staged.

So, let's move on and make our commit! Every commit requires a commit message. Think of a commit message like a comment on what this commit does. If you're writing a feature or fixing a bug, you would mention what this commit does in the message. A message can be specified using the `-m` flag. In this case, we're just creating a new commit, so let's document that.

```
git commit -m "Initial commit"
```

Congrats, you've just made your first commit!

### Looking at your commits
Okay, you've created a commit, but like, what is it? Let's look at the commit history in your repository.
```
$ git log
commit 371fba22b9562c9888371518a95c72ecd072cbc5 (HEAD -> main)
Author: Ashley Cui <acui@redhat.com>
Date:   Wed Jan 25 20:33:52 2023 -0500

    Initial commit
```
Using the `git log` command, we can see every commit that has been made in your repo. Git log shows you some information, including the commit hash, the author of the commit, and the commit message. The most important thing here is the commit hash, as this is globally unique to the commit. When doing other advanced git operations, you can specify the commit hash and it will guarantee that you will end up exactly here, at this commit.

Let's also take a closer look at your specfic initial commit:

```
$ git show 371fba22b9562c9888371518a95c72ecd072cbc5
commit 371fba22b9562c9888371518a95c72ecd072cbc5 (HEAD -> main)
Author: Ashley Cui <acui@redhat.com>
Date:   Wed Jan 25 20:33:52 2023 -0500

    Initial commit

diff --git a/newfile b/newfile
new file mode 100644
index 0000000..2ef267e
--- /dev/null
+++ b/newfile
@@ -0,0 +1 @@
+some content
```

Here, it shows the same commit information, as well as something called a diff. A git diff a powerful command that shows every changed line in every folder between two commits. If you ever need to compare two commits, you can read more into the `git diff` command.

## Branches and merges
A branch is a separate line of development within a repository. In the diagram below, we can see that there is a main branch, and another branch, `mybranch`
```
a - b - c - d   "main"
     \
       e - f    "mybranch"
```

From the git documentation, a branch is essentally "diverging from the main line of development and continue to do work without messing with that main line." Conventionally, the "main" branch is where complete features land, so usually its best practice to implement new things on a branch, and then merge it back into the main branch after the feature is complete.

### Branching
Lets make a new branch:

```
git branch mybranch
git checkout mybranch
```
The git branch command creates a new branch called "mybranch", and the git checkout command switches the branch that you're currently working on. You can see all your local branches using the `git branch` command. The asterisk denotes that we're currently on "mybranch"

```
$ git branch
  main
* mybranch
```

Let's create another file and commit, working on mybranch.

```
echo "more content" > anotherfile
git add .
git commit -m "created anotherfile"
```

We can see that we created a new commit on mybranch!
```
$ git log
commit d5362fc371d909e5299312b0d6bdfbd0c8fc683e (HEAD -> mybranch)
Author: Ashley Cui <acui@redhat.com>
Date:   Wed Jan 25 21:31:01 2023 -0500

    created anotherfile

commit 371fba22b9562c9888371518a95c72ecd072cbc5 (main)
Author: Ashley Cui <acui@redhat.com>
Date:   Wed Jan 25 20:33:52 2023 -0500

    Initial commit
```

### Merging
Remember, this feature at the moment only exists on our current branch. Let's get it onto the main branch via a merge:

```
git checkout main
git merge mybranch
```
Now, the commit you created on mybranch exists on the main branch!
