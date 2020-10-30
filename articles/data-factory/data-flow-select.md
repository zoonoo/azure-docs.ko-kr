---
title: 매핑 데이터 흐름에서 변환 선택
description: Azure Data Factory 매핑 데이터 흐름 선택 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/02/2020
ms.openlocfilehash: 2d8c4d1915e22ccabf193f1b34c5fc4797ead549
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93040240"
---
# <a name="select-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름에서 변환 선택

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

열 이름을 바꾸거나 열을 삭제 하거나 열을 다시 정렬 하려면 선택 변환을 사용 합니다. 이 변환은 행 데이터를 변경 하지 않지만 다운스트림에서 전파 되는 열을 선택 합니다. 

선택 변환에서 사용자는 고정 매핑을 지정 하거나, 패턴을 사용 하 여 규칙 기반 매핑을 수행 하거나, 자동 매핑을 사용 하도록 설정할 수 있습니다. 동일한 select 변환 내에서 고정 및 규칙 기반 매핑을 모두 사용할 수 있습니다. 열이 정의 된 매핑 중 하 나와 일치 하지 않으면 해당 열이 삭제 됩니다.

## <a name="fixed-mapping"></a>고정 매핑

프로젝션에 정의 된 열이 50 개 미만이 면 정의 된 모든 열은 기본적으로 고정 매핑을 갖습니다. 고정 매핑은 정의 된 들어오는 열을 가져와 정확한 이름으로 매핑합니다.

![고정 매핑](media/data-flow/fixedmapping.png "고정 매핑")

> [!NOTE]
> 고정 매핑을 사용 하 여 데이터베이스가 드리프트 열을 매핑하거나 이름을 바꿀 수 없습니다.

### <a name="mapping-hierarchical-columns"></a>계층적 열 매핑

고정 매핑은 계층 열의 하위 열을 최상위 열에 매핑하는 데 사용할 수 있습니다. 계층이 정의 된 경우 열 드롭다운을 사용 하 여 하위 열을 선택 합니다. Select 변환은 subcolumn 값과 데이터 형식을 사용 하 여 새 열을 만듭니다.

![계층 구조 매핑](media/data-flow/select-hierarchy.png "계층 구조 매핑")

## <a name="rule-based-mapping"></a>규칙 기반 매핑


> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4xiXz]

여러 열을 한 번에 매핑하거나 데이터베이스가 드리프트 열을 전달 하려면 규칙 기반 매핑을 사용 하 여 열 패턴을 통해 매핑을 정의 합니다. 열에 대 한,, 및을 기준으로 일치 시킵니다 `name` `type` `stream` `position` . 고정 및 규칙 기반 매핑의 모든 조합을 사용할 수 있습니다. 기본적으로 50 개 이상의 열을 포함 하는 모든 프로젝션은 모든 열에서 일치 하는 규칙 기반 매핑으로 기본 설정 되며 입력 이름을 출력 합니다. 

규칙 기반 매핑을 추가 하려면 **매핑 추가** 를 클릭 하 고 **규칙 기반 매핑을** 선택 합니다.

![추가 매핑에서 선택한 규칙 기반 매핑을 보여 주는 스크린샷](media/data-flow/rule2.png "규칙 기반 매핑")

각 규칙 기반 매핑에는 두 개의 입력이 필요 합니다. 두 개의 입력이 필요 합니다. 일치 하는 조건과 각 매핑된 열의 이름을 입력 해야 합니다. 두 값은 모두 [식 작성기](concepts-data-flow-expression-builder.md)를 통해 입력 됩니다. 왼쪽 식 상자에 부울 일치 조건을 입력 합니다. 오른쪽 식 상자에서 일치 하는 열을 매핑할 대상을 지정 합니다.

![스크린샷은 매핑을 보여 줍니다.](media/data-flow/rule-based-mapping.png "규칙 기반 매핑")

`$$`구문을 사용 하 여 일치 하는 열의 입력 이름을 참조 합니다. 위의 이미지를 예로 들 수 있습니다. 예를 들어, 사용자는 이름이 6 자 보다 짧은 모든 문자열 열에 대해 일치 하는 것으로 가정 합니다. 들어오는 열의 이름을 지정 하는 경우 `test` 식에서 `$$ + '_short'` 열의 이름을 바꿉니다 `test_short` . 이 매핑이 있는 유일한 매핑이 면 해당 조건을 충족 하지 않는 모든 열이 출력 된 데이터에서 삭제 됩니다.

패턴은 데이터베이스가 드리프트 및 정의 된 열 모두와 일치 합니다. 규칙에 따라 매핑되는 정의 된 열을 확인 하려면 규칙 옆에 있는 안경 아이콘을 클릭 합니다. 데이터 미리 보기를 사용 하 여 출력을 확인 합니다.

### <a name="regex-mapping"></a>Regex 매핑

아래쪽 펼침 단추를 클릭 하면 regex 매핑 조건을 지정할 수 있습니다. Regex 매핑 조건은 지정 된 regex 조건과 일치 하는 모든 열 이름과 일치 합니다. 표준 규칙 기반 매핑과 함께 사용할 수 있습니다.

![스크린샷에는 계층 수준 및 이름이 일치 하는 regex 매핑 조건이 표시 됩니다.](media/data-flow/regex-matching.png "규칙 기반 매핑")

위의 예제는 regex 패턴이 `(r)` 나 소문자 r을 포함 하는 모든 열 이름에 일치 합니다. 표준 규칙 기반 매핑과 유사 하 게 일치 하는 모든 열은 구문을 사용 하 여 오른쪽의 조건에 따라 변경 됩니다 `$$` .

