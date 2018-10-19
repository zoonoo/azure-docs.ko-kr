---
title: V3으로 마이그레이션 - Translator Text API
titlesuffix: Azure Cognitive Services
description: Translator Text API의 V2에서 V3으로 마이그레이션하는 방법을 알아봅니다.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: v-jansko
ms.openlocfilehash: eaf65bef28110d73378c213ae4781a409b86e1bd
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128182"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Translator Text API V2에서 V3으로 마이그레이션

Microsoft Translator 팀은 Translator Text API의 버전 3(V3)을 릴리스했습니다. 이 릴리스에는 새로운 기능, 사용되지 않는 메서드 및 Microsoft Translator Service 간에 데이터를 보내고 받는 새 형식이 포함되어 있습니다. 이 문서에서는 V3을 사용할 응용 프로그램을 변경하는 정보를 제공합니다. V2는 2018년 4월 30일에 사용되지 않고 2019년 4월 30일에 사용이 중단될 예정입니다.

이 문서의 끝에는 자세히 알아볼 유용한 링크가 포함됩니다.

## <a name="summary-of-features"></a>기능 요약

* No Trace - V3에서 No Trace는 Azure Portal에서 모든 가격 책정 계층에 적용됩니다. 이 기능은 V3 API에 제출된 텍스트가 Microsoft에 저장되지 않습니다.
* JSON - XML은 JSON으로 바뀝니다. 서비스에 보내고 서비스로부터 받은 모든 데이터는 JSON 형식입니다.
* 단일 요청의 여러 대상 언어 - 변환 메서드는 단일 요청의 변환에 여러 '대상' 언어를 허용합니다. 예를 들어 단일 요청은 '원본' 영어 및 '대상' 독일어, 스페인어 및 일본어 또는 다른 그룹의 언어일 수 있습니다.
* 이중언어 사전 - 이중언어 사전 메서드가 API에 추가되었습니다. 이 메서드에는 '조회' 및 '예제'가 포함됩니다.
* 음역 - 음역 메서드가 API에 추가되었습니다. 이 메서드는 하나의 스크립트에서 단어 및 문장(예: 아랍어)을 다른 스크립트(예: 라틴어)로 변환합니다.
* 언어 - 새로운 '언어' 메서드는 '번역', '사전' 및 '음역' 메서드와 함께 사용하기 위해 JSON 형식으로 언어 정보를 제공합니다.
* 번역에 대한 새로운 기능 - 새 기능이 '번역' 메서드에 추가되어 별도 메서드로 V2 API에 있던 기능 중 일부를 지원합니다. 예제는 TranslateArray입니다.
* 음성 메서드 - 텍스트 음성 변환 기능이 더 이상 Microsoft 변환기 API에서 지원되지 않습니다. 텍스트 음성 변환 기능은 Azure Cognitive 서비스 Bing Speech API에서 사용할 수 있습니다.

다음과 같은 V2 및 V3 메서드 목록은 V2와 함께 포함된 기능을 제공하는 V3 메서드 및 API를 식별합니다.

| V2 API 메서드   | V3 API 호환성 |
|:----------- |:-------------|
| 번역     | 번역          |
| TranslateArray      | 번역          |
| GetLanguageNames      | 언어          |
| GetLanguagesForTranslate     | 언어        |
| GetLanguagesForSpeak      | Cognitive Services Speech API         |
| 음성     | Cognitive Services Speech API          |
| 감지     | 감지         |
| DetectArray     | 감지         |
| AddTranslation     | Microsoft Translator HUB API         |
| AddTranslationArray    | Microsoft Translator HUB API          |
| BreakSentences      | BreakSentence         |
| GetTranslations      | 기능은 더 이상 지원되지 않습니다.         |
| GetTranslationsArray      | 기능은 더 이상 지원되지 않습니다.         |

## <a name="move-to-json-format"></a>JSON 형식으로 이동

Microsoft Translator Text 번역 V2는 XML 형식으로 데이터를 허용하고 반환했습니다. V3에서 API를 사용하여 보내고 받은 모든 데이터는 JSON 형식입니다. XML은 V3에서 더 이상 허용되거나 반환되지 않습니다. 

이 변경 내용은 V2 텍스트 번역 API에 대해 작성된 응용 프로그램의 여러 측면에 영향을 줍니다. 예를 들어: 언어 API는 텍스트 번역, 음역 및 두 개의 사전 메서드에 대한 언어 정보를 반환합니다. 한 번의 호출에서 모든 메서드에 대한 모든 언어 정보를 요청하거나 개별적으로 요청할 수 있습니다.

언어 메서드에는 인증이 필요하지 않습니다. 다음 링크를 클릭하여 JSON에서 V3에 대한 모든 언어 정보를 확인할 수 있습니다.

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation사전, 음역](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>인증 키

V2에 사용하는 인증 키는 V3에 허용됩니다. 새 구독을 가져올 필요가 없습니다. 장기 마이그레이션 기간 동안 앱에서 V2 및 V3를 혼합할 수 있으며 V2-XML에서 V3-JSON으로 마이그레이션하는 동안 더욱 쉽게 새 버전을 릴리스할 수 있습니다.

## <a name="pricing-model"></a>가격 책정 모델

Microsoft Translator V3는 V2와 동일한 방식으로 공백을 포함한 문자별로 가격 책정됩니다. V3의 새 기능은 청구에 사용하는 일부 문자를 변경했습니다.

| V3 메서드   | 청구에 계산되는 문자 |
|:----------- |:-------------|
| 언어     | 문자가 제출되지 않았습니다. 계산되지 않았습니다. 비용이 청구되지 않습니다.          |
| 번역     | 수는 번역에 제출되는 문자 수 및 번역되는 언어 및 문자 수에 따라 다릅니다. 제출된 50개 문자 및 요청된 5개 언어는 50x5입니다.           |
| 음역     | 음역에 제출된 문자 수가 계산됩니다.         |
| 사전 조회 및 예제     | 사전 조회 및 예제에 제출된 문자 수가 계산됩니다.         |
| BreakSentence     | 무료입니다.       |
| 감지     | 무료입니다.      |

## <a name="v3-end-points"></a>V3 엔드포인트

전역

* api.cognitive.microsofttranslator.com


## <a name="v3-api-text-translations-methods"></a>V3 API 텍스트 번역 메서드

[언어](reference/v3-0-languages.md)

[번역](reference/v3-0-translate.md)

[음역](reference/v3-0-transliterate.md)

[BreakSentence](reference/v3-0-break-sentence.md)

[검색](reference/v3-0-detect.md)

[사전/조회](reference/v3-0-dictionary-lookup.md)

[사전/예제](reference/v3-0-dictionary-examples.md)

## <a name="customization"></a>사용자 지정

Microsoft Translator V3는 기본적으로 신경망 기계 번역을 사용합니다. 따라서 Microsoft Translator Hub로 사용할 수 없습니다. Translator Hub만 기존의 통계 기계 번역을 지원합니다. 이제 신경망 번역에 대한 사용자 지정은 사용자 지정 변환기를 사용하여 사용할 수 있습니다. [신경망 기계 번역을 사용자 지정하는 방법에 대한 자세한 정보](customization.md)

V3 텍스트 API를 포함한 신경망 번역은 표준 범주(SMT, 음성, 텍스트, generalnn)의 사용을 지원하지 않습니다.


## <a name="links"></a>링크

* [Microsoft 개인 정보 취급 방침](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure 법률 정보](https://azure.microsoft.com/support/legal)
* [온라인 서비스 약관](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [V3.0 설명서 보기](reference/v3-0-reference.md)
