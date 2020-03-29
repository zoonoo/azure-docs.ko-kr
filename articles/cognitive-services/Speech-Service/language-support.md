---
title: 언어 지원 - 음성 서비스
titleSuffix: Azure Cognitive Services
description: 음성 변환 서비스는 음성 변환과 함께 음성 간 변환을 위한 다양한 언어를 지원합니다. 이 문서에서는 서비스 기능별 언어 지원의 포괄적인 목록을 제공합니다.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: dapine
ms.custom: seodec18
ms.openlocfilehash: d01ab60790311649e424a98d5a08c6af0bca90f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336048"
---
# <a name="language-and-voice-support-for-the-speech-service"></a>음성 서비스에 대한 언어 및 음성 지원

언어 지원은 음성 서비스 기능에 따라 다릅니다. 다음 표에는 [음성-텍스트,](#speech-to-text) [텍스트 변환](#text-to-speech)및 [음성 변환](#speech-translation) 서비스 제공에 대한 언어 지원이 요약되어 있습니다.

## <a name="speech-to-text"></a>음성 텍스트 변환

Microsoft 음성 SDK와 REST API는 모두 다음 언어(로캘)를 지원합니다. 정확도를 높이기 위해 오디오 + 사람이 표시한 자막 또는 관련 텍스트: 문장을 업로드하여 언어의 하위 집합에 대한 사용자 지정이 제공됩니다. 발음 사용자 정의는 현재 에 `en-US` 대해서만 `de-DE`사용할 수 있습니다. 사용자 지정에 대한 자세한 내용은 [여기를](how-to-custom-speech.md)참조하십시오.

<!--
To get the AM and ML bits:
https://westus.cris.ai/swagger/ui/index#/Custom%20Speech%20models%3A/GetSupportedLocalesForModels

To get pronunciation bits:
https://cris.ai -> Click on Adaptation Data -> scroll down to section "Pronunciation Datasets" -> Click on Import -> Locale: the list of locales there correspond to the supported locales
-->

| Locale  | 언어                          | 지원됨 | 사용자 지정                                    |
|---------|-----------------------------------|-----------|---------------------------------------------------|
| `ar-AE` | 아랍어(아랍에미리트)                      | yes       | 예                                                |
| `ar-BH` | 아랍어(바레인), 현대 표준 | yes       | 언어 모델                                    |
| `ar-EG` | 아랍어(이집트)                    | yes       | 언어 모델                                    |
| `ar-KW` | 아랍어(쿠웨이트)                   | yes       | 예                                                |
| `ar-QA` | 아랍어(카타르)                    | yes       | 예                                                |
| `ar-SA` | 아랍어(사우디아라비아)             | yes       | 예                                                |
| `ar-SY` | 아랍어(시리아)                    | yes       | 언어 모델                                    |
| `ca-ES` | 카탈로니아어                           | yes       | 언어 모델                                    |
| `da-DK` | 덴마크어(덴마크)                  | yes       | 언어 모델                                    |
| `de-DE` | 독일어(독일)                  | yes       | 음향 모델<br>언어 모델<br>발음 |
| `en-AU` | 영어(오스트레일리아)               | yes       | 음향 모델<br>언어 모델                  |
| `en-CA` | 영어(캐나다)                  | yes       | 음향 모델<br>언어 모델                  |
| `en-GB` | 영어(영국)          | yes       | 음향 모델<br>언어 모델<br>발음 |
| `en-IN` | 영어(인도)                   | yes       | 음향 모델<br>언어 모델                  |
| `en-NZ` | 영어(뉴질랜드)             | yes       | 음향 모델<br>언어 모델                  |
| `en-US` | 영어(미국)           | yes       | 음향 모델<br>언어 모델<br>발음 |
| `es-ES` | 스페인어(스페인)                   | yes       | 음향 모델<br>언어 모델                  |
| `es-MX` | 스페인어(멕시코)                  | yes       | 음향 모델<br>언어 모델                  |
| `fi-FI` | 핀란드어(핀란드)                 | yes       | 언어 모델                                    |
| `fr-CA` | 프랑스어(캐나다)                   | yes       | 음향 모델<br>언어 모델                  |
| `fr-FR` | 프랑스어(프랑스)                   | yes       | 음향 모델<br>언어 모델<br>발음 |
| `gu-IN` | 구자라트어(인도)                 | yes       | 언어 모델                                    |
| `hi-IN` | 힌디어(인도)                     | yes       | 음향 모델<br>언어 모델                  |
| `it-IT` | 이탈리아어(이탈리아)                   | yes       | 음향 모델<br>언어 모델<br>발음 |
| `ja-JP` | 일본어(일본)                  | yes       | 언어 모델                                    |
| `ko-KR` | 한국어(한국)                    | yes       | 언어 모델                                    |
| `mr-IN` | 마라티어(인도)                   | yes       | 언어 모델                                    |
| `nb-NO` | 노르웨이어(복말)(노르웨이)       | yes       | 언어 모델                                    |
| `nl-NL` | 네덜란드어(네덜란드)               | yes       | 언어 모델                                    |
| `pl-PL` | 폴란드어(폴란드)                   | yes       | 언어 모델                                    |
| `pt-BR` | 포르투갈어(브라질)               | yes       | 음향 모델<br>언어 모델<br>발음 |
| `pt-PT` | 포르투갈어(포르투갈)             | yes       | 언어 모델                                    |
| `ru-RU` | 러시아어(러시아)                  | yes       | 음향 모델<br>언어 모델                  |
| `sv-SE` | 스웨덴어(스웨덴)                  | yes       | 언어 모델                                    |
| `ta-IN` | 타밀어(인도)                     | yes       | 언어 모델                                    |
| `te-IN` | 텔루구어(인도)                    | yes       | 예                                                |
| `th-TH` | 태국어(태국)                   | yes       | 예                                                |
| `tr-TR` | 터키어(터키)                  | yes       | 예                                                |
| `zh-CN` | 중국어(북경어, 간체)    | yes       | 음향 모델<br>언어 모델                  |
| `zh-HK` | 중국어(광둥어, 전통)  | yes       | 언어 모델                                    |
| `zh-TW` | 중국어(대만어)      | yes       | 언어 모델                                    |

## <a name="text-to-speech"></a>텍스트 음성 변환

Microsoft 음성 SDK와 REST API는 모두 이러한 음성을 지원하며, 각 음성은 로캘로 식별되는 특정 언어와 방언을 지원합니다.

> [!IMPORTANT]
> 가격 책정은 표준, 사용자 지정 및 인공신경망 음성마다 다릅니다. 자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/) 페이지를 참조하세요.

