---
title: Azure Event Grid에 대한 이벤트 필터링
description: Azure Event Grid 구독을 만들 때 이벤트를 필터링하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 94445341891149d5d02c7f33caef20bf45123e9b
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/05/2021
ms.locfileid: "102197778"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Event Grid 구독에 대한 이벤트 필터링 이해

이 문서에서는 엔드포인트에 전송되는 이벤트를 필터링하는 다양한 방법을 설명합니다. 이벤트 구독을 만들 때 필터링에 대한 세 가지 옵션이 있습니다.

* 일정 유형
* 다음 값으로 시작하거나 끝나는 제목
* 고급 필드 및 연산자

## <a name="event-type-filtering"></a>이벤트 유형 필터링

기본적으로 이벤트 원본에 대한 모든 [이벤트 유형](event-schema.md)은 엔드포인트로 전송됩니다. 특정 이벤트 유형만 엔드포인트에 보내도록 결정할 수 있습니다. 예를 들어 리소스에 대한 업데이트 알림을 받을 수 있지만 삭제와 같은 다른 작업에 대한 알림을 받을 수 없습니다. 이런 경우 `Microsoft.Resources.ResourceWriteSuccess` 이벤트 유형별로 필터링합니다. 이벤트 유형으로 배열을 제공하거나 이벤트 원본에 대한 모든 이벤트 유형을 가져오도록 `All`을 지정합니다.

이벤트 유형별로 필터링에 대한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>제목 필터링

제목별 간단한 필터링을 위해 제목에 대한 시작 또는 끝 값을 지정합니다. 예를 들어 `.txt`로 끝나는 제목을 지정하여 스토리지 계정에 텍스트 파일 업로드와 관련된 이벤트만을 가져올 수 있습니다. 또는 `/blobServices/default/containers/testcontainer`로 시작하는 제목을 필터링하여 스토리지 계정에 다른 컨테이너가 아닌 해당 컨테이너에 대한 모든 이벤트를 가져올 수 있습니다.

사용자 지정 항목에 이벤트를 게시할 때 구독자가 이벤트에 관심이 있는지 더 쉽게 알 수 있도록 사용자 이벤트에 대한 제목을 만듭니다. 구독자는 제목 속성을 사용하여 이벤트를 필터링 및 라우팅합니다. 구독자가 해당 경로의 세그먼트를 기준으로 필터링할 수 있도록 이벤트가 발생하는 경로를 추가하는 것을 고려합니다. 구독자는 경로를 통해 이벤트를 제한적이거나 광범위하게 필터링할 수 있습니다. 제목에 `/A/B/C`와 같은 3개의 세그먼트 경로를 제공하는 경우 구독자는 첫 번째 세그먼트 `/A`를 기준으로 필터링하여 광범위한 이벤트 집합을 가져올 수 있습니다. 구독자는 `/A/B/C` 또는 `/A/D/E`와 같은 제목이 있는 이벤트를 가져옵니다. 다른 구독자는 `/A/B`를 기준으로 필터링하여 제한된 이벤트 집합을 얻을 수 있습니다.

제목별로 필터링 하기 위한 JSON 구문은 다음과 같습니다.

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>고급 필터링

데이터 필드에서 값을 기준으로 필터링하고 비교 연산자를 지정하려면 고급 필터링 옵션을 사용합니다. 고급 필터링에서 다음을 지정합니다.

* 연산자 형식 - 비교의 형식입니다.
* 키 - 필터링에 사용하는 이벤트 데이터의 필드입니다. 숫자, 부울, 문자열 또는 배열일 수 있습니다.
* values-키와 비교할 값입니다.

## <a name="key"></a>키
키는 필터링에 사용 중인 이벤트 데이터의 필드입니다. 다음 유형 중 하나일 수 있습니다.

- 숫자
- 부울
- String
- Array입니다. `enableAdvancedFilteringOnArrays`이 기능을 사용 하려면 속성을 true로 설정 해야 합니다. 현재 Azure Portal이 기능 사용을 지원 하지 않습니다. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

