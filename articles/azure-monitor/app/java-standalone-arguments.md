---
title: 모든 환경에서 실행되는 Java 응용 프로그램 모니터링 - Azure 모니터 응용 프로그램 인사이트
description: 앱을 계측하지 않고 Java 독립 실행형 에이전트를 사용하여 모든 환경에서 실행되는 Java 응용 프로그램에 대한 응용 프로그램 성능 모니터링. 분산 추적 및 응용 프로그램 맵.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641876"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Azure 모니터 응용 프로그램 인사이트를 위한 JVM args Java 독립 실행형 에이전트 구성



## <a name="azure-environments"></a>Azure 환경

[앱 서비스](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options)구성 .

## <a name="spring-boot"></a>Spring Boot

예를 들어 다음과 같은 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 경우 `-jar <myapp.jar>`JVM arg를 앞에 추가합니다.

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> 후에 `-jar <myapp.jar>` 배치 된 Args는 프로그램 ARGS로 응용 프로그램에 전달됩니다.


## <a name="tomcat-8-linux"></a>톰캣 8 (리눅스)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>톰캣을 통해 `apt-get` 설치하거나`yum`

를 통해 `apt-get` Tomcat을 `yum`설치한 `/etc/tomcat8/tomcat8.conf`경우 .  이 줄을 해당 파일의 끝에 추가합니다.

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>다운로드 및 압축 해제를 통해 설치된 Tomcat

다운로드를 통해 Tomcat를 설치하고 [https://tomcat.apache.org](https://tomcat.apache.org)에서 압축을 해제한 `<tomcat>/bin/catalina.sh`경우 파일이 있어야 합니다.  다음 내용으로 명명된 `<tomcat>/bin/setenv.sh` 동일한 디렉터리에서 새 파일을 만듭니다.

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

파일이 `<tomcat>/bin/setenv.sh` 이미 있는 경우 해당 파일을 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`수정하고 에 추가합니다.


## <a name="tomcat-8-windows"></a>톰캣 8 (윈도우)

### <a name="running-tomcat-from-the-command-line"></a>명령줄에서 Tomcat 실행

파일을 `<tomcat>/bin/catalina.bat`찾습니다.  다음 내용으로 명명된 `<tomcat>/bin/setenv.bat` 동일한 디렉터리에서 새 파일을 만듭니다.

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

따옴표는 필요하지 않지만 따옴표를 포함하려면 적절한 게재 위치가 있습니다.

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

파일이 `<tomcat>/bin/setenv.bat` 이미 있는 경우 해당 파일을 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`수정하고 에 추가하기만 하면 됩니다.

### <a name="running-tomcat-as-a-windows-service"></a>Tomcat을 Windows 서비스로 실행

파일을 `<tomcat>/bin/tomcat8w.exe`찾습니다.  실행 을 실행하고 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `Java Options` `Java` 탭 아래에 추가합니다.


## <a name="jboss-eap-7"></a>J보스 EAP 7

### <a name="standalone-server"></a>독립 실행형 서버

파일 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `JBOSS_HOME/bin/standalone.conf` (리눅스) 또는 `JBOSS_HOME/bin/standalone.conf.bat` (윈도우)의 기존 `JAVA_OPTS` 환경 변수에 추가 :

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>도메인 서버

기존 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `jvm-options` 에 `JBOSS_HOME/domain/configuration/host.xml`추가 :

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

단일 호스트에서 여러 관리 되는 서버를 실행 하는 `applicationinsights.agent.id` 경우 `system-properties` 각 `server`에 추가 해야 합니다.

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

지정된 `applicationinsights.agent.id` 값은 고유해야 합니다. 각 JVM 프로세스에는 고유한 로컬 응용 프로그램 인사이트 구성 및 로컬 응용 프로그램 인사이트 로그 파일이 필요하므로 응용 프로그램 인사이트 디렉터리 에서 하위 디렉터리를 만드는 데 사용됩니다. 또한 중앙 수집기에게 보고하는 `applicationinsights.properties` 경우 여러 관리되는 서버에서 파일을 공유하므로 해당 공유 `agent.id` 파일의 설정을 재정의하려면 지정된 `applicationinsights.agent.id` 파일이 필요합니다. `applicationinsights.agent.rollup.id`관리되는 서버당 `system-properties` `agent.rollup.id` 설정을 재정의해야 하는 경우 서버에서 유사하게 지정할 수 있습니다.


## <a name="jetty-9"></a>부두 9

다음 줄을 다음 줄에 추가합니다.`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>파야라 5

기존 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `jvm-options` 에 `glassfish/domains/domain1/config/domain.xml`추가 :

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>웹스피어 8

오픈 관리 **콘솔은 웹스피어 응용 프로그램 서버 > 응용 프로그램 서버를 > 서버로**이동하여 적절한 응용 프로그램 서버를 선택하고 다음을 클릭합니다. 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
"일반 JVM 인수"에서 다음을 추가합니다.
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
그런 다음 응용 프로그램 서버를 저장하고 다시 시작합니다.


## <a name="openliberty-18"></a>오픈리버티 18

서버 디렉터리(예: `jvm.options` `<openliberty>/usr/servers/defaultServer`)에 새 파일을 만들고 다음 줄을 추가합니다.
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
