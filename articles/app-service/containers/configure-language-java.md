---
title: 리눅스 자바 애플 리케이션을 구성
description: 앱에 대해 미리 빌드된 Java 컨테이너를 구성하는 방법을 알아봅니다. 이 문서에서는 가장 일반적인 구성 작업을 보여줍니다.
keywords: 푸른 응용 프로그램 서비스, 웹 앱, 리눅스, OSS, 자바, 자바 ee, 지, 자바
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245840"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Azure App Service용 Linux Java 앱 구성

Linux의 Azure App Service를 사용하면 Java 개발자가 완전히 관리되는 Linux 기반 서비스에서 Tomcat 또는 SE(Java 표준 버전) 패키지 웹 응용 프로그램을 신속하게 빌드, 배포 및 확장할 수 있습니다. 명령줄에서 또는 IntelliJ, Eclipse, Visual Studio Code 같은 편집기에서 Maven 플러그 인을 사용하여 애플리케이션을 배포할 수 있습니다.

이 가이드는 앱 서비스에서 기본 제공 Linux 컨테이너를 사용하는 Java 개발자를 위한 주요 개념 및 지침을 제공합니다. Azure 앱 서비스를 사용한 적이 없는 경우 [Java 빠른 시작을](quickstart-java.md)따르십시오.

## <a name="deploying-your-app"></a>앱 배포

