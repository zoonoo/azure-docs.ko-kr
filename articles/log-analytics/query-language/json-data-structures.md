---
title: Azure Log Analytics 쿼리에서 문자열 사용 | Microsoft Docs
description: 이 문서에서는 Analytics 포털을 사용하여 Log Analytics에서 쿼리를 작성하는 것에 대한 자습서를 제공합니다.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/16/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: f027754f26a9063aa5faa548fd01576624811005
ms.sourcegitcommit: f057c10ae4f26a768e97f2cb3f3faca9ed23ff1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2018
ms.locfileid: "40190946"
---
# <a name="working-with-json-and-data-structures-in-log-analytics-queries"></a>Log Analytics 쿼리에서 JSON 및 데이터 구조 사용

> [!NOTE]
> 이 단원을 완료하기 전에 [Analytics 포털 시작](get-started-analytics-portal.md) 및 [쿼리 시작](get-started-queries.md)을 완료해야 합니다.


중첩된 개체는 배열 또는 키-값 쌍 맵에 다른 개체를 포함하는 개체입니다. 이러한 개체는 JSON 문자열로 표시됩니다. 이 문서에서는 JSON이 데이터를 검색하고 중첩된 개체를 분석하는 데 사용되는 방법을 설명합니다.

## <a name="working-with-json-strings"></a>JSON 문자열 사용
알려진 경로의 특정 JSON 요소에 액세스하려면 `extractjson`을 사용합니다. 이 함수에는 다음 규칙을 사용하는 경로 식이 필요합니다.

- 루트 폴더를 나타내려면 _$_ 를 사용합니다.
- 다음 예제와 같이 인덱스 및 요소를 나타내려면 대괄호 또는 점 표기법을 사용합니다.


인덱스에는 대괄호를 사용하고 요소를 구분할 때는 점을 사용합니다.

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report
| extend status = extractjson("$.hosts[0].status", hosts_report)
```

다음은 대괄호 표기법만 사용할 때와 같은 결과를 나타냅니다.

```OQL
let hosts_report='{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}';
print hosts_report 
| extend status = extractjson("$['hosts'][0]['status']", hosts_report)
```

요소가 하나만 있으면 점 표기법만 사용해도 됩니다.

```OQL
let hosts_report='{"location":"North_DC", "status":"running", "rate":5}';
print hosts_report 
| extend status = hosts_report.status
```


## <a name="working-with-objects"></a>개체 사용

### <a name="parsejson"></a>parsejson
Json 구조에서 여러 요소에 액세스하려면 동적 개체로 액세스하는 것이 더 쉽습니다. 텍스트 데이터를 동적 개체로 캐스팅하려면 `parsejson`을 사용합니다. 동적 형식으로 변환되면 추가 함수를 사용하여 데이터를 분석할 수 있습니다.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend status0=hosts_object.hosts[0].status, rate1=hosts_object.hosts[1].rate
```



### <a name="arraylength"></a>arraylength
배열 내의 요소 수를 계산하려면 `arraylength`를 사용합니다.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| extend hosts_num=arraylength(hosts_object.hosts)
```

### <a name="mvexpand"></a>mvexpand
개체의 속성을 별도 행으로 구분하려면 `mvexpand`를 사용합니다.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| mvexpand hosts_object.hosts[0]
```

![mvexpand](media/json-data-structures/mvexpand.png)

### <a name="buildschema"></a>buildschema
개체의 모든 값을 허용하는 스키마를 가져오려면 `buildschema`를 사용합니다.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"location":"South_DC", "status":"stopped", "rate":3}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```

출력은 JSON 형식의 스키마입니다.
```json
{
    "hosts":
    {
        "indexer":
        {
            "location": "string",
            "rate": "int",
            "status": "string"
        }
    }
}
```
이 출력은 개체 필드의 이름과 일치하는 데이터 형식을 설명합니다. 

중첩된 개체는 다음 예제와 같이 스키마가 서로 다를 수 있습니다.

```OQL
let hosts_object = parsejson('{"hosts": [{"location":"North_DC", "status":"running", "rate":5},{"status":"stopped", "rate":"3", "range":100}]}');
print hosts_object 
| summarize buildschema(hosts_object)
```


![스키마 빌드](media/json-data-structures/buildschema.png)

## <a name="next-steps"></a>다음 단계
Log Analytics 쿼리 언어를 사용에 대해서는 다른 단원을 참조하세요.

- [문자열 작업](string-operations.md)
- [날짜 및 시간 작업](datetime-operations.md)
- [집계 함수](aggregations.md)
- [고급 집계](advanced-aggregations.md)
- [고급 쿼리 작성](advanced-query-writing.md)
- [조인](joins.md)
- [차트](charts.md)