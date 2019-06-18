---
title: Azure Monitor에서 효율적인 로그 쿼리 작성 | Microsoft Docs
description: Log Analytics에서 쿼리를 작성하는 방법을 위한 리소스에 대한 참조입니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
ms.author: bwren
ms.openlocfilehash: 25d6b582ed4d4e24df3841f4191471296e25abd8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60519379"
---
# <a name="writing-efficient-log-queries-in-azure-monitor"></a>Azure Monitor에서 효율적인 로그 쿼리 작성
이 문서에서는 Azure Monitor에서 로그 쿼리를 효율적으로 작성하기 위한 권장 사항을 제공합니다. 이러한 전략을 사용하여 최소의 오버헤드로 쿼리를 신속하게 실행할 수 있습니다.

## <a name="scope-your-query"></a>쿼리 범위 지정
쿼리가 실제로 필요한 것보다 더 많은 데이터를 처리하도록 하면 장기 실행 쿼리가 되며, 결과에 너무 많은 데이터가 포함되어 효과적으로 분석하지 못할 수 있습니다. 극단적인 경우, 쿼리 시간이 초과되어 실패할 수도 있습니다.

### <a name="specify-your-data-source"></a>데이터 원본 지정
효율적인 쿼리를 작성하는 첫 번째 단계는 필수 데이터 원본으로 범위를 제한하는 것입니다. 테이블 지정은 광범위한 텍스트 검색(예: `search *`)을 실행하는 것보다 항상 선호되는 방식입니다. 특정 테이블을 쿼리하려면 다음과 같은 테이블 이름으로 쿼리를 시작합니다.

``` Kusto
requests | ...
```

다음과 같이 쿼리에서 [검색](/azure/kusto/query/searchoperator)을 사용하여 특정 테이블의 여러 열에서 값을 검색할 수 있습니다.

``` Kusto
search in (exceptions) "The server was not found"

search in (exceptions, customEvents) "timeout"
```

다음과 같이 여러 테이블을 쿼리하려면 [union](/azure/kusto/query/unionoperator)을 사용합니다.

``` Kusto
union requests, traces | ...
```

### <a name="specify-a-time-range"></a>시간 범위 지정
또한 필요한 데이터의 시간 범위로 쿼리를 제한해야 합니다. 기본적으로 쿼리는 지난 24시간 동안 수집한 데이터를 포함합니다. [시간 범위 선택기](get-started-portal.md#select-a-time-range)에서 해당 옵션을 변경하거나 쿼리에 명시적으로 추가할 수 있습니다. 쿼리의 나머지 부분이 해당 범위 내의 데이터만 처리하도록 테이블 이름 바로 다음에 시간 필터를 추가하는 것이 좋습니다.

``` Kusto
requests | where timestamp > ago(1h)

requests | where timestamp between (ago(1h) .. ago(30m))
```
   
### <a name="get-only-the-latest-records"></a>최신 레코드만 가져오기

최신 레코드만 반환하려면 다음 쿼리와 같이  traces 테이블에 기록된 최신 10개 레코드를 반환하는 top 연산자를 사용합니다. 

``` Kusto
traces | top 10 by timestamp
```

   
### <a name="filter-records"></a>레코드 필터링
지정된 조건과 일치하는 로그만 검토하려면 다음 쿼리와 같이 _severityLevel_ 값이 0보다 큰 레코드만 반환하는 where 연산자를 사용합니다. 

``` Kusto
traces | where severityLevel > 0
```



## <a name="string-comparisons"></a>문자열 비교
[문자열을 평가](/azure/kusto/query/datatypes-string-operators)할 경우 일반적으로 전체 토큰을 찾을 때 `contains` 대신 `has`를 사용해야 합니다. `has`는 부분 문자열을 찾을 필요가 없기 때문에 좀 더 효율적입니다.

## <a name="returned-columns"></a>반환 열

처리할 열 세트를 사용자에게 필요한 범위로 좁히려면 [project](/azure/kusto/query/projectoperator)를 사용합니다.

``` Kusto
traces 
| project timestamp, severityLevel, client_City 
| ...
```

[extend](/azure/kusto/query/extendoperator)를 사용하여 값을 계산하고 사용자의 고유한 열을 만들 수 있지만 일반적으로 테이블 열을 필터링하는 것이 좀 더 효율적입니다.

예를 들어, _operation\_Name_을 필터링하는 아래의 첫 번째 쿼리가 새 _subscription_ 열을 만들고 필터링하는 두 번째 쿼리보다 좀 더 효율적입니다.

``` Kusto
customEvents 
| where split(operation_Name, "/")[2] == "acb"

customEvents 
| extend subscription = split(operation_Name, "/")[2] 
| where subscription == "acb"
```

## <a name="using-joins"></a>조인 사용
[join](/azure/kusto/query/joinoperator) 연산자를 사용하는 경우 더 적은 수의 행이 있는 테이블을 쿼리 왼쪽에 두도록 선택합니다.


## <a name="next-steps"></a>다음 단계
쿼리 모범 사례에 대한 자세한 내용은 [쿼리 모범 사례](/azure/kusto/query/best-practices)를 참조하세요.