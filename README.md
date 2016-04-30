# Step by Step of How to Install Oracle Database 12c on Ubuntu
## 1. Oracle Installation Prerequisites
Host file modification

The **/etc/hosts** file must contain a fully qualified name for the server
`IP-address fully-qualified-machine-name machine-name`

## 2.Hardware requirement
1. General Server Minimum Requirements
Server should be started in runlevel 3 or runlevel 5.
Server display cards provide at least 1024 x 768 display resolution.

2. Disk Space Requirements on Linux x86-64
Disk Space for Enterprise Edition Installation type is 6.4 GB.
1 GB of space in the **/tmp** directory on your Linux system.

3. Server memory Minimum Requirements
Ensure that your system meets the following memory requirements:
Minimum: 1 GB of RAM
Recommended: 2 GB of RAM or more Swap Space Requirement for Linux
- If RAM size is between 1 GB to 2 GB then Swap Space should be 1.5 times of the RAM.
- If RAM size is between 2 GB to 16 GB then Swap Space should be equal to the size of the RAM.
- If RAM size is more than 16 GB then Swap Space should be 16 GB.

## 3. Install Prerequisites packages
1. Open a command prompt by pressing **CTRL + ALT + T** and run following command as **a root/ root equivalent user** to downloads the package lists from the repositories and updates them to get imformation on the newest versions of packages and their dependencies. It will do this for all repositories and PPAs
`$ sudo apt-get update`

2. Run following command to handle changing dependencies with new versions of packages
`$ sudo apt-get dist-upgrade`

3. Restart machine by running following command
`$ sudo reboot --reboot`

4. Again run dist-upgrade to check if there is any new version of packages
`$ sudo apt-get dist-upgrade`

5. Run following command to install dependencies
`$ sudo apt-get -y install alien binutils build-essential cpp-4.7 debhelper g++-4.7 gawk gcc-4.7 gcc-4.7-base gettext html2text lib32z1 lib32ncurses5 intltool-debian ksh lib32z1-dev libaio-dev libaio1 libbeecrypt7 libc6 libc6-dev libc6-dev-i386 libelf-dev libelf1 libltdl-dev libltdl7 libmotif4 libodbcinstq4-1 libodbcinstq4-1:i386 libqt4-core libqt4-gui libsqlite3-0 libstdc++5 libstdc++6 libstdc++6-4.7-dev lsb lsb-core lsb-cxx lsb-desktop lsb-graphics make odbcinst pax po-debconf rpm rpm-common sysstat unixodbc unixodbc-dev unzip`

## 4. Creating Required Operating System Groups and Users
1. Run following command as **root/ root equivalent user** to create required Operating System groups and users
```
$ sudo addgroup oinstall
$ sudo addgroup dba
$ sudo addgroup nobody
$ sudo usermod -g nobody nobody
$ sudo useradd -g oinstall -G dba -p password -d /home/oracle -s /bin/bash oracle
$ sudo mkdir /home/oracle
$ sudo chown -R oracle:dba /home/oracle
$ sudo mkdir -p /u01/app/oracle
$ sudo mkdir -p /u01/binaries
$ sudo chown -R oracle:dba /u01
```

2. Set the password for the oracle user which we have created for installaion purpose
`$ sudo passwd oracle`

## 5. Configuring Kernel Parameters and Resource Limits
1. Make ourself as Red Hat by running following command as **root/ root equivalent user**
`$ echo 'Red Hat Linux release 6' | sudo tee -a /etc/redhat-release`

2. We need to create some soft links to start the installation. To do so run the following commands as **root/ root equivalent user**
```
$ sudo mkdir /usr/lib64
$ sudo ln -s /etc /etc/rc.d
$ sudo ln -s /lib/x86_64-linux-gnu/libgcc_s.so.1 /lib64/
$ sudo ln -s /usr/bin/awk /bin/awk
$ sudo ln -s /usr/bin/basename /bin/basename
$ sudo ln -s /usr/bin/rpm /bin/rpm
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libc_nonshared.a /usr/lib64/
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libpthread_nonshared.a /usr/lib64/
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /lib64/
$ sudo ln -s /usr/lib/x86_64-linux-gnu/libstdc++.so.6 /usr/lib64/
```

