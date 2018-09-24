---
title: Azure에서 Custom Speech Service의 기록 지침 | Microsoft Docs
description: Cognitive Services에서 Custom Speech Service에 대한 데이터를 준비하는 방법을 알아봅니다.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 2772665107f94a273731d2147154e546225b22dd
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46999735"
---
# <a name="transcription-guidelines"></a>기록 지침
음향 및 언어 모델 사용자 지정에 대한 텍스트 데이터를 효과적으로 사용하려면 다음 기록 지침을 따라야 합니다. 이러한 지침은 언어별로 제공됩니다.

## <a name="text-normalization"></a>텍스트 정규화

음향 또는 언어 모델 사용자 지정에서 효율적인 사용을 위해 텍스트 데이터는 정규화되어야 합니다. 즉, 시스템에서 읽을 수 있는 표준, 모호하지 않은 형식으로 변환을 의미합니다. 이 섹션에서는 데이터를 가져올 때 Custom Speech Service에 의해 수행되는 텍스트 정규화 및 사용자가 데이터를 가져오기 전에 수행해야 하는 텍스트 정규화를 설명합니다.

## <a name="inverse-text-normalization"></a>역 텍스트 정규화

"원시" 서식이 지정되지 않은 텍스트를 서식이 지정된 텍스트로 다시 변환하는 프로세스(즉, 대/소문자 및 문장 부호가 있는)를 ITN(역 텍스트 정규화)이라고 합니다. ITN은 Microsoft Cognitive Services Speech API에서 반환된 결과에서 수행됩니다. Custom Speech Service를 사용하여 배포된 사용자 지정 엔드포인트에서는 Microsoft Cognitive Services Speech API와 동일한 ITN을 사용합니다. 그러나 이 서비스는 현재 사용자 지정 ITN을 지원하지 않으므로 사용자 지정 언어 모델에서 도입된 새 용어는 인식 결과에서 서식이 지정되지 않습니다.

## <a name="transcription-guidelines-for-en-us"></a>en-US에 대한 기록 지침

이 서비스에 업로드된 텍스트 데이터는 ASCII 인쇄 가능한 문자 집합만 사용하여 일반 텍스트로 기록되어야 합니다. 파일의 각 줄에는 단일 음성에 대한 텍스트만 포함되어야 합니다.

유니코드 문장 부호 문자를 사용하지 않는 것이 중요합니다. 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 수집하는 경우 실수로 발생할 수 있습니다. 이러한 문자를 적절한 ASCII 대체 문자로 바꿉니다. 예: 

| 사용을 피할 유니코드 | ASCII 대체 문자 |
|----- | ----- |
| “Hello world”(여는 큰따옴표 및 닫는 큰따옴표) | “Hello world”(큰따옴표) |
| John’s day(오른쪽 작은따옴표) | John's day(아포스트로피) |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service에서 수행하는 텍스트 정규화

이 서비스는 언어 데이터 집합 또는 음향 데이터 집합에 대한 기록으로 가져온 데이터에서 다음 텍스트 정규화를 수행합니다. 다음 내용이 포함됩니다.

*   모든 텍스트를 소문자로 변환
*   단어 내부 아포스트로피를 제외한 모든 문장 부호 제거
*   달러 금액을 포함하여 숫자를 음성 형식으로 확장

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| Starbucks Coffee | starbucks coffee |
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman’s sidekick, Robin. | what said batman’s sidekick robin |
| Go get -em! | go get em |
| I’m double-jointed | i’m double jointed |
| 104 Main Street | one oh four main street |
| Tune to 102.7 | tune to one oh two point seven |
| Pi is about 3.14 | pi is about three point one four |
| It costs $3.14 | it costs three fourteen |

### <a name="text-normalization-required-by-users"></a>사용자에게 필요한 텍스트 정규화

데이터를 효과적으로 사용하려면 가져오기 전에 다음 정규화 규칙을 데이터에 적용해야 합니다.

*   음성 형식을 반영하려면 약어를 단어로 기록해야 합니다.
*   비표준 숫자 문자열을 단어로 작성해야 합니다.
*   영문자가 아닌 문자 또는 혼합 영숫자 문자가 포함된 단어는 발음되는 대로 전사해야 합니다.
*   일반적인 약어는 문자 간 마침표 또는 공백 없이 단일 엔터티로 유지할 수 있지만 다른 모든 머리글자어는 각 문자가 단일 공백으로 구분된 별도 문자로 작성되어야 합니다.

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 14 NE 3rd Dr. | fourteen northeast third drive |
| Dr. Strangelove | Doctor Strangelove |
| James Bond 007 | james bond double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| my blood type is O+ | My blood type is O positive |
| water is H20 | water is H 2 O |
| play OU812 by Van Halen | play O U 8 1 2 by Van Halen |

## <a name="transcription-guidelines-for-zh-cn"></a>zh-CN에 대한 기록 지침

Custom Speech Service에 업로드된 텍스트 데이터는 **UTF-8 인코딩(incl. BOM)** 을 사용해야 합니다. 파일의 각 줄에는 단일 음성에 대한 텍스트만 포함되어야 합니다.

