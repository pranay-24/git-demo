## Objective ##
Learn how to use branches to manage development

### Creating a branch ###
By default, git gives you one 'branch' of development, the **main ** branch. For small projects that's OK, but what if you want to try something different that might take a bit longer to work on and you don't want to disturb your working versions?

Specifically, let's say you have a 'production' version which you maintain on the **main ** branch, and you want a separate 'development' version to play with. You can do that with a branch.

You should be on the main  branch now, with a clean repository after the previous exercises. Let's check that:

```
> git status
On branch main 
nothing to commit, working directory clean
```

OK, good. Now let's make a **dev** branch:

```
> git branch dev
> git status
On branch main 
nothing to commit, working directory clean
```

Notice that git created the branch, but doesn't switch to that branch by default. You're still on the **main ** branch. So how do we know it really created a branch? Use **git branch** without any options:

```
> git branch 
  dev
* main 
```

The asterisk shows which branch you are on. Switch to the dev branch with the **git checkout** command, and check you're really there:

```
> git checkout dev
Switched to branch 'dev'
> git status
On branch dev
nothing to commit, working directory clean
> git branch
* dev
  main 
```

Both **git status** and **git branch** tell you you're on the **dev** branch now. Make some changes and commit them.

```
> echo "this is on my dev branch" > dev.txt
> git add dev.txt
> git commit -m 'add something on the dev branch'
[dev be51ba2] add something on the dev branch
 1 file changed, 1 insertion(+)
 create mode 100644 dev.txt
```

If you switch back to the **main ** branch, you won't see your **dev.txt**, which is as it should be!

```
> git checkout main 
Switched to branch 'main '
> ls
goodbye.txt hello.txt secret.txt
```

### Merging two branches, deleting branches ###

So, let's say we're happy with the changes in the **dev** branch, how do we get them back into the **main ** branch? Use the **git merge** command!

```
> git merge dev
Updating 744c0bb..be51ba2
Fast-forward
 dev.txt | 1 +
 1 file changed, 1 insertion(+)
 create mode 100644 dev.txt
> ls
dev.txt   goodbye.txt hello.txt secret.txt
```

You can continue developing on the **dev** branch, and **git merge** back into the **main ** branch (or any other branch, for that matter) as often as you like. If, on the other hand, you're finished with it, you can also delete it.

```
> git branch --delete dev
Deleted branch dev (was be51ba2).
> git branch
* main 
```

And there it is, gone!

### Dealing with conflicts ###
If you make conflicting changes on two branches of a repository, and then try to merge them, you end up in exactly the same situation as you did in the previous exercise, when we made conflicting changes in two copies of the same repository. Git will notify you of the conflict and you will have to fix it before moving on. It's worth practicing this a few times yourself, so let's create a file on the **main ** branch, then switch to a **dev** branch.

```
> echo "I am the main  branch" > branch-name.txt
> git add branch-name.txt 
> git commit -m 'add branch-name.txt'
[main  630ac14] add branch-name.txt
 1 file changed, 1 insertion(+)
 create mode 100644 branch-name.txt
> git checkout -b dev # shorthand way to create a branch and check it out at the same time
Switched to a new branch 'dev'
> git branch
* dev
  main 
```

Now change **branch-name.txt** on the **dev** branch, and commit it.

```
> cat branch-name.txt | sed -e 's%main %dev%' | tee branch-name.txt
I am the dev branch
> git add branch-name.txt
> git commit -m 'change branch-name.txt'
[dev bd25209] change branch-name.txt
 1 file changed, 1 insertion(+), 1 deletion(-)
```

Now go back to the **main ** branch and make a conflicting change to the same file, then try to merge the **dev** branch back in.

```
> git checkout main 
Switched to branch 'main '
> cat branch-name.txt | sed -e 's%I am%This is%' | tee branch-name.txt
This is the main  branch
> git add branch-name.txt 
> git commit -m 'change branch-name.txt in a way that conflicts with the dev branch'
[main  bbc2fc3] change branch-name.txt in a way that conflicts with the dev branch
 1 file changed, 1 insertion(+), 1 deletion(-)

> git merge dev
Auto-merging branch-name.txt
CONFLICT (content): Merge conflict in branch-name.txt
Automatic merge failed; fix conflicts and then commit the result.
> cat branch-name.txt 
<<<<<<< HEAD
This is the main  branch
=======
I am the dev branch
>>>>>>> dev
```

Fix the conflict by imposing our recent change

```
> cat branch-name.txt | grep This | tee branch-name.txt
This is the main  branch
> git add branch-name.txt 
> git commit -m 'resolve conflict'
[main  222724f] resolve conflict
```

Now we have to import that update back to the **dev** branch, because the **dev** branch is now behind the **main ** branch:

```
> git checkout dev
Switched to branch 'dev'
> git merge main 
Updating bd25209..222724f
Fast-forward
 branch-name.txt | 2 +-
 1 file changed, 1 insertion(+), 1 deletion(-)
```

### Conclusion ###
Branches give you a way to segregate development cycles, so that the main thread of development can remain stable while changes are tested in one or more branches.

However, you have to be aware of the potential for conflicts or divergence, and manage them when they arise. Branches are a powerful feature, but need to be used sensibly.

### Best Practices ###

- use branches for isolated new feature development, or for working on bug-fixes, without disturbing the main flow of stable code
- avoid long-lived branches, because of the risk of divergence between your banch and others
- you can minimize the risk of divergence by merging the **main ** branch into your development branch regularly, providing that doesn't conflict with the changes you are making, of course
- you _can_ minimize the risk of divergence further by using _more_ branches, so the **main ** branch development is 'quantized'. Then, as you merge each branch back into the **main **, you can _also_ merge whichever of those branches you want into your development branch (this is how some projects manage bugfixes)
- on the other hand, having too many branches can also be a nightmare to manage
- for all the above reasons, delete branches you no longer actively need. You can always re-use the branch name later, so you lose nothing

The bottom line is this: if you're not tidy and organized _without_ branches, branches won't save you. If you _are_ organized, they'll be very useful

