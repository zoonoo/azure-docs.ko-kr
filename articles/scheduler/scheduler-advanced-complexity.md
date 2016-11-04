---
title: Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법
description: Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법
services: scheduler
documentationcenter: .NET
author: krisragh
manager: dwrede
editor: ''

ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: krisragh

---
# Azure 스케줄러를 사용하여 복잡한 일정 및 고급 되풀이를 만드는 방법
## 개요
Azure 스케줄러의 핵심 작업은 *일정*입니다. 일정은 스케줄러가 작업을 실행할 시기와 방법을 결정합니다.

Azure 스케줄러를 사용하여 작업에 대해 서로 다른 일회성 및 되풀이 일정을 지정할 수 있습니다. *일회성* 일정은 지정된 시간에 한 번만 실행됩니다. 실질적으로 이는 한 번만 실행되는 *되풀이* 일정입니다. 되풀이 일정은 미리 지정된 빈도로 실행됩니다.

Azure 스케줄러의 이러한 유연성을 활용하여 다양한 비즈니스 시나리오를 지원할 수 있습니다.

* 정기적인 데이터 정리 - 예를 들어 매일 3개월 이상 지난 트윗을 삭제
* 아카이브 – 예를 들어 매달 송장 기록을 백업 서비스로 전송
* 외부 데이터 요청 - 예를 들어 NOAA에서 15분마다 새 ski 날씨 보고서 가져오기
* 이미지 처리 - 예를 들어 주중 매일, 사용량이 적은 시간에 그날 업로드된 이미지를 클라우드 컴퓨팅을 사용하여 압축

