Java
====

Installation of JVM
-------------------

### What to Install ###

If we will be compiling any Java programs we will want the JDK otherwise we can install just the JRE. Note that the JDK contains the JRE, so 
there is no disadvantage to installing the JDK except the additional storage requirements<sup>[1](#footnote01)</sup>

We will be using the OpenJDK version.

### JDK ###

- update the apt package index
- install the JDK<sup>[2](#footnote02)</sup>
  ```
  sudo apt-get install default-jdk
  ```
  
### JRE ###

- update the apt package index
- install the JRE<sup>[2](#footnote02)</sup>
  ```
  sudo apt-get install default-jre
  ```


Footnotes
---------
<a name="footnote01">1</a>: As of 2018-03-20 the size difference on Ubuntu was 17% (420MB for JDK, 360MB for JRE)  
<a name="footnote02">2</a>: If we didn't know the name of the package we wanted, we could perform a search using apt-cache:  
                            ```
                            sudo apt-cache search java
                            ```
