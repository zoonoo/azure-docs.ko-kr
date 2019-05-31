---
title: C#을 사용하여 Azure에서 첫 번째 지속성 함수 만들기
description: Visual Studio를 사용하여 Azure 지속성 함수를 만들고 게시합니다.
services: functions
documentationcenter: na
author: jeffhollan
manager: jeconnoc
keywords: Azure 함수, 함수, 이벤트 처리, 계산, 서버리스 아키텍처
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 11/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 0288d9c0932d012bc83f23053b661c5a7ea2ef82
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65872964"
---
# <a name="create-your-first-durable-function-in-c"></a>C\#으로 첫 번째 지속성 함수 만들기

*Durable Functions*는 서버리스 환경에서 상태 저장 함수를 작성할 수 있게 하는 [Azure Functions](../functions-overview.md)의 확장입니다. 확장은 상태, 검사점 및 다시 시작을 관리합니다.

이 문서에서는 Azure Functions용 Visual Studio 2019 도구를 사용하여 로컬로 "hello world" 지속성 함수를 만들고 테스트하는 방법에 대해 알아봅니다.  이 함수는 다른 함수에 대한 호출을 오케스트레이션하고 함께 연결합니다. 그런 후 함수 코드를 Azure에 게시합니다. 이러한 도구는 Visual Studio 2019에서 Azure 개발 워크로드의 일부로 제공됩니다.

