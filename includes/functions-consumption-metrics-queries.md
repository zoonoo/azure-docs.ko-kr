---
ms.openlocfilehash: 0bae8946b7d81fbf45698cefe7c8b00bbc94d00c
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168116"
---
#### <a name="determine-memory-usage"></a>메모리 사용량 확인 

**모니터링**아래에서 **로그 (분석)** 를 선택 하 고 다음 원격 분석 쿼리를 복사 하 여 쿼리 창에 붙여넣고 **실행**을 선택 합니다. 이 쿼리는 샘플링 된 각 시간의 총 메모리 사용량을 반환 합니다.

```
performanceCounters
| where name == "Private Bytes"
| project timestamp, name, value
```

결과는 다음 예제와 같습니다.

| 타임 스탬프 \[ UTC\]          | name          | 값       |
|----------------------------|---------------|-------------|
| 9/12/2019, 1:05:14 \. 947 AM | 프라이빗 바이트 | 209932288 |
| 9/12/2019, 1:06:14 \. 994 AM | 프라이빗 바이트 | 212189184 |
| 9/12/2019, 1:06:30 \. 010 AM | 프라이빗 바이트 | 231714816 |
| 9/12/2019, 1:07:15 \. 040 AM | 프라이빗 바이트 | 210591744 |
| 9/12/2019, 1:12:16 \. 285 AM | 프라이빗 바이트 | 216285184 |
| 9/12/2019, 1:12:31 \. 376 AM | 프라이빗 바이트 | 235806720 |

#### <a name="determine-duration"></a>기간 결정 

Azure Monitor는 함수 앱 인 리소스 수준에서 메트릭을 추적 합니다. Application Insights 통합은 함수 단위로 메트릭을 내보냅니다. 함수의 평균 기간을 가져오는 분석 쿼리 예제는 다음과 같습니다.

```
customMetrics
| where name contains "Duration"
| extend averageDuration = valueSum / valueCount
| summarize averageDurationMilliseconds=avg(averageDuration) by name
```

| name                       | averageDurationMilliseconds |
|----------------------------|-----------------------------|
| QueueTrigger AvgDurationMs | 16 \. 087                     |
| QueueTrigger MaxDurationMs | 90 \. 249                     |
| QueueTrigger MinDurationMs | 8 \. 522                      |
