---
title: Azure Functions와 Azure App Configuration에 대한 빠른 시작 | Microsoft Docs
description: Azure Functions와 함께 Azure App Configuration을 사용하는 경우 참고할 수 있는 빠른 시작입니다.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: balans
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: Azure Functions
ms.workload: tbd
ms.date: 02/24/2019
ms.author: yegu
ms.openlocfilehash: c09cb0f93f05a9574543ebabb398148638092c73
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864785"
---
# <a name="quickstart-create-an-azure-function-with-app-configuration"></a>빠른 시작: App Configuration으로 Azure 함수 만들기

Azure App Configuration은 Azure의 관리형 구성 서비스로서, 코드와 분리된 한 곳에서 모든 애플리케이션 설정을 쉽게 저장하고 관리할 수 있습니다. 이 빠른 시작은 서비스를 Azure 함수로 통합하는 방법을 보여줍니다. 

이 빠른 시작의 단계는 임의의 코드 편집기를 사용하여 수행할 수 있습니다. [Visual Studio Code](https://code.visualstudio.com/)는 Windows, macOS 및 Linux 플랫폼에서 사용할 수 있는 훌륭한 옵션입니다.

![로컬로 수행된 빠른 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="prerequisites"></a>필수 조건

이 빠른 시작을 수행하려면 [Visual Studio 2019](https://visualstudio.microsoft.com/vs)를 설치합니다. **Azure 개발** 워크로드도 설치되어 있어야 합니다. [최신 Azure Functions 도구](../azure-functions/functions-develop-vs.md#check-your-tools-version)도 설치합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-an-app-configuration-store"></a>앱 구성 저장소 만들기

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **구성 탐색기** >  **+ 만들기**를 선택하여 다음 키-값 쌍을 추가합니다.

    | 키 | 값 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration의 정보 |

    지금은 **레이블**과 **콘텐츠 형식**을 비워 두세요.

## <a name="create-a-function-app"></a>함수 앱 만들기

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>앱 구성 저장소에 연결

1. 마우스 오른쪽 단추로 프로젝트를 클릭하고, **NuGet 패키지 관리**를 선택합니다. **찾아보기** 탭에서 다음 NuGet 패키지를 검색하여 프로젝트에 추가합니다. 찾을 수 없으면 **시험판 포함** 확인란을 선택합니다.

    ```
    Microsoft.Extensions.Configuration.AzureAppConfiguration 1.0.0 preview or later
    ```

2. *Function1.cs*를 열고, .NET Core App Configuration 공급자에 대한 참조를 추가합니다.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. `builder.AddAzureAppConfiguration()`을 호출하여 App Configuration을 사용하도록 `Run` 메서드를 업데이트합니다.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        var config = builder.Build();
        string message = config["TestApp:Settings:Message"];
        message = message ?? req.Query["message"];

        string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
        dynamic data = JsonConvert.DeserializeObject(requestBody);
        message = message ?? data?.message;

        return message != null
            ? (ActionResult) new OkObjectResult(message)
            : new BadRequestObjectResult("Please pass a message from a configuration store, on the query string or in the request body");
    }
    ```

## <a name="test-the-function-locally"></a>로컬에서 함수 테스트

1. **ConnectionString**이라는 환경 변수를 설정하고, 앱 구성 저장소에 대한 액세스 키로 설정합니다. Windows 명령 프롬프트를 사용하는 경우 다음 명령을 실행하고, 명령 프롬프트를 다시 시작하여 변경 내용을 적용합니다.

        setx ConnectionString "connection-string-of-your-app-configuration-store"

    Windows PowerShell을 사용하는 경우 다음 명령을 실행합니다.

        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"

    macOS 또는 Linux를 사용하는 경우 다음 명령을 실행합니다.

        export ConnectionString='connection-string-of-your-app-configuration-store'

2. 함수를 테스트하려면 F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 **Azure Functions Core(CLI)** 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

3. Azure Functions 런타임 출력에서 함수의 URL을 복사합니다.

    ![VS에서 빠른 시작 함수 디버깅](./media/quickstarts/function-visual-studio-debugging.png)

4. HTTP 요청에 대한 URL을 브라우저의 주소 표시줄에 붙여 넣습니다. 다음 이미지에서는 함수에서 반환된 로컬 GET 요청에 대한 브라우저의 응답을 보여 줍니다.

    ![빠른 시작 함수 로컬 시작](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 새 앱 구성 저장소를 만들고, Azure 함수 앱에서 사용했습니다. App Configuration을 사용하는 방법을 자세히 알아보려면 인증에 대해 설명하는 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)
