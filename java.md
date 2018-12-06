Java
====

Installation of JVM
-------------------

### What to Install ###
If we will be compiling any Java programs we will want the JDK otherwise we can install just the JRE. Note that the JDK contains the JRE, so 
there is no disadvantage to installing the JDK except the additional storage requirements<sup>[1](#footnote01)</sup>

We will be using the OpenJDK version<sup>[2](#footnote02)</sup>.

### OpenJDK ###
- update the apt package index
- install the JDK<sup>[3](#footnote03)</sup>
  ```
  sudo apt-get install default-jdk
  ```

Note, if we wanted to install an older version of the JDK, we could do it like this:
```
sudo apt install openjdk-8-jdk
```
  
### OpenJRE ###
- update the apt package index
- install the JRE<sup>[3](#footnote03)</sup>
  ```
  sudo apt-get install default-jre
  ```


You can test that Java is installed by entering:

```
java -version
```

(Note you may need to restart your Bash session for the installation to fully take effect).

### Selecting an Instance ###
It is possible to have multiple versions of Java installed on system (at least on a Debian/Ubuntu Linux system). If more than one version
is installed, the desired version can be selected as default using the dpkg suite's `update-alternatives` command:

```
sudo update-alternatives --config <command_to_configure>
```

where `<command_to_configure>` is the command for which we want to set a default. Various Java components can be specified this way, such as 
`java` (the Java runtime), `javac` (the compiler), `javadoc` (documentation generator), `jarsigner` (signing tool), etc.

### Setting the `JAVA_HOME` Environment variable ###
The `JAVA_HOME` environment variable lets various programs know where Java is installed. To set it we do the following:
- note the installation location from `update-alternatives`:  
  `sudo update-alternatives --config java`
- edit `/etc/environment` and append the following line:
  `JAVA_HOME="<full/path/to/java>"` (e.g., `/lib/jvm/java-8-openjdk-amd64/jre/bin/java`)
- reload the environment file:
  `. /etc/environment`
- test that the variable was set:
  `echo $JAVA_HOME`
  


Footnotes
---------
<a name="footnote01">1</a>: As of 2018-03-20 the size difference on Ubuntu was 17% (420MB for JDK, 360MB for JRE)  
<a name="footnote02">2</a>: Note that OpenJDK v1.8 and v9 are both [officially supported](https://www.elastic.co/support/matrix#matrix_jvm) for Elastic Search  
<a name="footnote03">3</a>: If we didn't know the name of the package we wanted, we could perform a search using apt-cache:  
                            ```
                            sudo apt-cache search java
                            ```
