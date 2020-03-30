---
title: 대규모 데이터 세트로 작업
description: Azure 리소스 그래프로 작업하는 동안 대규모 데이터 집합에서 레코드를 가져옵니다.
ms.date: 03/20/2020
ms.topic: conceptual
ms.openlocfilehash: be15a6234935627ca748276e6330c50c3ee5a775
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80064736"
---
# <a name="working-with-large-azure-resource-data-sets"></a>큰 Azure 리소스 데이터 세트 작업

Azure Resource Graph는 Azure 환경의 리소스로 작업하고 해당 정보를 가져오기 위한 것입니다. Resource Graph를 사용하면 수천 개의 레코드를 쿼리하는 경우에도 이 데이터를 빠르게 가져올 수 있습니다. Resource Graph에는 이러한 큰 데이터 세트 작업을 위한 여러 가지 옵션이 있습니다.

빈도가 높은 쿼리 작업에 대한 지침은 [제한된 요청에 대한 지침을](./guidance-for-throttled-requests.md)참조하십시오.

## <a name="data-set-result-size"></a>데이터 세트 결과 크기

기본적으로 Resource Graph는 **100**개의 레코드만 반환하도록 쿼리를 제한합니다. 이 제어는 큰 데이터 세트를 생성하는 의도하지 않은 쿼리로부터 사용자와 서비스를 둘 다 보호합니다. 이 이벤트는 주로 고객이 특정 요구 사항에 맞는 방식으로 리소스를 찾고 필터링하기 위해 쿼리를 실험하는 경우에 발생합니다. 이 제어는 [top](/azure/kusto/query/topoperator) 또는 [limit](/azure/kusto/query/limitoperator) Azure Data Explorer 언어 연산자를 사용하여 결과를 제한하는 것과 다릅니다.

> [!NOTE]
> **First를**사용하는 경우 또는 `asc` `desc`을 사용하여 하나 이상의 열을 사용하여 결과를 정렬하는 것이 좋습니다. 정렬하지 않으면 반환된 결과가 임의이며 반복할 수 없습니다.

Resource Graph를 조작하는 모든 메서드를 통해 기본 제한을 재정의할 수 있습니다. 다음 예제에서는 데이터 세트 크기 제한을 _200_으로 변경하는 방법을 보여 줍니다.

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --first 200 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -First 200
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)에서 제어는 **$top**이며 **QueryRequestOptions**의 일부입니다.

‘가장 제한적’인 제어가 적용됩니다.__ 예를 들어 쿼리에서 **top** 또는 **limit** 연산자를 사용하며 **First**보다 많은 레코드를 생성하는 경우 반환되는 최대 레코드 수는 **First**와 같습니다. 마찬가지로, **top** 또는 **limit**가 **First**보다 작은 경우 반환되는 레코드 집합은 **top** 또는 **limit**로 구성된 더 작은 값이 됩니다.

**First**의 현재 허용된 최댓값은 _5000_입니다.

## <a name="skipping-records"></a>레코드 건너뛰기

큰 데이터 세트 작업을 위한 다음 옵션은 **Skip** 제어입니다. 이 제어를 사용하면 쿼리가 결과를 반환하기 전에 정의된 레코드 수를 점프하거나 건너뛸 수 있습니다. **Skip**은 결과 집합의 중간에 있는 레코드에 접근하기 위해 의미 있는 방식으로 결과를 정렬하는 쿼리에 유용합니다. 필요한 결과가 반환된 데이터 세트의 끝에 있는 경우, 다른 정렬 구성을 사용하고 대신 데이터 세트의 맨 위에서 결과를 검색하는 것이 더 효율적입니다.

> [!NOTE]
> **건너뛰기를**사용하는 경우 또는 `asc` `desc`을 사용하여 하나 이상의 열을 사용하여 결과를 정렬하는 것이 좋습니다. 정렬하지 않으면 반환된 결과가 임의이며 반복할 수 없습니다.

다음 예제에서는 반환된 결과 집합을 11번째 레코드로 시작하는 대신, 쿼리에서 생성하는 처음 _10_개의 레코드를 건너뛰는 방법을 보여 줍니다.

```azurecli-interactive
az graph query -q "Resources | project name | order by name asc" --skip 10 --output table
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project name | order by name asc" -Skip 10
```

[REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources)에서 제어는 **$skip**이며 **QueryRequestOptions**의 일부입니다.

## <a name="paging-results"></a>페이징 결과

