---
title: 애플리케이션 설정
titleSuffix: Azure Cognitive Services
description: 언어 이해 앱에 대 한 응용 프로그램 설정을 이해 합니다.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 05/29/2019
ms.author: diberry
ms.openlocfilehash: c19d328c3a5f4dd6dbe14ca94809e42c5655ea72
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66391166"
---
# <a name="application-settings"></a>애플리케이션 설정

이러한 응용 프로그램 설정에 저장 되는 [내보낸](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) 앱 및 [업데이트](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) REST Api를 사용 하 여 합니다. 앱 버전 설정 변경에 학습 되지 않은 앱 학습 신분을 다시 설정 합니다.

|설정|기본값|메모|
|--|--|--|
|NormalizePunctuation|True|문장 부호를 제거합니다.|
|NormalizeDiacritics|True|분음 부호를 제거합니다.|

## <a name="diacritics-normalization"></a>분음 부호 정규화 

Utterance 정규화 LUIS JSON 앱 파일에서 분음 부호 설정의 `settings` 매개 변수입니다.

```JSON
"settings": [
    {"name": "NormalizeDiacritics", "value": "true"}
] 
```

다음 길이 발언 분음 부호 정규화 표현에 미치는 영향을 보여 줍니다.

|False로 설정 하는 분음 부호를 사용 하 여|True로 분음 부호 설정|
|--|--|
|`quiero tomar una piña colada`|`quiero tomar una pina colada`|
|||

### <a name="language-support-for-diacritics"></a>분음 부호에 대 한 언어 지원

#### <a name="brazilian-portuguese-pt-br-diacritics"></a>포르투갈어 (브라질) `pt-br` 분음 부호

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
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

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
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

프랑스어 및 캐나다 하위 문화권이 포함 됩니다.

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
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

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
|--|--|
|`ä`|`a`|
|`ö `|`o`| 
|`ü `|`u`| 

#### <a name="italian-it-it-diacritics"></a>이탈리아어 `it-it` 분음 부호

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
|--|--|
|`à`|`a`|
|`è`|`e`|
|`é`|`e`|
|`ì`|`i`| 
|`í`|`i`| 
|`î`|`i`| 
|`ò`|`o`| 
|`ó `|`o`| 
|`ù`|`u`|
|`ú`|`u`|

#### <a name="spanish-es--diacritics"></a>스페인어 `es-` 분음 부호

스페인어 및 캐나다 멕시코 포함 됩니다.

|분음 부호를 false로 설정 합니다.|분음 부호를 false로 설정 합니다.|
|-|-|
|`á`|`a`|
|`é`|`e`|
|`í`|`i`| 
|`ó `|`o`| 
|`ú`|`u`|
|`ü`|`u`|
|`ñ`|`u`|


## <a name="punctuation-normalization"></a>문장 부호 정규화

Utterance 정규화 LUIS JSON 앱 파일에 문장 부호에 따라 설정 된 `settings` 매개 변수입니다.

```JSON
"settings": [
    {"name": "NormalizePunctuation", "value": "true"}
] 
```

다음 길이 발언 분음 부호 표현에 미치는 영향을 보여 줍니다.

|False로 설정 하는 분음 부호를 사용 하 여|True로 설정 하는 분음 부호를 사용 하 여|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>문장 부호가 제거

다음 문장 부호가 제거 됩니다 `NormalizePunctuation` 설정을 true로 합니다.

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
