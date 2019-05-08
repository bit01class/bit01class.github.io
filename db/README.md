# DB LECTURE



### Oracle port번호 변경

```sql
sqlplus '/ as sysdba'
exec dbms_xdb.sethttpport(0);
commit;

```



### mariaDB 설치\(Windows\)

■ [https://downloads.mariadb.org/](https://downloads.mariadb.org/)에서 최신 mariaDB를 다운로드 받는다. 현재 최신 버전은 10.1.11이다. window7 64bit 컴퓨터에 설치할 것이므로 패키징 파일 중에서 [mariadb-10.1.11-winx64.zip](https://downloads.mariadb.org/interstitial/mariadb-10.1.11/winx64-packages/mariadb-10.1.11-winx64.zip/from/http%3A//ftp.kaist.ac.kr/mariadb/)파일을 다운로드 받는다. 파일을 클릭하면  이름과 이메일 등의 정보를 입력하는 창이 나타나는데 입력하면 파일이 다운로드 된다.[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 00](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-00.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-00.png)[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 01](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-01.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-01.png)

[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 02](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-02.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-02.png)

■ 다운로드받은 mariadb-10.1.11-winx64.zip파일의 압축을 풀고 mariadb-10.1.11-winx64 폴더를 mariaDB를 실행하고자 하는 폴더로 이동한다. 지금은 C:\mariaDB\mariadb-10.1.11-winx64 폴더로 이동시켰다. 그리고 DOS창을 열고 bin디렉토리로 가서 mysql\_install\_db 명령어를 실행한다. **DOS 창을 열때 아래와 같이 관리자 권한으로 실행해야 하는데 만약 그렇지 않으면 FATAL ERROR: OpenSCManager failed \(5\) 에러가 발생하고 윈도우 서비스에 mariaDB가 등록되지 않는다.**   
[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 03](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-03.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-03.png)

■ 아래와 같이 mysql\_install\_db명령어를 실행하는데, 옵션으로 데이터베이스 파일은 ‘C:\mariaDB\data’폴더에 생성하고 윈도우 서비스명은 ‘mariaDBZip’, 포트는 ‘5306’, 패스워드는 ‘xxxx’으로 정했다.

**&gt;mysql\_install\_db --datadir=C:\mariaDB\data --service=mariaDBZip --port=5306 --password=mysql**

[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 05](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-05.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-05.png)

■ mysql\_install\_db 명령어를 실행 후 윈도우 서비스를 열면 서비스명이 mariaDBZip인 서비스가 등록된 것을 확인할 수 있다. 상단 시작 화살표를 클릭해서 mariaDB를 실행한다.  
[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 04](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-04.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-04.png)

■ DOS창을 열고 C:\mariaDB\mariadb-10.1.11-winx64\bin&gt;**mysql -u root -p –port=5306**명령어를 실행하고 패스워드를 입력하면 아래와 같이 mariaDB에 접속할 수 있다

[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 06](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-06.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-06.png)[![mariaDB window7 zip&#xBC84;&#xC804; &#xC124;&#xCE58; 07](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-07.png)](http://blog.iotinfra.net/wp-content/uploads/2016/02/mariaDB-window7-zip%EB%B2%84%EC%A0%84-%EC%84%A4%EC%B9%98-07.png)

 **SQLyog 설치하기**

[https://code.google.com/archive/p/sqlyog/wikis/Downloads.wiki](https://code.google.com/archive/p/sqlyog/wikis/Downloads.wiki)



[mirinae.maru](http://blog.iotinfra.net/?author=1)[2016/02/15](http://blog.iotinfra.net/?p=1090)  [0 Comments](http://blog.iotinfra.net/?p=1090#respond)









###  인증\(Authentication\), 권한부여\(Authorization\), 접근제어\(Access Control\)

```text
-- Create the new database
mysql> create database db_example; 
---------------------------------------------
mysql> SHOW VARIABLES LIKE 'skip_name_resolve';
mysql> FLUSH PRIVILEGES;
mysql> SELECT `user`, `host`, `password` FROM `mysql`.`user`;
---------------------------------------------
-- Creates the user
mysql> CREATE USER 'user01'@'%' IDENTIFIED BY 'class01';
-- Gives all the privileges to the new user on the newly created database
-- mysql> grant all on db_example.* to 'springuser'@'%'; 
mysql> GRANT EXECUTE, PROCESS, SELECT, SHOW DATABASES, SHOW VIEW, ALTER, ALTER ROUTINE, CREATE, CREATE ROUTINE, CREATE TABLESPACE, CREATE TEMPORARY TABLES, CREATE VIEW, DELETE, DROP, EVENT, INDEX, INSERT, REFERENCES, TRIGGER, UPDATE, CREATE USER, FILE, LOCK TABLES, RELOAD, REPLICATION CLIENT, REPLICATION SLAVE, SHUTDOWN, SUPER  ON *.* TO 'user02'@'%' WITH GRANT OPTION;
mysql> FLUSH PRIVILEGES;
mysql> SHOW GRANTS FOR 'user01'@'%';

```







### Driver

* com.mysql.jdbc.Driver
* | `jdbc:mysql://[host][,failoverhost...][:port]/[database][?propertyName1][=propertyValue1][&propertyName2][=propertyValue2]...` |
  | :--- |

```text

jdbc:mysql://localhost:3306/lesstif?useUnicode=true&characterEncoding=utf8

-- jdbc:mysql://localhost:3306/lesstif?useUnicode=true&amp;characterEncoding=utf8


```



### MySQL

```sql
-- sequence
idx int primary key auto_increment

-- nextval , currval
insert table tablename(sub,lvl) 
    select 'NOsubject',max(index)+1
    from tablename;
```



































