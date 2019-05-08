## WEB LECTURE

### 

### JavaSocket webserver 구현

{% code-tabs %}
{% code-tabs-item title="SocketServer.java" %}
```java
import java.io.*;
import java.net.*;
import java.util.regex.*;

public class SocketServer extends Thread{

    String notfound = ("HTTP/1.1 404 Not Found\r\n");
	private String root="target/classes/www/";
	private Socket client;

	public SocketServer(Socket client) {
		this.client=client;
	}

	public static void main(String[] args) {
		
		try {
			ServerSocket socket=new ServerSocket(80);
			while(true){
			Socket client = socket.accept();
			
			SocketServer server=new SocketServer(client);
			server.run();
			}
		} catch (IOException e) {
			e.printStackTrace();
		}
		
	}
	
	@Override
	public void run() {
		OutputStream os=null;
		InputStream is=null;
		DataOutputStream dos =null;
		byte[] buf=new byte[1024];
		try {
			os = client.getOutputStream();
			is = client.getInputStream();
			
			InputStreamReader isr = new InputStreamReader(is);
			BufferedReader br=new BufferedReader(isr);
			String req=br.readLine();
			if(req==null)return;
			System.out.print(req+"::");
			Matcher get = Pattern.compile("GET /?(\\S*).*").matcher(req);
			if(get.matches()){
				req=get.group(1);
				System.out.print(req+"::");
				String[] msgs=req.split("\\?");
				System.out.println(msgs[0]);
			dos = new DataOutputStream(os);
			
			
			File file=new File(root+msgs[0]);
			FileInputStream fis = new FileInputStream(file);
			dos.write("HTTP/1.1 200 OK \r\n".getBytes());
			dos.write("Content-Type:text/html;charset=utf-8\r\n".getBytes());
			dos.write("\r\n".getBytes());
			
			int su=fis.read(buf);
			dos.write(buf,0,su);
			}else{
				dos.write(notfound.getBytes());
				dos.write("\r\n".getBytes());
			}
		} catch (Exception e) {
		} finally{
			try {
				is.close();
				os.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
		}
	}

}

```
{% endcode-tabs-item %}
{% endcode-tabs %}



### 

### 

### CGI 연동 자바웹개발

