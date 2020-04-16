---
title: '빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기'
description: 이 빠른 시작에서는 Visual Studio를 사용하여 HTTP로 트리거된 Azure 함수를 만들고 게시하는 방법에 대해 알아봅니다.
ms.assetid: 82db1177-2295-4e39-bd42-763f6082e796
ms.topic: quickstart
ms.date: 03/06/2020
ms.custom: mvc, devcenter, vs-azure, 23113853-34f2-4f
ms.openlocfilehash: e58414fa94a4ef54eb2f288b2cd636c10611460d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308924"
---
# <a name="quickstart-create-your-first-function-in-azure-using-visual-studio"></a>빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 함수 만들기

Azure Functions를 사용하면 먼저 VM을 만들거나 웹 애플리케이션을 게시하지 않고도 서버리스 환경에서 코드를 실행할 수 있습니다.

이 빠른 시작에서는 Visual Studio 2019를 사용하여 "hello world" HTTP로 트리거된 C# 함수 앱을 로컬로 만들고 테스트한 다음, Azure에 게시하는 방법에 대해 알아봅니다. 

![브라우저의 localhost 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-local-final.png)

이 빠른 시작은 Visual Studio 2019용으로 설계되었습니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 먼저 [Visual Studio 2019](https://azure.microsoft.com/downloads/)를 설치합니다. 설치하는 동안 **Azure 개발** 워크로드를 선택했는지 확인합니다. Visual Studio 2017을 대신 사용하여 Azure Functions 프로젝트를 만들려면 먼저 [최신 Azure Functions 도구](functions-develop-vs.md#check-your-tools-version)를 설치해야 합니다.

![Azure 개발 워크로드가 포함된 Visual Studio 설치](media/functions-create-your-first-function-visual-studio/functions-vs-workloads.png)

[Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/dotnet/)을 만듭니다.

## <a name="create-a-function-app-project"></a>함수 앱 프로젝트 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

Visual Studio는 HTTP 트리거 함수 형식에 대한 상용구 코드가 포함된 프로젝트와 클래스를 만듭니다. `FunctionName` 메서드 특성은 함수 이름을 설정하며, 기본적으로 `Function1`입니다. `HttpTrigger` 특성은 HTTP 요청에서 함수를 트리거하도록 지정합니다. 상용구 코드는 요청 본문 또는 쿼리 문자열의 값을 포함하는 HTTP 응답을 보냅니다.

메서드에 적절한 특성을 적용하여 입력 및 출력 바인딩으로 함수의 기능을 확장합니다. 자세한 내용은 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)의 [트리거 및 바인딩](functions-dotnet-class-library.md#triggers-and-bindings) 섹션을 참조하세요.

함수 프로젝트 및 HTTP로 트리거되는 함수를 만들었으니, 로컬 컴퓨터에서 이 함수를 테스트할 수 있습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

Visual Studio는 Azure Functions Core Tools와 통합되므로 Azure Functions 런타임 전체를 사용하여 함수를 로컬로 테스트할 수 있습니다.  

[!INCLUDE [functions-run-function-test-local-vs](../../includes/functions-run-function-test-local-vs.md)]

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. 프로젝트를 처음 게시할 때 Visual Studio 게시에서 함수 앱을 만듭니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure에서 함수 테스트

1. **게시** 프로필 페이지에서 함수 앱의 기준 URL을 복사합니다. 로컬에서 함수를 테스트하는 데 사용한 URL의 `localhost:port` 부분을 새 기준 URL로 바꿉니다. `?name=<YOUR_NAME>` 쿼리 문자열을 이 URL에 추가하고 요청을 실행합니다.

    HTTP로 트리거된 함수를 호출하는 URL은 다음과 같은 형식입니다.

    `http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?name=<YOUR_NAME>`

2. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 다음 이미지는 함수에서 반환된 원격 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![브라우저의 함수 응답](./media/functions-create-your-first-function-visual-studio/functions-create-your-first-function-visual-studio-browser-azure.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [Clean-up resources](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Visual Studio를 사용하여 Azure에서 간단한 HTTP 트리거 함수가 있는 C# 함수 앱을 만들고 게시했습니다. 

다음 문서로 이동하여 함수에 Azure Storage 큐 바인딩을 추가하는 방법을 알아봅니다.
> [!div class="nextstepaction"]
> [함수에 Azure Storage 큐 바인딩 추가](functions-add-output-binding-storage-queue-vs.md)

