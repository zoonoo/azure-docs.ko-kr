---
title: Azure에서 첫 번째 Python 함수 만들기
description: Azure에서 Azure Functions Core Tools 및 Azure CLI를 사용하여 첫 번째 Azure 함수를 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 08/29/2018
ms.topic: quickstart
ms.service: functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 2c14cdf963d1d6c792e6101d8a2179a560dcf096
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2019
ms.locfileid: "55731260"
---
# <a name="create-your-first-python-function-in-azure-preview"></a>Azure에서 첫 번째 Python 함수 만들기(미리 보기)

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

이 빠른 시작 문서에서는 Azure CLI를 사용하여 Linux에서 실행되는 첫 번째 [서버리스](https://azure.com/serverless) Python 함수 앱을 만드는 방법을 안내합니다. [Azure Functions Core Tools](functions-run-local.md)를 사용하여 함수 코드를 로컬로 만든 후 Azure에 배포합니다. Linux에서 함수 앱을 실행하기 위한 미리 보기 고려 사항에 대해 자세히 알아보려면 [이 Linux의 함수 문서](https://aka.ms/funclinux)를 참조하세요.

다음 단계는 Mac, Windows 또는 Linux 컴퓨터에서 지원됩니다.

## <a name="prerequisites"></a>필수 조건

빌드하고 로컬로 테스트하려면 다음을 수행해야 합니다.

+ [Python 3.6](https://www.python.org/downloads/)을 설치합니다.

+ [Azure Functions Core Tools](functions-run-local.md#v2) 버전 2.2.70 이상을 설치합니다(.NET Core 2.x SDK 필요).

Azure에서 게시 및 실행하려면:

+ [Azure CLI]( /cli/azure/install-azure-cli) 버전 2.x 이상을 설치합니다.

+ 활성 상태인 Azure 구독이 필요합니다.
  [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>가상 환경 만들기 및 활성화

함수 프로젝트를 만들려면 Python 3.6 가상 환경에서 작업해야 합니다. 다음 명령을 실행하여 `.env`라는 가상 환경을 만들고 활성화합니다.

```bash
# In Bash
python3.6 -m venv .env
source .env/bin/activate

# In PowerShell
py -3.6 -m venv .env
.env\scripts\activate
```

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

이제 로컬 Functions 프로젝트를 만들 수 있습니다. 이 디렉터리는 Azure의 함수 앱에 해당합니다. 동일한 로컬 및 호스팅 구성을 공유하는 함수를 여러 개 포함할 수 있습니다.

터미널 창 또는 명령 프롬프트에서 다음 명령을 실행합니다.

```bash
func init MyFunctionProj
```

원하는 런타임으로 **python**을 선택합니다.

```output
Select a worker runtime:
1. dotnet
2. node
3. python
```

다음 출력과 유사한 출력이 표시됩니다.

```output
Installing wheel package
Installing azure-functions package
Installing azure-functions-worker package
Running pip freeze
Writing .gitignore
Writing host.json
Writing local.settings.json
Writing /MyFunctionProj/.vscode/extensions.json
```

_MyFunctionProj_라는 새 폴더가 만들어집니다. 계속하려면 이 폴더로 디렉터리를 변경합니다.

```bash
cd MyFunctionProj
```

## <a name="create-a-function"></a>함수 만들기

함수를 만들려면 다음 명령을 실행합니다.

```bash
func new
```

템플릿으로 `HTTP Trigger`를 선택하고 `HttpTrigger`의 **함수 이름**을 제공합니다.

```output
Select a template:
1. Blob trigger
2. Cosmos DB trigger
3. Event Grid trigger
4. Event Hub trigger
5. HTTP trigger
6. Queue trigger
7. Service Bus Queue trigger
8. Service Bus Topic trigger
9. Timer trigger

Choose option: 5
Function name: HttpTrigger
```

다음 출력과 유사한 출력이 표시됩니다.

```output
Writing /MyFunctionProj/HttpTrigger/sample.dat
Writing /MyFunctionProj/HttpTrigger/__init__.py
Writing /MyFunctionProj/HttpTrigger/function.json
The function "HttpTrigger" was created successfully from the "HTTP trigger" template.
```

_HttpTrigger_라는 하위 폴더가 만들어집니다. 여기에 기본 스크립트 파일인 `__init__.py` 및 함수에서 사용되는 트리거 및 바인딩을 설명하는 `function.json` 파일이 포함됩니다. 프로그래밍 모델에 대해 자세히 알아보기 위해 [Azure Functions Python 개발자 가이드](functions-reference-python.md)를 참조할 수 있습니다.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령을 사용하여 Functions 호스트를 로컬로 실행합니다.

```bash
func host start
```

Functions 호스트가 시작되면 HTTP 트리거 함수의 URL이 출력됩니다. (가독성을 위해 전체 출력이 잘렸습니다.)

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
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.
...

Http Functions:

        HttpTrigger: http://localhost:7071/api/HttpTrigger
```

출력에서 함수의 URL을 복사하고 브라우저의 주소 표시줄에 붙여넣습니다. 이 URL에 쿼리 문자열 `?name=<yourname>`을 추가하고 요청을 실행합니다.

    http://localhost:7071/api/HttpTrigger?name=<yourname>

다음 스크린샷에서는 브라우저에서 트리거될 때 함수의 응답을 보여줍니다.

![test](./media/functions-create-first-function-python/function-test-local-browser.png)

이제 함수 앱 및 Azure에 게시하기 위해 필요한 기타 리소스를 만들 준비가 되었습니다.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Azure에서 Linux 함수 앱 만들기

함수 앱은 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다. [az functionapp create](/cli/azure/functionapp) 명령을 사용하여 **Linux에서 실행되는 Python 함수 앱**을 만듭니다.

`<app_name>` 자리 표시자 및 `<storage_name>`의 스토리지 계정 이름을 대신하여 고유한 함수 앱 이름을 사용하여 다음 명령을 실행합니다. `<app_name>`은 함수 앱의 기본 DNS 도메인이기도 합니다. 이 이름은 Azure의 모든 앱에서 고유해야 합니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <app_name> --storage-account  <storage_name>
```

> [!NOTE]
> Linux가 아닌 App Service 앱이 포함된 `myResourceGroup`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다. Windows 앱과 Linux 앱을 동일한 리소스 그룹에 호스팅할 수 없습니다.  

함수 앱이 만들어지면 다음 메시지가 표시됩니다.

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

이제 Azure에서 함수 앱에 로컬 함수 프로젝트를 게시할 준비가 되었습니다.

## <a name="deploy-the-function-app-project-to-azure"></a>함수 앱 프로젝트를 Azure에 배포

Azure Functions Core Tools를 사용하여 다음 명령을 실행합니다. `<app_name>`을 이전 단계의 앱 이름으로 바꿉니다.

```bash
func azure functionapp publish <app_name>
```

다음 출력과 같이 보기 편하도록 나눠서 표시됩니다.

```output
Getting site publishing info...

...

Preparing archive...
Uploading content...
Upload completed successfully.
Deployment completed successfully.
Syncing triggers...
```

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>다음 단계

Python을 사용하여 Azure Functions 개발에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Functions Python 개발자 가이드](functions-reference-python.md)
> [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
