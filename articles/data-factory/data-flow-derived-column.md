---
title: 데이터 흐름 매핑의 파생된 열 변환
description: 매핑 데이터 흐름 파생 열 변환을 사용하여 Azure Data Factory에서 대규모로 데이터를 변환하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/14/2020
ms.openlocfilehash: 2e90a8779322cf8967ca9a194c6cc760f7c8b8f5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90532027"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 파생된 열 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

## <a name="create-and-update-columns"></a>열 만들기 및 업데이트

파생 열을 만들 때 새 열을 생성 하거나 기존 열을 업데이트할 수 있습니다. **열** 텍스트 상자에 만들고 있는 열을 입력 합니다. 스키마의 기존 열을 재정의 하려면 열 드롭다운을 사용할 수 있습니다. 파생 열의 식을 작성 하려면 **식 입력** 텍스트 상자를 클릭 합니다. 식 입력을 시작 하거나 식 작성기를 열어 논리를 생성할 수 있습니다.

![파생 열 설정](media/data-flow/create-derive-column.png "파생 열 설정")

파생 열을 더 추가 하려면 열 목록 위의 **추가** 를 클릭 하거나 기존 파생 열 옆에 있는 더하기 아이콘을 클릭 합니다. **열 추가** 또는 **열 패턴 추가**를 선택합니다.

![새 파생 열 선택](media/data-flow/add-derived-column.png "새 파생 열 선택")

### <a name="column-patterns"></a>열 패턴

스키마가 명시적으로 정의 되지 않았거나 열 집합을 대량으로 업데이트 하려는 경우에는 패턴 열을 만들 수 있습니다. 열 패턴을 사용 하면 열 메타 데이터에 따라 규칙을 사용 하 여 열을 일치 시키고 일치 하는 각 열에 대해 파생 열을 만들 수 있습니다. 자세한 내용은 파생 열 변환에서 [열 패턴을 작성 하는 방법](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate) 에 대해 알아봅니다.

![열 패턴](media/data-flow/column-pattern-derive.png "열 패턴")

## <a name="building-schemas-using-the-expression-builder"></a>식 작성기를 사용 하 여 스키마 작성

매핑 데이터 흐름 [식 작성기](concepts-data-flow-expression-builder.md)를 사용 하 여 파생 **열** 섹션에서 파생 열을 만들고, 편집 하 고, 관리할 수 있습니다. 변환에서 만들어지거나 변경 된 모든 열이 나열 됩니다. 열 이름을 클릭 하 여 편집할 열 또는 패턴을 대화형으로 선택 합니다. 추가 열을 추가 하려면 **새로 만들기** 를 선택 하 고 단일 열 또는 패턴을 추가할지 여부를 선택 합니다.

![새 열 만들기](media/data-flow/derive-add-column.png "새 열 만들기")

복합 열로 작업할 때 하위 열을 만들 수 있습니다. 이렇게 하려면 임의의 열 옆에 있는 더하기 아이콘을 클릭 하 고 하위 **열 추가**를 선택 합니다. 데이터 흐름의 복합 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조하세요.

![하위 열 추가](media/data-flow/derive-add-subcolumn.png "하위 열 추가")

데이터 흐름의 복합 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조하세요.

![복합 열 추가](media/data-flow/derive-complex-column.png "열 추가")

### <a name="locals"></a>로컬

여러 열에서 논리를 공유 하거나 논리를 구분 하려는 경우 파생 열 변환 내에 로컬을 만들 수 있습니다. 로컬은 다음 변환에 다운스트림을 전파 하지 않는 논리 집합입니다. 식 작성기 내에서 **식 요소로** 이동 하 고 **지역**을 선택 하 여 지역 변수를 만들 수 있습니다. **새로**만들기를 선택 하 여 새 항목을 만듭니다.

![로컬 만들기](media/data-flow/create-local.png "로컬 만들기")

로컬은 함수, 입력 스키마, 매개 변수 및 기타 지역을 포함 하 여 파생 된 열을 포함 하는 모든 expression 요소를 참조할 수 있습니다. 다른 지역 변수를 참조 하는 경우 참조 되는 로컬이 현재 항목의 "위" 여야 하므로 순서가 중요 합니다.

![로컬 만들기 2](media/data-flow/create-local-2.png "로컬 만들기 2")

파생 열에서 로컬을 참조 하려면 **식 요소** 보기에서 로컬를 클릭 하거나 이름 앞에 콜론을 사용 하 여 참조 합니다. 예를 들어 local1 라는 로컬은에서 참조 됩니다 `:local1` . 로컬 정의를 편집 하려면 식 요소 보기에서 해당 정의를 마우스로 가리키고 연필 아이콘을 클릭 합니다.

![지역 사용](media/data-flow/using-locals.png "지역 사용")

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림 `MoviesYear`를 사용하고 두 개의 파생 열을 만드는 `CleanData`라는 파생 열입니다. 첫 번째 파생 열은 `Rating` 열을 정수 형식의 등급 값으로 바꿉니다. 두 번째 파생 열은 이름이 'movies'로 시작하는 각 열과 일치하는 패턴입니다. 일치하는 각 열에 대해 일치하는 열의 값에 'movie_' 접두사를 붙인 값과 같은 `movie` 열을 만듭니다. 

Data Factory UX에서 이 변환은 아래 이미지와 같습니다.

![파생 예제](media/data-flow/derive-script.png "파생 예제")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>다음 단계

- [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아봅니다.
