# JAVA LECTURE

### Open JDK

* download - [link](https://developers.redhat.com/products/openjdk/download/)
* Ubuntu - sudo apt-get install openjdk-8-jdk
* Sentos - yum install java-1.8.0-openjdk-devel.x86\_64

```bash
# which javac
결과 : /usr/bin/javac
# which java 
결과 : /usr/bin/java
```

```bash
# ls -l /usr/bin/java*
lrwxrwxrwx 1 root root 22  1월 20 10:34 /usr/bin/java -> /etc/alternatives/java
lrwxrwxrwx 1 root root 23  1월 20 10:51 /usr/bin/javac -> /etc/alternatives/javac
lrwxrwxrwx 1 root root 25  1월 20 10:51 /usr/bin/javadoc -> /etc/alternatives/javadoc
lrwxrwxrwx 1 root root 23  1월 20 10:51 /usr/bin/javah -> /etc/alternatives/javah
lrwxrwxrwx 1 root root 23  1월 20 10:51 /usr/bin/javap -> /etc/alternatives/javap

# ls -l /etc/alternatives/java*
lrwxrwxrwx 1 root root 46  1월 20 10:51 /etc/alternatives/java -> /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
lrwxrwxrwx 1 root root 56  1월 20 10:51 /etc/alternatives/java.1.gz -> /usr/lib/jvm/java-8-openjdk-amd64/jre/man/man1/java.1.gz
lrwxrwxrwx 1 root root 43  1월 20 10:51 /etc/alternatives/javac -> /usr/lib/jvm/java-8-openjdk-amd64/bin/javac
lrwxrwxrwx 1 root root 53  1월 20 10:51 /etc/alternatives/javac.1.gz -> /usr/lib/jvm/java-8-openjdk-amd64/man/man1/javac.1.gz
lrwxrwxrwx 1 root root 45  1월 20 10:51 /etc/alternatives/javadoc -> /usr/lib/jvm/java-8-openjdk-amd64/bin/javadoc
lrwxrwxrwx 1 root root 55  1월 20 10:51 /etc/alternatives/javadoc.1.gz -> /usr/lib/jvm/java-8-openjdk-amd64/man/man1/javadoc.1.gz
lrwxrwxrwx 1 root root 43  1월 20 10:51 /etc/alternatives/javah -> /usr/lib/jvm/java-8-openjdk-amd64/bin/javah
lrwxrwxrwx 1 root root 53  1월 20 10:51 /etc/alternatives/javah.1.gz -> /usr/lib/jvm/java-8-openjdk-amd64/man/man1/javah.1.gz
lrwxrwxrwx 1 root root 43  1월 20 10:51 /etc/alternatives/javap -> /usr/lib/jvm/java-8-openjdk-amd64/bin/javap
lrwxrwxrwx 1 root root 53  1월 20 10:51 /etc/alternatives/javap.1.gz -> /usr/lib/jvm/java-8-openjdk-amd64/man/man1/javap.1.gz

# ls -l /usr/lib/jvm/java-8-openjdk-amd64
```

```bash
# yum list java*jdk-devel
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Available Packages
java-1.6.0-openjdk-devel.x86_64             1:1.6.0.40-1.13.12.6.el6_8              updates
java-1.7.0-openjdk-devel.x86_64             1:1.7.0.111-2.6.7.2.el6_8               updates
java-1.8.0-openjdk-devel.x86_64             1:1.8.0.101-3.b13.el6_8                 updates

# yum install java-1.8.0-openjdk-devel.x86_64
# rpm -qa java*jdk-devel
```



### ORALCE JDK install

1. apt-get install software-properties-common
2. add-apt-repository ppa:webupd8team/java
3. apt-get update
4. apt-get install oracle-java8-installer

{% embed url="https://zetawiki.com/wiki/%EC%9A%B0%EB%B6%84%ED%88%AC\_Java\_8\_%EC%84%A4%EC%B9%98" %}



### setting check

{% code-tabs %}
{% code-tabs-item title=".bashrc" %}
```bash
javac -version

## set path
which javac

## add profile
export JAVA_HOME=/user/bin/
echo $JAVA_HOME

export PATH=$PATH:$JAVA_HOME/bin
echo $PATH

which javac
```
{% endcode-tabs-item %}
{% endcode-tabs %}



#### other JDK

{% code-tabs %}
{% code-tabs-item title="Open jdk" %}
```bash
## config java and enter the number for which JDK to use of your choice.
$ sudo update-alternatives

Second way

List the available JDK's:

$ update-java-alternatives -l
java-1.7.0-openjdk-amd64 1071 /usr/lib/jvm/java-1.7.0-openjdk-amd64
java-1.8.0-openjdk-amd64 1069 /usr/lib/jvm/java-1.8.0-openjdk-amd64

# update-alternatives --config java 
# There is only one alternative in link group java (providing /usr/bin/java): /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java
# 설정할 것이 없습니다.
 
# update-alternatives --config javac
# There is only one alternative in link group javac (providing /usr/bin/javac): /usr/lib/jvm/java-8-openjdk-amd64/bin/javac
# 설정할 것이 없습니다.
```
{% endcode-tabs-item %}
{% endcode-tabs %}



### Compile

```bash
notepad src\com\bit\project\ClassName.java
cd src
javac -d .\..\dest com\bit\project\ClassName.java
cd ..\dest
java com.bit.project.ClassName
```



### Batch

```bash
@ECHO OFF
rem 명령문 출력안함
java com.bit.project.ClassName
rem 프로그램 종료후 멈추기
PAUSE
```













### Reflection

#### **java.lang.Class**

* String getName\(\) : 패키지 + 클래스 이름을 반환한다.
* int getModifiers\(\) : 클래스의 접근 제어자를 숫자로 반환한다.
* Field\[\] getFields\(\) : 접근 가능한 public 필드 목록을 반환한다.
* Field\[\] getDeclaredFields\(\) : 모든 필드 목록을 반환한다.
* Constructor\[\] getConstructors\(\) : 접근 가능한 public 생성자 목록을 반환한다.
* Constructor\[\] getDeclaredConstructors\(\) : 모든 생성자 목록을 반환한다.
* Method\[\] getMethods\(\) : 부모 클래스, 자신 클래스의 접근 가능한 public 메서드 목록을 반환한다.
* Method\[\] getDeclaredMethods\(\) : 모든 메서드 목록을 반환한다.

#### **java.lang.refelct.Constructor**

* String getName\(\) : 생성자 이름을 반환한다.
* int getModifiers\(\) : 생성자의 접근 제어자를 숫자로 반환한다.
* Class\[\] getParameterTypes\(\) : 생성자 패러미터의 데이터 타입을 반환한다.

#### java.lang.refelct.Field

* String getName\(\) : 필드 이름을 반환한다.
* int getModifiers\(\) : 필드의 접근 제어자를 숫자로 반환한다.

#### **java.lang.refelct.Method**

* String getName\(\) : 메서드 이름을 반환한다.
* int getModifiers\(\) : 메서드의 접근 제어자를 숫자로 반환한다.
* Class\[\] getParameterTypes\(\) : 메서드 패러미터의 데이터 타입을 반환한다.


