---
title: Linux 기반의 Azure App Service에서 Java Enterprise 웹앱 빌드 | Microsoft Docs
description: Linux 기반 Azure App Service의 Wildfly에서 작동하는 Java Enterprise 앱을 만드는 방법을 알아봅니다.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.openlocfilehash: 40bee31b7880a323a48e92912ee323c43c3a97da
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51634780"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>자습서: Azure에서 Java EE 및 Postgres 웹앱 빌드

이 자습서에서는 Azure App Service에서 Java EE(Enterprise Edition) 웹앱을 만들어서 Postgres 데이터베이스에 연결하는 방법을 보여줍니다. 자습서를 마치면 [Linux용 App Service](app-service-linux-intro.md)에서 실행되는 [Postgres용 Azure 데이터베이스](https://azure.microsoft.com/services/postgresql/)에 데이터를 저장하는 [WildFly](http://www.wildfly.org/about/) 애플리케이션이 생깁니다.

이 자습서에서는 다음 방법을 알아봅니다.
> [!div class="checklist"]
> * Maven을 사용하여 Azure에 Java EE 앱 배포
> * Azure에서 Postgres 데이터베이스 만들기
> * Postgres를 사용하도록 WildFly 서버 구성
> * 앱 업데이트 및 다시 배포
> * WildFly에서 단위 테스트 실행

## <a name="prerequisites"></a>필수 조건

1. [Git 다운로드 및 설치](https://git-scm.com/)
1. [Maven 3를 다운로드하여 설치](https://maven.apache.org/install.html)
1. [Azure CLI를 다운로드하여 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>샘플 앱 복제 및 편집

이 단계에서는 샘플 애플리케이션을 복제하고 배포를 위한 Maven 프로젝트 개체 모델(POM 또는 pom.xml)을 구성할 것입니다.

### <a name="clone-the-sample"></a>샘플 복제

터미널 창에서 작업 디렉터리로 이동하여 [샘플 리포지토리](https://github.com/Azure-Samples/wildfly-petstore-quickstart)를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Maven POM 업데이트

Maven POM을 원하는 App Service 이름 및 리소스 그룹으로 업데이트합니다. 이러한 값은 _pom.xml_ 파일의 더 깊은 곳에 있는 Azure 플러그 인에 삽입됩니다. App Service 계획 또는 인스턴스를 미리 만들 필요가 없습니다. Maven 플러그 인이 리소스 그룹 및 App Service를 새로 만듭니다(아직 없는 경우).

자리 표시자를 원하는 리소스 이름으로 바꿉니다.
```xml
<azure.plugin.appname>YOUR_APP_NAME</azure.plugin.appname>
<azure.plugin.resourcegroup>YOUR_RESOURCE_GROUP</azure.plugin.resourcegroup>
```

_pom.xml_의 `<plugins>` 섹션으로 스크롤하여 Azure 플러그 인을 검사할 수 있습니다.

## <a name="build-and-deploy-the-application"></a>응용 프로그램 빌드 및 배포

이제 Maven을 사용하여 애플리케이션을 빌드하고 App Service에 배포할 것입니다.

### <a name="build-the-war-file"></a>.war 파일 빌드

이 프로젝트의 POM은 애플리케이션을 웹 보관(WAR) 파일로 패키징합니다. Maven을 사용하여 애플리케이션 빌드:

```bash
mvn clean install -DskipTests
```

이 애플리케이션의 테스트 사례는 WildFly에 애플리케이션을 배포할 때 실행되도록 설계되었습니다. 로컬로 빌드하는 테스트를 건너뛰고, App Service에 애플리케이션이 배포되면 테스트를 실행하겠습니다.

### <a name="deploy-to-app-service"></a>App Service에 배포

WAR 파일이 준비되었으니, Azure 플러그 인을 사용하여 App Service에 배포할 수 있습니다.

```bash
mvn azure-webapp:deploy
```

배포가 완료되면 다음 단계를 계속 진행합니다.

### <a name="create-a-record"></a>레코드 만들기

브라우저를 열고 `https://<your_app_name>.azurewebsites.net/` 로 이동합니다. 축하합니다. Azure App Service에 Java EE 애플리케이션을 배포하셨습니다.

현재 이 애플리케이션은 메모리 내 H2 데이터베이스를 사용합니다. 탐색 모음에서 "관리자"를 클릭하고 새 범주를 만듭니다. App Service 인스턴스를 다시 시작하면 메모리 내 데이터베이스의 레코드가 손실됩니다. 다음 단계에서는 Azure에 Postgres 데이터베이스를 프로비전하고 이 데이터베이스를 사용하도록 WildFly를 구성하여 이 문제를 해결하겠습니다.

![관리자 단추 위치](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Postgres 데이터베이스 프로비전

Postgres 데이터베이스 서버를 프로비전하려면 터미널을 열고 원하는 서버 이름, 사용자 이름, 암호 및 위치 값을 사용하여 다음 명령을 실행합니다. App Service가 있는 동일한 리소스 그룹을 사용합니다. 나중에 사용할 수 있도록 암호를 기록해 두세요!

```bash
az postgres server create -n <desired-name> -g <same-resource-group> --sku-name GP_Gen4_2 -u <desired-username> -p <desired-password> -l <location>
```

포털로 이동하여 Postgres 데이터베이스를 검색합니다. 블레이드가 나타나면 "서버 이름" 및 "서버 관리자 로그인 이름" 값을 복사합니다. 나중에 필요합니다.

### <a name="allow-access-to-azure-services"></a>Azure 서비스에 대한 액세스 허용

Azure 데이터베이스 블레이드의 **연결 보안** 패널에서 "Azure 서비스에 대한 액세스 허용" 단추를 **켜기** 위치로 전환합니다.

![Azure 서비스에 대한 액세스 허용](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Postgres용 Java 앱 업데이트

이제 Postgres 데이터베이스를 사용할 수 있도록 Java 애플리케이션을 약간 변경하겠습니다.

### <a name="add-postgres-credentials-to-the-pom"></a>POM에 Postgres 자격 증명 추가

_pom.xml_에서 자리 표시자 값을 Postgres 서버 이름, 관리자 로그인 이름 및 암호로 바꿉니다. 이러한 값은 애플리케이션을 다시 배포할 때 App Service 인스턴스의 환경 변수로 삽입됩니다.

```xml
<azure.plugin.postgres-server-name>SERVER_NAME</azure.plugin.postgres-server-name>
<azure.plugin.postgres-username>USERNAME@FIRST_PART_OF_SERVER_NAME</azure.plugin.postgres-username>
<azure.plugin.postgres-password>PASSWORD</azure.plugin.postgres-password>
```

### <a name="update-the-java-transaction-api"></a>Java Transaction API 업데이트

다음으로, Java 애플리케이션이 우리가 앞에서 사용한 메모리 내 H2 데이터베이스가 아닌 Postgres와 통신하도록 Java Transaction API(JPA) 구성을 편집해야 합니다. 편집기를 _src/main/resources/META-INF/persistence.xml_로 엽니다. `<jta-data-source>`의 값을 `java:jboss/datasources/postgresDS`로 바꿉니다. 이제 JTA XML의 설정이 다음과 같습니다.

```xml
...
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
...
```

## <a name="configure-the-wildfly-application-server"></a>WildFly 애플리케이션 서버 구성

다시 구성된 애플리케이션을 배포하기 전에, WildFly 애플리케이션 서버를 Postgres 모듈 및 해당 종속성으로 업데이트해야 합니다. 서버를 구성하려면 `wildfly_config/` 디렉터리에 있는 4개 파일이 필요합니다.

- **postgresql-42.2.5.jar**: 이 JAR 파일은 Postgres용 JDBC 드라이버입니다. 자세한 내용은 [공식 웹 사이트](https://jdbc.postgresql.org/index.html)를 참조하세요.
- **postgres-module.xml**: 이 XML 파일은 Postgres 모듈의 이름을 선언합니다(org.postgres). 모듈을 사용하는 데 필요한 리소스와 종속성도 지정합니다.
- **jboss_cli_commands.cl**: 이 파일은 JBoss CLI에서 실행될 구성 명령을 포함합니다. WildFly 애플리케이션 서버에 Postgres 모듈을 추가하고, 자격 증명을 제공하고, JNDI 이름을 선언하고, 시간 제한 임계값을 설정하는 등의 명령을 포함합니다. JBoss CLI를 잘 모르는 경우 [공식 설명서](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli)를 참조하세요.
- **startup_script.sh**: 마지막으로, 이 셸 스크립트는 App Service 인스턴스가 시작될 때마다 실행됩니다. 이 스크립트는 `jboss_cli_commands.cli`의 명령을 JBoss CLI로 파이핑하는 한 가지 기능만 수행합니다.

이러한 파일의 콘텐츠, 특히 _jboss_cli_commands.cli_를 꼭 읽어보시기 바랍니다.

### <a name="ftp-the-configuration-files"></a>구성 파일을 FTP로 전송

FTP를 통해 `wildfly_config/`의 콘텐츠를 App Service 인스턴스로 전송해야 합니다. FTP 자격 증명을 가져오려면 Azure Portal의 App Service 블레이드에서 **게시 프로필 가져오기** 단추를 클릭합니다. 다운로드한 XML 문서를 보면 FTP 사용자 이름 및 암호가 있습니다. 게시 프로필에 대한 자세한 내용은 [이 문서](https://docs.microsoft.com/azure/app-service/app-service-deployment-credentials)를 참조하세요.

원하는 FTP 도구를 사용하여 `wildfly_config/`의 4개 파일을 `/home/site/deployments/tools/`에 전송합니다. (디렉터리가 아닌 파일만 전송해야 합니다.)


### <a name="finalize-app-service"></a>App Service 완료

App Service 블레이드에서 "애플리케이션 설정" 패널로 이동합니다. "런타임"에서 "시작 파일" 필드를 `/home/site/deployments/tools/startup_script.sh`로 설정합니다. 이렇게 하면 App Service 인스턴스가 만들어진 후, 하지만 WildFly 서버가 시작되기 전에 셸 스크립트가 실행됩니다.

마지막으로, App Service를 다시 시작합니다. 이 단추는 "개요" 창에 있습니다.

## <a name="redeploy-the-application"></a>애플리케이션 다시 배포

터미널 창에서 애플리케이션을 다시 빌드하여 다시 배포합니다.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

축하합니다! 이제 애플리케이션이 Postgres 데이터베이스를 사용하며 애플리케이션에서 생성된 레코드가 이전의 H3 메모리 내 데이터베이스 대신 Postgres에 저장됩니다. 레코드를 만들고 App Service를 다시 시작해보면 이 사실을 확인할 수 있습니다. 애플리케이션이 다시 시작되어도 레코드가 그 자리에 있습니다.

## <a name="clean-up"></a>정리

다른 자습서에서 이러한 리소스가 필요하지 않으면(다음 단계 참조) 다음 명령을 실행하여 삭제할 수 있습니다.

```bash
az group delete --name <your_resource_group> 
```

## <a name="next-steps"></a>다음 단계

App Service에 Java EE 애플리케이션을 배포했으니, [Java Enterprise 개발자 가이드](https://aka.ms/wildfly-quickstart)에서 서비스 설정, 문제 해결 및 애플리케이션 크기 조정에 대해 자세히 알아보세요.