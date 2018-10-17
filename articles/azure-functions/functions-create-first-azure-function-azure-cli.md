---
title: Azure CLI를 사용하여 첫 번째 함수 만들기
description: Azure CLI 및 Azure Functions Core Tools를 사용하여 서버를 사용하지 않는 실행을 위해 첫 번째 Azure Function을 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: f65ff3e38e3768dccf04e9eaa4345ffcea4daa9e
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2018
ms.locfileid: "47451123"
---
# <a name="create-your-first-function-from-the-command-line"></a>명령줄에서 첫 번째 함수 만들기

이 빠른 시작 토픽에서는 명령줄 또는 터미널에서 첫 번째 함수를 만드는 방법을 설명합니다. Azure CLI를 사용하여 함수를 호스트하고 [서버를 사용하지 않는](https://azure.microsoft.com/overview/serverless-computing/) 인프라인 함수 앱을 만듭니다. 함수 코드 프로젝트는 Azure에 함수 앱 프로젝트를 배포하는 데도 사용되는 [Azure Functions Core Tools](functions-run-local.md)를 사용하여 템플릿에서 생성됩니다.

Mac, Windows 또는 Linux 컴퓨터를 사용하여 아래 단계를 따르면 됩니다.

## <a name="prerequisites"></a>필수 조건

이 샘플을 실행하기 전에 다음이 있어야 합니다.

+ [Azure Core Tools 버전 2.x](functions-run-local.md#v2)를 설치합니다.

+ [Azure CLI]( /cli/azure/install-azure-cli)를 설치합니다. 이 문서에서 설명하는 단계를 수행하려면 Azure CLI 버전 2.0 이상이 필요합니다. `az --version`을 실행하여 버전을 찾습니다. [Azure Cloud Shell](https://shell.azure.com/bash)을 사용할 수도 있습니다.

+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>로컬 함수 앱 프로젝트 만들기

명령줄에서 다음 명령을 실행하여 현재 로컬 디렉터리의 `MyFunctionProj` 폴더에 함수 앱 프로젝트를 만듭니다. GitHub 리포지토리도 `MyFunctionProj`에서 생성됩니다.

```bash
func init MyFunctionProj
```

메시지가 표시되면 화살표 키를 사용하여 다음 언어 선택에서 작업자 런타임을 선택합니다.

+ `dotnet`: .NET 클래스 라이브러리 프로젝트(.csproj)를 만듭니다.
+ `node`: JavaScript 프로젝트를 만듭니다.

명령을 실행하는 경우 다음 출력과 같이 표시됩니다.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

## <a name="create-a-function"></a>함수 만들기

다음 명령은 새 프로젝트로 이동하고 `MyHtpTrigger`라는 HTTP 트리거된 함수를 만듭니다.

```bash
cd MyFunctionProj
func new --name MyHttpTrigger --template "HttpTrigger"
```

명령을 실행하는 경우 JavaScript 함수인 다음 출력과 같이 표시됩니다.

```output
Writing C:\functions\MyFunctionProj\MyHttpTrigger\index.js
Writing C:\functions\MyFunctionProj\MyHttpTrigger\sample.dat
Writing C:\functions\MyFunctionProj\MyHttpTrigger\function.json
```

## <a name="edit-the-function"></a>함수 편집

기본적으로 템플릿은 요청을 생성할 때 기능 키를 필요로 하는 함수를 만듭니다. Azure에서 함수를 쉽게 테스트하려면 익명 액세스를 허용하도록 함수를 업데이트해야 합니다. 이 변경을 만드는 방식은 함수 프로젝트 언어에 따라 달라집니다.

### <a name="c"></a>C\#

새 함수인 MyHttpTrigger.cs 코드 파일을 열고 함수 정의의 **AuthorizationLevel** 특성을 `anonymous`의 값으로 업데이트하고 변경 내용을 저장합니다.

```csharp
[FunctionName("MyHttpTrigger")]
        public static IActionResult Run([HttpTrigger(AuthorizationLevel.Anonymous, 
            "get", "post", Route = null)]HttpRequest req, ILogger log)
```

### <a name="javascript"></a>JavaScript

새 함수에 대한 function.json 파일을 열고, 텍스트 편집기에서 열고, **bindings.httpTrigger**의 **authLevel** 속성을 `anonymous`로 업데이트하고, 변경 내용을 저장합니다.

```json
  "bindings": [
    {
      "authLevel": "anonymous",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
  ]
```

이제 함수 키를 제공하지 않고도 Azure에서 함수를 호출할 수 있습니다. 로컬로 실행하는 경우 함수 키는 필요하지 않습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령은 함수 앱을 시작합니다. 앱은 Azure에 있는 동일한 Azure Functions 런타임을 사용하여 실행됩니다.

```bash
func host start --build
```

`--build` 옵션은 C# 프로젝트를 컴파일하는 데 필요합니다. JavaScript 프로젝트에는 이 옵션이 필요하지 않습니다.

함수 호스트가 시작되면 다음 출력과 같이 보기 편하도록 나눠서 작성합니다.

```output

                  %%%%%%
                 %%%%%%
            @   %%%%%%    @
          @@   %%%%%%      @@
       @@@    %%%%%%%%%%%    @@@
     @@      %%%%%%%%%%        @@
       @@         %%%%       @@
         @@      %%%       @@
           @@    %%      @@
                %%
                %

...

Content root path: C:\functions\MyFunctionProj
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

런타임 출력에서 `HTTPTrigger` 함수의 URL을 복사하고 브라우저의 주소 표시줄에 붙여넣습니다. 이 URL에 쿼리 문자열 `?name=<yourname>`을 추가하고 요청을 실행합니다. 다음은 로컬 함수에서 반환된 GET 요청에 대한 브라우저의 응답을 보여줍니다.

![브라우저에서 로컬로 테스트](./media/functions-create-first-azure-function-azure-cli/functions-test-local-browser.png)

이제 함수를 로컬로 실행했으므로 Azure에서 함수 앱 및 기타 필요한 리소스를 만들 수 있습니다.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>함수 앱 만들기

함수 실행을 호스트하는 함수 앱이 있어야 합니다. 함수 앱은 서버를 사용하지 않는 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 함수 앱을 만듭니다. 

다음 명령에서 `<app_name>` 자리 표시자 및 `<storage_name>`의 저장소 계정 이름을 고유한 함수 앱 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인으로 사용되므로 이름이 Azure의 모든 앱에서 고유해야 합니다. _deployment-source-url_ 매개 변수는 "Hello World" HTTP 트리거 함수를 포함하는 GitHub의 샘플 리포지토리입니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

_consumption-plan-location_ 매개 변수를 설정하는 것은 해당 함수 앱이 소비 호스팅 계획에서 호스팅된다는 뜻입니다. 이 서버리스 계획에서는 리소스가 필요에 따라 함수를 통해 동적으로 추가되며 함수가 실행된 것에 대해서만 지불합니다. 자세한 내용은 [올바른 호스팅 계획 선택](functions-scale.md)을 참조하세요.

함수 앱을 만들었으면 Azure CLI는 다음 예와 비슷한 정보를 표시합니다.

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

## <a name="configure-the-function-app"></a>함수 앱 구성

Core Tools 버전 2.x는 템플릿을 사용하여 Azure Functions 2.x 런타임에 대한 프로젝트를 만듭니다. 이로 인해 버전 2.x 런타임이 Azure에서 사용되는지 확인해야 합니다. `FUNCTIONS_WORKER_RUNTIME` 응용 프로그램 설정을 `~2`로 설정하면 함수 앱이 최신 2.x 버전에 고정됩니다. [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set) 명령을 사용하여 응용 프로그램 설정을 설정합니다.

다음 Azure CLI 명령에서 `<app_name>은 함수 앱의 이름입니다.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings FUNCTIONS_WORKER_RUNTIME=~2
```

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]
