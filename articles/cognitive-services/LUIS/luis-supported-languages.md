---
title: 지역화 지원 - LUIS(Language Understanding) - Azure Cognitive Services | Microsoft Docs
description: LUIS는 서비스 내에 다양한 기능을 포함합니다. 모든 기능이 동일한 언어 패리티에 있는 것은 아닙니다. 관심 있는 기능이 사용자가 원하는 언어 문화권에서 지원되는지 확인합니다. LUIS 앱은 문화권에 관련되며 설정된 후에는 변경할 수 없습니다.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/17/2017
ms.author: diberry
ms.openlocfilehash: 4fa58843f7e888a8fc1cfbbf76a8131bba6c488a
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2018
ms.locfileid: "42107357"
---
# <a name="culture-specific-understanding-in-luis-apps"></a>LUIS 앱의 문화권 관련 이해

LUIS는 서비스 내에 다양한 기능을 포함합니다. 모든 기능이 동일한 언어 패리티에 있는 것은 아닙니다. 관심 있는 기능이 사용자가 원하는 언어 문화권에서 지원되는지 확인합니다. LUIS 앱은 문화권에 관련되며 설정된 후에는 변경할 수 없습니다. 

## <a name="multi-language-luis-apps"></a>다국어 LUIS 앱
챗봇과 같은 다국어 LUIS 클라이언트 응용 프로그램이 필요한 경우, 몇 가지 옵션이 있습니다. LUIS가 모든 언어를 지원하는 경우에는 언어별로 LUIS 앱을 개발합니다. 각 LUIS 앱에는 고유한 앱 ID와 엔드포인트 로그가 있습니다. 지원되지 않는 언어 LUIS에 대한 Language Understanding을 제공해야 하는 경우, [Microsoft Translator API](../Translator/translator-info-overview.md)를 사용하여 발화를 지원되는 언어로 번역하고, 발화를 LUIS 엔드포인트에 제출하고, 결과 점수를 받을 수 있습니다.

## <a name="languages-supported"></a>지원되는 언어
LUIS는 발화를 다음 언어로 이해합니다.


