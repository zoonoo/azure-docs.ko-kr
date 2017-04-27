---
title: "풀 크기 조정 시작 이벤트 - Azure | Microsoft Docs"
ms.custom: 
ms.date: 2017-02-01
ms.prod: azure
ms.reviewer: 
ms.service: batch
ms.suite: 
ms.tgt_pltfrm: 
ms.topic: reference
ms.assetid: 8bd33e8b-6390-4a34-95dc-2e63d8bce941
caps.latest.revision: 6
author: tamram
ms.author: tamram
manager: timlt
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 5064d86063251aeed22688d8fac133c4be1a88f8
ms.lasthandoff: 04/13/2017

---
# <a name="pool-resize-start-event"></a>풀 크기 조정 시작 이벤트
풀 크기 조정 시작 이벤트 로그 본문

## <a name="remarks"></a>설명
 이 이벤트는 풀 크기 조정이 시작되면 내보내집니다. 풀 크기 조정은 비동기 이벤트이므로 풀 크기 조정 완료 이벤트는 크기 조정 작업이 완료되면 내보진다고 예상할 수 있습니다.

 다음 예에서는 수동 크기 조정을 통해 풀의 크기를 0개 노드에서 2개 노드로 조정하기 위한 풀 크기 조정 시작 이벤트의 본문을 보여 줍니다.

```
{
    "poolId": "myPool1",
    "nodeDeallocationOption": "invalid",
    "currentDedicated": 0,
    "targetDedicated": 2,
    "enableAutoScale": false,
    "isAutoPool": false
}
```

|요소|형식|참고 사항|
|-------------|----------|-----------|
|poolId|String|풀의 ID입니다.|
|nodeDeallocationOption|String|풀 크기가 감소하는 경우 풀에서 노드를 제거할 수 있는 시기를 지정합니다.<br /><br /> 가능한 값은 다음과 같습니다.<br /><br /> **requeue** – 실행 중인 태스크를 종료하고 해당 태스크를 다시 대기열에 추가합니다. 작업이 활성화되면 태스크가 다시 실행됩니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **terminate** – 실행 중인 태스크를 종료합니다. 태스크가 다시 실행되지 않습니다. 태스크가 종료되는 즉시 노드를 제거합니다.<br /><br /> **taskcompletion** – 현재 실행 중인 태스크가 완료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크가 완료되면 노드를 제거합니다.<br /><br /> **Retaineddata** - 현재 실행 중인 태스크가 완료될 때까지 기다린 후 모든 태스크 데이터 보존 기간이 만료될 때까지 기다립니다. 대기하는 동안 새 태스크를 예약하지 않습니다. 모든 태스크 보존 기간이 만료되면 노드를 제거합니다.<br /><br /> 기본값은 requeue입니다.<br /><br /> 풀 크기가 증가하는 경우 값이 **invalid**로 설정됩니다.|
|currentDedicated|Int32|현재 풀에 할당된 계산 노드 수입니다.|
|targetDedicated|Int32|풀에 대해 요청된 계산 노드 수입니다.|
|enableAutoScale|Bool|풀 크기가 시간이 지남에 따라 자동으로 조정되는지 여부를 지정합니다.|
|isAutoPool|Bool|풀이 작업의 자동 풀 메커니즘을 통해 만들어졌는지 여부를 지정합니다.|

