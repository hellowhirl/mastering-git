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
