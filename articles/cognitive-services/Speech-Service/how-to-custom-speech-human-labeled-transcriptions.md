---
title: 사람이 레이블 지정 된 음성 지침-음성 서비스
titleSuffix: Azure Cognitive Services
description: 단어를 삭제 하거나 잘못 대체 하는 경우와 같이 음성 인식 정확도를 향상 시키기 위해 오디오 데이터와 함께 사람이 레이블 지정 된 기능을 사용할 수 있습니다. 사람이 레이블 지정 된 음성으로 오디오 파일의 단어 단위로 단어를 표시 합니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "74806065"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>휴먼 레이블 작성 방법

단어를 삭제 하거나 잘못 대체 했을 때 발생 하는 문제, 특히 인식 정확도를 향상 시키려는 경우 오디오 데이터와 함께 사람이 레이블 지정 된 기능을 사용 하는 것이 좋습니다. 사람이 누구 인가요? 이는 간단 하 고 오디오 파일의 단어 단위로 단어 단위로 말합니다.

인식 기능을 향상 시키기 위해 많은 기록 데이터 샘플이 필요 하며, 10 ~ 1000 시간의 기록 데이터를 제공 하는 것이 좋습니다. 이 페이지에서는 고품질의 고급 기능을 만들 수 있도록 설계 된 지침을 검토 합니다. 이 가이드는 미국 영어, 중국어 (북경어) 및 독일어 섹션을 사용 하 여 로캘로 구분 됩니다.

## <a name="us-english-en-us"></a>미국 영어(en-US)

영어 오디오의 사용자 레이블이 지정 된 음성은 ASCII 문자만 사용 하 여 일반 텍스트로 제공 되어야 합니다. 라틴어-1 또는 유니코드 문장 부호 문자를 사용 하지 마십시오. 이러한 문자는 워드 프로세싱 응용 프로그램에서 텍스트를 복사 하거나 웹 페이지에서 데이터를 스크랩 때 실수로 추가 되는 경우가 많습니다. 이러한 문자가 있으면 적절 한 ASCII 대체를 사용 하 여 업데이트 해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체 | 메모 |
| ------------------- | ------------ | ----- |
| "Hello 세계" | "Hello world" | 여는 따옴표와 닫는 따옴표를 적절 한 ASCII 문자로 대체 했습니다. |
| John의 날 | John의 날 | 아포스트로피를 적절 한 ASCII 문자로 대체 했습니다. |
| it was good—no, it was great! | it was good--no, it was great! | Em 대시는 두 개의 하이픈으로 대체 되었습니다. |

### <a name="text-normalization-for-us-english"></a>영어 (미국)에 대 한 텍스트 정규화

텍스트 표준화는 모델을 학습 하는 데 사용 되는 일관 된 형식으로 단어를 변환 하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용 되지만 사용자 레이블이 지정 된 기록 데이터를 준비할 때 이러한 지침을 사용 하는 것이 좋습니다.

- 약어를 단어로 작성 합니다.
- 표준 이외의 숫자 문자열을 단어로 작성 합니다 (예: 회계 용어).
- 영문자가 아닌 문자 또는 혼합 영숫자 문자는 발음으로 transcribed 합니다.
- 단어로 발음 된 약어는 편집할 수 없습니다 (예: "방사형", "레이저", "RAM" 또는 "개 NATO").
- 각 문자가 공백으로 구분 되는 별도의 문자로 발음 되는 약어를 작성 합니다.

다음은 기록에서 수행 해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트               | 정규화 후 텍스트              |
| --------------------------- | ------------------------------------- |
| Dr. Bruce 배너            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| 내 혈압 형식은 O +입니다.         | My blood type is O positive           |
| 워터 H20                | 물은 H 2 O입니다.                        |
| Van Halen Play OU812     | Van Halen에서 O U 8 1 2 재생           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

다음 정규화 규칙은 자동으로 자동으로 적용 됩니다.

- 소문자를 사용 합니다.
- 단어 내에서 아포스트로피를 제외한 모든 문장 부호를 제거 합니다.
- 숫자를 달러 양과 같은 단어/음성 형식으로 확장 합니다.

다음은 기록에 대해 자동으로 수행 되는 정규화의 몇 가지 예입니다.

| 원래 텍스트                          | 정규화 후 텍스트          |
| -------------------------------------- | --------------------------------- |
| “Holy cow!” said Batman.               | holy cow said batman              |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| It 비용 \$ 3.14                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>북경어 중국어 (zh-cn-CN)

