---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 10/18/2020
ms.author: glenga
ms.openlocfilehash: 9213bdd8e63fc1e8ab7bdba8ac7a569be0dfe6be
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424852"
---
다음 명령을 실행하여 거의 실시간으로 [스트리밍 로그](../articles/azure-functions/functions-run-local.md#enable-streaming-logs)를 확인합니다.

```console
func azure functionapp logstream <APP_NAME> 
```

별도의 터미널 창 또는 브라우저에서 원격 함수를 다시 호출합니다. Azure의 함수 실행에 대한 자세한 로그가 터미널에 표시됩니다. 