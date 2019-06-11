---
title: 인덱서-Azure Search를 예약 하는 방법
description: 정기적으로 또는 특정 시간에 인덱스 콘텐츠를 Azure Search 인덱서를 예약 합니다.
ms.date: 05/31/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 764fca8d3cb4cd9c40d7880043637f89ef1a8578
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755382"
---
# <a name="how-to-schedule-indexers-for-azure-search"></a>Azure search 인덱서를 예약 하는 방법
일반적으로 인덱서를 만든 후에 즉시 한 번 실행 합니다. 포털, REST API 또는.NET SDK를 사용 하 여 요청 시 해당 작업을 다시 실행할 수 있습니다. 또한 일정에 따라 주기적으로 실행 하기 위한 인덱서를 구성할 수 있습니다.

인덱서 일정은 유용한 일부 상황:

* 원본 데이터는 시간이 지남에 따라 변경 하 고 자동으로 변경된 된 데이터를 처리 하는 데 Azure Search 인덱서를 하려는 키를 누릅니다.
* 인덱스에 여러 데이터 원본에서 채워지고 충돌을 줄이고 서로 다른 시간에 인덱서를 실행 하는지 확인 하려면.
* 원본 데이터는 매우 큰 및 처리 시간에 따라 인덱서를 확산 하려는 합니다. 많은 양의 데이터를 인덱싱에 대 한 자세한 내용은 참조 하세요. [Azure Search에서 큰 데이터 집합을 인덱싱하는 방법](search-howto-large-index.md)합니다.

스케줄러에는 Azure Search의 기본 제공 기능입니다. 검색 인덱서를 제어 하는 외부 스케줄러를 사용할 수 없습니다.

## <a name="define-schedule-properties"></a>일정 속성 정의

일정을 인덱서는 다음과 같은 두 개의 속성이 있습니다.
* **간격**를 정의 하는 시간 사이 인덱서 실행을 예약 합니다. 허용 된 가장 작은 간격은 5 분 이며 최대 24 시간입니다.
* **시작 시간 (UTC)** , 인덱서를 실행 해야 하는 처음으로 나타냅니다.