북경어 중국어 오디오의 사용자 레이블이 지정 된 음성 오디오는 바이트 순서 표식을 사용 하 여 u t f-8로 인코딩해야 합니다. 반자 문장 부호 문자를 사용하지 않아야 합니다. 이러한 문자는 워드 프로세싱 프로그램에서 데이터를 준비 하거나 웹 페이지에서 데이터를 스크랩 할 때 실수로 포함 될 수 있습니다. 이러한 문자가 있으면 적절 한 전자 대체가 적용 되도록 업데이트 해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | 대체   | 메모 |
| ------------------- | -------------- | ----- |
| "你好" | "你好" | 여는 따옴표와 닫는 따옴표를 적절 한 문자로 대체 했습니다. |
| 需要什么帮助? | 需要什么帮助？| 물음표를 적절 한 문자로 대체 했습니다. |

### <a name="text-normalization-for-mandarin-chinese"></a>북경어 중국어의 텍스트 정규화

텍스트 표준화는 모델을 학습 하는 데 사용 되는 일관 된 형식으로 단어를 변환 하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용 되지만 사용자 레이블이 지정 된 기록 데이터를 준비할 때 이러한 지침을 사용 하는 것이 좋습니다.

- 약어를 단어로 작성 합니다.
- 숫자 문자열은 음성 형식으로 기록합니다.

다음은 기록에서 수행 해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 我今年 21 | 我今年二十一 |
| 3 号楼 504 | 三号 楼 五 零 四 |

다음 정규화 규칙은 자동으로 자동으로 적용 됩니다.

- 모든 문장 부호 제거
- 숫자를 음성으로 확장 양식
- 전자 문자를 반자 문자로 변환 합니다.
- 모든 영어 단어에 대문자 사용

다음은 기록에 대해 자동으로 수행 되는 정규화의 몇 가지 예입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| ¥3.5 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 年 8 月 8 日 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 下午 5:00 的航班 | 下午 五点 的 航班 |
| 我今年 21 岁 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>독일어 (de) 및 기타 언어

독일 오디오 (및 기타 영어가 아닌 중국어 또는 북경어)에 대 한 음성으로 레이블이 지정 된 음성은 바이트 순서 표식을 사용 하 여 u t f-8로 인코딩해야 합니다. 각 오디오 파일에 대해 사람이 지정 된 한 가지 성적 증명서를 제공 해야 합니다.

### <a name="text-normalization-for-german"></a>독일어 텍스트 정규화

텍스트 표준화는 모델을 학습 하는 데 사용 되는 일관 된 형식으로 단어를 변환 하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용 되지만 사용자 레이블이 지정 된 기록 데이터를 준비할 때 이러한 지침을 사용 하는 것이 좋습니다.

- "."가 아닌 ","로 소수점을 씁니다.
- ":"이 아닌 ":"으로 시간 구분 기호를 작성 합니다. (예: 12:00 Uhr).
- "ca."와 같은 약어는 대체되지 않습니다. 전체 음성 폼을 사용 하는 것이 좋습니다.
- 네 개의 기본 수학 연산자(+, -, \* 및 /)를 제거합니다. "더하기," "빼기", "잘못 된" 및 "geteilt"와 같은 필기 형식으로 바꾸는 것이 좋습니다.
- 비교 연산자 (=, < 및 >)가 제거 됩니다. "gleich", "kleiner als" 및 "grösser als"로 바꾸는 것이 좋습니다.
- 쓰기 (예: 3/4 대신 "drei viertel")의 쓰기 (예: 3/4)입니다.
- "€" 기호를 "유로화"로 바꿉니다.

다음은 기록에서 수행 해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트    | 사용자 정규화 후의 텍스트 | 시스템 정규화 후의 텍스트       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

다음 정규화 규칙은 자동으로 자동으로 적용 됩니다.

- 모든 텍스트에 소문자를 사용 합니다.
- 다양 한 형식의 따옴표 ("테스트", ' 테스트 ', "테스트", «테스트»)를 포함 하 여 모든 문장 부호를 제거 합니다.
- 이 집합에서 특수 문자가 포함 된 행을 삭제 합니다. © ª 사용법® ° ± ² μ μ x ÿ Ø 사용법 사용법.
- 달러 또는 유로 금액을 포함 하 여 숫자를 음성으로 확장 합니다.
- A, o 및 사용자 에게만 움라우트을 허용 합니다. 다른 항목은 "th"로 대체 되거나 삭제 됩니다.

다음은 기록에 대해 자동으로 수행 되는 정규화의 몇 가지 예입니다.

| 원래 텍스트    | 정규화 후 텍스트 |
| ---------------- | ------------------------ |
| Frankfurter Ring | frankfurter ring         |
| ¡Eine Frage!     | eine frage               |
| wir, haben       | wir haben                |

## <a name="next-steps"></a>다음 단계

- [데이터 준비 및 테스트](how-to-custom-speech-test-data.md)
- [데이터 검사](how-to-custom-speech-inspect-data.md)
- [데이터 평가](how-to-custom-speech-evaluate-data.md)
- [모델 학습](how-to-custom-speech-train-model.md)
- [모델 배포](how-to-custom-speech-deploy-model.md)