* 아파치 서버 Apache 2.4.38 Win64 download
  * [https://www.apachelounge.com/download/](https://www.apachelounge.com/download/)
* Install cygwin
  * [https://cygwin.com/install.html](https://cygwin.com/install.html)
* Java program in cgi-bin folder
  * {% code-tabs %}
    {% code-tabs-item title="CgiTest.java" %}
    ```java
    public class {
            public static void main(String[] args) {
                    String type = "Content-Type: text/html; charset=utf-8\n\n";
                    String output = "<html><head><meta charset=\"utf-8\"></head>"
                                     +"<body><p>Hello world </p></body></html>";
                    System.out.println(type);
                    System.out.println(output);
            }
    }
    ```
    {% endcode-tabs-item %}
    {% endcode-tabs %}
* In cgi-bin folder and Compile
  * ```bash
    Run javac CgiTest.java
    ```
* Create test.cgi in cgi-bin folder
  * {% code-tabs %}
    {% code-tabs-item title="test.cgi" %}
    ```bash
    #!C:\cygwin64\bin\bash.exe
    java -cp ./ CgiTest

    ```
    {% endcode-tabs-item %}
    {% endcode-tabs %}
* Configure conf/httpd.conf and Allow cgi scripts execution
  * {% code-tabs %}
    {% code-tabs-item title="httpd.conf" %}
    ```bash
    LoadModule cgi_module modules/mod_cgi.so # remove(#)

    <Directory "c:/Apache24/cgi-bin">
    Options +ExecCGI
    AddHandler cgi-script .cgi .pl
    Options FollowSymLinks
    Require all granted
    </Directory>
    AddHandler cgi-script .cgi

    ```
    {% endcode-tabs-item %}
    {% endcode-tabs %}



### 아파치 톰캣 연동\(window os\)

#### 아파치 설치\(window\)

* 공식 사이트에서는 32비트만 다운로드 지원함
  * [http://mirror.apache-kr.org//httpd/binaries/](http://mirror.apache-kr.org//httpd/binaries/)
* Apache 2.4.38 Win64 download
  * [https://www.apachelounge.com/download/](https://www.apachelounge.com/download/)
* mod\_jk & isapi\_redirect.dll download \(추후 톰캣 연동\)
* C:\Apache24\conf\httpd.conf 수정

  {% code-tabs %}
  {% code-tabs-item title="httpd.conf" %}
  ```bash
  Define SRVROOT "c:/Apache24"
  ServerRoot "${SRVROOT}"
  ServerName 127.0.0.1:80         # or # localhost:80
  DocumentRoot "${SRVROOT}/htdocs"
  <Directory "${SRVROOT}/htdocs">
  ```
  {% endcode-tabs-item %}
  {% endcode-tabs %}

* SETX PATH /m "%PATH%;C:\Apache24\bin;"
* httpd -k install
  * httpd -k start
  * httpd -k stop
  * httpd -k restart

#### 톰캣 설치

* [https://tomcat.apache.org/download-80.cgi](https://tomcat.apache.org/download-80.cgi)
* SETX CATALINA\_HOME /m "C:\apache-tomcat-8.5.38"
* SETX PATH /m "%PATH%;%CATALINA\_HOME%\bin;"

#### 환경설정

* Apache 폴더-&gt;conf 폴더-&gt;**mod\_jk.conf**  

  {% code-tabs %}
  {% code-tabs-item title="mod\_jk.conf" %}
  ```bash
    <IfModule mod_jk.c>
        JkWorkersFile "C:/Apache24/conf/workers.properties"
        JkLogFile "C:/apache-tomcat-8.5.38/logs/mod_jk.log"
        JkLogLevel info
        JkAutoAlias "C:/apache-tomcat-8.5.38/webapps"
        JkMount /* ajp13
        JkMount /*.jsp ajp13
        JkMount /servlet/* ajp13
        JkMount /examples/*.jsp ajp13
        JkLogStampFormat "[%a %b %d %H:%M:%S %Y]"
        JkOptions +ForwardKeySize +ForwardURICompat -ForwardDirectories
        JkRequestLogFormat "%w %V %T"
    </IfModule>
  ```
  {% endcode-tabs-item %}
  {% endcode-tabs %}

* apache 폴더 -&gt; conf 폴더 -&gt;  **httpd.conf**  

  {% code-tabs %}
  {% code-tabs-item title="httpd.conf " %}
  ```bash
  # 확인
  # ServerName
  ServerName 127.0.0.1:80
  # port number
  Listen 80
  ~~~
  # 최하단 추가
  LoadModule jk_module modules/mod_jk.so
  Include conf/mod_jk.conf
  ```
  {% endcode-tabs-item %}
  {% endcode-tabs %}

* apache 폴더 -&gt; conf 폴더 -&gt;  **workers.properties** 

  {% code-tabs %}
  {% code-tabs-item title="workers.properties" %}
  ```bash
  workers.tomcat_home="/apache-tomcat-8.5.38"
  workers.java_home="/Program Files/Java/jdk1.8.0_152"
  ps=/
  worker.list=ajp13
  worker.ajp13.port=8009
  worker.ajp13.host=localhost
  worker.ajp13.type=ajp13
  ```
  {% endcode-tabs-item %}
  {% endcode-tabs %}



### ubuntu

#### install apache

```text
apache2 -v
sudo netstat -ntlp | grep apache2
sudo ufw allow 80/tcp
sudo /etc/init.d/apache2 start
sudo service apache2 start
```

#### apache & tomcat 연동

```bash
sudo apt-get install libapache2-mod-jk
sudo apt autoremove

sudo nano /etc/apache2/workers.properties
sudo nano /etc/apache2/mods-available/jk.conf
sudo nano /etc/apache2/sites-available/000-default.conf
sudo nano /etc/tomcat8/server.xml


```

* vi /etc/apache2/workers.properties

```perl

workers.tomcat_home=/usr/share/tomcat8     
workers.java_home=/usr/lib/jvm/java-8-openjdk-amd64 

ps=/
worker.list=ajp13
## worker.list= tomcat1, tomcat2 # 다중연동시
###############
worker.ajp13.port=8009
worker.ajp13.host=localhost
worker.ajp13.type=ajp13
worker.ajp13.lbfactor = 20

```

* vi /etc/apache2/mods-available/jk.conf

```text
<IfModule jk_module>

    # We need a workers file exactly once
    # and in the global server
    ###### 아래로 수정 #####
    #JkWorkersFile /etc/libapache2-mod-jk/workers.properties
    JkWorkersFile /etc/apache2/workers.properties

```

* vi /etc/apache2/sites-available/000-default.conf

```text
ServerAdmin webmaster@localhost
#DocumentRoot /var/www/html
DocumentRoot /var/lib/tomcat8/webapps/ROOT/
JkMount /* ajp13
```

```text
##### muti ####
SetEnvIf Request_URI "/*.js$" no-jk 
JkMount /* tomcat1
```

* vi /etc/tomcat8/server.xml

```text
port=8009 주석해제
```







### tomcat  use HTTPS

* create key file \(keystore\)
* ```bash
  ::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
  ::
  ::Windows : "%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keypass "설정할 패스워드" -storepass "설정할 패스워드" -keyalg RSA -keystore "저장될 keystore파일의 위치 및 파일명" -dname "CN="사용하는 URL 입력", OU=OrgUnit, O=MyCompany, C=KR"
  ::
  ::Unix : $JAVA_HOME/bin/keytool -genkey -alias tomcat -keypass "설정할 패스워드" -storepass "설정할 패스워드" -keyalg RSA -keystore "저장될 keystore파일의 위치 및 파일명" -dname "CN="사용하는 URL 입력", OU=OrgUnit, O=MyCompany, C=KR"
  ::
  ::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::

  >"%JAVA_HOME%\bin\keytool" -genkey -alias tomcat -keypass "123456" -storepass "123456" -keyalg RSA -keystore "c:/web/key.keystore" -dname
   "CN="localhost", OU=OrgUnit, O=MyCompany, C=KR"

  Warning:
  JKS 키 저장소는 고유 형식을 사용합니다. "keytool -importkeystore -srckeystore c:/web/key.keystore -destkeystore c:/web/key.keystore -deststoretype pkc
  s12"를 사용하는 산업 표준 형식인 PKCS12로 이전하는 것이 좋습니다.

  >
  ```
* tomcat setting\(in **conf** directory\)
* {% code-tabs %}
  {% code-tabs-item title="server.xml" %}
  ```markup
  <Connector port="443" protocol="HTTP/1.1" SSLEnabled="true"
  		maxThreads="150" scheme="https" secure="true"
  		clientAuth="false" sslProtocol="TLS" keystoreFile="/web/key.keystore"
  		keystorePass="123456" />
  ```
  {% endcode-tabs-item %}
  {% endcode-tabs %}
* tomcat restart
* https://localhost
* 고급 -&gt; 안전하지 않은 페이지로 이동





### 응답코드

Response Code 에 따른 응답코드

|  **Response Class Code**  |  **Response Class 의미** |  **설명** |
| :--- | :--- | :--- |
|  1 |  Informational \(정보\) |  리퀘스트를 받고, 처리 중에 있음. |
|  2 |  Success \(성공\) |  리퀘스트를 정상적으로 처리함. |
|  3 |  Redirection \(리디렉션\) |  리퀘스트 완료를 위해 추가 동작이 필요함. |
|  4 |  Client Error \(클라이언트 오류\) |  클라이언트 요청을 처리할 수 없어 오류 발생 |
|  5 |  Server Error \(서버 오류\) |  서버에서 처리를 하지 못하여 오류 발생 |

<table>
  <thead>
    <tr>
      <th style="text-align:left">
        <p>&#xBC88;&#xD638;</p>
        <p>&#xB300;&#xC5ED;</p>
      </th>
      <th style="text-align:left">&#xBC88;&#xD638;</th>
      <th style="text-align:left">&#xC601;&#xC5B4;&#xC124;&#xBA85;</th>
      <th style="text-align:left">&#xD55C;&#xAD6D;&#xC5B4;&#xC124;&#xBA85;</th>
      <th style="text-align:left">&#xBE44;&#xACE0;</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">1XX</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">&#xC815;&#xBCF4;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">100</td>
      <td style="text-align:left">Continue</td>
      <td style="text-align:left">&#xACC4;&#xC18D;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">101</td>
      <td style="text-align:left">Switching Protocols</td>
      <td style="text-align:left">&#xD504;&#xB85C;&#xD1A0;&#xCF5C; &#xC804;&#xD658;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">2XX</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">&#xC131;&#xACF5;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">200</td>
      <td style="text-align:left">OK</td>
      <td style="text-align:left">&#xC624;&#xB958;&#xC5C6;&#xC774; &#xC804;&#xC1A1; &#xC131;&#xACF5;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">204</td>
      <td style="text-align:left">No Content</td>
      <td style="text-align:left">&#xBB38;&#xC11C;&#xC5D0; &#xB0B4;&#xC6A9;&#xC774; &#xC5C6;&#xC74C;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">206</td>
      <td style="text-align:left">Partial Content</td>
      <td style="text-align:left">&#xC77C;&#xBD80;&#xBD84;</td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/Wget_%EC%9D%B4%EC%96%B4%EB%B0%9B%EA%B8%B0">wget &#xC774;&#xC5B4;&#xBC1B;&#xAE30;</a> 
      </td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">3XX &#xB9AC;&#xB2E4;&#xC774;&#xB809;&#xD2B8;</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">301</td>
      <td style="text-align:left">Moved Permanently</td>
      <td style="text-align:left">&#xC601;&#xAD6C; &#xC774;&#xB3D9;&#xB428;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">302</td>
      <td style="text-align:left">Found</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">4XX</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">&#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8; &#xC624;&#xB958;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/400_Bad_Request">400</a>
      </td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/400_Bad_Request">Bad Request</a>
      </td>
      <td style="text-align:left">&#xC11C;&#xBC84;&#xAC00; &#xC774;&#xD574;&#xD560; &#xC218; &#xC5C6;&#xB294;
        &#xC694;&#xCCAD;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">401</td>
      <td style="text-align:left">Unauthorized</td>
      <td style="text-align:left">&#xAD8C;&#xD55C; &#xC5C6;&#xC74C;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">403</td>
      <td style="text-align:left">Forbidden</td>
      <td style="text-align:left">&#xC811;&#xADFC;&#xAE08;&#xC9C0;(&#xAD8C;&#xD55C; &#xC5C6;&#xC74C;)</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/404">404</a> 
      </td>
      <td style="text-align:left">Not Found</td>
      <td style="text-align:left">&#xC694;&#xCCAD;&#xD55C; &#xD398;&#xC774;&#xC9C0;&#xAC00; &#xC5C6;&#xC74C;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">408</td>
      <td style="text-align:left">Request Timeout</td>
      <td style="text-align:left">&#xC694;&#xCCAD; &#xD0C0;&#xC784;&#xC544;&#xC6C3;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/414">414</a>
      </td>
      <td style="text-align:left">URI Too Long</td>
      <td style="text-align:left">URI&#xAC00; &#xB108;&#xBB34; &#xAE3A;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">415</td>
      <td style="text-align:left">Unsupported Media Type</td>
      <td style="text-align:left">&#xC9C0;&#xC6D0;&#xD558;&#xC9C0; &#xC54A;&#xB294; &#xBBF8;&#xB514;&#xC5B4;
        &#xD0C0;&#xC785;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left"><a href="https://zetawiki.com/wiki/451">451</a>
      </td>
      <td style="text-align:left">Unavailable For Legal Reasons</td>
      <td style="text-align:left">&#xBC95;&#xC801; &#xC774;&#xC720;&#xB85C; &#xC774;&#xC6A9;&#xBD88;&#xAC00;</td>
      <td
      style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left">5XX</td>
      <td style="text-align:left"></td>
      <td style="text-align:left"></td>
      <td style="text-align:left">&#xC11C;&#xBC84; &#xC624;&#xB958;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">500</td>
      <td style="text-align:left">Internal Server Error</td>
      <td style="text-align:left">&#xC11C;&#xBC84; &#xB0B4;&#xBD80; &#xC624;&#xB958;</td>
      <td style="text-align:left"></td>
    </tr>
    <tr>
      <td style="text-align:left"></td>
      <td style="text-align:left">503</td>
      <td style="text-align:left">Service Unavailable</td>
      <td style="text-align:left">&#xC11C;&#xBE44;&#xC2A4; &#xC0AC;&#xC6A9;&#xBD88;&#xAC00;</td>
      <td style="text-align:left"></td>
    </tr>
  </tbody>
</table>  
**200 번대 응답\(Response\) : 성공\(Success\)**

<table>
  <thead>
    <tr>
      <th style="text-align:left">200</th>
      <th style="text-align:left">OK</th>
      <th style="text-align:left">* &#xC694;&#xCCAD; &#xC815;&#xC0C1; &#xCC98;&#xB9AC;.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">204</td>
      <td style="text-align:left">No Content</td>
      <td style="text-align:left">
        <p>* &#xC694;&#xCCAD; &#xC815;&#xC0C1; &#xCC98;&#xB9AC;&#xD558;&#xC600;&#xC9C0;&#xB9CC;,
          &#xB3CC;&#xB824;&#xC904; &#xB9AC;&#xC18C;&#xC2A4; &#xC5C6;&#xC74C;.</p>
        <p>* &#xC751;&#xB2F5;&#xC5D0; &#xC5B4;&#xB5A0;&#xD55C; &#xC5D4;&#xD2F0;&#xD2F0;
          &#xBC14;&#xB514;(Entity Body)&#xB3C4; &#xD3EC;&#xD568;&#xD558;&#xC9C0;
          &#xC54A;&#xC74C;.</p>
        <p>* &#xC11C;&#xBC84;&#xC5D0;&#xC11C; &#xCC98;&#xB9AC; &#xD6C4;, &#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8;&#xC5D0;
          &#xC815;&#xBCF4;&#xB97C; &#xBCF4;&#xB0BC; &#xD544;&#xC694;&#xAC00; &#xC5C6;&#xB294;
          &#xACBD;&#xC6B0; &#xC0AC;&#xC6A9;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">206</td>
      <td style="text-align:left">Partial Content</td>
      <td style="text-align:left">* Range&#xAC00; &#xC9C0;&#xC815;&#xB41C; &#xC694;&#xCCAD;&#xC778; &#xACBD;&#xC6B0;,
        &#xC9C0;&#xC815;&#xB41C; &#xBC94;&#xC704;&#xB9CC;&#xD07C;&#xC758; &#xC694;&#xCCAD;&#xC744;
        &#xBC1B;&#xC558;&#xB2E4;&#xB294; &#xAC83;&#xC744; &#xC54C;&#xB824;&#xC90C;.</td>
    </tr>
  </tbody>
</table>  
  
**300 번대 응답\(Response\) : 리디렉션\(Redirection\)**

<table>
  <thead>
    <tr>
      <th style="text-align:left">301</th>
      <th style="text-align:left">Moved Permanently</th>
      <th style="text-align:left">* &#xC694;&#xCCAD;&#xB41C; &#xB9AC;&#xC18C;&#xC2A4;&#xC5D0;&#xB294; &#xC0C8;&#xB85C;&#xC6B4;
        URI&#xAC00; &#xC9C0;&#xC815;&#xB418;&#xC5B4; &#xC788;&#xAE30; &#xB54C;&#xBB38;&#xC5D0;,
        &#xC774;&#xD6C4;&#xB85C;&#xB294; &#xC0C8; URI&#xB97C; &#xC0AC;&#xC6A9;&#xD574;&#xC57C;
        &#xD55C;&#xB2E4;&#xB294; &#xAC83;&#xC744; &#xB098;&#xD0C0;&#xB0C4;. (&#xC601;&#xAD6C;&#xC801;&#xC778;
        URI &#xBCC0;&#xACBD;)</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">302</td>
      <td style="text-align:left">Found</td>
      <td style="text-align:left">* &#xC694;&#xCCAD;&#xB41C; &#xB9AC;&#xC18C;&#xC2A4;&#xC5D0;&#xB294; &#xC0C8;&#xB85C;&#xC6B4;
        URI&#xAC00; &#xC9C0;&#xC815;&#xB418;&#xC5B4; &#xC788;&#xAE30; &#xB54C;&#xBB38;&#xC5D0;,
        &#xC774;&#xD6C4;&#xB85C;&#xB294; &#xC0C8; URI&#xB97C; &#xC0AC;&#xC6A9;&#xD574;&#xC57C;
        &#xD55C; &#xB2E4;&#xB294; &#xAC83;&#xC744; &#xB098;&#xD0C0;&#xB0C4;. 301&#xACFC;
        &#xC720;&#xC0AC;&#xD558;&#xC9C0;&#xB9CC;, 302&#xB294; &#xC77C;&#xC2DC;&#xC801;&#xC778;
        URI &#xC774;&#xB3D9;)</td>
    </tr>
    <tr>
      <td style="text-align:left">303</td>
      <td style="text-align:left">See Other</td>
      <td style="text-align:left">* &#xC774; &#xC751;&#xB2F5;&#xC740; &#xC694;&#xCCAD;&#xC5D0; &#xB300;&#xD55C;
        &#xB9AC;&#xC18C;&#xC2A4;&#xB294; &#xB2E4;&#xB978; URI&#xC5D0; &#xC788;&#xAE30;
        &#xB54C;&#xBB38;&#xC5D0; GET &#xBA54;&#xC11C;&#xB4DC;&#xB97C; &#xC0AC;&#xC6A9;&#xD574;&#xC11C;
        &#xC5BB;&#xC5B4;&#xC57C; &#xD55C;&#xB2E4;&#xB294; &#xAC83;&#xC744; &#xB098;&#xD0C0;&#xB0C4;.
        302 &#xCF54;&#xB4DC;&#xC640; &#xAC19;&#xC9C0;&#xB9CC;, 303&#xC740; &#xB9AC;&#xB514;&#xB809;&#xC158;
        &#xC704;&#xCE58;&#xB97C; GET &#xBA54;&#xC11C;&#xB4DC;&#xB97C; &#xD1B5;&#xD574;
        &#xC5BB;&#xC5B4;&#xC57C; &#xD55C;&#xB2E4;&#xACE0; &#xBA85;&#xD655;&#xD558;&#xAC8C;
        &#xB418;&#xC5B4; &#xC788;&#xC74C;.</td>
    </tr>
    <tr>
      <td style="text-align:left">304</td>
      <td style="text-align:left">Not Modified</td>
      <td style="text-align:left">
        <p>* &#xC694;&#xCCAD;&#xD55C; &#xB9AC;&#xC18C;&#xC2A4;&#xAC00; &#xB9C8;&#xC9C0;&#xB9C9;
          &#xC694;&#xCCAD; &#xC774;&#xD6C4; &#xBCC0;&#xACBD;&#xB41C; &#xC801;&#xC774;
          &#xC5C6;&#xAE30; &#xB54C;&#xBB38;&#xC5D0; &#xAE30;&#xC874; &#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8;&#xC758;
          &#xB85C;&#xCEEC; &#xCE90;&#xC2DC; &#xB9AC;&#xC18C;&#xC2A4;&#xB97C; &#xC0AC;&#xC6A9;&#xD558;&#xB3C4;&#xB85D;
          &#xC54C;&#xB824;&#xC90C;.</p>
        <p>300&#xBC88;&#xB300;&#xB85C; &#xBD84;&#xB958;&#xB418;&#xC5B4; &#xC788;&#xC9C0;&#xB9CC;,
          &#xB9AC;&#xB514;&#xB809;&#xC158;&#xACFC;&#xB294; &#xAD00;&#xACC4;&#xC5C6;&#xB294;
          &#xCC98;&#xB9AC;&#xB97C; &#xD568;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">307</td>
      <td style="text-align:left">Temporary Redirect</td>
      <td style="text-align:left">* &#xC784;&#xC2DC;&#xB85C; &#xD398;&#xC774;&#xC9C0;&#xB97C; &#xB9AC;&#xB2E4;&#xC774;&#xB809;&#xD2B8;
        &#xD568;.</td>
    </tr>
  </tbody>
</table>  
  
 **400 번대 응답\(Response\) : 클라이언트 에러 \(Client Error\)**

<table>
  <thead>
    <tr>
      <th style="text-align:left">400</th>
      <th style="text-align:left">Bad Request</th>
      <th style="text-align:left">
        <p>* &#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8;&#xC758; &#xC694;&#xCCAD; &#xAD6C;&#xBB38;&#xC774;
          &#xC798;&#xBABB;&#xB428;.</p>
        <p>* &#xBE0C;&#xB77C;&#xC6B0;&#xC800;&#xB294; &#xC774; &#xC751;&#xB2F5;&#xC744;
          200 OK &#xC751;&#xB2F5;&#xACFC; &#xB3D9;&#xC77C;&#xD55C; &#xD615;&#xD0DC;&#xB85C;
          &#xCDE8;&#xAE09;&#xD568;.</p>
      </th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">401</td>
      <td style="text-align:left">Unauthorized</td>
      <td style="text-align:left">
        <p>* &#xC694;&#xCCAD; &#xCC98;&#xB9AC;&#xB97C; &#xC704;&#xD574; HTTP &#xC778;&#xC99D;(BASIC
          &#xC778;&#xC99D;, DIGEST &#xC778;&#xC99D;) &#xC815;&#xBCF4;&#xAC00; &#xD544;&#xC694;&#xD568;&#xC744;
          &#xC54C;&#xB824;&#xC90C;.</p>
        <p>&#xC811;&#xADFC; &#xD5C8;&#xC6A9;&#xC744; &#xCC28;&#xB2E8;&#xD568;. &#xCD5C;&#xCD08;
          &#xC694;&#xCCAD;&#xC5D0;&#xB294; &#xC778;&#xC99D; &#xB2E4;&#xC774;&#xC5BC;&#xB85C;&#xADF8;
          &#xD45C;&#xC2DC;&#xD558;&#xACE0;, &#xB450;&#xBC88;&#xC9F8;&#xB294; &#xC778;&#xC99D;
          &#xC2E4;&#xD328; &#xC751;&#xB2F5;&#xC744; &#xBCF4;&#xB0C4;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">403</td>
      <td style="text-align:left">Forbidden</td>
      <td style="text-align:left">
        <p>* &#xC811;&#xADFC; &#xAE08;&#xC9C0; &#xC751;&#xB2F5;. Directory Listing
          &#xC694;&#xCCAD;(&#xC11C;&#xBC84; &#xD30C;&#xC77C; &#xB514;&#xB809;&#xD1A0;&#xB9AC;
          &#xBAA9;&#xB85D; &#xD45C;&#xC2DC;) &#xBC0F; &#xAD00;&#xB9AC;&#xC790; &#xD398;&#xC774;&#xC9C0;
          &#xC811;&#xADFC; &#xB4F1;&#xC744; &#xCC28;&#xB2E8;&#xD558;&#xB294; &#xACBD;&#xC6B0;&#xC758;
          &#xC751;&#xB2F5;. (&#xD30C;&#xC77C; &#xC2DC;&#xC2A4;&#xD15C; &#xD37C;&#xBBF8;&#xC158;
          &#xAC70;&#xBD80;, &#xD5C8;&#xAC00; &#xB418;&#xC9C0; &#xC54A;&#xC740; IP
          &#xC8FC;&#xC18C;&#xB97C; &#xD1B5;&#xD55C; &#xC561;&#xC138;&#xC2A4;&#xC758;
          &#xAC70;&#xBD80; &#xB4F1;)</p>
        <p>* &#xC11C;&#xBC84;&#xB294; &#xC5D4;&#xD2F0;&#xD2F0; &#xBC14;&#xB514;&#xC5D0;
          &#xC811;&#xADFC; &#xAC70;&#xBD80;&#xC5D0; &#xB300;&#xD55C; &#xC774;&#xC720;&#xB97C;
          &#xBA85;&#xC2DC;&#xD558;&#xC5EC; &#xBCF4;&#xB0BC; &#xC218; &#xC788;&#xC74C;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">404</td>
      <td style="text-align:left">Not Found</td>
      <td style="text-align:left">* &#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8;&#xAC00; &#xC694;&#xCCAD;&#xD55C;
        &#xB9AC;&#xC18C;&#xC2A4;&#xAC00; &#xC11C;&#xBC84;&#xC5D0; &#xC5C6;&#xC74C;</td>
    </tr>
    <tr>
      <td style="text-align:left">405</td>
      <td style="text-align:left">Mothod Not Allowed</td>
      <td style="text-align:left">* &#xD5C8;&#xC6A9;&#xB418;&#xC9C0; &#xC54A;&#xB294; HTTP &#xBA54;&#xC11C;&#xB4DC;&#xB97C;
        &#xC0AC;&#xC6A9;&#xD568;.</td>
    </tr>
  </tbody>
</table>  
  
**500 번대 응답\(Response\) : 서버 에러 \(Server Error\)**

<table>
  <thead>
    <tr>
      <th style="text-align:left">500</th>
      <th style="text-align:left">Internal Server Error</th>
      <th style="text-align:left">* &#xC11C;&#xBC84;&#xC5D0;&#xC11C; &#xD074;&#xB77C;&#xC774;&#xC5B8;&#xD2B8;
        &#xC694;&#xCCAD;&#xC744; &#xCC98;&#xB9AC; &#xC911;&#xC5D0; &#xC5D0;&#xB7EC;&#xAC00;
        &#xBC1C;&#xC0DD;&#xD568;.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left">503</td>
      <td style="text-align:left">Service Unavailable</td>
      <td style="text-align:left">
        <p>* &#xC11C;&#xBC84;&#xAC00; &#xC77C;&#xC2DC;&#xC801;&#xC73C;&#xB85C; &#xC694;&#xCCAD;&#xC744;
          &#xCC98;&#xB9AC;&#xD560; &#xC218; &#xC5C6;&#xC74C;.</p>
        <p>* &#xC11C;&#xBC84;&#xAC00; &#xACFC;&#xBD80;&#xD558; &#xC0C1;&#xD0DC;&#xC774;&#xAC70;&#xB098;
          &#xC810;&#xAC80;&#xC911;&#xC774;&#xBBC0;&#xB85C; &#xC694;&#xCCAD;&#xC744;
          &#xCC98;&#xB9AC;&#xD560; &#xC218; &#xC5C6;&#xC74C;&#xC744; &#xC54C;&#xB824;&#xC90C;.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left">504</td>
      <td style="text-align:left">Gateway Timeout</td>
      <td style="text-align:left">* &#xC11C;&#xBC84;&#xB97C; &#xD1B5;&#xD558;&#xB294; &#xAC8C;&#xC774;&#xD2B8;&#xC6E8;&#xC774;&#xC5D0;
        &#xBB38;&#xC81C;&#xAC00; &#xBC1C;&#xC0DD;&#xD558;&#xC5EC; &#xC2DC;&#xAC04;&#xC774;
        &#xCD08;&#xACFC;&#xB428;.</td>
    </tr>
    <tr>
      <td style="text-align:left">505</td>
      <td style="text-align:left">HTTP Version Not Supported</td>
      <td style="text-align:left">* &#xD574;&#xB2F9; HTTP &#xBC84;&#xC804;&#xC5D0;&#xC11C;&#xB294; &#xC9C0;&#xC6D0;&#xB418;&#xC9C0;
        &#xC54A;&#xB294; &#xC694;&#xCCAD;&#xC784;&#xC744; &#xC54C;&#xB824;&#xC90C;.</td>
    </tr>
  </tbody>
</table>  
  
출처: [https://ko.wikipedia.org/wiki/HTTP\_%EC%83%81%ED%83%9C\_%EC%BD%94%EB%93%9C](https://ko.wikipedia.org/wiki/HTTP_%EC%83%81%ED%83%9C_%EC%BD%94%EB%93%9C)





### 버전별 web.xml 스키마 

* [ ] 2.5

{% code-tabs %}
{% code-tabs-item title="web.xml" %}
```markup
<?xml version="1.0" encoding="UTF-8"?> 
<web-app xmlns="http://java.sun.com/xml/ns/javaee" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:web="http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5"> 

</web-app> 


```
{% endcode-tabs-item %}
{% endcode-tabs %}

* [ ] 3.x

{% code-tabs %}
{% code-tabs-item title="web.xml" %}
```markup
<?xml version="1.0" encoding="UTF-8"?> 
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd" id="WebApp_ID" version="3.0"> 

</web-app>

```
{% endcode-tabs-item %}
{% endcode-tabs %}



### port check

```text
netstat -ano
  - ESTABLISHED : 현재 연결되어 있다는 뜻 
  - LISTENING : 연결을 위하여 접속을 기다리는 상태 
  - TIME_WAIT : 이미 해당 사이트와 연결이 종료되었거나 다음 연결을 위해 기다리는 상태 
  - SYN_SENT : 접속하기 위해 패킷을 보냈다는 뜻

taskkill /pid 0000 (taskkill -f /pid 0000)

```



### Put Parameter

* call

```text
curl  -X PUT ^
      --data "name=value" ^
      --header "Content-Type: text/plain" http://localhost:8080/context/path
```

* result

```text
protected void doPut(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
		
		BufferedReader br = new BufferedReader(new InputStreamReader(req.getInputStream()));

		String data = br.readLine();
}
```





### 파비콘\(favicon\) 설정

프로젝트 루트 디렉토리에 16x16 과 32x32의 이미지를 하나에 품은\(multiple sizes\) 혹은 단일 사이즈의 favicon.ico 파일을 업로드

```markup
<!-- 기본방법 -->
<head> 
<link rel="shortcut icon" href="favicon.ico">

</head>
<!-- 모바일,피씨 호환 -->
<head> 
<link rel="apple-touch-icon" sizes="180x180" href="/apple-touch-icon.png">
<link rel="icon" type="image/png" sizes="32x32" href="/favicon-32x32.png">
<link rel="icon" type="image/png" sizes="16x16" href="/favicon-16x16.png">
<link rel="manifest" href="/manifest.json">
<meta name="theme-color" content="#ffffff">

</head>

```











