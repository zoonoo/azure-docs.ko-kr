---
title: 데이터 흐름 매핑의 식 작성기
description: Azure Data Factory의 데이터 흐름 매핑에서 식 작성기를 사용 하 여 식 작성
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/9/2019
ms.openlocfilehash: 01aa2574ac6edd1ce5e1b209eac3e43bbed82fce
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969379"
---
# <a name="building-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 식 작성

매핑 데이터 흐름에서는 많은 변환 속성이 식으로 입력 됩니다. 이러한 식은 런타임에 spark 데이터 형식으로 계산 되는 열 값, 매개 변수, 함수, 연산자 및 리터럴로 구성 됩니다.

## <a name="opening-the-expression-builder"></a>식 작성기 열기

데이터 팩터리 UX의 식 편집 인터페이스는 **식 작성기**로 인식 됩니다. 식 논리에 입력 하면 데이터 팩터리가 강조 표시, 구문 검사 및 자동 완료를 위해 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) 코드 완성 기능을 사용 합니다.

![식 작성기](media/data-flow/xpb1.png "Kifejezésszerkesztő")

식이 필수인 파생 열 및 필터와 같은 변환에서 blue 식 상자를 클릭 하 여 식 작성기를 엽니다.

![식 작성기](media/data-flow/expressionbox.png "Kifejezésszerkesztő")

일치 또는 group by 조건에서 열을 참조할 때 식이 열에서 값을 추출할 수 있습니다. 식을 만들려면 ' 계산 열 ' 옵션을 선택 합니다.

![식 작성기](media/data-flow/computedcolumn.png "Kifejezésszerkesztő")

식 또는 리터럴 값이 유효한 입력 인 경우에는 ' 동적 콘텐츠 추가 '를 사용 하 여 리터럴로 계산 되는 식을 작성할 수 있습니다.

![식 작성기](media/data-flow/add-dynamic-content.png "Kifejezésszerkesztő")

## <a name="expression-language-reference"></a>식 언어 참조

데이터 흐름 매핑은 식에서 사용할 수 있는 기본 제공 함수 및 연산자를 포함 합니다. 사용할 수 있는 함수 목록은 [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md) 참조 페이지에서 찾을 수 있습니다.

## <a name="column-names-with-special-characters"></a>특수 문자가 포함 된 열 이름

특수 문자 또는 공백을 포함 하는 열 이름이 있는 경우 식에서 참조할 수 있도록 이름을 중괄호로 묶습니다.

```{[dbo].this_is my complex name$$$}```

## <a name="previewing-expression-results"></a>식 결과 미리 보기

[디버그 모드](concepts-data-flow-debug-mode.md) 를 설정 하는 경우 라이브 spark 클러스터를 사용 하 여 식이 평가 하는 결과에 대 한 진행 중인 미리 보기를 볼 수 있습니다. 논리를 작성 하는 동안 식을 실시간으로 디버그할 수 있습니다. 

![식 작성기](media/data-flow/exp4b.png "식 데이터 미리 보기")

새로 고침 단추를 클릭 하 여 소스의 라이브 샘플에 대 한 식 결과를 업데이트 합니다.

![식 작성기](media/data-flow/exp5.png "식 데이터 미리 보기")

## <a name="string-interpolation"></a>문자열 보간

리터럴 문자열 텍스트를 식과 함께 묶으려면 큰따옴표를 사용 합니다. 식 함수, 열 및 매개 변수를 포함할 수 있습니다. 문자열 보간은 쿼리 문자열에 매개 변수를 포함 하는 경우 문자열 연결의 광범위 한 사용을 방지 하는 데 유용 합니다. 식 구문을 사용 하려면 중괄호를 중괄호로 묶습니다.

문자열 보간의 몇 가지 예는 다음과 같습니다.

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="commenting-expressions"></a>식 주석 달기

한 줄 및 여러 줄 주석 구문을 사용 하 여 식에 주석을 추가 합니다.

![Megjegyzések](media/data-flow/comments.png "Megjegyzések")

유효한 주석의 예는 다음과 같습니다.

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

식의 맨 위에 주석을 넣으면 변환 텍스트 상자에 다음과 같이 표시 되어 변환 식을 문서화할 수 있습니다.

![Megjegyzések](media/data-flow/comments2.png "Megjegyzések")

## <a name="regular-expressions"></a>Reguláris kifejezések

많은 식 언어 함수는 정규식 구문을 사용 합니다. 정규식 함수를 사용 하는 경우 식 작성기는 백슬래시 (\\)를 이스케이프 문자 시퀀스로 해석 하려고 합니다. 정규식에서 백슬래시를 사용 하는 경우 전체 regex를 틱 (\`)으로 묶거나 이중 백슬래시를 사용 합니다.

틱 사용 예제

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

또는 이중 슬래시 사용

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="addressing-array-indexes"></a>배열 인덱스 주소 지정

배열을 반환 하는 식 함수를 사용 하 여 대괄호 ([])를 사용 하 여 해당 반환 배열 개체 내의 특정 인덱스를 처리 합니다. 배열이 1부터 사용 됩니다.

![식 작성기 배열](media/data-flow/expb2.png "식 데이터 미리 보기")

## <a name="keyboard-shortcuts"></a>Billentyűparancsok

* ```Ctrl-K Ctrl-C```: 주석 전체 줄
* ```Ctrl-K Ctrl-U```: 주석 처리 제거
* ```F1```: 편집기 도움말 명령 제공
* ```Alt-Down Arrow```: 현재 줄 아래로 이동
* ```Alt-Up Arrow```: 현재 줄 위로 이동
* ```Cntrl-Space```: 컨텍스트 도움말 표시

## <a name="convert-to-dates-or-timestamps"></a>날짜 또는 타임 스탬프로 변환

타임 스탬프 출력에 문자열 리터럴을 포함 하려면 ```toString()```으로 변환 해야 합니다.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

Epoch에서 날짜 또는 타임 스탬프로 밀리초로 변환 하려면 `toTimestamp(<number of milliseconds>)`을 사용 합니다. 시간을 초 단위로 가져오는 경우 1000를 곱합니다.

```toTimestamp(1574127407*1000l)```

위 식의 끝에 있는 후행 "l"은 인라인 구문으로 long 형식으로의 변환을 나타냅니다.

## <a name="next-steps"></a>Következő lépések

[데이터 변환 식 작성 시작](data-flow-expression-functions.md)
