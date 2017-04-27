---
title: "풀 삭제 완료 이벤트 - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 580a1278-5740-4143-826c-7d875ef127ff
caps.latest.revision: 5
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 991ee552ccc564be149844b58a0a3fea7452f2a3
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-complete-event"></a>풀 삭제 완료 이벤트
풀 삭제 완료 이벤트 로그 본문

## <a name="remarks"></a>설명
 이 이벤트는 풀 삭제 작업이 완료되면 내보내집니다.

 다음 예에서는 풀 삭제 완료 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool1",
    "startTime": "2016-09-09T22:13:48.579Z",
    "endTime": "2016-09-09T22:14:08.836Z"
}
```

|요소|형식|참고 사항|
|-------------|----------|-----------|
|id|String|풀의 ID입니다.|
|startTime|DateTime|풀 삭제가 시작된 시간입니다.|
|endTime|DateTime|풀 삭제가 완료된 시간입니다.|

## <a name="remarks"></a>설명
풀 크기 조정 작업의 상태 및 오류 코드에 대한 자세한 내용은 [계정에서 풀 삭제](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)를 참조하세요.
