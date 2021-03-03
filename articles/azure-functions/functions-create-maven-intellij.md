---
title: IntelliJ를 사용 하 여 Azure Functions에서 Java 함수 만들기
description: IntelliJ를 사용 하 여 간단한 HTTP로 트리거되는 Java 함수를 만드는 방법에 대해 알아봅니다 .이 함수는 Azure의 서버를 사용 하지 않는 환경에서 실행 되도록 게시 합니다.
author: jeffhollan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: jehollan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 1edfbca2f2a16aeebc1244b298426dc46d72c6d5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708818"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>IntelliJ를 사용 하 여 Azure에서 첫 번째 Java 함수 만들기

이 문서에서는 다음을 안내합니다.
- IntelliJ 아이디어 프로젝트에서 HTTP로 트리거되는 Java 함수를 만드는 방법입니다.
- 사용자 컴퓨터의 IDE (통합 개발 환경)에서 프로젝트를 테스트 하 고 디버깅 하기 위한 단계입니다.
- Azure Functions에 함수 프로젝트를 배포하기 위한 지침

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>개발 환경 설정

IntelliJ를 사용 하 여 Azure에 Java 함수를 만들고 게시 하려면 다음 소프트웨어를 설치 합니다.

+ 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
+ Java 8용 [Azure 지원 JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-jdk-long-term-support)
+ [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) Ultimate Edition 또는 Community Edition 설치
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ 최신 [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>설치 및 로그인

1. IntelliJ IDEA의 설정/기본 설정 대화 상자(Ctrl+Alt+S)에서 **플러그인** 을 선택합니다. 그런 다음, **Marketplace** 에서 **Azure Toolkit for IntelliJ** 를 찾고 **설치** 를 클릭합니다. 설치가 완료된 후 **다시 시작** 을 클릭하여 플러그인을 활성화합니다. 

    ![Marketplace의 Azure Toolkit for IntelliJ][marketplace]

2. Azure 계정에 로그인하려면 사이드바 **Azure Explorer** 를 연 다음, 위쪽 막대에서(또는 IDEA 메뉴 **도구/Azure/Azure 로그인** 에서) **Azure 로그인** 아이콘을 클릭합니다.
    ![IntelliJ Azure 로그인 명령][intellij-azure-login]

3. **Azure 로그인** 창에서 **디바이스 로그인** 을 선택하고 **로그인**([다른 로그인 옵션](/azure/developer/java/toolkit-for-intellij/sign-in-instructions))을 클릭합니다.

   ![디바이스 로그인을 선택한 Azure 로그인 창][intellij-azure-popup]

4. **Azure 디바이스 로그인** 대화 상자에서 **복사 및 열기** 를 클릭합니다.

   ![Azure 로그인 대화 상자 창][intellij-azure-copycode]

5. 브라우저에서, 마지막 단계에서 **복사 및 열기** 를 클릭할 때 복사한 디바이스 코드를 붙여넣은 후 **다음** 을 클릭합니다.

   ![디바이스 로그인 브라우저][intellij-azure-link-ms-account]

6. **구독 선택** 대화 상자에서 사용 하려는 구독을 선택 하 고 **선택** 을 클릭 합니다.

   ![구독 선택 대화 상자][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>로컬 프로젝트 만들기

이 섹션에서는 Azure Toolkit for IntelliJ를 사용하여 로컬 Azure Functions 프로젝트를 만듭니다. 이 문서의 뒷부분에서 함수 코드를 Azure에 게시합니다. 

1. IntelliJ 시작 대화 상자를 열고 *새 프로젝트 만들기* 를 선택하여 새 프로젝트 마법사를 열고 *Azure Functions* 를 선택합니다.

    ![함수 프로젝트 만들기](media/functions-create-first-java-intellij/create-functions-project.png)

1. *Http 트리거* 를 선택한 후 *다음* 을 클릭하고 마법사에 따라 다음 페이지의 모든 구성을 진행합니다. 프로젝트 위치를 확인한 다음, *마침* 을 클릭합니다. 그러면 Intellj IDEA에 새 프로젝트가 열립니다.

    ![함수 프로젝트 만들기 완료](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>로컬로 프로젝트 실행

1. `src/main/java/org/example/functions/HttpTriggerFunction.java`로 이동하여 생성된 코드를 확인합니다. 줄 *17* 옆에 녹색 *실행* 단추가 있는지 확인하고 클릭한 후 *'azure-function-exam...' 실행* 을 선택하면 몇 가지 로그로 함수 앱이 로컬에서 실행 중임을 확인할 수 있습니다.

    ![로컬 실행 프로젝트](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![로컬 실행 프로젝트 출력](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. 브라우저에서 출력된 엔드포인트(예: `http://localhost:7071/api/HttpTrigger-Java?name=Azure`)에 액세스하여 함수를 사용해 볼 수 있습니다.

    ![로컬 실행 함수 테스트 결과](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. 또한 로그는 사용자의 아이디어에 따라 출력 됩니다. 이제 *중지* 단추를 클릭 하 여 함수 앱을 중지 합니다.

    ![로컬 실행 함수 테스트 로그](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>프로젝트를 로컬로 디버그

1. 프로젝트의 함수 코드를 로컬로 디버깅 하려면 도구 모음에서 *디버그* 단추를 선택 합니다. 도구 모음이 표시 되지 않는 경우 모양 **보기**  >    >  **도구 모음** 을 선택 하 여 사용 하도록 설정 합니다.

    ![로컬 디버그 함수 앱 단추](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. `src/main/java/org/example/functions/HttpTriggerFunction.java` 파일의 줄 *20* 을 클릭하여 중단점을 추가하고 엔드포인트 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`에 액세스하면 중단점에 도달한 것을 확인할 수 있습니다. *한 단계 실행*, *조사식*, *평가* 같은 더 많은 디버그 기능을 사용해 볼 수 있습니다. 중지 단추를 클릭하여 디버그 세션을 중지합니다.

    ![로컬 디버그 함수 앱 중단](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>Azure에 프로젝트 배포

1. IntelliJ 프로젝트 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 *Azure -> Azure Functions에 배포* 를 선택합니다.

    ![Azure에 프로젝트 배포](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. 함수 앱이 아직 없는 경우 *사용 가능한 함수 없음을 클릭하여 새로 만듭니다*.

    ![Azure에서 함수 앱 만들기](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

1. 함수 앱 이름을 입력 하 고 적절 한 구독/플랫폼/리소스 그룹/App Service 계획을 선택 합니다. 여기에서 리소스 그룹/App Service 계획을 만들 수도 있습니다. 그런 다음 앱 설정을 변경 되지 않은 상태로 유지 하 고 *확인* 을 클릭 한 다음 새 함수 앱을 만들 때까지 몇 분 정도 기다립니다. *새 함수 앱 만들기...* 진행률 표시줄이 사라지면 완료된 것입니다.

    ![Azure 앱 만들기 마법사에 함수 앱 배포](media/functions-create-first-java-intellij/deploy-functions-create-app-wizard.png)

1. 배포하려는 함수 앱을 선택합니다(방금 만든 새 함수 앱이 자동으로 선택됨). *실행* 을 클릭하여 함수를 배포합니다.

    ![스크린샷 배포 Azure Functions 대화 상자를 표시 합니다.](media/functions-create-first-java-intellij/deploy-functions-run.png)

    ![Azure 로그에 함수 앱 배포](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>아이디어 로부터 함수 앱 관리

1. 아이디어에서 *Azure 탐색기* 를 사용 하 여 함수 앱을 관리할 수 있습니다. *함수 앱* 을 클릭 하면 모든 함수 앱이 여기에 표시 됩니다.

    ![탐색기에서 함수 앱 보기](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. 함수 앱 중 하나를 클릭 하 여 선택 하 고 마우스 오른쪽 단추를 클릭 한 다음 *속성 표시* 를 선택 하 여 세부 정보 페이지를 엽니다. 

    ![함수 앱 속성 표시](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. *Httptrigger-Java* 함수 앱을 마우스 오른쪽 단추로 클릭 하 고 *트리거 함수* 를 선택 하면 트리거 URL을 사용 하 여 브라우저가 열린 것을 볼 수 있습니다.

    ![스크린 샷에서 U R L이 있는 브라우저를 표시 합니다.](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>프로젝트에 함수 추가

1. *org.example.functions* 패키지를 마우스 오른쪽 단추로 클릭하고 *새로 만들기-> Azure 함수 클래스* 를 선택합니다. 

    ![프로젝트 항목에 함수 추가](media/functions-create-first-java-intellij/add-functions-entry.png)

1. 클래스 이름 *HttpTest* 를 입력하고 함수 클래스 만들기 마법사에서 *HttpTrigger* 를 선택한 후 *확인* 을 클릭하면 원하는 대로 새 함수를 만들 수 있습니다.

    ![함수 클래스 만들기 대화 상자를 보여 주는 스크린샷](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![프로젝트 출력에 함수 추가](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>함수 정리

1. Azure 탐색기에서 함수 삭제
      
      ![상황에 맞는 메뉴에서 선택한 삭제를 보여 주는 스크린샷](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>다음 단계

HTTP 트리거 함수를 사용 하 여 Java 프로젝트를 만들고, 로컬 컴퓨터에서 실행 하 고, Azure에 배포 했습니다. 이제 아래 방법으로 함수를 확장하겠습니다.

> [!div class="nextstepaction"]
> [Azure Storage 큐 출력 바인딩 추가](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
