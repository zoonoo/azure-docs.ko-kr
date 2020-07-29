---
title: Windows Java 앱 구성
description: Azure App Service의 Windows VM 인스턴스에서 실행 되도록 Java 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
keywords: azure app service, 웹 앱, windows, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
ms.openlocfilehash: d0850b2192df1392a245f27588ffcb8ddb337347
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87319340"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Azure App Service에 대 한 Windows Java 앱 구성

Azure App Service를 통해 Java 개발자는 완전히 관리 되는 Windows 기반 서비스에서 Tomcat 웹 응용 프로그램을 신속 하 게 빌드, 배포 및 확장할 수 있습니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드에서는 App Service에서를 사용 하는 Java 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [Java 빠른](app-service-web-get-started-java.md) 시작을 참조 하세요. Java 개발과 관련 되지 않은 App Service 사용에 대 한 일반적인 질문에 대 한 답변은 [App Service WINDOWS FAQ](faq-configuration-and-management.md)에서 확인할 수 있습니다.

## <a name="deploying-your-app"></a>앱 배포

[Maven 용 Azure 웹 앱 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 을 사용 하 여 war 파일을 배포할 수 있습니다. 또한 배포 시 많이 사용되는 IDE를 사용하면 [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 또는 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)에서 지원됩니다.

그렇지 않으면 배포 방법이 보관 형식에 따라 달라집니다.

- .war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)를 참조하세요.
- Kudu 사이트의 끝점을 사용 하 여 jar 파일을 Java SE에 배포 합니다 `/api/zipdeploy/` . 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)를 참조하세요.

FTP를 사용 하 여 war을 배포 하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure App Service 진단 개요](overview-diagnostics.md)를 참조하세요.

### <a name="use-flight-recorder"></a>비행 레코더 사용

Azul Jvm를 사용 하는 App Service에 대 한 모든 Java 런타임은 줄루어 비행 레코더와 함께 제공 됩니다. 이를 사용 하 여 JVM, 시스템 및 Java 수준 이벤트를 기록 하 여 동작을 모니터링 하 고 Java 응용 프로그램의 문제를 해결할 수 있습니다.

시간이 지정 된 기록을 수행 하려면 Java 응용 프로그램의 PID (프로세스 ID)가 필요 합니다. PID를 찾으려면 https://의 웹 앱 SCM 사이트로 브라우저를 엽니다. scm.azurewebsites.net/ProcessExplorer/>. <. 이 페이지에는 웹 앱에서 실행 중인 프로세스가 표시 됩니다. 테이블에서 "java" 라는 프로세스를 찾고 해당 PID (프로세스 ID)를 복사 합니다.

다음으로, SCM 사이트의 상단 도구 모음에서 **디버그 콘솔** 를 열고 다음 명령을 실행 합니다. `<pid>`을 이전에 복사한 프로세스 ID로 바꿉니다. 이 명령은 Java 응용 프로그램의 30 초 profiler 기록을 시작 하 고 디렉터리에 이라는 파일을 생성 합니다 `timed_recording_example.jfr` `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

자세한 내용은 [Jcmd 명령 참조](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)를 참조하세요.

#### <a name="analyze-jfr-files"></a>`.jfr`파일 분석

[FTPS](deploy-ftp.md)를 사용하여 JFR 파일을 로컬 컴퓨터에 다운로드합니다. JFR 파일을 분석하려면 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)을 다운로드하여 설치합니다. Zulu Mission Control에 대한 지침은 [Azul 설명서](https://docs.azul.com/zmc/) 및 [설치 지침](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)을 참조하세요.

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

자세한 내용은 [Cloud Shell에서 로그 스트리밍](troubleshoot-diagnostic-logs.md#in-cloud-shell)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.


## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Azure App Service은 Azure Portal 및 CLI를 통해 기본 튜닝 및 사용자 지정을 지원 합니다. 비 Java 관련 웹앱 구성에 대한 다음 문서를 검토하세요.

- [앱 설정 구성](configure-common.md#configure-app-settings)
- [사용자 지정 도메인 설정](app-service-web-tutorial-custom-domain.md)
- [TLS 바인딩 구성](configure-ssl-bindings.md)
- [CDN 추가](../cdn/cdn-add-to-web-app.md)
- [Kudu 사이트 구성](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

할당 된 메모리 또는 기타 JVM 런타임 옵션을 설정 하려면 옵션으로 이라는 [앱 설정을](configure-common.md#configure-app-settings) 만듭니다 `JAVA_OPTS` . App Service는이 설정을 시작 시 Java 런타임으로이를 환경 변수로 전달 합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `-Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Maven 플러그 인에서 앱 설정을 구성하려면 Azure 플러그 인 섹션에서 설정/값 태그를 추가합니다. 다음 예에서는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 애플리케이션을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1 인스턴스: `-Xms1024m -Xmx1024m`
- B2 및 S2 인스턴스: `-Xms3072m -Xmx3072m`
- B3 및 S3 인스턴스: `-Xms6144m -Xmx6144m`

