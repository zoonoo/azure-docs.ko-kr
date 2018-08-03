---
title: 음성 학습에 대한 전사 지침 | Microsoft Docs
description: Speech Service에 대한 음향 및 언어 모델과 음성 글꼴을 사용자 지정하기 위해 텍스트를 준비하는 방법을 알아봅니다.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: PanosPeriorellis
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 07/01/2018
ms.author: panosper
ms.openlocfilehash: 6043d01e26d9f28f90e45c2cc194863b3a9cd017
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/17/2018
ms.locfileid: "39072285"
---
# <a name="transcription-guidelines-for-using-speech-service"></a>Speech Service 사용에 대한 전사 지침

**Speech to Text** 또는 **Text to Speech**를 사용자 지정하려면 음성과 함께 텍스트를 제공해야 합니다. 텍스트의 각 줄은 단일 발화에 해당합니다. 텍스트는 음성과 가능한 한 정확하게 일치해야 합니다. 텍스트를 ‘대본’이라고 하며 대본을 특정 형식으로 만들어야 합니다.

Speech 서비스는 입력을 정규화하여 텍스트를 일관되게 유지합니다. 

이 문서에서는 두 가지 유형의 정규화를 모두 설명합니다. 지침은 언어에 따라 약간 달라집니다.

## <a name="us-english-en-us"></a>미국 영어(en-US)

텍스트 데이터는 ASCII 문자 집합만 사용하여 줄당 하나의 발언을 일반 텍스트로 기록해야 합니다.

확장(Latin-1) 또는 유니코드 문장 부호를 사용하지 않도록 합니다. 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 수집할 때 이러한 문자가 실수로 포함될 수 있습니다. 이러한 문자를 적절한 ASCII 대체 문자로 바꿉니다. 예: 

| 피할 문자 | 대체 문자 |
|----- | ----- |
| “Hello world”(여는 큰따옴표 및 닫는 큰따옴표) | “Hello world”(큰따옴표) |
| John’s day(오른쪽 작은따옴표) | John's day(아포스트로피) |
| it was good—no, it was great! (Em 대시) | it was good--no, it was great! (하이픈) |

### <a name="text-normalization-rules-for-english"></a>영어에 대한 텍스트 정규화 규칙

Speech 서비스는 다음 정규화 규칙을 이행합니다.

*   모든 텍스트를 소문자로 변환
*   단어 내부 아포스트로피를 제외한 모든 문장 부호 제거
*   달러 금액을 포함하여 숫자를 음성 형식으로 확장

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

텍스트 대본에 다음 정규화를 적용합니다.

*   약어를 단어로 기록해야 합니다.
*   비표준 숫자 문자열(예: 일부 날짜 또는 회계 양식)은 단어로 기록해야 합니다.
*   영문자가 아닌 문자 또는 혼합 영숫자 문자가 포함된 단어는 발음되는 대로 전사해야 합니다.
*   단어로 발음되는 약어는 그대로 둡니다. 예를 들어 radar, laser, RAM, NATO입니다.
*   개별 문자로 발음되는 약어는 공백으로 분리된 문자로 기록합니다. 예를 들어 IBM, CPU, FBI, TBD, NaN입니다. 

예를 들어 다음과 같은 노래를 선택할 수 있다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |
| UTF-8 with BOM | U T F 8 with BOM |

## <a name="chinese-zh-cn"></a>중국어(zh-CN)

Custom Speech Service에 업로드된 텍스트 데이터는 바이트 순서 표시자가 있는 UTF-8 인코딩을 사용해야 합니다. 파일에는 줄당 하나의 발언이 기록되어야 합니다.

반자 문장 부호 문자를 사용하지 않아야 합니다. 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 수집할 때 이러한 문자가 실수로 포함될 수 있습니다. 적절한 전자 대체 문자로 바꿉니다. 예: 

| 피할 문자 | 대체 문자 |
|----- | ----- |
| “你好”(여는 큰따옴표 및 닫는 큰따옴표) | “你好”(큰따옴표) |
| 需要什么帮助? (물음표) | 需要什么帮助？ |

### <a name="text-normalization-rules-for-chinese"></a>중국어에 대한 텍스트 정규화 규칙

Speech 서비스는 다음 정규화 규칙을 이행합니다.

*   모든 문장 부호 제거
*   숫자를 음성 형식으로 확장
*   전자 문자를 반자 문자로 변환
*   모든 영어 단어를 대문자로 변환

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

텍스트를 가져오기 전에 텍스트에 다음 정규화를 적용합니다.

*   약어는 음성 형식에 따라 단어로 기록해야 합니다.
*   숫자 문자열은 음성 형식으로 기록합니다.

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="other-languages"></a>기타 언어

**Speech to Text** Service에 업로드된 텍스트 데이터는 바이트 순서 표시자가 있는 UTF-8 인코딩을 사용해야 합니다. 파일에는 줄당 하나의 발언이 기록되어야 합니다.

> [!NOTE]
> 이러한 예제에서는 독일어를 사용합니다. 그러나 이러한 지침은 미국 영어 또는 중국어 이외의 모든 언어에 적용됩니다.

### <a name="text-normalization-rules-for-german"></a>독일어에 대한 텍스트 정규화 규칙

Speech 서비스는 다음 정규화 규칙을 이행합니다.

*   모든 텍스트를 소문자로 변환
*   다양한 유형의 따옴표(“test”, ‘test’, “test„ 또는 «테스트»)를 포함하여 모든 문장 부호 제거
*   집합 ¢ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬의 특수 문자를 포함하는 행 버리기
*   달러 또는 유로 금액을 포함하여 숫자를 단어 형식으로 확장
*   움라우트는 a, o, u에만 허용됩니다. 그 외에는 “th”로 바뀌거나 버려집니다.

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

텍스트를 가져오기 전에 텍스트에 다음 정규화를 적용합니다.

*   소수점 자릿수에는 ","(콤마)를 사용해야 하고 "."(마침표)를 사용하면 안 됨
*   시간과 분 사이의 시간 구분 기호는 “:”을 사용해야 하고 “.”: 12:00 Uhr을 사용하면 안 됨
*   ‘ca.’와 같은 약어는 대체되지 않습니다. 전체 형식을 사용하는 것이 좋습니다.
*   다섯 가지 기본 수학 연산자가 제거됨: +, -, \*, /. 리터럴 형식으로 바꾸는 것이 좋음: plus, minus, mal, geteilt.
*   비교 연산자(=, <, >) - gleich, kleiner als, grösser als에도 동일하게 적용됨
*   3/4 등의 분수를 단어 형식(예: ¾ 대신 ‘drei viertel’)으로 사용
*   € 기호를 단어 형식 “유로”로 바꾸기

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 사용자 정규화 후 | 시스템 정규화 후
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf ||
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|

## <a name="next-steps"></a>다음 단계

- [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
- [C#에서 음성 인식](quickstart-csharp-dotnet-windows.md)
