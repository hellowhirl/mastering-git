# Mastering-git

Notes for mastering git from cli and gui

## Basics

Get same notes as git website:

```
git config --help
```

Press 'space' to go to the next page, or 'q' to exit

To get quick refresher:

```
git config -h
```

Initialize git repository:

```
git init
```

## Creating Snapshots

### Git Workflow:

- Creating a commit is like taking a snapshot of our project
  - Each commit contains a complete snapshot of our proejct and this allows to quickly get back to a previous state
- Before saving snapshot to our repository it goes to the staging environment (different from other VC systems)
- Staging environemnt allows us to review our changes before we finally commit them to the repository
- Overall, Git compresses the content and doesn't store duplicate content

Even wheh we remove `file2` from the project we can still use below to remove from staging environemnt (Git knows to delete it)

```
git add file2
```

Standard command for writing content to a file:

```
echo hello > file1.txt
```

Add all files with .txt extension

```
git add *.txt
```

Append content to a file with `>>`

```
echo world >> file1.txt
```

If we run `git status` after modifiying a file but before staging with `add` then the status will return `modified: file1.txt`

To write a longer commit message which will open in our default editor:

```
git commit
```

- GIt will then tell us which files were changed and how many lines were inserted

### Commiting Best Practices

- commits shouldn't be too big, or too small
  - if you are doing too many things in 1 commit, then it will be hard to come up with a good commit message
- "As you reach a state you want to record, THEN commit
- best to differentiate below into 2 commits, not together
  - Bug fix
  - Fixed a typo
- create meaningful commit messages
- stick to a certain tense:
  - PRESENT: Fix the bug. (most used)
  - PAST: Fixed the bug.

### Skipping the Staging Area

99% of the time we should add to staging. However, to skip we use one of below (-a means "all modified files"):

```
git commit -a -m "Fix the bug that freezes the thank you page"
```

```
git commit -am "Fix the bug that freezes the thank you page"
```

### Removing files

Show information about files in the index and the working tree

```
git ls-files
```

Flow for removing a file:

```
rm file2.txt
git add file2.txt

# we can also use below as this is a common operation
git rm file2.txt
```

- `git rm` will remove a file from the staging area AND the working directory

When we use `git status` and we see green files, those are in the staging area

### Renaming files

Below 2 commands acheive same result:

```
mv file1.txt main.txt
git add file1.txt
git add main.txt

git mv file1.txt main.txt
```

### Ignoring files

Files that we don't want or need to share/synchronize with other team members

Create file called `.gitignore`

```
logs/
main.log
*.log
```

`logs/` - indicates a directory to ingore:

`main.log` - a specific file

`*.log` - a pattern for all .log files

Remember: .gitignore only works like this if we haven't already included a file or directory in our repository

- If we accidentally include a file in our repostiory and then later add it to .gitignore, git is NOT going to ignore it
- In this case git is not going to ignore the changes in this directory because it's already tracking this directory
- To solve this problem we have to remove this file ONLY from the staging area which is what we will be propssing for the next commit

  - for the `--cached` flag we see it only removes from the "index", which is the old term for "staging area"

    ```
    git rm --cached -r bin/
    git commit -m "remove the bin/ directory that was accidentally commited"
    ```

  - From this point forward git is no longer going to track changes in bin/ directory

There are various .gitignore templates available on GitHub

### Short status

Alternative to git status is `git status -s` to show more concise summary

- we have 2 columns: left represents "staging area", and right column represents the "working directory"
- a red "M" in the right column means we have some changes but they are not in the staging area
  - when we add to staging area then we will see green "M" in the left column for staging area, and nothing in right column
  - if we modify again then we will see green "M" in left column and red "M" in right column
- for a new file we will see 2 ?'s like "??"
  - when we add to staging area then we will see green "A" in left column which represents "added"

### Viewing staged and unstaged changes

See the changes in files in staging area that will go into next commit

```
git diff --staged
```

See the changes in files between staging area and working directory

```
git diff
```

### Virtual dIff tools

- There was once a practice to use Diff Tools like KDiff3, P4Merge, etc. but now most people just use built-in functionality in VS Code
- After setting up `difftool` in .gitconfig file we can use below command to launch in our default editor:

  ```
  git difftool --staged
  ```

### Viewing history with git log - Basics

- `git log` lists all the commits we have created, and if there are many we can go to next page with `spacebar`
- Each commit has a unique identifier which is a unique 40 character string
- `master` or `main` is the main branch (line of work) in git
- `HEAD` is a reference to the current branch; this is how git knows which branch we're currently working on

See git commits in reverse order:

```
git log --reverse
```

Simple comprehensive view of commits in history:

```
git log --oneline
```

### Viewing a commit

There are 2 ways to reference a commit (and see differences)

