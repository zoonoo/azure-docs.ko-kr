---
title: Python 함수에 Azure Storage 큐 바인딩 추가
description: 출력 바인딩을 사용하여 Python 함수와 Azure Storage 큐를 통합합니다.
ms.date: 01/15/2020
ms.topic: quickstart
ms.openlocfilehash: 6cea44dca666bbf002de6e2b7dd283f49ac7bd5a
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485168"
---
# <a name="add-an-azure-storage-queue-binding-to-your-python-function"></a>Python 함수에 Azure Storage 큐 바인딩 추가

이 문서에서는 [HTTP 트리거 Python 함수 만들기](functions-create-first-function-python.md)에서 만든 함수 및 스토리지 계정과 Azure Storage 큐를 통합합니다. 이 통합은 데이터를 HTTP 요청에서 큐의 메시지에 쓰는 *출력 바인딩*을 사용하여 달성할 수 있습니다. 이 문서를 완료하면 이전의 빠른 시작에서 소요된 몇 USD 센트를 초과하는 추가 비용이 발생하지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

- [HTTP 트리거 Python 함수 만들기](functions-create-first-function-python.md) 빠른 시작을 완료합니다. 해당 문서의 끝에서 리소스를 이미 정리한 경우 단계를 다시 수행하여 Azure에서 Functions 앱을 다시 만들지만 리소스는 그대로 유지합니다.

## <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage 연결 문자열 검색

이전 빠른 시작의 Azure에서 함수 앱을 만들 때 Storage 계정도 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 설정을 *local.settings.json* 파일에 다운로드하면 함수를 로컬로 실행할 때 동일한 계정의 Storage 큐에 해당 연결 쓰기를 사용할 수 있습니다. 

1. 프로젝트의 루트에서 다음 명령을 실행합니다. 여기서 `<app_name>`을 이전 빠른 시작의 함수 앱 이름으로 바꿉니다. 이 명령은 파일의 기존 값을 덮어씁니다.

    ```
    func azure functionapp fetch-app-settings <app_name>
    ```
    
1. *local.settings.json*을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage`라는 값을 찾습니다. 이 문서의 다른 섹션에서 `AzureWebJobsStorage` 이름과 연결 문자열을 사용합니다.

> [!IMPORTANT]
> *local.settings.json*에는 Azure에서 다운로드한 비밀이 포함되어 있으므로 항상 이 파일을 원본 제어에서 제외하세요. 로컬 함수 프로젝트를 사용하여 만든 *.gitignore* 파일은 기본적으로 해당 파일이 제외됩니다.

## <a name="add-an-output-binding-to-functionjson"></a>function.json에 출력 바인딩 추가

함수에는 하나의 트리거만 있을 수 있지만 여러 개의 입력 및 출력 바인딩이 있을 수 있으므로 사용자 지정 통합 코드를 작성하지 않고도 다른 Azure 서비스 및 리소스에 연결할 수 있습니다. 함수 폴더의 *function.json* 파일에서 해당 함수에 사용하는 언어에 맞게 이러한 바인딩을 선언합니다.

이전 빠른 시작에서 *HttpExample* 폴더의 *function.json* 파일에 있는 `bindings` 컬렉션에는 두 개의 바인딩이 포함되어 있습니다.

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

각 바인딩에는 적어도 하나의 형식, 방향 및 이름이 있습니다. 위의 예제에서 첫 번째 바인딩은 `in` 방향의 `httpTrigger` 형식입니다. `in` 방향의 경우 `name`은 트리거에서 호출할 때 함수로 보내는 입력 매개 변수의 이름을 지정합니다.

두 번째 바인딩은 `out` 방향의 `http` 형식입니다. 이 경우 `$return`의 특수 `name`은 이 바인딩에서 입력 매개 변수를 제공하지 않고 함수의 반환 값을 사용함을 나타냅니다.

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

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
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

이 경우 `msg`가 출력 인수로 함수에 제공됩니다. `queue` 형식의 경우 `queueName`에는 큐 이름을 지정하고, `connection`에는 *local.settings.json*에 있는 Azure Storage 연결의 *name*을 제공해야 합니다.

바인딩 세부 정보에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md) 및 [큐 출력 구성](functions-bindings-storage-queue-output.md#configuration)을 참조하세요.

## <a name="add-code-to-use-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

이제 *function.json*에 지정된 큐 바인딩을 사용하여 `msg` 출력 매개 변수를 받고 메시지를 큐에 쓰도록 함수를 업데이트할 수 있습니다.

다음 코드와 일치하도록 *HttpExample\\\_\_init\_\_.py*를 업데이트하여 `msg` 매개 변수를 함수 정의에 추가하고 `if name:` 문 아래에 `msg.set(name)`을 추가합니다.

```python
import logging

