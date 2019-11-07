---
title: 쿼리 언어 이해
description: 리소스 그래프 테이블에 대해 설명 하 고 Azure 리소스 그래프에서 사용할 수 있는 Kusto 데이터 형식, 연산자 및 함수에 대해 설명 합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 10/21/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: d0ba3195aef246ff49042f61dcec0b4397b5dde6
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2019
ms.locfileid: "73622630"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각 작업을 수행 하 고 [Kusto 쿼리 언어 (KQL)](/azure/kusto/query/index)를 기반으로 작동 합니다. 리소스 그래프에서 사용 하는 쿼리 언어에 대 한 자세한 내용은 [KQL에 대 한 자습서](/azure/kusto/query/tutorial)부터 시작 합니다.

이 문서에서는 리소스 그래프에서 지 원하는 언어 구성 요소에 대해 설명 합니다.

- [리소스 그래프 테이블](#resource-graph-tables)
- [지원 되는 KQL 언어 요소](#supported-kql-language-elements)
- [이스케이프 문자](#escape-characters)

## <a name="resource-graph-tables"></a>리소스 그래프 테이블

리소스 그래프는 리소스 관리자 리소스 형식 및 해당 속성에 대해 저장 하는 데이터에 대 한 여러 테이블을 제공 합니다. 이러한 테이블은 `join` 또는 `union` 연산자와 함께 사용 하 여 관련 리소스 형식에서 속성을 가져올 수 있습니다. 다음은 리소스 그래프에서 사용할 수 있는 테이블의 목록입니다.

|리소스 그래프 테이블 |설명 |
|---|---|
|리소스 |쿼리에 정의 된 내용이 없는 경우의 기본 테이블입니다. 대부분의 리소스 관리자 리소스 유형 및 속성은 다음과 같습니다. |
|ResourceContainers |구독 (미리 보기--`Microsoft.Resources/subscriptions`) 및 리소스 그룹 (`Microsoft.Resources/subscriptions/resourcegroups`) 리소스 유형 및 데이터를 포함 합니다. |
|AlertsManagementResources |`Microsoft.AlertsManagement`_와 관련 된_ 리소스를 포함 합니다. |
|SecurityResources |`Microsoft.Security`_와 관련 된_ 리소스를 포함 합니다. |

> [!NOTE]
> _리소스_ 는 기본 테이블입니다. _리소스_ 테이블을 쿼리 하는 동안에는 `join` 또는 `union`를 사용 하지 않는 한 테이블 이름을 제공 하지 않아도 됩니다. 그러나 권장 되는 방법은 항상 쿼리에 초기 테이블을 포함 하는 것입니다.

포털의 리소스 그래프 탐색기를 사용 하 여 각 테이블에서 사용할 수 있는 리소스 종류를 검색할 수 있습니다. 또는 `<tableName> | distinct type`와 같은 쿼리를 사용 하 여 해당 환경에 존재 하는 지정 된 리소스 그래프 테이블이 지 원하는 리소스 종류의 목록을 가져올 수 있습니다.

다음 쿼리는 간단한 `join`을 보여 줍니다. 쿼리 결과는 열을 함께 혼합 하 고 조인 된 테이블의 중복 열 이름 (이 예에서는 _ResourceContainers_ **)을 1**로 추가 합니다. _ResourceContainers_ 테이블에는 구독과 리소스 그룹 모두에 대 한 형식이 있으므로 _리소스_ 테이블에서 리소스에 조인 하는 데 사용할 수 있습니다.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

다음 쿼리는 `join`의 보다 복잡 한 사용을 보여 줍니다. 이 쿼리는 조인 된 테이블을 구독 리소스로 제한 하 고, `project`를 사용 하 여 원래 필드 _subscriptionId_ 와 이름 _필드를 모두_ _이름_ 으로 바꾸었습니다. 필드 이름 바꾸기는 _리소스_에 이미 존재 하므로 _name1_ 으로 추가 하는 `join`을 방지 합니다. 원래 테이블은 `where`를 사용 하 여 필터링 되 고 다음 `project`에는 두 테이블의 열이 포함 됩니다. 쿼리 결과는 유형, 주요 자격 증명 모음 이름 및 구독 이름을 표시 하는 단일 키 자격 증명 모음입니다.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `project`를 사용 하 여 `join` 결과를 제한 하는 경우 `join`에서 사용 하는 속성을 사용 하 여 두 테이블을 연결 합니다. 위 예제의 _subscriptionId_ 는 `project`에 포함 되어야 합니다.

## <a name="supported-kql-language-elements"></a>지원 되는 KQL 언어 요소

리소스 그래프는 모든 KQL [데이터 형식](/azure/kusto/query/scalar-data-types/), [스칼라 함수](/azure/kusto/query/scalarfunctions), [스칼라 연산자](/azure/kusto/query/binoperators)및 [집계 함수](/azure/kusto/query/any-aggfunction)를 지원 합니다. 특정 [테이블 형식 연산자](/azure/kusto/query/queries) 는 리소스 그래프에서 지원 되며, 그 중 일부는 다른 동작을 포함 합니다.

### <a name="supported-tabulartop-level-operators"></a>지원 되는 테이블 형식/상위 수준 연산자

다음은 특정 샘플과 함께 리소스 그래프에서 지 원하는 KQL 테이블 형식 연산자의 목록입니다.

|KQL |리소스 그래프 샘플 쿼리 |참고 사항 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[키 자격 증명 모음 수](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[특정 별칭에 대한 고유 값 표시](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[OS 유형별 가상 머신 개수 계산](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[구독 이름이 있는 Key vault](../samples/advanced.md#join) |지원 되는 조인 특색: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). 단일 쿼리에서 `join` 3 개로 제한 됩니다. 브로드캐스트 조인과 같은 사용자 지정 조인 전략은 허용 되지 않습니다. 단일 테이블 내에서 또는 _리소스_ 와 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[limit](/azure/kusto/query/limitoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`take`의 동의어 |
|[mv-확장](/azure/kusto/query/mvexpandoperator) |[특정 쓰기 위치를 사용 하 여 Cosmos DB 나열](../samples/advanced.md#mvexpand-cosmosdb) |_Rowlimit_ 최대 400 |
|[주문을](/azure/kusto/query/orderoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`sort`의 동의어 |
|[project](/azure/kusto/query/projectoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[결과에서 열 제거](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`order`의 동의어 |
|[summarize](/azure/kusto/query/summarizeoperator) |[Azure 리소스 개수 계산](../samples/starter.md#count-resources) |첫 페이지 단순화만 |
|[take](/azure/kusto/query/takeoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`limit`의 동의어 |
|[top](/azure/kusto/query/topoperator) |[이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[두 쿼리의 결과를 단일 결과로 결합](../samples/advanced.md#unionresults) |단일 테이블 허용: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _테이블_ 단일 쿼리에서 `union` 다리 3 개로 제한 됩니다. `union` 레그 테이블의 유사 항목 확인은 허용 되지 않습니다. 단일 테이블 내에서 또는 _리소스_ 와 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[where](/azure/kusto/query/whereoperator) |[스토리지를 포함하는 리소스 표시](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>이스케이프 문자

`.` 또는 `$`을 포함 하는 속성 이름 (예:)은 쿼리에서 래핑해야 하거나 이스케이프 해야 합니다. 그렇지 않으면 속성 이름이 올바르게 해석 되 고 예상 된 결과를 제공 하지 않습니다.

- `.`-속성 이름을 다음과 같이 래핑합니다. `['propertyname.withaperiod']`
  
  _Odata_속성을 래핑하는 쿼리 예제:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- 속성 이름에서 문자를 `$` 이스케이프 합니다. 사용 되는 이스케이프 문자는에서 실행 되는 셸 리소스 그래프에 따라 다릅니다.

  - **bash** - `\`

    Bash에서 속성 _\$형식을_ 이스케이프 하는 예제 쿼리입니다.

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -`$` 문자를 이스케이프 하지 않습니다.

  - **PowerShell** - ``` ` ```

    PowerShell에서 속성 _\$형식을_ 이스케이프 하는 예제 쿼리는 다음과 같습니다.

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 중인 언어를 참조 하세요.
- [고급 쿼리에서](../samples/advanced.md)고급 사용을 참조 하세요.
- [리소스를 탐색](explore-resources.md)하는 방법에 대해 자세히 알아보세요.