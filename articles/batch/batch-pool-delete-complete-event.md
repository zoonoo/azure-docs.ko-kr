---
title: Azure Batch 풀 삭제 완료 이벤트
description: Batch 풀 삭제 완료 이벤트에 대한 참조입니다. 이 이벤트는 풀 삭제 작업이 완료되면 내보내집니다.
ms.topic: reference
ms.date: 12/28/2020
ms.openlocfilehash: be6411a150ae6be424c0621eed768157154c7408
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "97803734"
---
# <a name="pool-delete-complete-event"></a>풀 삭제 완료 이벤트

 이 이벤트는 풀 삭제 작업이 완료되면 내보내집니다.

 다음 예에서는 풀 삭제 완료 이벤트의 본문을 보여 줍니다.

```
{
   "id": "myPool1",
   "startTime": "2016-09-09T22:13:48.579Z",
   "endTime": "2016-09-09T22:14:08.836Z"
}
```

|요소|Type|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
|`startTime`|DateTime|풀 삭제가 시작된 시간입니다.|
|`endTime`|DateTime|풀 삭제가 완료된 시간입니다.|

## <a name="remarks"></a>설명

풀 크기 조정 작업의 상태 및 오류 코드에 대한 자세한 내용은 [계정에서 풀 삭제](/rest/api/batchservice/delete-a-pool-from-an-account)를 참조하세요.
