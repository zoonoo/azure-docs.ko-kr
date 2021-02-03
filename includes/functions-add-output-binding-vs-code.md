---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 01/30/2020
ms.author: glenga
ms.openlocfilehash: 3759dce2ab527cab5b2d2afe8eae30461cbc9031
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/03/2021
ms.locfileid: "99493951"
---
Visual Studio Code를 사용 하면 편리한 프롬프트를 따라 파일의 function.js에 바인딩을 추가할 수 있습니다. 

바인딩을 추가 하려면 팔레트 (F1) 명령을 열고 **Azure Functions: 바인딩 추가**...를 입력 한 다음 새 바인딩에 대 한 함수를 선택 하 고 프롬프트를 따릅니다 .이는 함수에 추가 되는 바인딩 유형에 따라 달라 집니다. 

다음은 새 저장소 출력 바인딩을 정의 하는 예제 프롬프트입니다.

| 프롬프트 | 값 | Description |
| -------- | ----- | ----------- |
| **바인딩 방향 선택** | `out` | 바인딩은 출력 바인딩입니다. |
| **방향이 있는 바인딩 선택** | `Azure Queue Storage` | 바인딩은 Azure Storage 큐 바인딩입니다. |
| **코드에서이 바인딩을 식별 하는 데 사용 되는 이름입니다.** | `msg` | 코드에서 참조되는 바인딩 매개 변수를 식별하는 이름입니다. |
| **메시지가 전송될 큐** | `outqueue` | 바인딩이 데이터를 쓰는 큐의 이름입니다. 바인딩을 처음 사용할 때 *queueName* 이 없으면 바인딩이 알아서 만듭니다. |
| **"local.settings.js에서 설정"을 선택 합니다.** | `MyStorageConnection` | 저장소 계정에 대 한 연결 문자열을 포함 하는 응용 프로그램 설정의 이름입니다. 이 `AzureWebJobsStorage` 설정에는 함수 앱을 사용 하 여 만든 저장소 계정에 대 한 연결 문자열이 포함 됩니다. |

함수 폴더의 파일 **function.js** 에서 직접 (Macos에서 Ctrl + 클릭)을 마우스 오른쪽 단추로 클릭 하 고, **바인딩 추가** 를 선택 하 고, 동일한 프롬프트를 수행할 수도 있습니다.

이 예제에서는 `bindings` 파일의 function.js에 있는 배열에 다음 바인딩이 추가 됩니다.

```json
{
    "type": "queue",
    "direction": "out",
    "name": "msg",
    "queueName": "outqueue",
    "connection": "MyStorageConnection"
}
```