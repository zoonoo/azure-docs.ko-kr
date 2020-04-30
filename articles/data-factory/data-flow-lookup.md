---
title: 데이터 흐름 매핑의 조회 변환
description: 데이터 흐름 매핑에서 조회 변환을 사용 하 여 다른 원본에서 데이터를 참조 합니다.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: af4e33e2653aebe5d1c979aa314463e4beb7b0d7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233400"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 조회 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

조회 변환을 사용 하 여 데이터 흐름 스트림의 다른 원본에서 데이터를 참조할 수 있습니다. 조회 변환은 일치 하는 데이터의 열을 원본 데이터에 추가 합니다.

조회 변환은 왼쪽 우선 외부 조인과 유사 합니다. 주 스트림의 모든 행이 조회 스트림의 추가 열을 포함 하는 출력 스트림에 존재 합니다. 

## <a name="configuration"></a>Configuration

![조회 변환](media/data-flow/lookup1.png "조회")

**기본 스트림:** 들어오는 데이터 스트림입니다. 이 스트림은 조인의 왼쪽에 해당 합니다.

**조회 스트림:** 주 스트림에 추가 되는 데이터입니다. 추가 되는 데이터는 조회 조건에 따라 결정 됩니다. 이 스트림은 조인의 오른쪽에 해당 합니다.

**여러 행 일치:** 사용 하도록 설정 하면 주 스트림에서 여러 개의 일치 하는 행이 여러 행을 반환 합니다. 그렇지 않으면 ' Match on ' 조건에 따라 단일 행만 반환 됩니다.

**일치 기준:** ' 여러 행 일치 '가 사용 하도록 설정 된 경우에만 표시 됩니다. 행, 첫 번째 일치 또는 마지막 일치 항목에 대해 일치 여부를 선택 합니다. 행은 가장 빠르게 실행 되므로 권장 됩니다. 첫 번째 행 또는 마지막 행을 선택한 경우 정렬 조건을 지정 해야 합니다.

**조회 조건:** 일치 시킬 열을 선택 합니다. 같음 조건이 충족 되 면 행이 일치 하는 것으로 간주 됩니다. ' 계산 열 '을 가리키고 선택 하 여 [데이터 흐름 식 언어](data-flow-expression-functions.md)를 사용 하 여 값을 추출 합니다.

조회 변환은 같음 일치만 지원 합니다. 보다 큼 등의 다른 연산자를 포함 하도록 조회 식을 사용자 지정 하려면 [조인 변환에 크로스 조인을](data-flow-join.md#custom-cross-join)사용 하는 것이 좋습니다. 크로스 조인은 실행 시 가능한 카티션 곱을 방지 합니다.

두 스트림의 모든 열이 출력 데이터에 포함 됩니다. 중복 되거나 불필요 한 열을 삭제 하려면 조회 변환 후에 [선택 변환을](data-flow-select.md) 추가 합니다. 싱크 변환에서 열을 삭제 하거나 이름을 바꿀 수도 있습니다.

## <a name="analyzing-matched-rows"></a>일치 하는 행 분석

조회 변환 후에는 함수 `isMatch()` 를 사용 하 여 조회가 개별 행과 일치 하는지 여부를 확인할 수 있습니다.

![조회 패턴](media/data-flow/lookup111.png "조회 패턴")

이 패턴의 예는 조건부 분할 변환을 사용 하 여 `isMatch()` 함수에서 분할 하는 것입니다. 위의 예에서는 일치 하는 행이 위쪽 스트림을 통과 하 고 일치 하지 않는 행이 ```NoMatch``` 스트림을 통해 흐릅니다.

## <a name="testing-lookup-conditions"></a>조회 조건 테스트

디버그 모드에서 데이터 미리 보기를 사용 하 여 조회 변환을 테스트할 때 작은 알려진 데이터 집합을 사용 합니다. 대량 데이터 집합에서 행을 샘플링 하는 경우 테스트를 위해 읽을 행과 키를 예측할 수 없습니다. 결과는 명확 하지 않습니다. 즉, 조인 조건이 일치 하는 항목을 반환 하지 않을 수 있습니다.

## <a name="broadcast-optimization"></a>브로드캐스트 최적화

![브로드캐스트 조인](media/data-flow/broadcast.png "브로드캐스트 조인")

조인, 조회 및 존재 변환에서 하나 또는 두 데이터 스트림이 작업자 노드 메모리에 맞는 경우 **브로드캐스팅을**사용 하도록 설정 하 여 성능을 최적화할 수 있습니다. 기본적으로 spark 엔진은 한 쪽의 브로드캐스트 여부를 자동으로 결정 합니다. 브로드캐스트할 면을 수동으로 선택 하려면 **고정**을 선택 합니다.

조인이 시간 제한 오류로 인해 실행 되지 않는 한 **Off** 옵션을 통해 브로드캐스팅을 사용 하지 않도록 설정 하는 것은 권장 되지 않습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>예제

![조회 변환](media/data-flow/lookup-dsl-example.png "조회")

위의 조회 구성에 대 한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'auto')~> LookupKeys
```
## 
다음 단계

* [Join](data-flow-join.md) 및 [exists](data-flow-exists.md) 변환은 둘 다 여러 스트림 입력을 사용 합니다.
* 에서 [조건부 분할 변환을](data-flow-conditional-split.md) ```isMatch()``` 사용 하 여 일치 하는 값 및 일치 하지 않는 값에 대 한 행 분할
