---
title: 응용 프로그램 설정-LUIS
description: Azure Cognitive Services 언어 이해 앱에 대 한 응용 프로그램 설정은 앱 및 포털에 저장 됩니다.
ms.topic: reference
ms.date: 05/04/2020
ms.openlocfilehash: 7b545e0959a43520b7d643ef8c0658a1e1a3b295
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83590991"
---
# <a name="app-and-version-settings"></a>앱 및 버전 설정

이러한 설정은 [내보낸](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) 앱에 저장 되 고 REST API 또는 LUIS 포털로 업데이트 됩니다.

앱 버전 설정을 변경 하면 앱 학습 상태를 학습 되지 않음으로 다시 설정 합니다.

[!INCLUDE [App and version settings](includes/app-version-settings.md)]


텍스트 참조 및 예제는 다음과 같습니다.

* [종결](#punctuation-normalization)
* [부호가](#diacritics-normalization)

## <a name="diacritics-normalization"></a>분음 부호 정규화

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

다음 길이 발언에서는 문장 부호가 길이 발언에 미치는 영향을 보여 줍니다.

|문장 부호가 False로 설정 된|문장 부호가 True로 설정 된|
|--|--|
|`Hmm..... I will take the cappuccino`|`Hmm I will take the cappuccino`|
|||

### <a name="punctuation-removed"></a>문장 부호 제거 됨

이 true로 설정 된 경우 다음 문장 부호가 제거 됩니다 `NormalizePunctuation` .

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

* 분음 부호 및 문장 부호의 [개념](luis-concept-utterance.md#utterance-normalization-for-diacritics-and-punctuation) 에 대해 알아봅니다.
