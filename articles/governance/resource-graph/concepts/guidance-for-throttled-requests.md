---
title: 스로틀 된 요청에 대 한 지침
description: 요청을 피하 여 Azure 리소스 그래프에서 제한 되 고 더 나은 쿼리를 만드는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.openlocfilehash: c644d230846d9c644c3845348431eef36c8279c8
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276900"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 리소스 그래프에서 제한 된 요청에 대 한 지침

Azure 리소스 그래프 데이터를 프로그래밍 방식 및 자주 사용을 만들 때 고려 사항 만들어져 영향을 제한 하는 방법에 대 한 쿼리의 결과입니다. 도움이 될 수 있습니다 및 조직 제한 되지 않도록 하 고 Azure 리소스에 대 한 시기 적절 한 데이터의 흐름을 유지 관리 변경 되는 방식으로 데이터를 요청 했습니다.

이 문서에서는 4 개의 영역 및 Azure 리소스 그래프에 쿼리를 만드는 과정에 관련 된 패턴을 설명 합니다.

- 헤더가 제한 이해
- 일괄 처리 쿼리
- 시차 적용 쿼리
- 페이지 매김의 영향

## <a name="understand-throttling-headers"></a>헤더가 제한 이해

Azure 리소스 그래프는 특정 기간에 따라 각 사용자에 대 한 할당량 수를 할당 합니다. 예를 들어 사용자 제하지 없이 모든 5 초 창 내에서 최대 15 개의 쿼리를 보낼 수 있습니다. 할당량 값 많은 요인에 의해 결정 됩니다 이며 변경 될 수 있습니다.

모든 쿼리 응답에 Azure 리소스 그래프 두 제한 헤더를 추가 합니다.

- `x-ms-user-quota-remaining`(int): 사용자의 나머지 리소스 할당량입니다. 이 값은 쿼리 수에 매핑됩니다.
- `x-ms-user-quota-resets-after`(hh:mm:ss): 사용자의 할당량 소비 재설정 될 때까지 시간 기간입니다.

헤더의 원리를 보여 주기 위해 살펴보겠습니다 헤더 및 값을 포함 하는 쿼리 응답 `x-ms-user-quota-remaining: 10` 고 `x-ms-user-quota-resets-after: 00:00:03`입니다.

- 3 초 이내에 제하지 없이 최대 10 개의 쿼리를 제출할 수 있습니다.
- 3 초의 값에에서 `x-ms-user-quota-remaining` 하 고 `x-ms-user-quota-resets-after` 다시 설정 됩니다 `15` 및 `00:00:05` 각각.

