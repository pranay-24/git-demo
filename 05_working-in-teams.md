## Objective ##
Learn how to work in teams, where many people may be contributing to development

### Sharing a repository for development ###
If you only want to download code developed by someone else, you need only **clone** it, as we did in exercise 3.

If, on the other hand, you work in a team, chances are someone else owns the repository that you want to write code for. You can be given **read-only** access to a repository, or **read-write** access.

If you have read-write access, you can clone the repository, develop code, commit it and push as we did in exercise 3. You'll have to manage any conflicts that happen if multiple developers are changing the same file, so you'll need some co-ordination.

If you only have read-only access, or if you want to use git itself to help with the co-ordination between developers, you can use the **fork/pull** approach. This is a bit like branching, in that it gives you a place for development that then gets merged back into the main repository, but it's under more controlled conditions than simply allowing everyone to commit and push their changes.

### 'Forking' a remote repository ###
**Forking** a repository means making a personal copy of someone else's repository, one which you can modify even if you can't commit to the 'parent' repository. You make a fork through the web interface, so fire up your browsers.

Go to github, on the top there's a **Fork** button, click it and you will be directed to "Create a new fork" page. At the bottom of the page, click "Create fork" button and now you own version of that repository.

'cd' into it:

```
> git clone URL
Cloning into 'git-demo-fork'...
remote: Counting objects: 40, done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 40 (delta 9), reused 0 (delta 0)
Receiving objects: 100% (40/40), done.
Resolving deltas: 100% (9/9), done.
Checking connectivity... done.
> cd git-demo-fork
```

Now you can make changes here, commit them, and push them to your fork. They don't affect my original repository, only your fork of it.

```
> echo Hello from $USER > people.txt
> git add people.txt
> git commit -m 'say Hello'
[...]
> git push
[...]
```

There's nothing particularly special about my repository versus yours, except that yours is a fork of mine. For example, someone else can come along and fork your repository, instead of mine, and start development from there.

### Making a 'pull request' ###
OK, so your changes are in your repository, but how do we get them across to mine? Since many of you are modifying the **people.txt** file I don't want to let you all commit to it directly, so you have to go back to the web interface and make a 'pull request' (link on the left of the page).

A pull request lets you add a comment, then you make the request. I (the owner of the 'parent' repository) can see that you've made the request, and it's up to me to accept it and merge it back with the parent repository. If there are any conflicts, I can either resolve them, or refuse the pull request, and ask you to modify your changes.

This gives a degree of oversight, allowing me to control what gets back into my main branch from a large team of developers (you!) who are all making changes to the same files.

For bonus points, pair up with someone else and repeat this exercise by forking each others' repositories. That will give you practise at accepting and merging pull requests, as well as making them.

Note that forks and pull requests don't have to be made against the **main** branch, they can be against any branch in the repository. So you can work as a distributed team on a **dev** branch of a repository, for example.

### Conclusion ###
Forking a repository and making pull requests is a safe way of providing human oversight to a large development team. Consider using it instead of granting read-write access to too many people. You can grant read-only access to new people (students, new developers etc) and review their pull requests carefully until they've proven themselves, then give them read-write access.

Having multiple developers working on a repository needs careful handling. If several people tend to work on the same file(s) you will get lots of conflicts. Look for ways to re-organize your files to avoid having people tread on each others' work. E.g. you can organize your files as libraries or modules, or split out as separate sub-projects in their own repository.

### Best Practices ###

- grant read-write access only to trusted 'core' developers, grant read-only access to everyone else
- using modules or libraries, (i.e. having highly granular file organization) makes conflicts and pull requests much easier to handle