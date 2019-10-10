---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838929"
---
바인딩 특성은 function.json 파일에서 직접 정의됩니다. 바인딩 형식에 따라 추가 속성이 필요할 수 있습니다. [큐 출력 구성](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration)은 Azure Storage 큐 바인딩에 필요한 필드를 설명합니다. 확장을 통해 바인딩을 function.json 파일에 쉽게 추가할 수 있습니다. 

바인딩을 만들려면 HttpTrigger 폴더의 `function.json` 파일을 마우스 오른쪽 단추로 클릭하고(macOS에서 Ctrl+ 클릭) **바인딩 추가...** 를 선택합니다. 프롬프트에 따라 새 바인딩에 대해 다음 바인딩 속성을 정의합니다.

| prompt | 값 | 설명 |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **...방향으로 바인딩 선택** | `Azure Queue Storage` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **코드에서 이 바인딩을 식별하는 데 사용하는 이름** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **메시지가 전송될 큐** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName*이 없으면 바인딩이 알아서 만듭니다. |
| **"local.setting.json"에서 설정 선택** | `AzureWebJobsStorage` | 스토리지 계정의 연결 문자열이 포함된 애플리케이션 설정의 이름입니다. `AzureWebJobsStorage` 설정은 함수 앱을 사용하여 만든 Storage 계정의 연결 문자열을 포함합니다. |

바인딩은 function.json 파일의 `bindings` 배열에 추가되며, 이제 다음 예제처럼 표시됩니다.

```json
{
   ...

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