3. To change the shell configuration for oracle user run following commands as **root/ root equivalent user**
```
$ sudo cp /etc/security/limits.conf /etc/security/limits.conf.original
$ echo "#Oracle 12C shell limits:" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		soft	nproc		2048" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		hard	nproc		16384" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		soft	nofile		1024" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		hard	nofile		65536" | sudo tee -a /etc/security/limits.conf
```

4. Run following commands to change the kernel parameters as **root/ root equivalent user**
```
$ echo "#" | sudo tee -a /etc/sysctl.conf
$ echo "# Oracle 12C entries" | sudo tee -a /etc/sysctl.conf
$ echo "fs.aio-max-nr=1048576" | sudo tee -a /etc/sysctl.conf
$ echo "fs.file-max=6815744" | sudo tee -a /etc/sysctl.conf
$ echo "kernel.shmall=2097152" | sudo tee -a /etc/sysctl.conf
$ echo "kernel.shmmni=4096" | sudo tee -a /etc/sysctl.conf
$ echo "kernel.sem=250 32000 100 128" | sudo tee -a /etc/sysctl.conf
$ echo "net.ipv4.ip_local_port_range=9000 65500" | sudo tee -a /etc/sysctl.conf
$ echo "net.core.rmem_default=262144" | sudo tee -a /etc/sysctl.conf
$ echo "net.core.rmem_max=4194304" | sudo tee -a /etc/sysctl.conf
$ echo "net.core.wmem_default=262144" | sudo tee -a /etc/sysctl.conf
$ echo "net.core.wmem_max=1048586" | sudo tee -a /etc/sysctl.conf
$ echo "kernel.shmmax=1073741824" | sudo tee -a /etc/sysctl.conf
```
*Note: kernel.shmmax = max possible value, e.g. size of physical memory in bytes. Im my case machine is having 2GB so we are specifying 1GB. Adjust the parameter as per your configuration*

5. Load new kernel parametares, by running following as **root/ root equivalent user**
`$ sudo sysctl -p`

6. We need to do changes in start-up script, run following commands as **root/ root equivalent user**
```
$ for i in 0 1 2 3 4 5 6 S
> do sudo ln -s /etc/rc$i.d /etc/rc.d/rc$i.d
> done
```

