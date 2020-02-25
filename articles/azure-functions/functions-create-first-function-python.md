---
title: Azure Functions에서 HTTP 요청에 대한 서버리스 Python 함수 만들기
description: Azure Functions를 사용하여 서버리스 Python 코드를 만들어 클라우드에 배포합니다.
ms.date: 02/11/2020
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: 16d4d5627ea297d825092009511915f5b6e734b6
ms.sourcegitcommit: 0eb0673e7dd9ca21525001a1cab6ad1c54f2e929
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77212426"
---
# <a name="quickstart-create-a-python-function-in-azure-that-responds-to-http-requests"></a>빠른 시작: Azure에서 HTTP 요청에 응답하는 Python 함수 만들기

이 문서에서는 명령줄 도구를 사용하여 HTTP 요청에 응답하는 Python 함수를 만듭니다. 코드를 로컬로 테스트한 후 서버리스 Azure Functions 환경에 배포합니다. 이 빠른 시작을 완료하면 Azure 계정에서 약간의 비용(몇 USD 센트)이 발생합니다.

또한 이 문서의 [Visual Studio Code 기반 버전](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-python)도 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](./functions-run-local.md#v2) 버전 2.7.1846 이상
- [Azure CLI](/cli/azure/install-azure-cli) 버전 2.0.76 이상 
- [Python 3.7.4 - 64비트](https://www.python.org/downloads/release/python-374/). Python 3.7.4는 Azure Functions를 통해 확인되며, Python 3.8 이상 버전은 아직 지원되지 않습니다.

### <a name="prerequisite-check"></a>필수 구성 요소 확인

1. 터미널 또는 명령 창에서 `func --version`을 실행하여 Azure Functions Core Tools가 2.7.1846 버전 이상인지 확인합니다.
1. `az --version`을 실행하여 Azure CLI 버전이 2.0.76 이상인지 확인합니다.
1. `az login`을 실행하여 Azure에 로그인하고 활성 구독을 확인합니다.
1. `python --version`(Linux/MacOS) 또는 `py --version`(Windows)을 실행하여 Python 버전 보고서 3.7.x를 확인합니다.

## <a name="create-and-activate-a-virtual-environment"></a>가상 환경 만들기 및 활성화

적절한 폴더에서 다음 명령을 실행하여 `.venv`라는 가상 환경을 만들고 활성화합니다. Azure Functions에서 지원하는 Python 3.7을 사용해야 합니다.


# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Python에서 venv 패키지를 Linux 배포에 설치하지 않은 경우 다음 명령을 실행합니다.

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

활성화된 가상 환경에서 이후의 모든 명령을 실행합니다. (가상 환경을 종료하려면 `deactivate`를 실행하세요.)

## <a name="create-a-local-function-project"></a>로컬 함수 프로젝트 만들기

Azure Functions에서 함수 프로젝트는 각각 특정 트리거에 응답하는 하나 이상의 개별 함수에 대한 컨테이너입니다. 프로젝트의 모든 함수는 동일한 로컬 및 호스팅 구성을 공유합니다. 이 섹션에서는 단일 함수가 포함된 함수 프로젝트를 만듭니다.

1. 가상 환경에서 `func init` 명령을 실행하여 지정된 런타임을 사용하여 함수 프로젝트를 *LocalFunctionProj*라는 폴더에 만듭니다.

    ```
    func init LocalFunctionProj --python
    ```
    
    이 폴더에는 [local.settings.json](functions-run-local.md#local-settings-file) 및 [host.json](functions-host-json.md)이라는 구성 파일을 포함하여 프로젝트의 다양한 파일이 있습니다. *local.settings.json*은 Azure에서 다운로드한 비밀을 포함할 수 있으므로 이 파일은 기본적으로 *.gitignore* 파일의 원본 제어에서 제외됩니다.

    > [!TIP]
    > 함수 프로젝트는 특정 런타임에 연결되므로 프로젝트의 모든 함수는 동일한 언어로 작성해야 합니다.

1. 프로젝트 폴더로 이동합니다.

    ```
    cd LocalFunctionProj
    ```
    
1. 다음 명령을 사용하여 함수를 프로젝트에 추가합니다. 여기서 `--name` 인수는 함수의 고유 이름(이 경우 HttpExample)을 지정하고 `--template` 인수는 함수의 트리거(HTTP 트리거)를 지정합니다. 이 `func new` 명령은 프로젝트의 선택한 언어에 적합한 코드 파일과 *function.json*이라는 구성 파일을 포함하는 함수 이름과 일치하는 하위 폴더를 만듭니다.

    ```
    func new --name HttpExample --template "HTTP trigger"
    ```

### <a name="optional-examine-the-file-contents"></a>(선택 사항) 파일 내용 검사

원하는 경우 [로컬에서 함수 실행](#run-the-function-locally)으로 건너뛰고, 나중에 파일 내용을 검사할 수 있습니다.

#### <a name="__init__py"></a>\_\_init\_\_.py

*\_\_init\_\_.py*에는 *function.json*의 구성에 따라 트리거되는 `main()` Python 함수가 포함되어 있습니다.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
             "Please pass a name on the query string or in the request body",
             status_code=400
        )
```

HTTP 트리거의 경우 함수는 *function.json*에서 정의한 대로 `req` 변수의 요청 데이터를 받습니다. `req`는 [azure.functions.HttpRequest 클래스](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. *function.json*에 `$return`으로 정의되는 반환 개체는 [azure.functions.HttpResponse 클래스](/python/api/azure-functions/azure.functions.httpresponse)의 인스턴스입니다. 자세한 내용은 [Azure Functions HTTP 트리거 및 바인딩](functions-bindings-http-webhook.md)을 참조하세요.

#### <a name="functionjson"></a>function.json

*function.json*은 트리거 형식을 포함하여 함수의 입력 및 출력 `bindings`를 정의하는 구성 파일입니다. 원하는 경우 다른 Python 파일을 호출하도록 `scriptFile`을 변경할 수 있습니다.

```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "authLevel": "function",
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
}
```

각 바인딩에는 명령, 형식 및 고유한 이름이 필요합니다. HTTP 트리거의 입력 바인딩은 [`httpTrigger`](functions-bindings-http-webhook-trigger.md) 형식이고, 출력 바인딩은 [`http`](functions-bindings-http-webhook-output.md) 형식입니다.


## <a name="run-the-function-locally"></a>로컬에서 함수 실행

*LocalFunctionProj* 폴더에서 로컬 Azure Functions 런타임 호스트를 시작하여 함수를 시작합니다.

```
func start
```

다음과 같은 출력이 표시됩니다. (HttpExample이 아래와 같이 표시되지 않으면 *HttpExample* 폴더 내에서 호스트를 시작했을 가능성이 높습니다. 이 경우 **Ctrl**+**C**를 사용하여 호스트를 중지하고, 부모 *LocalFunctionProj* 폴더로 이동하여 `func start`를 다시 실행합니다.)

```output
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
```

이 출력에서 `HttpExample` 함수의 URL을 브라우저로 복사하고 `?name=<your-name>` 쿼리 문자열을 추가하여 전체 URL을 `http://localhost:7071/api/HttpExample?name=Functions`와 같이 만듭니다. 브라우저에서 `Hello Functions`와 같은 메시지가 표시됩니다.

![브라우저에서 로컬로 실행되는 함수의 결과](./media/functions-create-first-function-python/function-test-local-browser.png)

`func start`를 실행한 터미널에서도 요청 시 로그 출력이 표시됩니다.

준비가 되면 **Ctrl**+**C**를 사용하여 함수 호스트를 중지합니다.

## <a name="create-supporting-azure-resources-for-your-function"></a>함수를 지원하는 Azure 리소스 만들기

함수 코드를 Azure에 배포하기 전에 다음 세 가지 리소스를 만들어야 합니다.

- 리소스 그룹 - 관련 리소스에 대한 논리 컨테이너입니다.
- Azure Storage 계정 - 프로젝트에 대한 상태 및 기타 정보를 유지 관리합니다.
- Azure 함수 앱 - 함수 코드를 실행할 수 있는 환경을 제공합니다. 함수 앱은 로컬 함수 프로젝트에 매핑되며, 함수를 논리적 단위로 그룹화하여 리소스를 더 쉽게 관리, 배포 및 공유할 수 있습니다.

이러한 항목은 Azure CLI 명령을 사용하여 만듭니다. 각 명령은 완료 시 JSON 출력을 제공합니다.

1. 아직 로그인하지 않은 경우 [az login](/cli/azure/reference-index#az-login) 명령을 사용하여 Azure에 로그인합니다.

    ```azurecli
    az login
    ```
    
1. [az group create](/cli/azure/group#az-group-create) 명령을 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 `AzureFunctionsQuickstart-rg`라는 리소스 그룹을 `westeurope` 지역에 만듭니다. (일반적으로 `az account list-locations` 명령에서 사용 가능한 지역을 사용하여 리소스 그룹과 리소스를 가까운 지역에 만듭니다.)

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
    
    > [!NOTE]
    > Linux 및 Windows 앱을 동일한 리소스 그룹에 호스트할 수 없습니다. Windows 함수 앱 또는 웹앱이 포함된 `AzureFunctionsQuickstart-rg`이라는 기존 리소스 그룹이 있는 경우 다른 리소스 그룹을 사용해야 합니다.
    
1. [az storage account create](/cli/azure/storage/account#az-storage-account-create) 명령을 사용하여 범용 스토리지 계정을 리소스 그룹 및 지역에 만듭니다. 다음 예제에서 `<storage_name>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다. 이름은 3~24자의 숫자와 소문자만 포함해야 합니다. `Standard_LRS`는 일반적인 범용 계정을 지정합니다.

    ```azurecli
    az storage account create --name <storage_name> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```
    
    이 빠른 시작의 경우 스토리지 계정에 대한 약간의 비용(몇 USD 센트)만 발생합니다.
    
1. [az functionapp create](/cli/azure/functionapp#az-functionapp-create) 명령을 사용하여 Functions 앱을 만듭니다. 다음 예제에서 `<storage_name>`을 이전 단계에서 사용한 계정의 이름으로 바꾸고, `<app_name>`을 적절하고 전역적으로 고유한 이름으로 바꿉니다. `<app_name>`은 함수 앱의 기본 DNS 도메인이기도 합니다.

    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --name <app_name> --storage-account <storage_name>
    ```
    
    이 명령은 [Azure Functions 소비 계획](functions-scale.md#consumption-plan)에 따라 지정된 언어 런타임을 실행하는 함수 앱을 만듭니다. 여기서 발생하는 사용량에 대한 비용은 무료입니다. 또한 이 명령은 동일한 리소스 그룹에 연결된 Azure Application Insights 인스턴스를 프로비저닝하여 함수 앱을 모니터링하고 로그를 볼 수 있습니다. 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요. 인스턴스를 활성화할 때까지 비용이 발생하지 않습니다.
    
## <a name="deploy-the-function-project-to-azure"></a>Azure에 함수 프로젝트 배포

이제 필요한 리소스가 있으면 [func azure functionapp publish](functions-run-local.md#project-file-deployment) 명령을 사용하여 로컬 함수 프로젝트를 Azure의 함수 앱에 배포할 수 있습니다. 다음 예제에서 `<app_name>`을 앱 이름으로 바꿉니다.

```
func azure functionapp publish <app_name>
```

"... 이름의 앱을 찾을 수 없습니다."라는 오류가 표시되면 Azure에서 이전 `az functionapp create` 명령 후에 앱을 완전히 초기화하지 않았을 수 있으므로 몇 초 정도 기다렸다가 다시 시도하세요.

publish 명령은 다음 출력과 비슷한 결과를 표시합니다(간단히 하기 위해 잘랐음).

```output
Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
```

## <a name="invoke-the-function-on-azure"></a>Azure에서 함수 호출

함수에서 HTTP 트리거를 사용하므로 브라우저 또는 도구(예: curl)를 사용하여 URL에 대한 HTTP 요청을 수행하여 호출합니다. 두 경우 모두에서 `code` URL 매개 변수는 함수 엔드포인트를 사용하여 호출 권한을 부여하는 고유한 함수 키입니다.

# <a name="browser"></a>[브라우저](#tab/browser)

publish 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하여 `&name=Azure` 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](./media/functions-create-first-function-python/function-test-cloud-browser.png)


# <a name="curl"></a>[curl](#tab/curl)

**호출 URL**을 사용하고 `&name=Azure` 매개 변수를 추가하여 [curl](https://curl.haxx.se/)을 실행합니다. 이 명령의 출력은 "Hello Azure" 텍스트입니다.

![curl을 사용한 Azure에서 실행되는 함수의 출력](./media/functions-create-first-function-python/function-test-cloud-curl.png)

---

> [!TIP]
> 게시된 Python 앱의 거의 실시간 로그를 보려면 [Application Insights 라이브 메트릭 스트림](functions-monitoring.md#streaming-logs)을 사용합니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 [Azure Storage 큐 출력 바인딩 추가](functions-add-output-binding-storage-queue-python.md) 단계로 계속 진행하는 경우 이미 수행한 작업을 기반으로 하여 빌드되는 모든 리소스를 그대로 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Storage 큐 출력 바인딩 추가](functions-add-output-binding-storage-queue-python.md)
