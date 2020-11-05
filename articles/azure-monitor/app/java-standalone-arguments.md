---
title: JVM 인수 추가-Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java에 대 한 JVM 인수 추가
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3c9c38d4173413310b715e05c9dcfb9c15be9d4f
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377636"
---
# <a name="adding-the-jvm-arg-for-azure-monitor-application-insights-java"></a>Azure Monitor Application Insights Java에 대 한 JVM 인수 추가



## <a name="azure-environments"></a>Azure 환경

[App Services](../../app-service/configure-language-java.md#set-java-runtime-options)를 구성 합니다.

## <a name="spring-boot"></a>Spring Boot

이전 어딘가에 JVM 인수를 추가 합니다 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar` . 예를 들면 다음과 같습니다.

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Docker 진입점을 통한 스프링 부팅

*Exec* 폼을 사용 하는 경우 매개 변수 앞의 매개 변수 목록에 매개 변수를 추가 합니다 `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` `"-jar"` . 예를 들면 다음과 같습니다.

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

*Shell* 형식을 사용 하는 경우 앞에 JVM 인수를 추가 합니다 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar` . 예를 들면 다음과 같습니다.

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>또는를 통해 설치 된 Tomcat `apt-get``yum`

또는을 통해 Tomcat를 설치한 경우에 `apt-get` `yum` 는 파일이 있어야 `/etc/tomcat8/tomcat8.conf` 합니다.  해당 파일의 끝에 다음 줄을 추가 합니다.

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>다운로드 및 압축을 통해 설치 된 Tomcat

다운로드 및 압축 해제를 통해 Tomcat을 설치한 경우에 [https://tomcat.apache.org](https://tomcat.apache.org) 는 파일이 있어야 `<tomcat>/bin/catalina.sh` 합니다.  다음 콘텐츠를 사용 하 여 라는 동일한 디렉터리에 새 파일을 만듭니다 `<tomcat>/bin/setenv.sh` .

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

파일이 이미 있으면 `<tomcat>/bin/setenv.sh` 해당 파일을 수정 하 고 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 에 추가 `CATALINA_OPTS` 합니다.


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>명령줄에서 Tomcat 실행

파일을 찾습니다 `<tomcat>/bin/catalina.bat` .  다음 콘텐츠를 사용 하 여 라는 동일한 디렉터리에 새 파일을 만듭니다 `<tomcat>/bin/setenv.bat` .

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

따옴표가 필요 하지는 않지만이를 포함 하려는 경우 적절 한 배치는 다음과 같습니다.

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

파일이 이미 있는 경우 `<tomcat>/bin/setenv.bat` 해당 파일을 수정 하 고에 추가 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` 하면 `CATALINA_OPTS` 됩니다.

### <a name="running-tomcat-as-a-windows-service"></a>Windows 서비스로 Tomcat 실행

파일을 찾습니다 `<tomcat>/bin/tomcat8w.exe` .  실행 파일을 실행 하 고 `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `Java Options` 탭 아래의에를 추가 합니다 `Java` .


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>독립 실행형 서버

`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `JAVA_OPTS` 파일 `JBOSS_HOME/bin/standalone.conf` (Linux) 또는 (Windows)에서 기존 환경 변수에를 추가 합니다 `JBOSS_HOME/bin/standalone.conf.bat` .

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>도메인 서버

`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`에서 기존에 추가 `jvm-options` 합니다 `JBOSS_HOME/domain/configuration/host.xml` .

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

단일 호스트에서 여러 관리 되는 서버를 실행 하는 경우 각각에 대해를에 추가 해야 `applicationinsights.agent.id` 합니다 `system-properties` `server` .

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

지정 된 `applicationinsights.agent.id` 값은 고유 해야 합니다. 각 JVM 프로세스에 자체 로컬 applicationinsights.config 구성 및 로컬 applicationinsights.config 로그 파일이 필요 하므로 applicationinsights.config 디렉터리 아래에 하위 디렉터리를 만드는 데 사용 됩니다. 또한 중앙 수집기에 보고 하는 경우에는 `applicationinsights.properties` 여러 관리 되는 서버에서 파일을 공유 하므로 지정 된 `applicationinsights.agent.id` 가 해당 `agent.id` 공유 파일의 설정을 재정의 하는 데 필요 합니다. `applicationinsights.agent.rollup.id``system-properties`관리 되는 서버당 설정을 재정의 해야 하는 경우 서버에 비슷한 방식으로 지정할 수 있습니다 `agent.rollup.id` .


## <a name="jetty-9"></a>Jetty 9

다음 줄을에 추가 합니다. `start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

`-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar`에서 기존에 추가 `jvm-options` 합니다 `glassfish/domains/domain1/config/domain.xml` .

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

## <a name="websphere-8"></a>WebSphere 8

관리 콘솔을 엽니다. **서버 > WebSphere 응용 프로그램 서버 > 응용 프로그램 서버** 로 이동한 후 적절 한 응용 프로그램 서버를 선택 하 고 다음을 클릭 합니다. 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
"일반 JVM 인수"에서 다음을 추가 합니다.
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
그런 다음 응용 프로그램 서버를 저장 하 고 다시 시작 합니다.


## <a name="openliberty-18"></a>OpenLiberty 18

`jvm.options`서버 디렉터리 (예:)에 새 파일을 만들고 다음 `<openliberty>/usr/servers/defaultServer` 줄을 추가 합니다.
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
