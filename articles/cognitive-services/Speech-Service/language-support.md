---
title: 언어 지원-음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 서비스는 음성 번역을 통해 음성 텍스트 변환 및 텍스트 음성 변환 변환에 대 한 다양 한 언어를 지원 합니다. 이 문서에서는 서비스 기능별 언어 지원에 대 한 포괄적인 목록을 제공 합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: 0b6fea381bd6b4aa8ad3e7061e6f632176c41033
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/29/2020
ms.locfileid: "82113836"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>음성 서비스에 대 한 언어 및 음성 지원

언어 지원은 음성 서비스 기능에 따라 달라 집니다. 다음 표에서는 [음성 텍스트](#speech-to-text), [텍스트 음성](#text-to-speech)변환 및 [음성 변환](#speech-translation) 서비스 제공에 대 한 언어 지원을 요약 합니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

Microsoft Speech SDK와 REST API는 모두 다음 언어 (로캘)를 지원 합니다. 정확도를 높이기 위해 오디오 + 사람이 레이블 지정 된 성적 증명서 또는 관련 텍스트: 문장을 업로드 하 여 언어의 하위 집합에 대 한 사용자 지정이 제공 됩니다. 음성 사용자 지정은 현재 및 `en-US` `de-DE`에만 사용할 수 있습니다. [여기](how-to-custom-speech.md)에서 사용자 지정에 대해 자세히 알아보세요.

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Locale  | 언어                          | 지원됨 | 사용자 지정                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | 아랍어 (아랍에미리트)                      | 예       | 예                                                |
| `ar-BH` | 아랍어 (바레인), 최신 표준 | 예       | 언어 모델                                    |
| `ar-EG` | 아랍어(이집트)                    | 예       | 언어 모델                                    |
| `ar-KW` | 아랍어(쿠웨이트)                   | 예       | 예                                                |
| `ar-QA` | 아랍어(카타르)                    | 예       | 예                                                |
| `ar-SA` | 아랍어(사우디아라비아)             | 예       | 예                                                |
| `ar-SY` | 아랍어(시리아)                    | 예       | 언어 모델                                    |
| `ca-ES` | 카탈로니아어                           | 예       | 언어 모델                                    |
| `da-DK` | 덴마크어(덴마크)                  | 예       | 언어 모델                                    |
| `de-DE` | 독일어(독일)                  | 예       | 음향 모델<br>언어 모델<br>발음 |
| `en-AU` | 영어(오스트레일리아)               | 예       | 음향 모델<br>언어 모델                  |
| `en-CA` | 영어(캐나다)                  | 예       | 음향 모델<br>언어 모델                  |
| `en-GB` | 영어(영국)          | 예       | 음향 모델<br>언어 모델<br>발음 |
| `en-IN` | 영어(인도)                   | 예       | 음향 모델<br>언어 모델                  |
| `en-NZ` | 영어(뉴질랜드)             | 예       | 음향 모델<br>언어 모델                  |
| `en-US` | 영어(미국)           | 예       | 음향 모델<br>언어 모델<br>발음 |
| `es-ES` | 스페인어(스페인)                   | 예       | 음향 모델<br>언어 모델                  |
| `es-MX` | 스페인어(멕시코)                  | 예       | 음향 모델<br>언어 모델                  |
| `fi-FI` | 핀란드어(핀란드)                 | 예       | 언어 모델                                    |
| `fr-CA` | 프랑스어(캐나다)                   | 예       | 음향 모델<br>언어 모델                  |
| `fr-FR` | 프랑스어(프랑스)                   | 예       | 음향 모델<br>언어 모델<br>발음 |
| `gu-IN` | 구자라트어 (인도)                 | 예       | 언어 모델                                    |
| `hi-IN` | 힌디어(인도)                     | 예       | 음향 모델<br>언어 모델                  |
| `it-IT` | 이탈리아어(이탈리아)                   | 예       | 음향 모델<br>언어 모델<br>발음 |
| `ja-JP` | 일본어(일본)                  | 예       | 언어 모델                                    |
| `ko-KR` | 한국어(한국)                    | 예       | 언어 모델                                    |
| `mr-IN` | 마라티어(인도)                   | 예       | 언어 모델                                    |
| `nb-NO` | 노르웨이어(복말)(노르웨이)       | 예       | 언어 모델                                    |
| `nl-NL` | 네덜란드어(네덜란드)               | 예       | 언어 모델                                    |
| `pl-PL` | 폴란드어(폴란드)                   | 예       | 언어 모델                                    |
| `pt-BR` | 포르투갈어(브라질)               | 예       | 음향 모델<br>언어 모델<br>발음 |
| `pt-PT` | 포르투갈어(포르투갈)             | 예       | 언어 모델                                    |
| `ru-RU` | 러시아어(러시아)                  | 예       | 음향 모델<br>언어 모델                  |
| `sv-SE` | 스웨덴어(스웨덴)                  | 예       | 언어 모델                                    |
| `ta-IN` | 타밀어(인도)                     | 예       | 언어 모델                                    |
| `te-IN` | 텔루구어(인도)                    | 예       | 예                                                |
| `th-TH` | 태국어(태국)                   | 예       | 예                                                |
| `tr-TR` | 터키어(터키)                  | 예       | 예                                                |
| `zh-CN` | 중국어(북경어, 간체)    | 예       | 음향 모델<br>언어 모델                  |
| `zh-HK` | 중국어 (광둥어, 번체)  | 예       | 언어 모델                                    |
| `zh-TW` | 중국어(대만어)      | 예       | 언어 모델                                    |

## <a name="text-to-speech"></a>텍스트 음성 변환

Microsoft Speech SDK 및 REST Api는 모두 로캘에 의해 식별 되는 특정 언어 및 언어를 지 원하는 이러한 음성을 지원 합니다.

> [!IMPORTANT]
> 가격 책정은 표준, 사용자 지정 및 인공신경망 음성마다 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 페이지를 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트-음성 변환은 심층 신경망에서 지원되는 새로운 형식의 음성 합성입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다.

신경망을 사용 하 여 자연 스런 봇 및 음성 도우미와의 상호 작용을 보다 자연스럽 게 만들고 활용할 수 있습니다. 예를 들어 전자 서적에서 오디오 책으로의 디지털 텍스트를 변환 하 고 자동차 탐색 시스템을 향상 시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| Locale  | 언어            | 성별 | 전체 서비스 이름 매핑                                               | 짧은 음성 이름        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | 독일어(독일)    | Female | "Microsoft Server Speech Text to Speech Voice (de, KatjaNeural)"     | "KatjaNeural"     |
| `en-US` | 영어(미국)        | Female | "Microsoft Server Speech Text to Speech Voice (en-us, AriaNeural)"      | "en-us-AriaNeural"      |
| `en-US` | 영어(미국)        | Male   | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)"       | "en-us-GuyNeural"       |
| `it-IT` | 이탈리아어(이탈리아)     | Female | "Microsoft Server Speech Text to Speech Voice (it IT, ElsaNeural)"      | "it-ElsaNeural"      |
| `pt-BR` | 포르투갈어(브라질) | Female | "Microsoft Server Speech Text to Speech Voice (pt-BR, FranciscaNeural)" | "FranciscaNeural" |
| `zh-CN` | 중국어(북경어, 간체)  | Female | "Microsoft Server Speech Text to Speech Voice(zh-CN, XiaoxiaoNeural)"  | "zh-cn-XiaoxiaoNeural"  |

