Git
===

1: Install git
--------------

Update the apt package cache

```
sudo apt-get update
```

Install git

```
sudo apt-get install git
```

2: Configure global settings
----------------------------

username:

```
git config --global user.name "<name>"
```

useremail:

```
git config --global user.email <email_address>
```

3: Configure system settings
----------------------------

Append the following to `~/.bashrc`:

```
# set $VISUAL and $EDITOR (including for child shells)
export EDITOR='vim'
export VISUAL='vim'
```

Reload bashrc:

```
. ~/.bashrc
```

Confirm that bashrc gets loaded for [login][link01] sessions by checking .profile and .bash_profile:

```
less ~/.bash_profile
less ~/.profile
```

One of them (at least) should load .bashrc.


4: Link SSH key with Github
---------------------------

a) Create a key (if there isn't one already at `~/.ssh/id_rsa.pub`)

```
ssh-keygen -t rsa -b 4096 -C "<email_address>"
```

b) copy the contents of the created public key to the clipboard

c) go to github and select add a new SSH key, pasting the contents of the ssh key into the field


5: Clone a repo
---------------

```
git clone git@github.com:<user_id>/<repo>.git
```


Addendum 1: Combining Two Git Repos
-----------------------------------

1. [Create a new empty repository New.](#s1)
2. [Make an initial commit because we need one before we do a merge.](#s2)
3. Add a remote to old repository OldA.
4. Merge OldA/master to New/master.
5. Make a subdirectory OldA.
6. Move all files into subdirectory OldA.
7. Commit all of the file moves.
8. Repeat 3-6 for OldB.

<a name="s1">
### Create a new empty repository New ###
</a>

```Bash
mkdir new
cd new
git init
```

<a name="s2">
### Make an Initial Commit ###
</a>

```Bash
touch README.md
git add -A
git commit -m "Initial commit"
```


[link01]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
