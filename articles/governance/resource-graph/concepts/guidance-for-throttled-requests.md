---
title: 제한된 요청에 대한 지침
description: Azure 리소스 그래프가 제한 되는 것을 방지 하기 위해 더 나은 쿼리를 만드는 방법을 알아봅니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/19/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 85d68beb27ab27a2ada9acbf9482d35dec438c06
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71980280"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 리소스 그래프의 제한 된 요청에 대 한 지침

Azure 리소스 그래프 데이터를 프로그래밍 방식으로 자주 사용 하는 경우 제한이 쿼리 결과에 미치는 영향에 대 한 고려 사항을 고려해 야 합니다. 데이터를 요청 하는 방식을 변경 하면 사용자와 조직에서 제한 하는 것을 방지 하 고 Azure 리소스에 대 한 시기 적절 한 데이터 흐름을 유지 관리할 수 있습니다.

이 문서에서는 Azure 리소스 그래프에서 쿼리를 만드는 것과 관련 된 네 가지 영역 및 패턴에 대해 설명 합니다.

- 제한 헤더 이해
- 쿼리 일괄 처리
- 쿼리 시차
- 페이지 매김의 영향

## <a name="understand-throttling-headers"></a>제한 헤더 이해

Azure 리소스 그래프는 특정 기간에 따라 각 사용자에 대 한 할당량 번호를 할당 합니다. 예를 들어 사용자는 제한 없이 5 초 마다 최대 15 개의 쿼리를 보낼 수 있습니다. 할당량 값은 많은 요인에 의해 결정 되며 변경 될 수 있습니다.

모든 쿼리 응답에서 Azure 리소스 그래프는 다음과 같은 두 개의 제한 헤더를 추가 합니다.

- `x-ms-user-quota-remaining`(int): 사용자의 나머지 리소스 할당량입니다. 이 값은 쿼리 수에 매핑됩니다.
- `x-ms-user-quota-resets-after`(hh:mm:ss): 사용자의 할당량 소비가 다시 설정 될 때 까지의 기간입니다.

헤더의 작동 방식을 설명 하기 위해 `x-ms-user-quota-remaining: 10` 및 `x-ms-user-quota-resets-after: 00:00:03`의 헤더와 값이 있는 쿼리 응답을 살펴보겠습니다.

- 다음 3 초 이내에 최대 10 개의 쿼리를 제한 없이 제출할 수 있습니다.
- 3 초 후에 `x-ms-user-quota-remaining` 및 `x-ms-user-quota-resets-after`의 값이 각각 `15` 및 `00:00:05`으로 다시 설정 됩니다.

헤더를 사용 하 여 쿼리 요청을 _백오프_ 하는 예제를 보려면 [query in Parallel](#query-in-parallel)의 샘플을 참조 하세요.

## <a name="batching-queries"></a>쿼리 일괄 처리

쿼리를 병렬화 하는 것 보다 구독, 리소스 그룹 또는 개별 리소스를 기준으로 쿼리를 일괄 처리 하는 것이 더 효율적입니다. 큰 쿼리의 할당량 비용은 종종 많은 작은 쿼리 및 대상 쿼리를 위한 할당량 비용 보다 낮습니다. 일괄 처리 크기는 _300_보다 작은 것이 좋습니다.

- 잘못 최적화 된 방법의 예

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

- 최적화 된 일괄 처리 방법의 예 #1

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

- 최적화 된 일괄 처리 방법의 예 #2

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

## <a name="staggering-queries"></a>쿼리 시차

제한이 적용 되는 방식으로 인해 쿼리를 지연 하는 것이 좋습니다. 즉, 동시에 60 쿼리를 전송 하는 대신 쿼리를 5 초 동안 4 초 windows로 분할 합니다.

- 지연 되지 않은 쿼리 일정

  | 쿼리 수         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 시간 간격 (초) | 0-5 | 5-10 | 10-15 | 15-20 |

- 지그재그형 쿼리 일정

  | 쿼리 수         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 시간 간격 (초) | 0-5 | 5-10 | 10-15 | 15-20 |

다음은 Azure 리소스 그래프를 쿼리할 때 제한 헤더를 준수 하는 예입니다.

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

### <a name="query-in-parallel"></a>병렬 쿼리

병렬 처리를 통해 일괄 처리가 권장 되더라도 쿼리를 쉽게 일괄 처리할 수 없는 경우가 있습니다. 이러한 경우 여러 쿼리를 병렬 방식으로 전송 하 여 Azure 리소스 그래프를 쿼리 하는 것이 좋습니다. 다음은 이러한 시나리오에서 제한 헤더를 기반으로 _백오프_ 하는 방법의 예입니다.

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

Azure 리소스 그래프가 단일 쿼리 응답에서 최대 1000 개 항목을 반환 하므로 원하는 전체 데이터 집합을 가져오기 위해 [쿼리를 시작](./work-with-data.md#paging-results) 해야 할 수 있습니다. 그러나 일부 Azure 리소스 그래프 클라이언트는 페이지 매김을 다른 방식으로 처리 합니다.

- C# SDK

  ResourceGraph SDK를 사용 하는 경우 이전 쿼리 응답에서 반환 되는 skip 토큰을 다음으로 페이지가 매겨진 쿼리로 전달 하 여 페이지 매김을 처리 해야 합니다. 이 디자인은 페이지가 매겨진 모든 호출에서 결과를 수집 하 고 끝에 함께 결합 해야 함을 의미 합니다. 이 경우 전송 되는 각 페이지가 매겨진 쿼리는 하나의 쿼리 할당량을 사용 합니다.

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

- Azure CLI/Azure PowerShell

  Azure CLI 또는 Azure PowerShell를 사용 하는 경우 Azure 리소스 그래프에 대 한 쿼리는 최대 5000 항목에서 자동으로 페이지가 매겨집니다. 쿼리 결과는 페이지가 매겨진 모든 호출에서 항목의 결합 된 목록을 반환 합니다. 이 경우 쿼리 결과의 항목 수에 따라 페이지가 매겨진 단일 쿼리에서 둘 이상의 쿼리 할당량을 사용할 수 있습니다. 예를 들어 아래 예제에서는 쿼리를 한 번 실행할 때 쿼리 할당량을 5 개까지 사용할 수 있습니다.

  ```azurecli-interactive
  az graph query -q 'project id, name, type' -top 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'project id, name, type' -Top 5000
  ```

## <a name="still-get-throttled"></a>제한 된 상태 인가요?

위의 권장 사항을 연습 한 후 제한 되는 경우 팀에 [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)로 문의 하세요.

다음 세부 정보를 제공 합니다.

- 특정 사용 사례 및 비즈니스 드라이버는 더 높은 조정 제한을 요구 합니다.
- 액세스 권한이 있는 리소스는 몇 개입니까? 단일 쿼리에서 반환 되는의 개수는 몇 개입니까?
- 관심 있는 리소스 유형은 무엇 인가요?
- 쿼리 패턴은 무엇 인가요? Y 초 당 X 개 쿼리

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 중인 언어를 참조 하세요.
- [고급 쿼리에서](../samples/advanced.md)고급 사용을 참조 하세요.
- 리소스를 [탐색](explore-resources.md)하는 방법을 알아봅니다.