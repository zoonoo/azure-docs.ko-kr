---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493951"
---
Visual Studio Code를 사용하면 편리한 프롬프트 세트를 따라 function.json 파일에 바인딩을 추가할 수 있습니다. 

바인딩을 추가하려면 명령 팔레트(F1)를 열고, **Azure Functions: 바인딩 추가...** 를 입력하고, 새 바인딩에 대한 함수를 선택한 다음, 함수에 추가되는 바인딩 유형에 따라 달라지는 프롬프트를 따릅니다. 

다음은 새 스토리지 출력 바인딩을 정의하는 예제 프롬프트입니다.

| prompt | 값 | 설명 |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **방향으로 바인딩 선택** | `Azure Queue Storage` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **코드에서 이 바인딩을 식별하는 데 사용하는 이름** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **메시지가 전송될 큐** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName* 이 없으면 바인딩이 알아서 만듭니다. |
| **"local.settings.json"에서 설정 선택** | `MyStorageConnection` | 스토리지 계정의 연결 문자열이 포함된 애플리케이션 설정의 이름입니다. `AzureWebJobsStorage` 설정에는 함수 앱을 사용하여 만든 스토리지 계정의 연결 문자열이 포함됩니다. |

함수 폴더의 **function.json** 파일에서 직접 마우스 오른쪽 단추를 클릭(macOS에서는 Ctrl + 클릭)하고, **바인딩 추가** 를 선택하고, 동일한 프롬프트를 수행할 수도 있습니다.

이 예제에서는 function.json 파일에 있는 `bindings` 배열에 다음 바인딩이 추가됩니다.

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```