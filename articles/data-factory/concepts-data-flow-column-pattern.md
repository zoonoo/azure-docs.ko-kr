---
title: Azure 데이터 팩터리 매핑 데이터 흐름의 열 패턴
description: Azure Data Factory 매핑 데이터 흐름에서 열 패턴을 사용하여 일반화된 데이터 변환 패턴 생성
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/21/2019
ms.openlocfilehash: bfb4eeef482c4944e75b7805642bc93c23195208
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065531"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>데이터 흐름 매핑에 열 패턴 사용

여러 매핑 데이터 흐름 변환을 사용하면 하드 코딩된 열 이름 대신 패턴을 기반으로 템플릿 열을 참조할 수 있습니다. 이 일치를 *열 패턴이라고*합니다. 정확한 필드 이름을 요구하는 대신 이름, 데이터 형식, 스트림 또는 위치를 기반으로 열을 일치시키는 패턴을 정의할 수 있습니다. 열 패턴이 유용한 두 가지 시나리오가 있습니다.

* 들어오는 소스 필드가 텍스트 파일이나 NoSQL 데이터베이스의 열을 변경하는 경우와 같이 자주 변경되는 경우. 이 시나리오를 [스키마 드리프트라고](concepts-data-flow-schema-drift.md)합니다.
* 당신은 열의 큰 그룹에 공통 작업을 수행하려는 경우. 예를 들어 열 이름에 'total'이 있는 모든 열을 두 배로 캐스팅하려고 합니다.

열 패턴은 파생 열, 집계, 선택 및 싱크 변환에서 현재 사용할 수 있습니다.

## <a name="column-patterns-in-derived-column-and-aggregate"></a>파생 된 열 및 집계의 열 패턴

파생 된 열 또는 집계 변환의 집계 탭에 열 패턴을 추가 하려면 기존 열의 오른쪽에 있는 더하기 아이콘을 클릭 합니다. **열 패턴 추가를 선택합니다.** 

![열 패턴](media/data-flow/columnpattern.png "열 패턴")

식 [빌더를](concepts-data-flow-expression-builder.md) 사용하여 일치 조건을 입력합니다. 열 `name`및 `type` `stream` `position` 열의 열을 기반으로 열과 일치하는 부울 식을 만듭니다. 패턴은 조건이 true로 반환되는 모든 열, 드리프트 또는 정의에 영향을 미칩니다.

일치 조건 아래의 두 식 상자는 영향을 받는 열의 새 이름과 값을 지정합니다. 일치하는 `$$` 필드의 기존 값을 참조하는 데 사용합니다. 왼쪽 식 상자는 이름을 정의하고 오른쪽 식 상자는 값을 정의합니다.

![열 패턴](media/data-flow/columnpattern2.png "열 패턴")

위의 열 패턴은 유형 의 모든 열이 double과 일치하고 일치당 하나의 집계 열을 만듭니다. 새 열의 이름은 일치하는 열의 이름과 '_total'로 연결됩니다. 새 열의 값은 기존 이중 값의 반올림된 집계 합계입니다.

일치 조건이 올바른지 확인하려면 **검사** 탭에서 정의된 열의 출력 스키마를 확인하거나 데이터 미리 **보기** 탭에서 데이터의 스냅샷을 얻을 수 있습니다. 

![열 패턴](media/data-flow/columnpattern3.png "열 패턴")

## <a name="rule-based-mapping-in-select-and-sink"></a>선택 및 싱크의 규칙 기반 매핑

원본에서 열을 매핑하고 변환을 선택할 때 고정 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 의 `name` `type`및 `position` 열의 일치합니다. `stream` 고정 및 규칙 기반 매핑의 조합을 가질 수 있습니다. 기본적으로 50개 이상의 열을 가진 모든 프로젝션은 모든 열에 일치하고 입력된 이름을 출력하는 규칙 기반 매핑으로 기본설정됩니다. 

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

### <a name="rule-based-hierarchies"></a>규칙 기반 계층 구조

정의된 프로젝션에 계층구조가 있는 경우 규칙 기반 매핑을 사용하여 계층 하위 열을 매핑할 수 있습니다. 일치 조건과 매핑할 하위 열이 있는 복합 열을 지정합니다. 일치하는 모든 하위 열은 오른쪽에 지정된 '이름으로 이름' 규칙을 사용하여 출력됩니다.

![규칙 기반 매핑](media/data-flow/rule-based-hierarchy.png "규칙 기반 매핑")

위의 예제는 복잡한 열의 `a`모든 하위 열과 일치합니다. `a`두 개의 하위 `b` `c`열과 . 출력 스키마에는 두 개의 `b` 열이 포함되며 `c` '이름 `$$`으로' 조건이 있습니다.

## <a name="pattern-matching-expression-values"></a>패턴 일치 식 값입니다.

* `$$`런타임에 각 일치 의 이름이나 값으로 변환됩니다.
* `name`들어오는 각 열의 이름을 나타냅니다.
* `type`들어오는 각 열의 데이터 형식을 나타냅니다.
* `stream`각 스트림과 연결된 이름 또는 흐름의 변환을 나타냅니다.
* `position`는 데이터 흐름에서 열의 서수 위치입니다.

## <a name="next-steps"></a>다음 단계
* 데이터 변환을 위한 매핑 데이터 흐름 [표현 언어에](data-flow-expression-functions.md) 대해 자세히 알아보기
* [싱크 변환에서](data-flow-sink.md) 열 패턴을 사용하고 규칙 기반 매핑을 사용하여 [변환 선택](data-flow-select.md)