1\) by unique identifier, shorter if they are unique characters:

```
git show d64723
```

2\) by HEAD pointer, view last commit:

```
git show HEAD
```

- specify previous commits using tilde and how many steps we want to go back

  ```
  git show HEAD~1
  ```

To see exact version of file that is stored in a commit:

```
git show HEAD~1:.gitignore
```

To list all the files and directories stored in a commit:

```
git ls-tree HEAD~6
```

- `ls-tree` means list all the files in a tree
- files are represented using `blob` and directories are represented using `tree`
- and then using the `show` command we can view an object in git's database, like Commits as well as Tags

### Unstaging files

In the past people used `git reset` but it was confusing with flags like `--hard` or `--soft`, but now we use `git restore` to unstage files

In single or multiple files:

```
git restore --staged fileName1.js fileName2.js
```

For all files in staging area:

```
git restore --staged .
```

For all files in working area (local):

```
git restore .
```

- this will remove changes to staging area by replacing with changes in next environment, which is the last commit in our repo
- with a new file, git will return the file to its previous state, which is a new untracked file

### Discarding local changes

For new untracked files `git reset` will NOT WORK for new files, because a record of it doesn't exist anywhere in our environments. So instead we use the `clean` command

To remove all untracked files, including directories forcefully:

```
git clean -fd
```

To get interactive menu before cleaning:

```
git clean -i
```

### Restoring a file to an earlier version

We can always restore a repository to a previous version using:

```
git restore --source=HEAD~1 testingFile.js
```

- the `restore` command by default will restore files from the next environment up (commit -> staging, staging -> working directory)
- so here we change the default source to the commit before the last one, and specify the file we want to restore to previous version

## Browsing History

### Viewing the history

To see all the files that have been changed in each commit we use the `--stat` option:

```
# full details
git log ---stat

# shortened
git log --oneline --stat
```

To see what has been changed for each file in a commit:

```
git log --oneline --patch
```

### Filtering the history

```
# filter by author
git log --oneline --author="Mosh"

# filter by date
git log --oneline --after="2020-08-17"

# filter by relative date
git log --oneline --after="yesterday"
--after="one week ago"
--after="two days ago"
--after="one month ago"

# filter by words in commit message (case sensitive)
git log --oneline --grep="GUI"

# filter by content in the files
git log --oneline -S"hello()"

# filter by content in the files, and to see the actual changes in each commit
git log --oneline -S"hello()" --patch

# filter commits in a particular range
git log --oneline fb0d184..edb3594

# filter commits that have modified a particular file
git log --oneline toc.txt

# filter commits that have modified a particular file, if git complains that file name is ambiguous
git log --oneline -- toc.txt

# filter commits that have modified a particular file, and see actual changes in file
git log --oneline --patch -- toc.txt
```

### Formatting the log output

We can customize the output of the log command - refer to the docs

```
# dynamically display author name, commmit hash, commit date, colorize, and more
g log --pretty=format:"%Cgreen%an%Creset committed %h on %cd"
```

### Aliases in Git

We can set up aliases specifically for Git

```
git config --global alias."log --pretty=format:'%an committed %h'"
```

### Viewing a commit

Go to the HEAD (last commit) and then go 2 steps back

```
git show HEAD~2
```

If we want to see the final version of a file in a commit

```
git show HEAD~2:path/to/file.txt
```

See all files that have been added, removed or modified in a commit

```
git show HEAD~2 --name-only
```

See all files that have been added, removed or modified in a commit and see that status marked

```
git show HEAD~2 --name-status
```

## Viewing the changes across commits

If we want to see what has been changed in every file over last 3 commits

```
git diff HEAD~2 HEAD
```

If we want to see what has been changed in a single file over last 3 commits

```
git diff HEAD~2 HEAD specificFile.txt
```

We can also add flags like `--name-status` and `--name-only` to view the list of files that have been changed

### Checking out a commit

As we create more commits, MASTER continues to move and points to the last commit we created so far

HEAD: a special pointer that points to the current branch we are working on

`HEAD -> master`: HEAD is pointing to master

Checkout to attach HEAD pointer to a previous commit:

`git checkout ffbj35 `

`detached HEAD` state: when we check out a particular commit the HEAD pointer will move to that commit - HEAD is no longer attached to a branch anymore, and it points to a certain commit.

- In this state we should not create any new commits - only view it (look around) and make experimental changes.
  - If we create a commit in this state then that commit is not reachable by any other commits or pointers (a dead commit); Git would later check for these commits later and remove it to save space

To attach HEAD pointer to the master branch:

`git checkout master`

### Finding bugs using Bisect

When we don't know where a bug was introduced, we don't want to checkout each individual commit, it's going to take too long.

Using the bisect command we can divide our history in half and checkout various commits to find the first bad commit - we can quickly find the commit that introduced the issue (bug)

