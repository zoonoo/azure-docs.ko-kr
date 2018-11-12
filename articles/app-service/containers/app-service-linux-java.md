---
title: Linux 기반의 Azure App Service에 대한 Java 언어 지원 | Microsoft Docs
description: Linux 기반의 Azure App Service를 사용하여 Java 앱을 배포하는 방법을 안내하는 개발자 가이드입니다.
keywords: azure app service, 웹앱, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: a6752f9127a176eef9fd03e7ffddfa7450772def
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037686"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Linux 기반의 App Service에 대한 Java 개발자 가이드

Linux 기반의 Azure App Service는 Java 개발자가 Tomcat 또는 Java SE(Standard Edition) 패키지 웹 응용 프로그램을 신속하게 구축하고, 완벽하게 관리되는 Linux 기반 서비스에 배포하고, 규모를 조정할 수 있게 도와줍니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 응용 프로그램을 배포할 수 있습니다.

이 가이드에서는 Linux용 App Service를 사용하는 Java 개발자를 위해 핵심 개념을 설명하고 지침을 제공합니다. Linux용 Azure App Service를 사용한 경험이 없는 분들은 먼저 [Java 빠른 시작](quickstart-java.md)을 정독해야 합니다. Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 이러한 진단 도구를 사용하는 방법에 대한 자세한 내용은 [Azure App Service 진단 개요](/azure/app-service/app-service-diagnostics)를 참조하세요.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스 

앱을 실행하는 Linux 환경에 대한 SSH 연결을 사용할 수 있습니다. 웹 브라우저 또는 로컬 터미널을 통해 Linux 시스템에 연결하는 방법에 대한 전체 지침은 [Linux 기반의 Azure App Service에 대한 SSH 지원](/azure/app-service/containers/app-service-linux-ssh-support)을 참조하세요.

### <a name="streaming-logs"></a>스트리밍 로그

빠른 디버깅 및 문제 해결을 위해, Azure CLI를 사용하여 콘솔 로그를 스트리밍할 수 있습니다. `az webapp log config` 명령을 사용하여 로깅을 사용하도록 CLI를 구성합니다.

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

그런 다음, `az webapp log tail` 명령을 사용하여 로그를 콘솔로 스트리밍합니다.

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

