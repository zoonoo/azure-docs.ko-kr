---
title: Java 및 IntelliJ를 사용하여 Azure 함수 앱 만들기 | Microsoft Docs
description: Java 및 IntelliJ를 통해 서버리스 간단한 HTTP 트리거 앱을 만들고 Azure Functions에 게시하는 방법 가이드입니다.
services: functions
documentationcenter: na
author: jeffhollan
manager: jpconnock
keywords: Azure Functions, 함수, 이벤트 처리, 계산, 서버리스 아키텍처, Java
ms.service: functions
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 5e265543e2ce5feeed095d89cdb47ede9817bad1
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2018
ms.locfileid: "40002681"
---
# <a name="create-your-first-function-with-java-and-intellij-preview"></a>Java 및 IntelliJ를 사용하여 첫 번째 함수 만들기(미리 보기)

> [!NOTE] 
> Azure Functions용 Java는 현재 미리 보기로 제공되고 있습니다.

이 문서에서는 IntelliJ IDEA 및 Apache Maven을 통해 [서버리스](https://azure.microsoft.com/overview/serverless-computing/) 함수 프로젝트를 만들고, IDE의 컴퓨터에서 테스트 및 디버그한 다음, Azure Functions에 배포하는 방법을 보여 줍니다. 

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

Java 및 IntelliJ를 통해 함수 앱을 개발하려면 다음을 설치해야 합니다.

-  [Java Developer Kit](https://www.azul.com/downloads/zulu/) 버전 8
-  [Apache Maven](https://maven.apache.org) 버전 3.0 이상
-  [IntelliJ IDEA](https://www.jetbrains.com/idea/download) Community 또는 Ultimate(Maven 도구 포함)
-  [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT] 
> 이 퀵 스타트를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

Azure Functions를 작성, 실행 및 디버그하기 위한 로컬 개발 환경을 제공하는 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치하는 것이 좋습니다. 


## <a name="create-a-functions-project"></a>Functions 프로젝트 만들기

1. IntelliJ IDEA에서 **새 프로젝트 만들기**를 클릭합니다.  
1. **Maven**에서 만들도록 선택합니다.
1. [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)에 대해 **archetype에서 만들기** 및 **Archetype 추가** 확인란을 선택합니다.
    - GroupId: com.microsoft.azure
    - ArtifactId: azure-functions-archetype
    - 버전: [중앙 리포지토리](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![IntelliJ Maven create](media/functions-create-first-java-intellij/functions-create-intellij.png)의 최신 버전을 사용합니다.  
1. **다음**을 클릭하고 현재 프로젝트에 대한 세부 정보를 입력한 후 **마침**을 클릭합니다.

Maven은 이름이 _artifactId_인 새 폴더에 프로젝트 파일을 만듭니다. 프로젝트에서 생성된 코드는 HTTP 트리거 요청의 본문을 에코하는 간단한 [HTTP 트리거](/azure/azure-functions/functions-bindings-http-webhook) 함수입니다.

## <a name="run-functions-locally-in-the-ide"></a>IDE에서 로컬로 함수 실행

> [!NOTE]
> 로컬에서 함수를 실행하고 디버그하려면 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치해야 합니다.

1. 변경 내용을 가져오도록 선택하거나, [자동 가져오기](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)가 사용하도록 설정되었는지 확인합니다.
1. **Maven 프로젝트** 도구 모음을 엽니다.
1. 수명 주기에서 **패키지**를 두 번 클릭하여 솔루션을 패키징 및 빌드하고 대상 디렉터리를 만듭니다.
1. 플러그 인 -> azure-functions에서 **azure-functions:run**을 두 번 클릭하여 Azure Functions 로컬 런타임을 시작합니다.  
  ![Azure Functions에 대한 Maven 도구 모음](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

함수 테스트를 완료했으면 실행 대화 상자를 닫습니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

### <a name="debug-the-function-in-intellij"></a>IntelliJ에서 함수 디버그
디버그 모드로 함수 호스트를 시작하려면 함수를 실행할 때 인수로 **-DenableDebug**를 추가합니다. 터미널에서 아래 명령줄을 실행하거나 [Maven 목표](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)에서 해당 명령줄을 구성할 수 있습니다. 그런 다음, 함수 호스트가 5005에서 디버그 포트를 엽니다. 

```
mvn azure-functions:run -DenableDebug
```

IntelliJ에서 디버그하려면 **실행** 메뉴에서 **구성 편집**을 선택합니다. **원격**을 추가하려면 **+** 을 클릭하세요. **이름** 및 **설정**을 채운 다음, **확인**을 클릭하여 구성을 저장합니다. 설치 후 ‘사용자 원격 구성 이름’ **디버깅**을 클릭하거나 **Shift+F9** 키를 눌러 디버깅을 시작합니다.

![IntelliJ에서 함수 디버깅](media/functions-create-first-java-intellij/debug-configuration-intellij.PNG)

완료되면 디버거 및 실행 중인 프로세스를 중지합니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

Azure Functions에 대한 배포 프로세스는 Azure CLI의 계정 자격 증명을 사용합니다. 컴퓨터의 명령 프롬프트를 계속 사용하기 전에 [Azure CLI를 통해 로그인](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)합니다.

```azurecli
az login
```

`azure-functions:deploy` Maven 대상을 사용하여 새 함수 앱에 코드를 배포하거나, Maven 프로젝트 창에서 azure-functions:deploy 옵션을 선택합니다.

```
mvn azure-functions:deploy
```

배포가 완료되면 Azure 함수 앱에 액세스하는 데 사용할 수 있는 URL이 표시됩니다.

```output
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
```

## <a name="next-steps"></a>다음 단계

- Java 함수 개발에 대한 자세한 내용은 [Java 함수 개발자 가이드](functions-reference-java.md)를 참조합니다.
- `azure-functions:add` Maven 대상을 사용하여 프로젝트에 다른 트리거가 있는 다른 함수를 추가합니다.
