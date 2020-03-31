---
title: 인덱서 실행 예약
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 인덱서를 예약하여 주기적으로 또는 특정 시간에 콘텐츠를 인덱싱합니다.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 72326413d463d449d339b1f3fd241ba2c27b4b6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112952"
---
# <a name="how-to-schedule-indexers-in-azure-cognitive-search"></a>Azure 인지 검색에서 인덱서를 예약하는 방법

인덱서가 생성된 직후에 일반적으로 한 번 실행됩니다. 포털, REST API 또는 .NET SDK를 사용하여 필요에 따라 다시 실행할 수 있습니다. 일정에 따라 주기적으로 실행되도록 인덱서를 구성할 수도 있습니다.

인덱서 스케줄링이 유용한 몇 가지 상황:

* 원본 데이터는 시간이 지남에 따라 변경되며 Azure Cognitive Search 인덱서가 변경된 데이터를 자동으로 처리하려고 합니다.
* 인덱스는 여러 데이터 원본에서 채워지며 인덱서가 서로 다른 시간에 실행되도록 하여 충돌을 줄이려고 합니다.
* 원본 데이터가 매우 크며 시간이 지남에 따라 인덱서 처리를 분산하려고 합니다. 대용량 데이터를 인덱싱하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에서 대용량 데이터 집합을 인덱싱하는 방법을](search-howto-large-index.md)참조하세요.

스케줄러는 Azure 인지 검색의 기본 제공 기능입니다. 외부 스케줄러를 사용하여 검색 인덱서를 제어할 수 없습니다.

## <a name="define-schedule-properties"></a>일정 속성 정의

인덱서 일정에는 두 가지 속성이 있습니다.
* **간격은**예약된 인덱서 실행 사이의 시간을 정의합니다. 허용되는 가장 작은 간격은 5분이고 가장 큰 간격은 24시간입니다.
* **인덱서를**실행해야 하는 첫 번째 시간을 나타냅니다.

