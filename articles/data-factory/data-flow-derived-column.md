---
title: 데이터 흐름 매핑의 파생된 열 변환
description: 매핑 데이터 흐름 파생 열 변환을 사용하여 Azure Data Factory에서 대규모로 데이터를 변환하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 38ec2d4619f47bf9fc4d1815cb6e9990cef72dcf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606497"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 파생된 열 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

## <a name="derived-column-settings"></a>파생 열 설정

기존 열을 재정의하려면 열 드롭다운을 통해 선택합니다. 그렇지 않으면 열 선택 필드를 텍스트 상자로 사용하고 새 열의 이름을 입력합니다. 파생 열의 식을 작성하려면 '식 입력' 상자를 클릭하여 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 엽니다.

![파생 열 설정](media/data-flow/dc1.png "파생 열 설정")

파생 열을 더 추가하려면 기존 파생 열 위로 마우스를 이동하고 더하기 아이콘을 클릭합니다. **열 추가** 또는 **열 패턴 추가**를 선택합니다. 열 이름이 원본에서 달라진 경우 열 패턴을 유용하게 사용할 수 있습니다. 자세한 내용은 [열 패턴](concepts-data-flow-column-pattern.md)을 참조하세요.

![새 파생 열 선택](media/data-flow/columnpattern.png "새 파생 열 선택")

## <a name="build-schemas-in-output-schema-pane"></a>출력 스키마 창에서 스키마 빌드

스키마를 수정하고 추가하는 열이 출력 스키마 창에 나열됩니다. 여기에서 간단 및 복합 데이터 구조를 대화형으로 빌드할 수 있습니다. 필드를 더 추가하려면 **열 추가**를 선택합니다. 계층 구조를 작성하려면 **하위 열 추가**를 선택합니다.

![하위 열 추가](media/data-flow/addsubcolumn.png "하위 열 추가")

데이터 흐름의 복합 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조하세요.

![복합 열 추가](media/data-flow/complexcolumn.png "열 추가")

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

![파생 예제](media/data-flow/derive-script1.png "파생 예제")

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