import azure.functions as func


def main(req: func.HttpRequest, msg: func.Out[func.QueueMessage]) -> str:

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        msg.set(name)
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

`msg` 매개 변수는 [`azure.functions.InputStream class`](/python/api/azure-functions/azure.functions.httprequest)의 인스턴스입니다. `set` 메서드는 문자열 메시지를 큐에 씁니다. 이 경우 URL 쿼리 문자열의 함수에 전달된 이름입니다.

인증, 큐 참조 가져오기 또는 데이터 쓰기를 위한 코드를 작성할 필요가 *없습니다*. 이러한 모든 통합 작업은 Azure Functions 런타임 및 큐 출력 바인딩에서 편리하게 처리됩니다.

## <a name="run-and-test-the-function-locally"></a>로컬로 함수 실행 및 테스트

1. 터미널 또는 명령 프롬프트에서 *LocalFunctionProj* 함수 프로젝트 폴더로 이동합니다.

1. 다음 명령을 사용하여 Azure Functions 런타임 호스트를 시작합니다.

    ```
    func host start
    ```

1. 시작이 완료되고 `HttpExample` 엔드포인트의 URL이 표시되면 해당 URL을 브라우저에 복사하고 `?name=<your-name>` 쿼리 문자열을 추가하여 전체 URL을 만듭니다(예: `http://localhost:7071/api/HttpExample?name=Guido`). 브라우저에는 이전 문서에서와 같이 `Hello Guido`와 같은 메시지가 표시됩니다.

    `HttpExample` 엔드포인트가 표시되지 않으면 **Ctrl**+**C**를 사용하여 호스트를 중지하고 출력에서 오류가 있는지 확인합니다. 예를 들어 *function.json*에 오류가 있으면 호스트에서 엔드포인트를 활성화하지 않습니다. 또한 *HttpExample* 폴더가 아니라 함수 프로젝트 폴더에서 `func host start`를 실행하고 있는지 확인합니다.

1. 완료되면 **Ctrl**+**C**를 사용하여 호스트를 중지합니다.

> [!TIP]
> 시작하는 동안 호스트는 [Storage 바인딩 확장](functions-bindings-storage-blob.md#add-to-your-functions-app) 및 기타 Microsoft 바인딩 확장을 다운로드하여 설치합니다. 이 설치는 *host.json* 파일에서 기본적으로 다음 속성을 사용하여 바인딩 확장을 사용하도록 설정되어 있기 때문에 발생합니다.
>
> ```json
> {
>     "version": "2.0",
>     "extensionBundle": {
>         "id": "Microsoft.Azure.Functions.ExtensionBundle",
>         "version": "[1.*, 2.0.0)"
>     }
> }
> ```
>
> 바인딩 확장과 관련된 오류가 발생하면 위의 속성이 *host.json*에 있는지 확인하세요.

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage 큐의 메시지 보기

함수에서 웹 브라우저에 대한 HTTP 응답을 생성하는 경우 큐 바인딩에 지정된 대로 메시지를 `outqueue`라는 Azure Storage 큐에 쓰는 `msg.set(name)`도 호출합니다. 큐는 [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) 또는 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)에서 볼 수 있습니다. 또한 다음 단계에서 설명한 대로 Azure CLI에서 큐를 볼 수도 있습니다.

