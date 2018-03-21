ElasticSearch
=============

Pre-requisites
--------------

- Install the most current [JVM][link01]
- (Optional) confirm that the OS is included in the [ElasticSearch Support Matrix][link02]

Installation
------------
### Configure apt for Use With Elastic's Repository ###
- Install Elastic's signing key:  
  ```
  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  ```
  **Notes**:
  - `-q` means [`quiet`][link03] (turn off output)
  - `-O <file>` is an alias for [`--output-document=<file>`][link04] which tells wget that instead of writing the documents to the 
    appropriate files, all the files should be concatenated together and written to the specified <file>
  - `-` used as an argument to `-O` means that stdout should be used as the 'file'
  - `apt-key` is the [APT key management utility][link05]
  - `add <file>` adds a new key to the list of trusted keys. Using `-` as the file means to get the key from stdin
- Type `apt-key list` to verify that Elastic's signing key was added to the key manager
- Install `apt-transport-https` if not installed already:
  ```
  sudo apt-get install apt-transport-https
  ```




[link01]: https://github.com/Crossroadsman/ServerAdmin/blob/master/java.md
[link02]: https://www.elastic.co/support/matrix
[link03]: https://www.gnu.org/software/wget/manual/html_node/Logging-and-Input-File-Options.html#Logging-and-Input-File-Options
[link04]: https://www.gnu.org/software/wget/manual/html_node/Download-Options.html#Download-Options
[link05]: https://www.systutorials.com/docs/linux/man/8-apt-key/
