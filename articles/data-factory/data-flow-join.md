---
title: 매핑 데이터 흐름의 변환 조인
description: Azure Data Factory 매핑 데이터 흐름의 조인 변환을 사용하여 두 데이터 원본의 데이터 결합
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/02/2020
ms.openlocfilehash: 0c0e35f7f06afd0cafa4a1e353b7eda84ed226f2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81413658"
---
# <a name="join-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 변환 조인

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

조인 변환을 사용하여 매핑 데이터 흐름에서 두 소스 또는 스트림의 데이터를 결합합니다. 출력 스트림에는 조인 조건에 따라 일치하는 두 소스의 모든 열이 포함됩니다. 

## <a name="join-types"></a>조인 유형

매핑 데이터 흐름은 현재 다섯 가지 조인 유형을 지원합니다.

### <a name="inner-join"></a>내부 조인

내부 조인은 두 테이블에 일치하는 값이 있는 행만 출력합니다.

### <a name="left-outer"></a>왼쪽 우선 외부

왼쪽 외부 조인은 왼쪽 스트림의 모든 행과 오른쪽 스트림에서 일치하는 레코드를 반환합니다. 왼쪽 스트림의 행이 일치하지 않으면 오른쪽 스트림의 출력 열이 NULL로 설정됩니다. 출력은 내부 조인에 의해 반환되는 행과 왼쪽 스트림의 일치하지 않는 행이 됩니다.

> [!NOTE]
> 데이터 흐름에 사용되는 Spark 엔진은 조인 조건에서 가능한 카르테시안 제품으로 인해 때때로 고장나기도 합니다. 이 경우 사용자 지정 교차 조인으로 전환하고 조인 조건을 수동으로 입력할 수 있습니다. 이로 인해 실행 엔진이 관계의 양쪽에서 모든 행을 계산한 다음 행을 필터링해야 하므로 데이터 흐름의 성능이 느려질 수 있습니다.

### <a name="right-outer"></a>오른쪽 우선 외부

오른쪽 외부 조인은 오른쪽 스트림의 모든 행과 왼쪽 스트림에서 일치하는 레코드를 반환합니다. 오른쪽 스트림의 행이 일치하지 않으면 왼쪽 스트림의 출력 열이 NULL로 설정됩니다. 출력은 내부 조인에 의해 반환되는 행과 오른쪽 스트림의 일치하지 않는 행이 됩니다.

### <a name="full-outer"></a>완전 외부

전체 외부 조인은 일치하지 않는 열에 대해 NULL 값을 가진 양쪽의 모든 열과 행을 출력합니다.

### <a name="custom-cross-join"></a>사용자 지정 교차 조인

교차 조인은 조건에 따라 두 스트림의 교차 곱을 출력합니다. 같지 않은 조건을 사용하는 경우 사용자 지정 식을 교차 조인 조건으로 지정합니다. 출력 스트림은 조인 조건을 충족하는 모든 행이 됩니다.

이 조인 유형을 비등조 및 ```OR``` 조건에 사용할 수 있습니다.

전체 카르테시안 제품을 명시적으로 생성하려면 조인 전에 두 개의 독립 스트림 각각에서 파생 열 변환을 사용하여 일치하는 합성 키를 만듭니다. 예를 들어, 호출된 ```SyntheticKey``` 각 스트림에서 파생 열에 새 ```1```열을 만들고 에 동일하게 설정합니다. 그런 ```a.SyntheticKey == b.SyntheticKey``` 다음 사용자 지정 조인 식으로 사용합니다.

> [!NOTE]
> 사용자 지정 교차 조인에 왼쪽 및 오른쪽 관계의 각 측면에서 하나 이상의 열을 포함해야 합니다. 교차 를 실행하면 각 측면의 열 대신 정적 값으로 조인을 실행하면 전체 데이터 집합이 전체 검색되어 데이터 흐름이 제대로 수행되지 않습니다.

## <a name="configuration"></a>구성

1. **오른쪽 스트림** 드롭다운에서 참여할 데이터 스트림을 선택합니다.
1. **조인 유형** 선택
1. 조인 조건에 맞게 일치할 키 열을 선택합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열 간에 같음이 확인됩니다. 계산된 값을 통해 비교하려면 열 드롭다운 위로 마우스를 가져가고 **계산된 열을**선택합니다.