결과를 처리를 위해 더 작은 레코드 집합으로 설정해야 하거나 결과 집합이 반환된 _레코드 1000개에_ 대한 최대 허용 값을 초과하기 때문에 페이징을 사용합니다. [REST API](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources) **QueryResponse**는 결과 집합이 분할되었음을 나타내는 값(**resultTruncated** 및 **$skipToken**)을 제공합니다.
**resultTruncated**는 응답에 반환되지 않은 추가 레코드가 있는지 여부를 소비자에게 알리는 부울 값입니다. **count** 속성이 **totalRecords** 속성보다 작은 경우에도 이 조건을 식별할 수 있습니다. **totalRecords**는 쿼리와 일치하는 레코드 수를 정의합니다.

 **resultTruncated** 페이징을 사용 하지 않도록 설정 하거나 `id` 열 없이 불가능 하거나 쿼리가 요청 하는 것 보다 사용할 수 있는 리소스가 적은 경우 **true입니다.** **결과가** **true이면** **$skipToken** 속성이 설정되지 않습니다.

다음 예제는 Azure CLI 및 Azure PowerShell을 건너뛴 후 처음 3000개 레코드를 **건너뛰고** **처음** 1000개의 레코드를 반환하는 방법을 보여 주었습니다.

```azurecli-interactive
az graph query -q "Resources | project id, name | order by id asc" --first 1000 --skip 3000
```

```azurepowershell-interactive
Search-AzGraph -Query "Resources | project id, name | order by id asc" -First 1000 -Skip 3000
```

> [!IMPORTANT]
> 페이지 매김이 작동하려면 쿼리가 **ID** 필드를 **project**해야 합니다. 쿼리에서 누락된 경우 응답에 **$skipToken**포함되지 않습니다.

예제는 REST API 문서에서 [다음 페이지 쿼리](/rest/api/azureresourcegraph/resourcegraph(2018-09-01-preview)/resources/resources#next-page-query)를 참조하세요.

## <a name="formatting-results"></a>결과 서식 지정

리소스 그래프 쿼리의 결과는 _테이블과_ _ObjectArray의_두 가지 형식으로 제공됩니다. 형식은 요청 옵션의 일부로 **resultFormat** 매개 변수로 구성됩니다. _테이블_ 형식은 **resultFormat**의 기본값입니다.

Azure CLI의 결과는 기본적으로 JSON에서 제공됩니다. Azure PowerShell의 결과는 기본적으로 **PSCustomObject이지만** `ConvertTo-Json` cmdlet을 사용하여 JSON으로 빠르게 변환할 수 있습니다. 다른 SDK의 경우 _ObjectArray_ 형식을 출력하도록 쿼리 결과를 구성할 수 있습니다.

### <a name="format---table"></a>형식 - 표

기본 형식인 _Table_은 쿼리에서 반환되는 속성의 열 디자인 및 행 값을 강조 표시하도록 설계된 JSON 형식을 반환합니다. 이 형식은 구조화 된 테이블 이나 스프레드시트에 정의 된 데이터와 매우 유사 하 게 먼저 식별 된 열 및 해당 열에 정렬 된 데이터를 나타내는 각 행.

다음은 _테이블_ 서식이 있는 쿼리 결과의 샘플입니다.

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": {
        "columns": [{
                "name": "name",
                "type": "string"
            },
            {
                "name": "type",
                "type": "string"
            },
            {
                "name": "location",
                "type": "string"
            },
            {
                "name": "subscriptionId",
                "type": "string"
            }
        ],
        "rows": [
            [
                "veryscaryvm2-nsg",
                "microsoft.network/networksecuritygroups",
                "eastus",
                "11111111-1111-1111-1111-111111111111"
            ]
        ]
    },
    "facets": [],
    "resultTruncated": "true"
}
```

### <a name="format---objectarray"></a>형식 - 개체 배열

_ObjectArray_ 형식은 결과를 JSON 형식으로 반환합니다. 그러나 이 디자인은 열과 행 데이터가 배열 그룹에서 일치하는 JSON에서 일반적인 키/값 쌍 관계에 정렬됩니다.

다음은 _ObjectArray_ 서식이 있는 쿼리 결과의 샘플입니다.

```json
{
    "totalRecords": 47,
    "count": 1,
    "data": [{
        "name": "veryscaryvm2-nsg",
        "type": "microsoft.network/networksecuritygroups",
        "location": "eastus",
        "subscriptionId": "11111111-1111-1111-1111-111111111111"
    }],
    "facets": [],
    "resultTruncated": "true"
}
```

다음은 _ObjectArray_ 형식을 사용하도록 **resultFormat을** 설정하는 몇 가지 예입니다.

```csharp
var requestOptions = new QueryRequestOptions( resultFormat: ResultFormat.ObjectArray);
var request = new QueryRequest(subscriptions, "Resources | limit 1", options: requestOptions);
```

```python
request_options = QueryRequestOptions(
    result_format=ResultFormat.object_array
)
request = QueryRequest(query="Resources | limit 1", subscriptions=subs_list, options=request_options)
response = client.resources(request)
```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 되는 언어를 참조 하십시오.
- [고급 쿼리에서](../samples/advanced.md)고급 용도를 참조하십시오.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기