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

## Remote Git
Remember, everything you just did exists only on your local computer. Git becomes much more powerful when collaborating with others, and that's where remote repositories and Github comes in.

### What is a remote repository?
Basically, a repository that lives on the internet.

### Set up github
Make a github account! https://github.com/

### Fork a repository
## What is a fork?
Github does a good job of explaining what a fork is: A fork is a copy of a repository that you manage. Forks let you make changes to a project without affecting the original repository. You can fetch updates from or submit changes to the original repository with pull requests.

## Fork a repository
Let's fork this repository! Go to https://github.com/ashley-cui/git-workshop-shaw, and in the top right corner, click the fork button. This creates a copy of this repository that's owned by you! You should see a repo on your github profile at https://github.com/your-username/git-workshop-shaw. Now, any new work that you do will be on your fork.

## Clone a repository
In order to make changes to your fork, you need to have the repository locally. This is called cloning a repository - taking a remote repositiory and making a copy of it on your local machine.

```
git clone https://github.com/ashley-cui/git-workshop-shaw.git
cd git-workshop-shaw
```

Now, you should be inside a folder called git-workshop-shaw, which is a copy of your fork.

### Managing remotes
We can take a look at what remote repositories our current local repository is associated with using the `git remote` command.

```
$ git remote -v
origin	https://github.com/ashley-cui/git-workshop-shaw.git (fetch)
origin	https://github.com/ashley-cui/git-workshop-shaw.git (push)
```

This shows us that for `git fetch` and `git push`, if we use `origin` as an argument, git would fetch and push from https://github.com/ashley-cui/git-workshop-shaw.git.

### Pushing
When we create a local commit and want our remote repository to also have those changes, we need to push our commit. Use what you learned in the previous section and create a branch, add a file named "your name" and commit it. After, push your commit and branch using:

```
git push -u origin my-branch-name
```

The -u option tells git to create a new branch named my-branch-name on your origin remote repository. On subsequent pushes, you do not need to specify this, a generic `git push` would suffice.

Now, if you look at your fork on github, you should see a branch named my-branch-name. You should also see a notification that asks you if you want to create a pull request.


### Upstream and Pull Requests
#### Contributing upstream
Remember that you forked a repository, and remember that the commit you just pushed? Let's get that commit into the original repository, at https://github.com/ashley-cui/git-workshop-shaw! In order to do so, you need to create a pull request, since you do not have permissions to directly pull from the upstream repository. Use the GUI to open a pull request, and I'll merge them into the repository.

#### Keeping your branch up to date
Okay, now that a bunch of pull requests have been merged, you might want the most recent version of the repository, where everyone's name is in your local repository, as well as in your fork. In order to do this, we need to tell git to track the original repo. We can add this using `git remote add`.

```
git remote add upstream https://github.com/ashley-cui/git-workshop-shaw.git
```

This tells git that when you use the `upstream` argument on pushes and pulls, to push and pull from. Now, lets update your main branch.

```
git checkout main
git fetch upstream
git merge upstream/main
```

We switch to the main branch in order to update it. The `git fetch` operation tells git to go to download all the changes that happened upstream. This doesn't change anything on your actual local branches until you run `git merge`. These two commands are often used together In fact, `git fetch` and `git merge` used together is equivilent to `git pull`.

Run `git log` to see each other's commits!

Now, your local branch matches the upstream repository. However, your remote origin branch doesn't have the updates. You need to push these changes that you have locally to your origin branch.

```
git push
```

Now, check your remote origin branch, and it should have all the commits that exist upstream as well!

## More Advanced topics
### Merge conflicts
Occasionally, you may edit the same file and line of code as someone else.  A merge conflict is an event that takes place when Git is unable to automatically resolve differences in code between two commits. Git can merge the changes automatically only if the commits are on different lines or branches.

and when you go to pull or update your branch, git will not know how to resolve the merge, since it can't tell which version should be the latest. In this scenarion, you would have to open your text editor and manually merge the two files. Git usually will guide you through this process, so read the outputs and you'll navigate the merge conflict well. Generally speaking, you would need commit your corrected file, and run `git merge --continue`


#### Rebasing
Rebasing is another way to update branches who have diverged, as an alternative to merges. THe difference between merges and rebases is that merges preserves the history of both branches, while rebases rewrites history, by making it look like existing commits came after previous commits.

Rebases are particularly powerful, especially interactive rebases. I'd reccomend reading more into rebases, as they can solve a lot of problems, as well as create a lot of problems.

Original:

```
a - b - c - d   "main"
     \
       e - f        "mybranch"
```

Merge creates a merge commit "e"
```
a - b - c - d - g   "main"
     \        /
       e - f        "mybranch"
```


Rebase makes it look like that e always came after "e"
```
a - b - c - d               "main"
     \
       c - d - e - f        "mybranch"
```
#### Cherry Picking
Occasionally, you want one commit that exists in one branch in another branch. Git cherry pick does this. Say you have commit e on your main branch, and you want it on mybranch. To do this, you would run `git cherry-pick commit-hash-of-e` while on the main branch.

Original:
```
a - b - c - d   "main"
     \
       e - f        "mybranch"
```

Cherry picked e onto main branch
```
a - b - c - d - e  "main"
     \
       e - f        "mybranch"
```