> [!IMPORTANT]
> `en-US-JessaNeural` 음성이로 `en-US-AriaNeural`변경 되었습니다. 이전에 "Jessa"를 사용 하는 경우 "Aria"로 변환 합니다.

신경망을 구성 하 고 조정 하는 방법을 알아보려면 [음성 합성 마크업 언어](speech-synthesis-markup.md#adjust-speaking-styles)를 참조 하세요.

> [!TIP]
> 음성 합성 요청에서 전체 서비스 이름 매핑 또는 짧은 음성 이름을 사용할 수 있습니다.

### <a name="standard-voices"></a>표준 음성

75개를 초과하는 표준 음성은 45개 이상의 언어 및 로캘에서 사용할 수 있으며 텍스트를 합성된 음성으로 변환할 수 있습니다. 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| Locale | 언어 | 성별 | 전체 서비스 이름 매핑 | 짧은 이름 |
|--|--|--|--|--|
| <sup>1(sp1)</sup>`ar-EG` | 아랍어(이집트) | Female | "Microsoft Server Speech Text to Speech Voice(ar-EG, Hoda)" | "ar-예-Hoda" |
| `ar-SA` | 아랍어(사우디아라비아) | Male | “Microsoft Server Speech Text to Speech Voice(ar-SA, Naayf)” | "ar-Naayf" |
| `bg-BG` | 불가리아어 | Male | “Microsoft Server Speech Text to Speech Voice(bg-BG, Ivan)” | "bg-BG-Ivan" |
| `ca-ES` | 카탈로니아어(스페인) | Female | “Microsoft Server Speech Text to Speech Voice(ca-ES, HerenaRUS)” | "HerenaRUS" |
| `cs-CZ` | 체코어 | Male | “Microsoft Server Speech Text to Speech Voice(cs-CZ, Jakub)” | "cs-CZ-Jakub" |
| `da-DK` | 덴마크어 | Female | “Microsoft Server Speech Text to Speech Voice(da-DK, HelleRUS)” | "da-HelleRUS" |
| `de-AT` | 독일어(오스트리아) | Male | “Microsoft Server Speech Text to Speech Voice(de-AT, Michael)” | "Michael" |
| `de-CH` | 독일어(스위스) | Male | “Microsoft Server Speech Text to Speech Voice(de-CH, Karsten)” | "Karsten" |
| `de-DE` | 독일어(독일) | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, Hedda)" | "Hedda" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, HeddaRUS)" | "HeddaRUS" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice(de-DE, Stefan, Apollo)" | "Stefan-아폴로" |
| `el-GR` | 그리스어 | Male | “Microsoft Server Speech Text to Speech Voice(el-GR, Stefanos)” | "el-GR-Stefanos" |
| `en-AU` | 영어(오스트레일리아) | Female | "Microsoft Server Speech Text to Speech Voice(en-AU, Catherine)" | "en-us-Catherine" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-AU, HayleyRUS)” | "en-us-HayleyRUS" |
| `en-CA` | 영어(캐나다) | Female | "Microsoft Server Speech Text to Speech Voice(en-CA, Linda)" | "Linda" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-CA, HeatherRUS)” | "HeatherRUS" |
| `en-GB` | 영어(영국) | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, Susan, Apollo)” | "en-us-김소미-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, HazelRUS)” | "en-us-HazelRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-GB, George, Apollo)” | "en-us-George-아폴로" |
| `en-IE` | 영어(아일랜드) | Male | “Microsoft Server Speech Text to Speech Voice(en-IE, Sean)” | "en-us-최유정" |
| `en-IN` | 영어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, Heera, Apollo)” | "en-us-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, PriyaRUS)” | "en-us-PriyaRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-IN, Ravi, Apollo)” | "Ralvi-아폴로" |
| `en-US` | 영어(미국) | Female | “Microsoft Server Speech Text to Speech Voice(en-US, ZiraRUS)” | "en-us-ZiraRUS" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice (en-us, AriaRUS)" | "en-us-AriaRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-US, BenjaminRUS)” | "en-us-BenjaminRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-US, Guy24kRUS)” | "en-us-Guy24kRUS" |
| `es-ES` | 스페인어(스페인) | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, Laura, Apollo)” | "es-김-아폴로-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, HelenaRUS)” | "es-온-우" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(es-ES, Pablo, Apollo)” | "es-Pablo-아폴로" |
| `es-MX` | 스페인어(멕시코) | Female | “Microsoft Server Speech Text to Speech Voice(es-MX, HildaRUS)” | "HildaRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(es-MX, Raul, Apollo)” | "es-MX-Raul-아폴로" |
| `fi-FI` | 핀란드어 | Female | “Microsoft Server Speech Text to Speech Voice(fi-FI, HeidiRUS)” | "fi-HeidiRUS" |
| `fr-CA` | 프랑스어(캐나다) | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, Caroline)” | "fr-fr-Caroline" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, HarmonieRUS)” | "fr-fr-HarmonieRUS" |
| `fr-CH` | 프랑스어(스위스) | Male | “Microsoft Server Speech Text to Speech Voice(fr-CH, Guillaume)” | "fr-fr-Guillaume" |
| `fr-FR` | 프랑스어(프랑스) | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, Julie, Apollo)” | "fr-fr" (Julie-아폴로) |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, HortenseRUS)” | "fr-fr-HortenseRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(fr-FR, Paul, Apollo)” | "fr-fr-아폴로-아폴로" |
| `he-IL` | 히브리어(이스라엘) | Male | “Microsoft Server Speech Text to Speech Voice(he-IL, Asaf)” | "he-Asaf" |
| `hi-IN` | 힌디어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana, Apollo)” | "Kalpana-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana)” | "Kalpana" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(hi-IN, Hemant)” | "Hemant" |
| `hr-HR` | 크로아티아어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)” | "hr-HR-Matej" |
| `hu-HU` | 헝가리어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)” | "hu-hu-HU-HU-Szabolcs" |
| `id-ID` | 인도네시아어 | Male | "Microsoft Server Speech Text to Speech Voice(id-ID, Andika)" | "id-ID-Andika" |
| `it-IT` | 이탈리아어 | Male | “Microsoft Server Speech Text to Speech Voice(it-IT, Cosimo, Apollo)” | "it-IT-아폴로" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(it-IT, LuciaRUS)" | "it-LuciaRUS" |
| `ja-JP` | 일본어 | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ayumi, Apollo)” | "ja-jp-Ayumi-아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ichiro, Apollo)” | "ja-jp-Ichiro-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, HarukaRUS)” | "ja-jp-HarukaRUS" |
| `ko-KR` | 한국어 | Female | “Microsoft Server Speech Text to Speech Voice(ko-KR, HeamiRUS)” | "ko-kr-한국" |
| `ms-MY` | 말레이어 | Male | “Microsoft Server Speech Text to Speech Voice(ms-MY, Rizwan)” | "ms-Rizwan" |
| `nb-NO` | 노르웨이어 | Female | “Microsoft Server Speech Text to Speech Voice(nb-NO, HuldaRUS)” | "nb-HuldaRUS" |
| `nl-NL` | 네덜란드어 | Female | “Microsoft Server Speech Text to Speech Voice(nl-NL, HannaRUS)” | "nl-NL-Hus" |
| `pl-PL` | 폴란드어 | Female | “Microsoft Server Speech Text to Speech Voice(pl-PL, PaulinaRUS)” | "pl-PL-PaulinaRUS" |
| `pt-BR` | 포르투갈어(브라질) | Female | “Microsoft Server Speech Text to Speech Voice(pt-BR, HeloisaRUS)” | "pt-BR-" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(pt-BR, Daniel, Apollo)” | "pt-Daniel-아폴로" |
| `pt-PT` | 포르투갈어(포르투갈) | Female | “Microsoft Server Speech Text to Speech Voice(pt-PT, HeliaRUS)” | "pt – PT-고-미국" |
| `ro-RO` | 루마니아어 | Male | “Microsoft Server Speech Text to Speech Voice(ro-RO, Andrei)” | "ro-RO-Andrei" |
| `ru-RU` | 러시아어 | Female | “Microsoft Server Speech Text to Speech Voice(ru-RU, Irina, Apollo)” | "Irina-아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(ru-RU, Pavel, Apollo)” | "Pavel-아폴로" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(ru-RU, EkaterinaRUS)" | EkaterinaRUS |
| `sk-SK` | 슬로바키아어 | Male | “Microsoft Server Speech Text to Speech Voice(sk-SK, Filip)” | "나이-Filip" |
| `sl-SI` | 슬로베니아어 | Male | “Microsoft Server Speech Text to Speech Voice(sl-SI, Lado)” | "sl-SI-Lado" |
| `sv-SE` | 스웨덴어 | Female | “Microsoft Server Speech Text to Speech Voice(sv-SE, HedvigRUS)” | "HedvigRUS" |
| `ta-IN` | 타밀어(인도) | Male | “Microsoft Server Speech Text to Speech Voice(ta-IN, Valluvar)” | "ta-Valluvar" |
| `te-IN` | 텔루구어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(te-IN, Chitra)” | "te-Chitra" |
| `th-TH` | 태국어 | Male | “Microsoft Server Speech Text to Speech Voice(th-TH, Pattara)” | "Pattara" |
| `tr-TR` | 터키어(터키) | Female | “Microsoft Server Speech Text to Speech Voice(tr-TR, SedaRUS)” | "tr-SedaRUS" |
| `vi-VN` | 베트남어 | Male | “Microsoft Server Speech Text to Speech Voice(vi-VN, An)” | "vi-VN" |
| `zh-CN` | 중국어(북경어, 간체) | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, HuihuiRUS)” | "zh-cn-HuihuiRUS" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, Yaoyao, Apollo)” | "zh-cn-Yaoyao-아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-CN, Kangkang, Apollo)” | "zh-cn-Kangkang-아폴로" |
| `zh-HK` | 중국어 (광둥어, 번체) | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, Tracy, Apollo)” | "zh-cn-HK-Tracy-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, TracyRUS)” | "zh-cn-HK-TracyRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-HK, Danny, Apollo)” | "zh-cn-HK-Danny-아폴로" |
| `zh-TW` | 중국어(대만어) | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, Yating, Apollo)” | "zh-cn-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, HanHanRUS)” | "zh-cn-HanHanRUS" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-TW, Zhiwei, Apollo)” | "zh-cn-Zhiwei-아폴로" |

