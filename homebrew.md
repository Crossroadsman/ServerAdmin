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


Usage
-----
[Common command syntax][link04] (in the form `brew <command>`):
- `--version` display version information then quit
- `install <thing>` installs the package 'thing'
- `uninstall <thing>` uninstalls the package 'thing'
- `update` updates Homebrew from the latest version on git
- `list` lists all installed *formulae*




[link01]: https://brew.sh
[link02]: https://curl.haxx.se/docs/manpage.html
[link03]: https://robm.me.uk/ruby/2013/11/20/ruby-enp.html
[link04]: https://docs.brew.sh/Manpage
