# Mastering Git

## Basics

Get same notes as git website:

`git config --help`

Press 'space' to go to the next page, or 'q' to exit

To get quick refresher:

`git config -h`

Initialize git repository:

`git init`

## Creating Snapshots

### Git Workflow:

- Creating a commit is like taking a snapshot of our project
  - Each commit contains a complete snapshot of our proejct and this allows to quickly get back to a previous state
- Before saving snapshot to our repository it goes to the staging environment (different from other VC systems)
- Staging environemnt allows us to review our changes before we finally commit them to the repository
- Overall, Git compresses the content and doesn't store duplicate content

Even wheh we remove `file2` from the project we can still use below to remove from staging environemnt (Git knows to delete it)

`git add file2`

Standard command for writing content to a file:

`echo hello > file1.txt`

Add all files with .txt extension

`git add *.txt`

Append content to a file with `>>`

`echo world >> file1.txt`

If we run `git status` after modifiying a file but before staging with `add` then the status will return `modified: file1.txt`

To write a longer commit message which will open in our default editor:

`git commit`

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

`git commit -a -m "Fix the bug that freezes the thank you page"`

`git commit -am "Fix the bug that freezes the thank you page"`

### Removing files

Show information about files in the index and the working tree

`git ls-files`

Flow for removing a file:

```
rm file2.txt
git add file2.txt

// we can also use below as this is a common operation
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

  - for the `--cached` flag we we see it only removes from the "index", which is the old term for "staging area"

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

`git diff --staged`

See the changes in files between staging area and working directory

`git diff`

### Virtual dIff tools

- There was once a practice to use Diff Tools like KDiff3, P4Merge, etc. but now most people just use built-in functionality in VS Code
- After setting up `difftool` in .gitconfig file we can use below command to launch in our default editor:

  `git difftool --staged`

### Viewing history with git log - Basics

- `git log` lists all the commits we have created, and if there are many we can go to next page with `spacebar`
- Each commit has a unique identifier which is a unique 40 character string
- `master` or `main` is the main branch (line of work) in git
- `HEAD` is a reference to the current branch; this is how git knows which branch we're currently working on

See git commits in reverse order:

`git log --reverse`

Simple comprehensive view of commits in history:

`git log --oneline`

### Viewing a commit

2 ways to reference a commit (and see differences)

```
// by unique identifier, shorter if they are unique characters:
git show d64723

// HEAD pointer, view last commit
git show HEAD
// previous commits using tilde and how many steps we want to go back
git show HEAD~1

```

To see exact version of file that is stored in a commit:

`git show HEAD~1:.gitignore`

To list all the files and directories stored in a commit:

` git ls-tree HEAD~6`

- `ls-tree` means list all the files in a tree
- files are represented using `blob` and directories are represented using `tree`
- and then using the `show` command we can view an object in git's database, like Commits as well as Tags

### Unstaging files

In the past people used to use `git reset` but it was confusing with flags like `--hard` or `--soft`, but now we use:

```
// single or multiple files
git restore --staged fileName1.js fileName2.js

//all files in staging area
git restore --staged .

//all files in working area (local)
git restore .
```

- this will remove changes to staging area by replacing with changes in next environment, which is the last commit in our repo
- with a new file, git will return the file to its previous state, which is a new untracked file

### Discarding local changes

For new untracked files `git reset` will not work for new files, because a record of it doesn't exist anywhere in our environments. So instead we use the `clean` command

```
// to remove all untracked files, including directories forcefully
git clean -fd

// to get interactive menu before cleaning
git clean -i
```

### Restoring a file to an earlier version

We can always restore a repository to a previous version using:

`git restore --source=HEAD~1 testingFile.js`

- the `restore` command by default will restore files from the next environment up (commit -> staging, staging -> working directory)
- so here we change the default source to the commit before the last one, and specify the file we want to restore to previous version

## Browsing History

### Viewing the history

To see all the files that have been changed in eacc commit we use the `--stat` option:

```
# full details
git log ---stat

# shortened
git log --oneline --stat

```

To see what has been changed for each file in a commit:

`git log --oneline --patch`

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
