---
title: Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기
description: Visual Studio를 사용하여 HTTP 트리거 Azure 함수를 만들고 게시합니다.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 계산, 서버리스 아키텍처
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/17/2018
ms.author: glenga
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: 1c07da0121b8e350c55b139901d56cfe2133386d
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864491"
---
# <a name="create-your-first-function-using-visual-studio"></a>Visual Studio를 사용하여 첫 번째 함수 만들기

Azure Functions를 사용하면 먼저 VM을 만들거나 웹 애플리케이션을 게시하지 않고도 [서버를 사용하지 않는](https://azure.microsoft.com/solutions/serverless/) 환경에서 코드를 실행할 수 있습니다.

이 문서에서는 Azure Functions용 Visual Studio 2019 도구를 사용하여 로컬로 "hello world" 함수를 만들고 테스트하는 방법에 대해 알아봅니다. 그런 후 함수 코드를 Azure에 게시합니다. 이러한 도구는 Visual Studio 2019에서 Azure 개발 워크로드의 일부로 제공됩니다.

![브라우저의 localhost 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

이 항목에는 동일한 기본 단계를 보여주는 [비디오](#watch-the-video)가 포함되어 있습니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio 2019](https://azure.microsoft.com/downloads/)를 설치하고 **Azure 개발** 워크로드도 설치되어 있어야 합니다.

* [최신 Azure Functions 도구](functions-develop-vs.md#check-your-tools-version)가 있어야 합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>함수 앱 프로젝트 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio는 프로젝트를 만들고 그 안에는 선택한 함수 형식에 대한 상용구 코드를 포함하는 클래스를 만듭니다. 메서드의 **FunctionName** 특성은 함수 이름을 설정합니다. **HttpTrigger** 특성은 함수가 HTTP 요청에 의해 트리거되도록 지정합니다. 상용구 코드는 요청 본문 또는 쿼리 문자열의 값을 포함하는 HTTP 응답을 보냅니다. 메서드에 적절한 특성을 적용하여 입력 및 출력 바인딩을 함수에 추가할 수 있습니다. 자세한 내용은 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)의 [트리거 및 바인딩](functions-dotnet-class-library.md#triggers-and-bindings) 섹션을 참조하세요.

함수 프로젝트 및 HTTP로 트리거되는 함수를 만들었으니, 로컬 컴퓨터에서 이 함수를 테스트할 수 있습니다.

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

Azure Functions Core Tools를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. Visual Studio에서 처음으로 함수를 시작할 때 이 도구를 설치하도록 요구하는 메시지가 표시됩니다.

1. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 Azure Functions Core(CLI) 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 합니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-debugging.png)

3. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. 이 URL에 쿼리 문자열 `?name=<YOUR_NAME>`을 추가하고 요청을 실행합니다. 다음은 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 localhost 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local.png)

4. 디버깅을 중지하려면 **Shift + F5**를 누릅니다.

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. Visual Studio에서 직접 함수 앱을 만들 수 있습니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure에서 함수 테스트

1. 게시 프로필 페이지에서 함수 앱의 기준 URL을 복사합니다. 로컬에서 함수를 테스트할 때 사용한 URL의 `localhost:port` 부분을 새 기준 URL로 바꿉니다. 이전처럼 이 URL에 `?name=<YOUR_NAME>` 쿼리 문자열을 추가하고 요청을 실행합니다.

    HTTP로 트리거되는 함수를 호출하는 URL은 다음 형식이어야 합니다.

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME> 

2. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 다음은 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![브라우저의 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="watch-the-video"></a>비디오 보기

> [!VIDEO https://www.youtube-nocookie.com/embed/DrhG-Rdm80k]

## <a name="next-steps"></a>다음 단계

Visual Studio를 사용하여 간단하고 HTTP에서 트리거한 함수가 있는 C# 함수 앱을 만들고 게시했습니다.

* [다른 서비스와 통합되는 입력 및 출력 바인딩을 추가하는 방법을 알아봅니다.](functions-develop-vs.md#add-bindings)
* [.NET 클래스 라이브러리로 함수를 개발하는 방법을 자세히 알아봅니다](functions-dotnet-class-library.md).
