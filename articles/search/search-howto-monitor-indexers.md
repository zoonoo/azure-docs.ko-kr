---
title: 인덱서 상태 및 결과 모니터링
titleSuffix: Azure Cognitive Search
description: REST API 또는 .NET SDK를 사용 하 여 Azure Portal에서 Azure Cognitive Search 인덱서의 상태, 진행률 및 결과를 모니터링 합니다.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/28/2021
ms.openlocfilehash: a94720e6b84821d53a3bfdcbdce249390078940f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "99063266"
---
# <a name="how-to-monitor-azure-cognitive-search-indexer-status-and-results"></a>Azure Cognitive Search 인덱서 상태 및 결과를 모니터링 하는 방법

Azure Portal에서 또는 REST 호출 또는 Azure SDK를 통해 프로그래밍 방식으로 인덱서 처리를 모니터링할 수 있습니다. 인덱서 자체에 대 한 상태 외에도 특정 실행의 시작 및 종료 시간, 자세한 오류 및 경고를 검토할 수 있습니다.

## <a name="monitor-using-azure-portal"></a>Azure Portal 사용 모니터링

검색 서비스 개요 페이지에서 모든 인덱서의 현재 상태를 볼 수 있습니다. 포털 페이지를 몇 분 마다 새로 고치면 새 인덱서 실행에 대 한 증거는 표시 되지 않습니다.

   ![인덱서 목록](media/search-monitor-indexers/indexers-list.png "인덱서 목록")

| 상태 | 설명 |
|--------|-------------|
| **진행 중** | 활성 실행을 나타냅니다. 포털에서 부분 정보를 보고 합니다. 인덱싱이 진행 됨에 따라 성공 값이 응답으로 증가 하는 **문서** 를 볼 수 있습니다. 대량의 데이터를 처리 하는 인덱서를 실행 하는 데 시간이 오래 걸릴 수 있습니다. 예를 들어 수백만 개의 소스 문서를 처리 하는 인덱서는 24 시간 동안 실행 된 다음 거의 즉시 다시 시작할 수 있습니다. 고용량 인덱서의 상태는 항상 포털에서 **진행** 중 이라고 말할 수 있습니다. 인덱서를 실행 하는 경우에도 진행 중인 진행 상황과 이전 실행에 대 한 세부 정보를 사용할 수 있습니다. |
| **Success** | 실행이 성공 했음을 나타냅니다. 오류 수가 인덱서의 **최대 실패 항목** 설정 보다 적으면 개별 문서에 오류가 있어도 인덱서 실행이 성공할 수 있습니다. |
| **실패** | 오류가 발생 한 **최대 항목** 수를 초과 하 여 인덱싱이 중지 되었습니다. |
| **재설정** | 인덱서의 내부 변경 내용 추적 상태가 다시 설정 되었습니다. 인덱서는 모든 문서를 새로 고치는 것이 아니라 최신 타임 스탬프를 사용 하는 모든 문서를 새로 고치는 전체에서 실행 됩니다. |

목록의 인덱서를 클릭 하면 인덱서의 현재 실행 및 최근 실행에 대 한 자세한 정보를 볼 수 있습니다.

   ![인덱서 요약 및 실행 기록](media/search-monitor-indexers/indexer-summary.png "인덱서 요약 및 실행 기록")

**인덱서 요약** 차트는 가장 최근 실행에서 처리 된 문서 수의 그래프를 표시 합니다.

**실행 정보** 목록에는 가장 최근 실행 결과가 50으로 표시 됩니다. 목록에서 실행 결과를 클릭 하 여 해당 실행에 대 한 세부 정보를 확인 합니다. 여기에는 시작 시간과 종료 시간, 발생 한 오류 및 경고가 포함 됩니다.

   ![인덱서 실행 세부 정보](media/search-monitor-indexers/indexer-execution.png "인덱서 실행 세부 정보")

실행 중에 문서 관련 문제가 발생 하는 경우 오류 및 경고 필드에 나열 됩니다.

   ![오류가 있는 인덱서 세부 정보](media/search-monitor-indexers/indexer-execution-error.png "오류가 있는 인덱서 세부 정보")

경고는 일부 인덱서 유형에 공통적으로 사용 되며 항상 문제를 나타내지는 않습니다. 예를 들어 인식 서비스를 사용 하는 인덱서는 이미지 또는 PDF 파일에 처리할 텍스트가 포함 되지 않은 경우 경고를 보고할 수 있습니다. 

인덱서 오류 및 경고를 조사 하는 방법에 대 한 자세한 내용은 [Azure Cognitive Search의 일반적인 인덱서 문제 해결](search-indexer-troubleshooting.md)을 참조 하세요.

## <a name="monitor-using-get-indexer-status-rest-api"></a>인덱서 상태 가져오기 (REST API)를 사용 하 여 모니터링

