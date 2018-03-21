Uninstalling (APT)
==================

Sometimes we want to remove an application that was installed using APT. For packages installed using a package manager it is important to
only remove those packages using the package manager.

For removing applications with APT there are several common ways of doing it, depending on why you want to remove the package:

(Note all explanations are based on the following [ask ubuntu][link01] answer: [What is the correct way to completely remove an application?][link02])

`apt-get remove packagename`
----------------------------
This will remove the binaries but not any configuration or data files. It also leaves dependencies untouched.

`apt-get purge packagename`
---------------------------
This removes everything<sup>[1](#footnote01)</sup> (binaries and config files) for the package (but doesn't remove any dependencies).

`apt-get autoremove`
--------------------
Removes 'orphaned' packages: installed packages that were installed as a dependency but are no longer required by any installed programs.



<a name="#footnote01">1</a>: except files that are in users' home directories

[link01]: https://askubuntu.com
[link02]: https://askubuntu.com/questions/187888/what-is-the-correct-way-to-completely-remove-an-application
