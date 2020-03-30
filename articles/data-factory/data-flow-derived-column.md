---
title: 매핑 데이터 흐름에서 파생된 열 변환
description: 매핑 데이터 흐름 파생 열 변환을 사용하여 Azure Data Factory에서 대규모로 데이터를 변환하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/15/2019
ms.openlocfilehash: 66396de52b3709c1d9357f32a375a29a8dcdbd1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048746"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름에서 파생된 열 변환

파생 된 열 변환을 사용 하 여 데이터 흐름에서 새 열을 생성 하거나 기존 필드를 수정 합니다.

## <a name="derived-column-settings"></a>파생 열 설정

기존 열을 재정의하려면 열 드롭다운을 통해 열을 선택합니다. 그렇지 않으면 열 선택 필드를 텍스트 상자로 사용하고 새 열 이름을 입력합니다. 파생 된 열의 식을 작성 하려면 '식 입력' 상자를 클릭 하 여 [데이터 흐름 표현식 작성기를](concepts-data-flow-expression-builder.md)엽니다.

![파생 열 설정](media/data-flow/dc1.png "파생 열 설정")

파생 된 열을 추가하려면 기존 파생 된 열 위로 마우스를 가져 가서 더하기 아이콘을 클릭합니다. 열 **추가** 또는 **열 패턴 추가**중 하나를 선택합니다. 열 이름이 소스에서 변수인 경우 열 패턴이 유용할 수 있습니다. 자세한 내용은 [열 패턴을](concepts-data-flow-column-pattern.md)참조하십시오.

![새 파생 열 선택](media/data-flow/columnpattern.png "새 파생 열 선택")

## <a name="build-schemas-in-output-schema-pane"></a>출력 스키마 창에서 스키마 빌드

스키마를 수정하고 추가하는 열은 출력 스키마 창에 나열됩니다. 여기서는 간단하고 복잡한 데이터 구조를 대화형으로 빌드할 수 있습니다. 추가 필드를 추가하려면 **열 추가를**선택합니다. 계층을 빌드하려면 하위 **열 추가를**선택합니다.

![하위 열 추가](media/data-flow/addsubcolumn.png "하위 열 추가")

데이터 흐름에서 복잡한 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리를](format-json.md#mapping-data-flow-properties)참조하십시오.

![복잡한 열 추가](media/data-flow/complexcolumn.png "열 추가")

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

아래 예제는 들어오는 스트림을 `CleanData` `MoviesYear` 가져와 두 개의 파생 된 열을 만드는 파생 된 열입니다. 첫 번째 파생 열은 `Rating` 열을 정수 유형으로 등급 값으로 바꿉습니다. 두 번째 파생 열은 이름이 '동영상'으로 시작하는 각 열과 일치하는 패턴입니다. 일치하는 각 열에 대해 'movie_'으로 `movie` 접두매된 일치하는 열의 값과 동일한 열을 만듭니다. 

데이터 팩터리 UX에서 이 변환은 아래 이미지와 같습니다.

![예제 파생](media/data-flow/derive-script1.png "예제 파생")

이 변환에 대한 데이터 흐름 스크립트는 아래 코드조각에 있습니다.

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

- 데이터 흐름 [매핑 표현 언어에](data-flow-expression-functions.md)대해 자세히 알아봅니다.