### <a name="neural-voices"></a>인공신경망 음성

인공신경망 텍스트-음성 변환은 심층 신경망에서 지원되는 새로운 형식의 음성 합성입니다. 인공신경망 음성을 사용하는 경우 합성된 음성은 인간 음성 녹음과 거의 구분되지 않습니다.

신경 음성은 챗봇 및 음성 비서와의 상호 작용을 보다 자연스럽고 매력적으로 만들고, 전자책과 같은 디지털 텍스트를 오디오북으로 변환하고, 차량 내비게이션 시스템을 향상시키는 데 사용할 수 있습니다. 인간과 유사한 자연스러운 운율 및 단어의 명확한 조음을 사용하면 사용자가 AI 시스템과 상호 작용할 때 인공신경망 음성은 수신 피로도를 현저히 줄일 수 있습니다.

국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| Locale  | 언어            | 성별 | 전체 서비스 이름 매핑                                               | 짧은 음성 이름        |
|---------|---------------------|--------|-------------------------------------------------------------------------|-------------------------|
| `de-DE` | 독일어(독일)    | Female | "음성 음성마이크로 마이크로소프트 서버 음성 텍스트(de-DE, KatjaNeural)"     | "드 드 카자 뉴럴"     |
| `en-US` | 영어(미국)        | Female | "음성 음성마이크로 마이크로소프트 서버 음성 텍스트(en-US, AriaNeural)"      | "en-US-AriaNeural"      |
| `en-US` | 영어(미국)        | Male   | "Microsoft Server Speech Text to Speech Voice (en-US, GuyNeural)"       | "en-US-가이뉴럴"       |
| `it-IT` | 이탈리아어(이탈리아)     | Female | "음성 음성마이크로 마이크로소프트 서버 음성 텍스트(IT, 엘사뉴럴)"      | "IT-엘사뉴럴"      |
| `pt-BR` | 포르투갈어(브라질) | Female | "음성 음성마이크로 마이크로소프트 서버 음성 텍스트(pt-BR, 프란시스카뉴럴)" | "pt-BR-프란시스카뉴럴" |
| `zh-CN` | 중국어(북경어, 간체)  | Female | "Microsoft Server Speech Text to Speech Voice(zh-CN, XiaoxiaoNeural)"  | "zh-CN-샤오샤오성신경"  |

