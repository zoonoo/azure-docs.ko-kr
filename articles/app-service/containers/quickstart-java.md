---
title: Linux에서 Java 웹앱 만들기 - Azure App Service
description: 이 빠른 시작에서는 몇 분 안에 Linux의 Azure App Service에서 첫 번째 Java Hello World를 배포합니다.
keywords: azure, 앱 서비스, 웹앱, linux, java, maven, 빠른 시작
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jeconnoc
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.author: msangapu
ms.custom: mvc, seo-java-july2019, seo-java-august2019
ms.openlocfilehash: 10e5c31b4fbeb8af0235a067c57c3b209fe892af
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171296"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>빠른 시작: Linux의 App Service에서 Java 앱 만들기

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 사용하여 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 [Azure App Service용 Maven 플러그 인](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)에서 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 Java 웹 보관(WAR) 파일을 배포하는 방법을 보여줍니다.
> [!NOTE]
>
> IntelliJ 및 Eclipse와 같은 인기 있는 IDE를 사용하여 동일한 작업을 수행할 수도 있습니다. [Azure Toolkit for IntelliJ 빠른 시작](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) 또는 [Azure Toolkit for Eclipse 빠른 시작](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app)에서 유사한 문서를 체크 아웃합니다.
>
![Azure에서 실행되는 샘플 앱](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java 앱 만들기

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 앱을 만듭니다.

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Maven 플러그 인 구성

Maven에서 배포하려면 Cloud Shell의 코드 편집기를 사용하여 `helloworld` 디렉터리의 `pom.xml` 프로젝트 파일을 엽니다. 

```bash
code pom.xml
```

그런 다음, `pom.xml` 파일의 `<build>` 요소 내에 다음 플러그 인 정의를 추가합니다.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Linux           -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
   
            <!-- Java Runtime Stack for App on Linux-->
            <runtime>
                <os>linux</os>
                <javaVersion>jre8</javaVersion>
                <webContainer>tomcat 8.5</webContainer>
            </runtime> 
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

Azure App Service에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 계속하려면 [Azure CLI로 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

그런 다음 배포를 구성하고 명령 프롬프트에서 maven 명령 `mvn azure-webapp:config`를 실행하고 기본 구성을 사용할 수 있습니다. **확인(Y/N)** 메시지가 표시될 때까지 **ENTER** 키를 누른 후 **'y'** 를 누르면 구성이 완료됩니다.

```cmd
~@Azure:~/helloworld$ mvn azure-webapp:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.7.0:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use:
Define value for javaVersion(Default: jre8):
1. jre8 [*]
2. java11
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. jre8
3. TOMCAT 8.5 [*]
4. WILDFLY 14
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> 이 문서에서는 WAR 파일에 패키지된 Java 앱만 사용합니다. 플러그 인에서 JAR 웹 애플리케이션도 지원됩니다. [Linux 기반 App Service에 Java SE JAR 파일 배포](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)를 방문하여 직접 확인해 보세요.

`pom.xml`로 다시 이동하여 업데이트된 플러그인 구성을 확인하고 필요한 경우 App Service에 대한 다른 구성을 pom 파일에서 직접 수정할 수 있습니다. 몇 가지 일반적인 사항은 다음과 같습니다.

 자산 | 필수 | 설명 | 버전
---|---|---|---
`<schemaVersion>` | false | 구성 스키마의 버전을 지정합니다. 지원되는 값은 `v1`, `v2`입니다. | 1.5.2
`<resourceGroup>` | true | 웹앱에 대한 Azure 리소스 그룹입니다. | 0.1.0+
`<appName>` | true | 웹앱의 이름입니다. | 0.1.0+
[`<region>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) | true | 웹앱이 호스트되는 지역을 지정합니다(기본값: **westus**). [지원되는 지역](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#region) 섹션에 있는 모든 유효한 지역입니다. | 0.1.0+
[`<pricingTier>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme##pricingtier) | false | 웹앱에 대한 가격 책정 계층입니다. 기본값은 **P1V2**입니다.| 0.1.0+
[`<runtime>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting) | true | 런타임 환경 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#runtimesetting)에서 세부 정보를 볼 수 있습니다. | 0.1.0+
[`<deployment>`](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting) | true | 배포 구성이며, [여기](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme#deploymentsetting)에서 세부 정보를 볼 수 있습니다. | 0.1.0+

## <a name="deploy-the-app"></a>앱 배포

다음 명령을 사용하여 Java 앱을 Azure에 배포합니다.

```bash
mvn package azure-webapp:deploy
```

배포가 완료되면 웹 브라우저에서 다음 URL을 사용하여 배포된 애플리케이션으로 이동합니다(예: `http://<webapp>.azurewebsites.net`). 

![Azure에서 실행되는 샘플 앱](media/quickstart-java/java-hello-world-in-browser.png)

**축하합니다.** Linux의 App Service에 첫 번째 Java 앱을 배포했습니다.

## <a name="clean-up-resources"></a>리소스 정리

이전 단계에서 Azure 리소스를 리소스 그룹에 만들었습니다. 나중에 이러한 리소스가 필요하지 않을 것 같으면 Cloud Shell에서 다음 명령을 실행하여 리소스 그룹을 삭제합니다.

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

이 명령을 실행하는 데 1분 정도 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: PostgreSQL을 사용한 Java Enterprise 앱](tutorial-java-enterprise-postgresql-app.md)

> [!div class="nextstepaction"]
> [Java 앱 구성](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Jenkins를 사용하는 CI/CD](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Java 개발자 리소스용 기타 Azure](/java/azure/)
