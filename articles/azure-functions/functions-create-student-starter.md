---
title: Azure for Students Starter를 사용하여 함수 만들기
description: Azure for Students Starter 구독 내에서 Azure 함수 만드는 방법 알아보기
Customer intent: As a student, I want to be able to create an HTTP triggered Function App within the Student Starter plan so that I can easily add APIs to any project.
author: alexkarcher-msft
ms.topic: how-to
ms.date: 04/29/2020
ms.author: alkarche
ms.openlocfilehash: ffb6378d3dc4cc3fb23ea62157aad393d8ae6642
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83122868"
---
# <a name="create-a-function-using-azure-for-students-starter"></a>Azure for Students Starter를 사용하여 함수 만들기

이 자습서에서는 Azure for Students Starter 구독에서 "hello world" HTTP 함수를 만듭니다. 또한 이 구독 형식의 Azure Functions에서 사용할 수 있는 기능도 알아봅니다.

Microsoft *Azure for Students Starter*를 사용하면 비용을 들이지 않고 클라우드에서 개발하는 데 필요한 Azure 제품을 시작할 수 있습니다. [여기에서 이 제품에 대해 자세히 알아보세요.](https://azure.microsoft.com/offers/ms-azr-0144p/)

Azure Functions를 사용하면 먼저 VM을 만들거나 웹 애플리케이션을 게시하지 않고도 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 환경에서 코드를 실행할 수 있습니다. [여기에서 Functions에 대해 자세히 알아보세요.](./functions-overview.md)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function"></a>함수 만들기

 이 문서에서는 Azure Portal에서 Azure Functions를 사용하여 "hello world" HTTP 트리거 함수를 만드는 방법을 보여 줍니다.

![Azure Portal에서 함수 앱 만들기](./media/functions-create-student-starter/function-app-in-portal-editor.png)

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure 계정을 사용하여 [Azure Portal](https://portal.azure.com) 에 로그인합니다.

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱을 사용하면 함수를 논리 단위로 그룹화하여 더 쉽게 리소스를 관리, 배포, 크기 조정 및 공유할 수 있습니다.

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

다음으로 새 함수 앱에서 함수를 만듭니다.

## <a name="create-an-http-trigger-function"></a><a name="create-function"></a>HTTP 트리거 함수 만들기

1. **Functions** 창의 왼쪽 메뉴에서 **Functions**를 선택한 다음, 맨 위 메뉴에서 **추가**를 선택합니다. 
 
1. **새 함수** 창에서 **Http 트리거**를 선택합니다.

    ![HTTP 트리거 함수 선택](./media/functions-create-student-starter/function-app-select-http-trigger.png)

1. **새 함수** 창에서 **새 함수**의 기본 이름을 적용하거나 새 이름을 입력합니다. 

1. **권한 부여 수준** 드롭다운 목록에서 **익명**을 선택한 다음, **함수 만들기**를 선택합니다.

    Azure에서 HTTP 트리거 함수를 만듭니다. 이제 HTTP 요청을 전송하여 새 함수를 실행할 수 있습니다.

## <a name="test-the-function"></a>함수 테스트

1. 새 HTTP 트리거 함수의 왼쪽 메뉴에서 **코드 + 테스트**를 선택한 다음, 상단 메뉴에서 **함수 URL 가져오기**를 선택합니다.

    ![[함수 URL 가져오기] 선택](./media/functions-create-student-starter/function-app-select-get-function-url.png)

1. **함수 URL 가져오기** 대화 상자의 드롭다운 목록에서 **기본값**을 선택한 다음, **클립보드에 복사** 아이콘을 선택합니다. 

    ![Azure Portal에서 함수 URL 복사](./media/functions-create-student-starter/function-app-develop-tab-testing.png)

1. 함수 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. `?name=<your_name>` 쿼리 문자열 값을 이 URL의 마지막에 추가하고 Enter 키를 눌러 요청을 실행합니다. 

    다음 예에서는 브라우저의 응답을 보여 줍니다.

    ![브라우저에 함수 응답.](./media/functions-create-student-starter/function-app-browser-testing.png)

    요청 URL에는 기본적으로 HTTP를 통해 함수에 액세스하는 데 필요한 키가 포함됩니다.

1. 함수가 실행되면 추적 정보가 로그에 기록됩니다. 추적 출력을 보려면 포털의 **코드 + 테스트** 페이지로 돌아가서 페이지 하단에 있는 **로그** 화살표를 확장합니다.

   ![Azure Portal에서 함수 로그 뷰어.](./media/functions-create-student-starter/function-view-logs.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="supported-features-in-azure-for-students-starter"></a>Azure for Students Starter의 지원되는 기능

Azure for Students Starter에서는 아래의 주요 제한 사항을 제외하고, Azure Functions 런타임의 기능 대부분에 액세스할 수 있습니다.

* 트리거 유형으로는 HTTP 트리거만 지원됩니다.
    * 모든 입력 및 모든 출력 바인딩이 지원됩니다. [여기에서 전체 목록을 참조하세요.](functions-triggers-bindings.md)
* 지원되는 언어: 
    * C#(.NET Core 2)
    * JavaScript(Node.js 8 및 10)
    * F#(.NET Core 2)
    * [여기에서 상위 계획에서 지원되는 언어를 참조하세요.](supported-languages.md)
* Windows는 유일하게 지원되는 운영 체제입니다.
* 확장은 매일 최대 60분 동안 실행되는 [하나의 무료 계층 인스턴스](https://azure.microsoft.com/pricing/details/app-service/windows/)로 제한됩니다. HTTP 트래픽이 수신될 때 서버리스 방식으로 0에서 1개로 인스턴스를 자동으로 확장하게 되지만 추가 확장은 가능하지 않습니다.
* Functions 런타임의 [버전 2.x 이상](functions-versions.md)만 지원됩니다.
* 모든 개발자 도구는 함수 편집 및 게시를 지원합니다. 여기에는 VS Code, Visual Studio, Azure CLI 및 Azure Portal이 포함됩니다. 포털 이외의 도구를 사용하려는 경우 먼저 포털에서 앱을 만든 후, 기본 설정 도구에서 해당 앱을 배포 대상으로 선택해야 합니다.

## <a name="next-steps"></a>다음 단계

이제 간단한 HTTP 트리거 함수로 함수 앱을 만들었습니다. 다음으로, 로컬 도구, 추가 언어, 모니터링 및 통합에 대해 알아볼 수 있습니다.

 * [Visual Studio를 사용하여 첫 번째 함수 만들기](./functions-create-your-first-function-visual-studio.md)
 * [Visual Studio Code를 사용하여 첫 번째 함수 만들기](./functions-create-first-function-vs-code.md)
 * [Azure Functions JavaScript 개발자 가이드](./functions-reference-node.md)
 * [Azure Functions를 사용하여 Azure SQL Database에 연결](./functions-scenario-database-table-cleanup.md)
 * [Azure Functions HTTP 바인딩에 대해 자세히 알아보기](./functions-bindings-http-webhook.md)
 * [Azure Functions 모니터링](./functions-monitoring.md)
