---
title: 데이터 흐름 평면화 변환 매핑
description: Azure data factory 매핑 데이터 흐름 평면화 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 435d2469be8eb572caa02e381d84ae4e9ac32f4b
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77674845"
---
# <a name="azure-data-factory-flatten-transformation"></a>Azure Data Factory 평면화 변환

결합 변환은 계층 구조 내의 배열 값을 새 행으로 피벗 하는 데 사용할 수 있으며 기본적으로 데이터를 비 정규화 합니다.

![변환 도구 상자](media/data-flow/flatten5.png "변환 도구 상자")

![결합 변환 1](media/data-flow/flatten7.png "결합 변환 1")

## <a name="unroll-by"></a>언 롤

먼저 언 롤 하 고 피벗 하려는 배열 열을 선택 합니다.

![변환 설정 평면화](media/data-flow/flatten1.png "변환 설정 평면화")

## <a name="unroll-root"></a>언 롤 루트

기본적으로 ADF는 위에서 선택한 언 롤 배열의 구조를 평면화 합니다. 또는 계층의 다른 부분을 선택 하 여 언 롤 수 있습니다.

## <a name="input-columns"></a>입력 열

마지막으로, 들어오는 필드 및 unrolled 정규화 된 열을 기반으로 새 구조의 프로젝션을 선택 합니다.

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
source(output(
        name as string,
        location as string,
        satellites as string[],
        goods as (trade as boolean, customers as string[], orders as (orderId as string, orderTotal as double, shipped as (orderItems as (itemName as string, itemQty as string)[]))[])
    ),
    allowSchemaDrift: true,
    validateSchema: false) ~> source2
source2 foldDown(unroll(goods.orders.shipped.orderItems),
    mapColumn(
        name,
        each(goods.orders, match(type == 'integer')),
        each(goods.orders.shipped.orderItems, match(true())),
        location
    )) ~> Flatten1
Flatten1 sink(allowSchemaDrift: true,
    validateSchema: false,
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> sink1
```    

## <a name="next-steps"></a>다음 단계

* [피벗 변환을](data-flow-pivot.md) 사용 하 여 행을 열로 피벗 합니다.
* 피벗 해제 [변환을](data-flow-unpivot.md) 사용 하 여 열을 행으로 피벗 합니다.
