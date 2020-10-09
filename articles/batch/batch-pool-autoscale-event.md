---
title: Azure Batch 풀 자동 크기 조정 이벤트
description: 풀 자동 크기 조정을 실행 한 후에 내보내는 일괄 처리 풀 자동 크기 조정 이벤트에 대 한 참조입니다. 로그의 내용은 풀의 자동 크기 조정 수식과 평가 결과를 표시 합니다.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: efaf52fb860b744b458295a4009c017e5317be50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/08/2020
ms.locfileid: "91852181"
---
# <a name="pool-autoscale-event"></a>풀 자동 크기 조정 이벤트

 이 이벤트는 풀 자동 크기 조정이 실행 된 후에 내보내집니다. 로그의 내용은 풀의 자동 크기 조정 수식과 평가 결과를 표시 합니다.

 다음 예에서는 샘플 데이터가 충분 하지 않아 실패 한 풀 자동 크기 조정에 대 한 풀 자동 크기 조정 이벤트의 본문을 보여 줍니다.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|요소|Type|메모|
|-------------|----------|-----------|
|`id`|String|풀의 ID입니다.|
|`timestamp`|DateTime|자동 크기 조정이 실행 되는 타임 스탬프입니다.|
|`formula`|문자열|자동 크기 조정을 위해 정의 된 수식입니다.|
|`results`|문자열|수식에 사용 된 모든 변수에 대 한 평가 결과입니다.|
|[`error`](#error)|복합 형식|자동 크기 조정에 대 한 자세한 오류입니다.|

###  <a name="error"></a><a name="error"></a>개 오류

|요소 이름|Type|메모|
|------------------|----------|-----------|
|`code`|String|자동 크기 조정 오류에 대 한 식별자입니다. 코드는 고정 이며 프로그래밍 방식으로 사용 하기 위한 것입니다.|
|`message`|문자열|사용자 인터페이스에 표시 하기에 적합 한 자동 크기 조정 오류를 설명 하는 메시지입니다.|
|`values`|배열|자동 크기 조정 오류에 대 한 자세한 정보를 설명 하는 이름-값 쌍의 목록입니다.|
