---
title: 쿼리 언어 이해
description: Resource Graph 테이블과 Azure Resource Graph와 함께 사용 가능한 Kusto 데이터 형식, 연산자 및 함수를 설명합니다.
ms.date: 06/29/2020
ms.topic: conceptual
ms.openlocfilehash: 4c545a8a5113f800545660a3ea812b61711630c2
ms.sourcegitcommit: f684589322633f1a0fafb627a03498b148b0d521
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/06/2020
ms.locfileid: "85970453"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각각은 [KQL(Kusto Query Language)](/azure/kusto/query/index)을 기반으로 작동합니다. Resource Graph에서 사용하는 쿼리 언어에 대해 알아보려면 [KQL에 대한 자습서](/azure/kusto/query/tutorial)로 시작하세요.

이 문서에서는 Resource Graph에서 지원하는 언어 구성 요소에 대해 설명합니다.

- [Resource Graph 테이블](#resource-graph-tables)
- [리소스 그래프 사용자 지정 언어 요소](#resource-graph-custom-language-elements)
- [지원되는 KQL 언어 요소](#supported-kql-language-elements)
- [이스케이프 문자](#escape-characters)

## <a name="resource-graph-tables"></a>Resource Graph 테이블

리소스 그래프는 Azure Resource Manager 리소스 형식 및 해당 속성에 대해 저장 하는 데이터에 대 한 여러 테이블을 제공 합니다. 이러한 테이블을 `join` 또는 `union` 연산자와 함께 사용하여 관련 리소스 종류에서 속성을 가져올 수 있습니다. 다음은 Resource Graph에서 사용할 수 있는 테이블의 목록입니다.

|Resource Graph 테이블 |Description |
|---|---|
|리소스 |쿼리에 정의된 내용이 없는 경우 기본 테이블입니다. 대부분의 Resource Manager 리소스 종류 및 속성이 포함되어 있습니다. |
|ResourceContainers |subscription(미리 보기에서는 --`Microsoft.Resources/subscriptions`) 및 resource group(`Microsoft.Resources/subscriptions/resourcegroups`) 리소스 종류 및 데이터를 포함합니다. |
|AdvisorResources |`Microsoft.Advisor` _관련_ 리소스를 포함합니다. |
|AlertsManagementResources |`Microsoft.AlertsManagement` _관련_ 리소스를 포함합니다. |
|HealthResources |`Microsoft.ResourceHealth` _관련_ 리소스를 포함합니다. |
|MaintenanceResources |`Microsoft.Maintenance` _관련_ 리소스를 포함합니다. |
|SecurityResources |`Microsoft.Security` _관련_ 리소스를 포함합니다. |

리소스 종류를 비롯한 전체 목록을 보려면 [참조: 지원되는 테이블 및 리소스 종류](../reference/supported-tables-resources.md)를 확인하세요.

> [!NOTE]
> _리소스_는 기본 테이블입니다. _리소스_ 테이블을 쿼리하는 동안에는 `join` 또는 `union`을 사용하지 않는 한 테이블 이름을 제공하지 않아도 됩니다. 그러나 권장되는 방법은 항상 쿼리에 초기 테이블을 포함하는 것입니다.

포털의 Resource Graph 탐색기를 사용하여 각 테이블에서 사용할 수 있는 리소스 종류를 검색할 수 있습니다. 또는 `<tableName> | distinct type` 쿼리를 사용하여 해당 환경에 존재하는, 지정된 Resource Graph 테이블이 지원하는 리소스 종류의 목록을 가져올 수 있습니다.

다음 쿼리는 단순한 `join`을 보여 줍니다. 쿼리 결과는 열을 함께 혼합하며, 조인된 테이블의 모든 중복 열 이름(이 예에서는 _ResourceContainers_)에는 **1**이 추가됩니다. _ResourceContainers_ 테이블에는 구독과 리소스 그룹 모두에 대한 형식이 있으므로 각 형식을 _resources_ 테이블의 리소스에 조인하는 데 사용할 수 있습니다.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

다음 쿼리는 `join`의 더 복잡한 사용법을 보여 줍니다. 이 쿼리는 조인된 테이블을 구독 리소스로 제한하고 `project`를 사용하여 원래 필드 _subscriptionId_ 및 _SubName_으로 이름이 바뀐 _name_ 필드만 포함합니다. 필드 이름 바꾸기는 필드가 _리소스_에 이미 있으므로 `join`이 이를 _name1_로 추가하는 것을 방지합니다. 원래 테이블은 `where`를 사용하여 필터링되고 다음 `project`에는 두 테이블의 열이 포함됩니다. 쿼리 결과는 유형, 키 자격 증명 모음 이름 및 해당하는 구독 이름을 표시하는 단일 키 자격 증명 모음입니다.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `project`를 사용하여 `join` 결과를 제한하는 경우 `join`에서 두 테이블의 관계를 설정하기 위해 사용하는 _subscriptionId_ 속성을 `project`에 포함해야 합니다.

## <a name="resource-graph-custom-language-elements"></a>리소스 그래프 사용자 지정 언어 요소

### <a name="shared-query-syntax-preview"></a><a name="shared-query-syntax"></a>공유 쿼리 구문 (미리 보기)

미리 보기 기능으로 서, [공유 쿼리](../tutorials/create-share-query.md) 는 리소스 그래프 쿼리에서 직접 액세스할 수 있습니다. 이 시나리오에서는 표준 쿼리를 공유 쿼리로 만들고 다시 사용할 수 있습니다. 리소스 그래프 쿼리 내에서 공유 쿼리를 호출 하려면 `{{shared-query-uri}}` 구문을 사용 합니다. 공유 쿼리의 URI는 해당 쿼리의 **설정** 페이지에 있는 공유 쿼리의 _리소스 ID_ 입니다. 이 예제에서 공유 쿼리 URI는 `/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS` 입니다.
이 URI는 다른 쿼리에서 참조 하려는 구독, 리소스 그룹 및 공유 쿼리의 전체 이름을 가리킵니다. 이 쿼리는 [자습서: 쿼리 만들기 및 공유](../tutorials/create-share-query.md)에서 만든 쿼리와 같습니다.

> [!NOTE]
> 공유 쿼리를 참조 하는 쿼리는 공유 쿼리로 저장할 수 없습니다.

예제 1: 공유 쿼리만 사용

이 리소스 그래프 쿼리의 결과는 공유 쿼리에 저장 된 쿼리와 같습니다.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
```

예제 2: 공유 쿼리를 더 큰 쿼리의 일부로 포함

이 쿼리는 먼저 공유 쿼리를 사용 하 고를 사용 `limit` 하 여 결과를 추가로 제한 합니다.

```kusto
{{/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/SharedQueries/providers/Microsoft.ResourceGraph/queries/Count VMs by OS}}
| where properties_storageProfile_osDisk_osType =~ 'Windows'
```

## <a name="supported-kql-language-elements"></a>지원되는 KQL 언어 요소

Resource Graph는 모든 KQL [데이터 형식](/azure/kusto/query/scalar-data-types/), [스칼라 반환 함수](/azure/kusto/query/scalarfunctions), [스칼라 연산자](/azure/kusto/query/binoperators), [집계 함수](/azure/kusto/query/any-aggfunction)를 지원합니다. Resource Graph는 특정 [테이블 형식 연산자](/azure/kusto/query/queries)를 지원하며, 그 중 일부는 동작이 서로 다릅니다.

### <a name="supported-tabulartop-level-operators"></a>지원되는 테이블 형식/최상위 수준 연산자

Resource Graph에서 지원하는 KQL 테이블 형식 연산자와 특정 샘플의 목록은 다음과 같습니다.

|KQL |Resource Graph 샘플 쿼리 |메모 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[키 자격 증명 모음 계수](../samples/starter.md#count-keyvaults) | |
|[distinct](/azure/kusto/query/distinctoperator) |[특정 별칭에 대한 고유 값 표시](../samples/starter.md#distinct-alias-values) | |
|[extend](/azure/kusto/query/extendoperator) |[OS 유형별 가상 머신 개수 계산](../samples/starter.md#count-os) | |
|[join](/azure/kusto/query/joinoperator) |[구독 이름이 있는 키 자격 증명 모음](../samples/advanced.md#join) |지원되는 조인 버전: [innerunique](/azure/kusto/query/joinoperator#default-join-flavor), [inner](/azure/kusto/query/joinoperator#inner-join), [leftouter](/azure/kusto/query/joinoperator#left-outer-join). 단일 쿼리의 `join`은 3개로 제한됩니다. 브로드캐스트 조인과 같은 사용자 지정 조인 전략은 허용되지 않습니다. 단일 테이블 내에서 사용하거나 _Resources_ 테이블과 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[limit](/azure/kusto/query/limitoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`take`의 동의어 |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | 레거시 연산자. `mv-expand`를 대신 사용합니다. _RowLimit_. 최댓값은 400입니다. 기본값은 128입니다. |
|[mv-expand](/azure/kusto/query/mvexpandoperator) |[특정 쓰기 위치를 사용하여 Cosmos DB 나열](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_. 최댓값은 400입니다. 기본값은 128입니다. |
|[order](/azure/kusto/query/orderoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`sort`의 동의어 |
|[project](/azure/kusto/query/projectoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[결과에서 열 제거](../samples/advanced.md#remove-column) | |
|[sort](/azure/kusto/query/sortoperator) |[리소스를 이름별로 나열](../samples/starter.md#list-resources) |`order`의 동의어 |
|[summarize](/azure/kusto/query/summarizeoperator) |[Azure 리소스 개수 계산](../samples/starter.md#count-resources) |간소화된 첫 페이지만 |
|[take](/azure/kusto/query/takeoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |`limit`의 동의어 |
|[top](/azure/kusto/query/topoperator) |[이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](../samples/starter.md#show-sorted) | |
|[union](/azure/kusto/query/unionoperator) |[두 쿼리의 결과를 단일 결과로 결합](../samples/advanced.md#unionresults) |단일 테이블 허용: _T_ `| union` \[`kind=` `inner`\|`outer`\] \[`withsource=`_ColumnName_\] _Table_. 단일 쿼리의 `union` 레그는 3개로 제한됩니다. `union` 레그 테이블의 유사 항목 확인은 허용되지 않습니다. 단일 테이블 내에서 사용하거나 _Resources_ 테이블과 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[where](/azure/kusto/query/whereoperator) |[스토리지를 포함하는 리소스 표시](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>이스케이프 문자

`.` 또는 `$`를 포함하는 일부 속성 이름은 쿼리에서 래핑하거나 이스케이프해야 합니다. 그렇지 않으면 속성 이름이 올바르게 해석되고 올바른 결과를 제공하지 않습니다.

- `.` - `['propertyname.withaperiod']`처럼 속성 이름을 래핑합니다.
  
  _odata.type_ 속성을 래핑하는 예제 쿼리:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` - 속성 이름에서 문자를 이스케이프합니다. 사용되는 이스케이프 문자는 Resource Graph가 실행되는 셸에 따라 다릅니다.

  - **bash** - `\`

    Bash에서 속성 _\$type_을 이스케이프하는 예제 쿼리:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - `$` 문자를 이스케이프하지 않습니다.

  - **PowerShell** - ``` ` ```

    PowerShell에서 속성 _\$type_을 이스케이프하는 예제 쿼리:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어를 확인합니다.
- [고급 쿼리](../samples/advanced.md)의 고급 사용법을 확인합니다.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기