To start the bisect operation

```
git bisect start
```

Tell git where we know a bad commit is (at the current position, HEAD)

```
git bisect bad
```

Also tell Git where there is a good commit

```
git bisect good cj9025
```

This will take us to the middle of the commit history.

Then we can run our application or run our automated tests to see if the issue is still there or not.

```
git bisect good
```

Once again we run our application or run our automated tests to see if the issue is still there or not.

```
git bisect bad
```

Keep doing until we find the last bad commit.

```
git bisect bad
```

Then Git will know the first bad commit, and we will get full information about this commit (author, date/time, message, summary of changes)

When we are done we have to attach the HEAD pointer to the master branch.

```
git bisect reset
```

### Finding contributors using Shortlog

To find all the people that have commited to our project and who are the most active contributors:

```
git shortlog
```

Also there are options to see number of commits, summary, email, filter by date, etc.

```
git shortlog -n -s -e --before="" --afterr=""
```

### Viewing the history of a file

We can find all the commits that have touched a specific file, or files.

Simply append the file name at the end of `git log`

```
git log --oneline --patch toc.txt
```

### Restoring a deleted file

We make a mistake:

```
git rm toc.txt
git commit -m "accidentally delete file, for demonstration"
```

To show all the commits that have touched "toc.txt" file

```
git log --oneline -- toc.txt
```

To restore the file we have to look at the parent of the latest commit (before it was deleted).

Then we commit the file (which will show in the staging area)

```
git checkout a70r52 toc.txt

git commit -m "Restore toc.txt"
```

### Finding the author of a line using blame

To find the author of a particular line

```
git blame audience.txt

# to return email
git blame -e audience.txt

# to list only first 3 lines that were changed
git blame -L 1,3 audience.txt
```

### Tagging

We use tags to bookmark certain points in the history of our project

To give a lightweight tag to commit that HEAD is pointed to:

```
git tag v1.0
```

For specific commit just add the commit id:

```
git tag v0.7 c389d4
```

Then we can reference a commit using its tag, exactly the same we use a commit id

```
git checkout v1.0
```

Annotative tag (preferred):

```
git tag -a v1.1 -m "a message associated with this annotative tag"
```

To see the message associated with a tag:

```
git tag -n
```

To see more info about the tag as well full details of commmit:

```
git show v1.1
```

To delete a tag:

```
git tag -d v1.1
```

## Branching

A branch in git is just a pointer to a commit - the Master branch is just a pointer to the last commit in the main line of work

- As we make new commits git moves this pointer forward automatically (and stores this latest snapshot)
- When we create a new branch git creates a new pointer that can be moved around
  - Git moves this pointer forward with new commits as well (while Master pointer stays where it is)
- This pointer is just a tiny file that contains a 40-byte commit id
- HEAD is a special pointer which is another tiny file that contains a name of current target branch (like Master)
  - When we switch to a different branch git moves the HEAD pointer around and updates the tiny file with the name of the target branch (current one we are working on)

### Working with branches

When we switch branches Git resets our working directory to the snapshot stored in the last commit of the target branch

To create a new branch called "bugfix":

```
git branch bugfix
```

To see list of branches:

```
git branch
```

To see what branch we are currently working on:

```
git status
```

To see list of all local and remote branches:

```
git branch -a
```

- The asterisk (\*) indicates what branch we are currently on

To switch branches there are 2 methods:

```
git checkout bugfix

git switch bugfix
```

- `git switch` is more preferred these days because `checkout` was confusing

To rename a branch:

```
git branch -m bugfix bugfix/signup-form
```

- a yellow marker in zsh by our branch indicates that our branch is dirty (has some untracked changes)

To view commits across all branches use the `--all` option:

```
git log --oneline --all
```

To delete a branch:

```
git branch -d bugfix/signup-form
```

- use `-D` (capital D) to force the deletion, otherwise if the changes aren't merged upstream yet then it git will stop us

### Comparing branches

- As we commit to our branches, we need to know how they are diverging from master
- At some point in the future we are going to merge the bugfix branch into master

To find out what commits are coming in from another branch (in this case from bugfix to master):

```
git log master..bugfix/signup-form
```

To see the actual changes:

```
git diff master..bugfix/signup-form

// more concise way*
git diff bugfix/signup-form
```

\*remove `master..` which just refers to current branch

To just see what files are going to be affected:

```
git diff bugfix/signup-form --name-status
```

### Stashing

If we have local changes in our working directory that we haven't committed yet, these changes would get lost

- In this situation git doesn't allow us to switch branches
- We will get warning

```
error: Your local changes to the following files would be overwritten by checkout:
	audience.txt
Please commit your changes or stash them before you switch branches.
Aborting
```

In situations where we don't want to commit our changes yet then we should stash our changes

