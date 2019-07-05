---
title: 인덱서 상태 및 Azure Search 결과 모니터링 하는 방법
description: 상태, 진행률 및 결과 REST API 또는.NET SDK를 사용 하 여 Azure portal에서 Azure Search 인덱서를 모니터링 합니다.
ms.date: 06/28/2019
author: RobDixon22
manager: HeidiSteen
ms.author: v-rodixo
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 07b4fe2ef830c3ce09b655cf4b433d14923229a9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486286"
---
# <a name="how-to-monitor-azure-search-indexer-status-and-results"></a>Azure Search 인덱서 상태 및 결과 모니터링 하는 방법

Azure Search 상태 및 모니터링 모든 인덱서의 현재 및 기록 실행에 대 한 정보를 제공 합니다.

인덱서 모니터링 하려는 경우 유용 합니다.

* 진행 중인 동안 인덱서의 진행률 추적 실행 합니다.
* 진행 중이거나 이전 인덱서 실행의 결과 검토 합니다.
* 최상위 인덱서 확인 오류나 인덱싱되는 개별 문서에 대 한 경고 및 오류입니다.

## <a name="find-indexer-status-and-history-details"></a>인덱서 상태 및 기록 정보 찾기

인덱서 모니터링 정보를 포함 하 여 다양 한 방법으로 액세스할 수 있습니다.