인덱서 [상태 가져오기 명령을](/rest/api/searchservice/get-indexer-status)사용 하 여 인덱서의 상태 및 실행 기록을 검색할 수 있습니다.

```http
GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2020-06-30
api-key: [Search service admin key]
```

응답에는 전반적인 인덱서 상태, 마지막(또는 진행 중인) 인덱서 호출 및 최근 인덱서 호출 기록이 포함됩니다.

```output
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
```

실행 기록에는 최대 50의 최근 실행이 포함 되며,이는 역순 (가장 최근 first) 순서로 정렬 됩니다.

두 가지 상태 값이 있습니다. 최상위 수준 상태는 인덱서 자체에 대 한 것입니다. 인덱서 상태를 **실행** 하는 것은 인덱서가 올바르게 설정 되어 실행 가능 하지만 현재 실행 되 고 있지 않음을 의미 합니다.

각 인덱서의 실행에는 특정 실행이 진행 중인지 (**실행 중**) 또는 이미 **성공**, **transientFailure** 또는 **persistentFailure** 상태로 완료 되었는지 여부를 나타내는 자체 상태도 있습니다. 

변경 내용 추적 상태를 새로 고치기 위해 인덱서를 다시 설정 하면 **다시 설정** 상태를 사용 하 여 별도의 실행 기록 항목이 추가 됩니다.

상태 코드 및 인덱서 모니터링 데이터에 대 한 자세한 내용은 [인덱서 상태 가져오기](/rest/api/searchservice/get-indexer-status)를 참조 하세요.

## <a name="monitor-using-net"></a>.NET을 사용 하 여 모니터링

다음 c # 예제에서는 Azure Cognitive Search .NET SDK를 사용 하 여 인덱서 상태 및 가장 최근에 실행 된 실행의 결과에 대 한 정보를 콘솔에 씁니다.

```csharp
static void CheckIndexerStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        string indexerName = "hotels-sql-idxr";
        SearchIndexerStatus execInfo = indexerClient.GetIndexerStatus(indexerName);

        Console.WriteLine("Indexer has run {0} times.", execInfo.ExecutionHistory.Count);
        Console.WriteLine("Indexer Status: " + execInfo.Status.ToString());

        IndexerExecutionResult result = execInfo.LastResult;

        Console.WriteLine("Latest run");
        Console.WriteLine("Run Status: {0}", result.Status.ToString());
        Console.WriteLine("Total Documents: {0}, Failed: {1}", result.ItemCount, result.FailedItemCount);

        TimeSpan elapsed = result.EndTime.Value - result.StartTime.Value;
        Console.WriteLine("StartTime: {0:T}, EndTime: {1:T}, Elapsed: {2:t}", result.StartTime.Value, result.EndTime.Value, elapsed);

        string errorMsg = (result.ErrorMessage == null) ? "none" : result.ErrorMessage;
        Console.WriteLine("ErrorMessage: {0}", errorMsg);
        Console.WriteLine(" Document Errors: {0}, Warnings: {1}\n", result.Errors.Count, result.Warnings.Count);
    }
    catch (Exception e)
    {
        // Handle exception
    }
}
```

콘솔의 출력은 다음과 같습니다.

```output
Indexer has run 18 times.
Indexer Status: Running
Latest run
  Run Status: Success
  Total Documents: 7, Failed: 0
  StartTime: 11:29:31 PM, EndTime: 11:29:31 PM, Elapsed: 00:00:00.2560000
  ErrorMessage: none
  Document Errors: 0, Warnings: 0
```

두 가지 상태 값이 있습니다. 최상위 상태는 인덱서 자체의 상태입니다. 인덱서 상태를 **실행** 하는 것은 인덱서가 올바르게 설정 되어 실행 가능 하지만 현재 실행 되 고 있지 않음을 의미 합니다.

각 인덱서의 실행에는 특정 실행이 진행 중인지 (**실행 중**) 또는 이미 **성공** 또는 **TransientError** 상태를 사용 하 여 완료 되었는지에 대 한 자체 상태도 있습니다. 

변경 내용 추적 상태를 새로 고치기 위해 인덱서를 다시 설정 하면 **다시 설정** 상태를 사용 하 여 별도의 기록 항목이 추가 됩니다.

## <a name="next-steps"></a>다음 단계

상태 코드 및 인덱서 모니터링 정보에 대 한 자세한 내용은 다음 API 참조를 참조 하세요.

* [GetIndexerStatus (REST API)](/rest/api/searchservice/get-indexer-status)
* [IndexerStatus](/dotnet/api/azure.search.documents.indexes.models.indexerstatus)
* [IndexerExecutionStatus](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionstatus)
* [IndexerExecutionResult](/dotnet/api/azure.search.documents.indexes.models.indexerexecutionresult)