- stores our uncommitted in a safe place
- won't be part of our history
- will show up as green number count on command line

```
git stash push -m "New tax rules."
```

To include new files that we add we have to use the --all (-a) option.

- By default new files will not be included in a stash
- We can combine -a and -m as -am

```
git stash push -am "New stash message."
```

To stash away all unstaged changes:

```
git stash push -u
```

View all stashes:

```
git stash list
```

- Each stash has a unique identifier: stash @ {index}

To see changed in our stash compared to our working directory:

```
git stash show stash@{1}

// shorter way
git stash show 1
```

To apply changes from one of our stashes to our working directory:

```
git stash apply 1
```

Afterward to clean things up, we should delete the stash:

```
git stash drop 0
```

To delete all stashes:

```
git stash clear
```

### Merging

2 types of merges:

- Fast-forward merges: if branches have not diverged and there is a direct linear path then git just brings Master pointer forward to the latest commit of the branch that is ahead
  - When the merge operation is complete then the pointer is removed for the branch that we merged into the main branch
- 3-way merges: when branches diverge Git needs to reference 3 commit points: common ancestor, tip of Master, tip of new branch
  - based on these points GIt will figure out a way to combine all the changes and will create a new Merge Commit

### Fast-forward merging

The --graph option gives us a better idea on how our braches have diverged. Always better to include it:

```
git log --oneline --all --graph
```

- if we can see a linear path then we can e.g. get directly from a branch to master

Create a new branch and then switch to it:

```
git switch -C bugfix/login-form
```

### No fast-forward merging

from master branch

```
git merge --no-ff bugfix/login-form
```

- this tells git: even if fast-forward merge is possible, don't do it
- instead, create a merge commit that combines all the changes in the target branch and bring them into master
- it will bring up an autoamted merge commit message, which is usually fine

Pros and Cons of merge commits:

- Pros:
  - they are true refletion of history
  - make it easier to undo a feature (just a single commit to revert, as opposed to multiple)
- Cons:
  - pollutes the history and the linear history view is lost

To disable fast-forward merges in current repository:

```
git config ff no
```

To disable fast-forward merges globally:

```
git config --global ff no
```

### 3-Way merges

- When two branches diverge Git is going to look at the commit tips of both branches and compare them with their common ancestor
  - we can see this visually with the `--graph` option using `git log`
- Git is going to figure out how to merge or combine the changes into a new merge commit

```
git merge feature/change-password
```

- usually we can accept the deault merge commit message as it is
- when we look at our history again we can see the result of the merge
  - our master branch has moved up and is pointing to a new merge commit

### Viewing merged and unmerged branches

- When we are done with a branch, typically we should merge into master and then delete it
  - We may forget about some branches we forgot to delete, and these branches/pointers become stale, creating confusion in the future

To view list of branches that we have already merged into the current branch (most often it's master):

```
git branch --merged
```

- any branches showing here should be deleted

To return list of branches that have not been merged into the current branch:

```
git branch --no-merged
```

### Merge conflicts

Cases when Git cannot figure out how to merge changes:

- Change1, Change2 (same line of code has been changed in 2 branches)
- Changed, Deleted (a file)
- Add1, Add2 (same file name but different contents)

When we merge a branch with a conflict into our current branch we will see the message:

`CONFLICT (content): Merge conflict in foobar.txt`

- Git will stop the merge process and then we will have to manually combine the changes
- `Unmerged paths:` will show us the conflicted files
- Use VS Code's merge conflict tool or edit lines of code manually
- We should not introduce any new lines of code when fixing a merge conflict
  - This is called an `evil commit`
  - The whole point of a merge commit is to combine changes across 2 branches
  - However, there are real word situations where you need to introduce some new changes to make things work

### Graphical merge tools

Merge tools\*:

- Kdiff
- P4Merge

\* Powerful IDE's like IntelliJ and GitKraken don't require 3rd party merge tools

To set our default merge tool to P4Merge:

```
git config --global merge.tool p4merge
```

To tell Git where to find P4Merge:

```
git config --global mergetool.p4merge.path "/Applications/p4merge.app/Contents/MacOS/p4merge
```

To edit a conflict using our external merge tool:

```
git mergetool
```

We will see these 3 branches:

- REMOTE: target branch (incoming change) which is our bugfix branch
- BASE: the original verison (version of file before our branches diverged)
- LOCAL: current branch (in most cases it's master)

A merge tool like P4Merge let's us visually see the changes

- the pane on the bottom shows us how the changes will get combined
- we can use purple and green icons to choose which change we want to keep
  - hold down shift and we can keep both changes as well
- Browse through other conflicts in the merge by using the red arrows above
- When we are done we need to save the changes, then go back to terminal and then `git commit` (without message)
  - accept the default commit message, then we have succesffully merged our branches
