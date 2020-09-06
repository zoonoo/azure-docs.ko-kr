---
title: '빠른 시작: Azure App Service에서 Java 앱 만들기'
description: 몇 분 안에 첫 번째 Java Hello World를 Azure App Service에 배포합니다. Azure Web App Plugin for Maven을 사용하면 Java 앱을 편리하게 배포할 수 있습니다.
keywords: azure, 앱 서비스, 웹앱, windows, linux, java, maven, 빠른 시작
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
ms.openlocfilehash: 7130ed2965e2df0d366635f6ce84c822c1359b59
ms.sourcegitcommit: 3246e278d094f0ae435c2393ebf278914ec7b97b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/02/2020
ms.locfileid: "89378171"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>빠른 시작: Azure App Service에서 Java 앱 만들기

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다.  이 빠른 시작에서는 [Azure Web App Plugin for Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)과 함께 [Azure CLI](/cli/azure/get-started-with-azure-cli)를 사용하여 .jar 파일 또는 .war 파일을 배포하는 방법을 보여줍니다. 탭을 사용하여 Java SE와 Tomcat 지침 사이를 전환합니다.


> [!NOTE]
> IntelliJ 및 Eclipse와 같은 인기 있는 IDE를 사용하여 동일한 작업을 수행할 수도 있습니다. [Azure Toolkit for IntelliJ 빠른 시작](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) 또는 [Azure Toolkit for Eclipse 빠른 시작](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app)에서 유사한 문서를 확인하세요.


![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java 앱 만들기

# <a name="java-se"></a>[Java SE](#tab/javase)

[Spring Boot 시작하기](https://github.com/spring-guides/gs-spring-boot) 샘플 프로젝트를 복제합니다.

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

디렉터리를 완료된 프로젝트로 변경합니다.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 앱을 만듭니다.

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

그런 다음, 작업 디렉터리를 프로젝트 폴더로 변경합니다.

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Maven 플러그 인 구성

Azure App Service에 대한 배포 프로세스는 Azure CLI의 Azure 자격 증명을 자동으로 사용합니다. Azure CLI가 로컬로 설치되어 있지 않으면 Maven 플러그 인은 Oauth 또는 디바이스 로그인으로 인증됩니다. 자세한 내용은 [Maven 플러그 인으로 인증](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication)을 참조하세요.

아래의 maven 명령을 실행하여 배포를 구성합니다. 이 명령은 App Service 운영 체제, Java 버전 및 Tomcat 버전을 설정하는 데 도움이 됩니다.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. 메시지가 표시되면 `2`를 입력하여 **창**을 선택합니다.
2. Enter를 눌러 기본 Java 버전 1.8을 사용합니다.
3. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    ```
    Please confirm webapp properties
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 메시지가 표시되면 `2`를 입력하여 **창**을 선택합니다.
1. Enter를 눌러 기본 Java 버전 1.8을 사용합니다.
1. Enter를 눌러 기본 웹 컨테이너인 Tomcat 8.5를 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    요약 출력은 아래에 표시된 코드 조각과 유사하게 표시됩니다.

    ```
    Please confirm webapp properties
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. 메시지가 표시되면 Enter를 눌러 **linux**를 선택합니다.
2. Enter를 눌러 기본 Java 버전 1.8을 사용합니다.
3. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    ```
    Please confirm webapp properties
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. 메시지가 표시되면 Enter를 눌러 **linux**를 선택합니다.
1. Enter를 눌러 기본 Java 버전 1.8을 사용합니다.
1. Enter를 눌러 기본 웹 컨테이너인 Tomcat 8.5를 사용합니다.
1. 마지막으로, 마지막 프롬프트에서 Enter를 눌러 선택 내용을 확인합니다.

    ```
    Please confirm webapp properties
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : PremiumV2_P1v2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

필요한 경우 `pom.xml`에서 직접 App Service에 대한 구성을 수정할 수 있습니다. 몇 가지 일반적인 사항은 다음과 같습니다.

속성 | 필수 | Description | 버전
---|---|---|---
`<schemaVersion>` | false | 구성 스키마의 버전을 지정합니다. 지원되는 값은 `v1`, `v2`입니다. | 1.5.2
`<resourceGroup>` | true | 웹앱에 대한 Azure 리소스 그룹입니다. | 0.1.0+
`<appName>` | true | 웹앱의 이름입니다. | 0.1.0+
`<region>` | true | 웹앱이 호스트되는 지역을 지정합니다(기본값: **westeurope**). [지원되는 지역](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) 섹션에 있는 모든 유효한 지역입니다. | 0.1.0+
`<pricingTier>` | false | 웹앱에 대한 가격 책정 계층입니다. 기본값은 **P1V2**입니다.| 0.1.0+
`<runtime>` | true | 런타임 환경 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)에서 세부 정보를 볼 수 있습니다. | 0.1.0+
`<deployment>` | true | 배포 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)에서 세부 정보를 볼 수 있습니다. | 0.1.0+

`<appName>` 및 `<resourceGroup>`값을 잘 보어야 합니다(이 데모에서는 `helloworld-1590394316693` 및 `helloworld-1590394316693-rg`). 나중에 사용됩니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>앱 배포

Maven 플러그 인은 Azure CLI의 계정 자격 증명을 사용하여 App Services에 배포합니다. 계속하려면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

그런 후, 다음 명령을 사용하여 Java 앱을 Azure에 배포할 수 있습니다.

```bash
mvn package azure-webapp:deploy
```

배포가 완료되면 애플리케이션이 `http://<appName>.azurewebsites.net/`(이 데모에서는 `http://helloworld-1590394316693.azurewebsites.net`)에 준비됩니다. 로컬 웹 브라우저에서 url을 열면 다음이 표시됩니다.

![Azure App Service에서 실행 중인 샘플 앱](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**축하합니다.** App Service에 첫 번째 Java 앱을 배포했습니다.

> [!div class="nextstepaction"]
> [문제가 발생했습니다.](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것으로 예상되는 경우 포털에서 리소스 그룹을 삭제하거나 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Java를 사용하여 Azure SQL Database에 연결](../azure-sql/database/connect-query-java.md?toc=%2fazure%2fjava%2ftoc.json)

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure DB for MySQL에 연결](../mysql/connect-java.md)

> [!div class="nextstepaction"]
> [Java를 사용하여 Azure DB for PostgreSQL에 연결](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Java 개발자 리소스용 Azure](/java/azure/)

> [!div class="nextstepaction"]
> [Java 앱 구성](configure-language-java.md)

> [!div class="nextstepaction"]
> [Jenkins를 사용하는 CI/CD](/azure/developer/jenkins/deploy-to-azure-app-service-using-plugin)

> [!div class="nextstepaction"]
> [사용자 지정 도메인 매핑](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Azure용 Maven 플러그 인에 대한 자세한 정보](https://github.com/microsoft/azure-maven-plugins)