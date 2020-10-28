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
ms.openlocfilehash: a864717304dacaf6cf4c77c92050827af619ed39
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736686"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>Speech Service에 대한 언어 및 음성 지원

언어 지원은 Speech Service 기능에 따라 달라 집니다. 다음 테이블은 [음성 텍스트 변환](#speech-to-text), [텍스트 음성 변환](#text-to-speech) 및 [음성 번역](#speech-translation) 서비스 제공에 대한 언어 지원을 요약합니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

Microsoft Speech SDK와 REST API는 모두 다음 언어(로캘)를 지원합니다. 

정확성을 향상시키기 위해 사용자 지정은 **오디오 + 휴먼 레이블 음성 텍스트** 또는 **관련 텍스트: 문장** 을 업로드하여 언어의 하위 집합에 제공됩니다. 사용자 지정에 대한 자세한 내용은 [Custom Speech 시작](how-to-custom-speech.md)을 참조하세요.

<!--
To get the AM and ML bits:
https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| 언어                          | 로캘 | 사용자 지정                                   |
|-----------------------------------|--------|--------------------------------------------------|
|아랍어(바레인), 최신 표준  |`ar-BH` | 언어 모델                                   |
|아랍어(이집트)                     |`ar-EG` | 언어 모델                                   |
|아랍어(이라크)                      |`ar-IQ` | 언어 모델                                   |
|아랍어(요르단)                    |`ar-JO` | 언어 모델                                   |
|아랍어(쿠웨이트)                    |`ar-KW` | 언어 모델                                   |
|아랍어(레바논)                   |`ar-LB` | 언어 모델                                   |
|아랍어(오만)                      |`ar-OM` | 언어 모델                                   |
|아랍어(카타르)                     |`ar-QA` | 언어 모델                                   |
|아랍어(사우디아라비아)              |`ar-SA` | 언어 모델                                   |
|아랍어(시리아)                     |`ar-SY` | 언어 모델                                   |
|아랍어 (아랍에미리트)      |`ar-AE` | 언어 모델                                   |
|불가리아어(불가리아)               |`bg-BG` | 언어 모델                                   |
|카탈로니아어(스페인)                    |`ca-ES` | 언어 모델                                   |
|중국어(광둥어, 번체)   |`zh-HK` | 언어 모델                                   |
|중국어 (북경어, 간체)     |`zh-CN` | 음향 모델<br>언어 모델                 |
|중국어(대만어)       |`zh-TW` | 언어 모델                                   |
|크로아티아어(크로아티아)                 |`hr-HR` | 언어 모델                                   |
|체코어(체코)             |`cs-CZ` | 언어 모델                                   |
|덴마크어(덴마크)                   |`da-DK` | 언어 모델                                   |
|네덜란드어(네덜란드)                |`nl-NL` | 언어 모델                                   |
|영어(오스트레일리아)                |`en-AU` | 음향 모델<br>언어 모델                 |
|영어(캐나다)                   |`en-CA` | 음향 모델<br>언어 모델                 |
|영어 (홍콩)                |`en-HK` | 언어 모델                                   |
|영어(인도)                    |`en-IN` | 음향 모델<br>언어 모델                 |
|영어(아일랜드)                  |`en-IE` | 언어 모델                                   |
|영어(뉴질랜드)              |`en-NZ` | 음향 모델<br>언어 모델                 |
|영어(필리핀)              |`en-PH` | 언어 모델                                   |
|영어(싱가포르)                |`en-SG` | 언어 모델                                   |
|영어(남아프리카)             |`en-ZA` | 언어 모델                                   |
|영어(영국)           |`en-GB` | 음향 모델<br>언어 모델<br>발음|
|영어(미국)            |`en-US` | 음향 모델<br>언어 모델<br>발음|
|에스토니아어 (에스토니아)                  |`et-EE` | 언어 모델                                   |
|핀란드어(핀란드)                  |`fi-FI` | 언어 모델                                   |
|프랑스어(캐나다)                    |`fr-CA` | 음향 모델<br>언어 모델                 |
|프랑스어(프랑스)                    |`fr-FR` | 음향 모델<br>언어 모델<br>발음|
|독일어(독일)                   |`de-DE` | 음향 모델<br>언어 모델<br>발음|
|그리스어(그리스)                     |`el-GR` | 언어 모델                                   |
|구자라트어(인도)                  |`gu-IN` | 언어 모델                                   |
|힌디어(인도)                      |`hi-IN` | 음향 모델<br>언어 모델                 |
|헝가리어(헝가리)                |`hu-HU` | 언어 모델                                   |
|아일랜드어 (아일랜드)                     |`ga-IE` | 언어 모델                                   |
|이탈리아어(이탈리아)                    |`it-IT` | 음향 모델<br>언어 모델<br>발음|
|일본어(일본)                   |`ja-JP` | 언어 모델                                   |
|한국어(한국)                     |`ko-KR` | 언어 모델                                   |
|라트비아어(라트비아)                   |`lv-LV` | 언어 모델                                   |
|리투아니아어(리투아니아)             |`lt-LT` | 언어 모델                                   |
|몰타어 (몰타)                     |`mt-MT` | 언어 모델                                   |
|마라티어(인도)                    |`mr-IN` | 언어 모델                                   |
|노르웨이어 (BokmÃ ¥ l) (노르웨이)       |`nb-NO` | 언어 모델                                   |
|폴란드어(폴란드)                    |`pl-PL` | 언어 모델                                   |
|포르투갈어(브라질)                |`pt-BR` | 음향 모델<br>언어 모델<br>발음|
|포르투갈어(포르투갈)              |`pt-PT` | 언어 모델                                   |
|루마니아어(루마니아)                 |`ro-RO` | 언어 모델                                   |
|러시아어(러시아)                   |`ru-RU` | 음향 모델<br>언어 모델                 |
|슬로바키아어(슬로바키아)                  |`sk-SK` | 언어 모델                                   |
|슬로베니아어(슬로베니아)               |`sl-SI` | 언어 모델                                   |
|스페인어(아르헨티나)                |`es-AR` | 언어 모델                                   |
|스페인어(볼리비아)                  |`es-BO` | 언어 모델                                   |
|스페인어(칠레)                    |`es-CL` | 언어 모델                                   |
|스페인어(콜롬비아)                 |`es-CO` | 언어 모델                                   |
|스페인어(코스타리카)               |`es-CR` | 언어 모델                                   |
|스페인어 (쿠바)                     |`es-CU` | 언어 모델                                   |
|스페인어(도미니카 공화국)       |`es-DO` | 언어 모델                                   |
|스페인어(에콰도르)                  |`es-EC` | 언어 모델                                   |
|스페인어(엘살바도르)              |`es-SV` | 언어 모델                                   |
|스페인어(과테말라)                |`es-GT` | 언어 모델                                   |
|스페인어(온두라스)                 |`es-HN` | 언어 모델                                   |
|스페인어(멕시코)                   |`es-MX` | 음향 모델<br>언어 모델                 |
|스페인어(니카라과)                |`es-NI` | 언어 모델                                   |
|스페인어(파나마)                   |`es-PA` | 언어 모델                                   |
|스페인어(파라과이)                 |`es-PY` | 언어 모델                                   |
|스페인어(페루)                     |`es-PE` | 언어 모델                                   |
|스페인어(푸에르토리코)              |`es-PR` | 언어 모델                                   |
|스페인어(스페인)                    |`es-ES` | 음향 모델<br>언어 모델                 |
|스페인어(우루과이)                  |`es-UY` | 언어 모델                                   |
|스페인어 (미국)                      |`es-US` | 언어 모델                                   |
|스페인어(베네수엘라)                |`es-VE` | 언어 모델                                   |
|스웨덴어(스웨덴)                   |`sv-SE` | 언어 모델                                   |
|타밀어(인도)                      |`ta-IN` | 언어 모델                                   |
|텔루구어(인도)                     |`te-IN` | 언어 모델                                   |
|태국어(태국)                    |`th-TH` | 언어 모델                                   |
|터키어(터키)                   |`tr-TR` | 언어 모델                                   |

## <a name="text-to-speech"></a>텍스트 음성 변환

Microsoft Speech SDK 및 REST API는 이러한 음성을 제공하며, 해당 음성 각각은 로캘로 식별되는 특정 언어를 지원합니다. [음성/목록 API](rest-text-to-speech.md#get-a-list-of-voices)를 통해 각 특정 영역/엔드포인트에 지원되는 언어 및 음성의 전체 목록을 얻을 수도 있습니다. 

> [!IMPORTANT]
> 가격 책정은 표준, 사용자 지정 및 인공신경망 음성마다 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 페이지를 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트-음성 변환은 심층 신경망에서 지원되는 새로운 형식의 음성 합성입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다.

인공신경망 음성을 사용하여 챗봇 및 음성 도우미와의 상호 작용에 더 자연스럽게 참여하도록 만들고, eBook 같은 디지털 텍스트를 오디오북으로 변환하고, 자동차 내부 내비게이션 시스템을 향상시킬 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

|언어  | 로캘           | 성별 | 음성 이름 | 스타일 지원 |
|--|--|--|--|--|
| 아랍어(이집트) | `ar-EG` | Female | `ar-EG-SalmaNeural` | 일반 |
| 아랍어(사우디아라비아) | `ar-SA` | Female | `ar-SA-ZariyahNeural` | 일반 |
| 불가리아어 (Bulgary) | `bg-BG` <sup>새로 만들기</sup> | Female | `bg-BG-KalinaNeural` | 일반 |
| 광둥어 (중국어 번체, 홍콩) | `zh-HK` | Female | `zh-HK-HiuGaaiNeural` | 일반 |
| 카탈로니아어(스페인) | `ca-ES` | Female | `ca-ES-AlbaNeural` | 일반 |
| 크로아티아어(크로아티아) | `hr-HR` <sup>새로 만들기</sup> | Female | `hr-HR-GabrijelaNeural` | 일반 |
| 체코어 (체코) | `cs-CZ` <sup>새로 만들기</sup> | Female | `cs-CZ-VlastaNeural` | 일반 |
| 덴마크어(덴마크) | `da-DK` | Female | `da-DK-ChristelNeural` | 일반 |
| 네덜란드어(네덜란드) | `nl-NL` | Female | `nl-NL-ColetteNeural` | 일반 |
| 영어(오스트레일리아) | `en-AU` | Female | `en-AU-NatashaNeural` | 일반 |
| 영어(오스트레일리아) | `en-AU` <sup>새로 만들기</sup> | Male | `en-AU-WilliamNeural` | 일반 |
| 영어(캐나다) | `en-CA` | Female | `en-CA-ClaraNeural` | 일반 |
| 영어(인도) | `en-IN` | Female | `en-IN-NeerjaNeural` | 일반 |
| 영어(아일랜드) | `en-IE` <sup>새로 만들기</sup> | Female | `en-IE-EmilyNeural` | 일반 |
| 영어(영국) | `en-GB` | Female | `en-GB-LibbyNeural` | 일반 |
| 영어(영국) | `en-GB` | Female | `en-GB-MiaNeural` | 일반 |
| 영어(영국) | `en-GB` <sup>새로 만들기</sup> | Male | `en-GB-RyanNeural` | 일반 |
| 영어(미국) | `en-US` | Female | `en-US-AriaNeural` | 일반, 여러 음성 스타일 사용 가능 |
| 영어(미국) | `en-US` | Male | `en-US-GuyNeural` | 일반 |
| 영어(미국) | `en-US` <sup>새로 만들기</sup> | Female | `en-US-JennyNeural` | 일반, 여러 음성 스타일 사용 가능 |
| 핀란드어(핀란드) | `fi-FI` | Female | `fi-FI-NooraNeural` | 일반 |
| 프랑스어(캐나다) | `fr-CA` | Female | `fr-CA-SylvieNeural` | 일반 |
| 프랑스어(캐나다) | `fr-CA` <sup>새로 만들기</sup> | Male | `fr-CA-JeanNeural` | 일반 |
| 프랑스어(프랑스) | `fr-FR` | Female | `fr-FR-DeniseNeural` | 일반 |
| 프랑스어(프랑스) | `fr-FR` <sup>새로 만들기</sup> | Male | `fr-FR-HenriNeural` | 일반 |
| 프랑스어(스위스) | `fr-CH` <sup>새로 만들기</sup> | Female | `fr-CH-ArianeNeural` | 일반 |
| 독일어(오스트리아) | `de-AT` <sup>새로 만들기</sup> | Female | `de-AT-IngridNeural` | 일반 |
| 독일어(독일) | `de-DE` | Female | `de-DE-KatjaNeural` | 일반 |
| 독일어(독일) | `de-DE` <sup>새로 만들기</sup> | Male | `de-DE-ConradNeural` | 일반 |
| 독일어(스위스) | `de-CH` <sup>새로 만들기</sup> | Female | `de-CH-LeniNeural` | 일반 |
| 그리스어(그리스) | `el-GR` <sup>새로 만들기</sup> | Female | `el-GR-AthinaNeural` | 일반 |
| 히브리어(이스라엘) | `he-IL` <sup>새로 만들기</sup> | Female | `he-IL-HilaNeural` | 일반 |
| 힌디어(인도) | `hi-IN` | Female | `hi-IN-SwaraNeural` | 일반 |
| 헝가리어(헝가리) | `hu-HU` <sup>새로 만들기</sup> | Female | `hu-HU-NoemiNeural` | 일반 |
| 인도네시아어(인도네시아) | `id-ID` <sup>새로 만들기</sup> | Male | `id-ID-ArdiNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` | Female | `it-IT-ElsaNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` <sup>새로 만들기</sup> | Female | `it-IT-IsabellaNeural` | 일반 |
| 이탈리아어(이탈리아) | `it-IT` <sup>새로 만들기</sup> | Male | `it-IT-DiegoNeural` | 일반 |
| 일본어(일본) | `ja-JP` | Female | `ja-JP-NanamiNeural` | 일반 |
| 일본어(일본) | `ja-JP` <sup>새로 만들기</sup> | Male | `ja-JP-KeitaNeural` | 일반 |
| 한국어(한국) | `ko-KR` | Female | `ko-KR-SunHiNeural` | 일반 |
| 한국어(한국) | `ko-KR` <sup>새로 만들기</sup> | Male | `ko-KR-InJoonNeural` | 일반 |
| 말레이어(말레이시아) | `ms-MY` <sup>새로 만들기</sup> | Female | `ms-MY-YasminNeural` | 일반 |
| 북경어 (중국어 간체, 중국) | `zh-CN` | Female | `zh-CN-XiaoxiaoNeural` | 일반, 여러 음성 스타일 사용 가능 |
| 북경어 (중국어 간체, 중국) | `zh-CN` | Female | `zh-CN-XiaoyouNeural` | 어린이 음성, 이야기 내레이션에 최적화 |
| 북경어 (중국어 간체, 중국) | `zh-CN` | Male | `zh-CN-YunyangNeural` | 뉴스 읽기에 최적화 됨, 여러 음성 스타일 사용 가능 |
| 북경어 (중국어 간체, 중국) | `zh-CN` | Male | `zh-CN-YunyeNeural` | 이야기 내레이션에 최적화 |
| 북경어 (중국어 번체, 대만) | `zh-TW` | Female | `zh-TW-HsiaoYuNeural` | 일반 |
| 노르웨이어, BokmÃ ¥ l (노르웨이) | `nb-NO` | Female | `nb-NO-IselinNeural` | 일반 |
| 폴란드어(폴란드) | `pl-PL` | Female | `pl-PL-ZofiaNeural` | 일반 |
| 포르투갈어(브라질) | `pt-BR` | Female | `pt-BR-FranciscaNeural` | 일반, 여러 음성 스타일 사용 가능 |
| 포르투갈어(브라질) | `pt-BR` <sup>새로 만들기</sup> | Male | `pt-BR-AntonioNeural` | 일반 |
| 포르투갈어(포르투갈) | `pt-PT` | Female | `pt-PT-FernandaNeural` | 일반 |
| 루마니아어(루마니아) | `ro-RO` <sup>새로 만들기</sup> | Female | `ro-RO-AlinaNeural` | 일반 |
| 러시아어(러시아) | `ru-RU` | Female | `ru-RU-DariyaNeural` | 일반 |
| 슬로바키아어(슬로바키아) | `sk-SK` <sup>새로 만들기</sup> | Female | `sk-SK-ViktoriaNeural` | 일반 |
| 슬로베니아어(슬로베니아) | `sl-SI` <sup>새로 만들기</sup> | Female | `sl-SI-PetraNeural` | 일반 |
| 스페인어(멕시코) | `es-MX` | Female | `es-MX-DaliaNeural` | 일반 |
| 스페인어(멕시코) | `es-MX` <sup>새로 만들기</sup> | Male | `es-MX-JorgeNeural` | 일반 |
| 스페인어(스페인) | `es-ES` | Female | `es-ES-ElviraNeural` | 일반 |
| 스페인어(스페인) | `es-ES` <sup>새로 만들기</sup> | Male | `es-ES-AlvaroNeural` | 일반 |
| 스웨덴어(스웨덴) | `sv-SE` | Female | `sv-SE-HilleviNeural` | 일반 |
| 타밀어(인도) | `ta-IN` <sup>새로 만들기</sup> | Female | `ta-IN-PallaviNeural` | 일반 |
| 텔루구어(인도) | `te-IN` <sup>새로 만들기</sup> | Female | `te-IN-ShrutiNeural` | 일반 |
| 태국어(태국) | `th-TH` | Female | `th-TH-AcharaNeural` | 일반 |
| 태국어(태국) | `th-TH` <sup>새로 만들기</sup> | Female | `th-TH-PremwadeeNeural` | 일반 |
| 터키어(터키) | `tr-TR` | Female | `tr-TR-EmelNeural` | 일반 |
| 베트남어(베트남) | `vi-VN` <sup>새로 만들기</sup> | Female | `vi-VN-HoaiMyNeural` | 일반|

> [!IMPORTANT]
> `en-US-JessaNeural` 음성이 `en-US-AriaNeural`로 변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환하세요.

인공신경망 음성을 구성하고 조정하는 방법에 대해 알아보려면 [음성 합성 마크업 언어](speech-synthesis-markup.md#adjust-speaking-styles)를 참조하세요.

> [!TIP]
> 음성 합성 요청에서 "Microsoft Server Speech 텍스트 음성 변환 음성(en-US, AriaNeural)"과 같은 전체 서비스 이름 매핑을 계속 사용할 수 있습니다.

### <a name="standard-voices"></a>표준 음성

75개를 초과하는 표준 음성은 45개 이상의 언어 및 로캘에서 사용할 수 있으며 텍스트를 합성된 음성으로 변환할 수 있습니다. 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| 언어 | 로캘 | 성별 | 음성 이름 |
|--|--|--|--|
| 아랍어 (아랍어)  |  `ar-EG`  |  Female  |  `ar-EG-Hoda`|
| 아랍어(사우디아라비아)  |  `ar-SA`  |  Male  |  `ar-SA-Naayf`|
| 불가리아어(불가리아)  |  `bg-BG`  |  Male  |  `bg-BG-Ivan`|
| 광둥어 (중국어 번체, 홍콩)  |  `zh-HK`  |  Male  |  `zh-HK-Danny`|
| 광둥어 (중국어 번체, 홍콩)  |  `zh-HK`  |  Female  |  `zh-HK-TracyRUS`|
| 카탈로니아어(스페인)  |  `ca-ES`  |  Female  |  `ca-ES-HerenaRUS`|
| 크로아티아어(크로아티아)  |  `hr-HR`  |  Male  |  `hr-HR-Matej`|
| 체코어(체코)  |  `cs-CZ`  |  Male  |  `cs-CZ-Jakub`|
| 덴마크어(덴마크)  |  `da-DK`  |  Female  |  `da-DK-HelleRUS`|
| 네덜란드어(네덜란드)  |  `nl-NL`  |  Female  |  `nl-NL-HannaRUS`|
| 영어(오스트레일리아)  |  `en-AU`  |  Female  |  `en-AU-Catherine`|
| 영어(오스트레일리아)  |  `en-AU`  |  Female  |  `en-AU-HayleyRUS`|
| 영어(캐나다)  |  `en-CA`  |  Female  |  `en-CA-HeatherRUS`|
| 영어(캐나다)  |  `en-CA`  |  Female  |  `en-CA-Linda`|
| 영어(인도)  |  `en-IN`  |  Female  |  `en-IN-Heera`|
| 영어(인도)  |  `en-IN`  |  Female  |  `en-IN-PriyaRUS`|
| 영어(인도)  |  `en-IN`  |  Male  |  `en-IN-Ravi`|
| 영어(아일랜드)  |  `en-IE`  |  Male  |  `en-IE-Sean`|
| 영어(영국)  |  `en-GB`  |  Male  |  `en-GB-George`|
| 영어(영국)  |  `en-GB`  |  Female  |  `en-GB-HazelRUS`|
| 영어(영국)  |  `en-GB`  |  Female  |  `en-GB-Susan`|
| 영어(미국)  |  `en-US`  |  Male  |  `en-US-BenjaminRUS`|
| 영어(미국)  |  `en-US`  |  Male  |  `en-US-GuyRUS`|
| 영어(미국)  |  `en-US`  |  Female  |  `en-US-JessaRUS`|
| 영어(미국)  |  `en-US`  |  Female  |  `en-US-ZiraRUS`|
| 핀란드어(핀란드)  |  `fi-FI`  |  Female  |  `fi-FI-HeidiRUS`|
| 프랑스어(캐나다)  |  `fr-CA`  |  Female  |  `fr-CA-Caroline`|
| 프랑스어(캐나다)  |  `fr-CA`  |  Female  |  `fr-CA-HarmonieRUS`|
| 프랑스어(프랑스)  |  `fr-FR`  |  Female  |  `fr-FR-HortenseRUS`|
| 프랑스어(프랑스)  |  `fr-FR`  |  Female  |  `fr-FR-Julie`|
| 프랑스어(프랑스)  |  `fr-FR`  |  Male  |  `fr-FR-Paul`|
| 프랑스어(스위스)  |  `fr-CH`  |  Male  |  `fr-CH-Guillaume`|
| 독일어(오스트리아)  |  `de-AT`  |  Male  |  `de-AT-Michael`|
| 독일어(독일)  |  `de-DE`  |  Female  |  `de-DE-HeddaRUS`|
| 독일어(독일)  |  `de-DE`  |  Male  |  `de-DE-Stefan`|
| 독일어(스위스)  |  `de-CH`  |  Male  |  `de-CH-Karsten`|
| 그리스어(그리스)  |  `el-GR`  |  Male  |  `el-GR-Stefanos`|
| 히브리어(이스라엘)  |  `he-IL`  |  Male  |  `he-IL-Asaf`|
| 힌디어(인도)  |  `hi-IN`  |  Male  |  `hi-IN-Hemant`|
| 힌디어(인도)  |  `hi-IN`  |  Female  |  `hi-IN-Kalpana`|
| 헝가리어(헝가리)  |  `hu-HU`  |  Male  |  `hu-HU-Szabolcs`|
| 인도네시아어(인도네시아)  |  `id-ID`  |  Male  |  `id-ID-Andika`|
| 이탈리아어(이탈리아)  |  `it-IT`  |  Male  |  `it-IT-Cosimo`|
| 이탈리아어(이탈리아)  |  `it-IT`  |  Female  |  `it-IT-LuciaRUS`|
| 일본어(일본)  |  `ja-JP`  |  Female  |  `ja-JP-Ayumi`|
| 일본어(일본)  |  `ja-JP`  |  Female  |  `ja-JP-HarukaRUS`|
| 일본어(일본)  |  `ja-JP`  |  Male  |  `ja-JP-Ichiro`|
| 한국어(한국)  |  `ko-KR`  |  Female  |  `ko-KR-HeamiRUS`|
| 말레이어(말레이시아)  |  `ms-MY`  |  Male  |  `ms-MY-Rizwan`|
| 북경어 (중국어 간체, 중국)  |  `zh-CN`  |  Female  |  `zh-CN-HuihuiRUS`|
| 북경어 (중국어 간체, 중국)  |  `zh-CN`  |  Male  |  `zh-CN-Kangkang`|
| 북경어 (중국어 간체, 중국)  |  `zh-CN`  |  Female  |  `zh-CN-Yaoyao`|
| 북경어 (중국어 번체, 대만)  |  `zh-TW`  |  Female  |  `zh-TW-HanHanRUS`|
| 북경어 (중국어 번체, 대만)  |  `zh-TW`  |  Female  |  `zh-TW-Yating`|
| 북경어 (중국어 번체, 대만)  |  `zh-TW`  |  Male  |  `zh-TW-Zhiwei`|
| 노르웨이어, BokmÃ ¥ l (노르웨이)  |  `nb-NO`  |  Female  |  `nb-NO-HuldaRUS`|
| 폴란드어(폴란드)  |  `pl-PL`  |  Female  |  `pl-PL-PaulinaRUS`|
| 포르투갈어(브라질)  |  `pt-BR`  |  Male  |  `pt-BR-Daniel`|
| 포르투갈어(브라질)  |  `pt-BR`  |  Female  |  `pt-BR-HeloisaRUS`|
| 포르투갈어(포르투갈)  |  `pt-PT`  |  Female  |  `pt-PT-HeliaRUS`|
| 루마니아어(루마니아)  |  `ro-RO`  |  Male  |  `ro-RO-Andrei`|
| 러시아어(러시아)  |  `ru-RU`  |  Female  |  `ru-RU-EkaterinaRUS`|
| 러시아어(러시아)  |  `ru-RU`  |  Female  |  `ru-RU-Irina`|
| 러시아어(러시아)  |  `ru-RU`  |  Male  |  `ru-RU-Pavel`|
| 슬로바키아어(슬로바키아)  |  `sk-SK`  |  Male  |  `sk-SK-Filip`|
| 슬로베니아어(슬로베니아)  |  `sl-SI`  |  Male  |  `sl-SI-Lado`|
| 스페인어(멕시코)  |  `es-MX`  |  Female  |  `es-MX-HildaRUS`|
| 스페인어(멕시코)  |  `es-MX`  |  Male  |  `es-MX-Raul`|
| 스페인어(스페인)  |  `es-ES`  |  Female  |  `es-ES-HelenaRUS`|
| 스페인어(스페인)  |  `es-ES`  |  Female  |  `es-ES-Laura`|
| 스페인어(스페인)  |  `es-ES`  |  Male  |  `es-ES-Pablo`|
| 스웨덴어(스웨덴)  |  `sv-SE`  |  Female  |  `sv-SE-HedvigRUS`|
| 타밀어(인도)  |  `ta-IN`  |  Male  |  `ta-IN-Valluvar`|
| 텔루구어(인도)  |  `te-IN`  |  Female  |  `te-IN-Chitra`|
| 태국어(태국)  |  `th-TH`  |  Male  |  `th-TH-Pattara`|
| 터키어(터키)  |  `tr-TR`  |  Female  |  `tr-TR-SedaRUS`|
| 베트남어(베트남)  |  `vi-VN`  |  Male  |  `vi-VN-An`  |


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

| 언어 | 로캘 | 텍스트 종속 확인 | 텍스트 독립 확인 | 텍스트 독립 식별 |
|----|----|----|----|----|
|영어(미국)  |  ko-KR  |  예  |  예  |  예 |
|중국어(북경어, 간체) | zh-CN     |     해당 없음 |     예 |     예|
|영어(오스트레일리아)     | en-AU     | 해당 없음     | 예     | 예|
|영어(캐나다)     | en-CA     | 해당 없음 |     예 |     예|
|영어(영국)     | en-GB     | 해당 없음     | 예     | 예|
|프랑스어(캐나다)     | fr-CA     | 해당 없음     | 예 |     예|
|프랑스어(프랑스)     | fr-FR     | 해당 없음     | 예     | 예|
|독일어(독일)     | de-DE     | 해당 없음     | 예     | 예|
|이탈리아어 | it-IT     |     해당 없음     | 예 |     예|
|일본어     | ja-JP | 해당 없음     | 예     | 예|
|포르투갈어(브라질) | pt-BR |     해당 없음 |     예 |     예|
|스페인어(멕시코)     | es-MX     | 해당 없음 |     예 |     예|
|스페인어(스페인)     | es-ES | 해당 없음     | 예 |     예|

## <a name="next-steps"></a>다음 단계

* [평가판 Azure 계정 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
