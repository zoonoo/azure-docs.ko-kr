---
title: 타임시리즈 ID 선택 모범 사례 - Azure 타임시리즈 인사이트 | 마이크로 소프트 문서
description: Azure 열열 인사이트 미리 보기에서 타임시리즈 ID를 선택할 때의 모범 사례에 대해 알아봅니다.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: a62c2460698408f6a2bfa51c6638bdeaf88bb31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083534"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>시계열 ID 선택 모범 사례

이 문서에서는 Azure Time Series Insights 미리 보기 환경에 대한 타임시리즈 ID의 중요성과 이를 선택하는 모범 사례를 요약합니다.

## <a name="choose-a-time-series-id"></a>시계열 ID 선택

적절한 타임시리즈 ID를 선택하는 것은 매우 중요합니다. 시계열 ID 선택은 데이터베이스에 대한 파티션 키를 선택하는 것과 같습니다. 타임시리즈 인사이트 미리 보기 환경을 만들 때 필요합니다. 

> [!IMPORTANT]
> 타임시리즈 아이디는 다음과 같습니다.
> * *대/소문자 구분* 속성: 문자 및 문자 케이스는 검색, 비교, 업데이트 및 분할 할 때 사용됩니다.
> * *변경할 수 없는* 속성: 일단 만든 속성은 변경할 수 없습니다.

> [!TIP]
> 이벤트 소스가 IoT 허브인 경우 타임시리즈 ID는 ***iothub 연결 장치 ID일***수 있습니다.

따라야 할 주요 모범 사례는 다음과 같습니다.

* 여러 가지 고유한 값(예: 수백 또는 수천)이 있는 파티션 키를 선택합니다. 대부분의 경우 JSON의 장치 ID, 센서 ID 또는 태그 ID일 수 있습니다.
* 시계열 ID는 [시계열 모델](./time-series-insights-update-tsm.md)의 리프 노드 수준에서 고유해야 합니다.
* 타임 시리즈 ID의 속성 이름 문자열의 문자 제한은 128입니다. 타임시리즈 ID의 속성 값의 경우 문자 제한은 1,024입니다.
* 타임 시리즈 ID에 대한 고유 속성 값이 없는 경우 null 값으로 처리되고 고유 성 제약 조건의 동일한 규칙을 따릅니다.
* 최대 *3개의* 키 속성을 타임시리즈 ID로 선택할 수도 있습니다. 이들의 조합은 타임시리즈 ID를 나타내는 복합 키입니다.  
  > [!NOTE]
  > 세 가지 키 속성은 문자열이어야 합니다.
  > 한 번에 하나의 속성 대신 이 복합 키에 대해 쿼리해야 합니다.

## <a name="select-more-than-one-key-property"></a>두 개 이상의 키 속성 선택

다음 시나리오는 두 개 이상의 키 속성을 타임시리즈 ID로 선택하는 것을 설명합니다.  

### <a name="example-1-time-series-id-with-a-unique-key"></a>예 1: 고유한 키가 있는 열계 ID

* 레거시 자산 플릿이 있습니다. 각 키에는 고유한 키가 있습니다.
* 하나의 플릿은 속성 **장치Id에**의해 고유하게 식별됩니다. 다른 플릿의 경우 고유 속성은 **objectId**입니다. 두 함대 모두 다른 함대의 고유한 속성을 포함하지 않습니다. 이 예제에서는 두 개의 키인 **deviceId** 및 **objectId를**고유 키로 선택합니다.
* null 값을 허용하며 이벤트 페이로드에서 속성이 없는 경우 null 값으로 계산됩니다. 또한 각 이벤트 원본의 데이터에 고유한 Time Series ID가 있는 두 이벤트 원본으로 데이터를 보내는 것을 처리하는 적절한 방법입니다.

### <a name="example-2-time-series-id-with-a-composite-key"></a>예 2: 복합 키가 있는 열계 ID

* 동일한 자산 집합 내에서 여러 속성이 고유해야 합니다. 
* 스마트 빌딩 제조업체이며 모든 공간에 센서를 배치할 수 있습니다. 각 룸에서 일반적으로 **sensorId에**대해 동일한 값을 가수합니다. 예를 들어 **센서1,** **센서2**및 **센서3입니다.**
* 건물에는 **건물 flrRm의**사이트 간에 층과 객실 번호가 겹칩니다. 이 숫자에는 **1a,** **2b**및 **3a와**같은 값이 있습니다.
* **레드먼드,** **바르셀로나**및 **도쿄와**같은 값을 포함하는 속성, **위치가**있습니다. 고유성을 만들려면 다음 세 가지 속성을 **[sensorId,** **flrRm]** 및 **위치의**세 가지 속성을 [열렬 ID 키]로 지정합니다.

원시 이벤트 예:

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

Azure 포털에서 다음과 같이 복합 키를 입력할 수 있습니다. 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>다음 단계

* [데이터 모델링에](./time-series-insights-update-tsm.md)대해 자세히 알아보기.

* Azure [Time Series 인사이트 미리 보기 환경을](./time-series-insights-update-plan.md)계획합니다.