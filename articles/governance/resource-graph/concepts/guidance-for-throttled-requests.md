---
title: 제한된 요청에 대한 지침
description: Azure 리소스 그래프에서 요청이 제한되지 않도록 그룹화, 스태거, 패기 및 쿼리를 병렬로 사용하는 방법을 알아봅니다.
ms.date: 12/02/2019
ms.topic: conceptual
ms.openlocfilehash: fbd4bec715b187bcc643fe32b8452b0e062e7713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259852"
---
# <a name="guidance-for-throttled-requests-in-azure-resource-graph"></a>Azure 리소스 그래프의 제한된 요청에 대한 지침

Azure Resource Graph 데이터를 프로그래밍 방식으로 자주 사용할 때는 제한이 쿼리 결과에 미치는 영향을 고려해야 합니다. 데이터 요청 방식을 변경하면 사용자와 조직이 제한되는 것을 방지하고 Azure 리소스에 대한 시기 적합한 데이터 흐름을 유지하는 데 도움이 될 수 있습니다.

이 문서에서는 Azure 리소스 그래프에서 쿼리 생성과 관련된 네 가지 영역과 패턴을 다룹니다.

- 제한 헤더 이해
- 쿼리 그룹화
- 쿼리 시차
- 페이지 조정의 영향

## <a name="understand-throttling-headers"></a>제한 헤더 이해

Azure 리소스 그래프는 시간 창을 기반으로 각 사용자에 대한 할당량 번호를 할당합니다. 예를 들어 사용자는 제한되지 않고 5초마다 15개의 쿼리를 보낼 수 있습니다. 할당량 값은 여러 요인에 의해 결정되며 변경될 수 있습니다.

모든 쿼리 응답에서 Azure 리소스 그래프는 두 개의 제한 헤더를 추가합니다.

- `x-ms-user-quota-remaining`(int): 사용자의 나머지 리소스 할당량입니다. 이 값은 쿼리 수에 매핑됩니다.
- `x-ms-user-quota-resets-after`(hh:mm:ss): 사용자의 할당량 소비가 재설정될 때까지의 시간 기간입니다.

헤더의 작동 방식을 설명하기 위해 `x-ms-user-quota-remaining: 10` 및 `x-ms-user-quota-resets-after: 00:00:03`의 헤더와 값이 있는 쿼리 응답을 살펴보겠습니다.

- 이후 3초 이내에 제한없이 10개의 쿼리를 제출할 수 있습니다.
- 3초 후, `x-ms-user-quota-remaining` 의 `x-ms-user-quota-resets-after` 값과 `00:00:05` 각각으로 `15` 재설정됩니다.

