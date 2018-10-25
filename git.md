Git
===

Index
-----

1. [Install and Setup](#s1)
2. [HowTos](#s2)
   1. [Combine Multiple Git Repos](#s2)
3. [Other Useful Resources](#s3)
4. [Glossary](#s4)

<a name="s1">1: Install and Setup</a>
-------------------------------------

### 1: Install git ###
Update the apt package cache

```console
sudo apt-get update
```

Install git

```console
sudo apt-get install git
```

### 2: Configure global settings ###
username:

```console
git config --global user.name "<name>"
```

user.email:

```console
git config --global user.email <email_address>
```

### 3: Configure system settings ###
Append the following to `~/.bashrc`:

```bash
# set $VISUAL and $EDITOR (including for child shells)
export EDITOR='vim'
export VISUAL='vim'
```

Reload bashrc:

```console
. ~/.bashrc
```

Confirm that bashrc gets loaded for [login][link01] sessions by checking .profile and .bash_profile:

```console
less ~/.bash_profile
less ~/.profile
```

One of them (at least) should load .bashrc.


### 4: Link SSH key with Github ###
a) Create a key (if there isn't one already at `~/.ssh/id_rsa.pub`)

```console
ssh-keygen -t rsa -b 4096 -C "<email_address>"
```

b) copy the contents of the created public key to the clipboard

c) go to github and select add a new SSH key, pasting the contents of the ssh key into the field


### 5: Clone a repo ###
```console
git clone git@github.com:<user_id>/<repo>.git
```


<a name="s2">2: HowTo 1: Combining Two Git Repos</a>
----------------------------------------------------

1. [Create a new empty repository New.](#s2.1)
2. [Make an initial commit because we need one before we do a merge.](#s2.2)
3. [Create a new branch for old repo A's files](#s2.3)
4. [Create a remote association to old repo A](#s2.4)
5. [Pull the remote's files](#s2.5)
6. Move all the remote's files into a subdirectory
7. Checkout master
8. Merge the branch back into master
9. Repeat steps 3–8 for all remaining old repos

### <a name="s2.1">Create a new empty repository New</a> ###

```console
mkdir new
cd new
git init
```

### <a name="s2.2">Make an Initial Commit</a> ###
This creates a new master branch.
```console
echo New > README.md
git add -A
git commit -m "Initial commit"
```

### <a name="s2.3">Create a new branch for old repo A's files</a> ###
For clarity, branches are in snake case, remote names in Pascal case

```console
git checkout -b repo_a
```

### <a name="s2.4">Create a remote association to old repo A</a> ###
`RepoA` is the name we are using to associate the remote repo's url inside this repo.

```console
git remote add -f RepoA https://www.git.files/path/to/repo
```

### <a name="s2.5">Pull the remote's files</a> ###
Will probably need to explicitly allow unrelated histories

```console
git pull RepoA master --allow-unrelated-histories
```

Then will probably need to do a merge


<a name="s3">Other Useful Resources</a>
---------------------------------------

- [Git Flight Rules](https://github.com/k88hudson/git-flight-rules)  
  Inspired by NASA's flight rules for the space program, detailed step-by-step instructions for a wide range of git tasks


<a name="s4">Glossary</a>
-------------------------

- `HEAD`
  A pointer to the most recent commit
- `master`
  An arbitrary name that by convention refers to the main branch of the repo
- `origin`  
  An arbitrary name that by convention refers to the canonical version of the repo


[link01]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
