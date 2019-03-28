---
title: 비정상 탐지기 API를 사용 하는 경우 모범 사례
description: 비정상 탐지기 API를 사용 하 여 변칙을 검색 하는 경우 모범 사례에 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: article
ms.date: 03/26/2019
ms.author: aahi
ms.openlocfilehash: 467ac4e475a1c23e25b62c76cfbc959e7ed49465
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/26/2019
ms.locfileid: "58484044"
---
# <a name="best-practices-for-using-the-anomaly-detector-api"></a>비정상 탐지기 API를 사용 하 여에 대 한 모범 사례

비정상 탐지기 API는 상태 비저장 변칙 검색 서비스. 그 결과의 성능과 정확도 영향을 받을 수 있습니다.

* 어떻게 시계열 데이터 준비 됩니다.
* 사용 된 비정상 탐지기 API 매개 변수입니다.
* API 요청에 대 한 데이터 요소의 수입니다. 

이 문서를 사용 하 여 최상의 결과 얻는 데이터에 대 한 API를 사용 하 여에 대 한 모범 사례를 알아봅니다. 

## <a name="data-preparation"></a>데이터 준비

비정상 탐지기 API는 시계열 허용 데이터 형식이 JSON 요청 개체를 지정 합니다. 시계열에 순차적 시간 동안 기록 된 모든 숫자 데이터를 수 있습니다. 시계열 데이터의 windows API의 성능 향상을 위해 비정상 탐지기 API 끝점에 보낼 수 있습니다. 보낼 수 있습니다 하는 데이터 요소의 최소 수는 12이 고 최대값은 8640 지점입니다. 

비정상 탐지기 API로 전송 하는 데이터 요소는 숫자 값을 유효한 utc (협정 세계시) 타임 스탬프 있어야 합니다. 

```json
{
    "granularity": "daily",
    "series": [
      {
        "timestamp": "2018-03-01T00:00:00Z",
        "value": 32858923
      },
      {
        "timestamp": "2018-03-02T00:00:00Z",
        "value": 29615278
      },
    ]
}
```

### <a name="missing-data-points"></a>누락 데이터 요소가

누락 데이터 요소가 세부적으로 시뮬레이트하도록 (작은 샘플링 간격을 사용 하 여 특히 균등 하 게 분산된 시간 시계열 데이터 집합에 공통적인입니다. 예를 들어 데이터 샘플링 몇 분 마다). 데이터에서 요소의 예상된 수의 10% 미만의 누락 된 검색 결과에 부정적인 영향을 줄이 없어야 합니다. 데이터 요소는 이전 기간, 선형 보간을 또는 이동 평균에서 대체와 같은 특징에 따라 데이터의 간격을 채우는 것이 좋습니다.

### <a name="aggregate-distributed-data"></a>분산된 된 집계 데이터

비정상 탐지기 API는 균등 하 게 분산된 시계열에 가장 적합합니다. 데이터를 무작위로 분산 하는 경우 있습니다 해야 집계 당 분, 매시간 또는 매일 같은 시간 단위로 예를 들어 있습니다.

## <a name="anomaly-detection-on-data-with-seasonal-patterns"></a>계절성 패턴을 사용 하 여 데이터에서 변칙 검색

시계열 데이터에는 계절성 패턴 (하나는 정기적으로 발생 하는)을 알고 있는 경우에 정확도 API 응답 시간을 개선할 수 있습니다. 

지정 된 `period` JSON 요청 변칙 검색 대기 시간을 최대 50%까지 줄일 수 있습니다 생성 하는 경우. `period` 은 대략 데이터 요소는 시계열을 지정 하는 정수는 패턴을 반복 합니다. 예를 들어, 하루에 하나의 데이터 지점 사용 하 여 시계열은 것이 `period` 으로 `7`, (동일한 주간 패턴)을 사용 하는 시간 당 1 포인트를 사용 하 여 시계열 고는 `period` 의 `7*24`합니다. 데이터의 패턴을 잘 모를 경우이 매개 변수를 지정할 필요가 없습니다.

최상의 결과 얻으려면 제공 4 `period`의 분량의 데이터 요소 및 항목을 추가 합니다. 예를 들어 주 단위 패턴을 사용 하 여 시간별 데이터 위에 설명 된 대로 제공 해야 요청 본문에 673 데이터 요소 (`7 * 24 * 4 + 1`).

### <a name="sampling-data-for-real-time-monitoring"></a>실시간 모니터링에 대 한 샘플링 데이터

짧은 간격 (예: 초 또는 분)으로 스트리밍 데이터를 샘플링 하는 경우 권장 되는 데이터 요소 수를 전송 비정상 탐지기 API의 최대 숫자 허용된 (8640 데이터 요소)를 초과할 수 있습니다. 안정적인 계절성 패턴을 표시 하는 데이터, 시간 등 큰 시간 간격으로 시계열 데이터의 샘플을 전송 하는 것이 좋습니다. 이러한 방식으로 데이터를 샘플링 API 응답에 크게 개선할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [비정상 탐지기 API 란 무엇 인가요?](../overview.md)
* [빠른 시작: 비정상 탐지기 REST API를 사용 하 여 시계열 데이터에서 변칙을 검색 합니다.](../quickstarts/detect-data-anomalies-csharp.md)