> [!IMPORTANT]
> 음성이 `en-US-JessaNeural` 로 `en-US-AriaNeural`변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환합니다.

신경 음성을 구성하고 조정하는 방법을 알아보려면 [음성 합성 태그 언어를](speech-synthesis-markup.md#adjust-speaking-styles)참조하십시오.

> [!TIP]
> 음성 합성 요청에서 전체 서비스 이름 매핑 또는 짧은 음성 이름을 사용할 수 있습니다.

### <a name="standard-voices"></a>표준 음성

75개를 초과하는 표준 음성은 45개 이상의 언어 및 로캘에서 사용할 수 있으며 텍스트를 합성된 음성으로 변환할 수 있습니다. 국가별 가용성에 대한 자세한 내용은 [지역](regions.md#standard-and-neural-voices)을 참조하세요.

| Locale | 언어 | 성별 | 전체 서비스 이름 매핑 | 짧은 이름 |
|--|--|--|--|--|
| <sup>1</sup>`ar-EG` | 아랍어(이집트) | Female | "Microsoft Server Speech Text to Speech Voice(ar-EG, Hoda)" | "아르-EG-호다" |
| `ar-SA` | 아랍어(사우디아라비아) | Male | “Microsoft Server Speech Text to Speech Voice(ar-SA, Naayf)” | "아르-SA-나예프" |
| `bg-BG` | 불가리아어 | Male | “Microsoft Server Speech Text to Speech Voice(bg-BG, Ivan)” | "bg-BG-이반" |
| `ca-ES` | 카탈로니아어(스페인) | Female | “Microsoft Server Speech Text to Speech Voice(ca-ES, HerenaRUS)” | "ca-ES-헤레나루스" |
| `cs-CZ` | 체코어 | Male | “Microsoft Server Speech Text to Speech Voice(cs-CZ, Jakub)” | "cs-CZ-야쿠브" |
| `da-DK` | 덴마크어 | Female | “Microsoft Server Speech Text to Speech Voice(da-DK, HelleRUS)” | "다-DK-헬레루스" |
| `de-AT` | 독일어(오스트리아) | Male | “Microsoft Server Speech Text to Speech Voice(de-AT, Michael)” | "드-AT-마이클" |
| `de-CH` | 독일어(스위스) | Male | “Microsoft Server Speech Text to Speech Voice(de-CH, Karsten)” | "드 CH-카르스텐" |
| `de-DE` | 독일어(독일) | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, Hedda)" | "드 드 헤다" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, HeddaRUS)" | "드 드 헤다루스" |
|  |  | Male | "Microsoft Server Speech Text to Speech Voice(de-DE, Stefan, Apollo)" | "드 드 스테판 아폴로" |
| `el-GR` | 그리스어 | Male | “Microsoft Server Speech Text to Speech Voice(el-GR, Stefanos)” | "엘-GR-스테파노스" |
| `en-AU` | 영어(오스트레일리아) | Female | "Microsoft Server Speech Text to Speech Voice(en-AU, Catherine)" | "앙-아우-캐서린" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-AU, HayleyRUS)” | "엔-아우 헤일리루스" |
| `en-CA` | 영어(캐나다) | Female | "Microsoft Server Speech Text to Speech Voice(en-CA, Linda)" | "엔-CA-린다" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-CA, HeatherRUS)” | "엔-CA-헤더러스" |
| `en-GB` | 영어(영국) | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, Susan, Apollo)” | "엔 GB-수잔 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, HazelRUS)” | "엔-GB-헤이즐러스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-GB, George, Apollo)” | "엔 GB-조지 아폴로" |
| `en-IE` | 영어(아일랜드) | Male | “Microsoft Server Speech Text to Speech Voice(en-IE, Sean)” | "엔-IE-숀" |
| `en-IN` | 영어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, Heera, Apollo)” | "엔인히라 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, PriyaRUS)” | "엔인 프리야루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-IN, Ravi, Apollo)” | "엔인 라비 아폴로" |
| `en-US` | 영어(미국) | Female | “Microsoft Server Speech Text to Speech Voice(en-US, ZiraRUS)” | "엔-미국-지라루스" |
|  |  | Female | "음성 음성마이크로 마이크로소프트 서버 음성 텍스트(en-US, AriaRUS)" | "앙-미국-아리아루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-US, BenjaminRUS)” | "en-US-벤자민루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(en-US, Guy24kRUS)” | "en-US-Guy24kRUS" |
| `es-ES` | 스페인어(스페인) | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, Laura, Apollo)” | "에스-에스-로라-아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(es-ES, HelenaRUS)” | "에스-에스-헬레나루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(es-ES, Pablo, Apollo)” | "에스 에스 파블로 아폴로" |
| `es-MX` | 스페인어(멕시코) | Female | “Microsoft Server Speech Text to Speech Voice(es-MX, HildaRUS)” | "에스-MX-힐다루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(es-MX, Raul, Apollo)” | "에스-MX-라울 아폴로" |
| `fi-FI` | 핀란드어 | Female | “Microsoft Server Speech Text to Speech Voice(fi-FI, HeidiRUS)” | "파이-FI-하이디루스" |
| `fr-CA` | 프랑스어(캐나다) | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, Caroline)” | "FR-CA-캐롤라인" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, HarmonieRUS)” | "fr-CA-하모니루스" |
| `fr-CH` | 프랑스어(스위스) | Male | “Microsoft Server Speech Text to Speech Voice(fr-CH, Guillaume)” | "fr-CH-기요메" |
| `fr-FR` | 프랑스어(프랑스) | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, Julie, Apollo)” | "FR-FR-줄리 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, HortenseRUS)” | "FR-FR-호르텐세루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(fr-FR, Paul, Apollo)” | "FR-FR-폴-아폴로" |
| `he-IL` | 히브리어(이스라엘) | Male | “Microsoft Server Speech Text to Speech Voice(he-IL, Asaf)” | "그는 IL-아사프" |
| `hi-IN` | 힌디어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana, Apollo)” | "하이인 칼파나 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana)” | "하이인 칼파나" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(hi-IN, Hemant)” | "하이인 헤만트" |
| `hr-HR` | 크로아티아어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)” | "HR-HR-마테이" |
| `hu-HU` | 헝가리어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)” | "후후자볼크" |
| `id-ID` | 인도네시아어 | Male | "Microsoft Server Speech Text to Speech Voice(id-ID, Andika)" | "신분증-안디카" |
| `it-IT` | 이탈리아어 | Male | “Microsoft Server Speech Text to Speech Voice(it-IT, Cosimo, Apollo)” | "IT-코시모 아폴로" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(it-IT, LuciaRUS)" | "IT-루시아루스" |
| `ja-JP` | 일본어 | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ayumi, Apollo)” | "자-JP-아유미 아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ichiro, Apollo)” | "자-JP-이치로 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, HarukaRUS)” | "자-JP-하루카루스" |
| `ko-KR` | 한국어 | Female | “Microsoft Server Speech Text to Speech Voice(ko-KR, HeamiRUS)” | "코-KR-하미루스" |
| `ms-MY` | 말레이어 | Male | “Microsoft Server Speech Text to Speech Voice(ms-MY, Rizwan)” | "미스-마이-리즈완" |
| `nb-NO` | 노르웨이어 | Female | “Microsoft Server Speech Text to Speech Voice(nb-NO, HuldaRUS)” | "nb-NO-훌다루스" |
| `nl-NL` | 네덜란드어 | Female | “Microsoft Server Speech Text to Speech Voice(nl-NL, HannaRUS)” | "nl-NL-한나루스" |
| `pl-PL` | 폴란드어 | Female | “Microsoft Server Speech Text to Speech Voice(pl-PL, PaulinaRUS)” | "pl-PL-폴리나루스" |
| `pt-BR` | 포르투갈어(브라질) | Female | “Microsoft Server Speech Text to Speech Voice(pt-BR, HeloisaRUS)” | "pt-BR-헬로이사루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(pt-BR, Daniel, Apollo)” | "pt-BR-다니엘-아폴로" |
| `pt-PT` | 포르투갈어(포르투갈) | Female | “Microsoft Server Speech Text to Speech Voice(pt-PT, HeliaRUS)” | "pt-PT-헬리아루스" |
| `ro-RO` | 루마니아어 | Male | “Microsoft Server Speech Text to Speech Voice(ro-RO, Andrei)” | "로-안드레이" |
| `ru-RU` | 러시아어 | Female | “Microsoft Server Speech Text to Speech Voice(ru-RU, Irina, Apollo)” | "루-루-이리나-아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(ru-RU, Pavel, Apollo)” | "루-루-파벨-아폴로" |
|  |  | Female | "Microsoft Server Speech Text to Speech Voice(ru-RU, EkaterinaRUS)" | 루-에카테리나루스 |
| `sk-SK` | 슬로바키아어 | Male | “Microsoft Server Speech Text to Speech Voice(sk-SK, Filip)” | "SK-필립" |
| `sl-SI` | 슬로베니아어 | Male | “Microsoft Server Speech Text to Speech Voice(sl-SI, Lado)” | "sl-SI-라도" |
| `sv-SE` | 스웨덴어 | Female | “Microsoft Server Speech Text to Speech Voice(sv-SE, HedvigRUS)” | "sv-SE-HedvigRUS" |
| `ta-IN` | 타밀어(인도) | Male | “Microsoft Server Speech Text to Speech Voice(ta-IN, Valluvar)” | "타인발루바르" |
| `te-IN` | 텔루구어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(te-IN, Chitra)” | "테인 치트라" |
| `th-TH` | 태국어 | Male | “Microsoft Server Speech Text to Speech Voice(th-TH, Pattara)” | "TH-TH-파타라" |
| `tr-TR` | 터키어(터키) | Female | “Microsoft Server Speech Text to Speech Voice(tr-TR, SedaRUS)” | "TR-TR-세다루스" |
| `vi-VN` | 베트남어 | Male | “Microsoft Server Speech Text to Speech Voice(vi-VN, An)” | "vi-VN-An" |
| `zh-CN` | 중국어(북경어, 간체) | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, HuihuiRUS)” | "zh-CN-후이후이루스" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, Yaoyao, Apollo)” | "zh-CN-야오야오 아폴로" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-CN, Kangkang, Apollo)” | "zh-CN-강강-아폴로" |
| `zh-HK` | 중국어(광둥어, 전통) | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, Tracy, Apollo)” | "zh-HK-트레이시 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, TracyRUS)” | "zh-HK-트레이시루스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-HK, Danny, Apollo)” | "zh-HK-대니 아폴로" |
| `zh-TW` | 중국어(대만어) | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, Yating, Apollo)” | "zh-TW-예팅 아폴로" |
|  |  | Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, HanHanRUS)” | "zh-TW-한러스" |
|  |  | Male | “Microsoft Server Speech Text to Speech Voice(zh-TW, Zhiwei, Apollo)” | "Zh-TW-지웨이 아폴로" |

