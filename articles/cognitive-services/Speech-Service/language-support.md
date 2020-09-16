---
title: 언어 지원 - Speech Service
titleSuffix: Azure Cognitive Services
description: Speech Service는 음성 번역과 함께 음성 텍스트 변환 및 텍스트 음성 변환을 위해 다양한 언어를 지원합니다. 이 문서에서는 서비스 기능별 언어 지원에 대한 종합 목록을 제공합니다.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: trbye
ms.custom: seodec18
ms.openlocfilehash: de6cd4ee2c9800757399b7e32b59d903b817c657
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604396"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Speech Service에 대한 언어 및 음성 지원

언어 지원은 Speech Service 기능에 따라 달라 집니다. 다음 테이블은 [음성 텍스트 변환](#speech-to-text), [텍스트 음성 변환](#text-to-speech) 및 [음성 번역](#speech-translation) 서비스 제공에 대한 언어 지원을 요약합니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

Microsoft Speech SDK와 REST API는 모두 다음 언어(로캘)를 지원합니다. 

정확성을 향상시키기 위해 사용자 지정은 **오디오 + 휴먼 레이블 음성 텍스트** 또는 **관련 텍스트: 문장**을 업로드하여 언어의 하위 집합에 제공됩니다. 사용자 지정에 대한 자세한 내용은 [Custom Speech 시작](how-to-custom-speech.md)을 참조하세요.

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 로캘  | 언어                          | 사용자 지정                                    |
|---------|-----------------------------------|---------------------------------------------------|
| `ar-AE` | 아랍어(UAE)                      | 예                                                |
| `ar-BH` | 아랍어(바레인), 최신 표준 | 언어 모델                                    |
| `ar-EG` | 아랍어(이집트)                    | 언어 모델                                    |
| `ar-IL` | 아랍어(이스라엘)                   | No                                                |
| `ar-IQ` | 아랍어(이라크)                     | 예                                                |
| `ar-JO` | 아랍어(요르단)                   | 예                                                |
| `ar-KW` | 아랍어(쿠웨이트)                   | 예                                                |
| `ar-LB` | 아랍어(레바논)                  | No                                                |
| `ar-OM` | 아랍어(오만)                     | 예                                                |
| `ar-PS` | 아랍어(팔레스타인)                | 예                                                |
| `ar-QA` | 아랍어(카타르)                    | 예                                                |
| `ar-SA` | 아랍어(사우디아라비아)             | 예                                                |
| `ar-SY` | 아랍어(시리아)                    | 언어 모델                                    |
| `bg-BG` | 불가리아어(불가리아)              | 언어 모델                                    |
| `ca-ES` | 카탈로니아어(스페인)                   | 언어 모델                                    |
| `cs-CZ` | 체코어(체코)            | 언어 모델                                    | 
| `da-DK` | 덴마크어(덴마크)                  | 언어 모델                                    |
| `de-DE` | 독일어(독일)                  | 음향 모델<br>언어 모델<br>발음 |
| `el-GR` | 그리스어(그리스)                    | 언어 모델                                    |
| `en-AU` | 영어(오스트레일리아)               | 음향 모델<br>언어 모델                  |
| `en-CA` | 영어(캐나다)                  | 음향 모델<br>언어 모델                  |
| `en-GB` | 영어(영국)          | 음향 모델<br>언어 모델<br>발음 |
| `en-HK` | 영어 (홍콩)               | 언어 모델                                    | 
| `en-IE` | 영어(아일랜드)                 | 언어 모델                                    | 
| `en-IN` | 영어(인도)                   | 음향 모델<br>언어 모델                  |
| `en-NZ` | 영어(뉴질랜드)             | 음향 모델<br>언어 모델                  |
| `en-PH` | 영어(필리핀)             | 언어 모델                                    | 
| `en-SG` | 영어(싱가포르)               | 언어 모델                                    | 
| `en-US` | 영어(미국)           | 음향 모델<br>언어 모델<br>발음 |
| `en-ZA` | 영어(남아프리카)            | 언어 모델                                    | 
| `es-AR` | 스페인어(아르헨티나)               | 언어 모델                                    | 
| `es-BO` | 스페인어(볼리비아)                 | 언어 모델                                    | 
| `es-CL` | 스페인어(칠레)                   | 언어 모델                                    | 
| `es-CO` | 스페인어(콜롬비아)                | 언어 모델                                    | 
| `es-CR` | 스페인어(코스타리카)              | 언어 모델                                    | 
| `es-CU` | 스페인어 (쿠바)                    | 언어 모델                                    | 
| `es-DO` | 스페인어(도미니카 공화국)      | 언어 모델                                    | 
| `es-EC` | 스페인어(에콰도르)                 | 언어 모델                                    | 
| `es-ES` | 스페인어(스페인)                   | 음향 모델<br>언어 모델                  |
| `es-GT` | 스페인어(과테말라)               | 언어 모델                                    | 
| `es-HN` | 스페인어(온두라스)                | 언어 모델                                    | 
| `es-MX` | 스페인어(멕시코)                  | 음향 모델<br>언어 모델                  |
| `es-NI` | 스페인어(니카라과)               | 언어 모델                                    | 
| `es-PA` | 스페인어(파나마)                  | 언어 모델                                    | 
| `es-PE` | 스페인어(페루)                    | 언어 모델                                    | 
| `es-PR` | 스페인어(푸에르토리코)             | 언어 모델                                    | 
| `es-PY` | 스페인어(파라과이)                | 언어 모델                                    | 
| `es-SV` | 스페인어(엘살바도르)             | 언어 모델                                    | 
| `es-US` | 스페인어 (미국)                     | 언어 모델                                    | 
| `es-UY` | 스페인어(우루과이)                 | 언어 모델                                    | 
| `es-VE` | 스페인어(베네수엘라)               | 언어 모델                                    |
| `et-EE` | 에스토니아어 (에스토니아)                 | 언어 모델                                    | 
| `fi-FI` | 핀란드어(핀란드)                 | 언어 모델                                    |
| `fr-CA` | 프랑스어(캐나다)                   | 음향 모델<br>언어 모델                  |
| `fr-FR` | 프랑스어(프랑스)                   | 음향 모델<br>언어 모델<br>발음 |
| `ga-IE` | 아일랜드어 (아일랜드)                    | 언어 모델                                    |
| `gu-IN` | 구자라트어(인도)                 | 언어 모델                                    |
| `hi-IN` | 힌디어(인도)                     | 음향 모델<br>언어 모델                  |
| `hr-HR` | 크로아티아어(크로아티아)                | 언어 모델                                    |
| `hu-HU` | 헝가리어(헝가리)               | 언어 모델                                    | 
| `it-IT` | 이탈리아어(이탈리아)                   | 음향 모델<br>언어 모델<br>발음 |
| `ja-JP` | 일본어(일본)                  | 언어 모델                                    |
| `ko-KR` | 한국어(한국)                    | 언어 모델                                    |
| `lt-LT` | 리투아니아어(리투아니아)            | 언어 모델                                    |
| `lv-LV` | 라트비아어(라트비아)                  | 언어 모델                                    |
| `mr-IN` | 마라티어(인도)                   | 언어 모델                                    |
| `mt-MT` | 몰타어 (몰타)                    | 언어 모델                                    |
| `nb-NO` | 노르웨이어(복말)(노르웨이)       | 언어 모델                                    |
| `nl-NL` | 네덜란드어(네덜란드)               | 언어 모델                                    |
| `pl-PL` | 폴란드어(폴란드)                   | 언어 모델                                    |
| `pt-BR` | 포르투갈어(브라질)               | 음향 모델<br>언어 모델<br>발음 |
| `pt-PT` | 포르투갈어(포르투갈)             | 언어 모델                                    |
| `ro-RO` | 루마니아어(루마니아)                | 언어 모델                                    |
| `ru-RU` | 러시아어(러시아)                  | 음향 모델<br>언어 모델                  |
| `sk-SK` | 슬로바키아어(슬로바키아)                 | 언어 모델                                    |
| `sl-SI` | 슬로베니아어(슬로베니아)              | 언어 모델                                    |
| `sv-SE` | 스웨덴어(스웨덴)                  | 언어 모델                                    |
| `ta-IN` | 타밀어(인도)                     | 언어 모델                                    |
| `te-IN` | 텔루구어(인도)                    | 언어 모델                                    |
| `th-TH` | 태국어(태국)                   | 예                                                |
| `tr-TR` | 터키어(터키)                  | 언어 모델                                    |
| `zh-CN` | 중국어 (북경어, 간체)    | 음향 모델<br>언어 모델                  |
| `zh-HK` | 중국어(광둥어, 번체)  | 언어 모델                                    |
| `zh-TW` | 중국어(대만어)      | 언어 모델                                    |

## <a name="text-to-speech"></a>텍스트 음성 변환

Microsoft Speech SDK 및 REST API는 이러한 음성을 제공하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다. [음성/목록 API](rest-text-to-speech.md#get-a-list-of-voices)를 통해 각 특정 영역/엔드포인트에 지원되는 언어 및 음성의 전체 목록을 얻을 수도 있습니다. 

> [!IMPORTANT]
> 가격 책정은 표준, 사용자 지정 및 인공신경망 음성마다 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 페이지를 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트-음성 변환은 심층 신경망에서 지원되는 새로운 형식의 음성 합성입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다.

인공신경망 음성을 사용하여 챗봇 및 음성 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, eBook 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

|로캘  | 언어            | 성별 | 음성 이름 | 스타일 지원 |
|--|--|--|--|--|
| `ar-EG` | 아랍어(이집트)                  | Female | `ar-EG-SalmaNeural`      | 일반 |
| `ar-SA` | 아랍어(사우디아라비아)           | Female | `ar-SA-ZariyahNeural`    | 일반 |
| `ca-ES` | 카탈로니아어(스페인)                 | Female | `ca-ES-AlbaNeural`       | 일반 |
| `da-DK` | 덴마크어(덴마크)                | Female | `da-DK-ChristelNeural`   | 일반 |
| `de-DE` | 독일어(독일)                | Female | `de-DE-KatjaNeural`      | 일반 |
| `en-AU` | 영어(오스트레일리아)             | Female | `en-AU-NatashaNeural`    | 일반 |
| `en-CA` | 영어(캐나다)                | Female | `en-CA-ClaraNeural`      | 일반 |
| `en-GB` | 영어(영국)                    | Female | `en-GB-LibbyNeural`      | 일반 |
|         |                                 | Female | `en-GB-MiaNeural`        | 일반 |
| `en-IN` | 영어(인도)                 | Female | `en-IN-NeerjaNeural`     | 일반 |
| `en-US` | 영어(미국)                    | Female | `en-US-AriaNeural`       | 일반, 여러 음성 스타일 사용 가능 |
|         |                                 | Male   | `en-US-GuyNeural`        | 일반 |
| `es-ES` | 스페인어(스페인)                 | Female | `es-ES-ElviraNeural`     | 일반 |
| `es-MX` | 스페인어(멕시코)                | Female | `es-MX-DaliaNeural`      | 일반 |
| `fi-FI` | 핀란드어(핀란드)               | Female | `fi-FI-NooraNeural`      | 일반 |
| `fr-CA` | 프랑스어(캐나다)                 | Female | `fr-CA-SylvieNeural`     | 일반 |
| `fr-FR` | 프랑스어(프랑스)                 | Female | `fr-FR-DeniseNeural`     | 일반 |
| `hi-IN` | 힌디어(인도)                   | Female | `hi-IN-SwaraNeural`      | 일반 |
| `it-IT` | 이탈리아어(이탈리아)                 | Female | `it-IT-ElsaNeural`       | 일반 |
| `ja-JP` | 일본어                        | Female | `ja-JP-NanamiNeural`     | 일반 |
| `ko-KR` | 한국어                          | Female | `ko-KR-SunHiNeural`      | 일반 |
| `nb-NO` | 노르웨이어                       | Female | `nb-NO-IselinNeural`     | 일반 |
| `nl-NL` | 네덜란드어 (Netherland)              | Female | `nl-NL-ColetteNeural`    | 일반 |
| `pl-PL` | 폴란드어(폴란드)                 | Female | `pl-PL-ZofiaNeural`      | 일반 |
| `pt-BR` | 포르투갈어(브라질)             | Female | `pt-BR-FranciscaNeural`  | 일반, 여러 음성 스타일 사용 가능 |
| `tr-TR` | 터키어                         | Female | `tr-TR-EmelNeural`       | 일반 |
| `pt-PT` | 포르투갈어(포르투갈)           | Female | `pt-PT-FernandaNeural`   | 일반 |
| `ru-RU` | 러시아어(러시아)                | Female | `ru-RU-DariyaNeural`     | 일반 |
| `sv-SE` | 스웨덴어(스웨덴)                | Female | `sv-SE-HilleviNeural`    | 일반 |
| `th-TH` | 태국어(태국)                 | Female | `th-TH-AcharaNeural`     | 일반 |
| `zh-CN` | 중국어 (북경어, 간체)  | Female | `zh-CN-XiaoxiaoNeural`   | 일반, 여러 음성 스타일 사용 가능 |
|         |                                 | Female | `zh-CN-XiaoyouNeural`    | 어린이 음성, 이야기 내레이션에 최적화 |
|         |                                 | Male   | `zh-CN-YunyangNeural`    | 뉴스 읽기에 최적화, 다양한 음성 스타일 사용 가능 |
|         |                                 | Male   | `zh-CN-YunyeNeural`      | 이야기 내레이션에 최적화 |
| `zh-HK` | 중국어(광둥어, 번체)   | Female | `zh-HK-HiuGaaiNeural`| 일반 |
| `zh-TW` | 중국어(대만어)   | Female | `zh-TW-HsiaoYuNeural`    | 일반 |

> [!IMPORTANT]
> `en-US-JessaNeural` 음성이 `en-US-AriaNeural`로 변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환하세요.

인공신경망 음성을 구성하고 조정하는 방법에 대해 알아보려면 [음성 합성 마크업 언어](speech-synthesis-markup.md#adjust-speaking-styles)를 참조하세요.

> [!TIP]
> 음성 합성 요청에서 "Microsoft Server Speech 텍스트 음성 변환 음성(en-US, AriaNeural)"과 같은 전체 서비스 이름 매핑을 계속 사용할 수 있습니다.

### <a name="standard-voices"></a>표준 음성

75개를 초과하는 표준 음성은 45개 이상의 언어 및 로캘에서 사용할 수 있으며 텍스트를 합성된 음성으로 변환할 수 있습니다. 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| 로캘 | 언어 | 성별 | 음성 이름 |
|--|--|--|--|
| <sup>1</sup>`ar-EG` | 아랍어(이집트) | Female | "ar-EG-Hoda" |
| `ar-SA` | 아랍어(사우디아라비아) | Male | "ar-SA-Naayf" |
| `bg-BG` | 불가리아어 | Male |  "bg-BG-Ivan" |
| `ca-ES` | 카탈로니아어 | Female |  "ca-ES-HerenaRUS" |
| `cs-CZ` | 체코어 | Male | "cs-CZ-Jakub" |
| `da-DK` | 덴마크어 | Female |  "da-DK-HelleRUS" |
| `de-AT` | 독일어(오스트리아) | Male | "de-AT-Michael" |
| `de-CH` | 독일어(스위스) | Male |  "de-CH-Karsten" |
| `de-DE` | 독일어(독일) | Female |  "de-DE-Hedda" |
|  |  | Female | "de-DE-HeddaRUS" |
|  |  | Male |  "de-DE-Stefan-Apollo" |
| `el-GR` | 그리스어 | Male | "el-GR-Stefanos" |
| `en-AU` | 영어(오스트레일리아) | Female |  "en-AU-Catherine" |
|  |  | Female |  "en-AU-HayleyRUS" |
| `en-CA` | 영어(캐나다) | Female |  "en-CA-Linda" |
|  |  | Female |  "en-CA-HeatherRUS" |
| `en-GB` | 영어(영국) | Female |  "en-GB-Susan-Apollo" |
|  |  | Female |  "en-GB-HazelRUS" |
|  |  | Male |  "en-GB-George-Apollo" |
| `en-IE` | 영어(아일랜드) | Male | "en-IE-Sean" |
| `en-IN` | 영어(인도) | Female | "en-IN-Heera-Apollo" |
|  |  | Female |  "en-IN-PriyaRUS" |
|  |  | Male |  "en-IN-Ravi-Apollo" |
| `en-US` | 영어(미국) | Female |  "en-US-ZiraRUS" |
|  |  | Female | "en-US-AriaRUS" |
|  |  | Male | "en-US-BenjaminRUS" |
|  |  | Male |  "en-US-Guy24kRUS" |
| `es-ES` | 스페인어(스페인) | Female |  "es-ES-Laura-Apollo" |
|  |  | Female | "es-ES-HelenaRUS" |
|  |  | Male | "es-ES-Pablo-Apollo" |
| `es-MX` | 스페인어(멕시코) | Female |  "es-MX-HildaRUS" |
|  |  | Male | "es-MX-Raul-Apollo" |
| `fi-FI` | 핀란드어 | Female | "fi-FI-HeidiRUS" |
| `fr-CA` | 프랑스어(캐나다) | Female | "fr-CA-Caroline" |
|  |  | Female | "fr-CA-HarmonieRUS" |
| `fr-CH` | 프랑스어(스위스) | Male | "fr-CH-Guillaume" |
| `fr-FR` | 프랑스어(프랑스) | Female |  "fr-FR-Julie-Apollo" |
|  |  | Female |"fr-FR-HortenseRUS" |
|  |  | Male |  "fr-FR-Paul-Apollo" |
| `he-IL` | 히브리어(이스라엘) | Male |  "he-IL-Asaf" |
| `hi-IN` | 힌디어(인도) | Female | "hi-IN-Kalpana-Apollo" |
|  |  | Female |  "hi-IN-Kalpana" |
|  |  | Male |  "hi-IN-Hemant" |
| `hr-HR` | 크로아티아어 | Male | "hr-HR-Matej" |
| `hu-HU` | 헝가리어 | Male |  "hu-HU-Szabolcs" |
| `id-ID` | 인도네시아어 | Male | "id-ID-Andika" |
| `it-IT` | 이탈리아어 | Male |  "it-IT-Cosimo-Apollo" |
|  |  | Female |  "it-IT-LuciaRUS" |
| `ja-JP` | 일본어 | Female |  "ja-JP-Ayumi-Apollo" |
|  |  | Male | "ja-JP-Ichiro-Apollo" |
|  |  | Female |  "ja-JP-HarukaRUS" |
| `ko-KR` | 한국어 | Female | "ko-KR-HeamiRUS" |
| `ms-MY` | 말레이어 | Male |  "ms-MY-Rizwan" |
| `nb-NO` | 노르웨이어 | Female |  "nb-NO-HuldaRUS" |
| `nl-NL` | 네덜란드어 | Female |  "nl-NL-HannaRUS" |
| `pl-PL` | 폴란드어 | Female |  "pl-PL-PaulinaRUS" |
| `pt-BR` | 포르투갈어(브라질) | Female | "pt-BR-HeloisaRUS" |
|  |  | Male |  "pt-BR-Daniel-Apollo" |
| `pt-PT` | 포르투갈어(포르투갈) | Female | "pt-PT-HeliaRUS" |
| `ro-RO` | 루마니아어 | Male | "ro-RO-Andrei" |
| `ru-RU` | 러시아어 | Female |  "ru-RU-Irina-Apollo" |
|  |  | Male | "ru-RU-Pavel-Apollo" |
|  |  | Female |  ru-RU-EkaterinaRUS |
| `sk-SK` | 슬로바키아어 | Male | "sk-SK-Filip" |
| `sl-SI` | 슬로베니아어 | Male |  "sl-SI-Lado" |
| `sv-SE` | 스웨덴어 | Female | "sv-SE-HedvigRUS" |
| `ta-IN` | 타밀어(인도) | Male |  "ta-IN-Valluvar" |
| `te-IN` | 텔루구어(인도) | Female |  "te-IN-Chitra" |
| `th-TH` | 태국어 | Male |  "th-TH-Pattara" |
| `tr-TR` | 터키어(터키) | Female | "tr-TR-SedaRUS" |
| `vi-VN` | 베트남어 | Male |  "vi-VN-An" |
| `zh-CN` | 중국어 (북경어, 간체) | Female |  "zh-CN-HuihuiRUS" |
|  |  | Female | "zh-CN-Yaoyao-Apollo" |
|  |  | Male | "zh-CN-Kangkang-Apollo" |
| `zh-HK` | 중국어(광둥어, 번체) | Female |  "zh-HK-Tracy-Apollo" |
|  |  | Female | "zh-HK-TracyRUS" |
|  |  | Male |  "zh-HK-Danny-Apollo" |
| `zh-TW` | 중국어(대만어) | Female |  "zh-TW-Yating-Apollo" |
|  |  | Female | "zh-TW-HanHanRUS" |
|  |  | Male |  "zh-TW-Zhiwei-Apollo" |

**1** *ar-EG는 MSA(Modern Standard Arabic)를 지원합니다.*

> [!IMPORTANT]
> `en-US-Jessa` 음성이 `en-US-Aria`로 변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환하세요.

> [!TIP]
> 음성 합성 요청에서 "Microsoft Server Speech 텍스트 음성 변환 음성(en-US, AriaRUS)"과 같은 전체 서비스 이름 매핑을 계속 사용할 수 있습니다.

### <a name="customization"></a>사용자 지정

음성 사용자 지정은 `de-DE`, `en-GB`, `en-IN`, `en-US`, `es-MX`, `fr-FR`, `it-IT`, `pt-BR`, `zh-CN`에 사용할 수 있습니다. 사용자 지정 음성 모델을 학습하는 데 사용할 학습 데이터와 일치하는 올바른 로캘을 선택합니다. 예를 들어, 보유하고 있는 기록 데이터가 영국식 영어로 말하는 경우 `en-GB`를 선택합니다.

> [!NOTE]
> Custom Voice에서는 이중 언어 모델 학습(중국어-영어 이중 언어 제외)을 지원하지 않습니다. 영어도 말할 수 있는 중국어 음성을 학습하려면 "중국어-영어 이중 언어"를 선택합니다. 모든 로캘의 음성 학습은 모든 크기의 교육 데이터로 시작할 수 있는 `en-US` 및 `zh-CN`을 제외한 2,000개 이상의 발화 데이터 세트로 시작합니다.

## <a name="speech-translation"></a>음성 번역

**Speech Translation** API는 음성 간 음성 및 음성을 텍스트로 번역에 대해 다른 언어를 지원합니다. 원본 언어는 항상 음성 텍스트 변환 언어 테이블에 나와 있는 것이어야 합니다. 사용 가능한 대상 언어는 번역 대상이 음성인지 또는 텍스트인지에 따라 달라집니다. 들어오는 음성을 [60개 언어](https://www.microsoft.com/translator/business/languages/) 이상으로 변환할 수 있습니다. [음성 합성](language-support.md#text-languages)에 언어의 하위 집합을 사용할 수 있습니다.

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
| 클링곤어                 | `tlh-Latn`    |
| 클링곤어(plqaD)         | `tlh-Piqd`    |
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

## <a name="speaker-recognition"></a>Speaker Recognition

다양한 Speaker Recognition API에 대해 지원되는 언어는 다음 테이블을 참조하세요. Speaker Recognition에 대한 자세한 내용은 [개요](speaker-recognition-overview.md)를 참조하세요.

| 로캘 | 언어 | 텍스트 종속 확인 | 텍스트 독립 확인 | 텍스트 독립 식별 |
|----|----|----|----|----|
| ko-KR | 영어(미국) | 예 | 예 | 예 |
|zh-CN    |중국어(북경어, 간체)|    해당 없음|    예|    예|
|de-DE    |독일어(독일)    |해당 없음    |예    |예|
|en-GB    |영어(영국)    |해당 없음    |예    |예|
|fr-FR    |프랑스어(프랑스)    |해당 없음    |예    |예|
|en-AU    |영어(오스트레일리아)    |해당 없음    |예    |예|
|en-CA    |영어(캐나다)    |해당 없음|    예|    예|
|fr-CA    |프랑스어(캐나다)    |해당 없음    |예|    예|
|it-IT    |이탈리아어|    해당 없음    |예|    예|
|es-ES|    스페인어(스페인)    |해당 없음    |예|    예|
|es-MX    |스페인어(멕시코)    |해당 없음|    예|    예|
|ja-JP|    일본어    |해당 없음    |예    |예|
|pt-BR|    포르투갈어(브라질)|    해당 없음|    예|    예|

## <a name="next-steps"></a>다음 단계

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
