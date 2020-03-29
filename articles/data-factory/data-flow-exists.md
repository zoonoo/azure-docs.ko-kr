---
title: 매핑 데이터 흐름에 변환이 존재
description: Azure Data Factory 매핑 데이터 흐름에서 기존 변환을 사용하여 기존 행 확인
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/16/2019
ms.openlocfilehash: efcc45dcf3565b70305323701810c49c4a720394
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930405"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름에 변환이 존재

존재 변환은 데이터가 다른 원본 또는 스트림에 있는지 여부를 확인하는 행 필터링 변환입니다. 출력 스트림에는 오른쪽 스트림에 존재하거나 존재하지 않는 왼쪽 스트림의 모든 행이 포함됩니다. 존재 변환은 ```SQL WHERE EXISTS``` 및 ```SQL WHERE NOT EXISTS```와 유사합니다.

## <a name="configuration"></a>Configuration

1. **오른쪽 스트림** 드롭다운에서 확인 중인 데이터 스트림을 선택합니다.
1. **존재 유형** 설정에 존재할 데이터를 찾고 있는지 여부를 지정합니다.
1. **사용자 지정 식을**사용할지 여부를 선택합니다.
1. 현재 조건으로 비교할 키 열을 선택합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열 간에 같음이 확인됩니다. 계산된 값을 통해 비교하려면 열 드롭다운 위로 마우스를 가져가고 **계산된 열을**선택합니다.

![설정 존재](media/data-flow/exists.png "존재 1")

### <a name="multiple-exists-conditions"></a>여러 개의 존재 조건

각 스트림의 여러 열을 비교하려면 기존 행 옆에 있는 더하기 아이콘을 클릭하여 새 기존 조건을 추가합니다. 각 추가 조건은 "and" 문으로 조인됩니다. 두 열을 비교하는 것은 다음 식과 동일합니다.

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>사용자 지정 표현식

"and" 및 "와 같음"이 아닌 연산자가 포함된 자유 형식 식을 만들려면 **사용자 지정 식** 필드를 선택합니다. 파란색 상자를 클릭하여 데이터 흐름 표현식 작성기에서 사용자 지정 식을 입력합니다.

![사용자 지정 설정이 존재합니다.](media/data-flow/exists1.png "사용자 지정이 존재합니다.")

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: {'none' | 'left' | 'right' | 'both'}
    ) ~> <existsTransformationName>
```

### <a name="example"></a>예제

아래 예제는 왼쪽 스트림 `checkForChanges` 및 오른쪽 `NameNorm2` 스트림을 `TypeConversions`차지하는 존재 변환입니다.  존재 조건은 각 `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` 스트림의 `EMPID` 열과 `Region` 열이 일치하는 경우 true를 반환하는 표현식입니다. 우리가 존재를 확인하는 것처럼, `negate` 거짓입니다. 최적화 탭에서 브로드캐스트를 활성화할 수 `broadcast` 없으므로 `'none'`값이 있습니다.

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![존재 하는 예제](media/data-flow/exists-script.png "존재 하는 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'none'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>다음 단계

비슷한 변환은 [조회](data-flow-lookup.md) 및 [조인입니다.](data-flow-join.md)
