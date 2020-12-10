# Version control with Git

>[Udacity Course Link (ud123)](https://classroom.udacity.com/courses/ud123)

## Create a Repo

### init

- git init
  - creates a hidden .git directory. It is the brain/storage center for the repository.

### clone

- git clone "path-to-repository-to-clone"
  - takes the path to an existing repository
  - by default will create a directory with the same name as the repository that's being cloned
  - can be given a second argument that will be used as the name of the directory
  - will create the new repository inside of the current working directory

### status

- git status

## Review a Repo's History

### log

- git log
- git log --oneline
  - List one commit per line
  - shows the first 7 chars of the commit's SHA
  - shows the commit's message
- git log --stat
  - displays the file(s) that have been modified
  - displays the number of lines that have been added/removed
  - displays a summary line with the total number of modified files and lines that have been added/removed
- git log --patch
  - displays the files that have been modified
  - displays the location of the lines that have been added/removed
  - displays the actual changes that have been made
- git log -p "SHA-of-a-commit"

### show

- git show
  - it can be combined with most of the other flags
- git show "SHA-of-a-commit"

## Add Commits to a Repo

### add

- git add "filename-of-file-to-add-to-staging-index"
  - git rm --cached "filename-of-file-to-add-to-staging-index"
   to unstage file
  - git add . -> to stage all the current directory and sub-directory
  - takes a space-separated list of file names
  - alternatively, the period "." can be used in place of a list of files to tell Git to add the current directory

### commit

- git commit
  - will open the default editor
    - a commit message must be supplied
    - lines that start with a "#" are comments and will not be recorded
    - save the file after adding a commit message
    - close the editor to make the commit
- git commit -m "text-of-the-message"

#### A "good" commit message

- do keep the message short (less than 60-ish characters)
- do explain what the commit does (not *how* or *why*)

### diff

The "git diff" command can be used to see changes that have been made but haven't been committed, yet.

- git diff
  - displays the files that have been modified
  - displays the location of the lines that have been added/removed
  - displays the actual changes that have been made

### .gitignore

A .gitignore file list the names of files that you want Git to ignore and it will ignore them.

**Globbing** lets you use special characters to match patterns/characters.

In the .gitignore file, you can use the following:

- blank lines can be used for spacing
- \# - marks line as a comment
- \* - matches 0 or more characters
- ? - matches 1 character
- \[abc] - matches a, b, _or_ c
- \*\* - matches nested directories
- a/**/z - matches a/z or a/b/z or a/b/c/z

## Tagging, Branching, and Merging

### Tagging

- git tag -a "label-for-the-tag"
  - flag -a is for annotated tags. The annotation include extra information such as:
    - the person who made the tag
    - the date the tag was made
    - a message for the tag
- git tag -> displays all tags that are in repository
- git log --decorate (in the Git 2.13 the flag decorate is automatically included)
- git tag --delete "label-for-the-tag" -> delete the tag
- git tag -a "label-for-the-tag" "SHA-of-the-commit-you-want-to-tag"

### Branching

- git branch -> list all branch names in the repository
- git branch "branch-name" -> create new branches
- git branch -d "branch-name" -> delete branches
- git checkout "branch-to-switch" -> switch from the current branch to other

>exercise

- git checkout -b "new-branch-and-switch-to" -> creates a new branch and switch to
- git log --oneline --graph --all -> shows all branches at once

### Merging

Combining branches together is called merging.

The two main types of merges in Git is:

- a regular merge
- a Fast-forward merge

To undo the merge, use this command:

- git reset --hard HEAD^

To combine Git branches, use:

- git merge "name-of-branch-to-merge-in"
  - when a merge happens, Git will:
    - look at the branches that it's going to merge
    - look back along the branch's history to find a single commit that both branches have in their commit history
    - combine the lines of code that were changed on the separate branches together
    - makes a commit to record the merge

#### Fast-forward Merge

When we merge, we're merging some other branch into the current branch.

I want to merge in the footer branch (from the current master branch).

Since footer is directly ahead of master, this merge is one of the easiest merges to do. It will cause a Fast-forward merge.

- git checkout master
- git merge footer

#### A Regular Merge

Where two *divergent* branches are combined.

- git merge sidebar

In case of **merge conflict** Git will try to combine as much as it can, but then it will leave special markers (>>>) than tell you where you needs to manually fix.

## Undoing Changes

### Modifying the last commit

- git commit --amend
  - edit the file(s)
  - save the file(s)
  - stage the file(s)
  - and run git commit --amend

### Reverting a commit

When you tell Git to revert a specific commit, Git takes the changes that were made in commit and does the exact opposite of them.

- git revert "SHA-of-commit-to-revert"

Reverting creates a new commit that reverts or undos a previous commit.

### Resetting commits

Resetting, on the other hand, erases commits.

>Resetting is potential dangerous.
This is one of the few commands that lets you erase commits from the repository.
To alleviate the stress, Git does keep track of everything for about 30 days before it completely erases anything.
To access this content, you'll need to use the *git reflog* command.

#### Relative Commit References

- ^ -> indicates the parent commit
- ~ -> indicates the first parent commit

examples:

The parent commit of the current commit:

- HEAD^
- HEAD~
- HEAD~1

the grandparent commit:

- HEAD^^
- HEAD~2

and so on

- git reset "reference-to-commit" -> can be used to:
  - move the HEAD and current branch pointer to the referenced commit
  - erase commits
  - move committed changes to the stating index
  - unstage committed changes
- flags:
  - --mixed -> commit on "Working Directory"
  - --soft -> commit on "Staging Index"
  - --hard -> commit on Trash

Before you do any resetting, you can create a backup branch on the most-recent commit so that you can get back to the commits if you made a mistake.