이 문서에서는 Azure 스케줄러로 만들 수 있는 작업의 예를 살펴보겠습니다. 각 일정을 설명하는 JSON 데이터를 제공합니다. [스케줄러 REST API](https://msdn.microsoft.com/library/mt629143.aspx)를 사용하는 경우 동일한 이 JSON을 [Azure 스케줄러 작업 만들기](https://msdn.microsoft.com/library/mt629145.aspx)에도 사용할 수 있습니다.

## 지원되는 시나리오
이 항목에서 소개하는 여러 예를 통해 Azure 스케줄러가 지원하는 다양한 시나리오를 볼 수 있습니다. 포괄적으로 보면 이러한 예는 다음을 포함하여 여러 동작 패턴에 대한 일정을 만드는 방법을 설명합니다.

* 특정 날짜 및 시간에 한 번 실행
* 실행하고 명시된 횟수만큼 되풀이
* 즉시 실행하고 되풀이
* 실행하고 *n*분, 시간, 일, 주 또는 월마다 되풀이, 특정 시간에 시작
* 실행하고 주 또는 월마다 되풀이하지만 특정 일, 요일 또는 날짜에만 실행
* 실행하고 기간 내 여러 번 되풀이. 예를 들어 매월 마지막 금요일과 월요일, 매일 오전 5시 15분과 오후 5시 15분

## 날짜 및 날짜-시간
Azure 스케줄러 작업의 날짜는 [ISO-8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따르며 날짜만 포함합니다.

Azure 스케줄러 작업의 날짜-시간 참조는 [ISO-8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따르며 날짜와 시간 부분을 모두 포함합니다. 날짜-시간은 UTC 오프셋을 지정하지 않으며 UTC로 간주됩니다.

## 방법: JSON 및 REST API를 사용하여 일정 만들기
[Azure Scheduler REST API](https://msdn.microsoft.com/library/mt629143)를 사용하여 간단한 일정을 만들려면 먼저 [리소스 공급자로 구독을 등록](https://msdn.microsoft.com/library/azure/dn790548.aspx)(스케줄러의 공급자 이름은 _Microsoft.Scheduler_임)한 다음 [작업 컬렉션을 만들고](https://msdn.microsoft.com/library/mt629159.aspx) 마지막으로 [작업을 만듭니다](https://msdn.microsoft.com/library/mt629145.aspx). 작업을 만들 때는 아래 나열된 것과 같은 JSON을 사용하여 일정과 되풀이를 지정할 수 있습니다.

    {
        "startTime": "2012-08-04T00:00Z", // optional
         …
        "recurrence":                     // optional
        {
            "frequency": "week",     // can be "year" "month" "day" "week" "hour" "minute"
            "interval": 1,                // optional, how often to fire (default to 1)
            "schedule":                   // optional (advanced scheduling specifics)
            {
                "weekDays": ["monday", "wednesday", "friday"],
                "hours": [10, 22]                      
            },
            "count": 10,                  // optional (default to recur infinitely)
            "endTime": "2012-11-04",      // optional (default to recur infinitely)
        },
        …
    }

## 개요: 작업 스키마 기본 사항
다음 표에는 작업의 일정 및 되풀이에 관련된 주요 요소에 대한 간략한 개요가 나열되어 있습니다.

| **JSON 이름** | **설명** |
|:--- |:--- |
| ***startTime*** |_startTime_은 날짜-시간입니다. 간단한 일정의 경우 _startTime_은 처음 실행되는 시간이며 복잡한 일정의 경우 작업은 _startTime_보다 일찍 시작되지 않습니다. |
| ***recurrence*** |*recurrence* 개체는 작업에 대한 되풀이 규칙과 작업이 어떤 되풀이 조건으로 실행될지 지정합니다. 되풀이 개체가 지원하는 요소는 *frequency, interval, endTime, count* 및 _schedule_입니다. _recurrence_가 정의된 경우 _frequency_가 필수 요소이며 _recurrence_의 다른 요소는 선택 사항입니다. |
| ***frequency*** |*frequency* 문자열은 작업이 되풀이되는 빈도 단위를 나타냅니다. 지원되는 값은 *"minute", "hour", "day", "week"* 또는 _"month"_입니다. |
| ***interval*** |*interval_은 양의 정수이며 작업이 얼마나 자주 실행될지 결정하는 _frequency_의 간격을 나타냅니다. 예를 들어 _interval_이 3이고 _frequency_가 "week"인 경우 작업은 3주마다 되풀이됩니다. Azure 스케줄러가 지원하는 최대 _interval_은 월 빈도로 18개월, 주 빈도로 78주, 일 빈도로 548일입니다. 시간과 분 빈도의 경우 지원되는 _interval* 범위는 1 ~ 1000입니다. |
| ***endTime*** |*endTime* 문자열은 이 날짜-시간 이후에는 작업이 실행되지 않도록 지정합니다. *endTime_은 과거의 시간일 수 없습니다. _endTime* 또는 count가 지정되지 않으면 작업은 무한으로 실행됩니다. 같은 작업에 _endTime_과 _count_를 모두 포함해서는 안 됩니다. |
| ***count*** |<p>*count_는 0보다 큰 양의 정수로 작업이 완료되기 전에 몇 번 실행될지 지정합니다.</p><p>_count_는 작업이 완료된 것으로 결정되기 전에 실행되는 횟수를 나타냅니다. 예를 들어 _count_가 5로 지정되고 매일 실행되는 작업의 시작 날짜가 월요일인 경우, 이 작업은 금요일에 실행된 후 완료됩니다. 시작 날짜가 과거인 경우, 첫 실행 시간은 생성 시간에서 계산됩니다.</p><p>_endTime* 또는 _count_가 지정되지 않으면 작업은 무한으로 실행됩니다. 같은 작업에 _endTime_과 _count_를 모두 포함해서는 안 됩니다.</p> |
| ***schedule*** |작업에 빈도가 지정되면 되풀이 일정을 기반으로 되풀이가 변경됩니다. _schedule_에는 분, 시간, 요일, 날짜, 주차를 기반으로 하는 조건이 포함됩니다. |

## 개요: 작업 스키마 기본값, 제한 및 예
이 개요를 먼저 본 후, 각 요소에 대해 자세히 알아보겠습니다.

| **JSON 이름** | **값 형식** | **필수 여부** | **기본값** | **유효한 값** | **예제** |
|:--- |:--- |:--- |:--- |:--- |:--- |
| ***startTime*** |문자열 |아니요 |없음 |ISO-8601 날짜-시간 |<code>"startTime" : "2013-01-09T09:30:00-08:00"</code> |
| ***recurrence*** |Object |아니요 |없음 |되풀이 개체 |<code>"recurrence" : { "frequency" : "monthly", "interval" : 1 }</code> |
| ***frequency*** |문자열 |예 |없음 |"minute", "hour", "day", "week", "month" |<code>"frequency" : "hour"</code> |
| ***interval*** |Number |아니요 |1 |1 ~ 1000입니다. |<code>"interval":10</code> |
| ***endTime*** |문자열 |아니요 |없음 |현재 이후의 시간을 나타내는 날짜-시간입니다. |<code>"endTime" : "2013-02-09T09:30:00-08:00"</code> |
| ***count*** |Number |아니요 |없음 |1 이상 |<code>"count": 5</code> |
| ***schedule*** |Object |아니요 |없음 |일정 개체 |<code>"schedule" : { "minute" : [30], "hour" : [8,17] }</code> |

## 자세히 알아보기: *startTime*
다음 표는 _startTime_으로 작업이 실행되는 방식을 제어하는 방법을 설명합니다.

| **startTime 값** | **되풀이 없음** | **되풀이. 일정 없음** | **일정대로 되풀이** |
|:--- |:--- |:--- |:--- |
| **시작 시간 없음** |즉시 한 번 실행 |즉시 한 번 실행합니다. 마지막 실행 시간에서 계산된 값을 기반으로 후속 실행 수행 |<p>즉시 한 번 실행</p><p>되풀이 일정에 따라 후속 실행을 수행</p> |
| **시작 시간이 이전임** |즉시 한 번 실행 |<p>시작 시간 이후의 첫 실행 시간을 계산하고 이 시간에 실행</p><p>마지막 실행 시간을 기반으로 후속 실행 시간을 계산하여 실행</p><p>더 자세한 설명은 이 표 아래의 예를 참조하세요.</p> |<p>작업 시작은 지정된 시작 시간보다 _이전_이어서는 안 됩니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이 실행</p><p>되풀이 일정을 기반으로 후속 실행 수행</p> |
| **시작 시간이 이후이거나 현재임** |지정된 시작 시간에 한 번 실행 |<p>지정된 시작 시간에 한 번 실행</p><p>마지막 실행 시간으로 계산한 값을 기반으로 후속 실행을 수행</p> |<p>작업 시작은 지정된 시작 시간보다 _이전_이어서는 안 됩니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이 실행</p><p>되풀이 일정을 기반으로 후속 실행 수행</p> |

_startTime_이 이전이고 _recurrence_가 있으나 _schedule_이 없는 경우에 어떻게 되는지 예를 보겠습니다. 현재 시간이 2015-04-08 13:00, _startTime_은 2015-04-07 14:00, _recurrence_는 2일마다, _frequency_는 일, _interval_은 2로 정의되어 있다고 가정합니다. _startTime_이 현재 시간보다 이전임에 유의하십시오.

이러한 조건일 경우 _첫 실행_은 2015-04-09 14:00가 됩니다. 스케줄러 엔진이 시작 시간에서 되풀이 실행 시간을 계산합니다. 현재보다 이전의 모든 인스턴스는 무시됩니다. 엔진은 이후에 발생하는 다음 인스턴스를 사용합니다. 따라서 이 경우, _startTime_이 2015-04-07 오후 2:00이므로 다음 인스턴스는 이 시간에서 2일 후인 2015-04-09 오후 2:00입니다.

startTime이 2015-04-05 14:00이거나 2015-04-01 14:00이더라도 첫 실행 시간은 동일합니다. 첫 실행 후, 후속 실행은 일정을 사용하여 계산됩니다. 따라서 후속 실행은 2015-04-11 오후 2:00, 2015-04-13 오후 2:00, 2015-04-15 오후 2:00 순서가 됩니다.

마지막으로, 작업에 일정이 있을 경우 일정에 시간 및/또는 분이 설정되어 있지 않으면 첫 실행의 시간 및/또는 분이 기본값이 됩니다.

## 자세히 알아보기: *schedule*
_schedule_은 작업 실행의 횟수를 _제한_할 수 있는 방법이기도 합니다. 예를 들어 빈도가 "month"인 작업에 31일에만 실행되는 _schedule_이 있을 경우, 이 작업은 31<sup></sup>일이 있는 달에만 실행됩니다.

또한 *schedule_로 작업 실행의 횟수를 _늘릴* 수도 있습니다. 예를 들어 빈도가 “month"인 작업의 _schedule_이 매월 1일과 2일로 설정된 경우, 작업은 한 달에 한 번 실행되는 것이 아니라 매월 1<sup></sup>일과 2<sup></sup>일에 실행됩니다.

여러 일정 요소가 지정된 경우, 계산되는 순서는 가장 큰 것부터 가장 작은 것의 순서입니다. 즉 주차, 날짜, 요일, 시간, 분의 순서입니다.

다음 표는 *schedule* 요소의 세부 정보입니다.

| **JSON 이름** | **설명** | **유효한 값** |
|:--- |:--- |:--- |
| **minutes** |작업이 실행될 시간(분) |<ul><li>정수 또는</li><li>정수 배열</li></ul> |
| **hours** |작업이 실행될 시간(시간) |<ul><li>정수 또는</li><li>정수 배열</li></ul> |
| **weekDays** |작업이 실행될 요일입니다. 빈도가 주인 경우에만 지정할 수 있습니다. |<ul><li>"Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday" 또는 "Sunday"</li><li>위 값의 배열(최대 배열 크기는 7)</li></ul>대/소문자 구분 *안 함* |
| **monthlyOccurrences** |며칠에 작업을 실행할지 결정합니다. 빈도가 월인 경우에만 지정할 수 있습니다. |<ul><li>monthlyOccurence 개체의 배열:</li></ul> <pre>{ "day": *일*,<br /> "occurrence": *되풀이*<br />}</pre><p> _일_은 작업이 실행될 요일입니다. 예를 들어 {Sunday}는 매주 일요일입니다. 필수.</p><p>occurrence는 월 중에 _되풀이_되는 날입니다. 예를 들어 {Sunday, -1}은 월의 마지막 일요일입니다. 선택 사항입니다.</p> |
| **monthDays** |작업이 실행될 월의 일입니다. 빈도가 월인 경우에만 지정할 수 있습니다. |<ul><li>-31 ~ -1 사이의 값.</li><li>1 ~ 31 사이의 값.</li><li>위 값의 배열</li></ul> |

## 예: 되풀이 일정
다음은 일정 개체와 하위 요소에 초점을 맞춘 되풀이 일정의 다양한 예입니다.

아래 모든 일정의 _interval_은 1로 설정되어 있다고 가정합니다. 또한 _schedule_에 맞는 적절한 빈도가 설정되어 있다고 가정합니다. 예를 들어 빈도로 "day"를 사용할 수 없는 일정은 "monthDays"로 수정됩니다. 이러한 제한 사항은 위에 설명되어 있습니다.

| **예제** | **설명** |
|:--- |:--- |
| <code>{"hours":[5]}</code> |매일 오전 5시에 실행합니다. Azure 스케줄러는 "hours"의 각 값을 "minutes"의 각 값과 1:1로 짝을 맞춰 작업이 실행될 모든 시간의 목록을 만듭니다. |
| <code>{"minutes":[15], "hours":[5]}</code> |매일 오전 5시 15분에 실행 |
| <code>{"minutes":[15], "hours":[5,17]}</code> |매일 오전 5시 15분과 오후 5시 15분에 실행 |
| <code>{"minutes":[15,45], "hours":[5,17]}</code> |매일 오전 5시 15분, 5시 45분, 오후 5시 15분, 5시 45분에 실행 |
| <code>{"minutes":[0,15,30,45]}</code> |15분마다 실행 |
| <code>{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}</code> |1시간마다 실행 이 작업은 매시간 실행됩니다. 분은 _startTime_이 지정된 경우 이를 통해 제어되며, 지정되지 않은 경우 생성 시간으로 제어됩니다. 예를 들어 시작 시간 또는 생성 시간(둘 중 해당하는 것)이 오후 12:25라면 작업은 00:25, 01:25, 02:25, …, 23:25에 실행됩니다. 이 일정은 작업의 _frequency_가 "hour"이고 _interval_은 1이며 _schedule_이 없는 것과 동일합니다. 차이점은 이 일정의 _frequency_와 _interval_을 다르게 하여 다른 작업을 만들 수 있다는 점입니다. 예를 들어 _frequency_가 "month"라면 _frequency_가 "day"일 때와 달리 일정은 한 달에 한 번만 실행됩니다. |
| <code>{minutes:[0]}</code> |매시간 정각에 실행합니다. 이 작업은 매시간, 정각(예: 오전 12시, 1시, 2시 등)에만 실행됩니다. 이는 frequency가 “hour”, startTime이 0분인 작업과 동일합니다. 하지만 frequency가 “day”, “week” 또는 “month”라면 각각 하루에 한 번, 일주일에 한 번, 한 달에 한 번만 실행됩니다. |
| <code>{"minutes":[15]}</code> |매시간 15분에 실행됩니다. 오전 0시 15분, 1시 15분, 2시 15분 등으로 시작하여 오후 10시 15분, 11시 15분에 완료됩니다. |
| <code>{"hours":[17], "weekDays":["saturday"]}</code> |매주 토요일 오후 5시에 실행 |
| <code>{hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |매주 월요일, 수요일, 금요일 오후 5시에 실행 |
| <code>{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}</code> |매주 월요일, 수요일, 금요일 오후 5시 15분과 5시 45분에 실행 |
| <code>{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |매주 월요일, 수요일, 금요일 오전 5시와 오후 5시에 실행 |
| <code>{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}</code> |매주 월요일, 수요일, 금요일 오전 5시 15분, 5시 45분, 오후 5시 15분, 5시 45분에 실행 |
| <code>{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |월요일부터 금요일까지 15분마다 실행 |
| <code>{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}</code> |월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 15분마다 실행 |
| <code>{"weekDays":["sunday"]}</code> |일요일 시작 시간에 실행 |
| <code>{"weekDays":["tuesday", "thursday"]}</code> |화요일과 목요일 시작 시간에 실행 |
| <code>{"minutes":[0], "hours":[6], "monthDays":[28]}</code> |매월 28일 오전 6시에 실행(빈도가 month라고 가정) |
| <code>{"minutes":[0], "hours":[6], "monthDays":[-1]}</code> |월의 마지막 날짜 오전 6시에 실행합니다. 월의 마지막 날짜에 작업을 실행하려면 28, 29, 30 또는 31 대신 -1을 사용하십시오. |
| <code>{"minutes":[0], "hours":[6], "monthDays":[1,-1]}</code> |매월 1일과 말일의 오전 6시에 실행 |
| <code>{monthDays":[1,-1]}</code> |매월 1일과 말일의 시작 시간에 실행 |
| <code>{monthDays":[1,14]}</code> |매월 1일과 14일의 시작 시간에 실행 |
| <code>{monthDays":[2]}</code> |매월 2일 시작 시간에 실행 |
| <code>{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |매월 첫 번째 금요일 오전 5시에 실행 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}</code> |매월 첫 번째 금요일 시작 시간에 실행 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}</code> |매월 끝에서 세 번째 금요일 시작 시간에 실행 |
| <code>{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}</code> |매월 첫 번째와 마지막 금요일 시작 시간에 실행 |
| <code>{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}</code> |매월 5번째 금요일 시작 시간에 실행됩니다. 5번째 금요일에만 실행되도록 설정되었으므로 5번째 금요일이 없는 달에는 실행되지 않습니다. 매월 마지막 금요일에 실행되도록 하려면 occurrence로 5대신 -1을 사용하면 됩니다. |
| <code>{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}</code> |매월 마지막 금요일에 15분마다 실행 |
| <code>{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}</code> |매월 세 번째 수요일 오전 5시 15분, 5시 45분, 오후 5시 15분, 5시 45분에 실행 |

## 참고 항목
 [스케줄러란?](scheduler-intro.md)

 [Azure 스케줄러 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)

 [Azure 포털에서 스케줄러 사용 시작](scheduler-get-started-portal.md)

 [Azure 스케줄러의 버전 및 요금 청구](scheduler-plans-billing.md)

 [Azure 스케줄러 REST API 참조](https://msdn.microsoft.com/library/mt629143)

 [Azure 스케줄러 PowerShell cmdlet 참조](scheduler-powershell-reference.md)

 [Azure 스케줄러 고가용성 및 안정성](scheduler-high-availability-reliability.md)

 [Azure 스케줄러 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)

 [Azure 스케줄러 아웃바운드 인증](scheduler-outbound-authentication.md)

<!---HONumber=AcomDC_0824_2016-->