![변환 조인](media/data-flow/join.png "Join")

## <a name="optimizing-join-performance"></a>조인 성능 최적화

SSIS와 같은 도구에서 병합 조인과 달리 조인 변환은 필수 병합 조인 작업이 아닙니다. 조인 키에는 정렬이 필요하지 않습니다. 조인 작업은 브로드캐스트 또는 맵 측 조인중 Spark의 최적 조인 작업을 기반으로 발생합니다.

![조인 변환 최적화](media/data-flow/joinoptimize.png "최적화 참여")

하나 또는 둘 다의 데이터 스트림이 작업자 노드 메모리에 맞으면 최적화 탭에서 **브로드캐스트를** 사용하도록 설정하여 성능을 더욱 최적화합니다. 또한 조인 작업에서 데이터를 다시 분할하여 작업자당 메모리에 더 잘 맞을 수 있습니다.

## <a name="self-join"></a>셀프 조인

데이터 스트림을 자체로 자체 조인하려면 선택 변환을 사용하여 기존 스트림을 별칭으로 합니다. 변환 옆에 있는 더하기 아이콘을 클릭하고 새 분기를 선택하여 **새 분기를**만듭니다. 원래 스트림의 별칭에 선택 변환을 추가합니다. 조인 변환을 추가하고 원래 스트림을 **왼쪽 스트림으로** 선택하고 선택 변환을 **오른쪽 스트림으로 선택합니다.**

![자체 조인](media/data-flow/selfjoin.png "자체 조인")

## <a name="testing-join-conditions"></a>조인 조건 테스트

디버그 모드에서 데이터 미리 보기를 사용하여 조인 변환을 테스트할 때는 알려진 데이터의 작은 집합을 사용합니다. 큰 데이터 집합에서 행을 샘플링할 때 테스트를 위해 읽을 행과 키를 예측할 수 없습니다. 결과는 결정적이지 않으므로 조인 조건이 일치 항목을 반환하지 않을 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    join(
        <conditionalExpression>,
        joinType: { 'inner'> | 'outer' | 'left_outer' | 'right_outer' | 'cross' }
        broadcast: { 'none' | 'left' | 'right' | 'both' }
    ) ~> <joinTransformationName>
```

### <a name="inner-join-example"></a>내부 조인 예제

아래 예제는 왼쪽 스트림 `JoinMatchedData` 및 오른쪽 `TripData` 스트림을 `TripFare`차지하는 조인 변환입니다.  조인 조건은 `hack_license == { hack_license} && TripData@medallion == TripFare@medallion && vendor_id == { vendor_id} && pickup_datetime == { pickup_datetime}` 각 스트림의 `hack_license`" `medallion` `vendor_id`및 `pickup_datetime` 열이 일치하는 경우 true를 반환하는 식입니다. `joinType`이 `'inner'`인 경우 우리는 왼쪽 스트림에서만 방송을 가능하게하므로 `broadcast` 값이 `'left'`있습니다.

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![조인 예제](media/data-flow/join-script1.png "조인 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
TripData, TripFare
    join(
        hack_license == { hack_license}
        && TripData@medallion == TripFare@medallion
        && vendor_id == { vendor_id}
        && pickup_datetime == { pickup_datetime},
        joinType:'inner',
        broadcast: 'left'
    )~> JoinMatchedData
```

### <a name="custom-cross-join-example"></a>사용자 지정 교차 조인 예제

아래 예제는 왼쪽 스트림 `JoiningColumns` 및 오른쪽 `LeftStream` 스트림을 `RightStream`차지하는 조인 변환입니다. 이 변환은 두 개의 스트림을 가져와 `leftstreamcolumn` 열이 열보다 `rightstreamcolumn`큰 모든 행을 함께 조인합니다. `joinType`이 `cross`인 경우 브로드캐스팅이 `broadcast` 활성화되어 `'none'`있지 않은 값입니다.

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![조인 예제](media/data-flow/join-script2.png "조인 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
LeftStream, RightStream
    join(
        leftstreamcolumn > rightstreamcolumn,
        joinType:'cross',
        broadcast: 'none'
    )~> JoiningColumns
```

## <a name="next-steps"></a>다음 단계

데이터를 조인한 후 [파생 된 열을](data-flow-derived-column.md) 만들고 대상 데이터 저장소에 데이터를 [싱크합니다.](data-flow-sink.md)