## 6. Downloading Software
Login as a oracle user as we are going to install database using oracle user.
Go to following link to download Oracle 12c.
![Download Oracle Database 12c Enterprise Edition](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
Check **Accept License Agreement** radio button and click on **File 1**
This will redirect to the login screen. Enter your credentials and click on **Sign in** button
Download the file to **/u01/binaries**. Do the same for **File 2**.

## 7. Unpack Files
Login as a oracle user and open a terminal by pressing **CTRL + ALT + T**
Go to **/u01/binaries** folder using following command
`$ cd /u01/binaries`

Run following commands to extract binaries
```
$ unzip linuxamd64_12102_database_1of2.zip
$ unzip linuxamd64_12102_database_2of2.zip
```

Before doing the installation we need to give proper permission to the extracted file, to do the same run follwing command
`$ chown -Rf oracle:dba /u01/binaries`

## 8. Install Oracle 12c on Ubuntu
1. Login as a oracle user andstart installation using following commands
```
$ cd /u01/binaries/database
$ ./runInstaller -ignoreSysPrereqs
```

2. In the **Configure Security Updates** unckecked **I wish to receive security updates via My Oracle Support.** and click **Next**
It will ask about the confirmation, click **Yes**

3. As the Ubuntu is not supported OS we need to we are seeing the below error, just click on **Yes** button to go ahead

4. In **Selection Installation Option** choose **Create and configure a database** and click on **Next** to go further

5. In **System Class** screen select **Server Class** and click on **Next** to go further

6. In **Grid Installation Options** screen select **Single instance database installation** and click on **Next** to go further

7. In **Select Install Type** screen select **Advanced install** and click on **Next** to go further

8. In **Select Product Language** screen select appropriate language and click on **Next** to go further

9. In **Select Database Edition** screen select **Enterprise Edition**. Click **Next** to go further

10. In **Specify Installation Location** screen, ensure that following values are there and click **Next** to go further
Oracle base: /u01/app/oracle
Software location: /u01/app/oracle/product/12.1.0/dbhome_1

11. In **Create Inventory** screen, ensure that **Inventory Directory** is **/u01/app/oraInventory** and click **Next** to go further

12. In **Select Configuration Type** screen, select **General Purpose / Transaction Processing** and click **Next** to proceed further

13. In **Specify Database Identifiers** screen, let it be default and ensure that you have **unchecked Create as Container database** option.
Click **Next** to go further. Here we need to remember the **Oracle system identifier (SID)**

14. In **Specify Configuration Options** configure as below:
Under **Memory** select **Enable Automatic Memory Management: TRUE**
Under **Character sets** select **Use Unicode (AL32UTF8)**

15. Under **Specify Database Storage Options** ensure that database file location is **/u01/app/oracle/oradata** and click **Next** to go further

16. Leave default settings under **Specify Management Options** and click **Next** to go further

17. Leave default settings under **Specify Recovery Options** and click **Next** to go further

18. In **Specify Schema Passwords** screen I choose **Use the same password for all accounts** because of laziness. Enter password and click **Next** to go further
Skip the password warning by click **Yes**

19. Leave the default option under **Privileged Operating System groups** and click **Next** to go further

20. Save the summary for future use and click **Install** to go further

21. At around 59% during **Link binaries** you will see an error as **Error in invoking target 'all_no_orcl' of makefile**
in the log file observed following line
```
INFO: /usr/bin/ld: /u01/app/oracle/product/12.1.0/dbhome_1/lib//libclntsh.so: undefined reference to symbol 'ztucxtb'
/u01/app/oracle/product/12.1.0/dbhome_1/lib//libnnz12.so: error adding symbols: DSO missing from command line
 
INFO: collect2: error: ld returned 1 exit status
 
INFO: /u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/ins_rdbms.mk:878: recipe for target '/u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/orapwd' failed
```

ldd output of **/u01/app/oracle/product/12.1.0/dbhome_1/lib/libnnz12.so** file shows following
```
$ ldd /u01/app/oracle/product/12.1.0/dbhome_1/lib/libnnz12.so
	linux-vdso.so.1 =>  (0x00007fffd6aa0000)
	libclntshcore.so.12.1 => not found
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fa13b1f8000)
	/lib64/ld-linux-x86-64.so.2 (0x00007fa13bcef000)
```

Important line is **libclntshcore.so.12.1 => not found**. Run following command as **root/ root equivalent user** to rectify this
`$ sudo ln -s /u01/app/oracle/product/12.1.0/dbhome_1/lib/libclntshcore.so.12.1 /usr/lib/`

Now see the linking again
```
$ ldd /u01/app/oracle/product/12.1.0/dbhome_1/lib/libnnz12.so
	linux-vdso.so.1 => (0x00007fff5c5fe000)
	libclntshcore.so.12.1 => /usr/lib/libclntshcore.so.12.1 (0x00007f133b180000)
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007f133adb8000)
	libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007f133abb3000)
	libm.so.6 => /lib/x86_64-linux-gnu/libm.so.6 (0x00007f133a8af000)
	libpthread.so.0 => /lib/x86_64-linux-gnu/libpthread.so.0 (0x00007f133a692000)
	librt.so.1 => /lib/x86_64-linux-gnu/librt.so.1 (0x00007f133a489000)
	/lib64/ld-linux-x86-64.so.2 (0x00007f133bdff000)
```

Also we need to do changes in **ins_rdbms.mk** file
Take a backup of **/u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/ins_rbdms.mk** by running following command

