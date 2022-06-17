---
title: "Using a Repository"
slug: dirac-version-control-using-repository
teaching: 10
exercises: 0
questions:
- "How do I create a version control repository?"
- "Where does Git store information?"
objectives:
- "Create a repository from a template."
- "Clone and use a Git repository."
- "Describe the purpose of the `.git` directory."
keypoints:
- "`git clone` creates a local copy of a repository from a URL."
- "Git stores all of its repository data in the `.git` directory."
---

# Creating a Repository

A **repository** is a directory that is under **version control** - it can track changes to files within it. Git also makes it easy to sync up a **local repository** on your computer (or DIRAC server) with a **remote repository** on the internet. 

## Using a Template

We're going to work with some pre-existing template code, that's already stored in a repository. The first thing we need to do is create our own copy of that template, which we can do on [GitHub](https://github.com). First, visit [GitHub](https://github.com), and make sure you've signed in to your account.

Once you're signed in, [go to our template repository](https://github.com/Southampton-RSG-Training/dirac-version-control-template) and select **Use this template**:

![Use Template]({{ site.url }}{{ site.baseurl }}/fig/02-using-repository/template-copy.png)

We should get prompted to give details for what we'd like our copy of the template to be called. As this demo code is for analysing climate data, we'll name our copy of it `climate-analysis`. We also want it to be public, so anyone can see and copy our code:

![Repository Details]({{ site.url }}{{ site.baseurl }}/fig/02-using-repository/template-details.png)

> ## Public or Private?
> GitHub will allow you to create private repositories, so only people you specify can access the code, but it's always best to keep your code public - especially if you're going to use it in a paper! Code that generates or analyses data is a fundamental part of your method, and if you don't include your full method in papers your work can't be reproduced, and reproducibility is key to the scientific process. **Always** keep your repositories public unless you've got a strong reason, like embargoes imposed by industrial partners.
>
> A major advantage of this is if you leave academia, or you switch institution and forget to update the email on your GitHub account before you lose your old one, your work won't be lost forever!
{: .callout}

After a brief wait, GitHub will have created a **remote repository** - a copy of the files and their history stored on GitHub's servers. We want to copy that to our local machine, which we do using `git clone`. Click on the **code** button, and you should have a choice of ways to copy the code. Select **SSH**, then click the copy button to copy the repository's URL:

![Copy Repository URL]({{ site.url }}{{ site.baseurl }}/fig/02-using-repository/repository-url.png)

Now we'll download a copy of the repository to our server. First, though, just as we needed an SSH key to connect from our computer to the DIRAC server, we need an SSH key to connect from the DIRAC server to GitHub.

{: .caution}
> ## SSH vs HTTPS
> **Make sure you select SSH!** Whilst Git supports both **HTTPS** and **SSH**, **GitHub** will only let you *download* with **HTTPS**, as it's less secure. You can check if you accidentally selected **HTTPS** with:
>
>{: .bash}
>~~~
>git remote -v
>~~~
>
>{: .output}
>~~~
>$ origin	git@github.com:yourname/climate-analysis (fetch)
>$ origin	git@github.com:yourname/climate-analysis (push)
>~~~
>
> If you see **HTTPS**, you can fix this with:
>
>{: .bash} 
>~~~
>$ git remote set-url origin git@github.com:yourname/climate-analysis
>~~~
>




## Setting up an SSH Key

We've already set up an SSH key in order to access the DIRAC cluster, but that's on our *local* machine - we also need one on the DIRAC server we've connected to. We can create one on the command line as before - just go with the defaults for every option:

~~~
$ ssh-keygen -t rsa 
~~~
{: .bash}

~~~
Generating public/private rsa key pair.
Enter file in which to save the key (/cosma/home/ds007/dc-mang1/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /cosma/home/ds007/dc-mang1/.ssh/id_rsa.
Your public key has been saved in /cosma/home/ds007/dc-mang1/.ssh/id_rsa.pub.
The key fingerprint is:
SHA256:ltRWwH/GJxLDlndQLmtR4LbprhhVjjFbc5ZYoeYiFc8 dc-mang1@login6a.pri.cosma7.alces.network
The key's randomart image is:
+---[RSA 2048]----+
|         ..+..o*+|
|         ...X.=oo|
|        . o+oEOo+|
|       . o. =X=X.|
|        S. .+=*o |
|       .  ...o   |
|          .   .  |
|           o .   |
|          . ...  |
+----[SHA256]-----+
~~~
{: .output}

Now we've generated a key, we can head [back to GitHub](https://github.com) and register the key there. Go to [GitHub > Settings > SSH and GPG keys > Add new](https://github.com/settings/ssh/new), and you should see this:

![Add New SSH Key]({{ site.url }}{{ site.baseurl }}/fig/02-using-repository/ssh.png)

We need to fill in the details. Give the key a title like "DIRAC SSH key", and then paste your **public key** into the key box - we can find it in our `~/.ssh` folder:

~~~
$ ls ~/.ssh
~~~
{: .bash}

~~~
id_rsa  id_rsa.pub  known_hosts
~~~
{: .output}

You want to copy the contents of the `.pub` file, which you can display with:

~~~
$ cat ~/.ssh/id_rsa.pub
~~~
{: .language-bash}

~~~
ssh-rsa <SNIPPED FOR SECURITY> dc-mang1@login6a.pri.cosma7.alces.network
~~~
{: .output}

**Make sure you copy the `.pub` file and not the private key!** Your private key lives on your machine and is never shared with anyone else. Then click **Add key**.


## Cloning the Repository

We can finally clone the repository to the HPC server:

~~~
$ git clone git@github.com:yourname/climate-analysis.git
~~~
{: .language-bash}

~~~
Cloning into 'climate-analysis'...
The authenticity of host 'github.com (140.82.121.4)' can't be established.
ECDSA key fingerprint is SHA256:p2QAMXNIC1TJYWeIOttrVc98/R1BUFWu3/LiyKgUfQM.
ECDSA key fingerprint is MD5:7b:99:81:1e:4c:91:a5:0d:5a:2e:2e:80:13:3f:24:ca.
Are you sure you want to continue connecting (yes/no)? yes
~~~
{: .output}

Then, when you're prompted, continue the connection with `yes` and it will finish downloading:

~~~
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (4/4), done.
~~~
{: .output}

Now, if we use `ls` to list the contents of the directory, we should see we have a new directory, called `climate-analysis`, that's a **local repository** containing the code from our **remote repository**. This is linked up automatically - making it easy for us to download updates to the remote repository, or to send our changes back up to it.

> ## Creating Repositories Locally
> 
> You don't have to create your repositories on GitHub first!
> If you want, you can create a repository locally by entering any directory and using `git init`. From there, you can use the other commands we introduce in this section. 
> You can connect a local repository to an empty remote one using `git remote add origin URL_OF_REMOTE`.
{: .callout}


# Exploring a Repository

Now, let's **change to our code directory** and look at the files we just downloaded.

~~~
$ cd ~/climate-analysis
$ ls
~~~
{: .language-bash}

~~~
climate_analysis.py  temp_conversion.py
~~~
{: .output}

These are some Python files for analysing climate data-
you'll recognise them if you've done some of our earlier lessons.
Don't worry, you don't need to know Python to follow along.

You'll notice that even though this directory is a **version control repository**, nothing actually looks special about it. But, if we add the `-a` flag to show everything,
we can see that there's a hidden directory called `.git`:

~~~
$ ls -a
~~~
{: .language-bash}

~~~
.  ..  climate_analysis.py  .git  temp_conversion.py
~~~
{: .output}

Git stores information about the project in here.
If we ever delete it, we will lose the project's history.

### Check Status

We can check that everything is set up correctly
by asking Git to tell us the status of our project with the **status** command:

~~~
$ git status
~~~
{: .language-bash}

~~~
# On branch main
nothing to commit, working tree clean
~~~
{: .output}

A **branch** is an independent line of development.  We have only one, and the default name is **main**.

Our **local repository** is connected to a **remote repository** (called **origin** by default), and is currently up-to-date; we haven't made any changes to the code yet.

Git works on **commits** - snapshots of the current state of the repository. *"nothing to commit, working tree clean"* means that the directory currently looks exactly the same as the last snapshot we took of it, with no changes or edits.

> ## Branch names
> 
> In this workshop, we have a **default branch** called **main**. In older versions of Git,
> if you create a new repository on the command line, it'll have a default branch called **master**, and a lot of examples online will show **master** instead of **main**. Don't worry - branches work the same, regardless of what they're called!
{: .callout}

{% include links.md %}
