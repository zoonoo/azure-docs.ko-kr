---
title: 매핑 데이터 흐름의 변환 병합
description: 병합 된 변환을 사용하여 계층 적 데이터의 비정규화
author: kromerm
ms.author: makromer
ms.review: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: b19aae8ab6730936a826f5bb069bfdb7d696cdfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246639"
---
# <a name="flatten-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 변환 병합

병합 변환을 사용하여 JSON과 같은 계층 구조 내에서 배열 값을 가져 와서 개별 행으로 풀어줍니다. 이 프로세스를 비정규화라고 합니다.

## <a name="configuration"></a>Configuration

병합 변환에는 다음 구성 설정이 포함됩니다.

![설정 병합](media/data-flow/flatten1.png "설정 병합")

### <a name="unroll-by"></a>롤을 해제합니다.

언롤할 배열을 선택합니다. 출력 데이터는 각 배열의 항목당 하나의 행을 갖습니다. 입력 행의 배열에 의한 언롤이 null 또는 비어 있으면 롤을 해제하지 않은 값이 null로 하나의 출력 행이 있습니다.

### <a name="unroll-root"></a>루트 풀기

기본적으로 병합 변환은 배열이 존재하는 계층구조의 맨 위로 배열을 해제합니다. 선택적으로 배열을 언롤 루트로 선택할 수 있습니다. 언롤 루트는 배열에 의한 언롤을 포함하거나 포함하는 복잡한 개체의 배열이어야 합니다. 언롤 루트를 선택하면 출력 데이터에 언롤 루트의 항목당 하나 이상의 행이 포함됩니다. 입력 행에 unroll 루트에 항목이 없는 경우 출력 데이터에서 삭제됩니다. unroll 루트를 선택하면 항상 기본 동작보다 적은 수의 행이 출력됩니다.

### <a name="flatten-mapping"></a>플랫 매핑

선택 변환과 마찬가지로 들어오는 필드와 비정규화된 배열에서 새 구조의 투영을 선택합니다. 정규화되지 않은 배열이 매핑되면 출력 열은 배열과 동일한 데이터 형식이 됩니다. 배열에 의한 언롤이 하위 배열을 포함하는 복잡한 개체의 배열인 경우 해당 subarry의 항목을 매핑하면 배열이 출력됩니다.

검사 탭 및 데이터 미리 보기를 참조하여 매핑 출력을 확인합니다.

## <a name="examples"></a>예

평탄화 변환의 다음 예제는 다음 JSON 개체를 참조하십시오.

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

### <a name="no-unroll-root-with-string-array"></a>문자열 배열을 가진 루트 풀기 없음

| 롤을 해제합니다. | 루트 풀기 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품.고객 | None | name <br> 고객 = 상품.고객 |

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

### <a name="no-unroll-root-with-complex-array"></a>복잡한 배열을 가진 풀기 루트 없음

| 롤을 해제합니다. | 루트 풀기 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품.주문.배송.주문항목 | None | name <br> orderId = 상품.orders.orderId <br> 항목이름 = 상품.orders.shipped.order.orderItems.itemName <br> 항목Qty = 상품.orders.shipped.orderItems.itemQty <br> 위치 = 위치 |

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

### <a name="same-root-as-unroll-array"></a>언롤 배열과 동일한 루트

| 롤을 해제합니다. | 루트 풀기 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품.주문 | 상품.주문 | name <br> 상품.orders.shipped.orderItems.itemName <br> 상품.고객 <br> 위치 |

#### <a name="output"></a>출력

```
{ 'MSFT', ['Laptop','Charger'], ['government','distributer','retail'], 'Redmond'}
{ 'MSFT', ['Mice', 'Keyboard'], ['government','distributer','retail'], 'Redmond'}
{ 'Company1', ['Laptop','Charger'], ['store', 'store2'], 'Seattle'}
{ 'Company1', ['Chair', 'Lamp'], ['store', 'store2'], 'Seattle'}
{ 'Company2', null, ['Bank'], 'Bellevue'}
```

### <a name="unroll-root-with-complex-array"></a>복잡한 배열로 루트 풀기

| 롤을 해제합니다. | 루트 풀기 | 프로젝션 |
| --------- | ----------- | ---------- |
| 상품.주문.배송.주문 항목 | 상품.주문 |name <br> orderId = 상품.orders.orderId <br> 항목이름 = 상품.orders.shipped.order.orderItems.itemName <br> 항목Qty = 상품.orders.shipped.orderItems.itemQty <br> 위치 = 위치 |

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

* [피벗 변환을](data-flow-pivot.md) 사용하여 행을 열로 피벗합니다.
* [피벗 해제 변환을](data-flow-unpivot.md) 사용하여 열을 행으로 피벗합니다.
