1. Edit **/etc/hosts** file

`IP-address fully-qualified-machine-name machine-name`

2. `$ sudo apt-get dist-upgrade`

3. `$ sudo reboot --reboot`

4. `$ sudo apt-get dist-upgrade`

5. `$ sudo apt-get -y install alien binutils build-essential cpp-4.7 debhelper g++-4.7 gawk gcc-4.7 gcc-4.7-base gettext html2text lib32z1 lib32ncurses5 intltool-debian ksh lib32z1-dev libaio-dev libaio1 libbeecrypt7 libc6 libc6-dev libc6-dev-i386 libelf-dev libelf1 libltdl-dev libltdl7 libmotif4 libodbcinstq4-1 libodbcinstq4-1:i386 libqt4-core libqt4-gui libsqlite3-0 libstdc++5 libstdc++6 libstdc++6-4.7-dev lsb lsb-core lsb-cxx lsb-desktop lsb-graphics make odbcinst pax po-debconf rpm rpm-common sysstat unixodbc unixodbc-dev unzip`

6.
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

7. `$ sudo passwd oracle`

8. `$ echo 'Red Hat Linux release 6' | sudo tee -a /etc/redhat-release`

9. 
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

10. 
```
$ sudo cp /etc/security/limits.conf /etc/security/limits.conf.original
$ echo "#Oracle 12C shell limits:" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		soft	nproc		2048" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		hard	nproc		16384" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		soft	nofile		1024" | sudo tee -a /etc/security/limits.conf
$ echo "oracle		hard	nofile		65536" | sudo tee -a /etc/security/limits.conf
```

11. 
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

12. `$ sudo sysctl -p`

13. 
```
$ for i in 0 1 2 3 4 5 6 S
> do sudo ln -s /etc/rc$i.d /etc/rc.d/rc$i.d
> done
```

14. `$ cd /u01/binaries`

15. 
```
$ unzip linuxamd64_12102_database_1of2.zip
$ unzip linuxamd64_12102_database_2of2.zip
```

16. `$ chown -Rf oracle:dba /u01/binaries`

17. 
```
$ cd /u01/binaries/database
$ ./runInstaller -ignoreSysPrereqs
```

18. **Configure Security Updates**: unckecked **I wish to receive security updates via My Oracle Support.** -> **Next**-> **Yes** -> **Yes**

19. **Selection Installation Option**: **Create and configure a database** -> **Next**

20. **System Class**: **Server Class** -> **Next**

21. **Grid Installation Options**: **Single instance database installation** -> **Next**

22. **Select Install Type**: **Advanced install** -> **Next**

23. **Select Product Language**: **Next**

24. **Select Database Edition**: **Enterprise Edition** -> **Next**

25. **Specify Installation Location**:
* Oracle base: */u01/app/oracle*
* Software location: */u01/app/oracle/product/12.1.0/dbhome_1*
**Next**

26. **Create Inventory**:
* Inventory Directory: */u01/app/oraInventory*
**Next**

27. **Select Configuration Type**: **General Purpose / Transaction Processing** -> **Next**

28. **Specify Database Identifiers**: unchecked **Create as Container database** -> **Next**

29. **Specify Configuration Options**:
* Memory: **Enable Automatic Memory Management**
* Character sets*: **Use Unicode (AL32UTF8)**

30. **Specify Database Storage Options**: **Next**

31. **Specify Management Options**: **Next**

32. **Specify Recovery Options**: **Next**

33. **Specify Schema Passwords**: **Use the same password for all accounts** -> *Enter password* -> **Next** -> **Yes**

32. **Privileged Operating System groups**: **Next**

33. **Install**

*Error in invoking target 'all_no_orcl' of makefile*
34. `$ sudo ln -s /u01/app/oracle/product/12.1.0/dbhome_1/lib/libclntshcore.so.12.1 /usr/lib/`

35. `$ cp -rip /u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/ins_rdbms.mk /u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/ins_rdbms.mk.original`

36. Open **/u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/ins_rdbms.mk**

* Search **Linking password utility (orapwd)** -> $(ORAPWD_LINKLINE) -lnnz12

* Search **Linking HS OTS agent** -> $(HSOTS_LINKLINE) -lagtsh

* Search **Linking external procedure agent** -> $(EXTPROC_LINKLINE) -lagtsh

37. `$ sudo ln -s /u01/app/oracle/product/12.1.0/dbhome_1/lib/libclntsh.so.12.1 /usr/lib/`

38. `$ cp -rip /u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/env_rdbms.mk /u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/env_rdbms.mk.original`

39. Open **/u01/app/oracle/product/12.1.0/dbhome_1/rdbms/lib/env_rdbms.mk**

* Search **HSOTS_LINKLINE** -> $(OPT) -Wl,--no-as-needed

* Search **HSDEPXA_LINKLINE** -> $(OPT) -Wl,--no-as-needed

* Search **AMDU_LINKLINE** -> $(SOMAIN) -Wl,--no-as-needed

* Search **KFED_LINKLINE** -> $(SOMAIN) -Wl,--no-as-needed

* Search **KFNDG_LINKLINE** -> $(SOMAIN) -Wl,--no-as-needed

* Search **EXTPROC_LINKLINE** -> $(OPT) -Wl,--no-as-needed

* Search **ORACLE_LINKLINE** -> $(ORACLE_LINKER) -Wl,--no-as-needed

40. Open **/u01/app/oracle/product/12.1.0/dbhome_1/network/lib/ins_net_server.mk**

* Search **Linking $(TNSLSNR)** -> $(TNSLSNR_LINKLINE) -lnnz12 -lons

41. `$ sudo ln -s /u01/app/oracle/product/12.1.0/dbhome_1/lib/libocrutl12.so /usr/lib`

42. To start listener:
```
$ export ORACLE_BASE=/u01/app/oracle
$ export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
$ export ORACLE_OWNR=oracle
$ export PATH=$PATH:$ORACLE_HOME/bin
$ export ORACLE_SID=orcl
$ $ORACLE_HOME/bin/lsnrctl start
```

43. To start database:
```
$ sqlplus /nolog
SQL> connect sys as sysdba
SQL> STARTUP;
SQL> EXIT;
```

44. To stop database:
```
$ sqlplus /nolog
SQL> connect sys as sysdba
SQL> SHUTDOWN;
SQL> EXIT;
```

45. To stop listener:
`$ $ORACLE_HOME/bin/lsnrctl stop`

46. To create USER:
```
$ sqlplus /nolog
SQL> connect sys as sysdba
SQL> alter session set "_ORACLE_SCRIPT"=true;
```
