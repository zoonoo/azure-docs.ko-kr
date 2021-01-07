---
title: Azure Data Factory 매핑 데이터 흐름의 열 패턴
description: Azure Data Factory 매핑 데이터 흐름에서 열 패턴을 사용 하 여 일반화 된 데이터 변환 패턴 만들기
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: 68c211608cfceedaa9d13a595be6d1e5de17f1d5
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2020
ms.locfileid: "94845009"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>매핑 데이터 흐름에서 열 패턴 사용

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

여러 매핑 데이터 흐름 변환을 사용 하면 하드 코드 된 열 이름 대신 패턴을 기반으로 템플릿 열을 참조할 수 있습니다. 이러한 일치를 *열 패턴* 이라고 합니다. 정확한 필드 이름을 요구 하는 대신 이름, 데이터 형식, 스트림, 원점 또는 위치에 따라 열을 일치 시키는 패턴을 정의할 수 있습니다. 열 패턴이 유용한 두 가지 시나리오는 다음과 같습니다.

* 들어오는 원본 필드가 텍스트 파일 또는 NoSQL 데이터베이스에서 열을 변경 하는 경우와 같이 자주 변경 되는 경우 이 시나리오를 [스키마 드리프트](concepts-data-flow-schema-drift.md)라고 합니다.
* 많은 열 그룹에서 일반적인 작업을 수행 하려는 경우 예를 들어 열 이름에 ' total '이 포함 된 모든 열을 double로 캐스팅 하려고 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>파생 열 및 집계의 열 패턴

파생 열, 집계 또는 창 변환에서 열 패턴을 추가 하려면 열 목록 위에 있는 **추가** 를 클릭 하거나 기존 파생 열 옆에 있는 더하기 아이콘을 클릭 합니다. **열 패턴 추가** 를 선택 합니다.

![스크린샷 열 패턴을 추가 하는 더하기 아이콘을 보여 줍니다.](media/data-flow/add-column-pattern.png "열 패턴")

[식 작성기](concepts-data-flow-expression-builder.md) 를 사용 하 여 일치 조건에 입력 합니다. `name`열의,, `type` `stream` , `origin` 및을 기준으로 하는 열과 일치 하는 부울 식을 만듭니다 `position` . 이 패턴은 조건이 true를 반환 하는 열, 데이터베이스가 드리프트 또는 defined 열에 영향을 줍니다.

일치 조건 아래의 두 식 상자는 영향을 받는 열의 새 이름과 값을 지정 합니다. `$$`를 사용 하 여 일치 하는 필드의 기존 값을 참조 합니다. 왼쪽 식 상자는 이름을 정의 하 고 오른쪽 식 상자는 값을 정의 합니다.

![파생 열의 설정 탭을 보여 주는 스크린샷](media/data-flow/edit-column-pattern.png "열 패턴")

위의 열 패턴은 double 형식의 모든 열을 일치 시키고 일치 하는 항목 마다 하나의 파생 열을 만듭니다. `$$`열 이름 필드를 사용 하면 일치 하는 각 열이 같은 이름으로 업데이트 됩니다. 각 열의 값은 두 개의 소수점 점으로 반올림 된 기존 값입니다.

일치 조건이 올바른지 확인 하기 위해 **검사** 탭에서 정의 된 열의 출력 스키마 유효성을 검사 하거나 **데이터 미리 보기** 탭에서 데이터의 스냅숏을 가져올 수 있습니다. 

![스크린샷 출력 스키마 탭을 표시 합니다.](media/data-flow/columnpattern3.png "열 패턴")

### <a name="hierarchical-pattern-matching"></a>계층 패턴 일치

복합 계층 구조 내 에서도 패턴 일치를 빌드할 수 있습니다. `Each MoviesStruct that matches`데이터 스트림의 각 계층에 대 한 메시지가 표시 되는 섹션을 확장 합니다. 그런 다음 선택한 계층 내에서 속성에 대해 일치 하는 패턴을 빌드할 수 있습니다.

![계층적 열 패턴을 보여 주는 스크린샷](media/data-flow/patterns-hierarchy.png "계층의 열 패턴")

## <a name="rule-based-mapping-in-select-and-sink"></a>Select 및 sink의 규칙 기반 매핑

원본에서 열을 매핑하고 변환을 선택 하는 경우 고정 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 열에 대 한 `name` , `type` , `stream` , `origin` 및을 기준으로 일치 시킵니다 `position` . 고정 및 규칙 기반 매핑의 모든 조합을 사용할 수 있습니다. 기본적으로 50 개 이상의 열을 포함 하는 모든 프로젝션은 모든 열에서 일치 하는 규칙 기반 매핑으로 기본 설정 되며 입력 이름을 출력 합니다. 

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

### <a name="rule-based-hierarchies"></a>규칙 기반 계층

정의 된 프로젝션에 계층이 있는 경우 규칙 기반 매핑을 사용 하 여 계층 하위 열을 매핑할 수 있습니다. 일치 조건 및 하위 열을 매핑하려는 복합 열을 지정 합니다. 오른쪽에 지정 된 ' 이름 ' 규칙을 사용 하 여 일치 하는 모든 하위 열이 출력 됩니다.

![계층 구조에 대해를 사용 하는 규칙 기반 매핑을 보여 주는 스크린샷](media/data-flow/rule-based-hierarchy.png "규칙 기반 매핑")

위의 예는 복합 열의 모든 하위 열에서 일치 합니다 `a` . `a` 에는 두 개의 하위 열 및가 포함 되어 있습니다 `b` `c` . 출력 스키마는 두 개의 열을 포함 `b` 하 고 `c` ' Name as ' 조건은입니다 `$$` .

## <a name="pattern-matching-expression-values"></a>패턴 일치 식 값입니다.

* `$$` 런타임에 각 일치 항목의 이름이 나 값으로 변환 합니다. `$$`와 동등한 것으로 간주 `this` 합니다.
* `name` 들어오는 각 열의 이름을 나타냅니다.
* `type` 들어오는 각 열의 데이터 형식을 나타냅니다.
* `stream` 각 스트림 또는 흐름의 변환과 관련 된 이름을 나타냅니다.
* `position` 데이터 흐름에서 열의 서 수 위치입니다.
* `origin` 열이 시작 되거나 마지막으로 업데이트 된 변환입니다.

## <a name="next-steps"></a>다음 단계
* 데이터 변환에 대 한 매핑 데이터 흐름 [식 언어](data-flow-expression-functions.md) 에 대 한 자세한 정보
* [싱크 변환](data-flow-sink.md) 에서 열 패턴을 사용 하 고 규칙 기반 매핑을 사용 하 여 [변환 선택](data-flow-select.md)