**1** *ar-EG는 현대 표준 아랍어(MSA)를 지원합니다.*

> [!IMPORTANT]
> 음성이 `en-US-Jessa` 로 `en-US-Aria`변경되었습니다. 이전에 "Jessa"를 사용했다면 "Aria"로 변환합니다.

> [!TIP]
> 음성 합성 요청에서 전체 서비스 이름 매핑 또는 짧은 음성 이름을 사용할 수 있습니다.

### <a name="customization"></a>사용자 지정

음성 사용자 지정은 `de-DE` `en-GB`에 `en-IN` `en-US`사용할 `es-MX` `fr-FR`수 `it-IT` `pt-BR`있습니다 `zh-CN`. 사용자 지정 음성 모델을 학습하는 데 필요한 교육 데이터와 일치하는 올바른 로캘을 선택합니다. 예를 들어, 보유한 기록 데이터가 영어로 영어로 사용되는 `en-GB`경우 를 선택합니다.

> [!NOTE]
> 중국어-영어 이중 언어를 제외한 커스텀 보이스에서는 이중 언어 모델 교육을 지원하지 않습니다. 영어를 할 수 있는 중국어 음성을 훈련하려면 "중국어-영어 이중 언어"를 선택합니다. 모든 로캘의 음성 학습은 모든 규모의 학습 데이터로 시작할 수 있는 `en-US` `zh-CN` 경우를 제외하고 2,000개 이상의 발언데이터 집합으로 시작합니다.

## <a name="speech-translation"></a>음성 번역

**Speech Translation** API는 음성 간 음성 및 음성을 텍스트로 번역에 대해 다른 언어를 지원합니다. 원본 언어는 항상 음성-텍스트 언어 테이블에서 제공되어야 합니다. 사용 가능한 대상 언어는 번역 대상이 음성인지 또는 텍스트인지에 따라 달라집니다. 들어오는 음성을 [60개 언어](https://www.microsoft.com/translator/business/languages/) 이상으로 변환할 수 있습니다. 언어의 하위 집합은 [음성 합성에](language-support.md#text-languages)사용할 수 있습니다.

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

* [음성 서비스 평가판 구독 받기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](~/articles/cognitive-services/Speech-Service/quickstarts/speech-to-text-from-microphone.md?pivots=programming-language-chsarp)
