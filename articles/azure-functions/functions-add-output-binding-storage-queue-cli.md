---
title: 명령줄 도구를 사용하여 Azure Functions를 Azure Storage에 연결
description: 명령줄 프로젝트에 출력 바인딩을 추가하여 Azure Functions를 Azure Storage 큐에 연결하는 방법을 알아봅니다.
ms.date: 02/07/2020
ms.topic: quickstart
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: 9181caf516d5c2003cfe99b125d2921732cbbb9d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "79473390"
---
# <a name="connect-azure-functions-to-azure-storage-using-command-line-tools"></a>명령줄 도구를 사용하여 Azure Functions를 Azure Storage에 연결

이 문서에서는 [이전 빠른 시작](functions-create-first-azure-function-azure-cli.md)에서 만든 함수 및 스토리지 계정과 Azure Storage 큐를 통합합니다. 이 통합은 데이터를 HTTP 요청에서 큐의 메시지에 쓰는 *출력 바인딩*을 사용하여 달성할 수 있습니다. 이 문서를 완료하면 이전의 빠른 시작에서 소요된 몇 USD 센트를 초과하는 추가 비용이 발생하지 않습니다. 바인딩에 대해 자세히 알아보려면 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

## <a name="configure-your-local-environment"></a>로컬 환경 구성

시작하기 전에, [빠른 시작: 명령줄에서 Azure Functions 프로젝트 만들기](functions-create-first-azure-function-azure-cli.md) 문서를 완료해야 합니다. 해당 문서의 끝에서 리소스를 이미 정리한 경우, 해당 단계를 다시 수행하여 Azure에서 함수 앱 및 관련 리소스를 다시 만듭니다.

## <a name="retrieve-the-azure-storage-connection-string"></a>Azure Storage 연결 문자열 검색

이전 빠른 시작의 Azure에서 함수 앱을 만들 때 Storage 계정도 만들었습니다. 이 계정의 연결 문자열은 Azure의 앱 설정에 안전하게 저장됩니다. 이 설정을 *local.settings.json* 파일에 다운로드하면 함수를 로컬로 실행할 때 동일한 계정의 Storage 큐에 해당 연결 쓰기를 사용할 수 있습니다. 

1. 프로젝트의 루트에서 다음 명령을 실행합니다. 여기서 `<APP_NAME>`을 이전 빠른 시작의 함수 앱 이름으로 바꿉니다. 이 명령은 파일의 기존 값을 덮어씁니다.

    ```
    func azure functionapp fetch-app-settings <APP_NAME>
    ```
    
1. *local.settings.json*을 열고, Storage 계정 연결 문자열인 `AzureWebJobsStorage`라는 값을 찾습니다. 이 문서의 다른 섹션에서 `AzureWebJobsStorage` 이름과 연결 문자열을 사용합니다.

> [!IMPORTANT]
> *local.settings.json*에는 Azure에서 다운로드한 비밀이 포함되어 있으므로 항상 이 파일을 원본 제어에서 제외하세요. 로컬 함수 프로젝트를 사용하여 만든 *.gitignore* 파일은 기본적으로 해당 파일이 제외됩니다.

[!INCLUDE [functions-register-storage-binding-extension-csharp](../../includes/functions-register-storage-binding-extension-csharp.md)]

## <a name="add-an-output-binding-definition-to-the-function"></a>함수에 출력 바인딩 정의 추가

함수에는 하나의 트리거만 있을 수 있지만 여러 개의 입력 및 출력 바인딩이 있을 수 있으므로 사용자 지정 통합 코드를 작성하지 않고도 다른 Azure 서비스 및 리소스에 연결할 수 있습니다. 

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
이러한 바인딩은 함수 폴더의 *function.json* 파일에서 선언합니다. 이전 빠른 시작에서 *HttpExample* 폴더의 *function.json* 파일에 있는 `bindings` 컬렉션에는 두 개의 바인딩이 포함되어 있습니다.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-python"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json" range="2-18":::  
::: zone-end

