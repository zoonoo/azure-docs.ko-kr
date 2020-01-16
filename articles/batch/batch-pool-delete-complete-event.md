---
title: Azure Batch 풀 삭제 완료 이벤트 | Microsoft Docs
description: Batch 풀 삭제 완료 이벤트에 대한 참조입니다.
services: batch
author: ju-shim
manager: gwallace
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: jushiman
ms.openlocfilehash: bcbfe009e2880c0155a067305cc28317678cbfa6
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/15/2020
ms.locfileid: "76026701"
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

|요소|유형|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
|`startTime`|DateTime|풀 삭제가 시작된 시간입니다.|
|`endTime`|DateTime|풀 삭제가 완료된 시간입니다.|

## <a name="remarks"></a>설명
풀 크기 조정 작업의 상태 및 오류 코드에 대한 자세한 내용은 [계정에서 풀 삭제](https://docs.microsoft.com/rest/api/batchservice/delete-a-pool-from-an-account)를 참조하세요.