열 이름에 regex 일치 항목이 여러 개 있는 경우 `$n` ' n '에서 일치 하는 항목을 참조 하는 특정 일치 항목을 참조할 수 있습니다. 예를 들어 ' $2 '은 열 이름에서 두 번째 일치 항목을 참조 합니다.

### <a name="rule-based-hierarchies"></a>규칙 기반 계층

정의 된 프로젝션에 계층이 있는 경우 규칙 기반 매핑을 사용 하 여 계층 하위 열을 매핑할 수 있습니다. 일치 조건 및 하위 열을 매핑하려는 복합 열을 지정 합니다. 오른쪽에 지정 된 ' 이름 ' 규칙을 사용 하 여 일치 하는 모든 하위 열이 출력 됩니다.

![계층 구조에 대해를 사용 하는 규칙 기반 매핑을 보여 주는 스크린샷](media/data-flow/rule-based-hierarchy.png "규칙 기반 매핑")

위의 예는 복합 열의 모든 하위 열에서 일치 합니다 `a` . `a` 에는 두 개의 하위 열 및가 포함 되어 있습니다 `b` `c` . 출력 스키마는 두 개의 열을 포함 `b` 하 고 `c` ' Name as ' 조건은입니다 `$$` .

### <a name="parameterization"></a>매개 변수화

규칙 기반 매핑을 사용 하 여 열 이름을 매개 변수화 할 수 있습니다. 키워드를 사용 ```name``` 하 여 매개 변수에 대해 들어오는 열 이름을 일치 시킵니다. 예를 들어 데이터 흐름 매개 변수가 있는 경우 ```mycolumn``` 와 동일한 열 이름과 일치 하는 규칙을 만들 수 있습니다 ```mycolumn``` . 일치 하는 열의 이름을 ' 비즈니스 키 '와 같은 하드 코딩 된 문자열로 바꾸고 명시적으로 참조할 수 있습니다. 이 예제에서 일치 조건은 이며 ```name == $mycolumn``` 이름 조건은 ' 비즈니스 키 '입니다. 

## <a name="auto-mapping"></a>자동 매핑

선택 변환을 추가 하는 경우 자동 매핑 슬라이더를 전환 하 여 **자동 매핑을** 사용 하도록 설정할 수 있습니다. 자동 매핑을 사용 하면 선택 변환은 중복 항목을 제외 하 고 들어오는 모든 열을 입력과 동일한 이름으로 매핑합니다. 여기에는 데이터베이스가 드리프트 열이 포함 되며,이는 출력 데이터에 스키마에 정의 되지 않은 열이 포함 될 수 있음을 의미 합니다. 데이터베이스가 드리프트 열에 대 한 자세한 내용은 [스키마 드리프트](concepts-data-flow-schema-drift.md)를 참조 하세요.

![자동 매핑](media/data-flow/automap.png "자동 매핑")

자동 매핑을 사용 하면 선택 변환에서 중복 설정 건너뛰기와 기존 열에 새 별칭을 제공 합니다. 별칭은 동일한 스트림과 자체 조인 시나리오에서 여러 조인이 나 조회를 수행 하는 경우에 유용 합니다. 

## <a name="duplicate-columns"></a>중복 열

기본적으로 select 변환은 입력 및 출력 프로젝션 모두에서 중복 열을 삭제 합니다. 중복 된 입력 열은 종종 조인에서 열 이름이 중복 되는 조인 및 조회 변환에서 제공 됩니다. 서로 다른 두 입력 열을 같은 이름에 매핑하면 중복 출력 열이 발생할 수 있습니다. 확인란을 선택/해제 하 여 중복 열을 삭제할지 아니면 전달할지를 선택 합니다.

![중복 항목 건너뛰기](media/data-flow/select-skip-dup.png "중복 항목 건너뛰기")

## <a name="ordering-of-columns"></a>열 순서 지정

매핑의 순서는 출력 열의 순서를 결정 합니다. 입력 열이 여러 번 매핑되면 첫 번째 매핑만 적용 됩니다. 중복 열을 삭제 하는 경우 첫 번째 일치 항목이 유지 됩니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    select(mapColumn(
        each(<hierarchicalColumn>, match(<matchCondition>), <nameCondition> = $$), ## hierarchical rule-based matching
        <fixedColumn>, ## fixed mapping, no rename
        <renamedFixedColumn> = <fixedColumn>, ## fixed mapping, rename
        each(match(<matchCondition>), <nameCondition> = $$), ## rule-based mapping
        each(patternMatch(<regexMatching>), <nameCondition> = $$) ## regex mapping
    ),
    skipDuplicateMapInputs: { true | false },
    skipDuplicateMapOutputs: { true | false }) ~> <selectTransformationName>
```

### <a name="example"></a>예제

다음은 select 매핑과 해당 데이터 흐름 스크립트의 예입니다.

![스크립트 예제 선택](media/data-flow/select-script-example.png "스크립트 예제 선택")

```
DerivedColumn1 select(mapColumn(
        each(a, match(true())),
        movie,
        title1 = title,
        each(match(name == 'Rating')),
        each(patternMatch(`(y)`),
            $1 + 'regex' = $$)
    ),
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> Select1
```

## <a name="next-steps"></a>다음 단계
* Select를 사용 하 여 이름을 바꾸고, 순서를 바꾸고, 별칭을 사용 하 여 데이터를 데이터 저장소로 이동 하려면 [싱크 변환을](data-flow-sink.md) 사용 합니다.
