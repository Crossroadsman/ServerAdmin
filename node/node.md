[node.js][node_01]
==================

Installing Node
---------------
There are three main ways to install node. The recommended way is to use nvm.

### Installing the Distro-Stable Version for Ubuntu ###
See also [Digital Ocean's guide][digo_01]

- update apt's package listing
  ```console
  $ sudo apt update
  ```
- install node.js (note that due to a naming conflict, node.js on Ubuntu is
  called nodejs, not 'node' as on some other systems)
  ```console
  $ sudo apt install nodejs
  ```
- check node's version:
  ```console
  $ nodejs -v
  v8.10.0
  ```
- install npm:
  ```console
  $ sudo apt install npm
  ```
- check npm's version:
  ```console
  $ npm -v
  3.5.2
  ```


### Installing a Newer Version of Node Using a PPA ###
The Distribution-Stable release of node.js is quite old. You can add a PPA to 
get a newer version. See the [Digital Ocean Guide][digo_01] to go this route.


### Installing Using NVM (Node Version Manager) ###
Instead of using apt (which installs globally), you can install node into a
folder in your home directory which enables the use of multiple different
versions of node without conflicts).

- Get the nvm installation script from the project's [GitHub page][nvmg_01].
  Note that we are downloading the script, rather than just following the
  project's instructions which pipe the download straight into bash to execute
  the script. This gives us the chance to review the script before we run it:
  - using curl<sup>[1](#install_01)</sup>:
    ```console
    $ curl -L https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh \
    -o install_nvm.sh
    ```
  - or using wget<sup>[2](#install_02)</sup>:
    ```console
    $ wget https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh \
    -O install_nvm.sh
    ```

- Review the installation script:
  ```console
  $ cat install_nvm.sh
  ```

- Assuming the script is ok, execute it with bash to install nvm:
  ```console
  $ bash install_nvm.sh
  ```

- The script installs the software into `~/.nvm` and adds necessary lines to
  `~/.profile` or `~/.bashrc`. To gain access to nvm, either log out and log 
  back in or reload whichever file was updated (will have 
  `export NVM_DIR="$HOME/.nvm"` etc in it):
  - either:
    ```console
    $ . .profile
    ```
  - or:
    ```console
    $ . .bashrc
    ```

- Once nvm is installed, you can see which node versions are available:
  ```console
  $ nvm ls-remote
  ...
  v10.7.0
  v10.8.0
  v10.9.0
  v10.10.0
  v10.11.0
  v10.12.0
  v10.13.0   (LTS: Dubnium)
  v10.14.0   (LTS: Dubnium)
  v10.14.1   (Latest LTS: Dubnium)
  v11.0.0
  v11.1.0
  v11.2.0
  ...
  ```

- You can install versions using `nvm install`:
  - to install a specific version, use its version number:
    ```console
    $ nvm install 10.10.0
    Downloading and installing node v10.10.0...
    Downloading https://nodejs.org/dist/v10.10.0/node-v10.10.0-linux-x64.tar.xz...
    ######################################################################### 100.0%
    Computing checksum with sha256sum
    Checksums matched!
    Now using node v10.10.0 (npm v6.4.1)
    Creating default alias: default -> 10.10.0 (-> v10.10.0)
    ```
  - to install the latest version, use the alias `node`:
    ```console
    $ nvm install node
    Downloading and installing node v11.4.0...
    Downloading https://nodejs.org/dist/v11.4.0/node-v11.4.0-linux-x64.tar.xz...
    ######################################################################### 100.0%
    Computing checksum with sha256sum
    Checksums matched!
    Now using node v11.4.0 (npm v6.4.1)
    ```

- You can see which:
  - version of node is currently being used:
    ```console
    $ nvm which current
    /home/my_user/.nvm/versions/node/v11.4.0/bin/node
    ```
  - path a particular node version points to:
    ```console
    $ nvm which 10
    /home/my_user/.nvm/versions/node/v10.10.0/bin/node
    ```

- Specifying a full version number will look only at that exact version, 
  specifying a partial version number will look for the newest version in that
  branch. For example, `11.4.0` means only `11.4.0`, `8.1` will look for the
  most recent 8.1.x version, i.e., `8.1.4`; `8` will look for the most recent
  8.x.x version, i.e., `8.14.0`.

- Specifying `--lts` will only find the newest matching lts release; 
  `--lts/dubnium` will only find the newest matching lts release in the 
  `dubnium` branch.

- Once installed you can use nvm as follows:
  - set the currently in-use version:
    ```console
    $ nvm use 8
    Now using node v8.14.0 (npm v6.4.1)
    ```
    
    ```console
    $ nvm use node
    Now using node v11.4.0 (npm v6.4.1)
    ```
    
    ```console
    $ nvm use --lts
    Now using node v10.14.1 (npm v6.4.1)
    ```
  - run a particular version of node (node is an alias to the current 
    `use` version):
    ```console
    $ node --version
    v10.14.1
    ```
    
    ```console
    $ nvm run 8
    Running node v8.14.0 (npm v6.4.1)
    >
    ```
  - execute an arbitrary command in a subshell with the specified node version:
    ```console
    $ nvm exec 8.1 node --version
    Running node v8.1.4 (npm v5.0.3)
    v8.1.4
    ```
    
When using nvm, regular `npm` installs will be local to whichever package is in
use. Installing with the `-g` (global) option will install it to all projects
based on the same version of node.



Footnotes
---------
1. <a name="install_01"> </a>optional argument `-L <url>` (aka `--location`) 
   will follow 3XX redirects, `-o <filename>` (aka `--output`) writes to the
   specified filename (rather than stdout, `-o-` explicitly outputs to stdout)
2. <a name="install_02"> </a>`-O <filename>` writes the output to the specified
   filename. Note this is **not** equivalent to renaming the downloaded file
   to the specified filename, it is equivalent to shell redirection of the 
   download to the specified filename (so, for example, if more than one file 
   is being downloaded, they will all be concatenated into the single file.





[digo_01]: https://www.digitalocean.com/community/tutorials/how-to-install-node-js-on-ubuntu-18-04
[node_01]: https://nodejs.org/en/
[nvmg_01]: https://github.com/creationix/nvm
