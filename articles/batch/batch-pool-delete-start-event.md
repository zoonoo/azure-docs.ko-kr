---
title: "풀 삭제 시작 이벤트 - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: feddca1e-c69c-4257-be44-a36172e77157
caps.latest.revision: 4
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: ef61238c6dc342173215ae628565a0b87a86bcad
ms.lasthandoff: 04/13/2017

---
# <a name="pool-delete-start-event"></a>풀 삭제 시작 이벤트
풀 삭제 시작 이벤트 로그 본문

## <a name="remarks"></a>설명
 이 이벤트는 풀 삭제 작업이 시작되면 내보내집니다. 풀 삭제는 비동기 이벤트이므로 풀 삭제 완료 이벤트는 삭제 작업이 완료되면 내보진다고 예상할 수 있습니다.

 다음 예에서는 풀 삭제 시작 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool1"
}
```

|요소|형식|참고 사항|
|-------------|----------|-----------|
|id|string|풀의 ID입니다.|