::: zone pivot="programming-language-powershell"  
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json" range="2-18":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript, programming-language-powershell, programming-language-typescript"  
각 바인딩에는 적어도 하나의 형식, 방향 및 이름이 있습니다. 위의 예제에서 첫 번째 바인딩은 `in` 방향의 `httpTrigger` 형식입니다. `in` 방향의 경우 `name`은 트리거에서 호출할 때 함수로 보내는 입력 매개 변수의 이름을 지정합니다.  
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"  
컬렉션의 두 번째 바인딩에는 `res`라는 이름이 지정됩니다. 이 `http` 바인딩은 HTTP 응답을 작성하는 데 사용되는 출력 바인딩(`out`)입니다. 

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python"  
컬렉션의 두 번째 바인딩은 `out` 방향의 `http` 형식입니다. 이 경우 `$return`의 특수 `name`은 이 바인딩에서 입력 매개 변수를 제공하지 않고 함수의 반환 값을 사용함을 나타냅니다.

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-python/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-powershell"  
컬렉션의 두 번째 바인딩에는 `res`라는 이름이 지정됩니다. 이 `http` 바인딩은 HTTP 응답을 작성하는 데 사용되는 출력 바인딩(`out`)입니다. 

이 함수에서 Azure Storage 큐에 쓰려면 아래 코드와 같이 이름이 `msg`인 `queue` 형식의 `out` 바인딩을 추가합니다.

:::code language="json" source="~/functions-docs-powershell/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="3-26":::
::: zone-end  

::: zone pivot="programming-language-python,programming-language-javascript,programming-language-powershell,programming-language-typescript"  
이 경우 `msg`가 출력 인수로 함수에 제공됩니다. `queue` 형식의 경우 `queueName`에는 큐 이름을 지정하고, `connection`에는 *local.settings.json*에 있는 Azure Storage 연결의 *name*을 제공해야 합니다. 
::: zone-end  

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]  
::: zone-end  

바인딩 세부 정보에 대한 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md) 및 [큐 출력 구성](functions-bindings-storage-queue-output.md#configuration)을 참조하세요.

## <a name="add-code-to-use-the-output-binding"></a>출력 바인딩을 사용하는 코드 추가

이제 *function.json*에 지정된 큐 바인딩을 사용하여 `msg` 출력 매개 변수를 받고 메시지를 큐에 쓰도록 함수를 업데이트할 수 있습니다.

::: zone pivot="programming-language-python"     
[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]
::: zone-end  

::: zone pivot="programming-language-javascript"  
[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]
::: zone-end  

::: zone pivot="programming-language-typescript"  
[!INCLUDE [functions-add-output-binding-ts](../../includes/functions-add-output-binding-ts.md)]
::: zone-end  

::: zone pivot="programming-language-powershell"  
[!INCLUDE [functions-add-output-binding-powershell](../../includes/functions-add-output-binding-powershell.md)]  
::: zone-end

::: zone pivot="programming-language-csharp"  
[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]
::: zone-end 

인증, 큐 참조 가져오기 또는 데이터 쓰기를 위한 코드를 작성할 필요가 *없습니다*. 이러한 모든 통합 작업은 Azure Functions 런타임 및 큐 출력 바인딩에서 편리하게 처리됩니다.

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-extension-bundles-info](../../includes/functions-extension-bundles-info.md)]

## <a name="view-the-message-in-the-azure-storage-queue"></a>Azure Storage 큐의 메시지 보기