| 언어 |로캘  |  미리 빌드된 도메인 | 미리 빌드된 엔터티 | 구문 제안 | \**[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)<br>(감정 및<br>키워드)| 
|--|--|:--:|:--:|:--:|:--:|
| 미국 영어 |`en-US` | ✔ | ✔  |✔|✔|
| 캐나다 프랑스어 |`fr-CA` |-|   -   |-|✔|
| *[중국어](#chinese-support-notes) |`zh-CN` | ✔ | ✔ |✔|-|
| 네덜란드어 |`nl-NL` |-|  -   |-|✔|
| 프랑스어(프랑스) |`fr-FR` |-| ✔ |✔ |✔|
| 독일어 |`de-DE` |-| ✔ |✔ |✔|
| 이탈리아어 |`it-IT` |-| ✔ |✔|✔|
| *[일본어](#japanese-support-notes) |`ja-JP` |-| ✔ |✔|주요 구문만|
| 한국어 |`ko-KR` |-|   -   |-|주요 구문만|
| 포르투갈어(브라질) |`pt-BR` |-| ✔ |✔ |일부 하위 문화권은 아님|
| 스페인어(스페인) |`es-ES` |-| ✔ |✔|✔|
| 스페인어(멕시코)|`es-MX` |-|  -   |✔|✔|


언어 지원은 [미리 빌드된 엔터티](luis-reference-prebuilt-entities.md) 및 [미리 빌드된 도메인](luis-reference-prebuilt-domains.md)에 따라 다릅니다. 

### <a name="chinese-support-notes"></a>*중국어 지원 참고

 - `zh-cn` 문화권에서 LUIS는 기존 문자 집합 대신 중국어 간체 문자 집합을 예상합니다.
 - 의도, 엔터티, 기능 및 정규식의 이름은 중국어 또는 로마 문자일 수 있습니다.
 - `zh-cn` 문화권에서 지원되는 미리 빌드된 도메인에 대한 정보는 [미리 빌드된 도메인 참조](luis-reference-prebuilt-domains.md)를 참조하세요.
<!--- When writing regular expressions in Chinese, do not insert whitespace between Chinese characters.-->

### <a name="japanese-support-notes"></a>*일본어 지원 참고

 - LUIS는 구문 분석을 제공하지 않으며 Keigo와 비공식 일본어 간의 차이를 이해하지 못하므로 여러 수준의 형식을 응용 프로그램의 학습 예제로 통합해야 합니다. 
     - でございます는 です와 같지 않습니다. 
     - です는 だ와 같지 않습니다. 

### <a name="text-analytics-support-notes"></a>\*\*Text Analytics 지원 노트
Text Analytics에는 keyPhrase 미리 빌드된 엔터티 및 감정 분석이 포함되어 있습니다. 하위 문화권에는 포르투갈어만 지원됩니다. `pt-PT` 및 `pt-BR`. 다른 모든 문화권은 기본 문화권 수준에서 지원됩니다. Text Analytics [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/text-analytics/text-analytics-supported-languages)에 대해 자세히 알아봅니다. 

### <a name="speech-api-supported-languages"></a>Speech API 지원되는 언어
Speech 받아쓰기 모드 언어에 대해서는 Speech [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/Speech/api-reference-rest/supportedlanguages##interactive-and-dictation-mode)를 참조하세요.

### <a name="bing-spell-check-supported-languages"></a>Bing Spell Check 지원되는 언어
지원되는 언어 및 상태 목록은 Bing Spell Check [지원되는 언어](https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/bing-spell-check-supported-languages)를 참조하세요.

## <a name="rare-or-foreign-words-in-an-application"></a>응용 프로그램의 희귀 또는 외래 단어
`en-us` 문화권에서 LUIS는 은어를 포함하여 대부분의 영어 단어를 구분하도록 학습됩니다. `zh-cn` 문화권에서 LUIS는 대부분의 중국어 문자를 구분하도록 학습됩니다. `en-us`의 희귀 단어 또는 `zh-cn`의 문자를 사용할 때 LUIS가 해당 단어나 문자를 구분할 수 없는 것으로 보이면 해당 단어나 문자를 [구문 목록 기능](luis-how-to-add-features.md)에 추가할 수 있습니다. 예를 들어, 응용 프로그램 문화권 외부의 단어(외래 단어)는 구문 목록 기능에 추가해야 합니다. 희귀 단어 집합이 LUIS가 인식하도록 학습되어야 하는 클래스를 구성하지만 희귀 단어가 동의어가 아니고 서로 교환할 수 없음을 나타내기 위해 이 구문 목록을 서로 교환할 수 없는 것으로 표시해야 합니다.

### <a name="hybrid-languages"></a>하이브리드 언어
하이브리드 언어는 영어 및 중국어와 같은 두 문화권의 단어를 결합합니다. 앱은 단일 문화권을 기반으로 하므로 이러한 언어는 LUIS에서 지원되지 않습니다.

## <a name="tokenization"></a>토큰화
기계 학습을 수행하기 위해 LUIS는 문화권에 따라 발화를 [토큰](luis-glossary.md#token)으로 구분합니다. 

|언어|  모든 공백 또는 특수 문자 | 문자 수준|복합 단어|[토큰화된 엔터티가 반환됨](luis-concept-data-extraction.md#tokenized-entity-returned)
|--|:--:|:--:|:--:|:--:|
|중국어||✔||✔|
|네덜란드어|||✔|✔|
|미국 영어(en-us)|✔ ||||
|프랑스어(fr-FR)|✔||||
|프랑스어(fr-CA)|✔||||
|독일어|||✔|✔|
|이탈리아어|✔||||
|일본어||||✔|
|한국어||✔||✔|
|포르투갈어(브라질)|✔||||
|스페인어(es-ES)|✔||||
|스페인어(es-MX)|✔||||

 