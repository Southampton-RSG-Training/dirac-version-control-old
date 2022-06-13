---
title: "Remote Repositories"
slug: dirac-version-control-remote
teaching: 10
exercises: 0
questions:
- "How do I work with a remote repository?"
objectives:
- "Add an SSH key to a GitHub account"
- "Understand `git push` and `git pull`"
keypoints:
- "Git can easily synchronise your local repository with a remote one"
- "GitHub needs an SSH key to allow access"
---

We've learned how to use a **local repository** to store our code and view changes:

![Local Repository Commands]({{ site.url }}{{ site.baseurl }}/fig/05-remote/local.png){:width="60%"}

Now, however, we'd like to share the changes we've made to our code with others, as well as making sure we have an off-site backup in case things go wrong. We need to upload our changes in our **local repository** to a **remote repository**.

> ## Why Have an Off-site Backup?
>
> You might wonder why having an off-site backup (i.e. a copy not stored at your University) is so important. In 2005, [a fire destroyed a building at the University of Southampton](http://news.bbc.co.uk/1/hi/england/hampshire/4390048.stm). Some people's *entire PhD projects* were wiped out in the blaze. To ensure your PhD only involves a normal level of suffering, please make sure you have off-site backups of as much of your work as possible!
>
{: .callout}

## Setting Up GitHub

To do that, we'll use the **remote repository** we set up on GitHub at the start of the workshop. It's another repository, just like the **local repository** on your computer, that Git makes it easy to send and receive data from. Multiple **local repositories** can connect to the same **remote repository**, allowing you to collaborate with colleagues easily.

![Remote Repositories]({{ site.url }}{{ site.baseurl }}/fig/05-remote/remote.svg){:width="60%"}

So we're finally going to address all those *"Your branch is ahead of 'origin/main' by 3 commits"* messages we got from `git status`! However, GitHub doesn't let just anyone push to your repository - you need to prove you're the owner (or have been given access). As constantly re-entering a password is inconvenient and insecure, we'll set up an SSH key to authenticate our identity. 

We should already have one in order to access  the DIRAC cluster, so we just need to head [back to GitHub](https://github.com) and register the key there. Go to [GitHub > Settings > SSH and GPG keys > Add new](https://github.com/settings/ssh/new), and you should see this:

![Add New SSH Key]({{ site.url }}{{ site.baseurl }}/fig/05-remote/ssh.png)

We need to fill in the details. Give the key a title like "DIRAC SSH key", and then paste your **public key** into the key box - we can find it in our `~/.ssh` folder:

~~~
$ ls ~/.ssh
~~~
{: .language-bash}

~~~
config id_rsa  id_rsa.pub  known_hosts
~~~
{: .output}

You might have more SSH keys, or differently-named ones. You want to copy the contents of the `.pub` file you used for your DIRAC account into the key box, e.g.:

~~~
$ cat ~/.ssh/id_rsa.pub
~~~
{: .language-bash}

~~~
<SNIPPED FOR SECURITY>
~~~
{: .output}

**Make sure you copy the `.pub` file and not the private key!** Your private key lives on your machine and is never shared with anyone else.

Then click **Add key**, and you're sorted - you should be able to send your changes to GitHub, so let's go into how.

## Updating a Remote Repository

Now we've got our credentials set up, let's synchronise our code to the remote repository.
We do this with `git push`:

~~~
$ git push
~~~
{: .bash}

~~~
Enumerating objects: 11, done.
Counting objects: 100% (11/11), done.
Delta compression using up to 4 threads
Compressing objects: 100% (9/9), done.
Writing objects: 100% (9/9), 1.11 KiB | 1.11 MiB/s, done.
Total 9 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To github.com:smangham/climate-analysis.git
   c526612..e152071  main -> main
~~~
{: .output}

And we're done! This bit was easy as when we used `git clone` earlier, it set up our **local repository** to **track** the **remote repository**. The `main -> main` line shows we're sending our local branch called `main` to the remote repository as a branch called `main`.

> ## What *is* a branch, though?
> We're not covering them in this material, but they're very useful.
> Branches allow you to have alternate versions of the code 'branching off' from another branch (e.g. `main`). 
> You can try out new features in these branches without disrupting your `main` version of the code, then **merge them in** once you've finished.
{: .callout}

If we go back to the repository on GitHub, we can refresh the page and see our updates to the code:

![Updated remote repository]({{ site.url }}{{ site.baseurl }}/fig/05-remote/github.png)

Conveniently, the contents of `README.md` are shown on the main page, with formatting. [You can also add links, tables and more](https://docs.github.com/en/get-started/writing-on-github/getting-started-with-writing-and-formatting-on-github/basic-writing-and-formatting-syntax). Your code should always have a descriptive `README.md` file, so anyone visiting the repo can easily get started with it.

> ## How often should I push?
> Every day. The big advantage of GitHub is it's an easy way of getting an off-site backup, 
> which is vital to protect your code from computer failures or more dramatic accidents like
> wave tank leakages flooding an office or whole buildings burning down (both things that have happened at the University of Southampton!)
> ![In case of fire, git commit, git push, leave building](fig/05-remote/incaseoffire.jpg)
> [Credit: Mitch Altman, CC BY-SA 2.0](https://www.flickr.com/photos/maltman23/38138235276)
{: .callout}

## Collaborating on a Remote Repository

Now we know how to **push** our work from our local repository to a remote one, we need to know the reverse - how to **pull** updates to the code that someone else has made.

We want to invite other people to collaborate on our code, so we'll update the `README.md` with a request for potential collaborators to email us at our University email address.

```
nano README.md
cat README.md
``` {: .bash}

```
# Climate Analysis Toolkit

This is a set of python scripts designed to analyse climate datafiles.

If you're interested in collaborating, email me at s.w.mangham@soton.ac.uk.
``` {: .output}

```
git commit -am "Added collaboration info"
``` {: .bash}

```
[main 39a2c8f] Added collaboration info
 1 file changed, 2 insertions(+)
``` {: .output}

In this case, we use `git commit -am` where the `-a` means **commit all modified files we've previously used `git add` on**, and the `-m` bit means 'and here's the commit message' as usual. It's a handy shortcut. 

But **don't push to GitHub** just yet! We're going to set up a small conflict, of the kind you might see when working with a remote repository.

Now, pretending to be an existing collaborator, we'll go and add those installation instructions by editing our `README.md` file directly on GitHub. This isn't *common*, but if you want to quickly make some small changes to a single file it can be useful. We edit it as:

![GitHub edit button]({{ site.url }}{{ site.baseurl }}/fig/05-remote/edit-button.png)

And just expand it a little, making more use of GitHub's markdown formatting:

![GitHub editing Readme]({{ site.url }}{{ site.baseurl }}/fig/05-remote/edit-readme.png)

Then commit the changes directly to our `main` branch with a descriptive commit message:

![GitHub committing edit]({{ site.url }}{{ site.baseurl }}/fig/05-remote/edit-commit.png)

![Updated remote repository]({{ site.url }}{{ site.baseurl }}/fig/05-remote/github-updated.png)

### Push Conflicts

Great. Now let's go back to the terminal and try pushing our local changes to the remote repository. This is going to cause problems, however:

```
git push
``` 
{: .bash}



```
To github.com:smangham/climate-analysis.git
 ! [rejected]        main -> main (fetch first)
error: failed to push some refs to 'git@github.com:smangham/climate-analysis.git'
hint: Updates were rejected because the remote contains work that you do
hint: not have locally. This is usually caused by another repository pushing
hint: to the same ref. You may want to first integrate the remote changes
hint: (e.g., 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```
{: .output}

Git helpfully tells us that actually, there are commits present in the **remote repository** that we don't have in our **local repository**. 

### Merge Conflicts

We'll need to **pull** those commits into our local repository before we can push our own updates back!

```
git pull
``` 
{: .bash}

```
Auto-merging README.md
CONFLICT (content): Merge conflict in README.md
Automatic merge failed; fix conflicts and then commit the result.
``` 
{: .output}

We have created a conflict! Both us, and our remote collaborator, both edited `README.md`. Let's take a look at the file:


```
cat README.md
``` 
{: .bash}

```
# Climate Analysis Toolkit

This is a set of python scripts designed to analyse climate datafiles.

<<<<<<< HEAD
If you're interested in collaborating, email me at s.w.mangham@soton.ac.uk.
=======
To install a copy of the toolkit, open a terminal and run:
```
git clone git@github.com:smangham/climate-analysis.git
```

**This code is currently in development and not all features will work**
>>>>>>> 493dd81b5d5b34211ccff4b5d0daf8efb3147755
``` 
{: .output}

Git has tried to auto-merge the files, but unfortunately failed. It can handle most conflicts by itself, but if two commits edit the *exact same* part of a file it will need you to help it.

We can see the two different edits we made to the end of the `README.md` file, in a block defined by `<<<`, `===` and `>>>`. The top block is labelled `HEAD` (the changes in our latest local commit), whilst the bottom block is labelled with the commit ID of the commit we made on GitHub.

We can easily fix this using `nano`, by deleting all the markers and keeping the text we want:

```
nano README.md
cat README.md
``` 
{: .bash}

```
# Climate Analysis Toolkit

This is a set of python scripts designed to analyse climate datafiles.

If you're interested in collaborating, email me at s.w.mangham@soton.ac.uk.

To install a copy of the toolkit, open a terminal and run:
```
git clone git@github.com:smangham/climate-analysis.git
```

**This code is currently in development and not all features will work**
``` 
{: .output}

Now we've got a fixed and finished `README.md` file, we can commit our changes, and push them up to our remote repository:

```
git commit -am "Fixed merge conflict"
``` 
{: .bash}

```
[main 6f4df16] Fixed merge conflict
``` 
{: .output}

```
git push
``` 
{: .bash}

```
Enumerating objects: 10, done.
Counting objects: 100% (10/10), done.
Delta compression using up to 12 threads
Compressing objects: 100% (6/6), done.
Writing objects: 100% (6/6), 773 bytes | 773.00 KiB/s, done.
Total 6 (delta 2), reused 0 (delta 0)
remote: Resolving deltas: 100% (2/2), completed with 1 local object.
To github.com:smangham/climate-analysis
   493dd81..6f4df16  main -> main
``` 
{: .output}

Now back on GitHub we can see that our `README.md` shows the text from both commits, and our conflict is resolved:

![Resolved conflict on GitHub]({{ site.url }}{{ site.baseurl }}/fig/05-remote/conflict-resolved.png)

> ## Conflict Mitigation
> If you've got multiple different people working on a code at once,
> then the **branches** we mentioned earlier can really help reduce conflicts.
> Each collaborator can work on their own branch, and only merge them back in once everything is finished - dramatically reducing the number of conflicts!
{: .callout}

![Remote Repository Commands]({{ site.url }}{{ site.baseurl }}/fig/05-remote/remote.png){:width="60%"}

{% include links.md %}
