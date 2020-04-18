---
title: 매핑 데이터 흐름의 조회 변환
description: 매핑 데이터 흐름에서 조회 변환을 사용하여 다른 원본의 데이터를 참조합니다.
author: kromerm
ms.reviewer: daperlov
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/23/2020
ms.openlocfilehash: 24fe11610d2a91fcdb0f09b8e45ea6ff4b81bd70
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606375"
---
# <a name="lookup-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 조회 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

조회 변환을 사용하여 데이터 흐름 스트림의 다른 원본의 데이터를 참조합니다. 조회 변환은 일치하는 데이터에서 원본 데이터에 대한 열을 더합니다.

조회 변환은 왼쪽 외부 조인과 유사합니다. 기본 스트림의 모든 행은 조회 스트림의 추가 열이 있는 출력 스트림에 존재합니다. 

## <a name="configuration"></a>구성

![조회 변환](media/data-flow/lookup1.png "조회")

**기본 스트림:** 들어오는 데이터 스트림입니다. 이 스트림은 조인의 왼쪽과 같습니다.

**조회 스트림:** 기본 스트림에 추가된 데이터입니다. 추가되는 데이터는 조회 조건에 따라 결정됩니다. 이 스트림은 조인의 오른쪽에 해당합니다.

**여러 행 일치:** 활성화된 경우 기본 스트림에 일치하는 행이 여러 행을 반환합니다. 그렇지 않으면 '일치' 조건에 따라 단일 행만 반환됩니다.

**일치:** '여러 행 일치'가 활성화된 경우에만 표시됩니다. 행, 첫 번째 일치 또는 마지막 일치 에 일치 할지 여부를 선택합니다. 모든 행은 가장 빠른 행을 실행하기 때문에 권장됩니다. 첫 번째 행 또는 마지막 행을 선택한 경우 정렬 조건을 지정해야 합니다.

**조회 조건:** 일치할 열을 선택합니다. 같음 조건이 충족되면 행이 일치하는 것으로 간주됩니다. 마우스로 가리키고 '계산된 열'을 선택하여 [데이터 흐름 표현 언어를](data-flow-expression-functions.md)사용하여 값을 추출합니다.

조회 변환은 같음 일치만 지원합니다. 조회 식을 보다 큰 다른 연산자포함하도록 사용자 지정하려면 [조인 변환에서 교차 조인을](data-flow-join.md#custom-cross-join)사용하는 것이 좋습니다. 교차 조인은 실행에 발생할 수 있는 카르테시안 제품 오류를 방지합니다.

두 스트림의 모든 열이 출력 데이터에 포함됩니다. 중복하거나 원치 않는 열을 삭제하려면 조회 변환 후 [선택 변환을](data-flow-select.md) 추가합니다. 열은 싱크 변환에서 삭제하거나 이름을 바꿀 수도 있습니다.

## <a name="analyzing-matched-rows"></a>일치하는 행 분석

조회 변환 후 함수를 `isMatch()` 사용하여 조회가 개별 행에 대해 일치하는지 확인할 수 있습니다.

![조회 패턴](media/data-flow/lookup111.png "조회 패턴")

이 패턴의 예는 조건부 분할 변환을 `isMatch()` 사용하여 함수에서 분할하는 것입니다. 위의 예에서 일치하는 행은 최상위 스트림을 통과하고 일치하지 ```NoMatch``` 않는 행은 스트림을 통해 흐르게 됩니다.

## <a name="testing-lookup-conditions"></a>조회 조건 테스트

디버그 모드에서 데이터 미리 보기를 사용하여 조회 변환을 테스트할 때는 알려진 데이터의 작은 집합을 사용합니다. 큰 데이터 집합에서 행을 샘플링할 때 테스트를 위해 읽을 행과 키를 예측할 수 없습니다. 결과는 결정적이지 않으므로 조인 조건이 일치 항목을 반환하지 않을 수 있습니다.

## <a name="broadcast-optimization"></a>브로드캐스트 최적화

Azure 데이터 팩터리 매핑 데이터 흐름은 확장된 스파크 환경에서 실행됩니다. 데이터 집합이 작업자 노드 메모리 공간에 들어갈 수 있는 경우 브로드캐스팅을 사용하도록 설정하여 조회 성능을 최적화할 수 있습니다.

![브로드캐스트 조인](media/data-flow/broadcast.png "브로드캐스트 조인")

브로드캐스트를 활성화하면 전체 데이터 집합이 메모리로 푸시됩니다. 수천 개의 행만 포함하는 작은 데이터 집합의 경우 브로드캐스트를 통해 조회 성능을 크게 향상시킬 수 있습니다. 큰 데이터 집합의 경우 이 옵션을 사용하면 메모리 부족 예외가 발생할 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    lookup(
        <lookupConditionExpression>,
        multiple: { true | false },
        pickup: { 'first' | 'last' | 'any' },  ## Only required if false is selected for multiple
        { desc | asc }( <sortColumn>, { true | false }), ## Only required if 'first' or 'last' is selected. true/false determines whether to put nulls first
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <lookupTransformationName>
```
### <a name="example"></a>예제

![조회 변환](media/data-flow/lookup-dsl-example.png "조회")

위의 조회 구성에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
SQLProducts, DimProd lookup(ProductID == ProductKey,
    multiple: false,
    pickup: 'first',
    asc(ProductKey, true),
    broadcast: 'none')~> LookupKeys
```
## 
다음 단계

* [조인](data-flow-join.md) 및 [존재](data-flow-exists.md) 변환은 모두 여러 스트림 입력을 수행합니다.
* [조건부 분할 변환을](data-flow-conditional-split.md) 사용하여 ```isMatch()``` 일치 및 일치하지 않는 값에 대한 행 분할
