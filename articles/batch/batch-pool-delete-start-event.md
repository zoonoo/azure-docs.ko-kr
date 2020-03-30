---
title: Azure 일괄 처리 풀 삭제 시작 이벤트
description: Batch 풀 삭제 시작 이벤트에 대한 참조입니다. 이 이벤트는 풀 삭제 작업이 시작되면 내보내집니다.
services: batch
author: LauraBrenner
manager: evansma
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 04/20/2017
ms.author: labrenne
ms.openlocfilehash: 24a68c6656bd13f0c353d53870a51cdc940fd141
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022208"
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
