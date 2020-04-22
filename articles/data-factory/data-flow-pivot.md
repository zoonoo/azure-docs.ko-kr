---
title: 매핑 데이터 흐름의 피벗 변환
description: Azure 데이터 팩터리 매핑 데이터 흐름 피벗 변환을 사용하여 행에서 열로 피벗 데이터
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/30/2019
ms.openlocfilehash: a58444f81f60b48f9c2c76f13257a6a2431158a8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686395"
---
# <a name="pivot-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 피벗 변환


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

피벗 변환을 사용하여 단일 열의 고유한 행 값에서 여러 열을 만듭니다. 피벗은 컬럼별로 그룹을 선택하고 집계 함수를 사용하여 피벗 열을 생성하는 집계 [변환입니다.](data-flow-expression-functions.md#aggregate-functions)

## <a name="configuration"></a>구성

피벗 변환에는 열별로 그룹화, 피벗 키 및 피벗열을 생성하는 방법의 세 가지 입력이 필요합니다.

### <a name="group-by"></a>Group By

![옵션으로 그룹화](media/data-flow/pivot2.png "[옵션별로 그룹화")

피벗된 열을 집계할 열을 선택합니다. 출력 데이터는 동일한 그룹으로 모든 행을 하나의 행으로 그룹화합니다. 피벗 된 열에서 수행 된 집계는 각 그룹에 걸쳐 발생합니다.

이 섹션은 선택 사항입니다. 열별로 그룹을 선택하지 않으면 전체 데이터 스트림이 집계되고 하나의 행만 출력됩니다.

### <a name="pivot-key"></a>피벗 키

![피벗 키](media/data-flow/pivot3.png "피벗 키")

피벗 키는 행 값이 새 열로 피벗되는 열입니다. 기본적으로 피벗 변환은 각 고유 행 값에 대해 새 열을 만듭니다.

**값으로**레이블이 지정된 섹션에서 피벗할 특정 행 값을 입력할 수 있습니다. 이 섹션에 입력된 행 값만 피벗됩니다. Null **값을** 사용하도록 설정하면 열의 null 값에 대한 피벗 열이 생성됩니다.

### <a name="pivoted-columns"></a>피벗 된 열

![피벗 된 열](media/data-flow/pivot4.png "피벗 된 열")

열이 되는 각 고유 피벗 키 값에 대해 각 그룹에 대해 집계된 행 값을 생성합니다. 피벗 키당 여러 열을 작성할 수 있습니다. 각 피벗 열에는 하나 이상의 [집계 함수가](data-flow-expression-functions.md#aggregate-functions)포함되어야 합니다.

**열 이름 패턴:** 각 피벗 열의 열 이름을 서식을 지정하는 방법을 선택합니다. 출력된 열 이름은 피벗 키 값, 열 접두사 및 선택적 접두사, 충분, 중간 문자의 조합입니다. 

**열 배열:** 피벗 키당 두 개 이상의 피벗 열을 생성하는 경우 열의 순서를 지정하는 방법을 선택합니다. 

**열 접두사:** 피벗 키당 두 개 이상의 피벗 열을 생성하는 경우 각 열에 대한 열 접두사를 입력합니다. 이 설정은 피벗 열이 하나만 있는 경우 선택 사항입니다.

## <a name="help-graphic"></a>도움말 그래픽

아래 도움말 그래픽은 서로 다른 피벗 구성 요소가 서로 상호 작용하는 방법을 보여줍니다.

![피벗 도움말 그래픽](media/data-flow/pivot5.png "피벗 도움말 그래픽")

## <a name="pivot-metadata"></a>피벗 메타데이터

피벗 키 구성에 값을 지정하지 않으면 피벗된 열이 런타임에 동적으로 생성됩니다. 피벗 된 열의 수는 고유 피벗 키 값의 수에 피벗 열 수를 곱한 값과 같습니다. 변경 번호일 수 있기 때문에 UX는 **검사** 탭에 열 메타데이터를 표시하지 않으며 열 전파가 없습니다. 이러한 열을 변환하려면 데이터 흐름 매핑의 [열 패턴](concepts-data-flow-column-pattern.md) 기능을 사용합니다. 

특정 피벗 키 값이 설정된 경우 피벗 된 열이 메타데이터에 나타납니다.e 열 이름은 검사 및 싱크 매핑에서 사용할 수 있습니다.

### <a name="generate-metadata-from-drifted-columns"></a>드리프트 열에서 메타데이터 생성

Pivot는 행 값을 기반으로 동적으로 새 열 이름을 생성합니다. 데이터 흐름의 나중에 참조할 수 있는 메타데이터에 이러한 새 열을 추가할 수 있습니다. 이렇게 하려면 데이터 미리 보기에서 빠른 [동작을 드리프트한 맵을](concepts-data-flow-schema-drift.md#map-drifted-columns-quick-action) 사용합니다. 

![열 피벗](media/data-flow/newpivot1.png "드리프트 피벗 열 매핑")

### <a name="sinking-pivoted-columns"></a>피벗 열 싱크

피벗된 열은 동적이지만 대상 데이터 저장소에 계속 쓸 수 있습니다. 싱크 설정에서 **스키마 드리프트 허용을** 활성화합니다. 이렇게 하면 메타데이터에 포함되지 않은 열을 작성할 수 있습니다. 하지만 스키마 드리프트 옵션을 사용하면 데이터를 착륙시킬 수 있습니다.

### <a name="rejoin-original-fields"></a>원래 필드에 다시 참여

피벗 변환은 그룹을 피벗된 열만 투영합니다. 출력 데이터에 다른 입력 열을 포함하려면 자체 [조인](data-flow-join.md#self-join) 패턴을 사용합니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStreamName>
    pivot(groupBy(Tm),
        pivotBy(<pivotKeyColumn, [<specifiedColumnName1>,...,<specifiedColumnNameN>]),
        <pivotColumnPrefix> = <pivotedColumnValue>,
        columnNaming: '< prefix >< $N | $V ><middle >< $N | $V >< suffix >',
        lateral: { 'true' | 'false'}
    ) ~> <pivotTransformationName
```
### <a name="example"></a>예제

구성 섹션에 표시된 화면에는 다음과 같은 데이터 흐름 스크립트가 있습니다.

```
BasketballPlayerStats pivot(groupBy(Tm),
    pivotBy(Pos),
    {} = count(),
    columnNaming: '$V$N count',
    lateral: true) ~> PivotExample

```

## <a name="next-steps"></a>다음 단계

[피벗 해제 변환을](data-flow-unpivot.md) 시도하여 열 값을 행 값으로 변환합니다. 