자세한 내용은 [Azure CLI를 사용하여 로그 스트리밍](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [응용 프로그램 로깅](/azure/app-service/web-sites-enable-diagnostic-log#enablediag)을 사용하도록 설정하여 응용 프로그램의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob 저장소 컨테이너에 출력을 쓰도록 응용 프로그램을 구성합니다.

응용 프로그램에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다. 

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux용 Azure App Service는 기본적으로 Azure Portal 및 CLI를 통해 튜닝 및 사용자 지정이 가능합니다. 비 Java 관련 웹앱 구성에 대한 다음 문서를 검토하세요.

- [App Service 설정 구성](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [사용자 지정 도메인 설정](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [SSL 사용](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [CDN 추가](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

Tomcat 및 Java SE 환경에서 할당된 메모리 또는 기타 JVM 런타임 옵션을 설정하려면 아래와 같이 [응용 프로그램 설정](/azure/app-service/web-sites-configure#app-settings)으로 JAVA_OPTS를 설정합니다. App Service Linux는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **응용 프로그램 설정** 아래에서 `$JAVA_OPTS -Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Azure App Service Linux Maven 플러그 인에서 앱 설정을 구성하려면 Azure 플러그 인 섹션에서 설정/값 태그를 추가합니다. 다음 예제에서는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 응용 프로그램을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1 인스턴스: -Xms1024m -Xmx1024m
- B2 및 S2 인스턴스: -Xms3072m -Xmx3072m
- B3 및 S3 인스턴스: -Xms6144m -Xmx6144m


응용 프로그램 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 응용 프로그램 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

### <a name="turn-on-web-sockets"></a>웹 소켓 켜기

응용 프로그램의 Azure Portal **응용 프로그램 설정**에서 웹 소켓을 켭니다. 설정을 적용하려면 응용 프로그램을 다시 시작해야 합니다.

Azure CLI에서 다음 명령을 사용하여 웹 소켓 지원을 켭니다.

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

그런 다음, 응용 프로그램을 다시 시작합니다.

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>기본 문자 인코딩 설정 

Azure Portal에서, 웹앱의 **응용 프로그램 설정** 아래에 `$JAVA_OPTS -Dfile.encoding=UTF-8` 값을 사용하여 `JAVA_OPTS`이라고 하는 새 앱 설정을 만듭니다.

또는 App Service Maven 플러그 인을 사용하여 앱 설정을 구성할 수 있습니다. 플러그 인 구성에서 설정 이름 및 값 태그를 추가합니다. 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-application"></a>보안 응용 프로그램

Linux용 App Service에서 실행되는 Java 응용 프로그램의 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합은 다른 응용 프로그램과 동일합니다. 

### <a name="authenticate-users"></a>사용자 인증

Azure Portal에서 **인증 및 권한 부여** 옵션을 사용하여 앱 인증을 설정합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다.  자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) 및 기타 ID 공급자 관련 문서를 참조하세요.

여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) 문서의 지침을 따르세요.

 Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)를 사용하여 친숙한 Spring Security 주석 및 API로 응용 프로그램을 보호할 수 있습니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[기존 사용자 지정 SSL 인증서 바인딩](/azure/app-service/app-service-web-tutorial-custom-ssl)의 지침에 따라 기존 SSL 인증서를 업로드하고 응용 프로그램의 도메인 이름에 바인딩합니다. 기본적으로 응용 프로그램에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>데이터 원본에 연결

>[!NOTE]
> 응용 프로그램에서 Spring Framework 또는 Spring Boot를 사용하는 경우 Spring Data JPA에 대한 데이터베이스 연결 정보를 [응용 프로그램 속성 파일]에 환경 변수로 설정할 수 있습니다. 그런 다음, Azure Portal 또는 CLI에서 [앱 설정](/azure/app-service/web-sites-configure#app-settings)을 사용하여 응용 프로그램에 대한 이러한 값을 정의합니다.

이 섹션의 예제 구성 코드 조각은 MySQL 데이터베이스를 사용합니다. 추가 정보는 [MySQL](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-usagenotes-tomcat.html), [SQL Server JDBC](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?view=sql-server-2017) 및 [PostgreSQL](https://jdbc.postgresql.org/documentation/head/index.html)에 대한 구성 문서를 참조하세요.

관리되는 연결을 사용하여 JDBC(Java Database Connectivity) 또는 JPA(Java Persistence API)를 통해 데이터베이스에 연결하도록 Tomcat을 구성하려면 먼저 시작 시 Tomcat이 읽은 CATALINA_OPTS 환경 변수를 사용자 지정합니다. App Service Maven 플러그 인에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

또는 Azure Portal에서 상응하는 App Service 설정을 사용해도 됩니다.

다음으로, 데이터 원본을 한 응용 프로그램에만 제공할 것인지 또는 App Service 계획에서 실행 중인 모든 응용 프로그램에 제공할 것인지 결정합니다.

응용 프로그램 수준 데이터 원본의 경우: 

1. `context.xml` 파일이 없으면 웹 응용 프로그램에 추가하고, 프로젝트가 빌드될 때 WAR 파일의 `META-INF` 디렉터리에 추가합니다.

2. 이 파일에서는 데이터 원본을 JNDI 주소에 연결하는 `Context` 경로 항목을 추가합니다. The

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. 응용 프로그램의 데이터 원본을 사용하도록 응용 프로그램의 `web.xml`을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

공유 서버 수준 리소스의 경우:

1. 아직 구성이 있는 경우 SSH를 사용하여 `/usr/local/tomcat/conf`의 내용을 App Service Linux 인스턴스의 `/home/tomcat`에 복사합니다.

2. 컨텍스트를 `server.xml`에 추가

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. 응용 프로그램의 데이터 원본을 사용하도록 응용 프로그램의 `web.xml`을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 `/home/tomcat/lib` 디렉터리에 배치합니다. 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.  
    1. Azure App Service 웹앱 확장을 설치합니다.

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. 다음 CLI 명령을 실행하여 로컬 시스템에서 App Service로 SSH 터널을 만듭니다.

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 `/home/tomcat/lib` 폴더에 업로드합니다.

5. App Service Linux 응용 프로그램을 다시 시작합니다. Tomcat이 `CATALINA_HOME`을 `/home/tomcat`으로 다시 설정하고, 업데이트된 구성 및 클래스를 사용할 것입니다.

## <a name="docker-containers"></a>Docker 컨테이너

컨테이너에 Azure 지원 Zulu JDK를 사용하려면 [Azure에 지원되는 Azul Zulu Enterprise 다운로드 페이지](https://www.azul.com/downloads/azure-only/zulu/)에 나와 있는 미리 작성된 이미지를 끌어와서 사용하거나 [Microsoft Java GitHub 리포지토리](https://github.com/Microsoft/java/tree/master/docker)의 `Dockerfile` 예제를 사용하세요.

## <a name="runtime-availability-and-statement-of-support"></a>런타임 가용성 및 문 지원

Linux용 App Service는 Java 웹 응용 프로그램의 관리되는 호스팅에 다음과 같은 두 가지 런타임을 지원합니다.

- 웹 보관(WAR) 파일로 패키징된 응용 프로그램을 실행하기 위한 [Tomcat 서블릿 컨테이너](http://tomcat.apache.org/). 지원되는 버전은 8.5 및 9.0입니다.
- Java 보관(JAR) 파일로 패키징된 응용 프로그램을 실행하기 위한 Java SE 런타임 환경. 유일하게 지원되는 주 버전은 Java 8입니다.

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원 

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Linux용 Azure App Service에서 새 런타임 옵션을 통해 제공될 예정입니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

[정규화된 Azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용하여 Azure 또는 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/)용 제품을 개발하는 경우 [Azure에서 지원하는 Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/)에 대한 제품 지원이 제공됩니다.

### <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.