쿼리 요청을 _백오프하기_ 위해 헤더를 사용하는 예제를 보려면 [병렬 쿼리의](#query-in-parallel)샘플을 참조하십시오.

## <a name="grouping-queries"></a>쿼리 그룹화

구독, 리소스 그룹 또는 개별 리소스별로 쿼리를 그룹화하는 것이 쿼리를 병렬화하는 것보다 더 효율적입니다. 더 큰 쿼리의 할당량 비용은 많은 소규모 및 대상 쿼리의 할당량 비용보다 적은 경우가 많습니다. 그룹 크기는 _300보다_작게 하는 것이 좋습니다.

- 최적화되지 못한 접근 방식의 예

  ```csharp
  // NOT RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  foreach (var subscriptionId in subscriptionIds)
  {
      var userQueryRequest = new QueryRequest(
          subscriptions: new[] { subscriptionId },
          query: "Resoures | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

  // ...
  }
  ```

- 최적화된 그룹화 접근 방식의 #1 예

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var subscriptionIds = /* A big list of subscriptionIds */

  const int groupSize = 100;
  for (var i = 0; i <= subscriptionIds.Count / groupSize; ++i)
  {
      var currSubscriptionGroup = subscriptionIds.Skip(i * groupSize).Take(groupSize).ToList();
      var userQueryRequest = new QueryRequest(
          subscriptions: currSubscriptionGroup,
          query: "Resources | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

- 하나의 쿼리에서 여러 리소스를 가져오기 위한 최적화된 그룹화 방법의 #2 예

  ```kusto
  Resources | where id in~ ({resourceIdGroup}) | project name, type
  ```

  ```csharp
  // RECOMMENDED
  var header = /* your request header */
  var resourceIds = /* A big list of resourceIds */

  const int groupSize = 100;
  for (var i = 0; i <= resourceIds.Count / groupSize; ++i)
  {
      var resourceIdGroup = string.Join(",",
          resourceIds.Skip(i * groupSize).Take(groupSize).Select(id => string.Format("'{0}'", id)));
      var userQueryRequest = new QueryRequest(
          subscriptions: subscriptionList,
          query: $"Resources | where id in~ ({resourceIdGroup}) | project name, type");

      var azureOperationResponse = await this.resourceGraphClient
          .ResourcesWithHttpMessagesAsync(userQueryRequest, header)
          .ConfigureAwait(false);

    // ...
  }
  ```

## <a name="staggering-queries"></a>쿼리 시차

제한이 적용되는 방식으로 인해 쿼리를 엇갈리게 하는 것이 좋습니다. 즉, 동시에 60개의 쿼리를 보내는 대신 쿼리를 4개의 5초 창으로 엇갈린다.

- 엇갈림없는 쿼리 일정

  | Query Count         | 60  | 0    | 0     | 0     |
  |---------------------|-----|------|-------|-------|
  | 시간 간격(초) | 0-5 | 5-10 | 10-15 | 15-20 |

- 엇갈림 쿼리 일정

  | Query Count         | 15  | 15   | 15    | 15    |
  |---------------------|-----|------|-------|-------|
  | 시간 간격(초) | 0-5 | 5-10 | 10-15 | 15-20 |

다음은 Azure 리소스 그래프를 쿼리할 때 제한 헤더를 준수하는 예제입니다.

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

### <a name="query-in-parallel"></a>병렬로 쿼리

병렬화를 통해 그룹화를 권장하지만 쿼리를 쉽게 그룹화할 수 없는 경우가 있습니다. 이러한 경우 병렬 방식으로 여러 쿼리를 전송하여 Azure 리소스 그래프를 쿼리할 수 있습니다. 다음은 이러한 시나리오에서 제한 헤더를 기반으로 _백오프하는_ 방법의 예입니다.

```csharp
IEnumerable<IEnumerable<string>> queryGroup = /* Groups of queries  */
// Run groups in parallel.
await Task.WhenAll(queryGroup.Select(ExecuteQueries)).ConfigureAwait(false);

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

## <a name="pagination"></a>페이지 매김

Azure Resource Graph는 단일 쿼리 응답에서 최대 1,000개의 항목을 반환하므로 찾고 있는 전체 데이터 집합을 얻으려면 쿼리를 [페이지트해야](./work-with-data.md#paging-results) 할 수 있습니다. 그러나 일부 Azure 리소스 그래프 클라이언트는 페이지 조정을 다른 클라이언트와 다르게 처리합니다.

- C# SDK

  ResourceGraph SDK를 사용하는 경우 이전 쿼리 응답에서 반환되는 건너뛰기 토큰을 다음 paginated 쿼리로 전달하여 페이지 매김을 처리해야 합니다. 이 디자인은 모든 paginated 호출에서 결과를 수집하고 마지막에 함께 결합해야 한다는 것을 의미합니다. 이 경우 전송하는 각 paginated 쿼리는 하나의 쿼리 할당량을 수행합니다.

  ```csharp
  var results = new List<object>();
  var queryRequest = new QueryRequest(
      subscriptions: new[] { mySubscriptionId },
      query: "Resources | project id, name, type | top 5000");
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

  Azure CLI 또는 Azure PowerShell을 사용하는 경우 Azure 리소스 그래프에 대한 쿼리가 자동으로 페이그(paginat)되어 최대 5,000개의 항목을 가져옵니다. 쿼리 결과는 모든 페이그(paginated) 호출의 결합된 항목 목록을 반환합니다. 이 경우 쿼리 결과의 항목 수에 따라 페이그된 단일 쿼리가 두 개 이상의 쿼리 할당량을 사용할 수 있습니다. 예를 들어 아래 예제에서는 쿼리의 단일 실행에서 최대 5개의 쿼리 할당량을 사용할 수 있습니다.

  ```azurecli-interactive
  az graph query -q 'Resources | project id, name, type' --first 5000
  ```

  ```azurepowershell-interactive
  Search-AzGraph -Query 'Resources | project id, name, type' -First 5000
  ```

## <a name="still-get-throttled"></a>아직도 제한받을?

위의 권장 사항을 행사한 후 제한을 받는 경우 팀에 [resourcegraphsupport@microsoft.com](mailto:resourcegraphsupport@microsoft.com)문의하십시오.

다음 세부 정보를 제공합니다.

- 특정 사용 사례 및 비즈니스 드라이버가 더 높은 제한 제한에 대해 필요합니다.
- 액세스 권한이 있는 리소스는 몇 개입니까? 단일 쿼리에서 반환되는 쿼리 중 몇 개입니까?
- 어떤 종류의 리소스에 관심이 있습니까?
- 쿼리 패턴은 무엇입니까? Y초당 X 쿼리 등

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 되는 언어를 참조 하십시오.
- [고급 쿼리에서](../samples/advanced.md)고급 용도를 참조하십시오.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기