큐는 [Azure Portal](../storage/queues/storage-quickstart-queues-portal.md) 또는 [Microsoft Azure Storage Explorer](https://storageexplorer.com/)에서 볼 수 있습니다. 또한 다음 단계에 설명된 대로 Azure CLI에서 큐를 볼 수도 있습니다.

1. 함수 프로젝트의 *local.setting.json* 파일을 열고, 연결 문자열 값을 복사합니다. 터미널 또는 명령 창에서 다음 명령을 실행하여 `AZURE_STORAGE_CONNECTION_STRING`이라는 환경 변수를 만들고, `<MY_CONNECTION_STRING>` 대신 특정 연결 문자열을 붙여넣습니다. (이 환경 변수는 `--connection-string` 인수를 사용하여 연결 문자열을 각 후속 명령에 제공할 필요가 없음을 의미합니다.)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (선택 사항) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 명령을 사용하여 계정의 Storage 큐를 봅니다. 이 명령의 출력에는 함수에서 첫 번째 메시지를 해당 큐에 쓸 때 만들어진 `outqueue`라는 이름의 큐가 포함되어야 합니다.
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) 명령을 사용하여 이 큐의 메시지를 읽습니다. 이 메시지는 이전에 함수를 테스트할 때 사용한 이름이어야 합니다. 명령은 큐에서 첫 번째 메시지를 읽고 제거합니다. 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    이 스크립트는 certutil을 사용하여 로컬 임시 파일에서 base64로 인코딩된 메시지 컬렉션을 디코딩합니다. 출력이 없는 경우 오류가 발생하면 `> NUL`을 스크립트에서 제거하여 certutil 출력 억제를 중지해봅니다. 
    
    ---
    
    메시지 본문은 [Base64로 인코딩](functions-bindings-storage-queue-trigger.md#encoding)되어 저장되기 때문에 메시지를 표시하기 전에 먼저 디코딩해야 합니다. `az storage message get`을 실행하면 메시지가 큐에서 제거됩니다. `outqueue`에 메시지가 하나만 있는 경우 이 명령을 두 번째로 실행하면 메시지가 검색되는 대신 오류가 발생합니다.

## <a name="redeploy-the-project-to-azure"></a>Azure에 프로젝트 다시 배포

함수가 Azure Storage 큐에 메시지를 쓴 것을 로컬에서 확인했으면, 프로젝트를 다시 배포하여 Azure에서 실행되는 엔드포인트를 업데이트할 수 있습니다.

1. *LocalFunctionsProj* 폴더에서 [`func azure functionapp publish`](functions-run-local.md#project-file-deployment) 명령을 사용하여 프로젝트를 다시 배포합니다. 여기서 `<APP_NAME>`을 앱 이름으로 바꿉니다.

    ```
    func azure functionapp publish <APP_NAME>
    ```
    
1. 이전 빠른 시작에서와 같이 브라우저 또는 CURL을 사용하여 다시 배포된 함수를 테스트합니다.

    # <a name="browser"></a>[브라우저](#tab/browser)
    
    publish 명령의 출력에 표시된 **호출 URL** 전체를 브라우저 주소 표시줄에 복사하여 `&name=Functions` 쿼리 매개 변수를 추가합니다. 브라우저에서 함수를 로컬로 실행했을 때와 비슷한 출력이 표시됩니다.

    ![브라우저에서 보여 주는 Azure에서 실행되는 함수의 출력](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-browser.png)

    # <a name="curl"></a>[curl](#tab/curl)
    
    **호출 URL**을 사용하고 `&name=Functions` 매개 변수를 추가하여 [`curl`](https://curl.haxx.se/)을 실행합니다. 명령의 출력은 "Hello Functions" 텍스트여야 합니다.
    
    ![CURL을 사용한 Azure에서 실행되는 함수의 출력](./media/functions-add-output-binding-storage-queue-cli/function-test-cloud-curl.png)

    --- 

1. 이전 섹션에서 설명한 대로 Storage 큐를 다시 검사하여 큐에 쓴 새 메시지가 포함되어 있는지 확인합니다.

## <a name="clean-up-resources"></a>리소스 정리

완료한 후 추가 비용이 발생하지 않도록 다음 명령을 사용하여 리소스 그룹 및 포함된 모든 리소스를 삭제합니다.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

## <a name="next-steps"></a>다음 단계

Storage 큐에 데이터를 쓰도록 HTTP 트리거 함수를 업데이트했습니다. 이제 Core Tools 및 Azure CLI를 사용하여 명령줄에서 함수를 개발하는 방법에 대해 자세히 알아볼 수 있습니다.

+ [Azure Functions Core Tools 작업](functions-run-local.md)  

::: zone pivot="programming-language-csharp"  
+ [C# 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=csharp)

+ [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [JavaScript 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=javascript)

+ [Azure Functions JavaScript 개발자 가이드](functions-reference-node.md)  
::: zone-end  
::: zone pivot="programming-language-typescript"  
+ [TypeScript 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=typescript)

+ [Azure Functions TypeScript 개발자 가이드](functions-reference-node.md#typescript)  
::: zone-end  
::: zone pivot="programming-language-python"  
+ [Python의 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=python)

+ [Azure Functions Python 개발자 가이드](functions-reference-python.md)  
::: zone-end  
::: zone pivot="programming-language-powershell"  
+ [PowerShell 전체 함수 프로젝트에 대한 예제](/samples/browse/?products=azure-functions&languages=azurepowershell)

+ [Azure Functions PowerShell 개발자 가이드](functions-reference-powershell.md) 
::: zone-end
+ [Azure Functions 트리거 및 바인딩](functions-triggers-bindings.md)

+ [Functions 가격 페이지](https://azure.microsoft.com/pricing/details/functions/)

+ [소비 계획 비용 예측](functions-consumption-costs.md) 
