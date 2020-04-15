---
title: 응용 프로그램 설정 - LUIS
description: Azure Cognitive Services 언어 이해 앱에 대한 응용 프로그램 설정은 앱 및 포털에 저장됩니다.
ms.topic: reference
ms.date: 04/14/2020
ms.openlocfilehash: 9e17736cd6ff5074a6eab76a6cf5bdb8acedc185
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81382197"
---
# <a name="application-settings"></a>애플리케이션 설정

이러한 응용 프로그램 설정은 [내보낸](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) 앱에 저장되고 REST API로 [업데이트됩니다.](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 앱 버전 설정을 변경하면 앱 학습 상태가 학습되지 않은 상태로 재설정됩니다.

분음 부호 및 문장 부호의 [개념을](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) 알아봅니다.

|설정|기본값|메모|
|--|--|--|
|정규화 구두점|True|문장 부호를 제거합니다.|
|NormalizeDiacritics|True|분음 부호를 제거합니다.|

## <a name="diacritics-normalization"></a>분음 부호 정규화

`settings` 매개 변수의 LUIS JSON 앱 파일에 분음 부호에 대한 발화 정규화를 켭니다.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

다음 발언은 분음 부호 정규화가 발언에 미치는 영향을 보여 준다.

|분음 부호가 false로 설정된 경우|분음 부호가 true로 설정된 경우|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>분음 부호에 대한 언어 지원

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>브라질 포르투갈어 `pt-br` 분음 부호

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|-|-|
|`á`|`a`|
|`â`|`a`|
|`ã`|`a`|
|`à`|`a`|
|`ç`|`c`|
|`é`|`e`|
|`ê`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ô`|`o`|
|`õ`|`o`|
|`ú`|`u`|
|||

#### <a name="dutch-nl-nl-diacritics"></a>네덜란드 `nl-nl` 분음 부호

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|-|-|
|`á`|`a`|
|`à`|`a`|
|`é`|`e`|
|`ë`|`e`|
|`è`|`e`|
|`ï`|`i`|
|`í`|`i`|
|`ó`|`o`|
|`ö`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|||

#### <a name="french-fr--diacritics"></a>프랑스어 `fr-` 분음 부호

여기에는 프랑스어 및 캐나다 하위 문화가 모두 포함됩니다.

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|--|--|
|`é`|`e`|
|`à`|`a`|
|`è`|`e`|
|`ù`|`u`|
|`â`|`a`|
|`ê`|`e`|
|`î`|`i`|
|`ô`|`o`|
|`û`|`u`|
|`ç`|`c`|
|`ë`|`e`|
|`ï`|`i`|
|`ü`|`u`|
|`ÿ`|`y`|

#### <a name="german-de-de-diacritics"></a>독일어 `de-de` 분음 부호

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>이탈리아분음부 `it-it`

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`|
|`í`|`i`|
|`î`|`i`|
|`ò`|`o`|
|`ó`|`o`|
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>스페인어 `es-` 분음 부호

여기에는 스페인어와 캐나다 멕시코가 모두 포함됩니다.

|거짓으로 설정된 분음 부호|분음 부호가 true로 설정|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>문장 부호 정규화

`settings` 매개 변수에서 LUIS JSON 앱 파일에 대한 구두점을 보려면 발화 정규화를 켭니다.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

다음 발언은 문장 부호가 발언에 미치는 영향을 보여 준다.

|문장 부호를 false로 설정한 경우|문장 부호를 True로 설정|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>문장 부호 제거

다음 문장 부호가 `NormalizePunctuation` 제거되고 true로 설정됩니다.

|문장 부호|
|--|
|`-`|
|`.`|
|`'`|
|`"`|
|`\`|
|`/`|
|`?`|
|`!`|
|`_`|
|`,`|
|`;`|
|`:`|
|`(`|
|`)`|
|`[`|
|`]`|
|`{`|
|`}`|
|`+`|
|`¡`|

## <a name="next-steps"></a>다음 단계

* 분음 부호 및 문장 부호의 [개념을](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) 알아봅니다.
