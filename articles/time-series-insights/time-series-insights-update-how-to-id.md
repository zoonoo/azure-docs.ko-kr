---
title: Azure Time Series Insights 미리 보기의 시계열 ID 선택 모범 사례 | Microsoft Docs
description: Azure Time Series Insights 미리 보기에서 시계열 ID를 선택할 때의 모범 사례를 이해합니다.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: af540267e4afc1b248b66b1c6f4989b832c38b58
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237574"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>시계열 ID 선택 모범 사례

이 문서에서는 Azure Time Series Insights 미리 보기 파티션 키, 시계열 ID 및 선택 모범 사례에 대해 설명합니다.

## <a name="choose-a-time-series-id"></a>시계열 ID 선택

시계열 ID 선택은 데이터베이스에 대한 파티션 키를 선택하는 것과 같습니다. 디자인 타임에 수행해야 하는 중요한 결정입니다. 다른 시계열 ID를 사용하도록 기존 Time Series Insights 미리 보기 환경을 업데이트할 수 없습니다. 즉, 시계열 ID를 사용하여 환경을 만들 때 정책은 변경할 수 없는 변경 불가 속성입니다.

> [!IMPORTANT]
> 시계열 ID는 대/소문자를 구분하며 변경 불가(설정된 후에는 변경할 수 없음)입니다.

이 점에 유의해서, 적절한 시계열 ID를 선택하는 것이 중요합니다. 시계열 ID를 선택할 때는 다음과 같은 모범 사례를 따르는 것이 좋습니다.

* 다양한 범위의 값과 균일한 액세스 패턴을 가진 속성 이름을 선택합니다. 고유한 값이 많은(수백 개 또는 수천 개) 파티션 키를 사용하는 것이 좋습니다. 많은 고객의 경우 JSON의 DeviceID 또는 SensorID와 같습니다.
* 시계열 ID는 [시계열 모델](./time-series-insights-update-tsm.md)의 리프 노드 수준에서 고유해야 합니다.
* 시계열 ID 속성 이름 문자열은 최대 128자까지 가능하며, 시계열 ID 속성 값은 최대 1024자까지 가능합니다.
* 일부 고유 시계열 ID 속성 값이 누락되면 null 값으로 간주되어 고유성 제약 조건에 포함됩니다.

또한 최대 *3개*의 키 속성을 시계열 ID로 선택할 수 있습니다.

  > [!NOTE]
  > *3개*의 키 속성은 문자열이어야 합니다.

다음 시나리오에서는 둘 이상의 키 속성을 시계열 ID로 선택하는 방법을 설명합니다.  

### <a name="scenario-one"></a>시나리오 1

* 각각 고유 키를 가진 많은 레거시 집합이 있습니다.
* 예를 들어 한 집합은 *deviceId* 속성으로 고유하게 식별되고, 다른 집합의 고유 속성은 *objectId*입니다. 두 집합 모두, 다른 집합의 고유 속성은 없습니다. 이 예제에서는 두 개의 키인 deviceId 및 objectId를 고유 키로 선택합니다.
* Null 값이 허용되며, 이벤트 페이로드에 속성이 없을 경우 `null` 값으로 계산됩니다. 각 이벤트 원본의 데이터에 고유한 시계열 ID가 있는 두 가지 이벤트 원본으로 데이터를 전송하는 데 적합한 방법이기도 합니다.

### <a name="scenario-two"></a>시나리오 2

* 동일한 자산 집합 내에서 여러 속성이 고유해야 합니다. 
* 예를 들어 스마트 빌딩 제조업체이며 모든 방에 센서를 배포한다고 가정합니다. 일반적으로 각 방의 *sensorId* 값은 *sensor1*, *sensor2*, *sensor3* 등으로 동일합니다.
* 또한 빌딩의 *flrRm* 속성에는 사이트 간에 겹치는 층 및 방 번호가 있으며, 값은 *1a*, *2b*, *3a* 등입니다.
* 마지막으로, *location* 속성이 있으며 값은 *Redmond*, *Barcelona*, *Tokyo* 등입니다. 고유성을 만들려면 *sensorId*, *flrRm* 및 *location*의 세 가지 속성을 시계열 ID 키로 지정합니다.

## <a name="next-steps"></a>다음 단계

* [데이터 모델링](./time-series-insights-update-tsm.md)에 대해 자세히 알아봅니다.

* [Azure Time Series Insights(미리 보기) 환경](./time-series-insights-update-plan.md)을 계획합니다.