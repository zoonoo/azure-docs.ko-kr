---
title: 데이터 흐름 매핑의 결합 변환
description: 평면화 된 변환을 사용 하 여 계층적 데이터 비 정규화
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: a0e75957a0ab49394dab56f2b7fb847dee4b43cb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81413686"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 결합 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

분할 된 변환을 사용 하 여 JSON과 같은 계층 구조 구조 내에서 배열 값을 가져오고 개별 행으로 언 롤 수 있습니다. 이 프로세스를 비 정규화 라고 합니다.

## <a name="configuration"></a>Configuration

평면화 변환에는 다음과 같은 구성 설정이 포함 됩니다.

![설정 평면화](media/data-flow/flatten1.png "설정 평면화")

### <a name="unroll-by"></a>언 롤

언 롤 할 배열을 선택 합니다. 출력 데이터에는 각 배열의 항목당 하나의 행이 포함 됩니다. 입력 행의 언 롤 by 배열이 null 이거나 비어 있으면 unrolled 값이 null 인 출력 행이 하나씩 있게 됩니다.

### <a name="unroll-root"></a>언 롤 루트

기본적으로 평면화 된 변환은 배열에 존재 하는 계층의 맨 위로 배열을 롤백합니다. 필요에 따라 배열을 언 롤 루트로 선택할 수 있습니다. 언 롤 root는 언 롤 배열을 포함 하는 복합 개체의 배열 이어야 합니다. 언 롤 root를 선택 하는 경우 출력 데이터는 언 롤 root의 항목당 하나 이상의 행을 포함 합니다. 입력 행에 언 롤 루트의 항목이 없으면 출력 데이터에서 삭제 됩니다. 언 롤 root를 선택 하면 항상 기본 동작 보다 작거나 같은 수의 행이 출력 됩니다.

### <a name="flatten-mapping"></a>결합 매핑

선택 변환과 마찬가지로 들어오는 필드와 비 정규화 된 배열의 새 구조 프로젝션을 선택 합니다. 비 정규화 된 배열이 매핑되는 경우 출력 열은 배열과 동일한 데이터 형식이 됩니다. 언 롤 by 배열이 subarrays를 포함 하는 복합 개체의 배열인 경우 subarry의 항목을 매핑하면 배열이 출력 됩니다.

매핑 출력을 확인 하려면 검사 탭 및 데이터 미리 보기를 참조 하세요.

## <a name="examples"></a>예

평면화 변환의 아래 예제에 대해서는 다음 JSON 개체를 참조 하세요.

``` json
{
  "name":"MSFT","location":"Redmond", "satellites": ["Bay Area", "Shanghai"],
  "goods": {
    "trade":true, "customers":["government", "distributer", "retail"],
    "orders":[
        {"orderId":1,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":2}]}},
        {"orderId":2,"orderTotal":323.34,"shipped":{"orderItems":[{"itemName":"Mice","itemQty":2},{"itemName":"Keyboard","itemQty":1}]}}
    ]}}
{"name":"Company1","location":"Seattle", "satellites": ["New York"],
  "goods":{"trade":false, "customers":["store1", "store2"],
  "orders":[
      {"orderId":4,"orderTotal":123.34,"shipped":{"orderItems":[{"itemName":"Laptop","itemQty":20},{"itemName":"Charger","itemQty":3}]}},
      {"orderId":5,"orderTotal":343.24,"shipped":{"orderItems":[{"itemName":"Chair","itemQty":4},{"itemName":"Lamp","itemQty":2}]}}
    ]}}
{"name": "Company2", "location": "Bellevue",
  "goods": {"trade": true, "customers":["Bank"], "orders": [{"orderId": 4, "orderTotal": 123.34}]}}
{"name": "Company3", "location": "Kirkland"}
```

### <a name="no-unroll-root-with-string-array"></a>문자열 배열이 있는 언 롤 root 없음

| 언 롤 | 언 롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품. 고객 | None | name <br> customer = 상품 |

#### <a name="output"></a>출력

```
{ 'MSFT', 'government'}
{ 'MSFT', 'distributer'}
{ 'MSFT', 'retail'}
{ 'Company1', 'store'}
{ 'Company1', 'store2'}
{ 'Company2', 'Bank'}
{ 'Company3', null}
```

### <a name="no-unroll-root-with-complex-array"></a>복합 배열이 있는 언 롤 root 없음

| 언 롤 | 언 롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품. 주문 된 주문 항목 | None | name <br> orderId = 상품. 주문 주문 <br> itemName = 상품. orderItems. itemName <br> itemQty = 상품. orderItems. itemQty <br> location = location |

#### <a name="output"></a>출력

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
{ 'Company3', null, null, null, 'Kirkland'}
```

### <a name="same-root-as-unroll-array"></a>언 롤 배열과 동일한 루트

| 언 롤 | 언 롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품 주문 | 상품 주문 | name <br> 상품. 주문 항목. itemName <br> 상품. 고객 <br> 위치 |

#### <a name="output"></a>출력

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>복합 배열이 있는 언 롤 root

| 언 롤 | 언 롤 루트 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품. 주문 항목 | 상품 주문 |name <br> orderId = 상품. 주문 주문 <br> itemName = 상품. orderItems. itemName <br> itemQty = 상품. orderItems. itemQty <br> location = location |

#### <a name="output"></a>출력

```
{ 'MSFT', 1, 'Laptop', 20, 'Redmond'}
{ 'MSFT', 1, 'Charger', 2, 'Redmond'}
{ 'MSFT', 2, 'Mice', 2, 'Redmond'}
{ 'MSFT', 2, 'Keyboard', 1, 'Redmond'}
{ 'Company1', 4, 'Laptop', 20, 'Seattle'}
{ 'Company1', 4, 'Charger', 3, 'Seattle'}
{ 'Company1', 5, 'Chair', 4, 'Seattle'}
{ 'Company1', 5, 'Lamp', 2, 'Seattle'}
{ 'Company2', 4, null, null, 'Bellevue'}
```

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
foldDown(unroll(<unroll cols>),
    mapColumn(
        name,
        each(<array>(type == '<arrayDataType>')),
        each(<array>, match(true())),
        location
    )) ~> <transformationName>
```

### <a name="example"></a>예제

```
source foldDown(unroll(goods.orders.shipped.orderItems, goods.orders),
    mapColumn(
        name,
        orderId = goods.orders.orderId,
        itemName = goods.orders.shipped.orderItems.itemName,
        itemQty = goods.orders.shipped.orderItems.itemQty,
        location = location
    ),
    skipDuplicateMapInputs: false,
    skipDuplicateMapOutputs: false) 
```    

## <a name="next-steps"></a>다음 단계

* [피벗 변환을](data-flow-pivot.md) 사용 하 여 행을 열로 피벗 합니다.
* 피벗 해제 [변환을](data-flow-unpivot.md) 사용 하 여 열을 행으로 피벗 합니다.
