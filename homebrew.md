[Homebrew][link01]
==================

Homebrew is a package manager for MacOS that installs programs into a folder (which it calls the Cellar) and symlinks to `/usr/local/bin`,
except GUI apps installed using `brew cask` which are installed into the `Applications` folder.

Installation
------------
Follow the [installation instructions][link01]:

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

**Notes**:
1. the `curl` options [mean][link02] as follows:
  - `-f` fail silently;
  - `-s` silent mode: don't show progress bars or error messages
  - `-S` show error: show error messages (when used with with the `-s` flag)
  - `-L` automatically follow 3XX response codes to redirect locations (but don't send authentication credentials to any host except the 
    original one.
2. the `ruby` options [mean][link03] as follows:
  - `-e "<some ruby code>"` execute the following argument as ruby code

System Setup
------------
Homebrew uses Ruby which is installed by default on the Mac, but is typically a very old version<sup>[1](#footnote01)</sup>. Therefore we
want to be able to install 
new versions of the tools (including Ruby) that Homebrew needs, but without overwriting the system-installed versions (which might be
required for system-related purposes).  
The way we do this is to ensure that /usr/local/bin (where we install programs) appears before /usr/bin (where Apple installs programs) in
the PATH.  
Note that it doesn't matter (except aesthetically) if things appear multiple times in the PATH, all the matters is that the first 
appearance of a particular path appears before the first appearance of another path.
See [Bash Environment][link04] for which profile file to edit to modify PATH.

Note that `usr/local` may not be writable (this error might be shown when running `brew doctor`). To remedy this, `chown` `usr/local` as 
follows:
```bash
sudo chown -R $(whoami) /usr/local
```
A permissions error when attempting to do this is caused by Apple's SIP, which needs to be [disabled][link06] for Homebrew to install 
correctly.


Usage
-----
[Common command syntax][link05] (in the form `brew <command>`):
- `--version` display version information then quit
- `install <thing>` installs the bottled (binary) package 'thing'; or chain multiple packages: `brew install foo bar baz`
- `install <thing> --<compile_option>` installs the package (thing) by building from source with the specified option<sup>[2](#footnote02)</sup>
- `uninstall <thing>` uninstalls the package 'thing'
- `update` updates Homebrew from the latest version on git (and update the local package listing—called *formulae*)
- `list` lists all installed *formulae* (not casks);
- `list --version <package>` lists all installed versions of `<package>`
- `doctor` perform various diagnostics on Homebrew and installed packages
- `search` search the package directory
- `cask <brew_command> [brew_command_args]` use Homebrew Cask for installation. Typically used for installing GUI applications, e.g,:
  `brew cask install vlc`, `brew cask list`
- `cleanup` delete all old applications in the Cellar
- `upgrade` install the latest version (per the local formulae) of all apps in the Cellar (without deleting old versions)
- `deps <package>|--installed` list the `<package>`(/all installed packages) and what they depend on
- `uses <package> --installed` list the installed packages that depend on `<package>`
- `info <package>` get info about a package


Dependencies
------------

### What depends on `<package>` ###

```console
$ brew uses lua --installed
vim
```

Note: we use the `--installed` flag otherwise homebrew will tell us every package in its index that depends on `<package>`.

Here, vim is the only installed packaged that uses (depends on) lua.

### What does `<package>` depend on ###

```console
$ brew deps vim
gdbm
gettext
libyaml
lua
openssl
perl
python
readline
ruby
sqlite
xz
```

Here, vim uses all the listed packages above.

To get a list of every installed package and its dependencies:
```console
$ brew deps --installed
...
nettle: gmp
openexr: ilmbase
openjpeg: jpeg libpng libtiff little-cms2
openssl: 
p11-kit: libffi
...
```

Here we see that `nettle` depends on `gmp`; `openjpeg` depends on `jpeg`, `libpng`, `libtiff`, and `little-cms2`;
`openssl` doesn't depend on any other packages.

Just list the packages that no other packages depend on (but note caveat below):
```console
$ brew leaves
bash
bats
coreutils
...
```

Note: `brew leaves` (at least as of Dec 2016) only lists `dependencies` not `requirements`. As such, it is possible to 
remove a package (trusting the output of `brew leaves`) that is the only installed package meeting a particular requirement,
which will break the requiring package (this might be fixed in homebrew 2.0.1, since we didn't get the same results as 
described [here](https://blog.jpalardy.com/posts/untangling-your-homebrew-dependencies/)).

Note: casks are excluded from the dependency listing (both ways). For example, there is no `brew cask uses <package>`
command, and `brew deps <package>` will not indicate that `<package>` depends on a particular cask.


Footnotes
---------
1. <a name="footnote01"> </a> Apple expressly [warns developers not to use the Apple-supplied versions of Ruby, Perl, Python or any other
scripting languages that might be found on a MacOS installation](https://developer.apple.com/library/content/documentation/Security/Conceptual/System_Integrity_Protection_Guide/FileSystemProtections/FileSystemProtections.html#//apple_ref/doc/uid/TP40016462-CH2-DontLinkElementID_2).
2. <a name="footnote02"> </a> Homebrew is [moving away](https://github.com/Homebrew/homebrew-core/issues/31510) from allowing options (since these are maintained by Homebrew itself) in favour of having project owners/users [produce (and thus maintain) their own taps for building from source](https://docs.brew.sh/How-to-Create-and-Maintain-a-Tap).





[link01]: https://brew.sh
[link02]: https://curl.haxx.se/docs/manpage.html
[link03]: https://robm.me.uk/ruby/2013/11/20/ruby-enp.html
[link04]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
[link05]: https://docs.brew.sh/Manpage
[link06]: https://developer.apple.com/library/archive/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html
