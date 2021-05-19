---
title: Azure Functions Event Grid Blob 트리거
description: Event Grid Blob 트리거를 사용하여 설정하고 디버그하는 방법을 알아봅니다.
author: cachai2
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: cachai
ms.openlocfilehash: 259928cbfc046930d82bc3462db6d8aad4cd6b06
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108124998"
---
# <a name="azure-function-event-grid-blob-trigger"></a>Azure Functions Event Grid Blob 트리거

이 문서에서는 스토리지 계정에서 발생한 이벤트를 처리하는 로컬 Event Grid Blob 트리거된 함수를 디버그 및 배포하는 방법을 보여 줍니다.

> [!NOTE]
> Event Grid Blob 트리거는 미리 보기 상태입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 기존 함수 앱 만들기 또는 사용
- 기존 스토리지 계정 만들기 또는 사용
- [Microsoft.Azure.WebJobs.Extensions.Storage 확장](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2) 버전 5.0+ 설치
- [ngrok](https://ngrok.com/)를 다운로드하여 Azure에서 로컬 함수를 호출하도록 허용

## <a name="create-a-new-function"></a>새 함수 만들기

1. Visual Studio Code에서 함수 앱을 엽니다.

1. **F1** 키를 눌러 새 Blob 트리거 함수를 만듭니다. 스토리지 계정에 대한 연결 문자열을 사용해야 합니다.

1. Event Grid Blob 트리거의 기본 url은 다음과 같습니다.

    # <a name="c"></a>[C#](#tab/csharp)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName={functionname}
    ```

    # <a name="python"></a>[Python](#tab/python)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    # <a name="java"></a>[Java](#tab/java)

    ```http
    http://localhost:7071/runtime/webhooks/blobs?functionName=Host.Functions.{functionname}
    ```

    ---

    함수 앱의 이름을 확인하고 트리거 형식이 url에서 `blobs`로 표시되는 Blob 트리거인지 확인합니다. 이 작업은 나중에 방법 가이드에서 엔드포인트를 설정할 때 필요합니다.

1. 함수를 만든 후 Event Grid 원본 매개 변수를 추가합니다.

    # <a name="c"></a>[C#](#tab/csharp)
    함수 매개 변수에 **Source = BlobTriggerSource.EventGrid** 를 추가합니다.
    
    ```csharp
    [FunctionName("BlobTriggerCSharp")]
    public static void Run([BlobTrigger("samples-workitems/{name}", Source = BlobTriggerSource.EventGrid, Connection = "connection")]Stream myBlob, string name, ILogger log)
    {
        log.LogInformation($"C# Blob trigger function Processed blob\n Name:{name} \n Size: {myBlob.Length} Bytes");
    }
    ```

    # <a name="python"></a>[Python](#tab/python)
    function.json 바인딩 데이터에 **"source": "EventGrid"** 를 추가합니다.
    
    ```json
    {
      "scriptFile": "__init__.py",
      "bindings": [
        {
          "name": "myblob",
          "type": "blobTrigger",
          "direction": "in",
          "path": "samples-workitems/{name}",
          "source": "EventGrid",
          "connection": "MyStorageAccountConnectionString"
        }
      ]
    }
    ```

    # <a name="java"></a>[Java](#tab/java)
    **F5** 키를 눌러 함수를 빌드합니다. 빌드가 완료되면 **function.json** 바인딩 데이터에 **"source": "EventGrid"** 를 추가합니다.
    
    ```json
    {
      "scriptFile" : "../java-1.0-SNAPSHOT.jar",
      "entryPoint" : "com.function.{MyFunctionName}.run",
      "bindings" : [ {
        "type" : "blobTrigger",
        "direction" : "in",
        "name" : "content",
        "path" : "samples-workitems/{name}",
        "dataType" : "binary",
        "source": "EventGrid",
        "connection" : "MyStorageAccountConnectionString"
       } ]
    }
    ```

    ---

1. 로깅을 처리하는 줄에서 함수의 중단점을 설정합니다.

1. 디버깅 세션을 시작합니다.

    # <a name="c"></a>[C#](#tab/csharp)
    **F5 키를 눌러** 디버깅 세션을 시작합니다.

    # <a name="python"></a>[Python](#tab/python)
    **F5 키를 눌러** 디버깅 세션을 시작합니다.

    # <a name="java"></a>[Java](#tab/java)
    새 터미널을 열고 아래 mvn 명령을 실행하여 디버깅 세션을 시작합니다.

    ```bash
    mvn azure-functions:run
    ```

    ---

[!INCLUDE [functions-event-grid-local-dev](../../includes/functions-event-grid-local-dev.md)]

## <a name="debug-the-function"></a>함수 디버그
Blob 트리거가 스토리지 컨테이너에 새 파일이 업로드된 것을 인식하면 로컬 함수에서 중단점이 적중됩니다.

## <a name="deployment"></a>배포

Azure에 함수 앱을 배포할 때 로컬 엔드포인트의 웹후크 엔드포인트를 배포된 앱 엔드포인트로 업데이트합니다. 엔드포인트를 업데이트하려면 [스토리지 이벤트 추가](#add-a-storage-event)의 단계를 수행하고 5단계의 웹후크 URL에 대해 아래를 사용합니다. `<BLOB-EXTENSION-KEY>`는 **함수 앱** 의 왼쪽 메뉴에 있는 **앱 키** 섹션에서 찾을 수 있습니다.

# <a name="c"></a>[C#](#tab/csharp)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="python"></a>[Python](#tab/python)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

# <a name="java"></a>[Java](#tab/java)

```http
https://<FUNCTION-APP-NAME>.azurewebsites.net/runtime/webhooks/blobs?functionName=Host.Functions.<FUNCTION-NAME>&code=<BLOB-EXTENSION-KEY>
```

---

## <a name="clean-up-resources"></a>리소스 정리

이 문서에서 만든 리소스를 정리하려면 이 자습서에서 만든 Event Grid 구독을 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Event Grid를 사용하여 업로드된 이미지 크기 자동 조정](../event-grid/resize-images-on-storage-blob-upload-event.md)
- [Azure Functions의 Event Grid 트리거](./functions-bindings-event-grid.md)