* [Azure Portal](#portal)에서
* 사용 하 여 [REST API](#restapi)
* 사용 하 여 [.NET SDK](#dotnetsdk)

(형식을 다른 데이터 액세스 방법을 기반으로) 하는 경우 다음 모두를 포함 하는 정보를 모니터링 하는 사용 가능한 인덱서:

* 인덱서 자체에 대 한 상태 정보
* 최신 정보는 해당 상태, 시작 및 종료 시간 및 자세한 오류 및 경고를 포함 하 여 인덱서 실행 합니다.
* 기록 인덱서 실행 및 상태, 결과, 오류 및 경고의 목록입니다.

많은 양의 데이터를 처리 하는 인덱서 실행에 오랜 시간이 걸릴 수 있습니다. 예를 들어 수백만 개의 소스 문서를 처리 하는 인덱서는 24 시간 동안 실행 하 고 거의 즉시 다시 시작 수 있습니다. 고용량 인덱서에 대 한 상태 메시지가 항상 나타날 수 있습니다 **진행** 포털에서 합니다. 인덱서를 실행 하는 경우에 정보 진행 상태 및 이전에 실행 하는 방법에 대 한 사용할 수 있습니다.

<a name="portal"></a>

## <a name="monitor-indexers-in-the-portal"></a>포털의 모니터 인덱서

모든 인덱서에서의 현재 상태를 볼 수는 **인덱서** 검색 서비스 개요 페이지의 목록입니다.

   ![인덱서 목록을](media/search-monitor-indexers/indexers-list.png "인덱서 목록을")

인덱서 실행 될 때, 목록 표시에 상태 **진행**, 및 **Docs 성공** 값 지금까지 처리 된 문서 수를 보여 줍니다. 인덱서 상태 값을 업데이트 하려면 포털까지 몇 분 정도 걸릴 수 있습니다 및 문서 수를 계산 합니다.

가장 최근 실행 된 성공적으로 표시 된 인덱서의 **성공**합니다. 인덱서 실행 성공할 수 개별 문서에는 오류가 있는 경우에 오류 수가 인덱서의 보다 작으면 **최대 실패 항목** 설정 합니다.

가장 최근 실행 오류가 발생 하 여 종료 된 경우 상태 표시 **실패**합니다. 상태의 **재설정** 인덱서의 변경 내용 추적 상태를 다시 설정 했음을 의미 합니다.

인덱서의 현재 및 최근 인덱서의에 대 한 자세한 내용을 보려면 목록에서 클릭을 실행 합니다.

   ![인덱서 요약 및 실행 기록을](media/search-monitor-indexers/indexer-summary.png "인덱서 요약 및 실행 기록")

합니다 **인덱서 요약** 차트에는 가장 최근 실행에서 처리 된 문서 수의 그래프가 표시 됩니다.

합니다 **실행 세부 정보** 목록에서는 최대 50 개의 가장 최근 실행 결과 보여 줍니다.

실행 하는 방법에 대 한 세부 정보를 보려면 목록에서 실행 결과 클릭 합니다. 이 시작 및 종료 시간 및 모든 오류 및 발생 한 경고를 포함 합니다.

   ![인덱서 실행 세부 정보](media/search-monitor-indexers/indexer-execution.png "인덱서 실행 세부 정보")

문서 관련 문제가 있으면 실행 중, 오류 및 경고 필드에 나열 됩니다.

   ![인덱서 정보 오류로](media/search-monitor-indexers/indexer-execution-error.png "오류로 인덱서 세부 정보")

경고는 일반적으로 일부 유형의 인덱서 및 항상 문제를 나타내지 않습니다. 예를 들어 cognitive services를 사용 하는 인덱서는 이미지나 PDF 파일에서 처리 하는 데 모든 텍스트를 포함 하지 하는 경우 경고를 보고할 수 있습니다.

인덱서 오류 및 경고를 조사 하는 방법에 대 한 자세한 내용은 참조 하세요. [Azure Search에서 인덱서는 일반적인 문제 해결](search-indexer-troubleshooting.md)합니다.

<a name="restapi"></a>

## <a name="monitor-indexers-using-the-rest-api"></a>REST API를 사용 하 여 인덱서를 모니터링 합니다.

사용 하 여 인덱서 상태와 실행 기록을 검색할 수 있습니다 합니다 [인덱서 상태 가져오기 명령](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):

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

실행 기록은 50 가장 최근의 실행 시간의 역순 (가장 최근이 첫 번째) 정렬 되는 최대 포함 되어 있습니다.

여기에 메모는 두 가지 상태 값입니다. 인덱서 자체에 대 한 최상위 수준 상태가입니다. 인덱서 상태의 **실행** 인덱서 올바르게 설정 되어 있고 하지 있지만 실행할 수 있는 현재 의미를 실행 합니다.

인덱서가 실행 될 때마다 역시 해당 특정 실행 진행 중인 인지 여부를 나타내는 자체의 상태 (**실행**)를 사용 하 여 이미 완료 또는 **성공**, **transientFailure**, 또는 **persistentFailure** 상태입니다. 

별도 실행 기록 항목을 사용 하 여 항목이 해당 변경 내용 추적 상태를 새로 고치려면 인덱서 다시 설정 되는 **재설정** 상태입니다.

상태 코드 및 인덱서 데이터를 모니터링 하는 방법에 대 한 자세한 내용은 참조 하세요. [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status)합니다.

<a name="dotnetsdk"></a>

## <a name="monitor-indexers-using-the-net-sdk"></a>.NET SDK를 사용 하 여 인덱서를 모니터링 합니다.

Azure Search.NET SDK를 사용 하 여 인덱서에 대 한 일정을 정의할 수 있습니다. 이 작업을 수행 하려면 포함 된 **일정** 속성 인덱서를 업데이트할 때.

다음 C# 인덱서의 상태에 대 한 정보를 기록 하는 예제 및 콘솔에 결과 가장 최근 (또는 진행 중)를 실행 합니다.

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

콘솔의 출력을 다음과 같이 표시 됩니다.

    Indexer has run 18 times.
    Indexer Status: Running
    Latest run
      Run Status: Success
      Total Documents: 7, Failed: 0
      StartTime: 10:02:46 PM, EndTime: 10:02:47 PM, Elapsed: 00:00:01.0990000
      ErrorMessage: none
      Document Errors: 0, Warnings: 0

여기에 메모는 두 가지 상태 값입니다. 최상위 상태가 인덱서 자체의 상태를 보여 줍니다. 인덱서 상태의 **실행** 인덱서 올바르게 설정 되어 있음을 실행 하지만 하지는 현재 실행에 대해 사용할 수 있습니다.

인덱서가 실행 될 때마다 해당 특정 실행 지속적으로 인지 하는 것에 대 한 고유한 상태 있습니다 (**실행**)를 사용 하 여 이미 완료 되었으므로 또는 **성공** 또는 **TransientError** 상태입니다. 

별도 기록 항목을 사용 하 여 항목이 해당 변경 내용 추적 상태를 새로 고치려면 인덱서 다시 설정 되는 **재설정** 상태입니다.

상태 코드 및 인덱서 정보를 모니터링 하는 방법에 대 한 자세한 내용은 참조 하세요. [GetIndexerStatus](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) REST api에서입니다.

문서 관련 오류 또는 경고가 발생 하는 방법에 대 한 세부 정보 목록을 열거 하 여 검색할 수 있습니다 `IndexerExecutionResult.Errors` 고 `IndexerExecutionResult.Warnings`입니다.

인덱서를 모니터링 하는 데.NET SDK 클래스에 대 한 자세한 내용은 참조 하세요. [IndexerExecutionInfo](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutioninfo?view=azure-dotnet) 하 고 [IndexerExecutionResult](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexerexecutionresult?view=azure-dotnet)합니다.