[Azure 앱 서비스에 대한 Maven 플러그인을](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 사용하여 .jar 및 .war 파일을 모두 배포할 수 있습니다. 인기 있는 IID를 가진 배포는 [IntelliJ용 Azure 도구 키트](/java/azure/intellij/azure-toolkit-for-intellij) 또는 [이클립스용 Azure 도구 키트로도](/java/azure/eclipse/azure-toolkit-for-eclipse)지원됩니다.

그렇지 않으면 배포 방법은 아카이브 유형에 따라 달라집니다.

- .war 파일을 Tomcat에 배포하려면 `/api/wardeploy/` 엔드포인트를 사용하여 보관 파일을 게시합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file)를 참조하세요.
- Java SE 이미지에서 .jar 파일을 배포하려면 Kudu 사이트의 `/api/zipdeploy/` 엔드포인트를 사용합니다. 이 API에 대한 자세한 내용은 [이 설명서](https://docs.microsoft.com/azure/app-service/deploy-zip#rest)를 참조하세요.

FTP를 사용하여 .war 또는.jar을 배포하지 마십시오. FTP 도구는 시작 스크립트, 종속성 또는 기타 런타임 파일을 업로드하기 위해 설계되었습니다. 웹앱을 배포하기 위한 최적의 선택은 아닙니다.

## <a name="logging-and-debugging-apps"></a>앱 로깅 및 디버깅

Azure Portal을 통해 각 앱에 대한 성능 보고서, 트래픽 시각화 및 상태 확인을 사용할 수 있습니다. 자세한 내용은 [Azure 앱 서비스 진단 개요를](../overview-diagnostics.md)참조하십시오.

### <a name="ssh-console-access"></a>SSH 콘솔 액세스

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>진단 로그 스트림

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

자세한 내용은 [클라우드 셸의 로그 스트림 을](../troubleshoot-diagnostic-logs.md#in-cloud-shell)참조하십시오.

### <a name="app-logging"></a>앱 로깅

Azure Portal 또는 [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config)를 통해 [애플리케이션 로깅](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows)을 사용하도록 설정하여 애플리케이션의 표준 콘솔 출력 및 표준 콘솔 오류 스트림을 로컬 파일 시스템 또는 Azure Blob Storage에 쓰도록 App Service를 구성할 수 있습니다. 로컬 App Service 파일 시스템 인스턴스에 로깅하는 동작은 구성된 지 12시간 후에 비활성화 됩니다. 더 긴 시간 동안 보존하기를 원하는 경우 Blob Storage 컨테이너에 출력을 쓰도록 애플리케이션을 구성합니다. 자바 와 Tomcat 응용 프로그램 로그는 */ 홈 / 로그 파일 / 응용 프로그램 /* 디렉토리에서 찾을 수 있습니다.

애플리케이션에서 [Logback](https://logback.qos.ch/) 또는 [Log4j](https://logging.apache.org/log4j)를 추적에 사용하는 경우 [Application Insights에서 Java 추적 로그 탐색](/azure/application-insights/app-insights-java-trace-logs)의 로깅 프레임워크 구성 지침에 따라 이러한 추적 로그를 Azure Application Insights로 전송하여 검토할 수 있습니다.

### <a name="troubleshooting-tools"></a>문제 해결 도구

내장 된 자바 이미지는 [알파인 리눅스](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) 운영 체제를 기반으로합니다. 패키지 `apk` 관리자를 사용하여 문제 해결 도구 또는 명령을 설치합니다.

### <a name="flight-recorder"></a>비행 레코더

앱 서비스의 모든 Linux Java 이미지에는 Zulu 비행 레코더가 설치되어 있어 JVM에 쉽게 연결하여 프로파일러 레코딩을 시작하거나 힙 덤프를 생성할 수 있습니다.

#### <a name="timed-recording"></a>시간 제고

시작하려면 SSH를 앱 서비스에 넣고 `jcmd` 명령을 실행하여 실행 중인 모든 Java 프로세스 목록을 확인합니다. jcmd 자체 외에도 Java 응용 프로그램이 프로세스 ID 번호(pid)로 실행되는 것을 볼 수 있습니다.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

아래 명령을 실행하여 JVM의 30초 녹화를 시작합니다. 이렇게 하면 JVM을 프로파일화하고 홈 디렉토리에서 *jfr_example.jfr라는* JFR 파일을 만듭니다. (116을 Java 앱의 pid로 바꿉니다.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

30초 간격 동안 을 실행하여 `jcmd 116 JFR.check`레코딩이 일어나고 있는지 확인할 수 있습니다. 그러면 지정된 Java 프로세스에 대한 모든 레코딩이 표시됩니다.

#### <a name="continuous-recording"></a>연속 녹화

Zulu Flight 레코더를 사용하여 런타임[성능(소스)에](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)미치는 영향을 최소화하면서 Java 응용 프로그램을 지속적으로 프로파일러만들 수 있습니다. 이렇게 하려면 다음 Azure CLI 명령을 실행하여 필요한 구성을 사용하여 JAVA_OPTS 라는 앱 설정을 만듭니다. JAVA_OPTS 앱 설정의 내용은 앱이 `java` 시작될 때 명령으로 전달됩니다.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

레코딩이 시작되면 명령을 사용하여 언제든지 현재 기록 데이터를 `JFR.dump` 덤프할 수 있습니다.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

자세한 내용은 [Jcmd 명령 참조를](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190)참조하십시오.

### <a name="analyzing-recordings"></a>레코딩 분석

[FTPS를](../deploy-ftp.md) 사용하여 JFR 파일을 로컬 컴퓨터에 다운로드합니다. JFR 파일을 분석하려면 [줄루 미션 컨트롤을](https://www.azul.com/products/zulu-mission-control/)다운로드하여 설치하십시오. 줄루 미션 컨트롤에 대한 지침은 [Azul 설명서](https://docs.azul.com/zmc/) 및 [설치 지침을](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control)참조하십시오.

## <a name="customization-and-tuning"></a>사용자 지정 및 튜닝

Linux용 Azure 앱 서비스는 Azure 포털 및 CLI를 통해 즉시 튜닝 및 사용자 지정을 지원합니다. Java 관련 웹 앱이 아닌 웹 앱 구성에 대한 다음 문서를 검토합니다.

- [앱 설정 구성](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [사용자 지정 도메인 설정](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [SSL 바인딩 구성](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [CDN 추가](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [쿠두 사이트 구성](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Java 런타임 옵션 설정

Tomcat 및 Java SE 환경에서 할당된 메모리 또는 기타 JVM 런타임 옵션을 `JAVA_OPTS` 설정하려면 옵션과 함께 명명된 [앱 설정을](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) 만듭니다. App Service Linux는 시작될 때 이 설정을 Java 런타임에 환경 변수로 전달합니다.

Azure Portal에서, 웹앱의 **애플리케이션 설정** 아래에서 `-Xms512m -Xmx1204m`처럼 추가 설정을 포함하는 `JAVA_OPTS`라고 하는 새 앱 설정을 만듭니다.

Maven 플러그인에서 앱 설정을 구성하려면 Azure 플러그인 섹션에 설정/값 태그를 추가합니다. 다음 예제는 특정 최소 및 최대 Java 힙 크기를 설정합니다.

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

App Service 계획에서 배포 슬롯 하나를 사용하여 단일 애플리케이션을 실행하는 개발자는 다음 옵션을 사용할 수 있습니다.

- B1 및 S1 인스턴스:`-Xms1024m -Xmx1024m`
- B2 및 S2 인스턴스:`-Xms3072m -Xmx3072m`
- B3 및 S3 인스턴스:`-Xms6144m -Xmx6144m`

애플리케이션 힙 설정을 튜닝할 때 App Service 계획 세부 정보를 검토하고 여러 애플리케이션 및 배포 슬롯 요구 사항을 고려하여 최적의 메모리 할당을 찾아보세요.

JAR 응용 프로그램을 배포하는 경우 기본 제공 이미지가 앱을 올바르게 식별할 수 있도록 *app.jar라는* 이름을 지정해야 합니다. (메이븐 플러그인은 자동으로 이름을 바꾸기 를 수행합니다.) JAR 이름을 *app.jar로*바꾸지 않으려면 JAR을 실행하는 명령이 있는 셸 스크립트를 업로드할 수 있습니다. 그런 다음, 포털의 구성 섹션에 있는 [시작 파일](app-service-linux-faq.md#built-in-images) 텍스트 상자에 이 스크립트의 전체 경로를 붙여넣습니다. 시작 스크립트는 배치된 디렉터리에서 실행되지 않습니다. 따라서 항상 절대 경로를 사용하여 시작 스크립트의 파일을 참조해야 합니다(예: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>시작 시간 시간 조정

Java 응용 프로그램이 특히 큰 경우 시작 시간 제한을 늘려야 합니다. 이렇게 하려면 응용 프로그램 설정을 `WEBSITES_CONTAINER_START_TIME_LIMIT` 만들고 앱 서비스가 시간 만기 전에 기다려야 하는 초(초)로 설정합니다. 최대값은 `1800` 초입니다.

### <a name="pre-compile-jsp-files"></a>JSP 파일 사전 컴파일

Tomcat 응용 프로그램의 성능을 향상시키기 위해 앱 서비스에 배포하기 전에 JSP 파일을 컴파일할 수 있습니다. 당신은 아파치 슬링에서 제공하는 [메이븐 플러그인을](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) 사용할 수 있습니다, 또는이 [개미 빌드 파일을](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation)사용하여 .

## <a name="secure-applications"></a>보안 애플리케이션

Linux용 App Service에서 실행되는 Java 애플리케이션의 [보안 모범 사례](/azure/security/security-paas-applications-using-app-services) 집합은 다른 애플리케이션과 동일합니다.

### <a name="authenticate-users-easy-auth"></a>사용자 인증(이지 인증)

**인증 및 권한 부여** 옵션을 사용 하 고 Azure 포털에서 앱 인증을 설정 합니다. 여기서 Azure Active Directory 또는 Facebook, Google, GitHub 등의 소셜 로그인을 사용하여 인증을 사용하도록 설정할 수 있습니다. Azure Portal 구성은 단일 인증 공급자를 구성할 때만 작동합니다. 자세한 내용은 [Azure Active Directory 로그인을 사용하도록 App Service 앱 구성](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) 및 기타 ID 공급자 관련 문서를 참조하세요. 여러 로그인 공급자를 사용하도록 설정해야 하는 경우 [App Service 인증 사용자 지정](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json) 문서의 지침을 따르세요.

#### <a name="tomcat"></a>Tomcat

Tomcat 응용 프로그램은 주 오브젝트를 맵 개체로 캐스팅하여 서브릿에서 직접 사용자의 클레임에 액세스할 수 있습니다. Map 개체는 각 클레임 유형을 해당 형식에 대한 클레임 컬렉션에 매핑합니다. 아래 코드에서 `request` 의 `HttpServletRequest`인스턴스입니다.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

이제 특정 클레임에 대한 개체를 `Map` 검사할 수 있습니다. 예를 들어 다음 코드 조각은 모든 클레임 유형을 반복하고 각 컬렉션의 내용을 인쇄합니다.

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

사용자를 로그아웃하려면 `/.auth/ext/logout` 경로를 사용합니다. 다른 작업을 수행하려면 [앱 서비스 인증 및 권한 부여 사용에](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to)대한 설명서를 참조하십시오. 또한 Tomcat [HttpServletRequest 인터페이스와](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) 그 방법에 대한 공식 문서가 있습니다. 다음 서발 메서드는 앱 서비스 구성에 따라 수화됩니다.

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

이 기능을 사용하지 않도록 설정하려면 `WEBSITE_AUTH_SKIP_PRINCIPAL` `1`값으로 명명된 응용 프로그램 설정을 만듭니다. App Service에서 추가한 모든 서플릿 필터를 `WEBSITE_SKIP_FILTERS` 사용하지 않도록 `1`설정하려면 값으로 명명된 설정을 만듭니다.

#### <a name="spring-boot"></a>Spring Boot

Spring Boot 개발자는 [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable)를 사용하여 친숙한 Spring Security 주석 및 API로 애플리케이션을 보호할 수 있습니다. *application.properties* 파일에서 최대 헤더 크기를 늘려야 합니다. 이 값은 `16384`로 설정하는 것이 좋습니다.

### <a name="configure-tlsssl"></a>TLS/SSL 구성

[Azure App Service에서 SSL 바인딩이 있는 사용자 지정 DNS 이름 보안의](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) 지침에 따라 기존 SSL 인증서를 업로드하고 응용 프로그램의 도메인 이름에 바인딩합니다. 기본적으로 애플리케이션에서 HTTP 연결을 계속 허용합니다. 자습서의 단계에 따라 SSL 및 TLS를 적용하세요.

### <a name="use-keyvault-references"></a>키볼트 참조 사용

[Azure KeyVault는](../../key-vault/key-vault-overview.md) 액세스 정책 및 감사 기록을 통해 중앙 집중식 비밀 관리를 제공합니다. KeyVault에 암호(예: 암호 또는 연결 문자열)를 저장하고 환경 변수를 통해 응용 프로그램에서 이러한 비밀에 액세스할 수 있습니다.

먼저 키 자격 [증명 모음에](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) 대한 앱 액세스 권한을 부여하고 [응용 프로그램 설정에서 비밀에 대한 KeyVault 참조를 만드는](../app-service-key-vault-references.md#reference-syntax)방법에 대한 지침을 따릅니다. App Service 터미널에 원격으로 액세스하는 동안 환경 변수를 인쇄하여 참조가 비밀로 확인되는지 확인할 수 있습니다.

스프링 또는 Tomcat 구성 파일에 이러한 비밀을 삽입하려면 환경`${MY_ENV_VAR}`변수 주입 구문()을 사용합니다. 스프링 구성 파일의 경우 [외부 구성에](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)대한 이 설명서를 참조하십시오.

### <a name="using-the-java-key-store"></a>자바 키 저장소 사용

기본적으로 [앱 서비스 Linux에 업로드된](../configure-ssl-certificate.md) 모든 공개 또는 비공개 인증서는 컨테이너가 시작될 때 각 Java 키 저장소에 로드됩니다. 인증서를 업로드한 후 Java 키 저장소에 로드하려면 앱 서비스를 다시 시작해야 합니다. 공용 인증서는 의 키 저장소에 `$JAVA_HOME/jre/lib/security/cacerts`로드되고 개인 인증서는 에 `$JAVA_HOME/lib/security/client.jks`저장됩니다.

Java 키 저장소의 인증서와 JDBC 연결을 암호화하려면 추가 구성이 필요할 수 있습니다. 선택한 JDBC 드라이버에 대한 설명서를 참조하십시오.

- [Postgresql](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [Mysql](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Java 키 저장소 초기화

개체를 `import java.security.KeyStore` 초기화하려면 키스토어 파일을 암호로 로드합니다. 두 키 저장소의 기본 암호는 "changeit"입니다.

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

#### <a name="manually-load-the-key-store"></a>키 저장소를 수동으로 로드합니다.

인증서를 키 저장소에 수동으로 로드할 수 있습니다. 앱 서비스가 인증서를 키 저장소에 자동으로 로드하지 못하도록 설정하는 값을 `SKIP_JAVA_KEYSTORE_LOAD` `1` 가진 앱 설정을 만듭니다. Azure 포털을 통해 앱 서비스에 업로드된 모든 `/var/ssl/certs/`공용 인증서는 에 저장됩니다. 개인 인증서는 아래에 `/var/ssl/private/`저장됩니다.

앱 서비스에 [대한 SSH 연결을 열고](app-service-linux-ssh-support.md) 명령을 `keytool`실행하여 Java 키 도구를 상호 작용하거나 디버깅할 수 있습니다. 명령 목록은 [키 도구 설명서를](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) 참조하십시오. KeyStore API에 대한 자세한 내용은 [공식 설명서를](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html)참조하십시오.

## <a name="configure-apm-platforms"></a>APM 플랫폼 구성

이 섹션에서는 Linux의 Azure 앱 서비스에 배포된 Java 응용 프로그램을 NewRelic 및 AppDynamics 응용 프로그램 성능 모니터링(APM) 플랫폼과 연결하는 방법을 보여 주며 있습니다.

### <a name="configure-new-relic"></a>New Relic 구성

1. [NewRelic.com](https://newrelic.com/signup)에서 NewRelic 계정 만들기
2. NewRelic에서 자바 에이전트를 다운로드, 그것은 뉴 *렐릭 자바 x.x.x.zip와*유사한 파일 이름을해야합니다 .
3. 라이선스 키를 복사합니다. 이 키는 나중에 에이전트를 구성하는 데 필요합니다.
4. [앱 서비스 인스턴스에 SSH를](app-service-linux-ssh-support.md) 입력하고 새 디렉토리 */홈/사이트/wwwroot/apm을*만듭니다.
5. */home/site/wwwroot/apm*아래의 디렉토리에 압축되지 않은 NewRelic Java 에이전트 파일을 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/newrelic에*있어야 합니다.
6. */home/site/wwwroot/apm/newrelic/newrelic.yml에서* YAML 파일을 수정하고 자리 표시자 라이센스 값을 사용자 고유의 라이센스 키로 바꿉습니다.
7. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - 앱이 **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `JAVA_OPTS`라는 환경 변수를 만듭니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다.

### <a name="configure-appdynamics"></a>AppDynamics 구성

1. [AppDynamics.com](https://www.appdynamics.com/community/register/)에서 AppDynamics 계정 만들기
2. AppDynamics 웹 사이트에서 자바 에이전트를 다운로드하면 파일 이름이 *AppServerAgent-x.x.x.xxxxx.zip과 유사합니다.*
3. [앱 서비스 인스턴스에 SSH를](app-service-linux-ssh-support.md) 입력하고 새 디렉토리 */홈/사이트/wwwroot/apm을*만듭니다.
4. 자바 에이전트 파일을 */home/site/wwwroot/apm*아래의 디렉토리에 업로드합니다. 에이전트의 파일은 */home/site/wwwroot/apm/appdynamics에*있어야 합니다.
5. Azure Portal의 App Service에서 사용자 애플리케이션을 찾아 새 애플리케이션 설정을 만듭니다.
    - **Java SE**를 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `JAVA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.
    - **Tomcat**을 사용하는 경우 값이 `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>`인 `CATALINA_OPTS`라는 환경 변수를 만듭니다. 여기서 `<app-name>`은 App Service 이름입니다.

> [!NOTE]
> `JAVA_OPTS` 또는 `CATALINA_OPTS`에 대한 환경 변수가 이미 있는 경우 현재 값의 끝에 `-javaagent:/...` 옵션을 추가합니다.

## <a name="configure-jar-applications"></a>JAR 응용 프로그램 구성

### <a name="starting-jar-apps"></a>JAR 앱 시작

기본적으로 앱 서비스는 JAR 응용 프로그램의 이름을 *app.jar로*예상합니다. 이 이름이 있으면 자동으로 실행됩니다. Maven 사용자의 경우 *pom.xml* `<finalName>app</finalName>` `<build>` 섹션에 포함시켜 JAR 이름을 설정할 수 있습니다. 속성을 설정 하여 [Gradle에서 동일한 작업을 수행할 수 있습니다.](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) `archiveFileName`

JAR에 다른 이름을 사용하려면 JAR 파일을 실행하는 [시작 명령도](app-service-linux-faq.md#built-in-images) 제공해야 합니다. `java -jar my-jar-app.jar`)을 입력합니다. 포털, 구성 > 일반 설정 또는 응용 프로그램 설정에서 .라는 `STARTUP_COMMAND`응용 프로그램 설정을 사용 하 고 시작 명령에 대 한 값을 설정할 수 있습니다.

### <a name="server-port"></a>서버 포트

앱 서비스 Linux는 들어오는 요청을 포트 80으로 라우팅하므로 응용 프로그램도 포트 80에서 수신 해야 합니다. 응용 프로그램의 구성(예: Spring의 *application.properties* 파일) 또는 시작 명령(예: `java -jar spring-app.jar --server.port=80`시작 명령)에서 이 작업을 수행할 수 있습니다. 일반적인 Java 프레임워크에 대한 다음 설명서를 참조하십시오.

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [스파크자바](http://sparkjava.com/documentation#embedded-web-server)
- [마이크로나우트 (주)](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [프레임워크 재생](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [버트 (주)](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [쿼쿠스 (주)](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>데이터 원본

### <a name="tomcat"></a>Tomcat

이러한 지침은 데이터베이스 연결에 적용됩니다. 선택한 데이터베이스의 드라이버 클래스 이름 및 JAR 파일로 자리 표시자를 채워야 합니다. 공통 데이터베이스에 대한 클래스 이름 및 드라이버 다운로드가 포함된 표가 제공됩니다.

| 데이터베이스   | 드라이버 클래스 이름                             | JDBC 드라이버                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [다운로드](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [다운로드](https://dev.mysql.com/downloads/connector/j/)(“플랫폼 독립적” 선택) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [다운로드](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Tomcat을 Java 데이터베이스 연결(JDBC) 또는 JPA(Java 지속성 API)를 `CATALINA_OPTS` 사용하도록 구성하려면 먼저 시작 시 Tomcat에서 읽은 환경 변수를 사용자 지정합니다. [App Service Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)에서 앱 설정을 통해 이러한 값을 설정합니다.

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

또는 Azure 포털의 **구성** > **응용 프로그램 설정** 페이지에서 환경 변수를 설정합니다.

다음으로, 데이터 원본을 한 애플리케이션에만 제공할 것인지 또는 Tomcat 서블릿에서 실행 중인 모든 애플리케이션에 제공할 것인지 결정합니다.

#### <a name="application-level-data-sources"></a>응용 프로그램 수준 데이터 원본

1. 프로젝트의 *META-INF/디렉토리에* *context.xml* 파일을 만듭니다. *메타-INF/디렉토리가* 존재하지 않는 경우 만듭니다.

2. *context.xml에서*JNDI 주소에 데이터 원본을 연결하는 `Context` 요소를 추가합니다. `driverClassName` 자리 표시자를 위 테이블에 있는 드라이버의 클래스 이름으로 바꿉니다.

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

3. 응용 프로그램의 데이터 원본을 사용하도록 응용 프로그램의 *web.xml을* 업데이트합니다.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>공유 서버 수준 리소스

공유 서버 수준 데이터 원본을 추가하려면 Tomcat의 server.xml을 편집해야 합니다. 먼저 시작 [스크립트를](app-service-linux-faq.md#built-in-images) 업로드하고 **구성** > **시작 명령에서**스크립트에 대한 경로를 설정합니다. [FTP를](../deploy-ftp.md)사용하여 시작 스크립트를 업로드할 수 있습니다.

시작 스크립트는 [xsl을](https://www.w3schools.com/xml/xsl_intro.asp) server.xml 파일로 변환하고 결과 xml `/usr/local/tomcat/conf/server.xml`파일을 로 출력합니다. 시작 스크립트는 APK를 통해 libxslt를 설치해야합니다. xsl 파일 및 시작 스크립트는 FTP를 통해 업로드할 수 있습니다. 다음은 예제 시작 스크립트입니다.

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

마지막으로 Tomcat 클래스 경로에 드라이버 JA를 배치하고 앱 서비스를 다시 시작합니다.

1. JDBC 드라이버 파일을 */home/tomcat/lib* 디렉토리에 배치하여 Tomcat 클래스 로더에서 사용할 수 있는지 확인합니다. 이 디렉토리가 아직 없는 경우 이 디렉토리를 만듭니다. 이러한 파일을 앱 서비스 인스턴스에 업로드하려면 다음 단계를 수행합니다.

    1. 클라우드 [셸에서](https://shell.azure.com)웹앱 확장을 설치합니다.

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. 다음 CLI 명령을 실행하여 로컬 시스템에서 앱 서비스로 SSH 터널을 만듭니다.

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. SFTP 클라이언트를 사용하여 로컬 터널링 포트에 연결하고 *파일을 /home/tomcat/lib* 폴더에 업로드합니다.

    또는 FTP 클라이언트를 사용하여 JDBC 드라이버를 업로드할 수 있습니다. [FTP 자격 증명을 가져오기 위한 이러한 지침](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json)을 따릅니다.

2. 서버 수준 데이터 원본을 만든 경우 App Service Linux 애플리케이션을 다시 시작합니다. Tomcat이 `CATALINA_BASE`을 `/home/tomcat`로 다시 설정하고 업데이트된 구성을 사용합니다.

### <a name="spring-boot"></a>Spring Boot

Spring Boot 응용 프로그램의 데이터 원본에 연결하려면 연결 문자열을 만들고 *application.properties* 파일에 삽입하는 것이 좋습니다.

1. 앱 서비스 페이지의 "구성" 섹션에서 문자열의 이름을 설정하고 JDBC 연결 문자열을 값 필드에 붙여넣고 형식을 "사용자 지정"으로 설정합니다. 선택적으로 이 연결 문자열을 슬롯 설정으로 설정할 수 있습니다.

    이 연결 문자열은 응용 프로그램에서 환경 `CUSTOMCONNSTR_<your-string-name>`변수라는 이름으로 액세스할 수 있습니다. 예를 들어 위에서 만든 연결 문자열의 `CUSTOMCONNSTR_exampledb`이름이 지정됩니다.

2. *application.properties* 파일에서 환경 변수 이름으로 이 연결 문자열을 참조합니다. 이 예제에서는 다음을 사용합니다.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

이 항목에 대한 자세한 내용은 데이터 액세스 및 [외부 구성에](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) [대한 Spring Boot 설명서를](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) 참조하십시오.

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Tomcat을 사용하여 Redis를 세션 캐시로 사용

Redis 에 대 한 [Azure 캐시와](/azure/azure-cache-for-redis/)같은 외부 세션 저장소를 사용 하 여 Tomcat를 구성할 수 있습니다. 이렇게 하면 사용자가 앱의 다른 인스턴스로 전송될 때(예: 자동 크기 조정, 다시 시작 또는 장애 조치가 발생할 때) 사용자 세션 상태(예: 장바구니 데이터)를 유지할 수 있습니다.

Redis와 함께 Tomcat을 사용하려면 [영구 관리자](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) 구현을 사용하도록 앱을 구성해야 합니다. 다음 단계에서는 Pivotal [세션 관리자를](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) 사용하여 이 프로세스를 설명합니다.

1. Bash 터미널을 열고 `<variable>=<value>` 다음 환경 변수를 각각 설정하는 데 사용합니다.

    | 변수                 | 값                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | 앱 서비스 인스턴스를 포함하는 리소스 그룹의 이름입니다.       |
    | WEBAPP_NAME              | 앱 서비스 인스턴스의 이름입니다.                                     |
    | WEBAPP_PLAN_NAME         | 앱 서비스 계획의 이름입니다.                                         |
    | 지역                   | 앱이 호스팅되는 지역의 이름입니다.                           |
    | REDIS_CACHE_NAME         | Redis 인스턴스에 대한 Azure 캐시의 이름입니다.                           |
    | REDIS_PORT               | Redis 캐시가 수신하는 SSL 포트입니다.                             |
    | REDIS_PASSWORD           | 인스턴스의 기본 액세스 키입니다.                                  |
    | REDIS_SESSION_KEY_PREFIX | 앱에서 제공되는 세션 키를 식별하기 위해 지정한 값입니다. |

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

    서비스 인스턴스의 **속성** 또는 **액세스 키** 섹션을 참조하여 Azure Portal에서 이름, 포트 및 액세스 키 정보를 찾을 수 있습니다.

2. 다음 콘텐츠로 앱의 *src/main/webapp/META-INF/context.xml* 파일을 만들거나 업데이트합니다.

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

    이 파일은 앱에 대한 세션 관리자 구현을 지정하고 구성합니다. 이전 단계에서 설정한 환경 변수를 사용하여 계정 정보를 원본 파일에서 제거합니다.

3. FTP를 사용하여 세션 관리자의 JAR 파일을 앱 서비스 인스턴스에 업로드하여 */home/tomcat/lib* 디렉터리에 배치합니다. 자세한 내용은 [FTP/S를 사용하여 Azure 앱 서비스에 앱 배포를](https://docs.microsoft.com/azure/app-service/deploy-ftp)참조하세요.

4. 앱 서비스 인스턴스에 대한 [세션 선호도 쿠키를](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 비활성화합니다. 앱으로 이동한 다음 **ARR 선호도를** **해제로**> 구성 > 일반 설정을 설정하여 Azure 포털에서 이 작업을 수행할 수 있습니다. 또는 다음 명령을 사용할 수 있습니다.

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    기본적으로 App Service는 세션 선호도 쿠키를 사용하여 기존 세션이 있는 클라이언트 요청이 응용 프로그램의 동일한 인스턴스로 라우팅되도록 합니다. 이 기본 동작은 구성이 필요하지 않지만 앱 인스턴스가 다시 시작되거나 트래픽이 다른 인스턴스로 다시 라우팅될 때 사용자 세션 상태를 유지할 수 없습니다. 세션 쿠키 기반 라우팅을 끄기 위해 [기존 ARR 인스턴스 선호도](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) 구성을 사용하지 않도록 설정하면 구성된 세션 저장소가 간섭 없이 작동하도록 허용합니다.

5. 앱 서비스 인스턴스의 **속성** 섹션으로 이동하여 **추가 아웃바운드 IP 주소를 찾습니다.** 앱에 대해 가능한 모든 아웃바운드 IP 주소를 나타냅니다. 다음 단계에서 사용할 수 있습니다.

6. 각 IP 주소에 대해 Redis 인스턴스에 대한 Azure 캐시에 방화벽 규칙을 만듭니다. Redis 인스턴스의 **방화벽** 섹션에서 Azure 포털에서 이 작업을 수행할 수 있습니다. 각 규칙에 대해 고유한 이름을 제공하고 **시작 IP 주소** 및 종료 IP **주소** 값을 동일한 IP 주소로 설정합니다.

7. Redis 인스턴스의 **고급 설정** 섹션으로 이동하여 **SSL을 통해서만 액세스 허용을** **아니요로**설정합니다. 이렇게 하면 앱 서비스 인스턴스가 Azure 인프라를 통해 Redis 캐시와 통신할 수 있습니다.

8. 앱의 `azure-webapp-maven-plugin` *pom.xml* 파일의 구성을 업데이트하여 Redis 계정 정보를 참조합니다. 이 파일은 이전에 설정한 환경 변수를 사용하여 계정 정보를 원본 파일에서 제거합니다.

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

9. 앱을 다시 빌드하고 다시 배포합니다.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

이제 앱에서 세션 관리를 위해 Redis 캐시를 사용합니다.

이러한 지침을 테스트하는 데 사용할 수 있는 샘플은 GitHub에서 [크기 조정 상태 조정-상태 조정-java-웹-앱-온-azure](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) 리포지토리를 참조하세요.

## <a name="docker-containers"></a>Docker 컨테이너

컨테이너에 Azure 지원 Zulu JDK를 사용하려면 [Azure에 지원되는 Azul Zulu Enterprise 다운로드 페이지](https://www.azul.com/downloads/azure-only/zulu/)에 나와 있는 미리 작성된 이미지를 끌어와서 사용하거나 [Microsoft Java GitHub 리포지토리](https://github.com/Microsoft/java/tree/master/docker)의 `Dockerfile` 예제를 사용하세요.

## <a name="statement-of-support"></a>지원 서

### <a name="runtime-availability"></a>런타임 가용성

Linux용 App Service는 Java 웹 애플리케이션의 관리되는 호스팅에 다음과 같은 두 가지 런타임을 지원합니다.

- 웹 보관(WAR) 파일로 패키징된 애플리케이션을 실행하기 위한 [Tomcat 서블릿 컨테이너](https://tomcat.apache.org/). 지원되는 버전은 8.5 및 9.0입니다.
- Java 보관(JAR) 파일로 패키징된 애플리케이션을 실행하기 위한 Java SE 런타임 환경. 지원되는 버전은 Java 8 및 11입니다.

### <a name="jdk-versions-and-maintenance"></a>JDK 버전 및 유지 관리

OpenJDK의 Azul Zulu Enterprise 빌드는 Microsoft와 Azul Systems가 후원하는 Azure 및 Azure Stack에 대한 OpenJDK의 무료 다중 플랫폼 프로덕션 준비 배포입니다. 여기에는 Java SE 애플리케이션을 빌드하고 실행하기 위한 모든 구성 요소가 포함됩니다. [Java JDK 설치에서 JDK를](https://aka.ms/azure-jdks)설치할 수 있습니다.

지원되는 JDK는 매년 분기마다 1월, 4월, 7월, 10월에 자동으로 패치됩니다.

### <a name="security-updates"></a>보안 업데이트

Azul Systems에서 주요 보안 취약점에 대한 패치 및 수정 사항을 출시하는 즉시 고객에게 제공됩니다. [NIST Common Vulnerability Scoring System 버전 2](https://nvd.nist.gov/cvss.cfm)에서 기본 점수 9.0 이상을 받으면 "주요" 취약점으로 정의됩니다.

### <a name="deprecation-and-retirement"></a>사용 중단 및 사용 중지

지원되는 Java 런타임이 폐기되는 경우 폐기 예정인 런타임을 사용하는 Azure 개발자에게는 적어도 런타임 폐기 6개월 전에 사용 중단 알림이 제공됩니다.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>다음 단계

[Java 개발자용 Azure](/java/azure/) 센터를 방문하여 Azure 빠른 시작, 자습서 및 Java 참조 설명서를 찾아보세요.

Java 개발에 국한되지 않는 Linux용 App Service 사용에 대한 일반적인 질문의 답은 [App Service Linux FAQ](app-service-linux-faq.md)에서 찾을 수 있습니다.