**1** *ar-예: 최신 표준 아랍어 (MSA)를 지원 합니다.*

> [!IMPORTANT]
> `en-US-Jessa` 음성이로 `en-US-Aria`변경 되었습니다. 이전에 "Jessa"를 사용 하는 경우 "Aria"로 변환 합니다.

> [!TIP]
> 음성 합성 요청에서 전체 서비스 이름 매핑 또는 짧은 음성 이름을 사용할 수 있습니다.

### <a name="customization"></a>사용자 지정

음성 사용자 지정은, `de-DE` `en-GB` `en-IN` `en-US`,,, `es-MX` `fr-FR` `it-IT` `pt-BR`,,, 및 `zh-CN`에 사용할 수 있습니다. 사용자 지정 음성 모델을 학습 하는 데 사용할 학습 데이터와 일치 하는 올바른 로캘을 선택 합니다. 예를 들어, 기록 데이터를 영국 악센트를 사용 하 여 영어로 말한 경우를 선택 `en-GB`합니다.

> [!NOTE]
> 중국어 (번체) bi-다국어를 제외 하 고 사용자 지정 음성에서는 bi-다국어 모델 교육을 지원 하지 않습니다. 영어를 사용할 수 있는 중국어 음성을 학습 하려면 "중국어-영어"를 선택 합니다. 모든 로캘의 음성 교육은의 경우를 제외 하 고 `en-US` `zh-CN` 는 2000 + 길이 발언 데이터 집합으로 시작 합니다.

