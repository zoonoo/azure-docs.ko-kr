---
title: 응용 프로그램 설정-LUIS
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services 언어 이해 앱에 대 한 응용 프로그램 설정은 앱 및 포털에 저장 됩니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 11/12/2019
ms.author: diberry
ms.openlocfilehash: d1ead09f6248a6ad14646371aa70b42b57cf8e3f
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/04/2020
ms.locfileid: "78270813"
---
# <a name="application-settings"></a>애플리케이션 설정

이러한 응용 프로그램 설정은 [내보낸](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) 앱에 저장 되 고 REST api를 사용 하 여 [업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) 됩니다. 앱 버전 설정을 변경 하면 앱 학습 상태를 학습 되지 않음으로 다시 설정 합니다.

|설정|기본값|메모|
|--|--|--|
|NormalizePunctuation|True|문장 부호를 제거 합니다.|
|NormalizeDiacritics|True|분음 부호를 제거 합니다.|

## <a name="diacritics-normalization"></a>분음 부호 정규화

`settings` 매개 변수에서 LUIS JSON 앱 파일의 분음 부호에 대 한 utterance 정규화를 설정 합니다.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
]
```

다음 길이 발언 분음 부호 정규화가 길이 발언에 미치는 영향을 보여 줍니다.

|분음 부호가 false로 설정 된|분음 부호가 true로 설정 된|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>분음 부호에 대 한 언어 지원

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>포르투갈어 (브라질) `pt-br` 분음 부호

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
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

#### <a name="dutch-nl-nl-diacritics"></a>네덜란드어 `nl-nl` 분음 부호

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
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

여기에는 프랑스어 및 캐나다 subcultures 모두 포함 됩니다.

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
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

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
|--|--|
|`ä`|`a`|
|`ö`|`o`|
|`ü`|`u`|

#### <a name="italian-it-it-diacritics"></a>이탈리아어 `it-it` 분음 부호

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
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

여기에는 스페인어와 캐나다 멕시코 모두 포함 됩니다.

|분음 부호가 false로 설정 됩니다.|분음 부호를 true로 설정|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`|
|`ó`|`o`|
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>문장 부호 정규화

`settings` 매개 변수에서 LUIS JSON 앱 파일에 문장 부호에 대 한 utterance 정규화를 설정 합니다.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
]
```

다음 길이 발언에서는 문장 부호가 길이 발언에 미치는 영향을 보여 줍니다.

|문장 부호가 False로 설정 된|문장 부호가 True로 설정 된|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>문장 부호 제거 됨

다음 문장 부호는 `NormalizePunctuation` true로 설정 되어 제거 됩니다.

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
