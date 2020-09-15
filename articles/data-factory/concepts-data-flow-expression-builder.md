---
title: 데이터 흐름 매핑의 식 작성기
description: Azure Data Factory의 데이터 흐름 매핑에서 식 작성기를 사용 하 여 식을 작성 합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/14/2020
ms.openlocfilehash: 4297cc83ab3fa280e15480aefcd5aef8734c65ee
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531056"
---
# <a name="build-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 식 작성

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름에서는 많은 변환 속성이 식으로 입력 됩니다. 이러한 식은 런타임에 Spark 데이터 형식으로 계산 되는 열 값, 매개 변수, 함수, 연산자 및 리터럴로 구성 됩니다. 데이터 흐름을 매핑하면 **식 작성기**라는 이러한 식을 작성 하는 데 도움이 되는 전용 환경이 있습니다. 강조 표시, 구문 검사 및 자동 완성을 위해  [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense) 코드 완성 기능을 활용 하 여 식 작성기는 데이터 흐름을 쉽게 작성할 수 있도록 설계 되었습니다. 이 문서에서는 식 작성기를 사용 하 여 비즈니스 논리를 효과적으로 작성 하는 방법을 설명 합니다.

![식 작성기](media/data-flow/expresion-builder.png "식 작성기")

## <a name="open-expression-builder"></a>식 작성기 열기

식 작성기를 여는 여러 진입점이 있습니다. 이러한 데이터 흐름은 데이터 흐름 변환의 특정 컨텍스트에 따라 달라 집니다. 가장 일반적인 사용 사례는 [파생 열](data-flow-derived-column.md) 과 같은 변환과 사용자가 데이터 흐름 식 언어를 사용 하 여 열을 만들거나 업데이트 하는 [집계](data-flow-aggregate.md) 에 있습니다. 식 작성기는 열 목록 위에서 **열기 식 작성기** 를 선택 하 여 열 수 있습니다. 열 컨텍스트를 클릭 하 고 해당 식에 대 한 식 작성기를 직접 열 수도 있습니다.

![식 작성기 파생 열기](media/data-flow/open-expression-builder-derive.png "식 작성기 파생 열기")

[필터](data-flow-filter.md)와 같은 일부 변환에서는 파란색 식 텍스트 상자를 클릭 하면 식 작성기가 열립니다. 

![Blue 식 상자](media/data-flow/expressionbox.png "식 작성기")

일치 또는 그룹별 조건에서 열을 참조 하는 경우 식이 열에서 값을 추출할 수 있습니다. 식을 만들려면 **계산 열**을 선택 합니다.

![계산 열 옵션](media/data-flow/computedcolumn.png "식 작성기")

식 또는 리터럴 값이 유효한 입력 인 경우 **동적 콘텐츠 추가** 를 선택 하 여 리터럴 값으로 계산 되는 식을 작성 합니다.

![동적 콘텐츠 추가 옵션](media/data-flow/add-dynamic-content.png "식 작성기")

## <a name="expression-elements"></a>식 요소

데이터 흐름 매핑에서 식은 열 값, 매개 변수, 함수, 지역 변수, 연산자 및 리터럴로 구성 될 수 있습니다. 이러한 식은 문자열, 부울, 정수 등의 Spark 데이터 형식으로 계산 되어야 합니다.

![식 요소](media/data-flow/expression-elements.png "식 요소")

### <a name="functions"></a>Functions

데이터 흐름 매핑에는 식에 사용할 수 있는 기본 제공 함수 및 연산자가 있습니다. 사용 가능한 함수 목록은 [매핑 데이터 흐름 언어 참조](data-flow-expression-functions.md)를 참조 하세요.

#### <a name="address-array-indexes"></a>주소 배열 인덱스

배열 형식을 반환 하는 열 또는 함수를 처리 하는 경우 대괄호 ([])를 사용 하 여 특정 요소에 액세스 합니다. 인덱스가 없으면 식이 NULL로 계산 됩니다.

![식 작성기 배열](media/data-flow/expression-array.png "식 데이터 미리 보기")

> [!IMPORTANT]
> 데이터 흐름 매핑에서 배열은 1부터 시작 하는 의미입니다. 첫 번째 요소는 인덱스 1에서 참조 됩니다. 예를 들어 myArray [1]은 ' myArray ' 라는 배열의 첫 번째 요소에 액세스 합니다.

### <a name="input-schema"></a>입력 스키마

데이터 흐름에서 원본에 정의 된 스키마를 사용 하는 경우 여러 식에서 이름을 기준으로 열을 참조할 수 있습니다. 스키마 드리프트를 활용 하는 경우 또는 함수를 사용 하 여 명시적으로 열을 참조 `byName()` `byNames()` 하거나 열 패턴을 사용 하 여 열을 찾을 수 있습니다.

#### <a name="column-names-with-special-characters"></a>특수 문자가 포함 된 열 이름

특수 문자 또는 공백을 포함 하는 열 이름이 있는 경우 식에서 참조할 수 있도록 이름을 중괄호로 묶습니다.

```{[dbo].this_is my complex name$$$}```

### <a name="parameters"></a>매개 변수

