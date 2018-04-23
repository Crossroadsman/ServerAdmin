[Homebrew][link01]
==================

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
Homebrew uses Ruby which is installed by default on the Mac, but is typically a very old version. Therefore we want to be able to install 
new versions of the tools (including Ruby) that Homebrew needs, but without overwriting the system-installed versions (which might be
required for system-related purposes).
The way we do this is to ensure that /usr/local/bin (where we install programs) appears before /usr/bin (where Apple installs programs) in
the PATH.
Note that it doesn't matter (except aesthetically) if things appear multiple times in the PATH, all the matters is that the first 
appearance of a particular path appears before the first appearance of another path.
See [Bash Environment][link04] for which profile file to edit to modify PATH.


Usage
-----
[Common command syntax][link05] (in the form `brew <command>`):
- `--version` display version information then quit
- `install <thing>` installs the package 'thing'
- `uninstall <thing>` uninstalls the package 'thing'
- `update` updates Homebrew from the latest version on git
- `list` lists all installed *formulae*
- `doctor` perform various diagnostics on Homebrew and installed packages




[link01]: https://brew.sh
[link02]: https://curl.haxx.se/docs/manpage.html
[link03]: https://robm.me.uk/ruby/2013/11/20/ruby-enp.html
[link04]: https://github.com/Crossroadsman/TerminalTips/blob/master/BashEnvironmentVariables.md
[link05]: https://docs.brew.sh/Manpage
