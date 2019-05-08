# linux (ubuntu)

​

## JDK
  ```
  sudo apt install default-jdk              # version 2:1.11-71, or
  sudo apt install openjdk-11-jdk-headless  # version 11.0.3+7-1ubuntu1
  sudo apt install openjdk-8-jdk-headless   # version 8u212-b01-1
  sudo apt install ecj                      # version 3.16.0-1
  sudo apt install openjdk-12-jdk-headless  # version 12.0.1+12-1
  sudo apt install openjdk-13-jdk-headless  # version 13~13-0ubunt1
  ```


## Oracle(10g x86)
(https://oss.oracle.com/debian/dists/unstable/non-free/binary-i386/)

  ```
  sudo apt-get install libc6-i386
  wget -c http://oss.oracle.com/debian/dists/unstable/main/binary-i386/libaio_0.3.104-1_i386.deb http://oss.oracle.com/debian/dists/unstable/non-free/binary-i386/oracle-xe-universal_10.2.0.1-1.1_i386.deb
  sudo apt-get install bc
  ​
  sudo dpkg -i libaio_0.3.104-1_i386.deb
  ​
  sudo dpkg -i oracle-xe-universal_10.2.0.1-1.1_i386.deb
  ​
  sudo /etc/init.d/oracle-xe configure
  ​
  Enter [YOUR DEFINED PASSWORD]
  ```

* and edit your ~/.bashrc
  ```
  ORACLE_HOME=/usr/lib/oracle/xe/app/oracle/product/10.2.0/server
  PATH=$PATH:$ORACLE_HOME/bin
  export ORACLE_HOME
  export ORACLE_SID=XE
  export PATH
  ```

* go to http://127.0.0.1:8080/apex from your browser.

* sqlplus sys/[YOUR DEFINED PASSWORD]
  ```
  alter user HR account unlock ; 
  alter user HR identified by $password ; 
  exit
  ```
  
* sqlplus HR/$password
  ```
  SELECT table_name FROM user_tables;
  SELECT * FROM regions ;
  INSERT INTO REGIONS (REGION_ID, REGION_NAME) VALUES (666, 'Outer Mongolia') ;
  COMMIT ;
  ```