인덱서를 처음 만들 때 또는 나중에 인덱서의 속성을 업데이트하여 일정을 지정할 수 있습니다. 인덱서 일정은 [포털,](#portal)REST [API](#restApi)또는 [.NET SDK를](#dotNetSdk)사용하여 설정할 수 있습니다.

인덱서의 실행은 한 번에 하나만 실행할 수 있습니다. 인덱서가 다음 실행이 예약될 때 이미 실행 중인 경우 해당 실행은 다음 예약된 시간까지 연기됩니다.

좀 더 구체적인 예제를 살펴보겠습니다. 2019년 6월 1일 오전 8:00:00 UTC에서 **시간간격및** **시작 시간으로** 인덱서 일정을 구성한다고 가정합니다. 인덱서 실행시간이 1시간 이상 소요될 때 발생할 수 있는 일은 다음과 같습니다.

* 첫 번째 인덱서 실행은 2019년 6월 1일 또는 오전 8:00 UTC에서 시작됩니다. 이 실행에 20분(또는 1시간 미만의 기간)이 걸리는 것으로 가정합니다.
* 두 번째 실행은 2019년 6월 1일 또는 그 무렵에 시작됩니다(오전 9:00) 이 실행은 1시간 이상 70분이 걸리며 오전 10시 10분 UTC까지 완료되지 않는다고 가정합니다.
* 세 번째 실행은 UTC 오전 10:00에 시작하도록 예약되지만 이 때 이전 실행은 여전히 실행 중입니다. 그런 다음 이 예약된 실행을 건너뜁니다. 인덱서의 다음 실행은 UTC 오전 11:00까지 시작되지 않습니다.

> [!NOTE]
> 인덱서가 특정 일정으로 설정되어 있지만 실행될 때마다 동일한 문서에서 반복적으로 반복적으로 실패하는 경우 인덱서가 성공적으로 진행될 때까지 덜 빈번한 간격(최대 24시간마다 최대 한 번)으로 실행되기 시작합니다. 다시.  인덱서가 특정 시점에 문제가 발생해도 문제가 해결되었다고 생각되면 인덱서의 온디맨드 실행을 수행할 수 있으며 성공적으로 진행되면 인덱서가 설정된 일정 간격으로 다시 돌아갑니다.

<a name="portal"></a>

## <a name="schedule-in-the-portal"></a>포털에서 일정

포털의 데이터 가져오기 마법사를 사용하면 생성 시 인덱서의 일정을 정의할 수 있습니다. 기본 일정 설정은 **시간별**설정으로, 인덱서가 생성된 후 한 번 실행되고 그 후 매시간 다시 실행됩니다.

인덱서가 자동으로 다시 실행되지 않도록 하려면 일정 설정을 **한 번으로** 변경하거나 하루에 한 번 실행하도록 **매일로** 변경할 수 있습니다. 다른 간격 또는 특정 향후 시작 시간을 지정하려는 경우 **사용자 지정으로** 설정합니다.

일정을 **사용자 지정으로**설정하면 **간격** 및 **시작 시간(UTC)을**지정할 수 있는 필드가 나타납니다. 허용되는 가장 짧은 시간 간격은 5분이고 가장 긴 시간은 1440분(24시간)입니다.

   ![데이터 가져오기 마법사에서 인덱서 일정 설정](media/search-howto-schedule-indexers/schedule-import-data.png "데이터 가져오기 마법사에서 인덱서 일정 설정")

인덱서를 만든 후 인덱서의 편집 패널을 사용하여 일정 설정을 변경할 수 있습니다. 일정 필드는 데이터 가져오기 마법사와 동일합니다.

   ![인덱서 편집 패널에서 일정 설정](media/search-howto-schedule-indexers/schedule-edit.png "인덱서 편집 패널에서 일정 설정")

<a name="restApi"></a>

## <a name="schedule-using-rest-apis"></a>REST API 사용 일정

REST API를 사용하여 인덱서의 일정을 정의할 수 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **일정** 속성을 포함합니다. 아래 예제에서는 기존 인덱서를 업데이트하기 위한 PUT 요청을 보여 주며,

    PUT https://myservice.search.windows.net/indexers/myindexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: admin-key

    {
        "dataSourceName" : "myazuresqldatasource",
        "targetIndexName" : "target index name",
        "schedule" : { "interval" : "PT10M", "startTime" : "2015-01-01T00:00:00Z" }
    }

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

선택적 **startTime** 예약된 실행을 시작해야 하는 시기를 나타냅니다. 생략한 경우 현재 UTC 시간이 사용됩니다. 이 시간은 과거에 있을 수 있으며, 이 경우 첫 번째 실행은 원래 **startTime**이후 인덱서가 계속 실행된 것처럼 예약됩니다.

또한 Run Indexer 호출을 사용하여 언제든지 필요에 따라 인덱서를 실행할 수도 있습니다. 인덱서 실행 및 인덱서 일정 설정에 대한 자세한 내용은 REST API 참조에서 [인덱서 실행,](https://docs.microsoft.com/rest/api/searchservice/run-indexer) [인덱서 받기](https://docs.microsoft.com/rest/api/searchservice/get-indexer)및 [업데이트 인덱서를](https://docs.microsoft.com/rest/api/searchservice/update-indexer) 참조하십시오.

<a name="dotNetSdk"></a>

## <a name="schedule-using-the-net-sdk"></a>.NET SDK를 사용하여 일정

Azure 인지 검색 .NET SDK를 사용하여 인덱서의 일정을 정의할 수 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **일정** 속성을 포함합니다.

다음 C# 예제에서는 미리 정의된 데이터 원본 및 인덱스를 사용하여 인덱서를 만들고 지금부터 30분부터 매일 한 번씩 실행되도록 일정을 설정합니다.

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
**일정** 매개 변수가 생략된 경우 인덱서가 생성된 직후에 한 번만 실행됩니다.

**startTime** 매개 변수는 과거의 시간으로 설정할 수 있습니다. 이 경우 첫 번째 실행은 지정된 **startTime**이후 인덱서가 계속 실행되는 것처럼 예약됩니다.

일정은 [인덱싱일](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingschedule?view=azure-dotnet) 클래스를 사용하여 정의됩니다. **인덱싱일정** 생성자는 **TimeSpan** 개체를 사용하여 지정된 **간격** 매개 변수가 필요합니다. 허용되는 가장 작은 간격 값은 5분이고 가장 큰 간격값은 24시간입니다. **DateTimeOffset** 개체로 지정된 두 번째 **startTime** 매개 변수는 선택 사항입니다.

.NET SDK를 사용하면 **IIndexersOperations** 인터페이스에서 메서드를 구현하는 [SearchServiceClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient) 클래스 및 해당 [인덱서](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient.indexers) 속성을 사용하여 인덱서 작업을 제어할 수 있습니다. 

[실행,](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.run) [RunAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.indexersoperationsextensions.runasync)또는 [RunWithHttpMessagesAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations.runwithhttpmessagesasync) 메서드 중 하나를 사용하여 언제든지 필요에 따라 인덱서를 실행할 수 있습니다.

인덱서 생성, 업데이트 및 실행에 대한 자세한 내용은 [IIindexersOperations](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations?view=azure-dotnet)을 참조하십시오.
