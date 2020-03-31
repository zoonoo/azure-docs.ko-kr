---
title: 인덱서 상태 및 결과 모니터링
titleSuffix: Azure Cognitive Search
description: REST API 또는 .NET SDK를 사용하여 Azure Portal에서 Azure Cognitive Search 인덱서의 상태, 진행 률 및 결과를 모니터링합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 699b5a4e5a7f10c883667ca5030dd971855467f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74112986"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Azure 인지 검색 인덱서 상태 및 결과를 모니터링하는 방법

Azure Cognitive Search는 모든 인덱서의 현재 및 과거 실행에 대한 상태 및 모니터링 정보를 제공합니다.

인덱서 모니터링은 다음을 수행할 때 유용합니다.

* 진행 중인 실행 중에 인덱서의 진행 률을 추적합니다.
* 진행 중인 또는 이전 인덱서 실행 결과를 검토합니다.
* 최상위 인덱서 오류 및 인덱싱되는 개별 문서에 대한 오류 또는 경고를 식별합니다.

## <a name="get-status-and-history"></a>상태 및 기록 받기

인덱서 모니터링 정보에 다음과 같은 다양한 방법으로 액세스할 수 있습니다.

* Azure [포털에서](#portal)
* 나머지 [API](#restapi) 사용
* [.NET SDK](#dotnetsdk) 사용

사용 가능한 인덱서 모니터링 정보에는 다음이 모두 포함됩니다(데이터 형식은 사용된 액세스 방법에 따라 다릅니다).

* 인덱서 자체에 대한 상태 정보
* 상태, 시작 및 종료 시간, 자세한 오류 및 경고를 포함하여 인덱서의 최신 실행에 대한 정보입니다.
* 기록 인덱서 실행 목록 및 해당 상태, 결과, 오류 및 경고입니다.

대용량 데이터를 처리하는 인덱서를 실행하는 데 시간이 오래 걸릴 수 있습니다. 예를 들어 수백만 개의 원본 문서를 처리하는 인덱서가 24시간 동안 실행한 다음 거의 즉시 다시 시작할 수 있습니다. 대용량 인덱서의 상태는 항상 포털에서 **진행 중이라고** 말할 수 있습니다. 인덱서가 실행되는 경우에도 진행 중인 진행 률 및 이전 실행에 대한 세부 정보를 사용할 수 있습니다.

<a name="portal"></a>

## <a name="monitor-using-the-portal"></a>포털을 사용하여 모니터링

검색 서비스 개요 페이지에서 **인덱서** 목록에서 모든 인덱서의 현재 상태를 확인할 수 있습니다.

   ![인덱서 목록](media/search-monitor-indexers/indexers-list.png "인덱서 목록")

인덱서가 실행 중일 때 목록의 상태가 **진행 중임을**표시하고 문서 성공 값은 지금까지 처리된 문서 수를 **표시합니다.** 포털에서 인덱서 상태 값 및 문서 수를 업데이트하는 데 몇 분 정도 걸릴 수 있습니다.

가장 최근에 실행이 성공한 인덱서가 **성공을**보여줍니다. 개별 문서에 오류가 있는 경우에도 인덱서 실행이 성공할 수 있습니다., 오류 수가 인덱서의 **최대 실패 항목** 설정 보다 작으면.

가장 최근 실행이 오류로 끝나면 상태가 실패 를 **표시합니다.** **재설정** 상태는 인덱서의 변경 추적 상태가 재설정됨을 의미합니다.

인덱서의 현재 및 최근 실행에 대한 자세한 내용을 보려면 목록에서 인덱서를 클릭합니다.

   ![인덱서 요약 및 실행 기록](media/search-monitor-indexers/indexer-summary.png "인덱서 요약 및 실행 기록")

**인덱서 요약** 차트에는 가장 최근 실행에서 처리된 문서 수의 그래프가 표시됩니다.

**실행 세부 정보** 목록에는 가장 최근의 실행 결과가 최대 50개까지 표시됩니다.

목록에서 실행 결과를 클릭하여 해당 실행에 대한 세부 사항을 확인합니다. 여기에는 시작 및 종료 시간과 발생한 오류 및 경고가 포함됩니다.

   ![인덱서 실행 세부 정보](media/search-monitor-indexers/indexer-execution.png "인덱서 실행 세부 정보")

실행 중에 문서별 문제가 있는 경우 오류 및 경고 필드에 나열됩니다.

   ![오류와 인덱서 세부 정보](media/search-monitor-indexers/indexer-execution-error.png "오류와 인덱서 세부 정보")

경고는 일부 인덱서에서 일반적이며 항상 문제를 나타내는 것은 아닙니다. 예를 들어 인지 서비스를 사용하는 인덱서에는 이미지 또는 PDF 파일에 처리할 텍스트가 없을 때 경고를 보고할 수 있습니다.

인덱서 오류 및 경고 조사에 대한 자세한 내용은 [Azure Cognitive Search에서 일반적인 인덱서 문제 해결을](search-indexer-troubleshooting.md)참조하세요.

<a name="restapi"></a>

## <a name="monitor-using-rest-apis"></a>REST API를 사용하여 모니터링

[인덱서](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)상태 받기 명령을 사용하여 인덱서의 상태 및 실행 기록을 검색할 수 있습니다.

    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

응답에는 전반적인 인덱서 상태, 마지막(또는 진행 중인) 인덱서 호출 및 최근 인덱서 호출 기록이 포함됩니다.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

실행 기록에는 역순으로 정렬되는 가장 최근 실행 50개까지 포함됩니다(가장 최근의 첫 번째 실행).

두 가지 상태 값이 서로 다릅니다. 최상위 상태는 인덱서 자체에 대한 것입니다. **실행** 중인 인덱서 상태는 인덱서가 올바르게 설정되어 실행할 수 있지만 현재 실행 중이 아님을 의미합니다.

또한 인덱서의 각 실행은 특정 실행이 진행 중인지(실행**중인지)** **성공,** **일시 변환 실패**또는 영구 **실패** 상태로 이미 완료되었는지 여부를 나타내는 자체 상태를 가있습니다. 

인덱서가 변경 정보 추적 상태를 새로 고치도록 재설정되면 **재설정** 상태와 함께 별도의 실행 기록 항목이 추가됩니다.

상태 코드 및 인덱서 모니터링 데이터에 대한 자세한 내용은 [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)를 참조하십시오.

<a name="dotnetsdk"></a>

## <a name="monitor-using-the-net-sdk"></a>.NET SDK를 사용하여 모니터링

Azure 인지 검색 .NET SDK를 사용하여 인덱서의 일정을 정의할 수 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **일정** 속성을 포함합니다.

다음 C# 예제에서는 인덱서의 상태와 콘솔에 대한 가장 최근(또는 진행 중인) 실행 결과에 대한 정보를 기록합니다.

```csharp
static void CheckIndexerStatus(Indexer indexer, SearchServiceClient searchService)
{
    try
    {
        IndexerExecutionInfo execInfo = searchService.Indexers.GetStatus(indexer.Name);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("  Run Status: {0}", result.Status.ToString());
        Console.WriteLine("  Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("  StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("  ErrorMessage: {0}", errorMsg);
        Console.WriteLine("  Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

콘솔의 출력은 다음과 같습니다.

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

두 가지 상태 값이 서로 다릅니다. 최상위 상태는 인덱서 자체의 상태입니다. **실행의** 인덱서 상태는 인덱서가 올바르게 설정되어 실행에 사용할 수 있지만 현재 실행 중이 아님을 의미합니다.

인덱서의 각 실행은 특정 실행이 진행 중인지(실행**중)** 또는 **성공** 또는 **TransientError** 상태로 이미 완료되었는지 여부에 대한 자체 상태를 가있습니다. 

인덱서가 변경 정보 추적 상태를 새로 고치도록 재설정되면 **재설정** 상태와 함께 별도의 기록 항목이 추가됩니다.

상태 코드 및 인덱서 모니터링 정보에 대한 자세한 내용은 REST API의 [GetIndexerStatus를](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) 참조하십시오.

문서별 오류 또는 경고에 대한 세부 정보는 목록 `IndexerExecutionResult.Errors` 및 `IndexerExecutionResult.Warnings`을 열거하여 검색할 수 있습니다.

인덱서를 모니터링하는 데 사용되는 .NET SDK 클래스에 대한 자세한 내용은 [인덱서 실행정보](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) 및 [인덱서 실행결과](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)를 참조하십시오.
