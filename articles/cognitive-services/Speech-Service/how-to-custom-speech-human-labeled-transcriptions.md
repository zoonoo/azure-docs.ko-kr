---
title: 인간 라벨이 붙은 전사 지침 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 단어가 삭제되거나 잘못 대체되는 경우와 같이 음성 인식 정확도를 높이기 위해 오디오 데이터와 함께 사람이 레이블이 지정한 전사를 사용할 수 있습니다. 인간 라벨이 붙은 전사는 오디오 파일의 단어별, 축어적 전사입니다.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: erhopf
ms.openlocfilehash: 81b4ffc8f77673e52bb78f891e3de618b67e0d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806065"
---
# <a name="how-to-create-human-labeled-transcriptions"></a>인간 표지전사를 만드는 방법

인식 정확도를 향상시키고자 하는 경우, 특히 단어가 삭제되거나 잘못 대체될 때 발생하는 문제인 경우 오디오 데이터와 함께 사람이 표시한 전사를 사용하는 것이 좋습니다. 인간 표지된 전사란 무엇입니까? 즉, 쉽게, 그들은 단어로, 오디오 파일의 축어 적 전사입니다.

전사 데이터의 큰 샘플은 인식을 향상시키기 위해 필요합니다, 우리는 전사 데이터의 10 및 1,000 시간 사이에 제공하는 것이 좋습니다. 이 페이지에서는 고품질 전사를 만들 수 있도록 설계된 지침을 검토합니다. 이 가이드는 로캘로 나뉘어져 있으며, 미국 영어, 중국어, 독일어 섹션이 있습니다.

## <a name="us-english-en-us"></a>미국 영어(en-US)

영어 오디오에 대한 인간 레이블이 붙은 전사는 ASCII 문자만 사용하여 일반 텍스트로 제공되어야 합니다. 라틴어-1 또는 유니코드 문장 부호 문자를 사용하지 마십시오. 이러한 문자는 워드 프로세싱 응용 프로그램에서 텍스트를 복사하거나 웹 페이지에서 데이터를 스크랩할 때 실수로 추가되는 경우가 많습니다. 이러한 문자가 있는 경우 적절한 ASCII 대체문자로 업데이트해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | Substitution | 메모 |
| ------------------- | ------------ | ----- |
| "안녕하세요 세계" | "Hello world" | 개폐 인용 부호는 적절한 ASCII 문자로 대체되었습니다. |
| 요한의 날 | 요한의 날 | 아포스트로피는 적절한 ASCII 캐릭터로 대체되었습니다. |
| it was good—no, it was great! | it was good--no, it was great! | em 대시는 2개의 하이픈으로 대체되었습니다. |

### <a name="text-normalization-for-us-english"></a>미국 영어텍스트 정규화

텍스트 정규화는 모델을 학습할 때 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만, 사람이 레이블이 지정한 전사 데이터를 준비할 때 다음 지침을 사용하는 것이 좋습니다.

- 단어로 약어를 적는다.
- 비표준 숫자 문자열을 단어(예: 회계 용어)로 작성합니다.
- 비알파벳 문자 또는 혼합 영숫자 문자는 발음으로 전사해야 합니다.
- 단어로 발음되는 약어는 편집해서는 안 됩니다(예: "레이더", "레이저", "RAM" 또는 "NATO").
- 공백으로 구분된 각 글자로 별도의 문자로 발음되는 약어를 적는다.

다음은 전사에서 수행해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트               | 정규화 후 텍스트              |
| --------------------------- | ------------------------------------- |
| 닥터 브루스 배너            | Doctor Bruce Banner                   |
| James Bond, 007             | James Bond, double oh seven           |
| Ke$ha                       | Kesha                                 |
| How long is the 2x4         | How long is the two by four           |
| The meeting goes from 1-3pm | The meeting goes from one to three pm |
| 내 혈액형은 O+입니다.         | My blood type is O positive           |
| 물은 H20입니다.                | 물은 H 2 O입니다.                        |
| 반 할렌에 의해 OU812 재생     | 반 할렌에 의해 O U 8 1 2를 재생           |
| UTF-8 with BOM              | U T F 8 with BOM                      |

다음 정규화 규칙은 전사에 자동으로 적용됩니다.

- 소문자를 사용합니다.
- 단어 내의 아포스트로피를 제외한 모든 문장 부호를 제거합니다.
- 달러 금액과 같은 단어/음성 형식으로 숫자를 확장합니다.

다음은 전사에서 자동으로 수행되는 정규화의 몇 가지 예입니다.

| 원래 텍스트                          | 정규화 후 텍스트          |
| -------------------------------------- | --------------------------------- |
| “Holy cow!” said Batman.               | holy cow said batman              |
| “What?” said Batman's sidekick, Robin. | what said batman's sidekick robin |
| Go get -em!                            | go get em                         |
| I'm double-jointed                     | I'm double jointed                |
| 104 Elm Street                         | one oh four Elm street            |
| Tune to 102.7                          | tune to one oh two point seven    |
| Pi is about 3.14                       | pi is about three point one four  |
| 그것은 \$3.14 비용                        | it costs three fourteen           |

## <a name="mandarin-chinese-zh-cn"></a>중국어(zh-CN)

