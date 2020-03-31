---
title: 쿼리 언어 이해
description: 리소스 그래프 테이블과 Azure 리소스 그래프에서 사용할 수 있는 사용 가능한 Kusto 데이터 형식, 연산자 및 함수에 대해 설명합니다.
ms.date: 03/07/2020
ms.topic: conceptual
ms.openlocfilehash: 2f4be4d86a340867e1ad3015ff288f98fc54cecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927490"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각 작업은 [Kusto 쿼리 언어(KQL)를](/azure/kusto/query/index)기반으로 작동합니다. 리소스 그래프에서 사용하는 쿼리 언어에 대해 알아보려면 [KQL](/azure/kusto/query/tutorial)에 대한 자습서를 시작합니다.

이 문서에서는 리소스 그래프에서 지원하는 언어 구성 요소를 다룹니다.

- [리소스 그래프 표](#resource-graph-tables)
- [지원되는 KQL 언어 요소](#supported-kql-language-elements)
- [캐릭터 탈출](#escape-characters)

## <a name="resource-graph-tables"></a>리소스 그래프 표

리소스 그래프는 리소스 관리자 리소스 유형 및 해당 속성에 대해 저장하는 데이터에 대한 여러 테이블을 제공합니다. 이러한 테이블은 관련 `join` `union` 리소스 형식에서 속성을 얻기 위해 또는 연산자와 함께 사용할 수 있습니다. 리소스 그래프에서 사용할 수 있는 테이블 목록은 다음과 같습니다.

|리소스 그래프 표 |설명 |
|---|---|
|리소스 |쿼리에 정의된 테이블이 없는 경우 기본 테이블입니다. 대부분의 리소스 관리자 리소스 유형 및 속성은 여기에 있습니다. |
|리소스 컨테이너 |구독(미리 보기 `Microsoft.Resources/subscriptions`-- ) 및`Microsoft.Resources/subscriptions/resourcegroups`리소스 그룹 () 리소스 유형 및 데이터를 포함합니다. |
|어드바이저리소스 |와 _관련된_ `Microsoft.Advisor`리소스를 포함합니다. |
|경고관리리소스 |와 _관련된_ `Microsoft.AlertsManagement`리소스를 포함합니다. |
|유지 보수 리소스 |와 _관련된_ `Microsoft.Maintenance`리소스를 포함합니다. |
|보안 리소스 |와 _관련된_ `Microsoft.Security`리소스를 포함합니다. |

리소스 유형을 포함한 전체 목록은 [참조: 지원되는 테이블 및 리소스 형식을](../reference/supported-tables-resources.md)참조하십시오.

> [!NOTE]
> _리소스가_ 기본 테이블입니다. _Resources_ 테이블을 쿼리하는 동안 사용되지 않는 `join` `union` 한 테이블 이름을 제공할 필요가 없습니다. 그러나 권장되는 방법은 항상 쿼리에 초기 테이블을 포함하는 것입니다.

포털에서 리소스 그래프 탐색기를 사용하여 각 테이블에서 사용할 수 있는 리소스 유형을 검색합니다. 또는 사용자 환경에 있는 지정된 `<tableName> | distinct type` Resource Graph 테이블 지원 리소스 유형 목록을 가져옵니다.

다음 쿼리는 간단한 `join`을 보여 주며. 쿼리 결과는 열을 함께 혼합하고 이 예제의 조인된 테이블의 중복 된 열 이름인 _ResourceContainers는_ **1로**추가됩니다. _ResourceContainers_ 테이블에 구독 및 리소스 그룹에 대한 형식이 모두 있기 때문에 리소스 _테이블에서_ 리소스에 조인하는 데 두 형식 중 하나를 사용할 수 있습니다.

```kusto
Resources
| join ResourceContainers on subscriptionId
| limit 1
```

다음 쿼리는 의 보다 `join`복잡한 사용을 보여 주며 의 사용을 보여 주며 있습니다. 이 쿼리는 조인된 테이블을 구독 리소스로 제한하고 `project`를 사용하여 원래 필드 _subscriptionId_ 및 _SubName_으로 이름이 바뀐 _name_ 필드만 포함합니다. 필드 이름이 바짐은 필드가 `join` _리소스에_이미 있으므로 _name1로_ 추가하지 않습니다. 원래 테이블은 `where`를 사용하여 필터링되고 다음 `project`에는 두 테이블의 열이 포함됩니다. 쿼리 결과는 유형, 키 자격 증명 모음 이름 및 해당하는 구독 이름을 표시하는 단일 키 자격 증명 모음입니다.

```kusto
Resources
| where type == 'microsoft.keyvault/vaults'
| join (ResourceContainers | where type=='microsoft.resources/subscriptions' | project SubName=name, subscriptionId) on subscriptionId
| project type, name, SubName
| limit 1
```

> [!NOTE]
> `join` 결과를 `project`로 `join` 제한할 때 두 테이블을 관련시키는 데 사용되는 속성인 _subscriptionId를_ 위의 `project`예제에서 에 포함해야 합니다.

## <a name="supported-kql-language-elements"></a>지원되는 KQL 언어 요소

리소스 그래프는 모든 KQL [데이터 유형,](/azure/kusto/query/scalar-data-types/) [스칼라 함수,](/azure/kusto/query/scalarfunctions) [스칼라 연산자](/azure/kusto/query/binoperators)및 [집계 함수를](/azure/kusto/query/any-aggfunction)지원합니다. 특정 [테이블 형식 연산자는](/azure/kusto/query/queries) 리소스 그래프에서 지원되며 그 중 일부는 다른 동작을 갖습니다.

### <a name="supported-tabulartop-level-operators"></a>지원되는 테이블 형식/최상위 연산자

다음은 특정 샘플이 있는 리소스 그래프에서 지원하는 KQL 테이블 형식 연산자 목록입니다.

|KQL |리소스 그래프 샘플 쿼리 |메모 |
|---|---|---|
|[count](/azure/kusto/query/countoperator) |[키 볼트 수](../samples/starter.md#count-keyvaults) | |
|[별개](/azure/kusto/query/distinctoperator) |[특정 별칭에 대해 고유한 값 표시](../samples/starter.md#distinct-alias-values) | |
|[확장](/azure/kusto/query/extendoperator) |[OS 유형별로 가상 머신 개수 계산](../samples/starter.md#count-os) | |
|[가입](/azure/kusto/query/joinoperator) |[구독 이름이 있는 키 자격 증명 모음](../samples/advanced.md#join) |지원되는 맛에 가입하십시오 : [내부 고유,](/azure/kusto/query/joinoperator#default-join-flavor) [내부,](/azure/kusto/query/joinoperator#inner-join) [왼쪽 외부](/azure/kusto/query/joinoperator#left-outer-join). 단일 쿼리에서 3개로 `join` 제한됩니다. 브로드캐스트 조인과 같은 사용자 지정 조인 전략은 허용되지 않습니다. 단일 테이블 내에서 또는 _리소스_ 및 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[제한](/azure/kusto/query/limitoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |의 동의어`take` |
|[mvexpand](/azure/kusto/query/mvexpandoperator) | | 레거시 연산자, 대신 사용합니다. `mv-expand` _RowLimit_ 최대 400입니다. 기본값은 128입니다. |
|[mv 확장](/azure/kusto/query/mvexpandoperator) |[특정 쓰기 위치와 코스모스 DB 목록](../samples/advanced.md#mvexpand-cosmosdb) |_RowLimit_ 최대 400입니다. 기본값은 128입니다. |
|[순서](/azure/kusto/query/orderoperator) |[리소스를 이름별로 정렬하여 나열](../samples/starter.md#list-resources) |의 동의어`sort` |
|[프로젝트](/azure/kusto/query/projectoperator) |[리소스를 이름별로 정렬하여 나열](../samples/starter.md#list-resources) | |
|[project-away](/azure/kusto/query/projectawayoperator) |[결과에서 열 제거](../samples/advanced.md#remove-column) | |
|[정렬](/azure/kusto/query/sortoperator) |[리소스를 이름별로 정렬하여 나열](../samples/starter.md#list-resources) |의 동의어`order` |
|[요약](/azure/kusto/query/summarizeoperator) |[Azure 리소스 개수 계산](../samples/starter.md#count-resources) |단순화된 첫 페이지만 |
|[테이크](/azure/kusto/query/takeoperator) |[모든 공용 IP 주소 나열](../samples/starter.md#list-publicip) |의 동의어`limit` |
|[맨 위로](/azure/kusto/query/topoperator) |[이름 및 해당 OS 유형별로 처음 5개의 가상 머신 표시](../samples/starter.md#show-sorted) | |
|[연합](/azure/kusto/query/unionoperator) |[두 쿼리의 결과를 단일 결과로 결합](../samples/advanced.md#unionresults) |단일 테이블 _T_ `| union` \[ `kind=` `inner` \| `outer` \] \[허용: T `withsource=` _열 이름_ \] _테이블._ 단일 쿼리에서 3개의 `union` 다리 제한입니다. 다리 테이블의 `union` 퍼지 해상도는 허용되지 않습니다. 단일 테이블 내에서 또는 _리소스_ 및 _ResourceContainers_ 테이블 간에 사용할 수 있습니다. |
|[where](/azure/kusto/query/whereoperator) |[스토리지를 포함하는 리소스 표시](../samples/starter.md#show-storage) | |

## <a name="escape-characters"></a>이스케이프 문자

`.` 또는 `$`을 포함하는 속성 이름과 같은 일부 속성 이름은 쿼리에서 래핑되거나 이스케이프되어야 하거나 속성 이름이 잘못 해석되어 예상된 결과를 제공하지 않습니다.

- `.`- 다음과 같이 속성 이름을 래핑합니다.`['propertyname.withaperiod']`
  
  속성 _odata.type을_래핑하는 예제 쿼리 :

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`- 속성 이름의 문자를 이스케이프합니다. 사용되는 이스케이프 문자는 리소스 그래프에서 실행되는 셸에 따라 다릅니다.

  - **Bash** - `\`

    bash에서 속성 _ \$형식을_ 이스케이프하는 쿼리 예제:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** - `$` 문자를 이스케이프하지 마십시오.

  - **Powershell** - ``` ` ```

    PowerShell에서 속성 _ \$형식을_ 이스케이프하는 쿼리 예제:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리에서](../samples/starter.md)사용 되는 언어를 참조 하십시오.
- [고급 쿼리에서](../samples/advanced.md)고급 용도를 참조하십시오.
- [리소스 검색](explore-resources.md) 방법에 대해 자세히 알아보기