매개 변수는 파이프라인에서 런타임에 데이터 흐름에 전달 되는 값입니다. 매개 변수를 참조 하려면 **식 요소** 보기에서 매개 변수를 클릭 하거나 이름 앞에 달러 기호를 사용 하 여 해당 매개 변수를 참조 합니다. 예를 들어 parameter1 라는 매개 변수는에서 참조 됩니다 `$parameter1` . 자세히 알아보려면 [데이터 흐름 매핑 매개 변수화](parameters-data-flow.md)를 참조 하세요.

### <a name="locals"></a>로컬

여러 열에서 논리를 공유 하거나 논리를 구분 하는 경우 파생 된 column\. 내에 로컬을 만들 수 있습니다. 로컬을 참조 하려면 **식 요소** 보기에서 로컬를 클릭 하거나 이름 앞에 콜론을 사용 하 여 참조 합니다. 예를 들어 local1 라는 로컬은에서 참조 됩니다 `:local1` . [파생 열 설명서](data-flow-derived-column.md#locals)에서 지역에 대해 자세히 알아보세요.

## <a name="preview-expression-results"></a>식 결과 미리 보기

[디버그 모드](concepts-data-flow-debug-mode.md) 를 on으로 설정 하면 디버그 클러스터를 대화형으로 사용 하 여 식의 결과를 미리 볼 수 있습니다. 데이터 미리 보기 옆의 **새로 고침** 을 선택 하 여 데이터 미리 보기의 결과를 업데이트 합니다. 입력 열이 지정 된 경우 각 행의 출력을 볼 수 있습니다.

![진행 중인 미리 보기](media/data-flow/preview-expression.png "식 데이터 미리 보기")

## <a name="string-interpolation"></a>문자열 보간

식 요소를 사용 하는 긴 문자열을 만드는 경우 문자열 보간을 사용 하 여 복잡 한 문자열 논리를 쉽게 빌드할 수 있습니다. 문자열 보간은 매개 변수가 쿼리 문자열에 포함 된 경우 문자열 연결의 광범위 한 사용을 방지 합니다. 리터럴 문자열 텍스트를 식과 함께 묶으려면 큰따옴표를 사용 합니다. 식 함수, 열 및 매개 변수를 포함할 수 있습니다. 식 구문을 사용 하려면 중괄호를 중괄호로 묶습니다.

문자열 보간의 몇 가지 예는 다음과 같습니다.

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

* ```"{:playerName} is a {:playerRating} player"```

## <a name="commenting-expressions"></a>식 주석 달기

한 줄 및 여러 줄 주석 구문을 사용 하 여 식에 주석을 추가 합니다.

다음은 유효한 설명 예입니다.

* ```/* This is my comment */```

* ```/* This is a```
* ```multi-line comment */```

식의 맨 위에 주석을 입력 하면 변환 텍스트 상자에 표시 되어 변환 식을 문서화할 수 있습니다.

![변환 텍스트 상자의 주석](media/data-flow/comment-expression.png "의견")

## <a name="regular-expressions"></a>정규식

많은 식 언어 함수는 정규식 구문을 사용 합니다. 정규식 함수를 사용 하는 경우 식 작성기는 백슬래시 ( \\ )를 이스케이프 문자 시퀀스로 해석 하려고 합니다. 정규식에서 백슬래시를 사용 하는 경우 전체 regex를 backtick ()으로 묶거나 \` 이중 백슬래시를 사용 합니다.

백 틱을 사용 하는 예제:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

이중 슬래시를 사용 하는 예제는 다음과 같습니다.

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="keyboard-shortcuts"></a>바로 가기 키

다음은 식 작성기에서 사용할 수 있는 바로 가기 목록입니다. 식을 만들 때 대부분의 intellisense 바로 가기를 사용할 수 있습니다.

* Ctrl + K Ctrl + C: 줄 전체를 주석으로 처리 합니다.
* Ctrl + K Ctrl + U: 주석 처리를 제거 합니다.
* F1: 편집기 도움말 명령을 제공 합니다.
* Alt + 아래쪽 화살표 키: 현재 줄 아래로 이동 합니다.
* Alt + 위쪽 화살표 키: 현재 줄 위로 이동 합니다.
* Ctrl + 스페이스바: 상황에 맞는 도움말을 표시 합니다.

## <a name="commonly-used-expressions"></a>일반적으로 사용 되는 식

### <a name="convert-to-dates-or-timestamps"></a>날짜 또는 타임 스탬프로 변환

타임 스탬프 출력에 문자열 리터럴을 포함 하려면에서 변환을 래핑합니다 ```toString()``` .

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Epoch에서 날짜 또는 타임 스탬프로 밀리초를 변환 하려면를 사용 `toTimestamp(<number of milliseconds>)` 합니다. 시간을 초 단위로 가져오는 경우 1000를 곱합니다.

```toTimestamp(1574127407*1000l)```

이전 식의 끝에 있는 후행 "l"은 long 형식으로의 변환을 인라인 구문으로 나타냅니다.

### <a name="find-time-from-epoch-or-unix-time"></a>Epoch 또는 Unix 시간에서 시간 찾기

toLong (currentTimestamp ()-toTimestamp (' 1970-01-01 00:00:00.000 ', ' yyyy-MM-dd HH: MM: ss). SSS ')) * 1000l

## <a name="next-steps"></a>다음 단계

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
