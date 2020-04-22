---
title: 매핑 데이터 흐름의 변환 선택
description: Azure 데이터 팩터리 매핑 데이터 흐름 선택 변환
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/18/2020
ms.openlocfilehash: a90a2def874c7f081f83a34aea956083eb72879a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686491"
---
# <a name="select-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 변환 선택

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

선택 변환을 사용하여 열의 이름을 바꾸거나 놓거나 순서를 변경합니다. 이 변환은 행 데이터를 변경하지 않지만 다운스트림에 전파되는 열을 선택합니다. 

선택 변환에서 사용자는 고정 매핑을 지정하거나 패턴을 사용하여 규칙 기반 매핑을 수행하거나 자동 매핑을 사용하도록 설정할 수 있습니다. 고정 및 규칙 기반 매핑은 모두 동일한 선택 변환 내에서 사용할 수 있습니다. 열이 정의된 매핑 중 하나와 일치하지 않으면 삭제됩니다.

## <a name="fixed-mapping"></a>고정 매핑

프로젝션에 정의된 열이 50개 미만인 경우 정의된 모든 열에는 기본적으로 고정 매핑이 있습니다. 고정 매핑은 정의된 들어오는 열을 가져와정확한 이름을 매핑합니다.

![고정 매핑](media/data-flow/fixedmapping.png "고정 매핑")

> [!NOTE]
> 고정 매핑을 사용하여 드리프트 열을 매핑하거나 이름을 바꿀 수 없습니다.

### <a name="mapping-hierarchical-columns"></a>계층적 열 매핑

고정 매핑을 사용하여 계층 적 열의 하위 열을 최상위 열에 매핑할 수 있습니다. 정의된 계층구조가 있는 경우 열 드롭다운을 사용하여 하위 열을 선택합니다. 선택 변환은 하위 열의 값 및 데이터 형식이 있는 새 열을 만듭니다.

![계층 적 매핑](media/data-flow/select-hierarchy.png "계층 적 매핑")

## <a name="rule-based-mapping"></a>규칙 기반 매핑

한 번에 많은 열을 매핑하거나 드리프트 열을 다운스트림으로 전달하려면 규칙 기반 매핑을 사용하여 열 패턴을 사용하여 매핑을 정의합니다. 의 `name` `type`및 `position` 열의 일치합니다. `stream` 고정 및 규칙 기반 매핑의 조합을 가질 수 있습니다. 기본적으로 50개 이상의 열을 가진 모든 프로젝션은 모든 열에 일치하고 입력된 이름을 출력하는 규칙 기반 매핑으로 기본설정됩니다. 

규칙 기반 매핑을 추가하려면 **매핑 추가를** 클릭하고 **규칙 기반 매핑을**선택합니다.

![규칙 기반 매핑](media/data-flow/rule2.png "규칙 기반 매핑")

각 규칙 기반 매핑에는 일치하는 조건과 매핑된 각 열의 이름을 지정할 두 개의 입력이 필요합니다. 두 값은 식 [작성기](concepts-data-flow-expression-builder.md)에서 입력됩니다. 왼쪽 표현식 상자에 부울 일치 조건을 입력합니다. 오른쪽 표현식 상자에서 일치하는 열을 매핑할 내용을 지정합니다.

![규칙 기반 매핑](media/data-flow/rule-based-mapping.png "규칙 기반 매핑")

구문을 사용하여 `$$` 일치하는 열의 입력 이름을 참조합니다. 위의 이미지를 예로 들어 사용자가 이름이 6자보다 짧은 모든 문자열 열과 일치하려고 한다고 가정해 보겠습니다. 들어오는 열 이 하나인 `test`경우 `$$ + '_short'` 식은 열의 `test_short`이름을 바꿉니다. 이것이 유일한 매핑인 경우 조건을 충족하지 않는 모든 열은 출력된 데이터에서 삭제됩니다.

패턴은 드리프트 열과 정의된 열모두일치합니다. 규칙으로 매핑되는 정의된 열을 보려면 규칙 옆의 안경 아이콘을 클릭합니다. 데이터 미리 보기를 사용하여 출력을 확인합니다.

### <a name="regex-mapping"></a>정규기 매핑

아래쪽 갈매기 아이콘을 클릭하면 정규식 매핑 조건을 지정할 수 있습니다. 정규식 매핑 조건은 지정된 정규식 조건과 일치하는 모든 열 이름과 일치합니다. 표준 규칙 기반 매핑과 함께 사용할 수 있습니다.