애플리케이션 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 애플리케이션 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

### <a name="turn-on-web-sockets"></a>웹 소켓 켜기

애플리케이션의 Azure Portal **애플리케이션 설정**에서 웹 소켓을 켭니다. 설정을 적용하려면 애플리케이션을 다시 시작해야 합니다.

Azure CLI에서 다음 명령을 사용하여 웹 소켓 지원을 켭니다.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

그런 다음, 애플리케이션을 다시 시작합니다.

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>기본 문자 인코딩 설정

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에 `-Dfile.encoding=UTF-8` 값을 사용하여 `JAVA_OPTS`이라고 하는 새 앱 설정을 만듭니다.

또는 App Service Maven 플러그 인을 사용하여 앱 설정을 구성할 수 있습니다. 플러그 인 구성에서 설정 이름 및 값 태그를 추가합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>JSP 파일 미리 컴파일

Tomcat 애플리케이션의 성능을 향상시키기 위해 App Service에 배포하기 전에 JSP 파일을 컴파일할 수 있습니다. Apache Sling에서 제공하는 [Maven 플러그 인](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) 또는 [Ant 빌드 파일](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)을 사용할 수 있습니다.

## <a name="secure-applications"></a>보안 애플리케이션

App Service에서 실행 되는 Java 응용 프로그램에는 다른 응용 프로그램과 동일한 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합이 있습니다.

### <a name="authenticate-users-easy-auth"></a>사용자 인증(간편 인증)

Azure Portal에서 **인증 및 권한 부여** 옵션을 사용하여 앱 인증을 설정합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](configure-authentication-provider-aad.md) 및 기타 ID 공급자 관련 문서를 참조하세요. 여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](app-service-authentication-how-to.md) 문서의 지침을 따르세요.

#### <a name="tomcat"></a>Tomcat

Tomcat 애플리케이션은 주체 개체를 Map 개체로 캐스팅하여 서블릿에서 사용자의 클레임에 직접 액세스할 수 있습니다. Map 개체는 각 클레임 유형을 해당 유형에 대한 클레임 컬렉션에 매핑합니다. 아래 코드에서 `request`는 `HttpServletRequest`의 인스턴스입니다.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

이제 특정 클레임에 대한 `Map` 개체를 검사할 수 있습니다. 예를 들어 다음 코드 조각은 모든 클레임 형식을 반복하고 각 컬렉션의 내용을 인쇄합니다.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

사용자를 로그아웃시키려면 `/.auth/ext/logout` 경로를 사용합니다. 다른 작업을 수행하려면 [App Service 인증 및 권한 부여 사용](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)에 대한 설명서를 참조하세요. Tomcat [HttpServletRequest 인터페이스](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) 및 해당 메서드에 대한 공식 설명서도 있습니다. 다음 서블릿 메서드도 App Service 구성에 따라 하이드레이션됩니다.

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

