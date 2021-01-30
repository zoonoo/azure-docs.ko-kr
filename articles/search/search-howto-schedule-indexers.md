---
title: 인덱서 실행 예약
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서를 예약 하 여 콘텐츠를 주기적으로 또는 특정 시간에 인덱싱합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: dfd8526a035d4eef4d07539e541e37c88023b500
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/29/2021
ms.locfileid: "99063216"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure Cognitive Search에서 인덱서를 예약 하는 방법

인덱서는 생성 후 즉시 한 번 실행 됩니다. 그런 다음 Azure Portal, [인덱서 실행 (REST)](/rest/api/searchservice/run-indexer)또는 Azure SDK를 사용 하 여 요청 시 다시 실행할 수 있습니다. 또는 일정에 따라 실행 되도록 인덱서를 구성할 수도 있습니다. 인덱서 일정을 유용 하 게 사용할 수 있는 경우는 다음과 같습니다.

* 원본 데이터는 시간이 지남에 따라 변경 되며 검색 인덱서가 델타를 자동으로 처리 하려고 합니다.
* 원본 데이터가 매우 크고 인덱서 처리를 시간에 따라 분산 하려고 합니다. 대량의 데이터를 인덱싱하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search에서 대용량 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)을 참조 하세요.
* 검색 인덱스는 여러 데이터 원본에서 채워지고 인덱서는 충돌을 줄이기 위해 서로 다른 시간에 실행 하려고 합니다.

시각적으로 일정은 1 월 1 일에 시작 하 여 50 분 마다 실행 되는 것 처럼 보일 수 있습니다.

```json
{
    "dataSourceName" : "myazuresqldatasource",
    "targetIndexName" : "target index name",
    "schedule" : { "interval" : "PT50M", "startTime" : "2021-01-01T00:00:00Z" }
}
```

> [!NOTE]
> 스케줄러는 Azure Cognitive Search의 기본 제공 기능입니다. 외부 스케줄러는 지원 되지 않습니다.

## <a name="schedule-property"></a>일정 속성

일정은 인덱서 정의의 일부입니다. **Schedule** 속성을 생략 하면 인덱서가 생성 된 후 즉시 실행 됩니다. **일정** 속성을 추가 하는 경우 두 부분을 지정 합니다.

| 속성 | 설명 |
|----------|-------------|
|**간격** | 하다 연속 된 두 인덱서 실행의 시작 사이에 있는 시간입니다. 허용 되는 최소 간격은 5 분이 고 가장 긴 시간은 1440 분 (24 시간)입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. <br/><br/>예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.|
| **시작 시간(UTC)** | 필드 예약 된 실행을 시작 해야 하는 시기를 나타냅니다. 생략 하는 경우 현재 UTC 시간이 사용 됩니다. 이 시간은 이전에 일 수 있습니다 .이 경우 첫 번째 실행은 인덱서가 원래 **startTime** 이후에도 계속 실행 되 고 있는 것 처럼 예약 됩니다.<br/><br/>예: 1 `2021-01-01T00:00:00Z` 월 1 일 자정부터 `2021-01-05T22:28:00Z` 오후 10:28 시부터 시작 하 여 시작 1 월 5 일|

## <a name="scheduling-behavior"></a>예약 동작

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 다음 실행이 예약 될 때 인덱서가 이미 실행 중인 경우 해당 실행은 예약 된 다음 시간까지 연기 됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 시간 간격으로 시간 **간격** 으로 인덱서 일정을 구성 하 고 **시작 시간** 을 2019 년 6 월 1 일 오전 8:00:00 UTC로 구성 한다고 가정 합니다. 인덱서 실행이 1 시간 이상 소요 되는 경우 다음과 같은 상황이 발생할 수 있습니다.

* 첫 번째 인덱서 실행은 2019 년 6 월 1 일 또는 오전 8:00 UTC에 시작 됩니다. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
* 두 번째 실행은 6 월 1 일 또는 2019 9:00 오전에 시작 됩니다. 이 실행에는 1 시간 이상 70 분이 소요 되는 것으로 가정 하 고 10:10 AM UTC까지 완료 되지 않습니다.
* 세 번째 실행은 오전 10:00 시에 시작 되도록 예약 되어 있지만이 시점에서 이전 실행은 계속 실행 됩니다. 그런 다음이 예약 된 실행을 건너뜁니다. 다음 인덱서 실행은 오전 11:00에서 오전까지 시작 되지 않습니다.

> [!NOTE]
> 인덱서가 특정 일정으로 설정 되어 있지만 매번 같은 문서에서 반복적으로 실패 하는 경우 인덱서는 성공적으로 다시 진행 될 때까지 빈도가 낮은 간격으로 실행을 시작 합니다 (최대 24 시간 마다 한 번 이상). 기본 문제를 해결 한 것으로 생각 되는 경우 인덱서를 수동으로 실행할 수 있습니다. 인덱싱이 성공 하면 인덱서가 정기 일정으로 돌아옵니다.

## <a name="schedule-using-rest"></a>REST를 사용 하 여 예약

인덱서를 만들거나 업데이트할 때 **schedule** 속성을 지정 합니다.

```http
    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2020-06-30
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2021-01-01T00:00:00Z" }
    }
```

## <a name="schedule-using-net"></a>.NET을 사용 하 여 예약

다음 c # 예제에서는 미리 정의 된 데이터 원본 및 인덱스를 사용 하 여 Azure SQL database 인덱서를 만들고 지금 시작 하 여 매일 한 번씩 실행 되도록 일정을 설정 합니다.

```csharp
var schedule = new IndexingSchedule(TimeSpan.FromDays(1))
{
    StartTime = DateTimeOffset.Now
};

var indexer = new SearchIndexer("hotels-sql-idxr", dataSource.Name, searchIndex.Name)
{
    Description = "Data indexer",
    Schedule = schedule
};

await indexerClient.CreateOrUpdateIndexerAsync(indexer);
```

이 일정은 [Searchindexerclient](/dotnet/api/azure.search.documents.indexes.searchindexerclient)를 사용 하 여 인덱서를 만들거나 업데이트할 때 [indexingschedule](/dotnet/api/azure.search.documents.indexes.models.indexingschedule) 클래스를 사용 하 여 정의 됩니다. **Indexingschedule** 생성자에는 **TimeSpan** 개체를 사용 하 여 지정 된 **Interval** 매개 변수가 필요 합니다. 앞에서 설명한 것 처럼 허용 되는 가장 작은 간격 값은 5 분이 고 가장 큰 값은 24 시간입니다. **DateTimeOffset** 개체로 지정 된 두 번째 **StartTime** 매개 변수는 선택 사항입니다.

## <a name="next-steps"></a>다음 단계

일정에 따라 실행 되는 인덱서의 경우 검색 서비스에서 상태를 검색 하 여 작업을 모니터링 하거나 진단 로깅을 사용 하도록 설정 하 여 자세한 정보를 얻을 수 있습니다.

* [검색 인덱서 상태 모니터링](search-howto-monitor-indexers.md)
* [로그 데이터 수집 및 분석](search-monitor-logs.md)