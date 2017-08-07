---
title: "Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기 | Microsoft Docs"
description: "Azure Functions Tools for Visual Studio를 사용하여 간단하고 HTTP에서 트리거한 함수를 Azure에 만들고 게시합니다."
services: functions
documentationcenter: na
author: rachelappel
manager: erikre
editor: 
tags: 
keywords: "Azure 함수, 함수, 이벤트 처리, 계산, 서버를 사용하지 않는 아키텍처"
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.service: functions
ms.devlang: multiple
ms.topic: hero-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 07/05/2017
ms.author: glenga
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 4a6b706b63c4e1b0df3c46bce4ff6877efca4ead
ms.contentlocale: ko-kr
ms.lasthandoff: 08/02/2017

---
# <a name="create-your-first-function-using-visual-studio"></a>Visual Studio를 사용하여 첫 번째 함수 만들기

Azure Functions를 사용하면 먼저 VM을 만들거나 웹 응용 프로그램을 게시하지 않고도 서버를 사용하지 않는 환경에서 코드를 실행할 수 있습니다.

> [!IMPORTANT]
> 이 항목에서는 단계를 완료하기 위해 미리 보기 버전의 Visual Studio를 사용합니다. 진행하기 전에 [Visual Studio 2017 미리 보기 버전 15.3](https://www.visualstudio.com/vs/preview/)을 설치했는지 확인합니다.

이 항목에서는 Azure Function Tools for Visual Studio 2017을 사용하여 로컬에서 "hello world" 함수를 만들고 테스트하는 방법에 대해 알아봅니다. 그런 다음 함수 코드를 Azure에 게시합니다.

![Visual Studio 프로젝트의 Azure Functions 코드](./media/functions-create-your-first-function-visual-studio/functions-vstools-intro.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음을 설치합니다.

* [Visual Studio 2017 미리 보기 버전 15.3](https://www.visualstudio.com/vs/preview/)(**Azure 개발** 워크로드 포함)

    ![Azure 개발 워크로드를 통한 Visual Studio 2017 설치](./media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-functions-tools-for-visual-studio-2017"></a>Azure Functions Tools for Visual Studio 2017 설치

시작하기 전에 먼저 Azure Functions Tools for Visual Studio 2017을 다운로드하여 설치해야 합니다. 이 도구는 Visual Studio 2017 미리 보기 버전 15.3 이상 버전에서만 사용할 수 있습니다. Azure Functions Tools를 이미 설치한 경우 이 섹션을 건너뛸 수 있습니다.

[!INCLUDE [Install the Azure Functions Tools for Visual Studio](../../includes/functions-install-vstools.md)]   

## <a name="create-an-azure-functions-project-in-visual-studio"></a>Visual Studio에서 Azure Functions 프로젝트 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

이제 프로젝트를 만들었으므로 첫 번째 함수를 만들 수 있습니다.

## <a name="create-the-function"></a>함수 만들기

1. **솔루션 탐색기**에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목**을 차례로 선택합니다. **Azure 함수**를 선택하고 **추가**를 클릭합니다.

2. **HttpTrigger**를 선택하고, **함수 이름**을 입력하고, **익명**에 대한 **액세스 권한**을 선택하고, **만들기**를 클릭합니다. 만든 함수는 모든 클라이언트의 HTTP 요청으로 액세스됩니다. 

    ![새 Azure 함수 만들기](./media/functions-create-your-first-function-visual-studio/functions-vstools-add-new-function-2.png)

이제 HTTP에서 트리거한 함수를 만들었으므로 로컬 컴퓨터에서 해당 함수를 테스트할 수 있습니다.

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

Azure Functions Core 도구를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. Visual Studio에서 처음으로 함수를 시작할 때 이 도구를 설치하도록 요구하는 메시지가 표시됩니다.  

1. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 Azure Functions Core(CLI) 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다.  또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 합니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.  

    ![Azure 로컬 런타임](./media/functions-create-your-first-function-visual-studio/functions-vstools-f5.png)

3. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. 이 URL에 쿼리 문자열 `&name=<yourname>`을 추가하고 요청을 실행합니다. 다음은 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 localhost 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-test-local-browser.png)

4. 디버깅을 중지하려면 Visual Studio 도구 모음에서 **중지** 단추를 클릭합니다.

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. Visual Studio에서 직접 함수 앱을 만들 수 있습니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure에서 함수 테스트

1. 게시 프로필 페이지에서 함수 앱의 기준 URL을 복사합니다. 로컬에서 함수를 테스트할 때 사용한 URL의 `localhost:port` 부분을 새 기준 URL로 바꿉니다. 이전처럼 이 URL에 `&name=<yourname>` 쿼리 문자열을 추가하고 요청을 실행합니다.

    HTTP에서 트리거한 함수를 호출하는 URL은 다음과 같습니다.

        http://<functionappname>.azurewebsites.net/api/<functionname>?name=<yourname> 

2. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 다음은 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답을 보여 줍니다. 

    ![브라우저의 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-test-remote-browser.png)
 
## <a name="next-steps"></a>다음 단계

Visual Studio를 사용하여 간단하고 HTTP에서 트리거한 함수가 있는 C# 함수 앱을 만들었습니다. 

+ 다른 종류의 트리거 및 바인딩을 지원하도록 프로젝트를 구성하는 방법은 [Visual Studio용 Azure Functions 도구](functions-develop-vs.md#configure-the-project-for-local-development)의 [로컬 개발에 대한 프로젝트 구성](functions-develop-vs.md) 섹션을 참조하세요.
+ Azure Functions Core 도구를 사용하여 로컬에서 테스트하고 디버그하는 방법에 대한 자세한 내용은 [Azure Functions를 로컬로 코딩 및 테스트하는 방법](functions-run-local.md)을 참조하세요. 
+ .NET 클래스 라이브러리로 기능을 개발하는 방법에 대해 자세히 알아보려면 [Azure Functions에서 .NET 클래스 라이브러리 사용](functions-dotnet-class-library.md)을 참조하세요. 


