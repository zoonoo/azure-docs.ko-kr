---
title: 자바와 IntelliJ로 Azure 함수 만들기
description: Java 및 IntelliJ로 간단한 HTTP 트리거 서버리스 앱을 만들어 Azure에 게시하는 방법을 알아봅니다.
author: jeffhollan
ms.topic: conceptual
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter
ms.openlocfilehash: 7003dc19a7bfc405809de91534028aba8e0416c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136853"
---
# <a name="create-your-first-azure-function-with-java-and-intellij"></a>자바와 IntelliJ로 첫 번째 Azure 함수 만들기

이 문서에서는 다음을 안내합니다.
- IntelliJ IDEA 및 Apache Maven을 사용하여 [서버리스](https://azure.microsoft.com/overview/serverless-computing/) 함수 프로젝트를 만드는 방법
- 개발자 컴퓨터의 IDE(통합 개발 환경)에서 함수를 테스트 및 디버깅하는 단계
- Azure Functions에 함수 프로젝트를 배포하기 위한 지침

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

Java 및 IntelliJ를 사용하여 함수를 개발하려면 다음 소프트웨어를 설치합니다.

- [자바 개발자 키트](https://www.azul.com/downloads/zulu/) (JDK), 버전 8
- [Apache Maven](https://maven.apache.org) 버전 3.0 이상
- [IntelliJ IDEA](https://www.jetbrains.com/idea/download) Community 또는 Ultimate 버전(Maven 포함)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

> [!IMPORTANT]
> 이 문서의 단계를 완료하려면 JAVA_HOME 환경 변수를 JDK 설치 위치로 설정해야 합니다.

 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치하는 것이 좋습니다. 이 버전은 Azure Functions의 작성, 실행 및 디버깅을 위한 로컬 개발 환경을 제공합니다.

## <a name="create-a-functions-project"></a>Functions 프로젝트 만들기

1. IntelliJ IDEA에서 **새 프로젝트 만들기**를 선택합니다.  
1. **새 프로젝트** 창의 왼쪽 창에서 **Maven**을 선택합니다.
1. **archetype에서 만들기** 확인란을 선택한 다음, [azure-functions-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)으로 **Archetype 추가**를 선택합니다.
1. **Archetype 추가** 창에서 다음과 같이 필드를 완성합니다.
    - _그룹Id_: com.마이크로소프트.azure
    - _아티팩트 Id_: azure 함수-아키타입
    - _버전_: 중앙 [저장소의](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-archetype)
    ![최신 버전을 확인하고 IntelliJ IDEA의 아키타입에서 메이븐 프로젝트 만들기](media/functions-create-first-java-intellij/functions-create-intellij.png)  
1. **확인을**선택한 다음 **다음을**선택합니다.
1. 현재 프로젝트에 대한 세부 정보를 입력하고 **완료**를 선택합니다.

Maven은 이름이 _ArtifactId_ 값인 새 폴더에 프로젝트 파일을 만듭니다. 프로젝트에서 생성된 코드는 HTTP 트리거 요청의 본문을 에코하는 간단한 [HTTP 트리거](/azure/azure-functions/functions-bindings-http-webhook) 함수입니다.

## <a name="run-functions-locally-in-the-ide"></a>IDE에서 로컬로 함수 실행

> [!NOTE]
> 로컬로 함수를 실행하고 디버그하려면 [Azure Functions Core Tools 버전 2](functions-run-local.md#v2)를 설치해야 합니다.

1. 변경 내용을 수동으로 가져오거나 [자동 가져오기](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)를 사용합니다.
1. **메이븐 프로젝트** 도구 모음을 엽니다.
1. **수명 주기**를 확장하고 **패키지**를 엽니다. 새로 만든 대상 디렉터리에 솔루션이 빌드 및 패키징됩니다.
1. **플러그인** > **azure 함수를** 확장 하 고 **azure-함수를 엽니다.실행** Azure Functions 로컬 런타임을 시작 합니다.  
  ![Azure Functions에 대한 Maven 도구 모음](media/functions-create-first-java-intellij/functions-intellij-java-maven-toolbar.png)  

1. 함수 테스트를 완료했으면 실행 대화 상자를 닫습니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="debug-the-function-in-intellij"></a>IntelliJ에서 함수 디버그

1. 디버그 모드로 함수 호스트를 시작하려면 함수를 실행할 때 인수로 **-DenableDebug**를 추가합니다. [maven 목표](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)에서 구성을 변경하거나 터미널 창에서 다음 명령을 실행할 수 있습니다.  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   이 명령을 사용하면 함수 호스트가 5005에서 디버그 포트를 엽니다.

1. **실행** 메뉴에서 **구성 편집**을 선택합니다.
1. **(+)** 를 선택하여 **원격**을 추가합니다.
1. _이름_ 및 _설정_ 필드를 완료한 다음, **확인**을 선택하여 구성을 저장합니다.
1. 설정이 끝나면 **디버그 < 원격 구성 이름 >** 를 선택하거나 키보드에서 Shift+F9를 눌러 디버깅을 시작합니다.

1. 디버깅이 끝나면 디버거 및 실행 중인 프로세스를 중지합니다. 한 번에 하나의 함수 호스트만 활성화되고 로컬로 실행될 수 있습니다.

## <a name="deploy-the-function-to-azure"></a>Azure에 함수 배포

1. Azure에 함수를 배포하려면 먼저 [Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)를 사용하여 로그인해야 합니다.

   ``` azurecli
   az login
   ```

1. `azure-functions:deploy` Maven 대상을 사용하여 새 함수에 코드를 배포합니다. Maven 프로젝트 창에서 **azure-functions:deploy** 옵션을 선택할 수도 있습니다.

   ```
   mvn azure-functions:deploy
   ```

1. 함수가 성공적으로 배포된 후 Azure CLI 출력에서 함수 URL을 찾습니다.

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>다음 단계

- Java 함수 개발에 대한 자세한 내용은 [Java 함수 개발자 가이드](functions-reference-java.md)를 참조합니다.
- `azure-functions:add` Maven 대상을 사용하여 다른 트리거가 있는 함수를 프로젝트에 추가합니다.
