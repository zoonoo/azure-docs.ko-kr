---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 4fe159660421113e0f0ac0586ae7e4a22d5bcda7
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2020
ms.locfileid: "77474125"
---
### <a name="query-the-storage-queue"></a>Storage 큐 쿼리

다음 예제처럼 [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) 명령을 사용하여 계정의 Storage 큐를 볼 수 있습니다.

```azurecli-interactive
az storage queue list --output tsv
```

이 명령의 출력에는 함수가 실행될 때 생성된 `outqueue`라는 큐가 포함됩니다.

다음으로, 다음 예제와 같이 [`az storage message peek`](/cli/azure/storage/message#az-storage-message-peek) 명령을 사용하여 이 큐의 메시지를 확인합니다.

```azurecli-interactive
echo `echo $(az storage message peek --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
```

반환된 문자열은 함수를 테스트하기 위해 보낸 메시지와 동일해야 합니다.

> [!NOTE]  
> 이전 예제는 base64에서 반환된 문자열을 디코딩합니다. Queue 스토리지 바인딩이 Azure Storage에 [base64 문자열](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)로 쓰고 읽기 때문입니다.