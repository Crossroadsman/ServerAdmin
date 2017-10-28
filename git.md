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


3: Link SSH key with Github
---------------------------

a) Create a key (if there isn't one already at `~/.ssh/id_rsa.pub`)

```
ssh-keygen -t rsa -b 4096 -C "<email_address>"
```

b) copy the contents of the created public key to the clipboard

c) go to github and select add a new SSH key, pasting the contents of the ssh key into the field


4: Clone a repo
---------------

```
git clone git@github.com:<user_id>/<repo>.git
```

