---
title: 매핑 데이터 흐름의 표현표 작성기
description: Azure 데이터 팩터리에서 데이터 흐름 매핑에 표현식 작성기(표현식 작성기)를 사용하여 표현식 을 빌드합니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.date: 04/08/2020
ms.openlocfilehash: dda2812b5e2cc79d53658d568ba0845d593f41d6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605370"
---
# <a name="build-expressions-in-mapping-data-flow"></a>매핑 데이터 흐름에서 표현식 빌드

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

데이터 흐름을 매핑할 때 많은 변환 속성이 식으로 입력됩니다. 이러한 식은 런타임에 스파크 데이터 유형으로 평가하는 열 값, 매개 변수, 함수, 연산자 및 리터럴로 구성됩니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4tkur]

## <a name="open-expression-builder"></a>식 열기 빌더

Azure 데이터 팩터리 사용자 환경의 식 편집 인터페이스를 식 작성기라고 합니다. 식 논리를 입력할 때 Data Factory는 강조 표시, 구문 검사 및 자동 완성을 위해 [IntelliSense](https://docs.microsoft.com/visualstudio/ide/using-intellisense?view=vs-2019) 코드 완성을 사용합니다.

![표현식 작성기](media/data-flow/xpb1.png "식 작성기")

파생 된 열 및 필터와 같은 변환에서 식이 필수인 경우 파란색 식 상자를 선택하여 식 작성기 열기를 엽니다.

![파란색 식 상자](media/data-flow/expressionbox.png "식 작성기")

일치 또는 그룹별 조건에서 열을 참조할 때 식은 열에서 값을 추출할 수 있습니다. 식을 만들려면 **계산된 열을 선택합니다.**

![계산된 열 옵션](media/data-flow/computedcolumn.png "식 작성기")

식 또는 리터럴 값이 유효한 입력인 경우 **동적 콘텐츠 추가를** 선택하여 리터럴 값으로 평가하는 식을 작성합니다.

![동적 콘텐츠 옵션 추가](media/data-flow/add-dynamic-content.png "식 작성기")

## <a name="expression-language-reference"></a>표현식 언어 참조

데이터 흐름 매핑에는 식에 사용할 수 있는 기본 제공 함수와 연산자가 있습니다. 사용 가능한 함수 목록은 [매핑 데이터 흐름의 Expression 함수를](data-flow-expression-functions.md)참조하십시오.

## <a name="column-names-with-special-characters"></a>특수 문자가 있는 열 이름

특수 문자 나 공백을 포함하는 열 이름이있는 경우 식에서 참조할 수 있는 중괄호로 이름을 둘러싸십시오.

```{[dbo].this_is my complex name$$$}```

## <a name="preview-expression-results"></a>식 보기 결과 미리 보기

[디버그 모드가](concepts-data-flow-debug-mode.md) 켜져 있는 경우 라이브 Spark 클러스터를 사용하여 표현식이 평가하는 내용에 대한 진행 중인 미리 보기를 볼 수 있습니다. 논리를 빌드할 때 표현식을 실시간으로 디버깅할 수 있습니다. 

![진행 중인 미리 보기](media/data-flow/exp4b.png "표현식 데이터 미리 보기")

**새로 고침을** 선택하여 소스의 라이브 샘플에 대해 식의 결과를 업데이트합니다.

![새로 고침 단추](media/data-flow/exp5.png "표현식 데이터 미리 보기")

## <a name="string-interpolation"></a>문자열 보간

따옴표를 사용하여 문자 문자열 텍스트를 식과 함께 둘러싸습니다. 식 함수, 열 및 매개 변수를 포함할 수 있습니다. 문자열 보간은 매개 변수가 쿼리 문자열에 포함될 때 문자열 연결의 광범위한 사용을 방지하는 데 유용합니다. 식 구문을 사용하려면 곱슬 받침대로 둘러싸고

문자열 보간의 몇 가지 예:

* ```"My favorite movie is {iif(instr(title,', The')>0,"The {split(title,', The')[1]}",title)}"```

* ```"select * from {$tablename} where orderyear > {$year}"```

* ```"Total cost with sales tax is {round(totalcost * 1.08,2)}"```

## <a name="comment-expressions"></a>주석 표현식

한 줄 및 다중 줄 주석 구문을 사용하여 표현식에 주석을 추가합니다.

![단일 줄 및 다중 줄 주석 구문](media/data-flow/comments.png "주석")

다음 예제는 유효한 주석입니다.

* ```/* This is my comment */```

* ```/* This is a```
*   ```multi-line comment */```
   
* ```// This is a single line comment```

표현식 맨 위에 주석을 배치하면 변환 식을 문서화하는 변환 텍스트 상자에 표시됩니다.

![변환 텍스트 상자에 주석](media/data-flow/comments2.png "주석")

## <a name="regular-expressions"></a>정규식

많은 식 언어 함수는 정규식 구문을 사용합니다. 정규식 함수를 사용하는 경우 표현식 작성기는\\백슬래시()를 이스케이프 문자 시퀀스로 해석하려고 합니다. 정규식에서 백슬래시를 사용하는 경우 전체 정규식을 백틱()\`으로 둘러싸거나 이중 백슬래시를 사용합니다.

백틱을 사용하는 예제:

```
regex_replace('100 and 200', `(\d+)`, 'digits')
```

이중 슬래시를 사용하는 예:

```
regex_replace('100 and 200', '(\\d+)', 'digits')
```

## <a name="address-array-indexes"></a>주소 배열 인덱스

배열을 반환하는 식 함수를 사용하면 괄호([])를 사용하여 배열 개체를 반환하는 내부의 특정 인덱스를 처리합니다. 배열은 이를 기반으로 합니다.

![식 빌더 배열](media/data-flow/expb2.png "표현식 데이터 미리 보기")

## <a name="keyboard-shortcuts"></a>바로 가기 키

* Ctrl +K Ctrl +C: 전체 줄을 주석.
* Ctrl+K Ctrl+U: 주석 해제.
* F1: 편집기 도움말 명령을 제공합니다.
* Alt+아래쪽 화살표 키: 현재 선을 아래로 이동합니다.
* Alt+Up 화살표 키: 현재 선을 위로 이동합니다.
* Ctrl+스페이스바: 컨텍스트 도움말을 표시합니다.

## <a name="convert-to-dates-or-timestamps"></a>날짜 또는 타임스탬프로 변환

타임스탬프 출력에 문자열 리터럴을 포함하려면 변환을 ```toString()```에서 래핑합니다.

```toString(toTimestamp('12/31/2016T00:12:00', 'MM/dd/yyyy\'T\'HH:mm:ss'), 'MM/dd /yyyy\'T\'HH:mm:ss')```

epoch를 날짜 또는 타임스탬프로 변환하려면 `toTimestamp(<number of milliseconds>)`을 사용합니다. 시간이 몇 초 안에 오면 1,000을 곱합니다.

```toTimestamp(1574127407*1000l)```

이전 식의 끝에 있는 후행 "l"은 인라인 구문으로 긴 형식으로 변환을 의미합니다.

## <a name="next-steps"></a>다음 단계

[데이터 변환 표현식 작성 시작](data-flow-expression-functions.md)
