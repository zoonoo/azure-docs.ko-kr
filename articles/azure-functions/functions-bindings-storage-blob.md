---
title: Azure Functions에 대 한 Azure Blob storage 트리거 및 바인딩
description: Azure Functions에서 Azure Blob storage 트리거 및 바인딩을 사용 하는 방법을 알아봅니다.
author: craigshoemaker
ms.topic: reference
ms.date: 02/13/2020
ms.author: cshoe
ms.openlocfilehash: 4ec21086ee94610be1d9cf5da7b64c837b5311a9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100381531"
---
# <a name="azure-blob-storage-bindings-for-azure-functions-overview"></a>Azure Functions 개요에 대 한 Azure Blob storage 바인딩

Azure Functions는 [트리거와 바인딩을](./functions-triggers-bindings.md)통해 [Azure Storage](../storage/index.yml) 와 통합 됩니다. Blob storage와 통합 하면 blob 데이터의 변경 내용 및 읽기/쓰기 값에 반응 하는 함수를 작성할 수 있습니다.

| 작업 | Type |
|---------|---------|
| Blob storage 데이터 변경으로 함수 실행 | [트리거](./functions-bindings-storage-blob-trigger.md) |
| 함수에서 blob 저장소 데이터 읽기 | [입력 바인딩](./functions-bindings-storage-blob-input.md) |
| 함수가 blob 저장소 데이터를 쓰도록 허용 |[출력 바인딩](./functions-bindings-storage-blob-output.md) |

## <a name="add-to-your-functions-app"></a>함수 앱에 추가

### <a name="functions-2x-and-higher"></a>Functions 2.x 이상

트리거와 바인딩을 사용 하려면 적절 한 패키지를 참조 해야 합니다. NuGet 패키지는 .NET 클래스 라이브러리에 사용 되는 반면 확장 번들은 다른 모든 응용 프로그램 형식에 사용 됩니다.

| 언어                                        | 추가 방법 ...                                   | 설명 
|-------------------------------------------------|---------------------------------------------|-------------|
| C#                                              | [NuGet 패키지]설치, 버전 3(sp3) | |
| C # 스크립트, Java, JavaScript, Python, PowerShell | [확장 번들] 등록          | [Azure Tools 확장](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) 은 Visual Studio Code와 함께 사용 하는 것이 좋습니다. |
| C # 스크립트 (온라인 전용 Azure Portal)         | 바인딩 추가                            | 함수 앱을 다시 게시 하지 않고 기존 바인딩 확장을 업데이트 하려면 [확장 업데이트]를 참조 하세요. |

#### <a name="storage-extension-5x-and-higher"></a>저장소 확장 5.x 이상

새 버전의 저장소 바인딩 확장은 [Preview NuGet 패키지로](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage/5.0.0-beta.2)사용할 수 있습니다. 이 미리 보기에서는 [비밀 대신 id를 사용 하 여 연결](./functions-reference.md#configure-an-identity-based-connection)하는 기능이 도입 되었습니다. .NET 응용 프로그램의 경우에는에 바인딩할 수 있는 형식도 변경 하 여 `WindowsAzure.Storage` `Microsoft.Azure.Storage` [Azure. Storage. blob](/dotnet/api/azure.storage.blobs)의 형식을 최신 형식으로 바꿉니다.

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

## <a name="hostjson-settings"></a>host.json 설정

> [!NOTE]
> 5.0.0 이전 버전의 확장 버전을 사용 하는 경우에는이 섹션이 적용 되지 않습니다. 이러한 버전의 경우 blob에 대 한 전역 구성 설정이 없습니다.

이 섹션에서는 [확장 버전 5.0.0 이상을](#storage-extension-5x-and-higher)사용 하는 경우이 바인딩에 사용할 수 있는 전역 구성 설정에 대해 설명 합니다. 아래 파일 *에host.js* 예제에는이 바인딩에 대 한 버전 2.x + 설정만 포함 되어 있습니다. 기능 버전 2.x 이상에서 전역 구성 설정에 대 한 자세한 내용은 [ Azure Functions에 대 한 참조host.js](functions-host-json.md)를 참조 하세요.

```json
{
    "version": "2.0",
    "extensions": {
        "blobs": {
            "maxDegreeOfParallelism": "4"
        }
    }
}
```

|속성  |기본값 | 설명 |
|---------|---------|---------|
|maxDegreeOfParallelism|8 * (사용 가능한 코어 수)|각 blob 트리거 함수에 대해 허용 되는 동시 호출의 정수 수입니다. 허용 되는 최소값은 1입니다.|

## <a name="next-steps"></a>다음 단계

- [Blob storage 데이터가 변경 될 때 함수 실행](./functions-bindings-storage-blob-trigger.md)
- [함수가 실행 될 때 blob 저장소 데이터 읽기](./functions-bindings-storage-blob-input.md)
- [함수에서 blob 저장소 데이터 쓰기](./functions-bindings-storage-blob-output.md)
