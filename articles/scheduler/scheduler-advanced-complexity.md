---
title: 고급 작업 일정 및 되풀이 작성 - Azure Scheduler
description: Azure Scheduler에서 고급 일정과 되풀이 작업을 만드는 방법을 알아보기
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.suite: infrastructure-services
ms.assetid: 5c124986-9f29-4cbc-ad5a-c667b37fbe5a
ms.topic: article
ms.date: 11/14/2018
ms.openlocfilehash: a13ce85124dc84362ec1ee2aa39a16c2c3f09f88
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/04/2019
ms.locfileid: "55701015"
---
# <a name="build-advanced-schedules-and-recurrences-for-jobs-in-azure-scheduler"></a>Azure Scheduler에서 고급 일정과 되풀이 작업 작성

> [!IMPORTANT]
> Azure Scheduler는 사용이 중지되며 [Azure Logic Apps](../logic-apps/logic-apps-overview.md)가 대신 제공됩니다. 작업을 예약하려면 [Azure Logic Apps를 대신 사용해보세요](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

[Azure Scheduler](../scheduler/scheduler-intro.md) 작업 내에서, 일정은 Scheduler 서비스가 작업을 실행하는 시기와 방법을 결정하는 핵심입니다. Scheduler를 사용하여 작업에 대해 일회성 일정과 되풀이 일정을 여러 개 설정할 수 있습니다. 일회성 일정은 지정된 시간에 한 번만 실행되며 기본적으로 한 번만 실행되는 되풀이 일정입니다. 되풀이 일정은 지정된 빈도로 실행됩니다. 이러한 유연성을 통해 다양한 비즈니스 시나리오에 Scheduler를 사용할 수 있습니다. 예를 들면:

* **정기적으로 데이터 정리**: 3개월이 지난 트윗은 모두 삭제하는 일일 작업을 만듭니다.

* **데이터 보관**: 청구서 내역을 백업 서비스로 푸시하는 월간 작업을 만듭니다.

* **외부 데이터 요청**: 15분마다 실행되어 NOAA에서 일기 예보를 끌어오는 작업을 만듭니다.

* **이미지 처리**: 사용량이 적은 시간에 실행되어 클라우드 컴퓨팅을 사용하고 그날 업로드된 이미지를 압축하는 평일 작업을 만듭니다.

이 문서에서는 Scheduler와 [Azure Scheduler REST API](/rest/api/scheduler)를 사용하여 만들 수 있고 각 일정에 대한 JSON(JavaScript Object Notation) 정의를 포함하는 예제 작업에 대해 설명하고 합니다. 

## <a name="supported-scenarios"></a>지원되는 시나리오

다음 예제는 Azure Scheduler에서 지원하는 시나리오 범위와, 다음과 같은 다양한 동작 패턴에 대한 일정을 만드는 방법을 보여줍니다.

* 특정 날짜/시간에 한 번만 실행합니다.
* 특정 횟수만큼 실행하고 되풀이합니다.
* 즉시 실행하고 되풀이합니다.
* *n*분, 시간, 일, 주 또는 개월마다 실행되고 되풀이되며, 특정 시간에 시작됩니다.
* 매주 또는 매월 특정 요일과 특정 날짜에만 실행 및 반복됩니다.
* 특정 기간 동안 여러 번 실행 및 반복됩니다. 예를 들어, 매월 마지막 금요일과 월요일 또는 매일 오전 5시 15분 및 오후 5시 15분에 실행 및 반복됩니다.

이 문서의 뒷 부분에 시나리오에 대한 자세한 설명이 있습니다.

<a name="create-scedule"></a>

## <a name="create-schedule-with-rest-api"></a>REST API로 일정 만들기

[Azure Scheduler REST API](/rest/api/scheduler)로 기본 일정을 만들려면 다음 단계를 수행합니다.

1. [등록 작업 - Resource Manager REST API](https://docs.microsoft.com/rest/api/resources/providers)를 사용하여 리소스 공급자에 Azure 구독을 등록합니다. Azure Scheduler 서비스의 공급자 이름은 **Microsoft.Scheduler**입니다. 

1. Scheduler REST API의 [작업 컬렉션에 대한 만들기 또는 업데이트 작업](https://docs.microsoft.com/rest/api/scheduler/jobcollections)을 사용하여 작업 컬렉션을 만듭니다. 

1. [작업에 대한 만들기 또는 업데이트 작업](https://docs.microsoft.com/rest/api/scheduler/jobs/createorupdate)을 사용하여 작업을 만듭니다. 

## <a name="job-schema-elements"></a>작업 스키마 요소

아래 테이블은 작업의 되풀이 및 일정을 설정하는 데 사용할 수 있는 주요 JSON 요소에 대한 대략적인 개요를 제공합니다. 

| 요소 | 필수 | 설명 | 
|---------|----------|-------------|
| **startTime** | 아니요 | 기본 일정에서 작업이 처음 시작되는 시기를 지정하는 [ISO 8601 형식](http://en.wikipedia.org/wiki/ISO_8601)의 날짜/시간 문자열 값입니다. <p>복잡한 일정의 경우 작업은 **startTime** 이후에 시작됩니다. | 
| **recurrence** | 아니요 | 작업이 실행되는 시기에 대한 되풀이 규칙입니다. **recurrence** 개체는 **frequency**, **interval**, **schedule**, **count** 및 **endTime** 요소를 지원합니다. <p>**recurrence** 요소를 사용하는 경우에는 **frequency** 요소도 사용해야 하며 다른 **recurrence** 요소는 선택 사항입니다. |
| **frequency** | 예(**recurrence**를 사용하는 경우) | 발생 간격의 시간 단위이며 “분”, “시간”, “일”, “주”, “월” 및 “연도” 값을 지원합니다. | 
| **interval** | 아니요 | **frequency**에 기반한 발생 간격의 시간 단위 수를 결정하는 양의 정수입니다. <p>예를 들어 **interval**이 10이고 **frequency**가 "week"인 경우 작업은 10주마다 되풀이 됩니다. <p>각 빈도의 간격에 대한 최대 수는 다음과 같습니다. <p>- 18개월 <br>- 78주 <br>- 548일 <br>- 시간과 분의 경우 범위는 1 <= <*interval*> <= 1000입니다. | 
| **schedule** | 아니요 | 지정된 분 표시, 시간 표시, 요일 및 날짜를 기반으로 되풀이에 대한 변경을 정의합니다. | 
| **count** | 아니요 | 작업이 완료되기 전에 실행되는 횟수를 지정하는 양의 정수입니다. <p>예를 들어 일일 작업의 **count**가 7로 설정되고 시작 날짜가 월요일이면 이 작업은 일요일에 실행을 마칩니다. 시작 날짜가 이미 지난 경우에는 만든 시간에서 첫 번째 실행이 계산됩니다. <p>**endTime**이나 **count**가 없으면 작업이 무한으로 실행됩니다. 같은 작업에 **count**와 **endTime**을 둘 다 사용할 수 없지만 먼저 끝나는 규칙이 적용됩니다. | 
| **endTime** | 아니요 | 작업 실행이 중지되는 시기를 지정하는 [ISO 8601 형식](http://en.wikipedia.org/wiki/ISO_8601)의 날짜 또는 날짜/시간 문자열 값입니다. 과거의 **endTime**에 대한 값을 설정할 수 있습니다. <p>**endTime**이나 **count**가 없으면 작업이 무한으로 실행됩니다. 같은 작업에 **count**와 **endTime**을 둘 다 사용할 수 없지만 먼저 끝나는 규칙이 적용됩니다. |
|||| 

예를 들어,이 JSON 스키마는 기본 일정과 되풀이 작업을 설명합니다. 

```json
"properties": {
   "startTime": "2012-08-04T00:00Z", 
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "weekDays": ["Monday", "Wednesday", "Friday"],
         "hours": [10, 22]                      
      },
      "count": 10,       
      "endTime": "2012-11-04"
   },
},
``` 

*날짜 및 날짜/시간 값*

* Scheduler 작업의 날짜에는 날짜만 포함되며 [ISO 8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따릅니다.

* Scheduler 작업의 날짜-시간에는 날짜와 시간이 모두 포함되며 [ISO 8601 사양](http://en.wikipedia.org/wiki/ISO_8601)을 따르고, UTC 오프셋이 지정되지 않은 경우 UTC로 간주됩니다. 

자세한 내용은 [개념, 용어 및 엔터티](../scheduler/scheduler-concepts-terms.md)를 참조하세요.

<a name="start-time"></a>

## <a name="details-starttime"></a>세부 정보: startTime

이 표는 **startTime**으로 작업이 실행되는 방식을 어떻게 제어하는지 설명합니다.

| startTime | 되풀이 없음 | 되풀이, 일정 없음 | 일정대로 되풀이 |
|-----------|---------------|-------------------------|--------------------------|
| **시작 시간 없음** | 즉시 한 번 실행합니다. | 즉시 한 번 실행합니다. 마지막 실행 시간에서 계산된 후속 실행을 실행합니다. | 즉시 한 번 실행합니다. 되풀이 일정에 따른 후속 실행을 실행합니다. | 
| **과거의 시작 시간** | 즉시 한 번 실행합니다. | 시작 시간 이후 첫 번째 실행 시간을 계산하고 해당 시간에 실행합니다. <p>마지막 실행 시간에서 계산된 후속 실행을 실행합니다. <p>이 표 뒤에 나오는 예제를 참조하세요. | 지정된 시작 시간 직후에 작업을 시작합니다. 시작 시간에서 계산된 일정을 기반으로 첫 되풀이를 실행합니다. <p>되풀이 일정에 따른 후속 실행을 실행합니다. | 
| **미래 또는 현재 시간의 시작 시간** | 지정된 시작 시간에 한 번 실행됩니다. | 지정된 시작 시간에 한 번 실행됩니다. <p>마지막 실행 시간에서 계산된 후속 실행을 실행합니다. | 지정된 시작 시간 직후에 작업을 시작합니다. 시작 시간에서 계산된 일정에 따라 첫 번째 되풀이 항목을 실행합니다. <p>되풀이 일정에 따른 후속 실행을 실행합니다. |
||||| 

시작 시간이 지났고, 되풀이가 있지만 일정이 없는 조건의 예제를 가정해 보겠습니다.

```json
"properties": {
   "startTime": "2015-04-07T14:00Z", 
   "recurrence": {
      "frequency": "Day",
      "interval": 2
   }
}
```

* 현재 날짜 및 시간은 2015년 4월 8일 오후 1시입니다.

* 시작 날짜와 시간이 2015년 4월 7일 오후 2시이기 때문에 현재 날짜와 시간보다 과거입니다.

* 되풀이는 2일 간격입니다.

1. 이러한 조건 하에서 첫 번째 실행 시기는 2015년 4월 09일 오후 2시입니다. 

   Scheduler는 시작 시간을 기준으로 실행이 발생하는 시간을 계산하고 과거 인스턴스는 모두 삭제하고, 향후 다음 번 인스턴스를 사용합니다. 
   이 경우 **startTime**은 2015년 4월 7일 오후 2시이므로 다음 인스턴스는 이 시간에서 2일 후인 2015년 4월 9일 오후 2시입니다.

   **startTime**이 2017-04-05 오후 2시이거나 2017-04-01 오후 2시인지 여부에 관계없이 첫 번째 실행 시간은 동일합니다. 첫 번째 실행 후 후속 실행은 일정을 기반으로 계산됩니다. 
   
1. 실행은 다음 순서로 발생합니다. 
   
   1. 2015-04-11 오후 2시
   1. 2015-04-13 오후 2시 
   1. 2015-04-15 오후 2시
   1. 이후 계속...

1. 마지막으로, 작업에 일정이 있지만 시간과 분이 지정되지 않은 경우에는 첫 번째 실행의 시간과 분이 기본값이 됩니다.

<a name="schedule"></a>

## <a name="details-schedule"></a>세부 정보: 일정

**schedule**을 사용하여 작업 실행의 횟수를 *제한*할 수 있습니다. 예를 들어 **frequency**가 "month"인 작업이 31일에만 실행되는 일정이 있는 경우, 이 작업은 31일이 있는 달에만 실행됩니다.

또한 **schedule**을 사용하여 작업 실행의 횟수를 *늘릴* 수도 있습니다. 예를 들어 **frequency**가 "month"인 작업에 월의 1일과 2일에 실행되는 일정이 있는 경우, 이 작업은 한 달에 한 번 실행되는 것이 아니라 월의 1일과 2일에 실행됩니다.

둘 이상의 일정 요소를 지정하는 경우 가장 큰 것부터 가장 작은 것 순으로, 즉 주차, 월 일, 요일, 시간, 분의 순서로 계산됩니다.

다음 테이블은 schedule 요소의 세부 정보입니다.

| JSON 이름 | 설명 | 유효한 값 |
|:--- |:--- |:--- |
| **minutes** |작업이 실행되는 시간(분)입니다. |정수 배열입니다. |
| **hours** |작업이 실행되는 일의 시간(시)입니다. |정수 배열입니다. |
| **weekDays** |작업이 실행되는 요일입니다. 주 단위 빈도로만 지정할 수 있습니다. |다음 값 중 하나의 배열입니다(최대 배열 크기: 7).<br />- "Monday"<br />- "Tuesday"<br />- "Wednesday"<br />- "Thursday"<br />- "Friday"<br />- "Saturday"<br />- "Sunday"<br /><br />대/소문자를 구분하지 않습니다. |
| **monthlyOccurrences** |작업이 실행되는 월의 일을 결정합니다. 월 단위 빈도로만 지정할 수 있습니다. |**monthlyOccurrences** 개체의 배열입니다.<br /> `{ "day": day, "occurrence": occurrence}`<br /><br /> **day**는 작업이 실행되는 주의 요일입니다. 예를 들어 *{Sunday}* 는 월의 매주 일요일입니다. 필수 사항입니다.<br /><br />**occurrence**는 월 중 일의 되풀이 항목입니다. 예를 들어 *{Sunday, -1}* 은 월의 마지막 일요일입니다. 선택 사항입니다. |
| **monthDays** |작업이 실행되는 월의 일입니다. 월 단위 빈도로만 지정할 수 있습니다. |다음 값의 배열입니다.<br />- 1 이상 및 31 이하의 모든 값<br />- 1 이하 및 31 이상의 모든 값|

## <a name="examples-recurrence-schedules"></a>예제: 되풀이 일정

다음 예제에서는 다양한 되풀이 일정을 보여 줍니다. 이러한 예제는 일정 개체 및 해당 하위 요소에 중점을 둡니다.

이러한 일정은 **interval**이 1로 설정되어 있다고 가정합니다\. 또한 예제에서는 **schedule**의 값에 대해 올바른 **frequency** 값이 있다고 가정합니다. 예를 들어 "day"를 **frequency** 값으로 사용할 수 없으며, **schedule**에 **monthDays** 수정 내용이 있을 수 없습니다. 이러한 제한 사항은 이 문서의 앞 부분에서 설명하고 있습니다.

| 예 | 설명 |
|:--- |:--- |
| `{"hours":[5]}` |매일 오전 5시에 실행됩니다.<br /><br />Scheduler에서 "hours"의 각 값을 "minutes"의 각 값과 하나씩 일치시켜 작업이 실행되는 모든 시간의 목록을 만듭니다. |
| `{"minutes":[15], "hours":[5]}` |매일 오전 5시 15분에 실행됩니다. |
| `{"minutes":[15], "hours":[5,17]}` |매일 오전 5시 15분 및 오후 5시 15분에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17]}` |매일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45]}` |15분마다 실행됩니다. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` |매시간 실행됩니다.<br /><br />이 작업은 매시간 실행됩니다. 분이 지정된 경우 **startTime**에 대한 값으로 제어됩니다. **startTime** 값이 지정되지 않으면 분은 만든 시간으로 제어됩니다. 예를 들어 시작 시간 또는 만든 시간 중 해당하는 시간이 오후 12:25인 경우 작업은 00:25, 01:25, 02:25, …, 23:25에 실행됩니다.<br /><br />이 일정은 **frequency**가 "hour"이고 **interval**이 1이며 **schedule** 값이 없는 작업과 동일합니다. 차이점은 이 일정을 다른 **frequency**와 **interval** 값으로 사용하여 다른 작업을 만들 수 있다는 것입니다. 예를 들어 **frequency**가 "month"인 경우 일정은 매일(**frequency**가 "day"인 경우)이 아니라 한 달에 한 번만 실행됩니다. |
| `{minutes:[0]}` |매시간 정각에 실행됩니다.<br /><br />또한 이 작업은 매 시간 정각(오전 12시, 오전 1시, 오전 2시 등)에만 실행됩니다. 이 일정은 frequency가 "day"인 경우 **frequency**가 "hour", **startTime** 값이 0분, **schedule**이 없는 작업과 동일합니다. 그러나 **frequency**가 "week" 또는 "month"인 경우 일정은 각각 주에 하루 또는 월에 하루만 실행됩니다. |
| `{"minutes":[15]}` |매시간 15분에 실행됩니다.<br /><br />오전 0시 15분, 오전 1시 15분, 오전 2시 15분 등에서 시작하여 매 시간마다 실행됩니다. 오후 11시 15분에 끝납니다. |
| `{"hours":[17], "weekDays":["saturday"]}` |매주 토요일 오후 5시에 실행됩니다. |
| `{hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일, 금요일 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오전 5시 및 오후 5시에 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "weekDays":["monday", "wednesday", "friday"]}` |매주 월요일, 수요일 및 금요일 오전 5시 15분, 오전 5시 45분, 오후 5시 15분 및 오후 5시 45분에 실행됩니다. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |월요일부터 금요일까지 15분마다 실행됩니다. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` |월요일부터 금요일까지 오전 9시에서 오후 4시 45분 사이에 매 15분마다 실행됩니다. |
| `{"weekDays":["sunday"]}` |일요일 시작 시간에 실행됩니다. |
| `{"weekDays":["tuesday", "thursday"]}` |화요일 및 목요일 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` |매월 28일 오전 6시에 실행됩니다(**frequency**가 "month"라고 가정). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` |월의 마지막 일 오전 6시에 실행됩니다.<br /><br />월의 마지막 일에 작업을 실행하려면 28, 29, 30 또는 31 대신 -1을 사용합니다. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` |매월 첫 번째 일 및 마지막 일 오전 6시에 실행됩니다. |
| `{monthDays":[1,-1]}` |매월 첫 번째 일 및 마지막 일 시작 시간에 실행됩니다. |
| `{monthDays":[1,14]}` |매월 첫 번째 일 및 14일 시작 시간에 실행됩니다. |
| `{monthDays":[2]}` |매월 2일 시작 시간에 실행됩니다. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |매월 첫 번째 금요일 오전 5시에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` |매월 첫 번째 금요일 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` |매월 마지막 일부터 세 번째 금요일 시작 시간에 실행됩니다. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |매월 첫 번째와 마지막 금요일 오전 5시 15분에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` |매월 첫 번째 및 마지막 금요일 시작 시간에 실행됩니다. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` |매월 5번째 금요일 시작 시간에 실행됩니다.<br /><br />월에 5번째 금요일이 없으면 작업이 실행되지 않습니다. 작업이 매월 마지막으로 금요일에 실행되도록 하려면 occurrence에 대해 5대신 -1을 사용하는 것이 좋습니다. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` |매월 마지막 금요일에 15분마다 실행됩니다. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` |매월 세 번째 수요일에 오전 5시 15분, 오전 5시 45분, 오후 5시 15분, 오후 5시 45분에 실행됩니다. |

## <a name="see-also"></a>참고 항목

* [Azure Scheduler란?](scheduler-intro.md)
* [Azure Scheduler 개념, 용어 및 엔터티 계층 구조](scheduler-concepts-terms.md)
* [Azure Scheduler 제한, 기본값 및 오류 코드](scheduler-limits-defaults-errors.md)
