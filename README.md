# Mastering-git

Notes for mastering Git from cli and gui

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

2\) by HEAD pointer, view last commit\*:

```
git show HEAD
```

\* we can specify previous commits using tilde and how many steps we want to go back

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

```
git checkout ffbj35
```

`detached HEAD` state: when we check out a particular commit the HEAD pointer will move to that commit - HEAD is no longer attached to a branch anymore, and it points to a certain commit.

- In this state we should not create any new commits - only view it (look around) and make experimental changes.
  - If we create a commit in this state then that commit is not reachable by any other commits or pointers (a dead commit); Git would later check for these commits later and remove it to save space

To return from 'detached HEAD' state we can attach HEAD pointer back to the last branch (such as master):

```
git checkout master
```

Or, if we are unsure of which branch we have attached from, we can try\*:

```
git checkout -
```

\* This also tries to check out your last checked out branch.

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

Also tell Git where there is a good commit (can be anywhere, but a commit ID that is more recent will require fewer steps).

```
git bisect good cj9025
```

- If we are not sure where to start then running `git checkout __commitID__` can help us find a good commit

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

To see list of all local and remote branches:

```
git branch -a
```

To see what branch we are currently working on:

```
git status
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

If we can see a linear path then we can (e.g. get directly from a branch to master) then Git will fast-forward automatically when we merge

The --graph option gives us a better idea on how our braches have diverged. Always better to include it:

```
git log --oneline --all --graph
```

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

### Aborting a merge

- When doing a merge we may run into 1 or more conflicts
- When we start a merge we may not have enough time to resolve all the conflicts

To go to the previous state before the merge:

```
git merge --abort
```

### Undoing a faulty merge

- In situations where we don't combine the changes properly, we need to undo the merge and then remerge. We have 2 options:

1\) Remove the merge commit (as if it was never there)

```
git reset --hard HEAD~1
```

- HEAD then points to previous commit (on master in this case)
- `HEAD~1` means 1 commit before the last commit
- if we run `git log` then we will see that the merge commit is no longer in the history; however, it is still in our repository. If we make a note of its commitID then We can recover it with:

```
git reset --hard __commitID__
```

When resetting the HEAD pointer we have 3 options

`--soft`

- Git will have the HEAD pointer point to a different snapshot, but our working directory and staging area are not affected

`--mixed`

- Git will take that snapshot and put it in the staging area as well (local changes in our working directory will stay there)
- this is the default option, so we don't need to specify it

`--hard`

- All environments are going to look identical (the state we were in before we started the merge)
- Git will take that snapshot and put it in our working directory as well (in addition to staging area and last snapshot)

- be careful as this will rewrite history)
- rewriting history is ok if the history is local in our repository
- but if we have shared our commits with other team members or it's on a remote repository then we shouldn't rewrite history

2\) Revert the merge commit

- this approach creates a new commit that will cancel all the changes in faulty merge commit
- especially applies if we have shared our history with someone else

A merge commit has 2 parents. To revert this commit we have to tell Git how we want to revert the changes

```
git revert -m 1 HEAD
```

- the `-m 1` option specifies the first parent of a merge commit (usually that is the master branch)
- `HEAD` is the target commit (which is the last/current commit - our merge commit)
- it's fine to accept the deault commit message
- if we check our history we will see a new 'Revert "Merge branch ...` commit

### Squash merging

- In the case when we don't have good quality commits
- Create a new commit that combines all the changes in a bugfix branch (represents single logical commit that represents all the changes)
- A squash commit is not a merge commmit because it doesn't have 2 parents
- We are then left with a single and clean linear history (we can also delete the bugfix branch)
- Squash mergers should only be used with small, short-lived branches with bad history (bugfixes, quick features, etc.)

From our main branch:

```
git merge --squash bugfix/photo-upload
```

- Git will create a squash commit with all the changes combined
- but there is no commit message yet; these changes will still be in the staging area
- next create a commit message that is meaningful and summarizes all the changes in our bugfix branch

```
git commit -m "Something meaningful"
```