![규칙 기반 매핑](media/data-flow/regex-matching.png "규칙 기반 매핑")

위의 예제는 정규식 `(r)` 패턴 또는 소문자 r을 포함하는 열 이름과 일치합니다. 표준 규칙 기반 매핑과 마찬가지로 일치하는 모든 열은 구문을 사용하여 `$$` 오른쪽의 조건에 따라 변경됩니다.

열 이름에 정규식 일치 항목이 여러 개 있는 경우 `$n` 'n'이 어떤 일치 항목을 참조하는지 사용하여 특정 일치 항목을 참조할 수 있습니다. 예를 들어 '$2'는 열 이름 내의 두 번째 일치를 나타냅니다.

### <a name="rule-based-hierarchies"></a>규칙 기반 계층 구조

정의된 프로젝션에 계층구조가 있는 경우 규칙 기반 매핑을 사용하여 계층 하위 열을 매핑할 수 있습니다. 일치 조건과 매핑할 하위 열이 있는 복합 열을 지정합니다. 일치하는 모든 하위 열은 오른쪽에 지정된 '이름으로 이름' 규칙을 사용하여 출력됩니다.

![규칙 기반 매핑](media/data-flow/rule-based-hierarchy.png "규칙 기반 매핑")

위의 예제는 복잡한 열의 `a`모든 하위 열과 일치합니다. `a`두 개의 하위 `b` `c`열과 . 출력 스키마에는 두 개의 `b` 열이 포함되며 `c` '이름 `$$`으로' 조건이 있습니다.

### <a name="parameterization"></a>매개 변수화

규칙 기반 매핑을 사용하여 열 이름을 매개변수화할 수 있습니다. 키워드를 ```name``` 사용하여 들어오는 열 이름을 매개 변수와 일치시면 됩니다. 예를 들어 데이터 흐름 매개 ```mycolumn```변수가 있는 경우 ```mycolumn```와 같은 열 이름과 일치하는 규칙을 만들 수 있습니다. 일치하는 열의 이름을 '비즈니스 키'와 같은 하드 코딩된 문자열로 이름을 변경하고 명시적으로 참조할 수 있습니다. 이 예제에서 일치 조건은 ```name == $mycolumn``` '비즈니스 키'입니다. 

## <a name="auto-mapping"></a>자동 매핑

선택 변환을 추가할 때 **자동 매핑** 슬라이더를 전환하여 자동 매핑을 사용할 수 있습니다. 자동 매핑을 사용하면 선택 변환이 중복 항목을 제외한 모든 들어오는 열을 입력과 동일한 이름으로 매핑합니다. 여기에는 드리프트 열이 포함되며, 이는 출력 데이터에 스키마에 정의되지 않은 열이 포함될 수 있음을 의미합니다. 드리프트 열에 대한 자세한 내용은 [스키마 드리프트를](concepts-data-flow-schema-drift.md)참조하십시오.

![자동 매핑](media/data-flow/automap.png "자동 매핑")

자동 매핑을 켜면 선택 변환은 중복 설정을 건너뛰고 기존 열에 대한 새 별칭을 제공합니다. 앨리어싱은 동일한 스트림 및 자체 조인 시나리오에서 여러 조인 또는 조회를 수행할 때 유용합니다. 

## <a name="duplicate-columns"></a>중복 열

기본적으로 선택 변환은 입력 및 출력 프로젝션 모두에서 중복 열을 삭제합니다. 중복 입력 열은 종종 조인의 각 면에 열 이름이 중복되는 조인 및 조회 변환에서 옵니다. 두 개의 서로 다른 입력 열을 동일한 이름으로 매핑하는 경우 중복 출력 열이 발생할 수 있습니다. 확인란을 전환하여 중복 열을 삭제하거나 전달할지 여부를 선택합니다.

![중복 건너뛰기](media/data-flow/select-skip-dup.png "중복 건너뛰기")

## <a name="ordering-of-columns"></a>열 순서 지정

매핑 순서는 출력 열의 순서를 결정합니다. 입력 열이 여러 번 매핑되는 경우 첫 번째 매핑만 적용됩니다. 중복 된 열 삭제의 경우 첫 번째 일치 항목이 유지됩니다.

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

다음은 선택 매핑 및 데이터 흐름 스크립트의 예입니다.

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
* Select를 사용하여 열의 이름을 바꾸고 순서를 변경한 다음 [싱크 변환을](data-flow-sink.md) 사용하여 데이터를 데이터 저장소에 저장합니다.
