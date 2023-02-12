## Objective ##
 - learn how to create and use a remote repository

### Create a remote repository ###
So far we've worked with a local repository, but you'd like to have your repository backed up to a remote server, for safety and for access from home/work. You can use servers like github.com, gitlab.com, Github.org, or many others. We're using Github.org, of course.

### Create an empty remote repository ###
Login to your Github account. From your dashboard, click on Repositories tab, then click **New** button.

Give it a name (e.g. **git-demo**), and click **Create Repository**.

You then have a number of options, for now, take the **I have an existing project** link. Follow the instructions, you should see something like this:

```
> git remote add origin ssh://git@Github.org/TWildish/git-demo.git
> git push -u origin main
Counting objects: 16, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (16/16), 1.29 KiB | 0 bytes/s, done.
Total 16 (delta 0), reused 0 (delta 0)
To ssh://git@Github.org/TWildish/git-demo.git
 * [new branch]      main -> main
Branch main set up to track remote branch main from origin.
```

If you get SSH problems then you probably haven't set up your SSH keys in Github yet. Click on your avatar (top-right of the page), then **Github settings**, then **SSH keys** (from the menu on the left-hand side), then **Add key** and do what it says. Then try again.

If it all worked, go to the **Source** link on the left of your repository view, and _voila_, there is your code, with the full git history!

Except, not quite! Your tags are not pushed by default, you have to push them explicitly.

```
> git push --tags
Total 0 (delta 0), reused 0 (delta 0)
To ssh://git@Github.org/TWildish/git-demo.git
 * [new tag]         V1.0 -> V1.0
```

So, you now have a local repository (in the filesystem) and a remote repository, on Github.org. If you lose your local copy, you can get your code back from the remote repository. Congratulations, you have a backup! To get your code back from the remote, you use the **git clone** command. Put your own username in the URI for the clone command.

```
> cd ..
> rm -rf my-repository
> git clone ssh://git@Github.org/<your-Github-username>/git-demo.git
Cloning into 'git-demo'...
remote: Counting objects: 22, done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 22 (delta 2), reused 0 (delta 0)
Receiving objects: 100% (22/22), done.
Resolving deltas: 100% (2/2), done.
Checking connectivity... done.
> cd git-demo
> ls
file1.txt file2.txt goodbye.txt hello.txt
```

So there you are with all your files. Note that by default, git will clone the repository to a directory with the same name as the repository. So although we were working in the **my-repository** directory before, we're now in the **git-demo** directory.

### Developing with a remote repository ###

When you commit changes to your local repository, as you have done before, they are _not_ automatically sent to the remote repository. Changes are only sent to the remote when you explicitly **push** them there. E.g.

```
echo 123 > file1.txt
echo 456 > file2.txt
git add file1.txt
git commit -m 'add a new file' file1.txt
[...]
git add file2.txt
git commit -m 'add another new file' file2.txt
```

Go to your web-browser and look at the repository, you won't see **file1.txt** or **file2.txt** there, yet. Now

```
git push
```

Go back to your web-browser and refresh (reload) the **Source** page. You'll see both new files. If you click on the **Commits** menu option (on the left), you'll see you also have the commit history.

This means you can commit as often as you like to the local repository, working offline, and simply push it all to the remote repository when you have network access again. Handy for working on a plane!

### Working in two places in parallel ###

Let's say that some of the time you're working on a login node, other times you're working on your laptop, and you want to keep the code in sync between them. How do you do that?

Let's start by making a second clone of the repository:

```
> cd ..
> git clone ssh://git@Github.org/<your-Github-username>/git-demo.git demo2
Cloning into 'demo2'...
remote: Counting objects: 22, done.
remote: Compressing objects: 100% (11/11), done.
remote: Total 22 (delta 2), reused 0 (delta 0)
Receiving objects: 100% (22/22), done.
Resolving deltas: 100% (2/2), done.
Checking connectivity... done.
```

You now have two clones, one in **git-demo**, the other in **demo2**, and they're both identical - as clones should be. Let's change one, and propagate the changes to the other.

```
> cd git-demo
> echo "Created in git-demo" > file-git-demo.txt
> git add file-git-demo.txt
> git commit -m 'add another file'
[...]
> git push
```

