---
title: Azure에서 HTTP 트리거 함수 만들기
description: Azure에서 Azure Functions Core Tools 및 Azure CLI를 사용하여 첫 번째 Azure 함수를 만드는 방법을 알아봅니다.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 04/24/2019
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: python
manager: jeconnoc
ms.openlocfilehash: 88e8d543e8bc3c3ae07133ec333b33c4486c2684
ms.sourcegitcommit: 4c2b9bc9cc704652cc77f33a870c4ec2d0579451
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65864508"
---
# <a name="create-an-http-triggered-function-in-azure"></a>Azure에서 HTTP 트리거 함수 만들기

[!INCLUDE [functions-python-preview-note](../../includes/functions-python-preview-note.md)]

이 문서에서는 명령줄 도구를 사용하여 Azure Functions에서 실행되는 Python 프로젝트를 만드는 방법을 보여줍니다. 만드는 함수는 HTTP 요청에 의해 트리거됩니다. 마지막으로, Azure에서 [서버리스 함수](functions-scale.md#consumption-plan)로 실행되도록 프로젝트를 게시합니다.

이 문서는 Azure Functions에 대한 두 편의 빠른 시작 중 첫 번째입니다. 이 문서를 완료한 후에는 함수에 [Azure Storage 큐 출력 바인딩](functions-add-output-binding-storage-queue-python.md)을 추가합니다.

## <a name="prerequisites"></a>필수 조건

시작하려면 다음 조건을 충족해야 합니다.

+ [Python 3.6](https://www.python.org/downloads/)을 설치합니다.

+ [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.6.666 이상을 설치합니다.

+ [Azure CLI](/cli/azure/install-azure-cli) 버전 2.x 이상을 설치합니다.

+ 활성 Azure 구독.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-and-activate-a-virtual-environment"></a>가상 환경 만들기 및 활성화

Python 함수를 로컬로 개발하고 테스트하려면 Python 3.6 환경을 사용해야 합니다. 다음 명령을 실행하여 `.env`라는 가상 환경을 만들고 활성화합니다.

### <a name="bash-or-a-terminal-window"></a>Bash 또는 터미널 창:

```bash
python3.6 -m venv .env
source .env/bin/activate
```

### <a name="powershell-or-a-windows-command-prompt"></a>PowerShell 또는 Windows 명령 프롬프트:

```powershell
py -3.6 -m venv .env
.env\scripts\activate
```

나머지 명령은 가상 환경 내에서 실행됩니다.

## <a name="create-a-local-functions-project"></a>로컬 Functions 프로젝트 만들기

Functions 프로젝트는 Azure의 함수 앱에 해당합니다. 동일한 로컬 및 호스팅 구성을 공유하는 함수를 여러 개 포함할 수 있습니다.

가상 환경에서 다음 명령을 실행하고, 작업자 런타임으로 **python**을 선택합니다.

```command
func init MyFunctionProj
```

_MyFunctionProj_ 라는 폴더가 만들어지며, 이 폴더에는 다음과 같은 세 개 파일이 포함되어 있습니다.

* `local.settings.json` 파일은 로컬에서 실행될 때 앱 설정과 연결 문자열을 저장하는 데 사용됩니다. 이 파일은 Azure에 게시되지 않습니다.
* `requirements.txt` 파일에는 Azure에 게시할 때 설치되는 패키지 목록이 포함되어 있습니다.
* `host.json` 파일에는 함수 앱의 모든 함수에 영향을 주는 글로벌 구성 옵션이 포함되어 있습니다. 이 파일은 Azure에 게시됩니다.

다음과 같이 새 MyFunctionProj 폴더로 이동합니다.

```command
cd MyFunctionProj
```

다음으로, 확장 번들을 사용하도록 host.json 파일을 업데이트합니다.  

## <a name="reference-bindings"></a>바인딩 참조

확장 번들을 사용하면 향후 쉽게 바인딩 확장을 추가할 수 있습니다. 또한 .NET Core 2.x SDK를 설치할 필요가 없습니다. 확장 번들을 사용하려면 Core Tools 2.6.1071 이상 버전이 필요합니다. 

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

이제 프로젝트에 함수를 추가할 수 있습니다.

## <a name="create-a-function"></a>함수 만들기

프로젝트에 함수를 추가하려면 다음 명령을 실행합니다.

```command
func new
```

**HTTP 트리거** 템플릿을 선택하고 함수 이름으로 `HttpTrigger`를 입력한 다음, Enter 키를 누릅니다.

_HttpTrigger_ 라는 하위 폴더가 만들어지며, 이 폴더에는 다음 파일이 포함되어 있습니다.

* **function.json**: 함수, 트리거 및 기타 바인딩을 정의하는 구성 파일입니다. 이 파일을 검토하여 `scriptFile` 값이 함수가 들어 있는 파일을 가리키는지 확인할 수 있으며, 호출 트리거 및 바인딩은 `bindings` 배열에 정의됩니다.

  각 바인딩에는 명령, 형식 및 고유한 이름이 필요합니다. HTTP 트리거의 입력 바인딩은 [`httpTrigger`](functions-bindings-http-webhook.md#trigger) 형식이고, 출력 바인딩은 [`http`](functions-bindings-http-webhook.md#output) 형식입니다.

* **\_\_init\_\_.py**: HTTP 트리거 함수인 스크립트 파일입니다. 이 스크립트를 검토하여 기본값 `main()`이 포함되어 있는지 확인할 수 있습니다. 트리거의 HTTP 데이터는 `req`라는 바인딩 매개 변수를 사용하여 이 함수에 전달됩니다. function.json에 정의되는 `req`는 [azure.functions.HttpRequest 클래스](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. 

    function.json에 `$return`으로 정의되는 반환 개체는 [azure.functions.HttpResponse 클래스](/python/api/azure-functions/azure.functions.httpresponse)의 인스턴스입니다. 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩](functions-bindings-http-webhook.md)을 참조하세요.

## <a name="run-the-function-locally"></a>로컬에서 함수 실행

다음 명령은 Azure에 있는 동일한 Azure Functions 런타임을 사용하여 로컬로 실행되는 함수 앱을 시작합니다.

```bash
func host start
```

Functions 호스트가 시작되면 다음과 같이 보기 편하도록 분할된 출력이 표시됩니다.

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

        HttpTrigger: http://localhost:7071/api/MyHttpTrigger

[8/27/2018 10:38:27 PM] Host started (29486ms)
[8/27/2018 10:38:27 PM] Job host started
```

런타임 출력에서 `HttpTrigger` 함수의 URL을 복사하고 브라우저의 주소 표시줄에 붙여넣습니다. 이 URL에 쿼리 문자열 `?name=<yourname>`을 추가하고 요청을 실행합니다. 다음은 로컬 함수에서 반환된 GET 요청에 대한 브라우저의 응답을 보여줍니다.

![브라우저에서 로컬로 테스트](./media/functions-create-first-function-python/function-test-local-browser.png)

이제 함수를 로컬로 실행했으므로 Azure에서 함수 앱 및 기타 필요한 리소스를 만들 수 있습니다.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app-in-azure"></a>Azure에서 함수 앱 만들기

함수 앱은 함수 코드 실행을 위한 환경을 제공합니다. 이를 통해 함수를 논리 단위로 그룹화하여 더욱 쉽게 리소스를 관리, 배포 및 공유할 수 있습니다.

`<APP_NAME>` 자리 표시자 및 `<STORAGE_NAME>`의 스토리지 계정 이름을 대신하여 고유한 함수 앱 이름을 사용하여 다음 명령을 실행합니다. `<APP_NAME>`은 함수 앱의 기본 DNS 도메인이기도 합니다. 이 이름은 Azure의 모든 앱에서 고유해야 합니다.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --os-type Linux \
--consumption-plan-location westeurope  --runtime python \
--name <APP_NAME> --storage-account  <STORAGE_NAME>
```

> [!NOTE]
> Linux 및 Windows 앱을 동일한 리소스 그룹에 호스트할 수 없습니다. Windows 함수 앱 또는 웹앱이 포함된 `myResourceGroup`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다.

이제 로컬 함수 프로젝트를 Azure의 함수 앱에 게시할 준비가 되었습니다.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

## <a name="next-steps"></a>다음 단계

HTTP 트리거 함수를 사용하여 Python 함수 프로젝트를 만들고, 로컬 머신에서 실행하고, Azure에 배포했습니다. 이제 아래 방법으로 함수를 확장하겠습니다.

> [!div class="nextstepaction"]
> [Azure Storage 큐 출력 바인딩 추가](functions-add-output-binding-storage-queue-python.md)