## <a name="speech-translation"></a>음성 번역

**Speech Translation** API는 음성 간 음성 및 음성을 텍스트로 번역에 대해 다른 언어를 지원합니다. 소스 언어는 항상 음성 텍스트 언어 테이블에 있어야 합니다. 사용 가능한 대상 언어는 번역 대상이 음성인지 또는 텍스트인지에 따라 달라집니다. 들어오는 음성을 [60개 언어](https://www.microsoft.com/translator/business/languages/) 이상으로 변환할 수 있습니다. 언어의 하위 집합은 [음성 합성](language-support.md#text-languages)에 사용할 수 있습니다.

### <a name="text-languages"></a>텍스트 언어

| 텍스트 언어           | 언어 코드 |
|:------------------------|:-------------:|
| 아프리칸스어               | `af`          |
| 아랍어                  | `ar`          |
| 벵골어                  | `bn`          |
| 보스니아어(라틴 문자)         | `bs`          |
| 불가리아어               | `bg`          |
| 광둥어(번체) | `yue`         |
| 카탈로니아어                 | `ca`          |
| 중국어 간체      | `zh-Hans`     |
| 중국어 번체     | `zh-Hant`     |
| 크로아티아어                | `hr`          |
| 체코어                   | `cs`          |
| 덴마크어                  | `da`          |
| 네덜란드어                   | `nl`          |
| 영어                 | `en`          |
| 에스토니아어                | `et`          |
| 피지어                  | `fj`          |
| 필리핀어                | `fil`         |
| 핀란드어                 | `fi`          |
| 프랑스어                  | `fr`          |
| 독일어                  | `de`          |
| 그리스어                   | `el`          |
| 구자라트어                | `gu`          |
| 아이티 크리올          | `ht`          |
| 히브리어                  | `he`          |
| 힌디어                   | `hi`          |
| 몽 다오어               | `mww`         |
| 헝가리어               | `hu`          |
| 인도네시아어              | `id`          |
| 아일랜드어                   | `ga`          |
| 이탈리아어                 | `it`          |
| 일본어                | `ja`          |
| 칸나다어                 | `kn`          |
| 스와힐리어               | `sw`          |
| 클링곤어                 | `tlh`         |
| 클링곤어(plqaD)         | `tlh-Qaak`    |
| 한국어                  | `ko`          |
| 라트비아어                 | `lv`          |
| 리투아니아어              | `lt`          |
| 마다가스카르어                | `mg`          |
| 말레이어                   | `ms`          |
| 말라얄람어               | `ml`          |
| 몰타어                 | `mt`          |
| 마오리어                   | `mi`          |
| 마라티어                 | `mr`          |
| 노르웨이어               | `nb`          |
| 페르시아어                 | `fa`          |
| 폴란드어                  | `pl`          |
| 포르투갈어(브라질)     | `pt-br`       |
| 포르투갈어(포르투갈)   | `pt-pt`       |
| 펀잡어                 | `pa`          |
| 케레타로 오토미어         | `otq`         |
| 루마니아어                | `ro`          |
| 러시아어                 | `ru`          |
| 사모아어                  | `sm`          |
| 세르비아어(키릴 자모)      | `sr-Cyrl`     |
| 세르비아어(라틴 문자)         | `sr-Latn`     |
| 슬로바키아어                  | `sk`          |
| 슬로베니아어               | `sl`          |
| 스페인어                 | `es`          |
| 스웨덴어                 | `sv`          |
| 타히티어                | `ty`          |
| 타밀어                   | `ta`          |
| 텔루구어                  | `te`          |
| 태국어                    | `th`          |
| 통가어                  | `to`          |
| 터키어                 | `tr`          |
| 우크라이나어               | `uk`          |
| 우르두어                    | `ur`          |
| 베트남어              | `vi`          |
| 웨일스어                   | `cy`          |
| 유카텍 마야어            | `yua`         |

## <a name="next-steps"></a>다음 단계

* [음성 서비스 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
