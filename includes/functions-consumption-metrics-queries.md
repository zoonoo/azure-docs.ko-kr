---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "92168116"
---
#### <a name="determine-memory-usage"></a>메모리 사용량 확인 

**모니터링** 아래에서 **로그(분석)** 를 선택한 후, 다음 원격 분석 쿼리를 복사하여 쿼리 창에 붙여넣고 **실행** 을 선택합니다. 이 쿼리는 샘플링된 각 시간의 총 메모리 사용량을 반환합니다.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

결과는 다음 예제와 같습니다.

| 타임스탬프 \[UTC\]          | name          | 값       |
|----------------------------|---------------|-------------|
| 2019/9/12, 1:05:14\.947 AM | 프라이빗 바이트 | 209,932,288 |
| 2019/9/12, 1:06:14\.994 AM | 프라이빗 바이트 | 212,189,184 |
| 2019/9/12, 1:06:30\.010 AM | 프라이빗 바이트 | 231,714,816 |
| 2019/9/12, 1:07:15\.040 AM | 프라이빗 바이트 | 210,591,744 |
| 2019/9/12, 1:12:16\.285 AM | 프라이빗 바이트 | 216,285,184 |
| 2019/9/12, 1:12:31\.376 AM | 프라이빗 바이트 | 235,806,720 |

#### <a name="determine-duration"></a>기간 결정 

Azure Monitor는 Functions의 경우 함수 앱인 리소스 수준에서 메트릭을 추적합니다. Application Insights 통합은 함수 단위로 메트릭을 내보냅니다. 함수의 평균 기간을 가져오는 분석 쿼리 예제는 다음과 같습니다.

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16\.087                     |
| QueueTrigger MaxDurationMs | 90\.249                     |
| QueueTrigger MinDurationMs | 8\.522                      |
