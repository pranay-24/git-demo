## Objective ##

- learn how to undo changes that you don't want to keep
- learn how to avoid making mistakes in the first place

### Unstaging a modified file ###
Assume you've modified a number of files and want to commit some of them, but not all of them, at least not yet. However, you accidentally add one of the files you don't want to commit to the staging area, so it's on the list to be committed next. What do you do?

First let's add a 'secret' file to the staging area, by mistake. Then we'll remove it while leaving the rest of the files to be committed.

```
> echo "Goodbye cruel world" > goodbye.txt
> echo "This shouldn't go into the git repository" > secret.txt
> git add . # shorthand for 'add everything'
> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file:   goodbye.txt
  new file:   secret.txt
```

OK, so our **secret.txt** will be committed. Let's cancel that.

```
> git reset HEAD secret.txt
> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file:   goodbye.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

  secret.txt

```
**secret.txt** is now untracked, meaning we can commit the other changes and it won't go into the repository with them.

### Preventing files from being committed by mistake ###

OK, so we can commit safely now. But can we prevent that file from being committed in the first place? Yes! Files listed in **.gitignore** will be ignored by git.

```
> echo secret.txt > .gitignore
> git add .gitignore
> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  new file:   .gitignore
  new file:   goodbye.txt
```

Notice that **secret.txt** isn't mentioned anymore, which is how we like it! The **.gitignore** file can have wildcards, such as __*.o__, or __*.pyc__, to prevent any file matching those patterns from being committed. One pattern per line, as many patterns as you like. You can also have a **.gitignore** file per directory, for fine-grained control over what you commit.

Go ahead and commit these changes.

```git commit -m 'commit goodbye.txt, prevent secrets.txt from ever being committed'```

### Correcting a commit ###

We made a mistake in that last commit message, referring to **secrets.txt**, instead of **secret.txt**. We can correct that.

```
> git commit --amend -m 'commit goodbye.txt, prevent secret.txt from ever being committed'
[master 4d15910] commit goodbye.txt, prevent secret.txt from ever being committed
 Date: Mon Nov 21 16:08:07 2016 -0800
 2 files changed, 2 insertions(+)
 create mode 100644 .gitignore
 create mode 100644 goodbye.txt
```

Note that as well as correcting a commit message we could also **git add** any files that were overlooked, then use **git commit --amend** to add them to the last commit, rather than having to create a new commit.

### Abandoning modifications to a file ###
What if we change a file, but decide that we want to revert to the last committed version, and abandon the changes we made? We can do that, but bear in mind these changes are lost forever, there is no record of them.

```
> echo "Add some junk to a file" >> hello.txt
> git status
```

If you look at the output of the **git status** above, you'll see it tells you what you need to do to abandon the changes. So let's do it!

```
> git checkout -- hello.txt
> git status # Now we're clean again
> cat hello.txt
Hello world
Hello wildish
Hello anybody
Hello everybody
```

Notice that the 'junk' line we added is no longer there.

### Renaming files, removing files ###
Suppose you commit a file, then realize you want to give it a different name. You could **mv** it, but will git keep track if you do?

```
> ls > list.txt
> git add list.txt 
> git commit -m 'added a MANIFEST'
[master b3a0386] added a MANIFEST
 1 file changed, 7 insertions(+)
 create mode 100644 list.txt
> mv list.txt MANIFEST.txt
> git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

  deleted:    list.txt

Untracked files:
  (use "git add <file>..." to include in what will be committed)

  MANIFEST.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

Oops, git's confused now. It thinks you've deleted a file it knew about, and sees there's a file that you haven't told it to track yet. The correct way to do it is to tell **git** to move the file. So let's put it back, then do that:

```
> mv MANIFEST.txt list.txt
> git status
On branch master
nothing to commit, working directory clean
> git mv list.txt MANIFEST.txt
> git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

  renamed:    list.txt -> MANIFEST.txt
```

Much better! Now we can commit, and git will do the right thing.

```git commit -m 'added a MANIFEST'```

Maybe we decide we don't really want that MANIFEST file after all, so let's delete it from the repository:

```
> git rm MANIFEST.txt 
rm 'MANIFEST.txt'
> git commit -m 'removed the MANIFEST file'
[master 744c0bb] removed the MANIFEST file
 1 file changed, 4 deletions(-)
 delete mode 100644 MANIFEST.txt
> ls
goodbye.txt hello.txt secret.txt
```
**Nota Bene:** The file is gone from the repository, but still exists in the git history. So this is not a recipe for correcting a mistake where you commit a password file, or something like that.

### Undoing a commit ###
What if you commit something that you really want to keep out of the repository, so you want to erase all trace of that commit. Can we do that?

Basically, yes, but it's not trivial, and we won't cover that here. Make sure you don't commit stuff that shouldn't be there in the first place, and don't ever attempt to completely undo a commit.

### Deleting a tag, or moving it ###

Let's say your latest version is much better than the V2.0 you were planning to release, so you want to make this the V2.0 instead. You'll want to move the tag:

```
> git tag -f V2.0
Updated tag 'V2.0' (was a20b708)
```

OK, but now you find a fatal bug that's going to take time to fix. Rather than leave tag V2.0 in the system, you want to delete it until you've corrected the bug:

```
> git tag -d V2.0
Deleted tag 'V2.0' (was 4d15910)
git tag
V1.0
```

**N.B.** Tag names are free, think twice before moving or deleting a tag that may be in use somewhere else

## Conclusion ##
Some mistakes can be undone, but there's nothing like thinking it through first.

## Best Practices ##

- think about files that you don't want to commit, and add them to the **.gitignore** early.
- don't move or delete tags without good reason, someone else may be using them. Add a new tag instead.
- think carefully about what you might lose if you attempt to undo something. Is it better to go ahead, and then 'manually' revert the change? At least that way you preserve history.
- if you really have committed something that shouldn't have gone into the repository, you need more careful surgery than we can cover here to correct that.