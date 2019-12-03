---
title: Linux Java 앱 구성
description: 앱에 대해 미리 작성 된 Java 컨테이너를 구성 하는 방법에 대해 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여 줍니다.
keywords: azure app service, 웹 앱, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: edb8f25ff1e4fa01e905c3ae5c7d0ec7ab58f8bb
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2019
ms.locfileid: "74705945"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Azure App Service에 대 한 Linux Java 앱 구성

Linux에서 Azure App Service Java 개발자는 완전히 관리 되는 Linux 기반 서비스에서 Tomcat, WildFly 또는 Java Standard Edition (SE) 패키지 웹 응용 프로그램을 신속 하 게 빌드, 배포 및 확장할 수 있습니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드에서는 App Service의 기본 제공 Linux 컨테이너를 사용 하는 Java 개발자를 위한 주요 개념 및 지침을 제공 합니다. Azure App Service 사용한 적이 없는 경우 먼저 [java 빠른](quickstart-java.md) 시작 및 [java PostgreSQL 자습서](tutorial-java-enterprise-postgresql-app.md) 를 따르세요.

## <a name="deploying-your-app"></a>앱 배포

[Azure App Service에 대해 Maven 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 을 사용 하 여 jar 및 war 파일을 둘 다 배포할 수 있습니다. [Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) 또는 [Azure Toolkit for Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse)에서도 인기 있는 ide를 사용 하 여 배포할 수 있습니다.

그렇지 않으면 배포 방법이 보관 유형에 따라 달라 집니다.

- .war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)를 참조하세요.
- Java SE 이미지에서 .jar 파일을 배포하려면 Kudu 사이트의 `/api/zipdeploy/` 엔드포인트를 사용합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)를 참조하세요.

FTP를 사용하여 .war 또는.jar을 배포하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure App Service 진단 개요](../overview-diagnostics.md)를 참조 하세요.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

