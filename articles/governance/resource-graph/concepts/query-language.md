---
title: 쿼리 언어 이해
description: 사용 가능한 Kusto 연산자 및 Azure 리소스 그래프를 사용 하 여 사용 가능한 함수를 설명 합니다.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/22/2019
ms.topic: conceptual
ms.service: resource-graph
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: dcb21a6aedf16b034fad4f0822e22758dda03c33
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800502"
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

몇 개의 속성 이름을 포함 하는 것과 같은 `.` 또는 `$`, 래핑된 이거나 이스케이프 된 쿼리 또는 속성 이름을 올바르게 해석 되 고 예상된 결과 제공 하지 않습니다.

- `.` -속성 이름으로를 래핑하십시오. `['propertyname.withaperiod']`
  
  속성을 래핑하는 예제 쿼리입니다 _odata.type_:

  ```kusto
  where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.['odata.type']
  ```

- `$` -속성 이름에 문자를 이스케이프 합니다. 사용 된 이스케이프 문자 리소스 그래프에서 실행 되는 셸에 따라 달라 집니다.

  - **bash** - `\`

    속성을 이스케이프 하는 예제 쿼리입니다  _\$형식_ bash에서:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.\$type
    ```

  - **cmd** -이스케이프 하지는 `$` 문자입니다.

  - **PowerShell** - ``` ` ```

    속성을 이스케이프 하는 예제 쿼리입니다  _\$형식_ PowerShell에서:

    ```kusto
    where type=~'Microsoft.Insights/alertRules' | project name, properties.condition.`$type
    ```

## <a name="next-steps"></a>다음 단계

- [시작 쿼리](../samples/starter.md)에 사용되는 언어 알아보기
- [고급 쿼리](../samples/advanced.md)의 고급 사용법 알아보기
- [리소스 탐색](explore-resources.md)에 대해 알아보기