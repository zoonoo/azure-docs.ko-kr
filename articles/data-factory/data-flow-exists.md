---
title: 매핑 데이터 흐름의 존재 변환
description: Azure Data Factory 매핑 데이터 흐름에서 exists 변환을 사용 하 여 기존 행을 확인 합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/07/2020
ms.openlocfilehash: 805b51bf4e6d8feab9539f660dfc72ca78b82d5c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "82982635"
---
# <a name="exists-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 존재 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

존재 변환은 데이터가 다른 원본 또는 스트림에 있는지 확인 하는 행 필터링 변환입니다. 출력 스트림에는 왼쪽 스트림에 존재 하거나 오른쪽 스트림에 존재 하지 않는 모든 행이 포함 됩니다. 존재 변환은 ```SQL WHERE EXISTS``` 및와 비슷합니다 ```SQL WHERE NOT EXISTS``` .

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vZKz]

## <a name="configuration"></a>Configuration

1. **올바른 스트림** 드롭다운에서 있는지 확인 하는 데이터 스트림을 선택 합니다.
1. 존재 **하는 데이터** 를 찾고 있는지 여부를 지정 합니다.
1. **사용자 지정 식을**사용할지 여부를 선택 합니다.
1. 존재 조건으로 비교할 키 열을 선택 합니다. 기본적으로 데이터 흐름은 각 스트림의 한 열이 같은지 검색합니다. 계산된 값을 통해 비교하려면 열 드롭다운 위로 마우스를 이동하여 **계산 열**을 선택합니다.

![Exists 설정](media/data-flow/exists.png "exists 1")

### <a name="multiple-exists-conditions"></a>여러 개의 exists 조건

각 스트림에서 여러 열을 비교 하려면 기존 행 옆에 있는 더하기 아이콘을 클릭 하 여 새 exists 조건을 추가 합니다. 각 추가 조건은 "and" 문으로 조인 됩니다. 두 열을 비교 하는 것은 다음 식과 같습니다.

`source1@column1 == source2@column1 && source1@column2 == source2@column2`

### <a name="custom-expression"></a>사용자 지정 식

"And" 및 "equals" 이외의 연산자를 포함 하는 자유 형식 식을 만들려면 **사용자 지정 식** 필드를 선택 합니다. 파란색 상자를 클릭 하 여 데이터 흐름 식 작성기를 통해 사용자 지정 식을 입력 합니다.

![Exists 사용자 지정 설정](media/data-flow/exists1.png "exists 사용자 지정")

## <a name="broadcast-optimization"></a>브로드캐스트 최적화

![브로드캐스트 조인](media/data-flow/broadcast.png "브로드캐스트 조인")

조인, 조회 및 있음 변환에서 하나 또는 두 데이터 스트림이 작업자 노드 메모리에 맞는 경우 **브로드캐스팅**를 사용하도록 설정하여 성능을 최적화할 수 있습니다. 기본적으로 spark 엔진은 한쪽에서 브로드캐스트할지 여부를 자동으로 결정합니다. 브로드캐스트할 쪽을 수동으로 선택하려면 **고정**을 선택합니다.

조인이 실행되는 동안 시간 제한 오류가 발생하는 경우에만 **해제** 옵션을 통해 브로드캐스팅을 사용하지 않도록 설정하는 것이 좋습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<leftStream>, <rightStream>
    exists(
        <conditionalExpression>,
        negate: { true | false },
        broadcast: { 'auto' | 'left' | 'right' | 'both' | 'off' }
    ) ~> <existsTransformationName>
```

### <a name="example"></a>예제

아래 예제는 `checkForChanges` 왼쪽 스트림과 오른쪽 스트림을 취하는 이라는 exists 변환입니다 `NameNorm2` `TypeConversions` .  Exists 조건은 `NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region` `EMPID` 각 스트림의 및 열이 모두 일치 하는 경우 true를 반환 하는 식입니다 `Region` . 존재 여부를 확인 하는 중 `negate` 이 false입니다. 최적화 탭에서 브로드캐스팅을 사용 하도록 설정 하지 않으므로 `broadcast` 값이 `'none'` 있습니다.

Data Factory UX에서 이 변환은 아래 이미지와 같습니다.

![Exists 예](media/data-flow/exists-script.png "Exists 예")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
NameNorm2, TypeConversions
    exists(
        NameNorm2@EmpID == TypeConversions@EmpID && NameNorm2@Region == DimEmployees@Region,
        negate:false,
        broadcast: 'auto'
    ) ~> checkForChanges
```

## <a name="next-steps"></a>다음 단계

유사 변환은 [조회](data-flow-lookup.md) 및 [조인](data-flow-join.md)입니다.
