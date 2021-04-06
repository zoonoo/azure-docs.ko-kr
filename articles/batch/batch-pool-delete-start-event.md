---
title: Azure Batch 풀 삭제 시작 이벤트
description: Batch 풀 삭제 시작 이벤트에 대한 참조입니다. 이 이벤트는 풀 삭제 작업이 시작되면 내보내집니다.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: 86f6eb8e7b269cb45f692398e9e60390375ca073
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803751"
---
# <a name="pool-delete-start-event"></a>풀 삭제 시작 이벤트

 이 이벤트는 풀 삭제 작업이 시작되면 내보내집니다. 풀 삭제는 비동기 이벤트이므로 풀 삭제 완료 이벤트는 삭제 작업이 완료되면 내보진다고 예상할 수 있습니다.

 다음 예에서는 풀 삭제 시작 이벤트의 본문을 보여 줍니다.

```
{
   "id": "myPool1"
}
```

|요소|Type|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
