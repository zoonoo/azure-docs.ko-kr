---
title: Linux의 Azure App Service에서 Java 웹앱을 만들기 위한 빠른 시작
description: 이 빠른 시작에서는 몇 분 안에 Linux의 Azure App Service에서 첫 번째 Java Hello World를 배포합니다.
services: app-service\web
documentationcenter: ''
author: msangapu
manager: cfowler
editor: ''
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/07/2018
ms.author: msangapu
ms.custom: mvc
ms.openlocfilehash: b9e8d2b9eacfa5c427ffe3f27ea99bbd35651d57
ms.sourcegitcommit: 51a1476c85ca518a6d8b4cc35aed7a76b33e130f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/25/2018
ms.locfileid: "47165979"
---
# <a name="quickstart-create-a-java-web-app-in-app-service-on-linux"></a>빠른 시작: Linux의 App Service에서 Java 웹앱 만들기

[Linux의 App Service](app-service-linux-intro.md)는 Linux 운영 체제를 사용하여 확장성이 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 빠른 시작에서는 [Azure Web Apps용 Maven 플러그 인(미리 보기)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)에서 [Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)를 사용하여 Java WAR(Web Application aRchive) 파일을 배포하는 방법을 보여 줍니다.

![Azure에서 실행되는 샘플 앱](media/quickstart-java/java-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Java 앱 만들기

Cloud Shell 프롬프트에서 다음 Maven 명령을 실행하여 `helloworld`라는 새 웹앱을 만듭니다.

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
        <version>1.4.0</version>
        <configuration>
   
            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appName>${WEBAPP_NAME}</appName>
            <region>${REGION}</region>
   
            <!-- Java Runtime Stack for Web App on Linux-->
            <linuxRuntime>tomcat 8.5-jre8</linuxRuntime>
   
        </configuration>
    </plugin>
</plugins>
```    


> [!NOTE] 
> 이 문서에서는 WAR 파일에 패키지된 Java 앱만 사용합니다. 플러그 인은 JAR 웹 응용 프로그램도 지원합니다. 이러한 응용 프로그램에 대해 다음 대체 플러그 인 정의를 사용합니다. 이 구성은 Maven에서 빌드한 JAR을 로컬 파일 시스템의 `${project.build.directory}/${project.build.finalName}.jar`에 배포합니다.
>
>```xml
><plugin>
>            <groupId>com.microsoft.azure</groupId>
>            <artifactId>azure-webapp-maven-plugin</artifactId>
>            <version>1.4.0</version>
>            <configuration>
>                <deploymentType>jar</deploymentType>
>
>           <!-- Web App information -->
>            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
>            <appName>${WEBAPP_NAME}</appName>
>            <region>${REGION}</region>  
>
>                <!-- Java Runtime Stack for Web App on Linux-->
>                <linuxRuntime>jre8</linuxRuntime>
>            </configuration>
>         </plugin>
>```    


플러그인 구성에서 다음 자리 표시자를 업데이트합니다.

| Placeholder | 설명 |
| ----------- | ----------- |
| `RESOURCEGROUP_NAME` | 웹앱을 만들 새 리소스 그룹의 이름입니다. 앱의 모든 리소스를 한 그룹에 배치하여 다 함께 관리할 수 있습니다. 예를 들어 리소스 그룹을 삭제하면 앱과 연결된 모든 리소스가 삭제됩니다. 이 값을 고유한 새 리소스 그룹(예: *TestResources*)으로 업데이트합니다. 이 리소스 그룹 이름을 사용하여 이후 섹션에서 모든 Azure 리소스를 정리합니다. |
| `WEBAPP_NAME` | 앱 이름은 Azure(WEBAPP_NAME.azurewebsites.net)에 배포할 때 웹앱에 대한 호스트 이름의 일부가 됩니다. 이 값을 Java 앱을 호스팅할 새 Azure 웹앱의 고유 이름(예: *contoso*)으로 업데이트합니다. |
| `REGION` | 웹앱이 호스팅되는 Azure 지역입니다(예: `westus2`). `az account list-locations` 명령을 사용하여 Cloud Shell 또는 CLI에서 지역 목록을 가져올 수 있습니다. |

## <a name="deploy-the-app"></a>앱 배포

다음 명령을 사용하여 Java 앱을 Azure에 배포합니다.

```bash
mvn package azure-webapp:deploy
```

배포가 완료되면 웹 브라우저에서 다음 URL을 사용하여 배포된 응용 프로그램으로 이동합니다(예: `http://<webapp>.azurewebsites.net/helloworld`). 

![Azure에서 실행되는 샘플 앱](media/quickstart-java/java-hello-world-in-browser-curl.png)

**축하합니다.** Linux의 App Service에 첫 번째 Java 앱을 배포했습니다.


[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]


## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Maven을 사용하여 Java 웹앱을 만들고, [Azure Web Apps용 Maven 플러그 인(미리 보기)](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin)을 구성한 다음, 웹 보관용으로 패키지된 Java 웹앱을 Linux의 App Service에 배포했습니다. Azure와 함께 Java를 사용하는 방법에 대해 자세히 알아보려면 아래 링크로 이동하세요.

> [!div class="nextstepaction"]
> [Java 개발자용 Azure](https://docs.microsoft.com/java/azure/)