1. 함수 프로젝트의 *local.setting.json* 파일을 열고, 연결 문자열 값을 복사합니다. 터미널 또는 명령 창에서 다음 명령을 실행하여 `AZURE_STORAGE_CONNECTION_STRING`이라는 환경 변수를 만들고, `<connection_string>` 대신 특정 연결 문자열을 붙여넣습니다. (이 환경 변수는 `--connection-string` 인수를 사용하여 연결 문자열을 각 후속 명령에 제공할 필요가 없음을 의미합니다.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<connection_string>"
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    set AZURE_STORAGE_CONNECTION_STRING="<connection_string>"
    ```
    
    ---
    
1. (선택 사항) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 명령을 사용하여 계정의 Storage 큐를 봅니다. 이 명령의 출력에는 함수에서 첫 번째 메시지를 해당 큐에 쓸 때 만들어진 `outqueue`라는 이름의 큐가 포함되어야 합니다.
    
    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    az storage queue list --output tsv
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    az storage queue list --output tsv
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    az storage queue list --output tsv
    ```
    
    ---


1. [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 명령을 사용하여 이 큐의 메시지를 봅니다. 이 메시지는 이전에 함수를 테스트할 때 사용한 이름이어야 합니다. 명령은 [base64 인코딩](functions-bindings-storage-queue-trigger.md#encoding)의 큐에서 첫 번째 메시지를 검색하므로 메시지를 텍스트로 보려면 해당 메시지도 디코딩해야 합니다.

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    메시지 컬렉션을 역참조하고 base64에서 디코딩해야 하므로 PowerShell을 실행하고 PowerShell 명령을 사용합니다.

    ---
    
## <a name="redeploy-the-project-to-azure"></a>Azure에 프로젝트 다시 배포

이제 함수를 로컬로 테스트하고 해당 함수에서 메시지를 Azure Storage 큐에 작성했음을 확인했으므로 프로젝트를 다시 배포하여 Azure에서 실행되는 엔드포인트를 업데이트할 수 있습니다.

1. *LocalFunctionsProj* 폴더에서 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 명령을 사용하여 프로젝트를 다시 배포합니다. 여기서 `<app_name>`을 앱 이름으로 바꿉니다.

    ```
    func azure functionapp publish <app_name>
    ```
    
1. 이전 빠른 시작에서와 같이 브라우저 또는 CURL을 사용하여 다시 배포된 함수를 테스트합니다.

    # <a name="browser"></a>[브라우저](#tab/browser)
    
    publish 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하여 `&name=Azure` 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

    ![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](./media/functions-create-first-function-python/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    **호출 URL**을 사용하고 `&name=Azure` 매개 변수를 추가하여 [curl](https://curl.haxx.se/)을 실행합니다. 이 명령의 출력은 "Hello Azure" 텍스트입니다.
    
    ![curl을 사용한 Azure에서 실행되는 함수의 출력](./media/functions-create-first-function-python/function-test-cloud-curl.png)

    --- 

1. 이전 섹션에서 설명한 대로 Storage 큐를 다시 검사하여 큐에 쓴 새 메시지가 포함되어 있는지 확인합니다.

    Azure CLI를 사용하여 큐를 검사하는 경우 `az storage message peek` 명령은 큐의 첫 번째 메시지만 표시합니다. 메시지 처리를 시뮬레이션하려면 동일한 모든 인수가 포함된 `az storage message get`을 대신 사용합니다. `get` 명령은 메시지를 반환하고 큐에서 이를 제거합니다. 그런 다음, 큐가 비워질 때까지 동일한 명령을 반복할 수 있습니다(명령에서 오류를 제공함).

## <a name="clean-up-resources"></a>리소스 정리

다음 [Application Insights 통합 사용](functions-monitoring.md#manually-connect-an-app-insights-resource) 단계로 계속 진행하는 경우 이미 수행한 작업을 기반으로 하여 빌드되는 모든 리소스를 그대로 유지합니다.

그렇지 않으면 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions 앱과 Application Insights 통합 사용](functions-monitoring.md#manually-connect-an-app-insights-resource)

기타 리소스:

- [Python의 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=python)
- [Azure Functions Python 개발자 가이드](functions-reference-python.md)
- [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)
- [Functions 가격 페이지](https://azure.microsoft.com/pricing/details/functions/)
- [소비 계획 비용 예측](functions-consumption-costs.md) 문서