![Azure에서 지속성 함수 실행](./media/durable-functions-create-first-csharp/functions-vs-complete.png)

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* [Visual Studio 2019](https://azure.microsoft.com/downloads/)를 설치합니다. **Azure 개발** 워크로드도 설치되어 있어야 합니다.

* [최신 Azure Functions 도구](../functions-develop-vs.md#check-your-tools-version)가 있어야 합니다.

* [Azure Storage 에뮬레이터](../../storage/common/storage-use-emulator.md)가 설치 및 실행되고 있는지 확인합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="create-a-function-app-project"></a>함수 앱 프로젝트 만들기

Azure Functions 템플릿은 Azure에서 함수 앱에 게시할 수 있는 프로젝트를 만듭니다. 함수 앱을 통해 함수를 논리적 단위로 그룹화하여 관리, 배포 및 리소스를 공유할 수 있습니다.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기** > **프로젝트**를 선택합니다.

2. **새 프로젝트** 대화 상자에서 **설치됨**을 선택하고 **Visual C#**  > **클라우드**를 확장하고, **Azure Functions**를 선택하고, 프로젝트에 대한 **이름**을 입력하고, **확인**을 클릭합니다. 함수 앱 이름은 C# 네임스페이스로 유효해야 하므로 밑줄, 하이픈 또는 기타 영숫자가 아닌 문자는 사용하지 마세요.

    ![Visual Studio에서 함수를 만드는 새 프로젝트 대화 상자](./media/durable-functions-create-first-csharp/functions-vs-new-project.png)

3. 이미지 아래의 표에 지정된 설정을 사용합니다.

    ![Visual Studio의 새 함수 대화 상자](./media/durable-functions-create-first-csharp/functions-vs-new-function.png)

    | 설정      | 제안 값  | 설명                      |
    | ------------ |  ------- |----------------------------------------- |
    | **버전** | Azure Functions 2.x <br />(.NET Core) | .NET Core를 지원하는 Azure Functions의 버전 2.x 런타임을 사용하는 함수 프로젝트를 만듭니다. Azure Functions 1.x는 .NET Framework를 지원합니다. 자세한 내용은 [Azure Functions 런타임 버전을 대상으로 지정하는 방법](../functions-versions.md)을 참조하세요.   |
    | **템플릿** | Empty | 빈 함수 앱을 만듭니다. |
    | **Storage 계정**  | 저장소 에뮬레이터 | 스토리지 계정은 지속성 함수 상태 관리에 필요합니다. |

4. **확인**을 클릭하여 빈 함수 프로젝트를 만듭니다. 이 프로젝트에는 함수를 실행하는 데 필요한 기본 구성 파일이 있습니다.

## <a name="add-functions-to-the-app"></a>함수를 앱에 추가

다음 단계에서는 템플릿을 사용하여 프로젝트에 지속성 함수 코드를 만듭니다.

1. Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 누르고 **추가** > **새 Azure Function**을 선택합니다.

    ![새 함수 추가](./media/durable-functions-create-first-csharp/functions-vs-add-new-function.png)

2. **Azure Function**이 추가 메뉴에서 선택되어 있는지 확인하고 C# 파일의 이름을 지정합니다.  **추가**를 누릅니다.

3. **Durable Functions 오케스트레이터** 템플릿을 선택하고 **확인**을 클릭합니다.

    ![지속성 템플릿 선택](./media/durable-functions-create-first-csharp/functions-vs-select-template.png)  

새 지속성 함수가 앱에 추가됩니다.  새 .cs 파일을 열어 콘텐츠를 봅니다. 이 지속성 함수는 다음 메서드를 포함하는 간단한 함수 체이닝 예제입니다.  

| 방법 | FunctionName | 설명 |
| -----  | ------------ | ----------- |
| **`RunOrchestrator`** | `<file-name>` | 지속성 오케스트레이션을 관리합니다. 이 경우 오케스트레이션이 시작되고, 목록을 만들며 세 가지 함수 호출의 결과를 목록에 추가합니다.  세 가지 함수 호출이 완료되면 목록이 반환됩니다. |
| **`SayHello`** | `<file-name>_Hello` | 함수는 hello를 반환합니다. 이것은 오케스트레이션되고 있는 비즈니스 논리를 포함하는 함수입니다. |
| **`HttpStart`** | `<file-name>_HttpStart` | 오케스트레이션 인스턴스를 시작하고 상태 확인 응답을 반환하는 [HTTP 트리거 함수](../functions-bindings-http-webhook.md)입니다. |

함수 프로젝트 및 지속성 함수를 만들었으니, 이제 로컬 컴퓨터에서 이 함수를 테스트할 수 있습니다.

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

Azure Functions Core Tools를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. Visual Studio에서 처음으로 함수를 시작할 때 이 도구를 설치하도록 요구하는 메시지가 표시됩니다.

1. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 Azure Functions Core(CLI) 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 합니다.

2. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![Azure 로컬 런타임](./media/durable-functions-create-first-csharp/functions-vs-debugging.png)

3. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여넣고 요청을 실행합니다. 다음은 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![브라우저의 localhost 함수 응답](./media/durable-functions-create-first-csharp/functions-vs-status.png)

    응답은 지속성 오케스트레이션이 성공적으로 시작되었음을 알리는 HTTP 함수의 초기 결과입니다.  아직 오케스트레이션의 최종 결과는 아닙니다.  응답에는 몇 가지 유용한 URL이 포함되어 있습니다.  현재로는 오케스트레이션의 상태를 쿼리해보겠습니다.

4. `statusQueryGetUri`에 대한 URL 값을 복사하고 브라우저의 주소에 붙여넣은 후 요청을 실행합니다.

    요청은 상태에 대한 오케스트레이션 인스턴스를 쿼리합니다. 최종 응답은 다음과 같이 표시되어야 합니다.  이는 인스턴스가 완료되었음을 나타내며, 지속성 함수의 출력 또는 결과를 포함합니다.

    ```json
    {
        "instanceId": "d495cb0ac10d4e13b22729c37e335190",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-11-08T07:07:40Z",
        "lastUpdatedTime": "2018-11-08T07:07:52Z"
    }
    ```

5. 디버깅을 중지하려면 **Shift + F5**를 누릅니다.

함수가 로컬 컴퓨터에서 제대로 실행되는지 확인한 후에 해당 프로젝트를 Azure에 게시해야 합니다.

## <a name="publish-the-project-to-azure"></a>Azure에 프로젝트 게시

프로젝트를 게시하려면 먼저 Azure 구독에 함수 앱이 있어야 합니다. Visual Studio에서 직접 함수 앱을 만들 수 있습니다.

[!INCLUDE [Publish the project to Azure](../../../includes/functions-vstools-publish.md)]

## <a name="test-your-function-in-azure"></a>Azure에서 함수 테스트

1. 게시 프로필 페이지에서 함수 앱의 기준 URL을 복사합니다. 로컬에서 함수를 테스트할 때 사용한 URL의 `localhost:port` 부분을 새 기준 URL로 바꿉니다.

    지속성 함수 HTTP 트리거를 호출하는 URL은 다음 형식이어야 합니다.

        http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>_HttpStart

2. HTTP 요청에 대한 이러한 새 URL을 브라우저의 주소 표시줄에 붙여넣습니다. 게시된 앱을 사용하는 경우 앞으로 같은 상태 응답을 가져와야 합니다.

## <a name="next-steps"></a>다음 단계

Visual Studio를 사용하여 C# 지속성 함수 앱을 만들고 게시했습니다.

> [!div class="nextstepaction"]
> [일반적인 지속성 함수 패턴에 대해 알아보기](durable-functions-concepts.md)
