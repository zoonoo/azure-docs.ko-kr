---
title: Java 앱 구성
description: Azure App Service에서 실행 되도록 Java 앱을 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
keywords: azure app service, 웹 앱, windows, oss, java, tomcat, jboss
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18, devx-track-java
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 65b31bd39c85ea9073bb9415b9829df12b7d9e35
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171577"
---
# <a name="configure-a-java-app-for-azure-app-service"></a>Azure App Service에 대 한 Java 앱 구성

Azure App Service를 통해 Java 개발자는 완전히 관리 되는 서비스에서 Java SE, Tomcat 및 JBoss EAP 웹 응용 프로그램을 신속 하 게 빌드, 배포 및 확장할 수 있습니다. Maven 플러그 인을 사용 하 여 응용 프로그램을 배포 하거나, 명령줄에서 또는 IntelliJ, Eclipse 또는 Visual Studio Code 같은 편집기에 배포 합니다.

이 가이드에서는 App Service를 사용 하는 Java 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [Java 빠른](quickstart-java.md) 시작을 참조 하세요. Java 개발과 관련 되지 않은 App Service 사용에 대 한 일반적인 질문은 [APP SERVICE FAQ](faq-configuration-and-management.md)에 답변 되어 있습니다.

## <a name="deploying-your-app"></a>앱 배포