**Event Grid 스키마** 의 이벤트에 대해,,,, `ID` `Topic` `Subject` `EventType` `DataVersion` 또는 이벤트 데이터 ( `data.key1` 예:)에 대해 다음 값을 사용 합니다.

**클라우드 이벤트 스키마** 의 이벤트에 대해 키에 `eventid` ,, `source` `eventtype` , `eventtypeversion` 또는 이벤트 데이터 값 (예:)을 사용 `data.key1` 합니다.

**사용자 지정 입력 스키마** 의 경우와 같이 이벤트 데이터 필드를 사용 `data.key1` 합니다. 데이터 섹션의 필드에 액세스 하려면 `.` (점) 표기법을 사용 합니다. 예를 들어 `data.sitename` , `data.appEventTypeDetail.action` `sitename` `action` 다음 샘플 이벤트에 대해 또는에 액세스 합니다.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>값
값은 number, string, boolean 또는 array 일 수 있습니다.

## <a name="operators"></a>연산자

**숫자** 에 사용할 수 있는 연산자는 다음과 같습니다.

## <a name="numberin"></a>NumberIn
**키** 값이 지정 된 **필터** 값 중 하나인 경우 숫자 in 연산자는 true로 평가 됩니다. 다음 예제에서는 `counter` 섹션의 특성 값이 5 또는 1 인지 여부를 확인 `data` 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a, b, c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
**키** 값이 지정 된 **필터** 값이 **아닌** 경우에는 숫자 notin이 true로 평가 됩니다. 다음 예제에서는 `counter` 섹션의 특성 값이 41 및 0이 아닌지 여부를 확인 `data` 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a, b, c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
NumberLessThan 연산자는 **키** 값이 지정 된 **필터** 값 **보다 작은** 경우 true로 평가 됩니다. 다음 예제에서는 섹션의 특성 값이 100 미만인 지 여부를 확인 `counter` `data` 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값에 대해 검사 됩니다. 키가 포함 된 의사 코드 `[v1, v2, v3]` 는 다음과 같습니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
NumberGreaterThan 연산자는 **키** 값이 지정 된 **필터** 값 **보다 큰** 경우 true로 평가 됩니다. 다음 예제에서는 `counter` 섹션의 특성 값 `data` 이 20 보다 큰지 여부를 확인 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값에 대해 검사 됩니다. 키가 포함 된 의사 코드 `[v1, v2, v3]` 는 다음과 같습니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
NumberLessThanOrEquals 연산자는 **키** 값이 지정 된 **필터** 값 **보다 작거나 같으면** true로 평가 됩니다. 다음 예제에서는 `counter` 섹션의 특성 값 `data` 이 100 보다 작거나 같은지 여부를 확인 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값에 대해 검사 됩니다. 키가 포함 된 의사 코드 `[v1, v2, v3]` 는 다음과 같습니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
NumberGreaterThanOrEquals 연산자는 **키** 값이 지정 된 **필터** 값 **보다 크거나 같으면** true로 평가 됩니다. 다음 예제에서는 `counter` 섹션의 특성 값 `data` 이 30 보다 크거나 같은지 여부를 확인 합니다. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값에 대해 검사 됩니다. 키가 포함 된 의사 코드 `[v1, v2, v3]` 는 다음과 같습니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>숫자 범위
**키** 값이 지정 된 **필터 범위** 중 하나에 있으면 숫자 inrange 연산자가 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값 `data` 이 두 범위 (3.14159-999.95, 3000-4000) 중 하나에 있는지 여부를 확인 합니다. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`속성은 범위의 배열입니다. 이전 예제에서는 두 범위의 배열입니다. 확인할 범위가 하나 있는 배열의 예는 다음과 같습니다. 

**범위가 1 인 배열:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 키를 포함 하는 의사 코드는 `[v1, v2, v3]` 및 필터: 범위의 배열입니다. 이 의사 코드에서 `a` 및 `b` 는 배열의 각 범위에서 낮은 값과 높은 값입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>숫자 Notinrange
**키** 값이 지정 된 **필터 범위** 에 **속하지 않는** 경우에는 숫자 notinrange 연산자가 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값 `data` 이 두 범위 (3.14159-999.95, 3000-4000) 중 하나에 있는지 여부를 확인 합니다. 인 경우 연산자는 false를 반환 합니다. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`속성은 범위의 배열입니다. 이전 예제에서는 두 범위의 배열입니다. 확인할 범위가 하나 있는 배열의 예는 다음과 같습니다.

