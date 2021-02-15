---
title: Azure Functions 개요에 대 한 Azure Queue storage 트리거 및 바인딩
description: Azure Queue Storage 트리거를 사용하고 Azure Functions에서 바인딩을 출력하는 방법을 이해합니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/18/2020
ms.author: cshoe
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: a1b9d03da29b7c89055303fa97fc38c2ef734b23
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381480"
---
# <a name="azure-queue-storage-trigger-and-bindings-for-azure-functions-overview"></a>Azure Functions 개요에 대 한 Azure Queue storage 트리거 및 바인딩

새 Azure 큐 저장소 메시지가 만들어지고 함수 내에서 큐 메시지를 작성할 수 Azure Functions.

| 작업 | 형식 |
|---------|---------|
| 큐 저장소 데이터 변경으로 함수 실행 | [트리거](./functions-bindings-storage-queue-trigger.md) |
| 큐 저장소 메시지 쓰기 |[출력 바인딩](./functions-bindings-storage-queue-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 3(sp3) | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

#### <a name="storage-extension-5x-and-higher"></a>저장소 확장 5.x 이상

새 버전의 저장소 바인딩 확장은 [Preview NuGet 패키지로](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)사용할 수 있습니다. 이 미리 보기에서는 [비밀 대신 id를 사용 하 여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. .NET 응용 프로그램의 경우에는에 바인딩할 수 있는 형식도 변경 하 여 `WindowsAzure.Storage` `Microsoft.Azure.Storage` [Azure. Storage. queue](/dotnet/api/azure.storage.queues)에서 최신 형식으로 형식을 바꿉니다.

> [!NOTE]
> 미리 보기 패키지는 확장 번들에 포함 되지 않으며 수동으로 설치 해야 합니다. .NET 앱의 경우 패키지에 대 한 참조를 추가 합니다. 다른 모든 앱 유형에 대해서는 [확장 업데이트]를 참조 하세요.

[core tools]: ./functions-run-local.md
[확장 번들]: ./functions-bindings-register.md#extension-bundles
[NuGet 패키지]: https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage
[확장 업데이트]: ./functions-bindings-register.md
[Azure Tools extension]: https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack

### <a name="functions-1x"></a>Functions 1.x

함수 1.x 앱은 [WebJobs](https://www.nuget.org/packages/Microsoft.Azure.WebJobs) NuGet 패키지 버전 2.x를 자동으로 참조 합니다.

[!INCLUDE [functions-storage-sdk-version](../../includes/functions-storage-sdk-version.md)]

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 설정

이 섹션에서는 버전 2.x 이상에서이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래 파일 *에host.js* 예제에는이 바인딩에 대 한 버전 2.x + 설정만 포함 되어 있습니다. 2.x 이상 버전의 전역 구성 설정에 대 한 자세한 내용은 [ Azure Functions에 대 한 참조host.js](functions-host-json.md)를 참조 하세요.

> [!NOTE]
> Functions 1.x에서 host.json의 참조는 [Azure Functions 1.x에 대한 host.json 참조](functions-host-json-v1.md)를 참조하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "queues": {
            "maxPollingInterval": "00:00:02",
            "visibilityTimeout" : "00:00:30",
            "batchSize": 16,
            "maxDequeueCount": 5,
            "newBatchThreshold": 8,
            "messageEncoding": "base64"
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------|
|maxPollingInterval|00:00:01|큐 폴링 사이의 최대 간격입니다. 최소는 00:00:00:00.100 (100 밀리초)이 고 최대 00:01:00 (1 분) 씩 증가 합니다.  함수 2.x 이상에서는 데이터 형식이이 고 `TimeSpan` 버전 1.x에서는 밀리초 단위입니다.|
|visibilityTimeout|00:00:00|메시지 처리가 실패하는 경우 재시도 사이의 간격입니다. |
|batchSize|16|함수 런타임이 동시에 검색하고 병렬로 처리하는 큐 메시지 수입니다. 처리되는 개수가 `newBatchThreshold`로 감소하면 런타임은 다른 일괄 처리를 가져와 해당 메시지의 처리를 시작합니다. 따라서 함수당 처리되는 최대 동시 메시지 수는 `batchSize` + `newBatchThreshold`입니다. 이 제한은 큐 트리거 함수에 개별적으로 적용됩니다. <br><br>하나의 큐에 수신된 메시지에 대해 병렬 실행을 방지하려면 `batchSize`을 1로 설정합니다. 그러나이 설정은 함수 앱이 단일 VM (가상 컴퓨터) 에서만 실행 되는 한 동시성을 제거 합니다. 함수 앱이 여러 VM에 확장되면 각 VM은 각 큐 트리거 함수의 인스턴스 하나를 실행할 수 있습니다.<br><br>최대 `batchSize`은 32입니다. |
|maxDequeueCount|5|포이즌 큐로 이동하기 전에 메시지 처리를 시도할 횟수입니다.|
|newBatchThreshold|batchSize/2|동시에 처리되는 메시지의 수가 이 숫자로 내려갈 때마다 런타임은 다른 일괄 처리를 검색합니다.|
|messageEncoding|base64| 이 설정은 [확장 버전 5.0.0 이상](#storage-extension-5x-and-higher)에서만 사용할 수 있습니다. 메시지의 인코딩 형식을 나타냅니다. 유효한 값은 `base64` 및 `none`입니다.|

## <a name="next-steps"></a>다음 단계

- [큐 저장소 데이터 변경 내용으로 함수 실행 (트리거)](./functions-bindings-storage-queue-trigger.md)
- [큐 저장소 메시지 쓰기 (출력 바인딩)](./functions-bindings-storage-queue-output.md)