- If we check git log then we will see our bugfix trailing off
- When we check `git branch --no-merged` then we will see this branch still remaining, which is confusing
  - this is because we do not have a merge commit that connects our bugfix branch
- Be sure te delete our squashed branch:

```
git branch -D bugfix/photo-upload
```

- if there are any conflicts before the merge then resolve as usual

### Rebasing

- This is another tehcnique to bring changes from one branch to another for when our history gets long and complicated
- Allows us to do a fast forward merge from master into new branch
- Rebasing rewrites history
  - ok for local changes
  - otherwise if working with shared branches in a team, it can create a big mess
  - Git will create new commits that resemble the changes on our other feature branch
  - this can get messed up if other team members push their code on top of that; their history would be lost

#### 1.) First we switch to our target branch, then we tell Git to change the base of this other branch to the last (latest) commit on master:

```
git switch feature/shopping-cart

git rebase master
```

#### 2.) Most of the time in the real world, we will end up with conflicts

When we have a merge conflict upon rebase we can use our merge tool or VS Code:

```
git mergetool

// fix the conflicts

git rebase --continue
```

- in VS Code after fixing conflicts then we still have to add the fixed files to the staging area (with `git add` or using VS Code features)

Other options:

`--skip`

- skips the current commit and move on to next commit (when we don't care about a conflict)

`--abort`

- when we don't have enough time to resolve conflicts we can just abort the rebase here (takes us back to previous state before we started rebasing)

Afterward we may see an extra file like 'toc.txt.orig' created by P4Merge. To remove it we can use `git rm` or this command\*:

```
git clean -fd
```

\* assuming we don't have any other untracked files that we want to add to our repository

For the future, in order to prevent our merge tool from adding these types of files:

```
git config --global mergetool.keepBackup false
```

#### 3.) to overwrite anything we have previously commited to this side branch then we will have to force push this new ordering of commit history with:

```
g push -f
```

#### 4.) If we succesfully rebase on our sub-branch and decide to merge to master then Git will do a fast-forward merge to bring the master pointer upward

```
git switch master

git merge feature/shopping-cart
```

### Cherry picking

Situation: when some change in another branch has some interesting changes that we want to have in master, but we are not quite ready to merge our feature branch into master

- take a particular commit and apply it on top of master

From the master branch we can take a commitID from another feature branch

```
git cherry-pick __commitID__
```

- when we check our git log we can see that master has moved forward and we have cherry picked the commit from the feature branch

### Picking files from another branch

Situation: when we are only interested in bringing in a single from another branch rather than an entire commit

To bring the toc.txt file to our master branch, first switch to master then:

```
git restore --source=feature/send-email -- toc.txt
```

- this is similar to restoring a file to an earlier version (in this case we are specifying a branch name)
- \-- lets Git know that this is a file name
- then commit the change to the master branch

### Branching in VS Code

- we can use GitLens plugin
- if we name our branches `bugfix/something` or `feature/something` then GitLens will automatically create folders for us
- the extension Git Graph will allow us to see our branches in a graph
  - we can access it in the source control panel, under a new icon that resembles a graph (it's beautiful)

## Collaboration

### Workflows

Centralized workflow

- colloborators can push/pull changes into central repostiory to be in sync
- everyone has push access to write to the central repository (most common)

Integration-manager

- someone is in charge of integrating changes
- for open source projects
- 1 or more maintainers and many contributors
- only maintainers have push/write access
- outsiders cannot push to official repository so that's why we have to fork it to get a copy in the cloud
  - because forked repo is in the clould it is also visible to the maintainer of the project, and that is how they can pull in our changes

If we want to contribute then follow these steps:

1. fork repo to get a copy of it in the cloud
2. clone the forked repostiory to get a local copy on our machine
3. make a few commits and then push our work to our forked repostiory
4. send pull request to maintainer of the project
5. maintainer then pulls in our changes and reviews them
6. if changes are good then they can merge our work into their local repository
7. finally they can push the merge changes into the official repostiory

### GitHub repositories

- When we select README file then GitHub will create the first commit for us (handy)
- Even though a repo is public, only the repo owner can push/write to it

To give others push access to our repository:

- Go to repo Settings -> Manage Access -> Invite a Collaborator

### Cloning a repository

```
git clone git@github.com:hellowhirl/react-seconds-counter-game.git AlternativeProjectName
```

- we can change the name of the project folder in our local by specifying at the end like "AlternativeProjectName"

When we look at our git log we will notice these 2 pointers: `origin/main` and `origin/HEAD`

- when we clone a repository GIt names the source repository (the one on GitHub) as `origin`
- `origin/master` tells us where is the master branch in that repository (on GitHub)
  - useful because we can start to have multiple repositories that can evolve independently
  - techincally this is what we call a `remote tracking branch`: cannot check it out or commit to it
- `origin/HEAD` tells us where is HEAD pointer in our origin repository

To see the list of remote repositories (repos that are not on our machine/current directory):

```
git remote
```

To see more details about remote repository we can use the verbose option:

```
git remote -v
```

### Fetching

To download new commits from the remote repository\*:

```
git fetch origin
```

- moves origin/master forward
- even though we have downloaded a new commit, our working directory is not updated
- \* if we leave out `origin` then Git assumes that we want to fetch commits from origin anyway: `git fetch`

To bring in new changes from origin/master, we have to merge into master:

```
git merge origin/master
```

- if we have a direct linear path then we will have a fast forward merge
- on the other hand, if we have a conflict then we would have to resolve it

To see how our remote and local branches are diverging:

```
git branch -vv
```

- shows the branches that are linked and if it is behind by a # of commits

### Pulling

Most of the the time when we want to bring the changes from remote repo into our local we need to do a `fetch merge`

- we can combine these two commands with a `pull`
- when our branches have diverged Git is going to download a new commit from the remote and put it into our local repository
- In this situation our local branch has diverged from the remote. So, Git will then perform a 3-way merge (creating a merge commit) to bring the remote changes into our master branch:

```
git pull
```

will give us non-linear history:

```
*   c9628cc (HEAD -> main) Merge branch 'main' of github.com:hellowhirl/mastering-git into main
|\
| * 49e0e21 (origin/main, origin/HEAD) Update README for demonstrating pull command
* | d389423 WIP - about pulling
|/
* a256ed4 fetching
```

Alternatively we can use rebasing (which approach is better depends):

```
git pull --rebase
```

will give us linear history:

```
* d0409a1 (HEAD -> main) WIP - about pulling
* 49e0e21 (origin/main, origin/HEAD) Update README for demonstrating pull command
* a256ed4 fetching
```

- Git is going to change the base of our master and move it up a commit(s).
  - it is replaying our local changes on top of the changes made by others in origin/master

### Pushing

When our local branch is ahead of origin/master we can push our new commit(s) to origin with:

```
git push origin master
```

- if we are on master branch then we can drop master
- we can also drop origin because Git will assume origin by default

```
git push
```

- Sometimes our push may get rejected
  - DO NOT USE: `git push -f` unless we have screw something up
  - to fix we first have to do a pull
  - then do a merge or a rebase
  - fix any conflicts (if any) and then do a push

### Storing credentials

To push our commits we have to enter our GitHub credentials (username and password). We can store our credentials in memory or on disk and tell Git where to find it

To set a configuration value (for 15 minutes in memory):

```
git config --global credential.helper cache
```

To store credenials on Mac disk we should use Keychain on Mac, check if Keychain Helper is installed:

```
git credential-osxkeychain
```

Then to store credentials on the Mac:

```
git config --global credential.helper osxkeychain
```

### Sharing tags

By default the push command doesn't transfer our tags to a remote repository.

To explicitly push a tag named "v1.0" to remote:

```
git push origin v1.0
```

- if we look on GitHub we can see automatic assets generated for us
- we can download our entire source code from that point of time (very useful)

To remove tag from origin

```
git push origin --delete v1.0
```

### Releases

- On GitHub we can create a release to package our software along with source code, binary files and release notes

  - this is a high level feature built on top of tags

Creating a new release:

- when we creat a release GitHub will automatically add our tag to the latest commit
- select target branch (like master)
- add a title (can be same as tag name like v1.0)
- in the Write section we can add our release notes, and using hyphens (-) we can add bullet points (Markdown syntax)
- if the release is not production ready then we can tick "This is a pre-release"
- Click "Publish release" when ready
- we can also edit or delete a release on the release page

### Sharing branches

- our branches are private (local) by default
- to collobrate with others using a branch then we have to explicitly push that branch (same as we do for tags)

If we create a branch from our local with `git switch -C feature/testing` and then `git push` we will see:

```
fatal: The current branch feature/testing has no upstream branch.
```

- this branch is not linked to a branch in origin

To see all remote tracking branches:

```
git branch -r
```

To link a private branch to a branch in origin:

```
git push -u origin feature/testing
```

- `-u` is shorthand for `--set-upstream`
- then type remote branch followed by target branch
- we can then see that this branch is linked to remote tracking branch with `git branch -vv`

If we want to link the current branch we are working on, just use `HEAD` instead of name of branch:

```
git push -u origin HEAD
```

At some point, we will want to remove this branch with:

```
git push -d origin feature/testing
```

- we still need to delete the branch from our local with `git branch -d feature/testing`

### Collaboration workflow

- a new branch was created from GH
- To get new remote branches (and tags) from origin we run `git fetch`.
- But when do `git branch` we won't see our new branch.
- We can only see it with `git branch -r`.

To creat a private branch in our local repository that maps to this remote tracking branch:

```
git switch -C feature/change-password origin/feature/change-password
```

- we will then see "Branch 'feature/change-password' set up to track remote branch 'feature/change-password' from 'origin'
- now we can add new commits to this branch from our machine and then push them to origin

#### The rest of the flow

- after someone else pushes commits to that branch on origin, while on that branch we can pull in their changes with `git pull`.
  - we can also do `git pull` from master branch periodically
- merge changes from new feature into master
- send these changes to GH with `git push`

#### Cleaning up

- we can do `git push -d _____` and `git branch -d _____`

Finally, to remove tracking branches that are not on the remote from our local machine:

```
git remote prune origin
```

### Pull requests

Opening a discussion with the team before merging into master

We can use 'Compare & pull request' or 'New pull request' buttons on GH

- add reviewer(s)
- (click away from from Review changes button as it's confusing)
- go back and forth with comments about lines of code
- write general review comment and then click 'Finish your review'
- mark as 'Comment', 'Approve', or 'Request changes'
- finally 'Merge pull reuest'
  - can be merged by reviewer or or person who started it (depends on culture of team)
- it is common practice to click 'Delete branch' after merging
  - if mistake was made we can always click 'Restore branch'
- clean up our lcoal environment with `git remote prune origin`

### Resolving Conflicts

"This branch has conflicts that must be resolved"

- we can use the coommand line, or the GitHub user interface
  - on command line: just like before
  - on GitHub:
    - delete the markers, make the chnages, then click `[Mark as resolved]`
    - commit changes onto the target branch (before the merge) and click `[Commit merge]`
    - Finally we can click `[Merge pull request]`

### Issues

- Issue tracking in GitHub goes hand-in-hand with pull requests
- We can encapsulate all the details (description, images, etc.) in a single place
- Able to assign people, add labels (including customizing these labels) and assign to a milestone
- When issue is submitted then we can have a conversation with the team about it
- We can link the issue to a pull request - so issue will be automatically closed when merge request is successfully merged

### Labels

- From Issues tab, When we click on `[Labels]` we can see all the default labels that exist on every GitHub repo
  - we can edit these as we please
- We can also create new labels with a color of our liking (randomly generated)

### Milestones

- used to track the progress of various issues
  - add some issues to a milestone and then see how much we have progressed towards that milestone
- From Issues tab we can create a milestone with description, set a due date, and then assign it to an issue
  - when the issue is closed then the % completion will reflect in the milestone

### Contributing to Open-source Projects

2 ways:

- create a new issue from issues that we want to work on (feature/bug/etc.)
- we can go to Issues on GH and select an existing issue

Since we do not have push access to somoene else's repository we need to `Fork` it

- GH will take a complete copy of the repo and put it in our account
