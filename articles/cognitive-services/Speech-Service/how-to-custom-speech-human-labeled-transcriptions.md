---
title: 사람이 레이블이 지정 된 기록 지침-음성 서비스
titlesuffix: Azure Cognitive Services
description: 단어 삭제 하거나 올바르게 대체 하지는 발생 하는 문제에 특히 인식 정확도 개선 하려는 경우 사람이 레이블이 지정 된 기록 오디오 데이터와 함께 사용 하는 것이 좋습니다. 사람이 레이블이 지정 된 기록은 무엇 인가요? 간단 하 게, 오디오 파일의 단어 단위로, 정확 하 게 기록 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: 7f0b467284872f3d936984741c6d092705008a5a
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/02/2019
ms.locfileid: "65026729"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>기록 사람이 레이블을 만드는 방법

단어 삭제 하거나 올바르게 대체 하지는 발생 하는 문제에 특히 인식 정확도 개선 하려는 경우 사람이 레이블이 지정 된 기록 오디오 데이터와 함께 사용 하는 것이 좋습니다. 사람이 레이블이 지정 된 기록은 무엇 인가요? 간단 하 게, 오디오 파일의 단어 단위로, 정확 하 게 기록 합니다.

기록 데이터의 10 ~ 1,000 시간의 제공 권장 기록 데이터의 큰 샘플을 인식 기능을 향상 하는 데 필요 합니다. 이 페이지에서는 고품질 기록을 만들 수 있도록 하는 지침을 살펴보겠습니다. 이 가이드는 미국 영어, 북경어 중국어 및 독일어에 대 한 섹션을 사용 하 여 로캘에 따라 세분화 됩니다.

## <a name="us-english-en-us"></a>미국 영어(en-US)

영어 오디오에 대 한 사람이 레이블이 지정 된 기록만 ASCII 문자를 사용 하 여 일반 텍스트로 제공 되어야 합니다. 라틴어-1 또는 유니코드 문장 부호 문자를 사용 하지 않도록 합니다. 이러한 문자는 워드 프로세싱 응용 프로그램 또는 웹 페이지에서 데이터를 스크랩에서 텍스트를 복사 하는 경우에 종종 실수로 추가 됩니다. 이러한 문자가 있는 경우에 적절 한 ASCII 대체를 사용 하 여 업데이트 해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체 문자 | 메모 |
|---------------------|--------------|-------|
| "Hello world" | "Hello world" | 적절 한 ASCII 문자를 사용 하 여 대체 된 중괄호 및 닫는 따옴표입니다. |
| John의 하루 | John의 하루 | 아포스트로피는 적절 한 ASCII 문자를 사용 하 여 값으로 대체 되었습니다. |
| it was good—no, it was great! | it was good--no, it was great! | 하이픈을 두 개를 사용 하 여 em 대시로 대체 되었습니다. |

### <a name="text-normalization-for-us-english"></a>영어 (미국)에 대 한 텍스트 정규화

텍스트 정규화 하는 단어의 모델을 학습할 때 사용 되는 일관 된 형식으로 변환 합니다. 그러나 정규화 규칙에 일부 텍스트를 자동으로 적용 됩니다, 그리고 좋습니다 사람이 레이블이 지정 된 기록 데이터를 준비할 때 다음이 지침을 사용 하 여:

* 단어의 약어를 작성 합니다.
* 단어 (예: 회계 용어)에서 비표준 숫자 문자열을 작성 합니다.
* 발음으로 알파벳이 아닌 문자 또는 혼합된 영숫자 감정과 해야 합니다.
* 단어 발음 있는 약어 (예: "실행", "레이저", "RAM" 또는 "NATO") 편집할 수 없습니다.
* 공백으로 구분 하 여 각 문자를 사용 하 여 별도 문자로 발음 약어 출력 합니다.

대사에서 수행 해야 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 정규화 후 텍스트 |
|---------------|--------------------------|
| Dr. Bruce Banner | Doctor Bruce Banner |
| James Bond, 007 | James Bond, double oh seven |
| Ke$ha | Kesha |
| How long is the 2x4 | How long is the two by four |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| 혈 형식은 내 O + | My blood type is O positive |
| 최고 수 위는 H20 | 최고 수 위는 H 2 O |
| Van Halen 여 OU812 재생 | O U Van Halen 8 1 2 재생 |
| UTF-8 with BOM | U T F 8 with BOM |

다음 정규화 규칙 기록에 자동으로 적용 됩니다.

* 소문자를 사용 합니다.
* 단어 내의 아포스트로피를 제외한 모든 문장 부호를 제거 합니다.
* 달러 금액 등의 단어/음성 형식으로 숫자를 확장 합니다.

대사에서 자동으로 수행 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 정규화 후 텍스트 |
|---------------|--------------------------|
| “Holy cow!” said Batman. | holy cow said batman |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em! | go get em |
| I'm double-jointed | I'm double jointed |
| 104 Elm Street | one oh four Elm street |
| Tune to 102.7 | tune to one oh two seven |
| Pi is about 3.14 | pi is about three point one four |
It costs $3.14| it costs three fourteen |

## <a name="mandarin-chinese-zh-cn"></a>북경어 중국어 (zh-cn)