인덱서를 처음 만들 때 또는 나중에 인덱서 속성을 업데이트 하 여 일정을 지정할 수 있습니다. 사용 하 여 인덱서 일정을 설정할 수 있습니다 합니다 [포털](#portal)의 [REST API](#restApi), 또는 [.NET SDK](#dotNetSdk)합니다.

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 인덱서가 이미 실행 중인 예약 된 다음 실행 하는 경우 해당 실행은 다음 예약 시간까지 연기 됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 사용 하 여 인덱서 일정을 구성 하는 가정를 **간격** 매시간의 및 **시작 시간** 오전 8 시 (UTC)에 2019 년 6 월 1 년입니다. 인덱서 실행 시간 보다 오래 걸리는 경우 발생할 수 있는 다음과 같습니다.

* 첫 번째 인덱서 실행 2019 년 6 월 1 년 8:00 AM UTC 시경에 시작합니다. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
* 두 번째 실행 시작 2019 년 6 월 1, 오전 9:00 시경 UTC입니다. 이 실행 되는 70 분-1 시간 넘게-오전 10시 10분 UTC까지 완료 되지 것입니다 만든다고 가정 합니다.
* 세 번째 실행 오전 10시 UTC에 시작 되도록 설정 되어 있지만 시점 이전에 실행 한 계속 실행 합니다. 이 예약 된 실행을 건너뜀 합니다. 다음 인덱서 실행은 오전 11시 UTC까지 시작 되지 않습니다.

<a name="portal"></a>

## <a name="define-a-schedule-in-the-portal"></a>포털에서 일정을 정의 합니다.

포털에서 데이터 가져오기 마법사를 만들 때 인덱서에 대 한 일정을 정의할 수 있습니다. 기본 일정 설정은 **시간별**, 만들어진 후 다시 1 시간 마다 실행 나중에 한 번만 실행 인덱서를 의미 하는 합니다.

일정 설정을 변경할 수 있습니다 **되 면** 자동으로 다시 실행 되도록 인덱서를 하지 못하도록 하려는 경우 또는 **일일** 하루에 한 번 실행 합니다. 설정할 **사용자 지정** 다른 간격 또는 미래의 특정 시작 시간을 지정 하려는 경우.

일정 설정 하면 **사용자 지정**, 필드를 지정할 수 있도록 표시를 **간격** 하며 **시작 시간 (UTC)** 합니다. 허용 된 간격은 5 분이 고 가장 긴 시간이 짧은 경우 1440 분 (24 시간)

   ![데이터 가져오기 마법사에서 설정 인덱서 일정](media/search-howto-schedule-indexers/schedule-import-data.png "데이터 가져오기 마법사에서 설정 인덱서 일정")

인덱서를 만든 후에 인덱서 편집 패널을 사용 하 여 일정 설정을 변경할 수 있습니다. 일정 필드는 데이터 가져오기 마법사에서와 동일 합니다.

   ![인덱서 편집 패널에서 일정을 설정](media/search-howto-schedule-indexers/schedule-edit.png "인덱서 편집 패널에서 일정을 설정 합니다.")

<a name="restApi"></a>

## <a name="define-a-schedule-using-the-rest-api"></a>REST API를 사용 하 여 일정 정의

REST API를 사용 하 여 인덱서에 대 한 일정을 정의할 수 있습니다. 이 작업을 수행 하려면 포함 된 **일정** 속성 인덱서를 업데이트할 때. 아래 예제에서는 기존 인덱서를 업데이트 하는 PUT 요청을 보여 줍니다.

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

선택적 **startTime** 예약된 실행을 시작할 때를 나타냅니다. 생략한 경우 현재 UTC 시간이 사용됩니다. 이 시간이 과거에는 원래 이후에 지속적으로 인덱서를 실행 하는 경우에 따라 경우 첫 번째 실행은 예약 될 수 있습니다 **startTime**합니다.

또한 언제 든 지 인덱서 실행 호출을 사용 하 여 필요에 따라 인덱서를 실행할 수 있습니다. 인덱서를 실행 하 고 인덱서 일정을 설정 하는 방법에 대 한 자세한 내용은 참조 하세요. [인덱서 실행](https://docs.microsoft.com/rest/api/searchservice/run-indexer)를 [Get Indexer](https://docs.microsoft.com/rest/api/searchservice/get-indexer), 및 [인덱서 업데이트](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 는 REST API 참조.

<a name="dotNetSdk"></a>

## <a name="define-a-schedule-using-the-net-sdk"></a>.NET SDK를 사용 하 여 일정 정의

Azure Search.NET SDK를 사용 하 여 인덱서에 대 한 일정을 정의할 수 있습니다. 이 작업을 수행 하려면 포함 된 **일정** 속성 인덱서를 업데이트할 때.

다음 C# 30 분 지금부터 시작 매일 한 번 실행 되도록 일정을 설정 하는 예제는 미리 정의 된 데이터 원본 및 인덱스를 사용 하 여 인덱서를 만듭니다.

```
    Indexer indexer = new Indexer(
        name: "azure-sql-indexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        schedule: new IndexingSchedule(
                        TimeSpan.FromDays(1), 
                        new DateTimeOffset(DateTime.UtcNow.AddMinutes(30))
                    )
        );
    await searchService.Indexers.CreateOrUpdateAsync(indexer);
```
경우는 **일정** 인덱서는 한 번만 실행 만들어진 후에 즉시, 매개 변수를 생략 합니다.

합니다 **startTime** 과거의 시간까지 매개 변수를 설정할 수 있습니다. 이후에 지속적으로 인덱서를 실행 하는 경우에 따라 첫 번째 실행은 예약 하는 경우는 주어진 **startTime**합니다.

일정을 사용 하 여 정의 되는 [IndexingSchedule](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) 클래스입니다. **IndexingSchedule** 생성자를 사용 하려면를 **간격** 사용 하 여 지정 된 매개 변수를 **TimeSpan** 개체입니다. 간격이 허용 되는 최소값은 5 분 이며 최대 24 시간입니다. 두 번째 **startTime** 로 지정 된 매개 변수를 **DateTimeOffset** 개체, 선택 사항입니다.

.NET SDK를 사용 하 여 컨트롤 인덱서 작업을 수 있습니다 합니다 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) 클래스 및 해당 [인덱서](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) 메서드를 구현 하는 속성을 **IIndexersOperations**인터페이스입니다. 

중 하나를 사용 하 여 언제 든 필요에 따라 인덱서를 실행할 수 있습니다 합니다 [실행할](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run), [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync), 또는 [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) 메서드.

만드는 방법에 대 한 자세한 내용은 업데이트 및 인덱서 실행 참조 [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)합니다.
