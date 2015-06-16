<properties 
	pageTitle="Azure에 사용자 지정 Java 웹 앱 업로드" 
	description="이 자습서에서는 Azure 앱 서비스 웹 앱에 사용자 지정 Java 웹 앱을 업로드하는 방법을 보여줍니다." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="robmcm"/>

# Azure에 사용자 지정 Java 웹 앱 업로드

이 항목에서는 [Azure 앱 서비스](http://go.microsoft.com/fwlink/?LinkId=529714) 웹 앱에 사용자 지정 Java 웹 앱을 업로드하는 방법을 설명합니다. 이 항목에는 모든 Java 웹 사이트이나 웹 앱에 적용되는 정보와 특정 응용 프로그램의 일부 예제도 포함되어 있습니다.

Azure에서는 [Azure 앱 서비스에서 Java 웹 앱 만들기](web-sites-java-get-started.md)에서 설명한 대로 Azure 미리보기 포털의 구성 UI 및 Azure 마켓플레이스를 사용하여 Java 웹 앱을 만드는 방법을 제공합니다. 이 자습서는 포털 구성 UI 또는 Azure 마켓플레이스를 사용하지 않는 시나리오용입니다.

## 구성 지침

다음에서는 Azure의 사용자 지정 Java 웹 앱에 필요한 설정을 설명합니다.

- Java 프로세스에서 사용하는 HTTP 포트는 동적으로 할당됩니다. 이 프로세스에서는 환경 변수 `HTTP_PLATFORM_PORT`의 포트를 사용해야 합니다.
- 단일 HTTP 수신기 이외의 모든 수신 포트는 사용하지 않도록 설정해야 합니다. Tomcat에서는 종료, HTTPS 및 AJP 포트가 포함됩니다.
- 컨테이너는 IPv4 트래픽에 대해서만 구성해야 합니다.
- 응용 프로그램의 **startup** 명령은 구성에서 설정해야 합니다.
- 쓰기 권한이 있는 디렉터리가 필요한 응용 프로그램은 Azure 웹 앱의 콘텐츠 디렉터리(**D:\\home**)에 있어야 합니다. 환경 변수 `HOME`은 D:\\home을 참조합니다.  

필요에 따라 환경 변수를 web.config 파일에서 설정할 수 있습니다.

## web.config httpPlatform 구성

다음에서는 web.config 내의 **httpPlatform** 형식을 설명합니다.
                                 
**arguments**(기본값 = ""). **processPath** 설정에서 지정한 실행 파일 또는 스크립트의 인수입니다.

예제(**processPath**도 함께 표시):

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
    arguments="start"
    
    processPath="%JAVA_HOME\bin\java.exe"
    arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"


**processPath** - HTTP 요청을 수신 대기하는 프로세스를 시작할 실행 파일 또는 스크립트의 경로입니다.


예제:


    processPath="%JAVA_HOME%\bin\java.exe"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat"

    processPath="%HOME%\site\wwwroot\bin\tomcat\bin\catalina.bat"
                                                                                       
**rapidFailsPerMinute**(기본값 = 10). **processPath**에 지정된 프로세스에 대해 허용되는 분당 작동 중단 횟수입니다. 이 제한을 초과한 경우 **HttpPlatformHandler**에서 남은 시간(분) 동안 프로세스 시작을 중지합니다.
                                    
**requestTimeout**(기본값 = "00:02:00"). **HttpPlatformHandler**가 `%HTTP_PLATFORM_PORT%`에서 수신 대기하는 프로세스의 응답을 대기하는 기간입니다.

**startupRetryCount**(기본값 = 10). **HttpPlatformHandler**가 **processPath**에 지정된 프로세스 시작을 시도하는 횟수입니다. 자세한 내용은 **startupTimeLimit**를 참조하십시오.

**startupTimeLimit**(기본값 = 10초). 실행 파일/스크립트가 포트에서 수신 대기하는 프로세스를 시작할 때까지 **HttpPlatformHandler**가 대기하는 기간입니다. 이 시간 제한을 초과하는 경우 **HttpPlatformHandler**가 프로세스를 중단하고 프로세스의 시작을 **startupRetryCount**번 다시 시도합니다.
                                                                                      
**stdoutLogEnabled**(기본값 = "true"). true인 경우 **processPath** 설정에 지정된 프로세스의 **stdout** 및 **stderr**가 **stdoutLogFile**(**stdoutLogFile** 섹션 참조)에 지정된 파일로 리디렉션됩니다.
                                    
**stdoutLogFile**(기본값 = "d:\\home\\LogFiles\\httpPlatformStdout.log"). **processPath**에 지정된 프로세스의 **stdout** 및 **stderr**가 로깅될 절대 파일 경로입니다.
                                    
> [AZURE.NOTE]`%HTTP_PLATFORM_PORT%`는 **arguments**의 일부 또는 **httpPlatform** **environmentVariables** 목록의 일부로 지정해야 하는 특수 자리 표시자입니다. 이 자리 표시자는 **HttpPlatformHandler**에서 내부적으로 생성한 포트로 대체되므로 **processPath**에서 지정한 프로세스가 이 포트에서 수신 대기할 수 있습니다.

## 배포

Java 기반 웹 앱은 IIS(인터넷 정보 서비스) 기반 웹 응용 프로그램에서 사용하는 것과 대부분 동일한 방법을 통해 쉽게 배포할 수 있습니다. FTP, Git 및 Kudu는 모두 배포 메커니즘으로 지원되며, 웹 앱의 통합 SCM 기능입니다. WebDeploy는 프로토콜로 작동하지만 Visual Studio에서 Java를 개발하지 않으므로 WebDeploy는 Java 웹 앱 배포 사용 사례로 적합하지 않습니다.

## 응용 프로그램 구성 예제

다음 응용 프로그램에 대해 앱 서비스 웹 앱에서 Java 응용 프로그램을 사용하도록 설정하는 방법을 보여 주는 예제로 web.config 파일 및 응용 프로그램 구성을 제공합니다.

### Tomcat
앱 서비스 웹 앱과 함께 제공되는 Tomcat에는 두 가지 변형이 있지만 Tomcat에서는 계속 고객별 인스턴스를 업로드할 수 있습니다. 다음은 다른 JVM으로 Tomcat을 설치한 예제입니다.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httpPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\bin\tomcat\bin\startup.bat" 
	        arguments="">
	      <environmentVariables>
	        <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	        <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\bin\tomcat" />
	        <environmentVariable name="JRE_HOME" value="%HOME%\site\wwwroot\bin\java" /> <!-- optional, if not specified, this will default to %programfiles%\Java -->
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

Tomcat 쪽에서 변경해야 할 몇 가지 구성이 있습니다. 다음을 설정하도록 server.xml을 편집해야 합니다.

-	종료 포트 = -1
-	HTTP 커넥터 포트 = {port.http}
-	HTTP 커넥터 주소 = "127.0.0.1"
-	HTTPS 및 AJP 커넥터를 주석으로 처리
-	또한 catalina.properties 파일에서 `java.net.preferIPv4Stack=true`를 추가하여 IPv4 설정도 지정할 수 있음
    
Direct3d 호출은 앱 서비스 웹 앱에서 지원되지 않습니다. 이러한 호출을 사용하지 않도록 설정하려면 응용 프로그램에서 해당 호출을 수행하는 다음 Java 옵션을 추가합니다. `-Dsun.java2d.d3d=false`

### Jetty

Tomcat의 경우와 마찬가지로 고객이 고유한 Jetty 인스턴스를 업로드할 수 있습니다. Jetty의 전체 설치를 실행하는 경우 구성은 다음과 유사합니다.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	      <add name="httppPlatformHandler" path="*" verb="*" modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%JAVA_HOME%\bin\java.exe" 
	         arguments="-Djava.net.preferIPv4Stack=true -Djetty.port=%HTTP_PLATFORM_PORT% -Djetty.base=&quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115&quot; -jar &quot;%HOME%\site\wwwroot\bin\jetty-distribution-9.1.0.v20131115\start.jar&quot;"
	        startupTimeLimit="20"
		  startupRetryCount="10"
		  stdoutLogEnabled="true">
	    </httpPlatform>
	  </system.webServer>
	</configuration>

start.ini에서 `java.net.preferIPv4Stack=true`를 설정하도록 Jetty 구성을 변경해야 합니다.

### Hudson

이 테스트에서는 구성을 설정하는 데 Hudson 3.1.2 war 및 기본 Tomcat 7.0.50 인스턴스를 사용하지만 UI는 사용하지 않습니다. Hudson이 소프트웨어 빌드 도구이므로 웹 앱에서 **AlwaysOn** 플래그를 설정할 수 있는 전용 인스턴스에 Hudson을 설치하는 것이 좋습니다.

1. **d:\\home\\site\\wwwroot**와 같이 웹 앱의 루트 디렉토리에서 **webapps** 디렉토리를 만들고(디렉토리가 없는 경우), Hudson.war을 **d:\\home\\site\\wwwroot\\webapps**에 저장합니다.
2. Apache Maven 3.0.5(Hudson과 호환됨)를 다운로드하여 **d:\\home\\site\\wwwroot**에 저장합니다.
3. **d:\\home\\site\\wwwroot**에서 web.config를 만들어 다음 내용을 붙여넣습니다.
	
		<?xml version="1.0" encoding="UTF-8"?>
		<configuration>
		  <system.webServer>
		    <handlers>
		      <add name="httppPlatformHandler" path="*" verb="*" 
		modules="httpPlatformHandler" resourceType="Unspecified" />
		    </handlers>
		    <httpPlatform processPath="%AZURE_TOMCAT7_HOME%\bin\startup.bat"
		startupTimeLimit="20"
		startupRetryCount="10">
		<environmentVariables>
		  <environmentVariable name="HUDSON_HOME" 
		value="%HOME%\site\wwwroot\hudson_home" />
		  <environmentVariable name="JAVA_OPTS" 
		value="-Djava.net.preferIPv4Stack=true -Duser.home=%HOME%/site/wwwroot/user_home -Dhudson.DNSMultiCast.disabled=true" />
		</environmentVariables>            
		    </httpPlatform>
		  </system.webServer>
		</configuration>

    이제 웹 앱을 다시 시작하여 변경 내용을 적용할 수 있습니다. http://yourwebapp/hudson에 연결하여 Hudson을 시작합니다.

4. Hudson에서 자체 구성을 완료하면 다음 화면이 표시됩니다.

    ![Hudson](./media/web-sites-java-custom-upload/hudson1.png)
    
5. Hudson 구성 페이지에 액세스합니다. **Manage Hudson**를 클릭한 다음 **시스템 구성**을 클릭합니다.
6. 아래와 같이 JDK를 구성합니다.

	![Hudson 구성](./media/web-sites-java-custom-upload/hudson2.png)

7. 아래와 같이 Maven을 구성합니다.

	![Maven 구성](./media/web-sites-java-custom-upload/maven.png)

8. 설정을 저장합니다. Hudson을 구성하였으므로 이제 사용할 준비가 되었습니다.

Hudson에 대한 자세한 내용은 [http://hudson-ci.org](http://hudson-ci.org)를 참조하십시오.

### Liferay

Liferay는 앱 서비스 웹 앱에서 지원됩니다. Liferay에 상당한 메모리가 필요할 수 있으므로, 충분한 메모리를 제공할 수 있는 중대형 전용 작업자에서 웹 앱을 실행해야 합니다. 또한 Liferay는 시작하는 데 몇 분 정도 걸릴 수 있습니다. 이런 이유로 웹 앱을 **Always On**으로 설정하는 것이 좋습니다.

Liferay를 다운로드한 후에 Tomcat과 함께 제공되는 Liferay 6.1.2 Community Edition GA3을 사용하여 다음 파일을 편집합니다.

**Server.xml**

- 종료 포트를 -1로 변경합니다.
- HTTP 커넥터를 `<Connector port="${port.http}" protocol="HTTP/1.1" connectionTimeout="600000" address="127.0.0.1" URIEncoding="UTF-8" />`로 변경합니다.
- AJP 커넥터를 주석으로 처리합니다.

**liferay\\tomcat-7.0.40\\webapps\\ROOT\\WEB-INF\\classes** 폴더에서 이름이 **portal-ext.properties**인 파일을 만듭니다. 이 파일에 다음과 같은 줄을 포함해야 합니다.

    liferay.home=%HOME%/site/wwwroot/liferay

tomcat-7.0.40 폴더와 동일한 디렉터리 수준에서 다음 내용을 포함한 **web.config** 파일을 만듭니다.

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <handlers>
	<add name="httpPlatformHandler" path="*" verb="*"
	     modules="httpPlatformHandler" resourceType="Unspecified" />
	    </handlers>
	    <httpPlatform processPath="%HOME%\site\wwwroot\tomcat-7.0.40\bin\catalina.bat" 
	                  arguments="run" 
	                  startupTimeLimit="10" 
	                  requestTimeout="00:10:00" 
	                  stdoutLogEnabled="true">
	      <environmentVariables>
	  <environmentVariable name="CATALINA_OPTS" value="-Dport.http=%HTTP_PLATFORM_PORT%" />
	  <environmentVariable name="CATALINA_HOME" value="%HOME%\site\wwwroot\tomcat-7.0.40" />
	        <environmentVariable name="JRE_HOME" value="D:\Program Files\Java\jdk1.7.0_51" /> 
	        <environmentVariable name="JAVA_OPTS" value="-Djava.net.preferIPv4Stack=true" />
	      </environmentVariables>
	    </httpPlatform>
	  </system.webServer>
	</configuration>

**httpPlatform** 블록 아래에 **requestTimeout**이 “00:10:00”으로 설정되어 있습니다. 해당 시간을 줄일 수 있지만 그러면 Liferay가 부트스트랩하는 동안 시간 제한 오류가 발생할 수 있습니다. 이 값을 변경하면 Tomcat server.xml의 **connectionTimeout**도 수정해야 합니다.

위의 web.config에서는 JRE_HOME 환경 변수가 64비트 JDK를 가리키도록 지정했습니다. 기본값은 32비트지만 Liferay에 상당한 수준의 메모리가 필요할 수 있으므로 64비트 JDK를 사용하는 것이 좋습니다.

이러한 내용을 변경하면 Liferay를 실행하는 웹 앱을 다시 시작한 후 http://yourwebapp를 엽니다. Liferay 포털은 웹 앱 루트에서 사용할 수 있습니다.

Liferay에 대한 자세한 내용은 [http://www.liferay.com](http://www.liferay.com)을 참조하십시오.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!--HONumber=54--> 