북경어 중국어 오디오에 대 한 사람이 레이블이 지정 된 기록에는 utf-8 바이트 순서 표식을 사용 하 여 인코딩 이어야 합니다. 반자 문장 부호 문자를 사용하지 않아야 합니다. 이러한 문자 포함할 수 있습니다 실수로 웹 페이지에서 데이터를 수집 하기 위해 워드 프로세서 프로그램에서 데이터를 준비 하는 경우. 이러한 문자가 있는 경우에 적절 한 전자 대체를 사용 하 여 업데이트 해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체 문자 | 메모 |
|---------------------|--------------|-------|
| "你好" | "你好" | 닫는 따옴표 문자를 사용 하 여 대체 된 합니다. |
| 需要什么帮助? | 需要什么帮助？ | 물음표와 적절 한 문자 값으로 대체 되었습니다. |

### <a name="text-normalization-for-mandarin-chinese"></a>북경어 중국어 텍스트 정규화

텍스트 정규화 하는 단어의 모델을 학습할 때 사용 되는 일관 된 형식으로 변환 합니다. 그러나 정규화 규칙에 일부 텍스트를 자동으로 적용 됩니다, 그리고 좋습니다 사람이 레이블이 지정 된 기록 데이터를 준비할 때 다음이 지침을 사용 하 여:

* 단어의 약어를 작성 합니다.
* 숫자 문자열은 음성 형식으로 기록합니다.

대사에서 수행 해야 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 정규화 후 텍스트 |
|---------------|--------------------------|
| 我今年21 | 我今年二十一 |
| 3号楼504 | 三号 楼 五 零 四 |

다음 정규화 규칙 기록에 자동으로 적용 됩니다.

* 모든 문장 부호를 제거 합니다.
* 음성된 형식으로 숫자를 확장 합니다.
* 반자 문자를 반자 문자 변환
* 모든 영어 단어에 대문자 사용

대사에서 자동으로 수행 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 정규화 후 텍스트 |
|---------------|--------------------------|
| 3.1415 | 三 点 一 四 一 五 |
| ￥3.5 | 三 元 五 角 |
| w f y z |W F Y Z |
| 1992年8月8日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午5:00的航班 | 下午 五点 的 航班 |
| 我今年21岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>독일어 (DE-DE) 및 기타 언어

독일어 (및 다른 영어가 아닌 오디오나 북경어 중국어)에 대 한 사람이 레이블이 지정 된 기록 utf-8 바이트 순서 표식을 사용 하 여 인코딩 이어야 합니다. 한 사람이 레이블이 지정 된 대 본 각 오디오 파일에 대해 제공 되어야 합니다.

### <a name="text-normalization-for-german"></a>독일어 텍스트 정규화

텍스트 정규화 하는 단어의 모델을 학습할 때 사용 되는 일관 된 형식으로 변환 합니다. 그러나 정규화 규칙에 일부 텍스트를 자동으로 적용 됩니다, 그리고 좋습니다 사람이 레이블이 지정 된 기록 데이터를 준비할 때 다음이 지침을 사용 하 여:

*   소수점으로 쓰기 ","아니라"."입니다.
*   작성 시간 구분 기호로 ":"아니라"." (예: 12시 Uhr).
*   "ca."와 같은 약어는 대체되지 않습니다. 전체 음성된 형식을 사용 하는 것이 좋습니다.
*   네 개의 기본 수학 연산자(+, -, \* 및 /)를 제거합니다. 작성된 형식을 대체 하는 것이 좋습니다. "더하기," "와 뺀 값 을" "맬웨어", "geteilt."
*   비교 연산자가 제거 됩니다 (=, <, 및 >). "gleich", "kleiner als" 및 "grösser als"로 바꾸는 것이 좋습니다.
*   문서 형식에서 3/4 등의 분수를 작성 (예: "drei viertel" 3/4 대신).
*   작성 된 형태의 "유로입니다." "," 기호 대체

대사에서 수행 해야 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 사용자 정규화 뒤 텍스트 | 시스템 정규화 뒤 텍스트 |
|---------------|-------------------------------|---------------------------------|
| Es ist 12.23 Uhr | Es ist 12:23 Uhr | es ist zwölf uhr drei und zwanzig uhr |
| {12.45} | {12,45} | zwölf komma vier fünf |
| 2 + 3 - 4 | 2 plus 3 minus 4 | zwei plus drei minus vier |

다음 정규화 규칙 기록에 자동으로 적용 됩니다.

* 모든 텍스트에 소문자를 사용 합니다.
* 모든 문장 부호, 다양 한 유형의 따옴표를 포함 하 여 제거 ("test", 'test', "test" 및 «테스트»는 확인).
* 이 집합에서 특수 문자를 사용 하 여 행을 무시:™ ¤ ￥ 개의 § © 이란 ¬® ° ²에서 μ × ÿ Ø¬¬ 합니다.
* 달러 금액 유로 등 음성된 형식으로 숫자를 확장 합니다.
* 움라우트 수락을 o에 대해서만 있습니다. 다른 "th"으로 대체 됩니다 또는 삭제 합니다.

대사에서 자동으로 수행 하는 정규화의 몇 가지 예는 다음과 같습니다.

| 원래 텍스트 | 정규화 후 텍스트 |
|---------------|--------------------------|
| Frankfurter Ring | frankfurter ring |
| ¡Eine Frage! | eine frage |
| wir, haben | wir haben |

## <a name="next-steps"></a>다음 단계

* [준비 및 테스트 데이터](how-to-custom-speech-test-data.md)
* [데이터를 검사 합니다.](how-to-custom-speech-inspect-data.md)
* [데이터를 평가 합니다.](how-to-custom-speech-evaluate-data.md)
* [모델 학습](how-to-custom-speech-train-model.md)
* [모델 배포](how-to-custom-speech-deploy-model.md)