자세한 내용은 [Cloud Shell의 스트림 로그](../troubleshoot-diagnostic-logs.md#in-cloud-shell)를 참조 하세요.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. Java 및 Tomcat 앱 로그는 */home/LogFiles/Application/* 디렉터리에서 찾을 수 있습니다.

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

### <a name="troubleshooting-tools"></a>문제 해결 도구

기본 제공 Java 이미지는 [알파인 Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 운영 체제를 기반으로 합니다. `apk` 패키지 관리자를 사용 하 여 문제 해결 도구나 명령을 설치 합니다.

### <a name="flight-recorder"></a>비행 레코더

App Service의 모든 Linux Java 이미지는 줄루어 비행 레코더를 설치 하 여 JVM에 쉽게 연결 하 고 프로파일러 기록을 시작 하거나 힙 덤프를 생성할 수 있습니다.

#### <a name="timed-recording"></a>시간 기록

시작 하려면 App Service에 SSH를 실행 하 고 `jcmd` 명령을 실행 하 여 실행 중인 모든 Java 프로세스 목록을 확인 합니다. Jcmd 자체 외에 pid (프로세스 ID 번호)를 사용 하 여 Java 응용 프로그램이 실행 되 고 있는 것을 볼 수 있습니다.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

아래 명령을 실행 하 여 JVM의 30 초 기록을 시작 합니다. 그러면 JVM을 프로 파일링 하 고 홈 디렉터리에 *jfr_example. jfr* 이라는 jfr 파일을 만듭니다. (116을 Java 앱의 pid로 대체 합니다.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30 초 간격 동안 `jcmd 116 JFR.check`를 실행 하 여 기록이 발생 하는지 확인할 수 있습니다. 그러면 지정 된 Java 프로세스의 모든 기록이 표시 됩니다.

#### <a name="continuous-recording"></a>연속 녹화

줄루어 비행 레코더를 사용 하 여 런타임 성능 ([원본](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf))에 미치는 영향을 최소화 하면서 Java 응용 프로그램을 지속적으로 프로 파일링 할 수 있습니다. 이렇게 하려면 다음 Azure CLI 명령을 실행 하 여 필요한 구성을 사용 하 여 JAVA_OPTS 이라는 앱 설정을 만듭니다. 앱이 시작 되 면 JAVA_OPTS 앱 설정의 내용이 `java` 명령에 전달 됩니다.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

기록이 시작 된 후에는 `JFR.dump` 명령을 사용 하 여 언제 든 지 현재 기록 데이터를 덤프할 수 있습니다.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

자세한 내용은 [Jcmd 명령 참조](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)를 참조 하세요.

### <a name="analyzing-recordings"></a>녹음 분석

[FTPS](../deploy-ftp.md) 를 사용 하 여 jfr 파일을 로컬 컴퓨터에 다운로드 합니다. JFR 파일을 분석 하려면 [줄루어 업무 제어](https://www.azul.com/products/zulu-mission-control/)를 다운로드 하 여 설치 합니다. 줄루어 업무 제어에 대 한 지침은 [Azul 설명서](https://docs.azul.com/zmc/) 및 [설치 지침](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)을 참조 하세요.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux 용 Azure App Service에서는 Azure Portal 및 CLI를 통해 기본 튜닝 및 사용자 지정을 지원 합니다. Java와 관련 되지 않은 웹 앱 구성에 대 한 다음 문서를 검토 합니다.

- [앱 설정 구성](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [사용자 지정 도메인 설정](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [SSL 바인딩 구성](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [CDN 추가](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Kudu 사이트 구성](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

Tomcat 및 Java SE 환경에서 할당 된 메모리 또는 기타 JVM 런타임 옵션을 설정 하려면 옵션을 사용 하 여 `JAVA_OPTS` 이라는 [앱 설정을](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) 만듭니다. App Service Linux는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `-Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Maven 플러그 인에서 앱 설정을 구성 하려면 Azure 플러그 인 섹션에서 설정/값 태그를 추가 합니다. 다음 예에서는 특정 최소 및 최대 Java 힙 크기를 설정 합니다.

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

JAR 응용 프로그램을 배포 하는 경우 기본 제공 이미지가 앱을 올바르게 식별할 수 있도록이 파일의 이름을 *app.config* 로 지정 해야 합니다. Maven 플러그 인은이 이름을 자동으로 바꿉니다. JAR의 이름을 *app.config*로 변경 하지 않으려는 경우에는 명령을 사용 하 여 셸 스크립트를 업로드 하 여 jar을 실행할 수 있습니다. 그런 다음 포털의 구성 섹션에 있는 [시작 파일](app-service-linux-faq.md#built-in-images) 텍스트 상자에이 스크립트의 전체 경로를 붙여넣습니다. 시작 스크립트가 배치 된 디렉터리에서 실행 되지 않습니다. 따라서 항상 절대 경로를 사용 하 여 시작 스크립트의 파일을 참조 합니다 (예: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>시작 시간 제한 조정

Java 응용 프로그램이 특히 클 경우 시작 시간 제한을 늘려야 합니다. 이 작업을 수행 하려면 응용 프로그램 설정 `WEBSITES_CONTAINER_START_TIME_LIMIT` 만들고 제한 시간이 초과 될 때까지 App Service 기다려야 하는 시간 (초)으로 설정 합니다. 최대값은 `1800` 초입니다.

### <a name="pre-compile-jsp-files"></a>JSP 파일 미리 컴파일

Tomcat 응용 프로그램의 성능을 향상 시키기 위해 App Service에 배포 하기 전에 JSP 파일을 컴파일할 수 있습니다. Apache에서 제공 하거나이 [Ant 빌드 파일](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)을 사용 하 여 제공 되는 [Maven 플러그 인](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) 을 사용할 수 있습니다.

## <a name="secure-applications"></a>보안 애플리케이션

Linux용 App Service에서 실행되는 Java 애플리케이션의 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합은 다른 애플리케이션과 동일합니다.

### <a name="authenticate-users-easy-auth"></a>사용자 인증 (Easy Auth)

**인증 및 권한 부여** 옵션을 사용 하 여 Azure Portal에서 앱 인증을 설정 합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) 및 기타 ID 공급자 관련 문서를 참조하세요. 여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) 문서의 지침을 따르세요.

#### <a name="tomcat-and-wildfly"></a>Tomcat 및 WildFly

Tomcat 또는 WildFly 응용 프로그램은 주 개체를 Map 개체로 캐스팅 하 여 서블릿에 사용자의 클레임에 직접 액세스할 수 있습니다. Map 개체는 각 클레임 형식을 해당 형식에 대 한 클레임 컬렉션에 매핑합니다. 아래 코드에서 `request`은 `HttpServletRequest`인스턴스입니다.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

이제 특정 클레임에 대 한 `Map` 개체를 검사할 수 있습니다. 예를 들어 다음 코드 조각은 모든 클레임 형식을 반복 하 고 각 컬렉션의 내용을 인쇄 합니다.

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

사용자를 로그 아웃 하려면 `/.auth/ext/logout` 경로를 사용 합니다. 다른 작업을 수행 하려면 [App Service 인증 및 권한 부여 사용](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)에 대 한 설명서를 참조 하세요. Tomcat [Httpserv request 인터페이스](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) 및 해당 메서드에 대 한 공식 설명서도 있습니다. 다음 서블릿 메서드도 App Service 구성에 따라 하이드레이션 됩니다.

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

이 기능을 사용 하지 않도록 설정 하려면 `1`값으로 `WEBSITE_AUTH_SKIP_PRINCIPAL` 라는 응용 프로그램 설정을 만듭니다. App Service에서 추가한 모든 서블릿 필터를 사용 하지 않도록 설정 하려면 `1`값을 사용 하 여 `WEBSITE_SKIP_FILTERS` 라는 설정을 만듭니다.

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다. *응용 프로그램의 속성* 파일에서 최대 헤더 크기를 늘려야 합니다. 이 값은 `16384`로 설정하는 것이 좋습니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

기존 SSL 인증서를 업로드 하 고 응용 프로그램의 도메인 이름에 바인딩하려면 [Azure App Service의 ssl 바인딩과 함께 사용자 지정 DNS 이름 보안](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) 설정의 지침을 따릅니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

### <a name="use-keyvault-references"></a>KeyVault 참조 사용

[Azure KeyVault](../../key-vault/key-vault-overview.md) 는 액세스 정책 및 감사 기록을 통해 중앙 집중화 된 비밀 관리를 제공 합니다. 키 자격 증명 모음에 암호 또는 연결 문자열과 같은 암호를 저장 하 고 환경 변수를 통해 응용 프로그램에서 이러한 비밀에 액세스할 수 있습니다.

먼저, Key Vault에 대 한 [앱 액세스 권한을 부여](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) 하 고 [응용 프로그램 설정에서 암호에 대 한 keyvault 참조](../app-service-key-vault-references.md#reference-syntax)를 설정 하는 지침을 따르세요. App Service 터미널에 원격으로 액세스 하는 동안 환경 변수를 인쇄 하 여 참조가 암호로 확인 되는지 확인할 수 있습니다.

이러한 암호를 스프링 또는 Tomcat 구성 파일에 삽입 하려면 환경 변수 삽입 구문 (`${MY_ENV_VAR}`)을 사용 합니다. 스프링 구성 파일은 [표면화 된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)에 대 한이 설명서를 참조 하세요.

### <a name="using-the-java-key-store"></a>Java 키 저장소 사용

기본적으로 [App Service Linux에 업로드](../configure-ssl-certificate.md) 된 공용 또는 개인 인증서는 컨테이너가 시작 될 때 Java 키 저장소에 로드 됩니다. 즉, 아웃 바운드 TLS 연결을 만들 때 업로드 된 인증서를 연결 컨텍스트에서 사용할 수 있습니다. 인증서를 업로드 한 후에는 Java 키 저장소에 로드 하기 위해 App Service를 다시 시작 해야 합니다.

App Service에 대 한 [SSH 연결을 열고](app-service-linux-ssh-support.md) 명령 `keytool`를 실행 하 여 Java 키 도구를 상호 작용 하거나 디버그할 수 있습니다. 명령 목록은 [키 도구 설명서](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) 를 참조 하세요. 인증서는 Java의 기본 키 저장소 파일 위치 `$JAVA_HOME/jre/lib/security/cacerts`에 저장 됩니다.

JDBC 연결을 암호화 하는 데 추가 구성이 필요할 수 있습니다. 선택한 JDBC 드라이버에 대 한 설명서를 참조 하세요.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)

#### <a name="manually-initialize-and-load-the-key-store"></a>수동으로 키 저장소 초기화 및 로드

키 저장소를 초기화 하 고 인증서를 수동으로 추가할 수 있습니다. `1` 값을 사용 하 여 키 저장소로 인증서를 자동으로 로드할 App Service 사용 하지 않도록 설정 하 `SKIP_JAVA_KEYSTORE_LOAD`앱 설정을 만듭니다. Azure Portal를 통해 App Service에 업로드 된 모든 공용 인증서는 `/var/ssl/certs/`에 저장 됩니다. 개인 인증서는 `/var/ssl/private/`에 저장 됩니다.

키 저장소 API에 대 한 자세한 내용은 [공식 설명서](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)를 참조 하세요.

## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 NewRelic 및 AppDynamics APM (응용 프로그램 성능 모니터링) 플랫폼과 함께 Linux의 Azure App Service에 배포 된 Java 응용 프로그램을 연결 하는 방법을 보여 줍니다.

### <a name="configure-new-relic"></a>New Relic 구성

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 Java 에이전트를 다운로드 합니다. *newrelic-java-x*와 유사한 파일 이름을 갖게 됩니다.
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [App Service 인스턴스에 SSH](app-service-linux-ssh-support.md) 하 고 새 디렉터리 */home/site/wwwroot/apm*을 만듭니다.
5. 압축을 푼 NewRelic Java 에이전트 파일을 */home/site/wwwroot/apm*아래의 디렉터리에 업로드 합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic*에 있어야 합니다.
6. */Home/site/wwwroot/apm/newrelic/newrelic.yml* 에서 yaml 파일을 수정 하 고 자리 표시자 라이선스 값을 자신의 라이선스 키로 바꿉니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - 앱이 **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.
    - **WildFly**를 사용 하는 경우 Java 에이전트 및 jboss 구성 설치에 대 한 지침은 [여기](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) 에서 새 유물 설명서를 참조 하세요.

### <a name="configure-appdynamics"></a>AppDynamics 구성

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 Java 에이전트를 다운로드 합니다. 파일 이름은 AppServerAgent-x. x. x. x. x. x. x. x. x. x. x. *xxxxx*
3. [App Service 인스턴스에 SSH](app-service-linux-ssh-support.md) 하 고 새 디렉터리 */home/site/wwwroot/apm*을 만듭니다.
4. */Home/site/wwwroot/apm*아래의 디렉터리에 Java 에이전트 파일을 업로드 합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics*에 있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - **WildFly**를 사용 하는 경우 Java 에이전트 및 jboss 구성 설치에 대 한 지침은 [여기](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) 에서 appdynamics 설명서를 참조 하세요.

> [!NOTE]
> `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

## <a name="configure-jar-applications"></a>JAR 응용 프로그램 구성

### <a name="starting-jar-apps"></a>JAR 앱 시작

기본적으로 App Service는 JAR 응용 프로그램의 이름을 *app.config*로 지정 합니다. 이 이름이 있으면 자동으로 실행 됩니다. Maven 사용자의 경우 *pom .xml*의 `<build>` 섹션에 `<finalName>app</finalName>`를 포함 하 여 JAR 이름을 설정할 수 있습니다. `archiveFileName` 속성을 설정 하 여 [Gradle에서 동일한 작업을 수행할 수](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) 있습니다.

JAR에 다른 이름을 사용 하려는 경우 JAR 파일을 실행 하는 [시작 명령도](app-service-linux-faq.md#built-in-images) 제공 해야 합니다. 예: `java -jar my-jar-app.jar` 시작 명령에 대 한 값은 포털의 구성 > 일반 설정 또는 `STARTUP_COMMAND`라는 응용 프로그램 설정으로 설정할 수 있습니다.

### <a name="server-port"></a>서버 포트

App Service Linux는 들어오는 요청을 80 포트에 라우팅합니다. 따라서 응용 프로그램은 포트 80 에서도 수신 대기 해야 합니다. 응용 프로그램의 구성 (예: 스프링의 *응용 프로그램 속성* 파일) 또는 시작 명령 (예: `java -jar spring-app.jar --server.port=80`)에서이 작업을 수행할 수 있습니다. 일반적인 Java 프레임 워크는 다음 설명서를 참조 하세요.

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [재생 프레임 워크](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>데이터 원본

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

JDBC (Java Database Connectivity) 또는 JPA (Java 지 속성 API)를 사용 하도록 Tomcat를 구성 하려면 먼저 Tomcat에서 읽은 `CATALINA_OPTS` 환경 변수를 사용자 지정 합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure Portal의 **구성** > **응용 프로그램 설정** 페이지에서 환경 변수를 설정 합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>응용 프로그램 수준 데이터 원본

1. 프로젝트의 *META-INF/* 디렉터리에 *컨텍스트별* 파일을 만듭니다. 존재 하지 않는 경우 *META-INF/* 디렉터리를 만듭니다.

2. *컨텍스트별*에서 데이터 원본을 JNDI 주소에 연결 하는 `Context` 요소를 추가 합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

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

3. 응용 프로그램의 데이터 소스를 사용 하도록 응용 프로그램의 *웹 .xml* 을 업데이트 합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>공유 서버 수준 리소스

공유 되는 서버 수준 데이터 원본을 추가 하려면 Tomcat의 system.xml을 편집 해야 합니다. 먼저 [시작 스크립트](app-service-linux-faq.md#built-in-images) 를 업로드 하 고 **구성** > **시작 명령**에서 스크립트에 대 한 경로를 설정 합니다. [FTP](../deploy-ftp.md)를 사용 하 여 시작 스크립트를 업로드할 수 있습니다.

시작 스크립트는 [xsl 변환을](https://www.w3schools.com/xml/xsl_intro.asp) server .xml 파일로 만들고 결과 xml 파일을 `/usr/local/tomcat/conf/server.xml`에 출력 합니다. 시작 스크립트는 apk를 통해 upxslt를 설치 해야 합니다. Xsl 파일 및 시작 스크립트는 FTP를 통해 업로드할 수 있습니다. 다음은 시작 스크립트의 예입니다.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /usr/local/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /home/tomcat/conf/server.xml
```

예제 xsl 파일은 아래에 제공 되어 있습니다. 예제 xsl 파일은 Tomcat 서버에 새 커넥터 노드를 추가 합니다.

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

마지막으로 Tomcat 클래스 경로에 드라이버 Jar를 저장 하 고 App Service를 다시 시작 합니다.

1. JDBC 드라이버 파일을 */home/tomcat/lib* 디렉터리에 배치 하 여 Tomcat classloader에서 사용할 수 있는지 확인 합니다. (아직 없는 경우이 디렉터리를 만듭니다.) App Service 인스턴스에 이러한 파일을 업로드 하려면 다음 단계를 수행 합니다.

    1. [Cloud Shell](https://shell.azure.com)에서 webapp 확장을 설치 합니다.

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 다음 CLI 명령을 실행 하 여 로컬 시스템에서 App Service에 대 한 SSH 터널을 만듭니다.

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP 클라이언트를 사용 하 여 로컬 터널링 포트에 연결 하 고 파일을 */home/tomcat/lib* 폴더에 업로드 합니다.

    또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json)을 따릅니다.

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_BASE`을 `/home/tomcat`로 다시 설정하고 업데이트된 구성을 사용합니다.

### <a name="spring-boot"></a>Spring Boot

스프링 부팅 응용 프로그램에서 데이터 원본에 연결 하려면 연결 문자열을 만들어 *응용 프로그램 속성* 파일에 삽입 하는 것이 좋습니다.

1. App Service 페이지의 "구성" 섹션에서 문자열의 이름을 설정 하 고, JDBC 연결 문자열을 값 필드에 붙여넣고, 유형을 "Custom"으로 설정 합니다. 필요에 따라이 연결 문자열을 슬롯 설정으로 설정할 수 있습니다.

    이 연결 문자열은 `CUSTOMCONNSTR_<your-string-name>`이라는 환경 변수로 응용 프로그램에서 액세스할 수 있습니다. 예를 들어 위에서 만든 연결 문자열의 이름은 `CUSTOMCONNSTR_exampledb`로 지정 됩니다.

2. *응용 프로그램 속성* 파일에서 환경 변수 이름을 사용 하 여이 연결 문자열을 참조 합니다. 이 예에서는 다음을 사용 합니다.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

이 항목에 대 한 자세한 내용은 데이터 액세스 및 [표면화 된 구성](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) 에 대 한 [스프링 부팅 설명서](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) 를 참조 하세요.

## <a name="configure-java-ee-wildfly"></a>Java EE 구성 (WildFly)

> [!NOTE]
> App Service Linux의 Java Enterprise Edition은 현재 미리 보기로 제공 됩니다. 프로덕션 작업에는이 스택을 사용 **하지 않는** 것이 좋습니다.

Linux에서 Azure App Service를 통해 Java 개발자는 완전히 관리 되는 Linux 기반 서비스에서 java Enterprise (Java EE) 응용 프로그램을 빌드, 배포 및 확장할 수 있습니다.  기본 Java Enterprise runtime environment는 오픈 소스 [WildFly](https://wildfly.org/) 응용 프로그램 서버입니다.

이 섹션은 다음 하위 섹션을 포함합니다.

- [App Service으로 크기 조정](#scale-with-app-service)
- [응용 프로그램 서버 구성 사용자 지정](#customize-application-server-configuration)
- [모듈 및 종속성 설치](#install-modules-and-dependencies)
- [데이터 원본 구성](#configure-data-sources)
- [메시지 broker로 Service Bus 사용](#use-service-bus-as-a-message-broker)

### <a name="scale-with-app-service"></a>App Service의 크기 조정

Linux 기반의 App Service에서 실행 중인 WildFly 애플리케이션 서버는 도메인 구성이 아닌 독립 실행형 모드에서 실행됩니다. App Service 계획을 확장할 때 각 WildFly 인스턴스는 독립 실행형 서버로 구성됩니다.

[크기 조정 규칙](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md)을 사용하고 [인스턴스 수를 늘려서](../manage-scale-up.md?toc=/azure/app-service/containers/toc.json) 애플리케이션을 수직 또는 수평적으로 확장합니다.

### <a name="customize-application-server-configuration"></a>사용자 지정 애플리케이션 서버 구성

웹 앱 인스턴스는 상태 비저장 이므로 시작 시 응용 프로그램에 필요한 WildFly 구성을 지원 하도록 각 새 인스턴스를 시작 하도록 구성 해야 합니다.
다음을 수행하기 위해 WildFly CLI를 호출하도록 시작 Bash 스크립트를 작성할 수 있습니다.

- 데이터 원본 설정
- 메시징 공급자 구성
- WildFly 서버 구성에 다른 모듈 및 종속성을 추가 합니다.

WildFly이 실행 중이 고 응용 프로그램이 시작 되기 전에 스크립트가 실행 됩니다. 스크립트는 */opt/jboss/wildfly/bin/jboss-cli.sh* 에서 호출 된 [jboss CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) 를 사용 하 여 서버 시작 후 필요한 구성 또는 변경 내용으로 응용 프로그램 서버를 구성 해야 합니다.

CLI의 대화형 모드를 사용 하 여 WildFly를 구성 하지 마세요. 대신 다음과 같은 `--file` 명령을 사용하여 JBoss CLI에 명령 스크립트를 제공할 수 있습니다.

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

FTP를 사용 하 여 */home* 디렉터리 아래에 있는 App Service 인스턴스의 위치 (예: */home/site/deployments/tools*)에 시작 스크립트를 업로드 합니다. 자세한 내용은 [FTP/S를 사용 하 여 Azure App Service에 앱 배포](https://docs.microsoft.com/azure/app-service/deploy-ftp)를 참조 하세요.

Azure Portal의 **시작 스크립트** 필드를 시작 셸 스크립트의 위치 (예: */home/site/deployments/tools/your-startup-script.sh*)로 설정 합니다.

응용 프로그램 구성에서 [앱 설정을](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) 제공 하 여 스크립트에서 사용할 환경 변수를 전달 합니다. 애플리케이션 설정은 연결 문자열 및 버전 제어에서 벗어나도록 애플리케이션을 구성하는 데 필요한 기타 비밀을 유지합니다.

### <a name="install-modules-and-dependencies"></a>모듈 및 종속성 설치

JBoss CLI를 통해 모듈 및 해당 종속성을 WildFly 클래스 경로에 설치 하려면 자체 디렉터리에 다음 파일을 만들어야 합니다. 일부 모듈 및 종속성에는 JNDI 명명 또는 기타 API 관련 구성과 같은 추가 구성이 필요할 수 있습니다. 따라서 이 목록은 대부분의 경우에서 종속성을 구성해야 하는 최소 세트입니다.

- [XML 모듈 설명자](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). 이 XML 파일은 모듈의 이름, 특성 및 종속성을 정의합니다. 이 [예제 module.xml 파일](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource)은 Postgres 모듈, 해당 JAR 파일 JDBC 종속성 및 필요한 기타 모듈 종속성을 정의합니다.
- 사용자 모듈에 대한 필수 JAR 파일 종속성.
- 새 모듈을 구성하기 위한 JBoss CLI 명령을 사용하는 스크립트. 이 파일은 종속성을 사용하도록 서버를 구성하기 위해 JBoss CLI에서 실행할 명령을 포함합니다. 모듈, 데이터 원본 및 메시징 공급자를 추가하는 명령에 대한 문서는 [이 문서](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)를 참조하세요.
- JBoss CLI를 호출하고 이전 단계에서 스크립트를 실행하는 Bash 시작 스크립트. 이 파일은 App Service 인스턴스를 다시 시작 하거나 확장 중에 새 인스턴스를 프로 비전 할 때 실행 됩니다. 이 시작 스크립트는 jboss 명령이 JBoss CLI에 전달 되기 때문에 응용 프로그램에 대 한 다른 구성을 수행할 수 있습니다. 최소한 이 파일은 JBoss CLI 명령 스크립트를 JBoss CLI에 전달하는 단일 명령일 수 있습니다.

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

모듈에 대 한 파일 및 내용이 있으면 아래 단계에 따라 모듈을 WildFly 응용 프로그램 서버에 추가 합니다.

1. FTP를 사용 하 여 */home* 디렉터리에서 App Service 인스턴스의 위치 (예: */home/site/deployments/tools*)에 파일을 업로드 합니다. 자세한 내용은 [FTP/S를 사용 하 여 Azure App Service에 앱 배포](../deploy-ftp.md)를 참조 하세요.
2. Azure Portal의 **구성** > **일반 설정** 페이지에서 시작 **스크립트** 필드를 시작 셸 스크립트의 위치 (예: */home/site/deployments/tools/startup.sh*)로 설정 합니다.
3. 포털의 **개요** 섹션에서 **다시 시작** 단추를 누르거나 Azure CLI를 사용 하 여 App Service 인스턴스를 다시 시작 합니다.

### <a name="configure-data-sources"></a>데이터 원본 구성

데이터 원본에 액세스 하도록 WildFly/JBoss를 구성 하려면 "모듈 및 종속성 설치" 섹션에서 위에 설명 된 일반 프로세스를 사용 합니다. 다음 섹션에서는 PostgreSQL, MySQL 및 SQL Server 데이터 원본에 대 한이 프로세스에 대 한 구체적인 정보를 제공 합니다.

이 섹션에서는 앱, App Service 인스턴스 및 Azure 데이터베이스 서비스 인스턴스가 이미 있다고 가정 합니다. 아래 지침은 App Service 이름, 해당 리소스 그룹 및 데이터베이스 연결 정보를 나타냅니다. Azure Portal에서이 정보를 찾을 수 있습니다.

샘플 앱을 사용 하 여 처음부터 전체 프로세스를 진행 하려면 [자습서: Azure에서 JAVA EE 및 Postgres 웹 앱 빌드](tutorial-java-enterprise-postgresql-app.md)를 참조 하세요.

다음 단계에서는 기존 App Service 및 데이터베이스를 연결 하기 위한 요구 사항을 설명 합니다.

1. [PostgreSQL](https://jdbc.postgresql.org/download.html), [MySQL](https://dev.mysql.com/downloads/connector/j/)또는 [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server)에 대 한 JDBC 드라이버를 다운로드 합니다. 다운로드 한 보관 파일의 압축을 풀어 드라이버 jar 파일을 가져옵니다.

2. *모듈 .xml* 과 같은 이름을 사용 하 여 파일을 만들고 다음 태그를 추가 합니다. `<module name>` 자리 표시자 (꺾쇠 괄호 포함)를 PostgreSQL, MySQL 용 `com.mysql` `com.microsoft` 또는 SQL Server에 대 한 `org.postgres`으로 바꿉니다. `<JDBC .jar file path>`를 이전 단계의 jar 파일 이름으로 바꾸고,이 파일을 App Service 인스턴스에 저장할 위치의 전체 경로를 포함 합니다. */Home* 디렉터리 아래의 모든 위치를 사용할 수 있습니다.

    ```xml
    <?xml version="1.0" ?>
    <module xmlns="urn:jboss:module:1.1" name="<module name>">
        <resources>
           <resource-root path="<JDBC .jar file path>" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.transaction.api"/>
        </dependencies>
    </module>
    ```

3. *Datasource-commands* 와 같은 이름으로 파일을 만들고 다음 코드를 추가 합니다. `<JDBC .jar file path>`을 이전 단계에서 사용한 값으로 바꿉니다. `<module file path>`을 이전 단계의 파일 이름 및 App Service 경로로 바꿉니다 (예: */home/module.xml*).

    **PostgreSQL**

    ```console
    module add --name=org.postgres --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=postgres:add(driver-name=postgres,driver-module-name=org.postgres,driver-class-name=org.postgresql.Driver,driver-xa-datasource-class-name=org.postgresql.xa.PGXADataSource)

    data-source add --name=postgresDS --driver-name=postgres --jndi-name=java:jboss/datasources/postgresDS --connection-url=$DATABASE_CONNECTION_URL --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=org.postgresql.Driver --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLExceptionSorter --jta=true --use-java-context=true --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.postgres.PostgreSQLValidConnectionChecker

    reload --use-current-server-config=true
    ```

    **MySQL**

    ```console
    module add --name=com.mysql --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=mysql:add(driver-name=mysql,driver-module-name=com.mysql,driver-class-name=com.mysql.cj.jdbc.Driver)

    data-source add --name=mysqlDS --jndi-name=java:jboss/datasources/mysqlDS --connection-url=$DATABASE_CONNECTION_URL --driver-name=mysql --user-name=$DATABASE_SERVER_ADMIN_FULL_NAME --password=$DATABASE_SERVER_ADMIN_PASSWORD --use-ccm=true --max-pool-size=5 --blocking-timeout-wait-millis=5000 --enabled=true --driver-class=com.mysql.cj.jdbc.Driver --jta=true --use-java-context=true --exception-sorter-class-name=com.mysql.cj.jdbc.integration.jboss.ExtendedMysqlExceptionSorter

    reload --use-current-server-config=true
    ```

    **SQL Server**

    ```console
    module add --name=com.microsoft --resources=<JDBC .jar file path> --module-xml=<module file path>

    /subsystem=datasources/jdbc-driver=sqlserver:add(driver-name=sqlserver,driver-module-name=com.microsoft,driver-class-name=com.microsoft.sqlserver.jdbc.SQLServerDriver,driver-datasource-class-name=com.microsoft.sqlserver.jdbc.SQLServerDataSource)

    data-source add --name=sqlDS --jndi-name=java:jboss/datasources/sqlDS --driver-name=sqlserver --connection-url=$DATABASE_CONNECTION_URL --validate-on-match=true --background-validation=false --valid-connection-checker-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLValidConnectionChecker --exception-sorter-class-name=org.jboss.jca.adapters.jdbc.extensions.mssql.MSSQLExceptionSorter

    reload --use-current-server-config=true
    ```

    이 파일은 다음 단계에서 설명 하는 시작 스크립트를 통해 실행 됩니다. WildFly 모듈로 JDBC 드라이버를 설치 하 고 해당 WildFly 데이터 원본을 만든 다음 서버를 다시 로드 하 여 변경 내용이 적용 되도록 합니다.

4. *Startup.sh* 와 같은 이름으로 파일을 만들고 다음 코드를 추가 합니다. `<JBoss CLI script>`을 이전 단계에서 만든 파일 이름으로 바꿉니다. App Service 인스턴스에 파일을 배치 하는 위치의 전체 경로를 포함 해야 합니다 (예: */home/datasource-commands.cli*).

    ```bash
    #!/usr/bin/env bash
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=<JBoss CLI script>
    ```

5. FTP를 사용 하 여 JDBC 파일, 모듈 XML 파일, JBoss CLI 스크립트 및 시작 스크립트를 App Service 인스턴스에 업로드 합니다. 이전 단계에서 지정한 위치 (예: */home*)에 이러한 파일을 넣습니다. FTP에 대 한 자세한 내용은 [ftp/S를 사용 하 여 Azure App Service에 앱 배포](https://docs.microsoft.com/azure/app-service/deploy-ftp)를 참조 하세요.

6. Azure CLI를 사용 하 여 데이터베이스 연결 정보를 저장 하는 설정을 App Service에 추가 합니다. `<resource group>` 및 `<webapp name>`을 App Service 사용 하는 값으로 바꿉니다. `<database server name>`, `<database name>`, `<admin name>`및 `<admin password>`를 데이터베이스 연결 정보로 바꿉니다. Azure Portal에서 App Service 및 데이터베이스 정보를 가져올 수 있습니다.

    **PostgreSQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:postgresql://<database server name>:5432/<database name>?ssl=true \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **MySQL**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT \
            DATABASE_SERVER_ADMIN_FULL_NAME=<admin name> \
            DATABASE_SERVER_ADMIN_PASSWORD=<admin password>
    ```

    **SQL Server:**

    ```bash
    az webapp config appsettings set \
        --resource-group <resource group> \
        --name <webapp name> \
        --settings \
            DATABASE_CONNECTION_URL=jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
    ```

    DATABASE_CONNECTION_URL 값은 각 데이터베이스 서버에 대해 다르며 Azure Portal 값과 다릅니다. WildFly에서 사용 하려면 여기에 표시 된 URL 형식 (및 위의 코드 조각)이 필요 합니다.

    * **PostgreSQL:** `jdbc:postgresql://<database server name>:5432/<database name>?ssl=true`
    * **MySQL:** `jdbc:mysql://<database server name>:3306/<database name>?ssl=true\&useLegacyDatetimeCode=false\&serverTimezone=GMT`
    * **SQL Server:** `jdbc:sqlserver://<database server name>:1433;database=<database name>;user=<admin name>;password=<admin password>;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;`

7. Azure Portal에서 App Service으로 이동 하 고 **구성** > **일반 설정** 페이지를 찾습니다. 시작 **스크립트** 필드를 시작 스크립트의 이름 및 위치 (예: */home/startup.sh*)로 설정 합니다.

다음 번에 App Service 다시 시작 하면 시작 스크립트가 실행 되 고 필요한 구성 단계가 수행 됩니다. 이 구성이 제대로 수행 되었는지 테스트 하려면 SSH를 사용 하 여 App Service에 액세스 한 다음 Bash 프롬프트에서 직접 시작 스크립트를 실행 하면 됩니다. App Service 로그를 검사할 수도 있습니다. 이러한 옵션에 대 한 자세한 내용은 [앱 로깅 및 디버깅](#logging-and-debugging-apps)을 참조 하세요.

다음에는 앱에 대 한 WildFly 구성을 업데이트 하 고 다시 배포 해야 합니다. 다음 단계를 사용하세요.

1. 앱에 대 한 *src/main/resources/META-INF/지 속성* 파일을 열고 `<jta-data-source>` 요소를 찾습니다. 다음과 같이 내용을 바꿉니다.

    **PostgreSQL**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

    **MySQL**

    ```xml
    <jta-data-source>java:jboss/datasources/mysqlDS</jta-data-source>
    ```

    **SQL Server**

    ```xml
    <jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
    ```

2. Bash 프롬프트에서 다음 명령을 사용 하 여 앱을 다시 빌드하고 다시 배포 합니다.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

3. Azure Portal의 **개요** 섹션에서 **다시 시작** 단추를 누르거나 Azure CLI를 사용 하 여 App Service 인스턴스를 다시 시작 합니다.

이제 App Service 인스턴스가 데이터베이스에 액세스 하도록 구성 되었습니다.

WildFly를 사용 하 여 데이터베이스 연결을 구성 하는 방법에 대 한 자세한 내용은 [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7), [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource)또는 [SQL Server](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898)를 참조 하세요.

### <a name="use-service-bus-as-a-message-broker"></a>메시지 broker로 Service Bus 사용

메시지 broker로 [Azure Service Bus](/azure/service-bus-messaging) 를 사용 하도록 WildFly 및 메시지 기반 bean을 구성할 수 있습니다. 구성 후에는 [Apache Qpid](https://qpid.apache.org) 를 JMS (Java Message Service) 클라이언트로 사용 하 여 메시지를 보내고 받을 수 있습니다. EJBs (엔터프라이즈 Java Bean)를 사용 하 여 원격 JMS 연결 팩터리 및 큐를 구성 하는 JMS 리소스 어댑터 (JMS RA)를 구성 하는 몇 가지 단계가 있습니다. 이 원격 설치는 Azure Service Bus을 가리키고 AMQP 프로토콜용 Apache Qpid JMS 공급자를 사용 합니다.

다음 단계에서는 필수 구성 및 코드에 대해 설명 합니다. 이러한 단계에서는 bean, Service Bus 네임 스페이스, 큐 및 구독이 있는 토픽을 호스팅하기 위한 App Service 인스턴스를 만들었다고 가정 합니다. 이러한 리소스를 만드는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [빠른 시작: Linux의 Azure App Service에서 Java 앱 만들기](/azure/app-service/containers/quickstart-java)
- [빠른 시작: Azure CLI을 사용 하 여 Service Bus 큐 만들기](/azure/service-bus-messaging/service-bus-quickstart-cli)
- [빠른 시작: 항목에 대 한 Service Bus 토픽 및 구독을 만들려면 Azure Portal를 사용 합니다.](/azure/service-bus-messaging/service-bus-quickstart-topics-subscriptions-portal)

1. Bash 터미널을 열고 다음 명령을 사용 하 여 Azure 리소스 정보를 환경 변수에 저장 합니다. 자리 표시자 (꺾쇠 괄호 포함)를 표시 된 값으로 바꿉니다.

    | 변수            | Value                                                                      |
    |---------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME  | App Service 인스턴스를 포함 하는 리소스 그룹의 이름입니다.       |
    | WEBAPP_NAME         | App Service 인스턴스의 이름입니다.                                     |
    | 지역              | 앱이 호스트 되는 영역의 이름입니다.                           |
    | DEFAULT_SBNAMESPACE | Service Bus 네임 스페이스의 이름입니다.                                    |
    | SB_SAS_POLICY       | 네임 스페이스에 대 한 SAS (공유 액세스 서명) 정책의 이름입니다.   |
    | SB_SAS_KEY          | 큐의 SAS 정책에 대 한 기본 또는 보조 키입니다.                  |
    | SB_QUEUE            | Service Bus 큐의 이름입니다.                                        |
    | SB_TOPIC            | Service Bus 항목의 이름입니다.                                        |
    | SB_SUBSCRIPTION     | 항목에 대 한 구독 이름입니다.                                |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=${WEBAPP_NAME}-appservice-plan
    REGION=<region>
    DEFAULT_SBNAMESPACE=<namespace>
    SB_SAS_POLICY=<SAS policy>
    SB_SAS_KEY=<SAS key>
    SB_QUEUE=<queue>
    SB_TOPIC=<topic>
    SB_SUBSCRIPTION=<subscription>
    PROVIDER_URL=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000
    ```

    이 정보는 Azure 포털에서 찾을 수 있습니다. SAS 정책 및 키의 경우 앱이 큐 및 토픽 구독 모두에 액세스할 수 있도록 네임 스페이스에 대 한 값을 사용 해야 합니다. Azure Portal에서 이러한 값을 찾으려면 네임 스페이스 리소스로 이동 하 고 **공유 액세스 정책**을 선택한 다음 **RootManageSharedAccessKey** 정책을 선택 합니다.

2. [Apache Qpid JMS 공급자](https://qpid.apache.org/components/jms/index.html)를 다운로드 합니다. *Lib* 와 *lib/선택적* 디렉터리에서 .jar 파일을 찾습니다.

3. *모듈 .xml* 이라는 파일을 만들고 다음 태그를 추가 합니다. 파일 이름이 1 단계에서 추출한 파일과 일치 하도록 각 jar 파일에 대 한 올바른 버전으로 `<version>` 자리 표시자 (꺾쇠 괄호 포함)의 각 인스턴스를 바꿉니다.

    ```xml
    <module xmlns="urn:jboss:module:1.1" name="org.jboss.genericjms.provider">
        <resources>
            <resource-root path="proton-j-<version>.jar"/>
            <resource-root path="qpid-jms-client-<version>.jar"/>
            <resource-root path="slf4j-log4j12-<version>.jar"/>
            <resource-root path="slf4j-api-<version>.jar"/>
            <resource-root path="log4j-<version>.jar"/>
            <resource-root path="netty-buffer-<version>.jar" />
            <resource-root path="netty-codec-<version>.jar" />
            <resource-root path="netty-codec-http-<version>.jar" />
            <resource-root path="netty-common-<version>.jar" />
            <resource-root path="netty-handler-<version>.jar" />
            <resource-root path="netty-resolver-<version>.jar" />
            <resource-root path="netty-transport-<version>.jar" />
            <resource-root path="netty-transport-native-epoll-<version>-linux-x86_64.jar" />
            <resource-root path="netty-transport-native-kqueue-<version>-osx-x86_64.jar" />
            <resource-root path="netty-transport-native-unix-common-<version>.jar" />
            <resource-root path="qpid-jms-discovery-<version>jar" />
        </resources>
        <dependencies>
            <module name="javax.api"/>
            <module name="javax.jms.api"/>
        </dependencies>
    </module>
    ```

4. *Startup.sh* 라는 파일을 만들고 다음 코드를 추가 합니다.

    ```bash
    echo "Generating jndi.properties file in /home/site/deployments/tools directory"
    echo "connectionfactory.mymdbconnection=amqps://${DEFAULT_SBNAMESPACE}.servicebus.windows.net?amqp.idleTimeout=120000&jms.username=${SB_SAS_POLICY}&jms.password=${SB_SAS_KEY}" > /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbqueue=${SB_QUEUE}" >> /home/site/deployments/tools/jndi.properties
    echo "topic.mymdbtopic=${SB_TOPIC}" >> /home/site/deployments/tools/jndi.properties
    echo "queue.mymdbsubscription=${SB_TOPIC}/Subscriptions/${SB_SUBSCRIPTION}" >> /home/site/deployments/tools/jndi.properties
    echo "====== contents of /home/site/deployments/tools/jndi.properties ======"
    cat /home/site/deployments/tools/jndi.properties
    echo "====== EOF /home/site/deployments/tools/jndi.properties ======"
    echo "Generating commands.cli file for /home/site/deployments/tools directory"
    echo "# Start batching commands" > /home/site/deployments/tools/commands.cli
    echo "batch" >> /home/site/deployments/tools/commands.cli
    echo "# Configure the ee subsystem to enable MDB annotation property substitution" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:write-attribute(name=annotation-property-replacement,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "# Define system properties to be used in the substititution" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.queue:add(value=java:global/remoteJMS/mymdbqueue})" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.mymdb.topic:add(value=java:global/remoteJMS/mymdbsubscription)" >> /home/site/deployments/tools/commands.cli
    echo "/system-property=property.connection.factory:add(value=java:global/remoteJMS/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ee:list-add(name=global-modules, value={\"name\" => \"org.jboss.genericjms.provider\", \"slot\" =>\"main\"}" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=naming/binding=\"java:global/remoteJMS\":add(binding-type=external-context,module=org.jboss.genericjms.provider,class=javax.naming.InitialContext,environment=[java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory,org.jboss.as.naming.lookup.by.string=true,java.naming.provider.url=/home/site/deployments/tools/jndi.properties])" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra:add(module=org.jboss.genericjms,transaction-support=XATransaction)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:add(class-name=org.jboss.resource.adapter.jms.JmsManagedConnectionFactory, jndi-name=java:/jms/mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=ConnectionFactory:add(value=mymdbconnection)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd/config-properties=JndiParameters:add(value=\"java.naming.factory.initial=org.apache.qpid.jms.jndi.JmsInitialContextFactory;java.naming.provider.url=/home/site/deployments/tools/jndi.properties\")" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=resource-adapters/resource-adapter=generic-ra/connection-definitions=sbf-cd:write-attribute(name=security-application,value=true)" >> /home/site/deployments/tools/commands.cli
    echo "/subsystem=ejb3:write-attribute(name=default-resource-adapter-name, value=generic-ra)" >> /home/site/deployments/tools/commands.cli
    echo "# Run the batch commands" >> /home/site/deployments/tools/commands.cli
    echo "run-batch" >> /home/site/deployments/tools/commands.cli
    echo "reload" >> /home/site/deployments/tools/commands.cli
    echo "====== contents of /home/site/deployments/tools/commands.cli ======"
    cat /home/site/deployments/tools/commands.cli
    echo "======= EOF /home/site/deployments/tools/commands.cli ========"
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider
    mkdir /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main
    cp  /home/site/deployments/tools/*.jar /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/module.xml /opt/jboss/wildfly/modules/system/layers/base/org/jboss/genericjms/provider/main/
    cp /home/site/deployments/tools/jndi.properties /opt/jboss/wildfly/standalone/configuration/
    /opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/home/site/deployments/tools/commands.cli
    echo "Startup Run done"
    ```

    App Service 인스턴스는 시작 될 때마다이 스크립트를 실행 하 여 WildFly에 필요한 추가 구성을 제공 합니다. 이 스크립트는 앱 종속성을 필요한 위치에 복사 합니다. 또한 1 단계에 표시 된 환경 변수를 사용 하는 *jndi* 및 *commands. cli* 파일을 생성 합니다. 이러한 값은 이후 단계에서 App Service 인스턴스에도 전달 됩니다.

    *Commands* 파일은 시작 스크립트에 의해 시작 되는 [Wildfly cli](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) 스크립트입니다. 이 파일의 명령은 jndi 및 JNDI를 구성 하 여 *jndi 속성* 파일을 사용 합니다. 이러한 명령은 앱과 Service Bus 큐 또는 토픽 간에 연결을 만듭니다.

5. FTP를 사용 하 여 jar 파일, *모듈 .xml* 파일 및 *startup.sh* 파일을 App Service 인스턴스에 업로드 합니다. */Home* 디렉터리에 *startup.sh* 를 넣고 */home/site/deployments/tools* 디렉터리에 다른 파일을 넣습니다. 종속성의 전이적 닫기를 수행 하려면 *모듈 .xml* 파일에 나열 된 모든 jar 파일을 업로드 해야 합니다. FTP에 대 한 자세한 내용은 [ftp/S를 사용 하 여 Azure App Service에 앱 배포](https://docs.microsoft.com/azure/app-service/deploy-ftp)를 참조 하세요.

6. MessageListener 구현을 업데이트 하 여 다음 `import` 문을 추가 합니다.

    ```java
    import javax.ejb.ActivationConfigProperty;
    import javax.ejb.MessageDriven;
    import javax.ejb.TransactionAttribute;
    import javax.ejb.TransactionAttributeType;
    import javax.ejb.TransactionManagement;
    import javax.ejb.TransactionManagementType;
    import javax.jms.JMSException;
    import javax.jms.Message;
    import javax.jms.MessageListener;
    import javax.jms.TextMessage;
    ```

7. 다음에는 다음 예제와 일치 하도록 수신기 클래스 주석을 업데이트 합니다. 이 클래스는 메시지 수신을 기록 하는 샘플 구현을 제공 합니다.

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyQueueListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.queue}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Queue"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
    public class MyQueueListener implements MessageListener {

        private static final Logger LOGGER = Logger.getLogger(TopicListener.class.toString());

        public void onMessage(Message rcvMessage) {
            TextMessage msg = null;
            try {
                if (rcvMessage instanceof TextMessage) {
                    msg = (TextMessage) rcvMessage;
                    LOGGER.info("Received Message from topic: " + msg.getText());
                } else {
                    LOGGER.warning("Message of wrong type: " + rcvMessage.getClass().getName());
                }
            } catch (JMSException e) {
                LOGGER.warning("Exception on message : " + e.getMessage());
                throw new RuntimeException(e);
            }
        }
    }
    ```

    `connectionFactory` 및 `destinationLookup` 값은 *startup.sh* 스크립트에 의해 구성 된 WildFly 시스템 속성 값을 나타냅니다. `destinationType` 값은 `javax.jms.Queue`Service Bus 큐 인스턴스에 연결 중임을 나타냅니다. 이 값은 다음과 같이 Service Bus 토픽에 연결 하는 경우 `javax.jms.Topic` 해야 합니다.

    ```java
    @TransactionManagement(TransactionManagementType.BEAN)
    @TransactionAttribute(TransactionAttributeType.NOT_SUPPORTED)
    @MessageDriven(name = "MyTopicListener", activationConfig = {
            @ActivationConfigProperty(propertyName = "connectionFactory", propertyValue = "${property.connection.factory}"),
            @ActivationConfigProperty(propertyName = "destinationLookup", propertyValue = "${property.mymdb.topic}"),
            @ActivationConfigProperty(propertyName = "destinationType", propertyValue = "javax.jms.Topic"),
            @ActivationConfigProperty(propertyName = "acknowledgeMode", propertyValue = "Auto-acknowledge") })
        public class MyTopicListener implements MessageListener {
        // ...
    }
    ```

8. *Pom .xml* 파일의 `dependencies` 섹션을 업데이트 하 여 다음 종속성을 추가 합니다.

    ```xml
    <dependencies>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>qpid-jms-client</artifactId>
            <version>0.40.0</version>
        </dependency>
        <dependency>
            <groupId>org.apache.qpid</groupId>
            <artifactId>proton-j</artifactId>
            <version>0.31.0</version>
        </dependency>
        <dependency>
            <groupId>javax.enterprise</groupId>
            <artifactId>cdi-api</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.ejb</groupId>
            <artifactId>jboss-ejb-api_3.2_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.jms</groupId>
            <artifactId>jboss-jms-api_2.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.servlet</groupId>
            <artifactId>jboss-servlet-api_4.0_spec</artifactId>
            <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.jboss.spec.javax.annotation</groupId>
            <artifactId>jboss-annotations-api_1.3_spec</artifactId>
            <scope>provided</scope>
        </dependency>
    </dependencies>
    ```

9. Service Bus 계정 정보를 참조 하도록 *pom .xml* 파일의 `azure-webapp-maven-plugin` 구성을 업데이트 합니다. 필요한 경우 `1.7.0`을 [Azure App Service용 Maven 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)의 최신 버전으로 변경합니다.

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}</appServicePlanName>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>

            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>wildfly 14-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>DEFAULT_SBNAMESPACE</name>
                    <value>${DEFAULT_SBNAMESPACE}</value>
                </property>
                <property>
                    <name>SB_SAS_POLICY</name>
                    <value>${SB_SAS_POLICY}</value>
                </property>
                <property>
                    <name>SB_SAS_KEY</name>
                    <value>${SB_SAS_KEY}</value>
                </property>
                <property>
                    <name>PROVIDER_URL</name>
                    <value>${PROVIDER_URL}</value>
                </property>
                <property>
                    <name>SB_QUEUE</name>
                    <value>${SB_QUEUE}</value>
                </property>
                <property>
                    <name>SB_TOPIC</name>
                    <value>${SB_TOPIC}</value>
                </property>
                <property>
                    <name>SB_SUBSCRIPTION</name>
                    <value>${SB_SUBSCRIPTION}</value>
                </property>
            </appSettings>
        </configuration>
    </plugin>
    ```

    이러한 설정은 로컬로 설정 하는 것과 동일한 환경 변수를 갖도록 App Service 인스턴스를 구성 합니다. 환경 변수를 사용 하 여 계정 정보를 원본 파일에서 제외 합니다.

10. 앱을 다시 빌드하고 다시 배포 합니다.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

이제 메시지 기반 bean이 메시징 메커니즘으로 Service Bus를 사용 하도록 구성 되었습니다.

다음 번에 App Service 다시 시작 하면 시작 스크립트가 실행 되 고 필요한 구성 단계가 수행 됩니다. 이 구성이 제대로 수행 되었는지 테스트 하려면 SSH를 사용 하 여 App Service에 액세스 한 다음 Bash 프롬프트에서 직접 시작 스크립트를 실행 하면 됩니다. App Service 로그를 검사할 수도 있습니다. 이러한 옵션에 대 한 자세한 내용은 [앱 로깅 및 디버깅](#logging-and-debugging-apps)을 참조 하세요.

이러한 지침을 테스트 하는 데 사용할 수 있는 샘플은 GitHub의 [마이그레이션-java-ee-app-v-2](https://github.com/Azure-Samples/migrate-java-ee-app-to-azure-2) 리포지토리를 참조 하 고 `helloworld-mdb-propertysubstitution` 샘플을 찾습니다.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Tomcat를 사용 하 여 Redis를 세션 캐시로 사용

[Redis 용 Azure Cache](/azure/azure-cache-for-redis/)와 같은 외부 세션 저장소를 사용 하도록 Tomcat를 구성할 수 있습니다. 그러면 사용자가 앱의 다른 인스턴스로 전송 될 때 (예: 자동 크기 조정, 다시 시작 또는 장애 조치가 발생 하는 경우) 사용자 세션 상태 (예: 쇼핑 카트 데이터)를 유지할 수 있습니다.

Redis와 함께 Tomcat를 사용 하려면 [PersistentManager](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) 구현을 사용 하도록 앱을 구성 해야 합니다. 다음 단계에서는 [Pivotal 세션 관리자](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) 를 사용 하 여이 프로세스를 설명 합니다. redis.

1. Bash 터미널을 열고 `<variable>=<value>`를 사용 하 여 다음의 각 환경 변수를 설정 합니다.

    | 변수                 | Value                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | App Service 인스턴스를 포함 하는 리소스 그룹의 이름입니다.       |
    | WEBAPP_NAME              | App Service 인스턴스의 이름입니다.                                     |
    | WEBAPP_PLAN_NAME         | App Service 계획의 이름입니다.                                         |
    | 지역                   | 앱이 호스트 되는 영역의 이름입니다.                           |
    | REDIS_CACHE_NAME         | Redis 인스턴스에 대 한 Azure 캐시의 이름입니다.                           |
    | REDIS_PORT               | Redis 캐시가 수신 하는 SSL 포트입니다.                             |
    | REDIS_PASSWORD           | 인스턴스에 대 한 기본 액세스 키입니다.                                  |
    | REDIS_SESSION_KEY_PREFIX | 앱에서 제공 하는 세션 키를 식별 하기 위해 지정 하는 값입니다. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    서비스 인스턴스의 **속성** 또는 **액세스 키** 섹션에서 확인 하 여 Azure Portal에 대 한 이름, 포트 및 액세스 키 정보를 찾을 수 있습니다.

2. 다음 콘텐츠를 사용 하 여 앱의 *src/main/webapp/META-INF/context .xml* 파일을 만들거나 업데이트 합니다.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    이 파일은 앱에 대 한 세션 관리자 구현을 지정 하 고 구성 합니다. 이전 단계에서 설정한 환경 변수를 사용 하 여 계정 정보를 원본 파일에서 제외 합니다.

3. FTP를 사용 하 여 세션 관리자의 JAR 파일을 App Service 인스턴스에 업로드 하 고 */home/tomcat/lib* 디렉터리에 배치 합니다. 자세한 내용은 [FTP/S를 사용 하 여 Azure App Service에 앱 배포](https://docs.microsoft.com/azure/app-service/deploy-ftp)를 참조 하세요.

4. App Service 인스턴스에 대 한 [세션 선호도 쿠키](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 를 사용 하지 않도록 설정 합니다. 앱으로 이동한 다음 **구성 > 일반 설정 > ARR 선호도** 를 **Off**로 설정 하 여 Azure Portal에서이 작업을 수행할 수 있습니다. 또는 다음 명령을 사용할 수 있습니다.

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    기본적으로 App Service는 세션 선호도 쿠키를 사용 하 여 기존 세션의 클라이언트 요청이 응용 프로그램의 동일한 인스턴스로 라우팅되도록 합니다. 이 기본 동작에는 구성이 필요 없지만, 앱 인스턴스를 다시 시작 하거나 트래픽을 다른 인스턴스로 다시 라우팅될 때 사용자 세션 상태를 유지할 수 없습니다. [기존 ARR 인스턴스 선호도 구성을 사용 하지 않도록 설정](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 하 여 세션 쿠키 기반 라우팅을 해제 하면 구성 된 세션 저장소가 간섭 없이 작동할 수 있습니다.

5. App Service 인스턴스의 **속성** 섹션으로 이동 하 여 **추가 아웃 바운드 IP 주소**를 찾습니다. 앱에 대 한 모든 가능한 아웃 바운드 IP 주소를 나타냅니다. 다음 단계에서 사용 하기 위해 복사 합니다.

6. 각 IP 주소에 대해 Azure Cache for Redis 인스턴스에 대 한 방화벽 규칙을 만듭니다. Redis 인스턴스의 **방화벽** 섹션에서 Azure Portal에 대해이 작업을 수행할 수 있습니다. 각 규칙에 고유한 이름을 지정 하 고 **시작 ip 주소** 와 **끝 ip 주소** 값을 동일한 IP 주소로 설정 합니다.

7. Redis 인스턴스의 **고급 설정** 섹션으로 이동 하 여 **SSL을 통해서만 액세스 허용** 을 **아니요**로 설정 합니다. 이렇게 하면 App Service 인스턴스가 Azure 인프라를 통해 Redis 캐시와 통신할 수 있습니다.

8. Redis 계정 정보를 참조 하도록 앱의 *pom .xml* 파일에서 `azure-webapp-maven-plugin` 구성을 업데이트 합니다. 이 파일은 이전에 설정한 환경 변수를 사용 하 여 계정 정보를 원본 파일에서 제외 합니다.

    필요한 경우 `1.7.0`을 [Azure App Service용 Maven 플러그 인](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)의 최신 버전으로 변경합니다.

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. 앱을 다시 빌드하고 다시 배포 합니다.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

이제 앱에서 세션 관리에 Redis cache를 사용 합니다.

이러한 지침을 테스트 하는 데 사용할 수 있는 샘플은 GitHub의 [확장-상태 저장-java-웹-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) 리포지토리를 참조 하세요.

## <a name="docker-containers"></a>Docker 컨테이너

컨테이너에 Azure 지원 Zulu JDK를 사용하려면 [Azure에 지원되는 Azul Zulu Enterprise 다운로드 페이지](https://www.azul.com/downloads/azure-only/zulu/)에 나와 있는 미리 작성된 이미지를 끌어와서 사용하거나 [Microsoft Java GitHub 리포지토리](https://github.com/Microsoft/java/tree/master/docker)의 `Dockerfile` 예제를 사용하세요.

## <a name="statement-of-support"></a>지원 문의

### <a name="runtime-availability"></a>런타임 가용성

Linux용 App Service는 Java 웹 애플리케이션의 관리되는 호스팅에 다음과 같은 두 가지 런타임을 지원합니다.

- 웹 보관(WAR) 파일로 패키징된 애플리케이션을 실행하기 위한 [Tomcat 서블릿 컨테이너](https://tomcat.apache.org/). 지원되는 버전은 8.5 및 9.0입니다.
- Java 보관(JAR) 파일로 패키징된 애플리케이션을 실행하기 위한 Java SE 런타임 환경. 지원 되는 버전은 Java 8 및 11입니다.

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

OpenJDK의 Azul Zulu Enterprise 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 여기에는 Java SE 애플리케이션을 빌드하고 실행하기 위한 모든 구성 요소가 포함됩니다. [JAVA Jdk 설치](https://aka.ms/azure-jdks)에서 jdk를 설치할 수 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.

Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.