now go to the other clone (or open a second terminal window there) and **pull** the changes

```
> cd ../demo2
> git pull
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From ssh://Github.org/TWildish/git-demo
   66d822b..1b00d66  main     -> origin/main
Updating 66d822b..1b00d66
Fast-forward
 file-git-demo.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 file-git-demo.txt
```

Bingo, you have your new file, so you can keep both clones up to date.

### Syncronizing clones ###
What if you change the same file in both repositories, creating a conflict? Let's do that. In the first clone's directory, do the following:

```
> echo `pwd` > current-dir.txt
> git add current-dir.txt
> git commit -m 'add current-dir.txt'
[..]
> git push
```

Now go to the other clone's directory and try to do the same:
```
> cd ../demo2/
> echo `pwd` > current-dir.txt
> git add current-dir.txt
> git commit -m 'add current-dir.txt'
[main d36d777] add current-dir.txt
 1 file changed, 1 insertion(+)
 create mode 100644 current-dir.txt
> git push
To ssh://git@Github.org/TWildish/git-demo.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'ssh://git@Github.org/TWildish/git-demo.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

So this push was rejected, rightly, because we already pushed changes from the other clone. We need to do as suggested, and try a **git pull** to get those changes locally, so we can resolve any conflict.

```
> git pull
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 1), reused 0 (delta 0)
Unpacking objects: 100% (3/3), done.
From ssh://Github.org/TWildish/git-demo
   1b00d66..1397c5d  main     -> origin/main
Auto-merging current-dir.txt
CONFLICT (add/add): Merge conflict in current-dir.txt
Automatic merge failed; fix conflicts and then commit the result.
```

Git finds there is a real conflict (the penultimate line), which tells us which file to fix. If you look there, you'll see the conflict clearly marked:

```
> cat current-dir.txt 
<<<<<<< HEAD
/Users/wildish/demo2
=======
/Users/wildish/git-demo
>>>>>>> 1397c5d43b3749d10d38ddf75d3c8135534cf049
```

You can look up the details of what that means, but intuitively it's clear enough, there are two lines written to the same place in the same file. You need to fix this before you can do anything else, git won't let you proceed otherwise. So, edit the file and clean it up somehow, then commit it, and now you'll be able to push it to the remote repository.

```
> vi current-dir.txt 
> cat current-dir.txt 
/Users/wildish/demo2
/Users/wildish/git-demo
> git add current-dir.txt 
> git commit -m 'fix merge conflict'
[main 559cde9] fix merge conflict
> git push
Counting objects: 6, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (4/4), done.
Writing objects: 100% (6/6), 575 bytes | 0 bytes/s, done.
Total 6 (delta 2), reused 0 (delta 0)
To ssh://git@Github.org/TWildish/git-demo.git
   1397c5d..559cde9  main -> main
```

Now that the conflict has been resolved, you can update the first clone too, by pulling the changes that you committed in the second clone:

```
> cd ../git-demo/
> git pull
remote: Counting objects: 6, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 6 (delta 2), reused 0 (delta 0)
Unpacking objects: 100% (6/6), done.
From ssh://Github.org/TWildish/git-demo
   1397c5d..559cde9  main     -> origin/main
Updating 1397c5d..559cde9
Fast-forward
 current-dir.txt | 1 +
 1 file changed, 1 insertion(+)
```

This time git didn't have a problem, because it can figure out for itself how to merge the differences between the remote and local repositories.

## Conclusion ##
Using a remote repository brings a few advantages:

- you have a backup, in case your laptop/development machine dies
- you can have multiple copies of your code, on multiple machines, and can co-ordinate them safely

## Best practices ##
- don't use one repository for everything, like a kitchen sink. That becomes unwieldly
- instead, create a separate remote repository for every development project you work on
- as well as committing early and often, remember to **push** often, so your changes get uploaded to the remote repository
- if you have multiple copies of a repository checked out - e.g. one copy on your laptop, one on Genepool - make a habit of doing a **git pull** when you switch to working in one place or the other, to keep things in sync
- in short, sync regularly, everywhere, to keep any conflicts manageable