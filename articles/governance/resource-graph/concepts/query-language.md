---
title: 쿼리 언어 이해
description: Azure 리소스 그래프에서 사용할 수 있는 Kusto 연산자 및 함수에 대해 설명 합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
ms.openlocfilehash: 54bb0b4f21752b91ceb9d4004c153ff4d95006aa
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2019
ms.locfileid: "71976769"
---
# <a name="understanding-the-azure-resource-graph-query-language"></a>Azure Resource Graph 쿼리 언어 이해

Azure Resource Graph 쿼리 언어는 다양한 연산자 및 함수를 지원합니다. 각각 [Azure Data Explorer](../../../data-explorer/data-explorer-overview.md)를 기반으로 작동합니다.

Resource Graph에서 사용하는 쿼리 언어를 알아보는 가장 좋은 방법은 Azure Data Explorer [쿼리 언어](/azure/kusto/query/index)의 설명서로 시작하는 것입니다. 이 설명서에서는 언어의 구조 및 지원되는 다양한 연산자와 함수의 작동 원리를 설명합니다.

## <a name="supported-tabular-operators"></a>지원되는 테이블 형식 연산자

다음은 Resource Graph에서 지원되는 테이블 형식 연산자 목록입니다.

- [count](/azure/kusto/query/countoperator)
- [distinct](/azure/kusto/query/distinctoperator)
- [extend](/azure/kusto/query/extendoperator)
- [limit](/azure/kusto/query/limitoperator)
- [order by](/azure/kusto/query/orderoperator)
- [project](/azure/kusto/query/projectoperator)
- [project-away](/azure/kusto/query/projectawayoperator)
- [sample](/azure/kusto/query/sampleoperator)
- [sample-distinct](/azure/kusto/query/sampledistinctoperator)
- [sort by](/azure/kusto/query/sortoperator)
- [summarize](/azure/kusto/query/summarizeoperator)
- [take](/azure/kusto/query/takeoperator)
- [top](/azure/kusto/query/topoperator)
- [top-nested](/azure/kusto/query/topnestedoperator)
- [top-hitters](/azure/kusto/query/tophittersoperator)
- [where](/azure/kusto/query/whereoperator)

## <a name="supported-functions"></a>지원되는 함수

다음은 Resource Graph에서 지원되는 함수 목록입니다.

- [ago()](/azure/kusto/query/agofunction)
- [buildschema()](/azure/kusto/query/buildschema-aggfunction)
- [strcat()](/azure/kusto/query/strcatfunction)
- [isnotempty()](/azure/kusto/query/isnotemptyfunction)
- [tostring()](/azure/kusto/query/tostringfunction)
- [zip()](/azure/kusto/query/zipfunction)

## <a name="escape-characters"></a>이스케이프 문자

@No__t-0 또는 `$`을 포함 하는 속성 이름 (예:)은 쿼리에서 래핑해야 하거나 이스케이프 해야 합니다. 그렇지 않으면 속성 이름이 잘못 해석 되 고 예상 된 결과를 제공 하지 않습니다.

- `.`-`['propertyname.withaperiod']`과 같이 속성 이름을 래핑합니다.
  
  _Odata_속성을 래핑하는 쿼리 예제:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$`-속성 이름에서 문자를 이스케이프 합니다. 사용 되는 이스케이프 문자는에서 실행 되는 셸 리소스 그래프에 따라 다릅니다.

  - **bash** - `\`

    Bash에서 _\$type_ 속성을 이스케이프 처리 하는 예제 쿼리입니다.

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -`$` 문자를 이스케이프 하지 않습니다.

  - **PowerShell** - ``` ` ```

    PowerShell에서 _\$type_ 속성을 이스케이프 처리 하는 예제 쿼리입니다.

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어 알아보기
- [고급 쿼리](../samples/advanced.md)의 고급 사용법 알아보기
- [리소스 탐색](explore-resources.md)에 대해 알아보기