반자 문장 부호 문자를 사용하지 않는 것이 중요합니다. 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 수집하는 경우 실수로 발생할 수 있습니다. 이러한 문자를 적절한 전자 대체 문자로 바꿉니다. 예: 

| 사용을 피할 유니코드 | ASCII 대체 문자 |
|----- | ----- |
| “你好”(여는 큰따옴표 및 닫는 큰따옴표) | "你好"(큰따옴표) |
| 需要什么帮助? (물음표) | 需要什么帮助？ |

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service에서 수행하는 텍스트 정규화

이 음성 서비스는 언어 데이터 집합 또는 음향 데이터 집합에 대한 기록으로 가져온 데이터에서 다음 텍스트 정규화를 수행합니다. 다음 내용이 포함됩니다.

*   모든 문장 부호 제거
*   숫자를 음성 형식으로 확장
*   전자 문자를 반자 문자로 변환
*   모든 영어 단어를 대문자로 변환

예를 들어 다음과 같은 노래를 선택할 수 있다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗 ? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

### <a name="text-normalization-required-by-users"></a>사용자에게 필요한 텍스트 정규화

데이터를 효과적으로 사용하려면 가져오기 _전에_ 다음 정규화 규칙을 데이터에 적용해야 합니다.

*   음성 형식을 반영하려면 약어를 단어로 기록해야 합니다.
*   이 서비스는 일부 숫자 수량을 다루지 않습니다. 숫자 문자열은 음성 형식으로 기록하는 것이 더 안정적입니다.

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

## <a name="transcription-guidelines-for-de-de"></a>de-DE에 대한 기록 지침

Custom Speech Service에 업로드된 텍스트 데이터는 **UTF-8 인코딩(incl. BOM)** 만을 사용해야 합니다. 파일의 각 줄에는 단일 음성에 대한 텍스트만 포함되어야 합니다.

### <a name="text-normalization-performed-by-the-custom-speech-service"></a>Custom Speech Service에서 수행하는 텍스트 정규화

이 서비스는 언어 데이터 집합 또는 음향 데이터 집합에 대한 기록으로 가져온 데이터에서 다음 텍스트 정규화를 수행합니다. 다음 내용이 포함됩니다.

*   모든 텍스트를 소문자로 변환
*   영어 또는 독일어 따옴표("test", 'test', “test„ 또는 «test»)를 포함하여 모든 문장 부호 제거
*   ^ ¢ £ ¤ ¥ ¦ § © ª ¬ ® ° ± ² µ × ÿ Ø¬¬의 특수 문자를 포함하는 모든 행 버리기
*   달러 또는 유로 금액을 포함하여 숫자를 단어 형식으로 확장
*   움라우트는 a, o, u에만 허용됩니다. 그 외에는 “th”로 바뀌거나 버려집니다.

다음은 몇 가지 예제입니다.

| 원본 텍스트 | 정규화 후 |
|----- | ----- |
| Frankfurter Ring | frankfurter ring |
| "Hallo, Mama!" sagt die Tochter. | hallo mama sagt die tochter |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |
| Das macht $10 | das macht zehn dollars |


### <a name="text-normalization-required-by-users"></a>사용자에게 필요한 텍스트 정규화

데이터를 효과적으로 사용하려면 가져오기 전에 다음 정규화 규칙을 데이터에 적용해야 합니다.

*   소수점 자릿수에는 ,(콤마)를 사용해야 하고 .(마침표)를 사용하면 안 됨 예:, 2.3%가 아닌 2,3%
*   시간과 분 사이의 시간 구분 기호는 :(콜론)을 사용해야 하고 .(마침표)를 사용하면 안 됨(예: 12:00 Uhr)
*   ‘ca.’, ‘bzw.’와 같은 약어는 바뀌지 않습니다. 정확한 발음을 가지려면 전체 양식을 사용하는 것이 좋습니다.
*   다섯 가지 기본 수학 연산자가 제거됨: +, -, \*, /.
 리터럴 형식으로 바꾸는 것이 좋음: plus, minus, mal, geteilt.
*   비교기(=, <, >) - gleich, kleiner als, grösser als에도 동일하게 적용됨
*   3/4 등의 분수를 단어 형식(예: ¾ 대신 ‘drei viertel’)으로 사용
*   € 기호를 단어 형식 “유로”로 바꾸기


다음은 몇 가지 예제입니다.

| 원본 텍스트 | 사용자 정규화 후 | 시스템 정규화 후
|--------  | ----- | -------- |
| Es ist 12.23Uhr | Es ist 12:23Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 3 < 5 | 3 kleiner als 5 | drei kleiner als vier |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier|
| Das macht 12€ | Das macht 12 Euros | das macht zwölf euros |



## <a name="next-steps"></a>다음 단계
* [사용자 지정 음성-텍스트 엔드포인트를 사용하는 방법](cognitive-services-custom-speech-create-endpoint.md)
* [사용자 지정 음향 모델](cognitive-services-custom-speech-create-acoustic-model.md)로 정확도 개선
* [사용자 지정 언어 모델](cognitive-services-custom-speech-create-language-model.md)로 정확도 개선