중국어 오디오에 대한 인간 라벨 전사는 바이트 순서 마커로 인코딩된 UTF-8이어야 합니다. 반자 문장 부호 문자를 사용하지 않아야 합니다. 이러한 문자는 워드 프로세싱 프로그램에서 데이터를 준비하거나 웹 페이지에서 데이터를 스크레이핑할 때 실수로 포함될 수 있습니다. 이러한 문자가 있는 경우 적절한 전체 너비 대체로 업데이트해야 합니다.

다음은 몇 가지 예입니다.

| 피할 문자 | Substitution   | 메모 |
| ------------------- | -------------- | ----- |
| "가성비 좋은" | "가성비 좋은" | 개폐 인용 부호는 적절한 문자로 대체되었습니다. |
| 需要什么帮助? | 需要什么帮助？| 물음표가 적절한 문자로 대체되었습니다. |

### <a name="text-normalization-for-mandarin-chinese"></a>중국어 텍스트 정규화

텍스트 정규화는 모델을 학습할 때 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만, 사람이 레이블이 지정한 전사 데이터를 준비할 때 다음 지침을 사용하는 것이 좋습니다.

- 단어로 약어를 적는다.
- 숫자 문자열은 음성 형식으로 기록합니다.

다음은 전사에서 수행해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 21 | 我今年二十一 |
| 3 ~504 | 三号 楼 五 零 四 |

다음 정규화 규칙은 전사에 자동으로 적용됩니다.

- 모든 문장 부호 제거
- 숫자를 음성 양식으로 확장
- 전체 너비 문자를 절반 너비 문자로 변환
- 모든 영어 단어에 대문자 사용

다음은 전사에서 자동으로 수행되는 정규화의 몇 가지 예입니다.

| 원래 텍스트 | 정규화 후 텍스트 |
| ------------- | ------------------------ |
| 3.1415 | 三 点 一 四 一 五 |
| 3.5엔 | 三 元 五 角 |
| w f y z | W F Y Z |
| 1992 비 고 8 월 8 일 | 一 九 九 二 年 八 月 八 日 |
| 你吃饭了吗? | 你 吃饭 了 吗 |
| 5:00 | 下午 五点 的 航班 |
| 21 | 我 今年 二十 一 岁 |

## <a name="german-de-de-and-other-languages"></a>독일어(de-DE) 및 기타 언어

독일어 오디오(및 기타 비영어 또는 중국어 언어)에 대한 사람이 레이블이 지정한 전사는 바이트 순서 마커로 인코딩된 UTF-8이어야 합니다. 각 오디오 파일에 대해 사람이 표시한 성적 증명서를 하나 제공해야 합니다.

### <a name="text-normalization-for-german"></a>독일어텍스트 정규화

텍스트 정규화는 모델을 학습할 때 사용되는 일관된 형식으로 단어를 변환하는 것입니다. 일부 정규화 규칙은 텍스트에 자동으로 적용되지만, 사람이 레이블이 지정한 전사 데이터를 준비할 때 다음 지침을 사용하는 것이 좋습니다.

- 소수점을 ""가 아닌 ""로 작성합니다.
- 시간 구분 기호를 "가 아닌 "로 작성합니다." (예: 12:00 Uhr).
- "ca."와 같은 약어는 대체되지 않습니다. 전체 음성 양식을 사용하는 것이 좋습니다.
- 네 개의 기본 수학 연산자(+, -, \* 및 /)를 제거합니다. "플러스", "마이너스", "mal", "geteilt"라는 서면 양식으로 교체하는 것이 좋습니다.
- 비교 연산자가 제거됩니다(=, < 및 >). "gleich", "kleiner als" 및 "grösser als"로 바꾸는 것이 좋습니다.
- 3/4와 같은 분수를 서면 양식으로 작성합니다(예: 3/4 대신 "drei viertel").
- "€" 기호를 "유로"로 바꿉니다.

다음은 전사에서 수행해야 하는 정규화의 몇 가지 예입니다.

| 원래 텍스트    | 사용자 정규화 후 텍스트 | 시스템 정규화 후 텍스트       |
| ---------------- | ----------------------------- | ------------------------------------- |
| Es ist 12.23 Uhr | Es ist 12:23 Uhr              | es ist zwölf uhr drei und zwanzig uhr |
| {12.45}          | {12,45}                       | zwölf komma vier fünf                 |
| 2 + 3 - 4        | 2 plus 3 minus 4              | zwei plus drei minus vier             |

다음 정규화 규칙은 전사에 자동으로 적용됩니다.

- 모든 텍스트에 소문자를 사용합니다.
- 인용 부호의 다양한 유형을 포함하여 모든 문장 부호를 제거 ("테스트", "테스트", "테스트", «테스트»는 확인).
- 이 세트에서 특수 문자로 행을 버리십시오: • • § © ~ ® ° μ μ × ø
- 달러 또는 유로 금액을 포함하여 음성 양식으로 숫자를 확장합니다.
- o, 그리고 당신만을 위해 움라우트를 수락하십시오. 다른 것은 "th"로 대체되거나 폐기될 것입니다.

다음은 전사에서 자동으로 수행되는 정규화의 몇 가지 예입니다.

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