**범위가 1 인 배열:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 키를 포함 하는 의사 코드는 `[v1, v2, v3]` 및 필터: 범위의 배열입니다. 이 의사 코드에서 `a` 및 `b` 는 배열의 각 범위에서 낮은 값과 높은 값입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


**부울** 에 사용할 수 있는 연산자는 다음과 같습니다. 

## <a name="boolequals"></a>BoolEquals
BoolEquals 연산자는 **키** 값이 지정 된 부울 값 **필터** 이면 true로 평가 됩니다. 다음 예제에서는 섹션의 특성 값이 인지 여부를 확인 `isEnabled` `data` `true` 합니다. 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

키가 배열인 경우 배열의 모든 값은 필터 부울 값을 기준으로 검사 됩니다. 키가 포함 된 의사 코드 `[v1, v2, v3]` 는 다음과 같습니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

**문자열** 에 사용할 수 있는 연산자는 다음과 같습니다.

## <a name="stringcontains"></a>StringContains
**Stringcontains** 는 **키** 값에 지정 된 **필터** 값 (부분 문자열)이 **포함** 된 경우 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값이 `data` 지정 된 부분 문자열 중 하나 ( `microsoft` 또는)를 포함 하는지 확인 `azure` 합니다. 예를 들어,에 `azure data factory` 는가 `azure` 있습니다. 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
**Stringnotcontains** 연산자는 지정 된 **필터** 값을 하위 문자열로 **포함 하지** **않는 경우** true로 평가 됩니다. 키에 지정 된 값 중 하나를 하위 문자열로 포함 하는 경우 연산자는 false로 평가 됩니다. 다음 예제에서 연산자는 `key1` 섹션의 특성 값에 `data` `contoso` 및 부분 문자열이 없는 경우에만 true를 반환 합니다 `fabrikam` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```

## <a name="stringbeginswith"></a>StringBeginsWith
**StringBeginsWith** 연산자는 **키** 값이 지정 된 **필터** 값 **으로 시작** 하는 경우 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값이 `data` 또는로 시작 하는지 여부를 확인 합니다 `event` `grid` . 예를 들어는 `event hubs` 로 시작 `event` 합니다.  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
**StringNotBeginsWith** 연산자는 **키** 값이 지정 된 **필터** 값 **으로 시작 하지** 않는 경우 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값이 `data` 또는로 시작 하지 않는지 확인 합니다 `event` `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
**Stringendswith** 연산자는 **키** 값이 지정 된 **필터** 값 중 하나로 **끝나는** 경우 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값이 `data` 또는 또는로 끝나는지 여부를 확인 `jpg` `jpeg` `png` 합니다. 예를 들어는 `eventgrid.png` 로 끝납니다 `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
**키** 값이 지정 된 **필터** 값 **으로 끝나지** 않는 경우 **stringnotendswith** 연산자는 true로 평가 됩니다. 다음 예제에서는 `key1` 섹션의 특성 값이 `data` 또는 또는로 끝나지 않는지 여부를 확인 합니다 `jpg` `jpeg` `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>StringIn
**Stringin** 연산자는 **키** 값이 지정 된 **필터** 값 중 하 나와 **정확 하 게 일치** 하는지 여부를 확인 합니다. 다음 예제에서는 `key1` 섹션의 특성 값 `data` 이 또는 인지 여부를 확인 합니다 `exact` `string` `matches` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
**Stringnotin** 연산자는 **키** 값이 지정 된 **필터** 값 **과 일치 하지** 않는지 여부를 확인 합니다. 다음 예제에서는 섹션의 특성 값이 및이 아닌지 여부를 확인 합니다 `key1` `data` `aws` `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

키가 배열인 경우 배열의 모든 값은 필터 값의 배열에 대해 검사 됩니다. 다음은 키가 있는 의사 코드 `[v1, v2, v3]` 와 필터 `[a,b,c]` 입니다. 필터의 데이터 형식과 일치 하지 않는 데이터 형식의 키 값은 모두 무시 됩니다.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


모든 문자열 비교는 대/소문자를 구분 하지 않습니다.

> [!NOTE]
> 이벤트 JSON에 고급 필터 키가 포함 되어 있지 않으면 필터는 다음 연산자에 대해 **일치 하지 않는** 것으로 Evaulated. NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, 숫자 In, BoolEquals, stringcontains, StringNotContains, StringBeginsWith, StringNotBeginsWith, Stringcontains, StringNotEndsWith, stringcontains.
> 
>다음 연산자와 **일치** 하는 필터를 evaulated 합니다.


## <a name="isnullorundefined"></a>IsNullOrUndefined
키의 값이 NULL 이거나 정의 되지 않은 경우 IsNullOrUndefined 연산자가 true로 평가 됩니다. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

다음 예에서는 key1이 없으므로 연산자가 true로 평가 됩니다. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

다음 예에서 key1은 null로 설정 되므로 연산자가 true로 평가 됩니다.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

이 예에서는 key1에 다른 값이 있는 경우 연산자가 false로 평가 됩니다. 

## <a name="isnotnull"></a>IsNotNull
IsNotNull 연산자는 키의 값이 NULL 이거나 정의 되지 않은 경우 true로 평가 됩니다. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>OR 및 및
여러 값이 있는 단일 필터를 지정 하는 경우 **또는** 작업이 수행 되므로 키 필드의 값은 다음 값 중 하나 여야 합니다. 예를 들면 다음과 같습니다.

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

여러 필터를 지정 하는 경우 **및** 작업이 수행 되므로 각 필터 조건이 충족 되어야 합니다. 예를 들면 다음과 같습니다. 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
**CloudEvents 스키마** 의 이벤트에 대해,,, `eventid` `source` `eventtype` `eventtypeversion` 또는 이벤트 데이터 ( `data.key1` 예:)에 대해 다음 값을 사용 합니다. 

[CloudEvents 1.0에서 확장 컨텍스트 특성](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes)을 사용할 수도 있습니다. 다음 예제에서 `comexampleextension1` 및 `comexampleothervalue` 는 확장 컨텍스트 특성입니다. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

다음은 필터에 확장 컨텍스트 특성을 사용 하는 예입니다.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>제한 사항

고급 필터링에는 다음과 같은 제한이 있습니다.

* 5 이벤트 그리드 구독 당 모든 필터의 고급 필터 및 25 필터 값
* 문자열 값당 512자
* **in** 및 **not in** 연산자에 대한 5개의 값
* 문자에 **`.` (점)** 이 있는 키입니다. 예를 들어 `http://schemas.microsoft.com/claims/authnclassreference` 또는 `john.doe@contoso.com`입니다. 현재는 키에 이스케이프 문자를 사용할 수 없습니다. 

둘 이상의 필터에 동일한 키를 사용할 수 있습니다.





## <a name="next-steps"></a>다음 단계

* PowerShell 및 Azure CLI를 사용하여 이벤트 필터링에 대해 알아보려면 [Event Grid에 대한 이벤트 필터링](how-to-filter-events.md)을 참조하세요.
* Event Grid를 빠르게 시작하려면 [Azure Event Grid를 사용하여 사용자 지정 이벤트 만들기 및 라우팅](custom-event-quickstart.md)을 참조하세요.