[Maven 용 Azure 웹 앱 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 을 사용 하 여 war 또는 .jar 파일을 배포할 수 있습니다. [Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/) 또는 [Azure Toolkit for Eclipse](/azure/developer/java/toolkit-for-eclipse)를 사용 하 여 널리 사용 되는 ide를 배포할 수도 있습니다.

그렇지 않으면 배포 방법이 보관 형식에 따라 달라집니다.

### <a name="java-se"></a>Java SE

Kudu 사이트의 끝점을 사용 하 여 jar 파일을 Java SE에 배포 합니다 `/api/zipdeploy/` . 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#rest)를 참조하세요.

### <a name="tomcat"></a>Tomcat

.war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#deploy-war-file)를 참조하세요.

::: zone pivot="platform-linux"

### <a name="jboss-eap"></a>JBoss EAP

JBoss에 게 war 파일을 배포 하려면 끝점을 사용 `/api/wardeploy/` 하 여 보관 파일을 게시 합니다. 이 API에 대한 자세한 내용은 [이 설명서](./deploy-zip.md#deploy-war-file)를 참조하세요.

귀 파일을 배포 하려면 [FTP를 사용](deploy-ftp.md)합니다.

::: zone-end

FTP를 사용하여 .war 또는.jar을 배포하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure App Service 진단 개요](overview-diagnostics.md)를 참조하세요.

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

::: zone pivot="platform-windows"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

::: zone-end
::: zone pivot="platform-linux"

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

::: zone-end

자세한 내용은 [Cloud Shell에서 로그 스트리밍](troubleshoot-diagnostic-logs.md#in-cloud-shell)을 참조하세요.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

::: zone pivot="platform-linux"

### <a name="troubleshooting-tools"></a>문제 해결 도구

기본 제공 Java 이미지는 [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 운영 체제를 기반으로 합니다. `apk` 패키지 관리자를 사용하여 문제 해결 도구 또는 명령을 설치합니다.

::: zone-end

### <a name="flight-recorder"></a>비행 레코더

Azul Jvm를 사용 하는 App Service에 대 한 모든 Java 런타임은 줄루어 비행 레코더와 함께 제공 됩니다. 이를 사용 하 여 JVM, 시스템 및 응용 프로그램 이벤트를 기록 하 고 Java 응용 프로그램의 문제를 해결할 수 있습니다.

::: zone pivot="platform-windows"

#### <a name="timed-recording"></a>시간 기록

시간이 지정 된 기록을 수행 하려면 Java 응용 프로그램의 PID (프로세스 ID)가 필요 합니다. PID를 찾으려면 https://의 웹 앱 SCM 사이트로 브라우저를 엽니다. scm.azurewebsites.net/ProcessExplorer/>. <. 이 페이지에는 웹 앱에서 실행 중인 프로세스가 표시 됩니다. 테이블에서 "java" 라는 프로세스를 찾고 해당 PID (프로세스 ID)를 복사 합니다.

다음으로, SCM 사이트의 상단 도구 모음에서 **디버그 콘솔** 를 열고 다음 명령을 실행 합니다. `<pid>`을 이전에 복사한 프로세스 ID로 바꿉니다. 이 명령은 Java 응용 프로그램의 30 초 profiler 기록을 시작 하 고 디렉터리에 이라는 파일을 생성 합니다 `timed_recording_example.jfr` `D:\home` .

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

::: zone-end
::: zone pivot="platform-linux"

App Service에 SSH를 실행 하 고 `jcmd` 명령을 실행 하 여 실행 중인 모든 Java 프로세스 목록을 확인 합니다. Jcmd 자체 외에 PID(프로세스 ID 번호)로 Java 애플리케이션이 실행 중인 것을 볼 수 있습니다.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

아래 명령을 실행하여 JVM의 30초 기록을 시작합니다. 그러면 JVM을 프로파일링하고 홈 디렉터리에 *jfr_example.jfr*이라는 JFR 파일을 만듭니다. (116을 Java 앱의 PID로 대체합니다.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30초 간격 동안 `jcmd 116 JFR.check`를 실행하여 기록이 발생하는지 확인할 수 있습니다. 그러면 지정된 Java 프로세스의 모든 기록이 표시됩니다.

#### <a name="continuous-recording"></a>연속 기록

Zulu 비행 레코더를 사용하여 런타임 성능에 대한 영향을 최소화하면서 Java 애플리케이션을 지속적으로 프로파일링할 수 있습니다([원본](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). 이렇게 하려면 다음 Azure CLI 명령을 실행하여 필요한 구성으로 JAVA_OPTS 앱 설정을 만듭니다. 앱이 시작되면 JAVA_OPTS 앱 설정의 내용이 `java` 명령에 전달됩니다.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

기록이 시작되면 `JFR.dump` 명령을 사용하여 언제든지 현재 기록 데이터를 덤프할 수 있습니다.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

::: zone-end

#### <a name="analyze-jfr-files"></a>`.jfr`파일 분석

[FTPS](deploy-ftp.md)를 사용하여 JFR 파일을 로컬 컴퓨터에 다운로드합니다. JFR 파일을 분석하려면 [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/)을 다운로드하여 설치합니다. Zulu Mission Control에 대한 지침은 [Azul 설명서](https://docs.azul.com/zmc/) 및 [설치 지침](/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)을 참조하세요.

### <a name="app-logging"></a>앱 로깅

::: zone pivot="platform-windows"

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](troubleshoot-diagnostic-logs.md#enable-application-logging-windows)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */home/LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

::: zone-end
::: zone pivot="platform-linux"

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */home/LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

Linux 기반 App Services에 대한 Azure Blob Storage 로깅은 [Azure Monitor(미리 보기)](./troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)를 사용해서만 구성할 수 있습니다. 

::: zone-end

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](../azure-monitor/app/java-trace-logs.md)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux용 Azure App Service는 기본적으로 Azure Portal 및 CLI를 통해 튜닝 및 사용자 지정이 가능합니다. 비 Java 관련 웹앱 구성에 대한 다음 문서를 검토하세요.

- [앱 설정 구성](configure-common.md#configure-app-settings)
- [사용자 지정 도메인 설정](app-service-web-tutorial-custom-domain.md)
- [SSL 바인딩 구성](configure-ssl-bindings.md)
- [CDN 추가](../cdn/cdn-add-to-web-app.md)
- [Kudu 사이트 구성](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)


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

App Service에서 실행 되는 Java 응용 프로그램에는 다른 응용 프로그램과 동일한 [보안 모범 사례](../security/fundamentals/paas-applications-using-app-services.md) 집합이 있습니다.

### <a name="authenticate-users-easy-auth"></a>사용자 인증(간편 인증)

Azure Portal에서 **인증 및 권한 부여** 옵션을 사용하여 앱 인증을 설정합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](configure-authentication-provider-aad.md) 및 기타 ID 공급자 관련 문서를 참조하세요. 여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](app-service-authentication-how-to.md) 문서의 지침을 따르세요.

#### <a name="java-se"></a>Java SE

Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다. *application.properties* 파일에서 최대 헤더 크기를 늘려야 합니다. 이 값은 `16384`로 설정하는 것이 좋습니다.

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

사용자를 로그아웃시키려면 `/.auth/ext/logout` 경로를 사용합니다. 다른 작업을 수행하려면 [App Service 인증 및 권한 부여 사용](./app-service-authentication-how-to.md)에 대한 설명서를 참조하세요. Tomcat [HttpServletRequest 인터페이스](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) 및 해당 메서드에 대한 공식 설명서도 있습니다. 다음 서블릿 메서드도 App Service 구성에 따라 하이드레이션됩니다.

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

이 기능을 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_AUTH_SKIP_PRINCIPAL` 애플리케이션 설정을 만듭니다. App Service에서 추가한 모든 서블릿 필터를 사용하지 않도록 설정하려면 값이 `1`인 `WEBSITE_SKIP_FILTERS` 설정을 만듭니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[Azure App Service에서 SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md) 지침에 따라 기존 SSL 인증서를 업로드하고 애플리케이션의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

### <a name="use-keyvault-references"></a>KeyVault 참조 사용

[Azure KeyVault](../key-vault/general/overview.md)는 액세스 정책 및 감사 기록을 통해 중앙 집중식 비밀 관리를 제공합니다. 키 자격 증명 모음에 암호 또는 연결 문자열과 같은 비밀을 저장하고 환경 변수를 통해 애플리케이션에서 이러한 비밀에 액세스할 수 있습니다.

먼저 [Key Vault에 앱 액세스 권한을 부여](app-service-key-vault-references.md#granting-your-app-access-to-key-vault)하고 [애플리케이션 설정에서 사용자 비밀을 KeyVault 참조로 만드는](app-service-key-vault-references.md#reference-syntax) 지침을 따릅니다. App Service 터미널에 원격으로 액세스하는 동안 환경 변수를 인쇄하여 참조가 비밀로 해결되는지 확인할 수 있습니다.

이러한 비밀을 Spring 또는 Tomcat 구성 파일에 삽입하려면 환경 변수 삽입 구문(`${MY_ENV_VAR}`)을 사용합니다. Spring 구성 파일은 [표면화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)에 대한 이 설명서를 참조하세요.

::: zone pivot="platform-linux"

### <a name="use-the-java-key-store"></a>Java 키 저장소 사용

기본적으로 [App Service Linux에 업로드된](configure-ssl-certificate.md) 공용 또는 프라이빗 인증서는 컨테이너가 시작될 때 해당 Java 키 저장소에 로드됩니다. 인증서를 업로드한 후에는 Java 키 저장소에 로드하기 위해 App Service를 다시 시작해야 합니다. 공용 인증서는 `$JAVA_HOME/jre/lib/security/cacerts`에서 키 저장소로 로드되고 프라이빗 인증서는 `$JAVA_HOME/lib/security/client.jks`에 저장됩니다.

Java Key Store의 인증서를 사용하여 JDBC 연결을 암호화하려면 추가 구성이 필요할 수 있습니다. 선택한 JDBC 드라이버에 대한 설명서를 참조하세요.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](/sql/connect/jdbc/connecting-with-ssl-encryption)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [MongoDB](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initialize-the-java-key-store"></a>Java 키 저장소 초기화

`import java.security.KeyStore` 개체를 초기화하려면 암호를 사용하여 키 저장소 파일을 로드합니다. 두 키 저장소의 기본 암호는 "changeit"입니다.

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>수동으로 키 저장소 로드

키 저장소에 인증서를 수동으로 로드할 수 있습니다. App Service가 인증서를 키 저장소에 자동으로 로드하지 못하도록 값이 `1`인 앱 설정 `SKIP_JAVA_KEYSTORE_LOAD`를 만듭니다. Azure Portal을 통해 App Service에 업로드된 모든 공용 인증서는 `/var/ssl/certs/`에 저장됩니다. 프라이빗 인증서는 `/var/ssl/private/`에 저장됩니다.

App Service에 [SSH 연결을 열고](configure-linux-open-ssh-session.md) `keytool` 명령을 실행하여 Java Key Tool을 상호 작용하거나 디버그할 수 있습니다. 명령 목록은 [핵심 도구 설명서](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html)를 참조하세요. KeyStore API에 대한 자세한 내용은 [공식 설명서](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)를 참조하세요.

::: zone-end

## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 NewRelic 및 AppDynamics APM(애플리케이션 성능 모니터링) 플랫폼을 사용하여 Linux의 Azure App Service에 배포된 Java 애플리케이션을 연결하는 방법을 보여 줍니다.

### <a name="configure-new-relic"></a>New Relic 구성

::: zone pivot="platform-windows"

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 *newrelic-java-x.x.x.zip*과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm*을 만듭니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 */home/site/wwwroot/apm* 아래 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic*에 있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml*에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

    - **JAVA SE** 앱의 경우 `JAVA_OPTS` 값을 사용 하 여 라는 환경 변수를 만듭니다 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - **Tomcat**의 경우 값으로 이라는 환경 변수를 만듭니다 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end
::: zone pivot="platform-linux"

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드합니다. 파일 이름은 *newrelic-java-x.x.x.zip*과 유사합니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm*을 만듭니다.
5. 압축이 풀린 NewRelic Java 에이전트 파일을 */home/site/wwwroot/apm* 아래 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic*에 있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml*에서 YAML 파일을 수정하고 자리 표시자 라이선스 값을 사용자의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
   
    - **JAVA SE** 앱의 경우 `JAVA_OPTS` 값을 사용 하 여 라는 환경 변수를 만듭니다 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .
    - **Tomcat**의 경우 값으로 이라는 환경 변수를 만듭니다 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar` .

::: zone-end

>  `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

### <a name="configure-appdynamics"></a>AppDynamics 구성

::: zone pivot="platform-windows"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드하면 파일 이름은 *AppServerAgent-x.x.x.xxxxx.zip*과 유사합니다.
3. [Kudu 콘솔](https://github.com/projectkudu/kudu/wiki/Kudu-console) 을 사용 하 여 */home/site/wwwroot/apm*새 디렉터리를 만듭니다.
4. Java 에이전트 파일을 */home/site/wwwroot/apm* 아래의 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics*에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

   - **JAVA SE** 응용 프로그램의 경우 값으로 이라는 환경 변수를 만듭니다 `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` . 여기서는 App Service 이름입니다.
   - **Tomcat** apps의 경우 값으로 이라는 환경 변수를 만듭니다 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` . 여기서 `<app-name>` 는 App Service 이름입니다.

::: zone-end
::: zone pivot="platform-linux"

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드하면 파일 이름은 *AppServerAgent-x.x.x.xxxxx.zip*과 유사합니다.
3. [App Service 인스턴스에 대해 SSH를 실행](configure-linux-open-ssh-session.md)하고 새 디렉터리 */home/site/wwwroot/apm*을 만듭니다.
4. Java 에이전트 파일을 */home/site/wwwroot/apm* 아래의 디렉터리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics*에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.

   - **JAVA SE** 응용 프로그램의 경우 값으로 이라는 환경 변수를 만듭니다 `JAVA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` `<app-name>` . 여기서는 App Service 이름입니다.
   - **Tomcat** apps의 경우 값으로 이라는 환경 변수를 만듭니다 `CATALINA_OPTS` `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` . 여기서 `<app-name>` 는 App Service 이름입니다.

::: zone-end

> [!NOTE]
>  `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

## <a name="configure-data-sources"></a>데이터 원본 구성

### <a name="java-se"></a>Java SE

Spring Boot 애플리케이션에서 데이터 원본에 연결하려면 연결 문자열을 만들어 *application.properties* 파일에 삽입하는 것이 좋습니다.

1. App Service 페이지의 "구성" 섹션에서 문자열의 이름을 설정하고, JDBC 연결 문자열을 값 필드에 붙여넣고, 유형을 "사용자 지정"으로 설정합니다. 필요에 따라 이 연결 문자열을 슬롯 설정으로 설정할 수 있습니다.

    이 연결 문자열은 `CUSTOMCONNSTR_<your-string-name>`라는 환경 변수로 애플리케이션에서 액세스할 수 있습니다. 예를 들어 위에서 만든 연결 문자열의 이름은 `CUSTOMCONNSTR_exampledb`로 지정됩니다.

2. *application.properties* 파일에서 환경 변수 이름을 사용하여 이 연결 문자열을 참조합니다. 이 예에서는 다음을 사용합니다.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

이 항목에 대한 자세한 내용은 [데이터 액세스에 대한 Spring Boot 설명서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) 및 [표면화된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)을 참조하세요.

::: zone pivot="platform-windows"

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

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

#### <a name="shared-server-level-resources"></a>공유 서버 수준 리소스

공유 서버 수준 데이터 원본을 추가하려면 Tomcat의 server.xml을 편집해야 합니다. 먼저 [시작 스크립트](faq-app-service-linux.md#built-in-images)를 업로드하고 **구성** > **시작 명령**의 스크립트에 대한 경로를 설정합니다. [FTP](deploy-ftp.md)를 사용하여 시작 스크립트를 업로드할 수 있습니다.

시작 스크립트는 server.xml 파일의 [xsl 변환](https://www.w3schools.com/xml/xsl_intro.asp) 파일을 만들고 결과 xml 파일을 `/usr/local/tomcat/conf/server.xml`로 출력합니다. 시작 스크립트는 apk를 통해 libxslt를 설치해야 합니다. FTP를 통해 xsl 파일 및 시작 스크립트를 업로드할 수 있습니다. 다음은 예제 시작 스크립트입니다.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

예제 xsl 파일은 아래에 제공됩니다. 예제 xsl 파일은 Tomcat server.xml에 새 커넥터 노드를 추가합니다.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>구성 완료

마지막으로 Tomcat 클래스 경로에 드라이버 JAR을 배치하고 App Service를 다시 시작합니다.

1. JDBC 드라이버 파일을 Tomcat classloader에 사용할 수 있도록 */home/tomcat/lib* 디렉터리에 배치합니다. (아직 존재하지 않는 경우 이 디렉터리를 만듭니다.) 이러한 파일을 App Service 인스턴스에 업로드하려면 다음 단계를 수행합니다.

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

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_BASE`을 `/home/tomcat`로 다시 설정하고 업데이트된 구성을 사용합니다.

### <a name="jboss-eap"></a>JBoss EAP

[JBoss EAP에 데이터 원본을 등록할](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/7.0/html/configuration_guide/datasource_management)때 세 가지 핵심 단계는 jdbc 드라이버 업로드, jdbc 드라이버를 모듈로 추가 및 모듈 등록입니다. App Service은 상태 비저장 호스팅 서비스 이므로, 컨테이너가 시작 될 때 데이터 원본 모듈을 추가 하 고 등록 하는 구성 명령을 스크립팅된 후에 적용 해야 합니다.

1. 데이터베이스의 JDBC 드라이버를 가져옵니다. 
2. JDBC 드라이버에 대 한 XML 모듈 정의 파일을 만듭니다. 아래에 표시 된 예제는 PostgreSQL에 대 한 모듈 정의입니다.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="org.postgres">
        <resources>
        <!-- ***** IMPORTANT : REPLACE THIS PLACEHOLDER *******-->
        <resource-root path="/home/site/deployments/tools/postgresql-42.2.12.jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

1. JBoss CLI 명령을 이라는 파일에 배치 `jboss-cli-commands.cli` 합니다. JBoss 명령은 모듈을 추가 하 고이를 데이터 원본으로 등록 해야 합니다. 아래 예제에서는 PostgreSQL에 대 한 JBoss CLI 명령을 보여 줍니다.

    ```bash
    #!/usr/bin/env bash
    module add --name=org.postgres --resources=/home/site/deployments/tools/postgresql-42.2.12.jar --module-xml=/home/site/deployments/tools/postgres-module.xml

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name="postgres",driver-module-name="org.postgres",driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=${POSTGRES_CONNECTION_URL,env.POSTGRES_CONNECTION_URL:jdbc:postgresql://db:5432/postgres} --user-name=${POSTGRES_SERVER_ADMIN_FULL_NAME,env.POSTGRES_SERVER_ADMIN_FULL_NAME:postgres} --password=${POSTGRES_SERVER_ADMIN_PASSWORD,env.POSTGRES_SERVER_ADMIN_PASSWORD:example} --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker
    ```

1. `startup_script.sh`JBoss CLI 명령을 호출 하는 시작 스크립트를 만듭니다. 아래 예제에서는를 호출 하는 방법을 보여 줍니다 `jboss-cli-commands.cli` . 나중에 컨테이너가 시작 될 때이 스크립트를 실행 하 App Service configre 합니다. 

    ```bash
    $JBOSS_HOME/bin/jboss-cli.sh --connect --file=/home/site/deployments/tools/jboss-cli-commands.cli
    ```

1. 원하는 FTP 클라이언트를 사용 하 여 JDBC 드라이버, `jboss-cli-commands.cli` , `startup_script.sh` 및 모듈 정의를에 업로드 `/site/deployments/tools/` 합니다.
2. 컨테이너가 시작 될 때 실행 되도록 사이트를 구성 `startup_script.sh` 합니다. Azure Portal에서 **구성**  >  **일반 설정**  >  **시작 명령**으로 이동 합니다. 시작 명령 필드를로 설정 `/home/site/deployments/tools/startup_script.sh` 합니다. 변경 내용을 **저장**합니다.

데이터 원본이 JBoss 서버에 추가 되었는지 확인 하려면 webapp에 SSH를 실행 하 고를 실행 `$JBOSS_HOME/bin/jboss-cli.sh --connect` 합니다. JBoss에 연결 되 면를 실행 `/subsystem=datasources:read-resource` 하 여 데이터 원본 목록을 인쇄 합니다.

::: zone-end

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

## <a name="choosing-a-java-runtime-version"></a>Java 런타임 버전 선택

App Service를 통해 사용자는 JVM의 주 버전 (예: Java 8 또는 Java 11) 및 부 버전 (예: 1.8.0 _232 또는 11.0.5)을 선택할 수 있습니다. 새 부 버전을 사용할 수 있게 되 면 부 버전이 자동으로 업데이트 되도록 선택할 수도 있습니다. 대부분의 경우 프로덕션 사이트는 고정 된 부 JVM 버전을 사용 해야 합니다. 이렇게 하면 부 버전 자동 업데이트 중에 발생 하는 중단이 발생 하지 않습니다.

부 버전을 고정 하도록 선택 하는 경우 사이트에서 JVM 부 버전을 주기적으로 업데이트 해야 합니다. 최신 부 버전에서 응용 프로그램을 실행 하려면 준비 슬롯을 만들고 준비 사이트에서 부 버전을 증가 시킵니다. 새 부 버전에서 응용 프로그램이 올바르게 실행 되었는지 확인 한 후에는 스테이징 및 프로덕션 슬롯을 교환할 수 있습니다.

## <a name="jboss-eap-hardware-options"></a>JBoss EAP 하드웨어 옵션

JBoss EAP는 프리미엄 및 격리 된 하드웨어 옵션 에서만 사용할 수 있습니다. 공개 미리 보기로 제공 되는 무료, 공유, 기본 또는 표준 계층에서 JBoss EAP 사이트를 만든 고객은 예기치 않은 동작을 방지 하기 위해 프리미엄 또는 격리 된 하드웨어 계층으로 확장 해야 합니다.

## <a name="java-runtime-statement-of-support"></a>Java 런타임 문 지원

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

Azure에서 지원되는 JDK(Java Development Kit)는 [Azul Systems](https://www.azul.com/)를 통해 제공하는 [Zulu](https://www.azul.com/downloads/azure-only/zulu/)입니다. OpenJDK의 Azul Zulu Enterprise 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 여기에는 Java SE 애플리케이션을 빌드하고 실행하기 위한 모든 구성 요소가 포함됩니다. [Java JDK 설치](https://aka.ms/azure-jdks)에서 JDK를 설치할 수 있습니다.

주 버전 업데이트는 Azure App Service의 새로운 런타임 옵션을 통해 제공 됩니다. 고객은 App Service 배포를 구성하여 최신 버전의 Java로 업데이트해야 하며, 주 업데이트를 테스트하고 요구 사항을 충족하도록 관리할 책임이 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다. Azure의 Java에 대 한 자세한 내용은 [이 지원 문서](/azure/developer/java/fundamentals/java-jdk-long-term-support)를 참조 하세요.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/vuln-metrics/cvss)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

Tomcat 8.0이 [2018 년 9 월 30 일까 지 종료 (EOL)](https://tomcat.apache.org/tomcat-80-eol.html)에 도달 했습니다. Azure App Service에서 런타임을 계속 사용할 수 있지만 Azure는 Tomcat 8.0에 보안 업데이트를 적용 하지 않습니다. 가능 하면 응용 프로그램을 Tomcat 8.5 또는 9.0로 마이그레이션합니다. Tomcat 8.5 및 9.0은 모두 Azure App Service에서 사용할 수 있습니다. 자세한 내용은 [공식 Tomcat 사이트](https://tomcat.apache.org/whichversion.html) 를 참조 하세요. 

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.


### <a name="local-development"></a>로컬 개발

개발자는 [Azul의 다운로드 사이트](https://www.azul.com/downloads/azure-only/zulu/)에서 로컬 개발용 Azul Zulu Enterprise JDK의 프로덕션 버전을 다운로드할 수 있습니다.

### <a name="development-support"></a>개발 지원

Azure [지원 Azul 줄루어 JDK](https://www.azul.com/downloads/azure-only/zulu/) 에 대 한 제품 지원은 azure 용으로 개발 하거나 [정규화 된 azure 지원 계획](https://azure.microsoft.com/support/plans/)을 사용 하 여 [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) 때 Microsoft를 통해 제공 됩니다.

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.

Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](faq-app-service-linux.md)에서 찾을 수 있습니다.