헤더를 사용 하는 예제를 보려는 _백오프_ 에서 샘플을 참조 한 쿼리 요청 [병렬로 쿼리](#query-in-parallel)합니다.

## <a name="batching-queries"></a>일괄 처리 쿼리

구독, 리소스 그룹 또는 개별 리소스에서 쿼리를 일괄 처리 쿼리를 병렬화 하는 보다 더 효율적입니다. 할당량 보다 큰 쿼리 비용은 종종 많은 소형 및 대상 쿼리의 할당량 비용 보다도 합니다. 일괄 처리 크기를 권장 되는 보다 작은 _300_합니다.

- 최적화 되지 않은 접근 방법의 예

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 최적화 된 일괄 처리 방법의 예제 1

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int batchSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / batchSize; ++i)
  {
      var currSubscriptionBatch = subscriptionIds.Skip(i * batchSize).Take(batchSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionBatch,
          query: "project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 최적화 된 일괄 처리 방법의 예제 2

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int batchSize = 100;
  for (var i = 0; i <= resourceIds.Count / batchSize; ++i)
  {
      var resourceIdBatch = string.Join(",",
          resourceIds.Skip(i * batchSize).Take(batchSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"where id in~ ({resourceIds}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>시차 적용 쿼리

방식으로 인해 제한 적용, 시차를 적용 되도록 쿼리 것이 좋습니다. 즉, 동시 쿼리 수가 60에 보내는 대신 쿼리에 4 개의 5 초 windows 분산:

- 비 지연 쿼리 일정

  | 쿼리 수         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 시간 간격 (초) | 0-5 | 5-10 | 10-15 | 15-20 |

- 쿼리 일정 지연

  | 쿼리 수         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 시간 간격 (초) | 0-5 | 5-10 | 10-15 | 15-20 |

다음은 Azure 리소스 Graph를 쿼리할 때 제한 헤더를 존중의 예가입니다.

```csharp
while (/* Need to query more? */)
{
    var userQueryRequest = /* ... */
    // Send post request to Azure Resource Graph
    var azureOperationResponse = await this.resourceGraphClient
        .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
        .ConfigureAwait(false);

    var responseHeaders = azureOperationResponse.response.Headers;
    int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
    TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
    if (remainingQuota == 0)
    {
        // Need to wait until new quota is allocated
        await Task.Delay(resetAfter).ConfigureAwait(false);
    }
}
```

### <a name="query-in-parallel"></a>병렬에서 쿼리

병렬 처리를 통해 일괄 처리 권장 하는 경우에 경우가 쿼리 없습니다 쉽게 일괄 처리 합니다. 이러한 경우에 병렬 방식으로 여러 쿼리를 전송 하 여 Azure 리소스 그래프를 쿼리 하는 것이 좋습니다. 하는 방법의 예로 _백오프_ 헤더 이러한 시나리오에서는 제한에 따라:

```csharp
IEnumerable<IEnumerable<string>> queryBatches = /* Batches of queries  */
// Run batches in parallel.
await Task.WhenAll(queryBatches.Select(ExecuteQueries)).ConfigureAwait(false);

async Task ExecuteQueries(IEnumerable<string> queries)
{
    foreach (var query in queries)
    {
        var userQueryRequest = new QueryRequest(
            subscriptions: subscriptionList,
            query: query);
        // Send post request to Azure Resource Graph.
        var azureOperationResponse = await this.resourceGraphClient
            .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
            .ConfigureAwait(false);
        
        var responseHeaders = azureOperationResponse.response.Headers;
        int remainingQuota = /* read and parse x-ms-user-quota-remaining from responseHeaders */
        TimeSpan resetAfter = /* read and parse x-ms-user-quota-resets-after from responseHeaders */
        if (remainingQuota == 0)
        {
            // Delay by a random period to avoid bursting when the quota is reset.
            var delay = (new Random()).Next(1, 5) * resetAfter;
            await Task.Delay(delay).ConfigureAwait(false);
        }
    }
}
```

## <a name="pagination"></a>페이지 매기기

Azure 리소스 그래프 최대 1000 항목의 단일 쿼리 응답에서을 반환 하므로 해야 할 수 있습니다 [매기](./work-with-data.md#paging-results) 쿼리를 찾고 전체 데이터 집합을 가져옵니다. 그러나 일부 Azure 리소스 Graph 클라이언트는 다르게 페이지 매김을 처리합니다.

- C# SDK

  ResourceGraph SDK를 사용 하는 경우 다음 페이지 매긴된 쿼리에서 이전 쿼리 응답에서 반환 되 고 skip 토큰을 전달 하 여 페이지 매김 처리 해야 합니다. 이 디자인 모든 페이지 매긴된 호출의 결과 수집 하 고 끝에 결합 해야 함을 의미 합니다. 이 경우 보내는 각 페이지 매긴된 쿼리는 하나의 쿼리 할당량을 사용 합니다.

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "project id, name, type | top 5000");
  var azureOperationResponse = await this.resourceGraphClient
      .ResourcesWithHttpMessagesAsync(queryRequest, header)
      .ConfigureAwait(false);
  while (!string.Empty(azureOperationResponse.Body.SkipToken))
  {
      queryRequest.SkipToken = azureOperationResponse.Body.SkipToken;
      // Each post call to ResourceGraph consumes one query quota
      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(queryRequest, header)
          .ConfigureAwait(false);
      results.Add(azureOperationResponse.Body.Data.Rows);

      // Inspect throttling headers in query response and delay the next call if needed.
  }
  ```

- Azure CLI / Azure PowerShell

  Azure CLI 또는 Azure PowerShell을 사용 하는 경우 Azure 리소스 그래프에 대 한 쿼리는 자동으로 페이지를 매긴 최대 5000 개 항목을 가져올 수 합니다. 쿼리 결과 페이지 매긴된 대 한 모든 호출에서 항목의 결합 된 목록을 반환합니다. 이 경우 쿼리 결과의 항목 개수에 따라 단일 페이지 매긴된 쿼리에서 둘 이상의 쿼리 할당량을 사용할 수 있습니다. 예를 들어, 아래 예제에서는 쿼리를 실행 하는 단일 최대 5 개의 쿼리 할당량을 사용할 수 있습니다.

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>여전히 제한?

위의 권장 사항을 검토 한 후에 제한 되나요는, 하는 경우 팀에 문의 [ resourcegraphsupport@microsoft.com ](mailto:resourcegraphsupport@microsoft.com)합니다.

이러한 세부 정보를 제공 합니다.

- 특정 사용 사례 및 비즈니스 드라이버는 더 높은 제한 한도 필요합니다.
- 얼마나 많은 리소스에 대 한 액세스 있습니까? 됩니다 얼마나 많은 단일 쿼리에서 반환 되는?
- 어떤 유형의 리소스에 관심이 있습니까?
- 쿼리 패턴 이란? X 등 Y 초당 쿼리 합니다.

## <a name="next-steps"></a>다음 단계

- 사용 중인 언어 참조 [스타터 쿼리](../samples/starter.md)합니다.
- 고급 참조에서는 [고급 쿼리](../samples/advanced.md)합니다.
- 하는 방법을 알아봅니다 [리소스를 탐색](explore-resources.md)합니다.