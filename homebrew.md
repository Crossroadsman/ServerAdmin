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
- `install <thing>` installs the package 'thing'; or chain multiple packages: `brew install foo bar baz`
- `uninstall <thing>` uninstalls the package 'thing'
- `update` updates Homebrew from the latest version on git (and update the local package listing—called *formulae*)
- `list` lists all installed *formulae*
- `doctor` perform various diagnostics on Homebrew and installed packages
- `search` search the package directory
- `cask <brew_command> [brew_command_args]` use Homebrew Cask for installation. Typically used for installing GUI applications, e.g,:
  `brew cask install vlc`
- `brew cleanup` delete all old applications in the Cellar
- `brew upgrade` install the latest version (per the local formulae) of all apps in the Cellar (without deleting old versions)



Footnotes
---------
<a name="footnote01">1</a>: Apple expressly [warns developers not to use the Apple-supplied versions of Ruby, Perl, Python or any other
scripting languages that might be found on a MacOS installation](https://developer.apple.com/library/content/documentation/Security/Conceptual/System_Integrity_Protection_Guide/FileSystemProtections/FileSystemProtections.html#//apple_ref/doc/uid/TP40016462-CH2-DontLinkElementID_2).


[link01]: https://brew.sh
[link02]: https://curl.haxx.se/docs/manpage.html
[link03]: https://robm.me.uk/ruby/2013/11/20/ruby-enp.html
[link04]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
[link05]: https://docs.brew.sh/Manpage
[link06]: https://developer.apple.com/library/archive/documentation/Security/Conceptual/System_Integrity_Protection_Guide/ConfiguringSystemIntegrityProtection/ConfiguringSystemIntegrityProtection.html
