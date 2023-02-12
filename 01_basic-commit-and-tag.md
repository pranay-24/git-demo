## Objective: ##

- learn how to use git standalone, with a local repository.
- learn how to tag code, and switch between tags

### Create a repository, add code to it ###
1) create a new repository under your $HOME, 'cd' into it

```
> git init my-repository
Initialized empty Git repository in VS code - if using terminal in VS code
> cd my-repository
> 
```

2) create a file, see what git thinks of it:

```
> echo "Hello world" > hello.txt
> git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

  hello.txt

nothing added to commit but untracked files present (use "git add" to track)
```

So, git knows you've created a file, but it's listed under "Untracked files", because you haven't told git to do anything with it.

3) add the file to the staging area, check the status again
  
```
> git add hello.txt
> git status
On branch main

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

  new file:   hello.txt

```

Now git knows about the file, but hasn't actually put it in the repository yet, it's in the 'staging area'. That's what "Changes to be committed" means.

4) Now commit the changes to the repository, with **git commit**

```
> git commit
```

git will put you into an editor window (whatever your $EDITOR points at). Type in a message of some sort, save the file and exit the editor. You'll see something like this:


```
[master (root-commit) 2568a1e] this is my first commit
 1 file changed, 1 insertion(+)
 create mode 100644 hello.txt
```

and *git status* will now show you that the working directory is _clean_, with _nothing to commit_

```
> git status
On branch main
nothing to commit, working tree clean
```

### Tag code, switch between tags ###
5) Tag this version, so you can recover it later if you need to. Then list all the tags you have so far

```
> git tag V1.0
> git tag
V1.0
```

6) Make some changes to your file, commit them, add another tag. Then make _another_ change, add that, but _don't_ tag it
```
> echo Hello $USER >> hello.txt 
> git add hello.txt 
> git commit -m 'updated my file'
[master a20b708] updated my file
 1 file changed, 1 insertion(+)
> git tag V2.0
> git tag
V1.0
V2.0
> echo Hello anybody >> hello.txt
> git add hello.txt 
> git commit -m 'updated my file again'
[master ed83367] updated my file again
 1 file changed, 1 insertion(+)
```

7) switch to first version, using the tag

```
> git checkout V1.0
Note: checking out 'V1.0'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 2568a1e... initial commit
> cat hello.txt 
Hello world
```

Things to note:

 - you now have the original version of the file back, which you tagged as *V1.0*
 - this is not enough to develop from, because it's only a *tag*, not a *branch*. That's what the message about 'detached HEAD' means. More later...

8) switch back to latest version, the 'HEAD' version
```
> git checkout main
> cat hello.txt
Hello world
Hello wildish
Hello anybody
```

9) What if you forgot to tag a version? You can still go to it, using the 'commit id':

- update the file once again
- use **git log** to look at the log messages
- use **git checkout <commit-id>** to check out the precise version you want

```
> echo "Hello everybody" >> hello.txt 
> git add hello.txt 
> git commit -m 'updated my file yet again'
[master 5553884] updated my file yet again
 1 file changed, 1 insertion(+)
> git log --pretty="%H %s"
5553884952b86a86e6521773af09c094876938b6 updated my file yet again
ed83367d1e1b05def749bab1c693fe77ba6474b9 updated my file again
a20b708fa9e9daf6cb407f8208c8086353787aa8 updated my file
2568a1e7beb21650aa5fbad07fa0139258e7efd1 my first commit

> cat hello.txt 
Hello world
Hello wildish
Hello anybody
Hello everybody
> git checkout ed83367d1e1b05def749bab1c693fe77ba6474b9
Note: checking out 'ed83367d1e1b05def749bab1c693fe77ba6474b9'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at ed83367... updated my file yet again
> cat hello.txt 
Hello world
Hello wildish
Hello anybody
```

Before going on to the next lesson, switch back to the 'main' branch

```
> git checkout main
Previous HEAD position was ed83367... updated my file again
Switched to branch 'main'
```

## Conclusion ##
You can now develop code and go back to previous versions should you want to. If you've tagged the code, it's easy, just use the tag-name. If you haven't tagged it, you'll need to dig through the commit-history, but you can still do it.

## Best practices ##
- use a git repository for _every_ development project you do, no matter how small
- commit frequently, so you have regular backups
- use meaningful commit messages
- tag versions of interest
- use meaningful tag names
