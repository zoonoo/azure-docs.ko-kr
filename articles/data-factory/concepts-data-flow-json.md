---
title: 데이터 흐름 JSON 개념 Azure Data Factory 매핑
description: Data Factory 매핑 데이터 흐름에는 계층 구조를 사용 하 여 JSON 문서를 처리 하는 기본 제공 기능이 있습니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 08/30/2019
ms.openlocfilehash: 69dce46052c18eec7c3f1fa2082907ed62b367d6
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703336"
---
# <a name="mapping-data-flow-json-handling"></a>데이터 흐름 JSON 처리 매핑

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="creating-json-structures-in-expression-editor"></a>식 편집기에서 JSON 구조 만들기
### <a name="derived-column-transformation"></a>파생 열 변환
파생 열 식 편집기를 통해 복잡 한 열을 데이터 흐름에 추가 하는 것이 더 쉽습니다. 새 열을 추가 하 고 편집기를 연 후에는 두 가지 옵션이 있습니다. JSON 구조를 수동으로 입력 하거나 UI를 사용 하 여 하위 열을 대화형으로 추가 합니다.

#### <a name="interactive-ui-json-design"></a>대화형 UI JSON 디자인
출력 스키마 쪽 창에서 새 하위 열은 `+` 메뉴를 사용 하 여 추가할 수 있습니다. 하위 ![열 추가 하위](media/data-flow/addsubcolumn.png "열 추가")

여기에서 새 열과 하위 열을 동일한 방식으로 추가할 수 있습니다. 복합 필드가 아닌 각 필드에 대해 식 편집기의 오른쪽에 식을 추가할 수 있습니다.

![복합 열](media/data-flow/complexcolumn.png "복합 열")

#### <a name="manual-json-design"></a>수동 JSON 디자인
JSON 구조를 수동으로 추가 하려면 새 열을 추가 하 고 편집기에 식을 입력 합니다. 식의 일반적인 형식은 다음과 같습니다.
```
@(
    field1=0,
    field2=@(
        field1=0
    )
)
```
"ComplexColumn" 이라는 열에 대해이 식을 입력 한 경우 다음 JSON으로 싱크에 기록 됩니다.
```
{
    "complexColumn": {
        "field1": 0,
        "field2": {
            "field1": 0
        }
    }
}
```

#### <a name="sample-manual-script-for-complete-hierarchical-definition"></a>전체 계층 정의에 대 한 예제 수동 스크립트
```
@(
    title=Title,
    firstName=FirstName,
    middleName=MiddleName,
    lastName=LastName,
    suffix=Suffix,
    contactDetails=@(
        email=EmailAddress,
        phone=Phone
    ),
    address=@(
        line1=AddressLine1,
        line2=AddressLine2,
        city=City,
        state=StateProvince,
        country=CountryRegion,
        postCode=PostalCode
    ),
    ids=[
        toString(CustomerID), toString(AddressID), rowguid
    ]
)
```

## <a name="source-format-options"></a>원본 형식 옵션
### <a name="default"></a>기본값
```
{ "json": "record 1" }
{ "json": "record 2" }
{ "json": "record 3" }
```

### <a name="single-document"></a>단일 문서
* 옵션 1
```
[
    {
        "json": "record 1"
    },
    {
        "json": "record 2"
    },
    {
        "json": "record 3"
    }
]
```

* 옵션 2
```
File1.json
{
    "json": "record 1"
}
File2.json
{
    "json": "record 2"
}
File3.json
{
    "json": "record 3"
}
```

### <a name="unquoted-column-names"></a>따옴표 붙지 않은 열 이름
```
{ json: "record 1" }
{ json: "record 2" }
{ json: "record 3" }
```

### <a name="has-comments"></a>설명 있음
```
{ "json": /** comment **/ "record 1" }
{ "json": "record 2" }
{ /** comment **/ "json": "record 3" }
```

### <a name="single-quoted"></a>작은따옴표
```
{ 'json': 'record 1' }
{ 'json': 'record 2' }
{ 'json': 'record 3' }
```

### <a name="backslash-escaped"></a>백슬래시가 이스케이프 되었습니다.
```
{ "json": "record 1" }
{ "json": "\} \" \' \\ \n \\n record 2" }
{ "json": "record 3" }
```

## <a name="higher-order-functions"></a>고차 함수
## <a name="filter"></a>filter
제공 된 조건자를 충족 하지 않는 배열에서 요소를 필터링 합니다. 필터에는 #item 조건자 함수의 한 요소에 대 한 참조가 필요 합니다.

### <a name="examples"></a>예
```
filter([1, 2, 3, 4], #item > 2) => [3, 4]
filter(['a', 'b', 'c', 'd'], #item == 'a' || #item == 'b') => ['a', 'b']
```

## <a name="map"></a>지도(map)
제공 된 식을 사용 하 여 배열의 각 요소를 새 요소에 매핑합니다. Map에는 #item 식 함수에서 하나의 요소에 대 한 참조가 필요 합니다.

### <a name="examples"></a>예
```
map([1, 2, 3, 4], #item + 2) => [3, 4, 5, 6]
map(['a', 'b', 'c', 'd'], #item + '_processed') => ['a_processed', 'b_processed', 'c_processed', 'd_processed']
```

## <a name="reduce"></a>줄이십시오
배열의 요소를 누적 합니다. 줄이기를 사용 하면 첫 번째 식 함수에서 누적기 및 one 요소에 대 한 참조가 #acc 및 #item으로 예상 되며 결과 값이 두 번째 식 함수에서 사용 될 #result으로 예상 됩니다.

### <a name="examples"></a>예
```
reduce([1, 2, 3, 4], 0, #acc + #item, #result) => 10
reduce(['1', '2', '3', '4'], '0', #acc + #item, #result) => '01234'
reduce([1, 2, 3, 4], 0, #acc + #item, #result + 15) => 25
```

## <a name="sort"></a>sort
제공 된 조건자 함수를 사용 하 여 배열을 정렬 합니다. Sort는 #item1 및 #item2 식 함수에서 두 개의 연속 된 요소에 대 한 참조가 필요 합니다.

### <a name="examples"></a>예
```
sort([4, 8, 2, 3], compare(#item1, #item2)) => [2, 3, 4, 8]
sort(['a3', 'b2', 'c1'],
        iif(right(#item1, 1) >= right(#item2, 1), 1, -1)) => ['c1', 'b2', 'a3']
sort(['a3', 'b2', 'c1'],
        iif(#item1 >= #item2, 1, -1)) => ['a3', 'b2', 'c1']
```

## <a name="contains"></a>다음 값을 포함하는 경우
제공 된 배열의 요소가 제공 된 조건자에서 true로 평가 되 면 true를 반환 합니다. Contains에는 #item 조건자 함수의 한 요소에 대 한 참조가 필요 합니다.

### <a name="examples"></a>예
```
contains([1, 2, 3, 4], #item == 3) => true
contains([1, 2, 3, 4], #item > 5) => false
```

## <a name="next-steps"></a>다음 단계

* [파생 열 변환을 사용 하 여 계층 구조를 작성 합니다.](data-flow-derived-column.md)