이 기능을 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_AUTH_SKIP_PRINCIPAL` 애플리케이션 설정을 만듭니다. App Service에서 추가한 모든 서블릿 필터를 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_SKIP_FILTERS` 설정을 만듭니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[Azure App Service에서 tls 바인딩을 사용 하 여 사용자 지정 DNS 이름 보안](configure-ssl-bindings.md) 설정의 지침에 따라 기존 TLS/SSL 인증서를 업로드 하 고 응용 프로그램의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

### <a name="use-keyvault-references"></a>KeyVault 참조 사용

[Azure KeyVault](../key-vault/general/overview.md)는 액세스 정책 및 감사 기록을 통해 중앙 집중식 비밀 관리를 제공합니다. 키 자격 증명 모음에 암호 또는 연결 문자열과 같은 비밀을 저장하고 환경 변수를 통해 애플리케이션에서 이러한 비밀에 액세스할 수 있습니다.

먼저 [Key Vault에 앱 액세스 권한을 부여](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)하고 [애플리케이션 설정에서 사용자 비밀을 KeyVault 참조로 만드는](app-service-key-vault-references.md#reference-syntax) 지침을 따릅니다. App Service 터미널에 원격으로 액세스하는 동안 환경 변수를 인쇄하여 참조가 비밀로 해결되는지 확인할 수 있습니다.

이러한 비밀을 Spring 또는 Tomcat 구성 파일에 삽입하려면 환경 변수 삽입 구문(`${MY_ENV_VAR}`)을 사용합니다. Spring 구성 파일은 [표면화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)에 대한 이 설명서를 참조하세요.


## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 NewRelic 및 AppDynamics APM(애플리케이션 성능 모니터링) 플랫폼을 사용하여 Linux의 Azure App Service에 배포된 Java 애플리케이션을 연결하는 방법을 보여 줍니다.

### <a name="configure-new-relic"></a>New Relic 구성

1. [NewRelic.com](https://newrelic.com/signup) 에서 새 유물 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 *newrelic-java-x.x.x.zip*과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 을 사용 하 여 */home/site/wwwroot/apm*새 디렉터리를 만듭니다.
5. 압축을 푼 새 유물 Java 에이전트 파일을 */home/site/wwwroot/apm*아래의 디렉터리에 업로드 합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic*에 있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml*에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - 앱이 **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.

### <a name="configure-appdynamics"></a>AppDynamics 구성

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드하면 파일 이름은 *AppServerAgent-x.x.x.xxxxx.zip*과 유사합니다.
3. [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 을 사용 하 여 */home/site/wwwroot/apm*새 디렉터리를 만듭니다.
4. Java 에이전트 파일을 */home/site/wwwroot/apm* 아래의 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics*에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.

>  `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

## <a name="data-sources"></a>데이터 원본

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

JDBC(Java Database Connectivity) 또는 JPA(Java Persistence API)를 사용하도록 Tomcat을 구성하려면 먼저 시작 시 Tomcat에서 읽은 `CATALINA_OPTS` 환경 변수를 사용자 지정합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure Portal의 **구성** > **애플리케이션 설정** 페이지에서 환경 변수를 설정합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>애플리케이션 수준 데이터 원본

1. 프로젝트의 *META-INF/* 디렉터리에 *context.xml* 파일을 만듭니다. *META-INF/* 디렉터리가 없으면 디렉터리를 만듭니다.

2. *context.xml*에서는 데이터 원본을 JNDI 주소에 연결하는 `Context` 요소를 추가합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection"
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}"
            password="${connURL}"
        />
    </Context>
    ```

3. 애플리케이션의 데이터 원본을 사용하도록 애플리케이션의 *web.xml*을 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>구성 완료

마지막으로 Tomcat 클래스 경로에 드라이버 Jar를 저장 하 고 App Service를 다시 시작 합니다. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 */home/tomcat/lib* 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.

1. [Cloud Shell](https://shell.azure.com)에서 웹앱 확장을 설치합니다.

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. 다음 CLI 명령을 실행하여 로컬 시스템에서 App Service로 SSH 터널을 만듭니다.

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 파일을 */home/tomcat/lib* 폴더에 업로드합니다.

또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](deploy-configure-credentials.md)을 따릅니다.

## <a name="configuring-tomcat"></a>Tomcat 구성

Tomcat의 `server.xml` 또는 다른 구성 파일을 편집 하려면 먼저 포털에서 Tomcat 주 버전을 기록해 둡니다.

1. 명령을 실행 하 여 버전에 대 한 Tomcat home 디렉터리를 찾습니다 `env` . 로 시작 하 고 주 버전과 일치 하는 환경 변수를 검색 `AZURE_TOMCAT` 합니다. 예를 들어 `AZURE_TOMCAT85_HOME` 는 Tomcat 8.5에 대 한 Tomcat 디렉터리를 가리킵니다.
1. 버전에 대 한 Tomcat home 디렉터리를 확인 한 후에 구성 디렉터리를로 복사 합니다 `D:\home` . 예를 들어 `AZURE_TOMCAT85_HOME` 의 값이 인 경우 `D:\Program Files (x86)\apache-tomcat-8.5.37` 복사 된 디렉터리의 새 경로는 `D:\home\apache-tomcat-8.5.37` 입니다.

마지막으로, App Service를 다시 시작합니다. 배포는 이전과 마찬가지로로 이동 해야 합니다 `D:\home\site\wwwroot\webapps` .

## <a name="configure-java-se"></a>Java SE 구성

을 실행 하는 경우 Windows의 Java SE에 대 한 JAR 응용 프로그램 `server.port` 은 응용 프로그램이 시작 될 때 명령줄 옵션으로 전달 됩니다. 환경 변수에서 HTTP 포트를 수동으로 확인할 수 있습니다 `HTTP_PLATFORM_PORT` . 이 환경 변수의 값은 응용 프로그램이 수신 하는 HTTP 포트가 됩니다. 

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다.

주 버전 업데이트는 Windows 용 Azure App Service의 새로운 런타임 옵션을 통해 제공 됩니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다. Azure의 Java에 대 한 자세한 내용은 [이 지원 문서](https://docs.microsoft.com/azure/developer/java/fundamentals/java-jdk-long-term-support)를 참조 하세요.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/vuln-metrics/cvss)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

Tomcat 8.0이 [2018 년 9 월 30 일까 지 종료 (EOL)](https://tomcat.apache.org/tomcat-80-eol.html)에 도달 했습니다. 런타임이 아직 Azure App Service 사용할 수는 동안 Azure는 Tomcat 8.0에 보안 업데이트를 적용 하지 않습니다. 가능 하면 응용 프로그램을 Tomcat 8.5 또는 9.0로 마이그레이션합니다. Tomcat 8.5 및 9.0은 모두 Azure App Service에서 사용할 수 있습니다. 자세한 내용은 [공식 Tomcat 사이트](https://tomcat.apache.org/whichversion.html) 를 참조 하세요. 

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

Azure [지원 Azul 줄루어 JDK](https://www.azul.com/downloads/azure-only/zulu/) 에 대 한 제품 지원은 azure 용으로 개발 하거나 [정규화 된 azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용 하 여 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 때 Microsoft를 통해 제공 됩니다.

### <a name="runtime-support"></a>런타임 지원

개발자는 [정규화된 지원 계획](https://azure.microsoft.com/support/plans/)이 있는 경우 Azure 지원을 통해 Azul Zulu JDK 관련 [문제를 제기](/azure/azure-portal/supportability/how-to-create-azure-support-request)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 항목에서는 Windows의 Azure App Service에 대 한 Java 런타임 설명을 제공 합니다.

- Azure App Service에서 웹 응용 프로그램을 호스트 하는 방법에 대 한 자세한 내용은 [App Service 개요](overview.md)를 참조 하세요.
- Azure 개발의 Java에 대 한 자세한 내용은 [Azure Java 개발자 센터를](https://docs.microsoft.com/java/azure/?view=azure-java-stable)참조 하세요.
