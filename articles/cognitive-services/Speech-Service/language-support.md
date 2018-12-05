---
title: 언어 지원 - Speech Service API
titleSuffix: Azure Cognitive Services
description: Speech Service에서 지원되는 자연 언어 목록입니다.
services: cognitive-services
author: erhopf
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: conceptual
ms.date: 09/25/2018
ms.author: erhopf
ms.openlocfilehash: fa4563b84eb0882832a89ccc98396ff487f51b48
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284764"
---
# <a name="language-and-region-support-for-speech-service-api"></a>Speech Service API에 대한 언어 및 지역 지원

Speech Service 기능마다 다른 언어가 지원됩니다. 다음 표에서는 언어 지원을 요약합니다.

## <a name="speech-to-text"></a>음성을 텍스트로 변환

Microsoft Speech Recognition API는 다음 언어를 지원합니다. 서로 다른 수준의 사용자 지정을 각 언어에 사용할 수 있습니다.

  코드 | 언어 | [음향 적응](how-to-customize-acoustic-models.md) | [언어 적응](how-to-customize-language-model.md) | [발음 적응](how-to-customize-pronunciation.md)
 ------|----------|---------------------|---------------------|-------------------------
 ar-EG | 아랍어(이집트), 현대 표준 | 아니요 | yes | 아니요
 ca-ES | 카탈로니아어(스페인) | 아니요 | 아니요 | 아니요
 da-DK | 덴마크어(덴마크) | 아니요 | 아니요 | 아니요
 de-DE | 독일어(독일) | yes | yes | 아니요
 en-AU | 영어(오스트레일리아) | 아니요 | 예 | yes
 en-CA | 영어(캐나다) | 아니요 | 예 | yes
 en-GB | 영어(영국) | 아니요 | 예 | yes
 en-IN | 영어(인도) | yes | 예 | yes
 en-NZ | 영어(뉴질랜드) | 아니요 | 예 | yes  
 en-US | 영어(미국) | yes | 예 | yes
 es-ES | 스페인어(스페인) | 아니요 | yes | 아니요
 es-MX | 스페인어(멕시코) | 아니요 | yes | 아니요
 fi-FI | 핀란드어(핀란드) | 아니요 | 아니요 | 아니요
 fr-CA | 프랑스어(캐나다) | 아니요 | yes | 아니요
 fr-FR | 프랑스어(프랑스) | yes | yes | 아니요
 hi-IN | 힌디어(인도) | 아니요 | yes | 아니요
 it-IT | 이탈리아어(이탈리아) | yes | yes | 아니요
 ja-JP | 일본어(일본) | 아니요 | yes | 아니요
 ko-KR | 한국어(한국) | 아니요 | yes | 아니요
 nb-NO | 노르웨이어(복말)(노르웨이) | 아니요 | 아니요 | 아니요
 nl-NL | 네덜란드어(네덜란드) | 아니요 | yes | 아니요
 pl-PL | 폴란드어(폴란드) | 아니요 | 아니요 | 아니요
 pt-BR | 포르투갈어(브라질) | 아니요 | yes | 아니요
 pt-PT | 포르투갈어(포르투갈) | 아니요 | yes | 아니요
 ru-RU | 러시아어(러시아) | yes | yes | 아니요
 sv-SE | 스웨덴어(스웨덴) | 아니요 | 아니요 | 아니요
 zh-CN | 중국어(북경어, 간체) | yes | yes | 아니요
 zh-HK | 중국어(북경어, 번체) | 아니요 | yes | 아니요
 zh-TW | 중국어(대만어) | 아니요 | yes | 아니요
 th-TH | 태국어(태국) | 아니요 | 아니요 | 아니요


## <a name="text-to-speech"></a>텍스트에서 음성 변환

Speech Synthesis API는 각각 로캘로 식별되는 특정 언어를 지원하는 다음과 같은 음성을 제공합니다.

로캘 | 언어 | 성별 | 서비스 이름 매핑
-------|----------|---------|--------------------
ar-EG\* | 아랍어(이집트) | Female | "Microsoft Server Speech Text to Speech Voice(ar-EG, Hoda)"
ar-SA | 아랍어(사우디아라비아) | Male | “Microsoft Server Speech Text to Speech Voice(ar-SA, Naayf)”
bg-BG | 불가리아어 | Male | “Microsoft Server Speech Text to Speech Voice(bg-BG, Ivan)”
ca-ES | 카탈로니아어(스페인) | Female | “Microsoft Server Speech Text to Speech Voice(ca-ES, HerenaRUS)”
cs-CZ | 체코어 | Male | “Microsoft Server Speech Text to Speech Voice(cs-CZ, Jakub)”
da-DK | 덴마크어 | Female | “Microsoft Server Speech Text to Speech Voice(da-DK, HelleRUS)”
de-AT | 독일어(오스트리아) | Male | “Microsoft Server Speech Text to Speech Voice(de-AT, Michael)”
de-CH | 독일어(스위스) | Male | “Microsoft Server Speech Text to Speech Voice(de-CH, Karsten)”
de-DE | 독일어(독일) | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, Hedda)"
| | | Female | "Microsoft Server Speech Text to Speech Voice(de-DE, HeddaRUS)"
| | | Male | "Microsoft Server Speech Text to Speech Voice(de-DE, Stefan, Apollo)"
el-GR | 그리스어 | Male | “Microsoft Server Speech Text to Speech Voice(el-GR, Stefanos)”
en-AU | 영어(오스트레일리아) | Female | "Microsoft Server Speech Text to Speech Voice(en-AU, Catherine)"
| | | Female | “Microsoft Server Speech Text to Speech Voice(en-AU, HayleyRUS)”
en-CA | 영어(캐나다) | Female | "Microsoft Server Speech Text to Speech Voice(en-CA, Linda)"
| | | Female | “Microsoft Server Speech Text to Speech Voice(en-CA, HeatherRUS)”
en-GB | 영어(영국) | Female | “Microsoft Server Speech Text to Speech Voice(en-GB, Susan, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(en-GB, HazelRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(en-GB, George, Apollo)”
en-IE | 영어(아일랜드) |Male | “Microsoft Server Speech Text to Speech Voice(en-IE, Sean)”
en-IN | 영어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(en-IN, Heera, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(en-IN, PriyaRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(en-IN, Ravi, Apollo)”
en-US | 영어(미국) |Female | “Microsoft Server Speech Text to Speech Voice(en-US, ZiraRUS)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(en-US, JessaRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(en-US, BenjaminRUS)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(en-US, Jessa24kRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(en-US, Guy24kRUS)”
es-ES | 스페인어(스페인) |Female | “Microsoft Server Speech Text to Speech Voice(es-ES, Laura, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(es-ES, HelenaRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(es-ES, Pablo, Apollo)”
es-MX | 스페인어(멕시코) | Female | “Microsoft Server Speech Text to Speech Voice(es-MX, HildaRUS)”
| | | Male | “Microsoft Server Speech Text to Speech Voice(es-MX, Raul, Apollo)”
fi-FI | 핀란드어 | Female | “Microsoft Server Speech Text to Speech Voice(fi-FI, HeidiRUS)”
fr-CA | 프랑스어(캐나다) |Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, Caroline)”
| | | Female | “Microsoft Server Speech Text to Speech Voice(fr-CA, HarmonieRUS)”
fr-CH | 프랑스어(스위스)|Male | “Microsoft Server Speech Text to Speech Voice(fr-CH, Guillaume)”
fr-FR | 프랑스어(프랑스)|Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, Julie, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(fr-FR, HortenseRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(fr-FR, Paul, Apollo)”
he-IL| 히브리어(이스라엘) | Male| “Microsoft Server Speech Text to Speech Voice(he-IL, Asaf)”
hi-IN | 힌디어(인도) | Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(hi-IN, Kalpana)”
| | | Male | “Microsoft Server Speech Text to Speech Voice(hi-IN, Hemant)”
hr-HR | 크로아티아어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)”
hu-HU | 헝가리어 | Male | “Microsoft Server Speech Text to Speech Voice(hr-HR, Matej)”
id-ID | 인도네시아어| Male | "Microsoft Server Speech Text to Speech Voice(id-ID, Andika)"
it-IT | 이탈리아어 |Male | “Microsoft Server Speech Text to Speech Voice(it-IT, Cosimo, Apollo)”
| | |Female | "Microsoft Server Speech Text to Speech Voice(it-IT, LuciaRUS)"
ja-JP | 일본어 |Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ayumi, Apollo)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(ja-JP, Ichiro, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(ja-JP, HarukaRUS)”
ko-KR | 한국어 |Female | “Microsoft Server Speech Text to Speech Voice(ko-KR, HeamiRUS)”
ms-MY | 말레이어 | Male | “Microsoft Server Speech Text to Speech Voice(ms-MY, Rizwan)”
nb-NO | 노르웨이어 | Female | “Microsoft Server Speech Text to Speech Voice(nb-NO, HuldaRUS)”
nl-NL | 네덜란드어 | Female | “Microsoft Server Speech Text to Speech Voice(nl-NL, HannaRUS)”
pl-PL | 폴란드어 | Female | “Microsoft Server Speech Text to Speech Voice(pl-PL, PaulinaRUS)”
pt-BR | 포르투갈어(브라질) | Female | “Microsoft Server Speech Text to Speech Voice(pt-BR, HeloisaRUS)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(pt-BR, Daniel, Apollo)”
pt-PT | 포르투갈어(포르투갈) | Female | “Microsoft Server Speech Text to Speech Voice(pt-PT, HeliaRUS)”
ro-RO | 루마니아어 | Male | “Microsoft Server Speech Text to Speech Voice(ro-RO, Andrei)”
ru-RU |러시아어| Female | “Microsoft Server Speech Text to Speech Voice(ru-RU, Irina, Apollo)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(ru-RU, Pavel, Apollo)”
| | |Female | "Microsoft Server Speech Text to Speech Voice(ru-RU, EkaterinaRUS)"
sk-SK | 슬로바키아어|Male | “Microsoft Server Speech Text to Speech Voice(sk-SK, Filip)”
sl-SI | 슬로베니아어|Male | “Microsoft Server Speech Text to Speech Voice(sl-SI, Lado)”
sv-SE | 스웨덴어|Female | “Microsoft Server Speech Text to Speech Voice(sv-SE, HedvigRUS)”
ta-IN | 타밀어(인도) |Male | “Microsoft Server Speech Text to Speech Voice(ta-IN, Valluvar)”
te-IN | 텔루구어(인도) |Female | “Microsoft Server Speech Text to Speech Voice(te-IN, Chitra)”
th-TH | 태국어|Male | “Microsoft Server Speech Text to Speech Voice(th-TH, Pattara)”
tr-TR |터키어| Female | “Microsoft Server Speech Text to Speech Voice(tr-TR, SedaRUS)”
vi-VN | 베트남어|Male | “Microsoft Server Speech Text to Speech Voice(vi-VN, An)”
zh-CN | 중국어(본토)|Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, HuihuiRUS)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(zh-CN, Yaoyao, Apollo)”
| | |Male | “Microsoft Server Speech Text to Speech Voice(zh-CN, Kangkang, Apollo)”
zh-HK | 중국어(홍콩)|Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, Tracy, Apollo)”
| | |Female | “Microsoft Server Speech Text to Speech Voice(zh-HK, TracyRUS)”
| || Male | “Microsoft Server Speech Text to Speech Voice(zh-HK, Danny, Apollo)”
zh-TW | 중국어(대만)|Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, Yating, Apollo)”
| || Female | “Microsoft Server Speech Text to Speech Voice(zh-TW, HanHanRUS)”
| || Male | “Microsoft Server Speech Text to Speech Voice(zh-TW, Zhiwei, Apollo)”

\* *ar-EG는 MSA(Modern Standard Arabic)를 지원합니다.*

### <a name="customization"></a>사용자 지정

음성 사용자 지정은 미국 영어(en-US), 본토 중국어(zh-CN), 프랑스어(fr-FR), 독일어(de-DE) 및 이탈리아어(it-IT)로 사용할 수 있습니다.

> [!NOTE]
> 프랑스어, 독일어 및 이탈리아 음성 학습은 2,000개 이상의 발화가 있는 데이터 세트로 시작합니다. 중국어-영어 이중 언어 모델도 2,000개 이상의 발화로 이루어진 초기 데이터 집합으로 지원됩니다.

## <a name="speech-translation"></a>Speech Translation

**Speech Translation** API는 음성 간 음성 및 음성을 텍스트로 번역에 대해 다른 언어를 지원합니다. 원본 언어는 다음과 같은 음성 언어 테이블 중 하나여야 합니다. 사용 가능한 대상 언어는 번역 대상이 음성인지 또는 텍스트인지에 따라 달라집니다. 들어오는 음성을 [60개 언어](https://www.microsoft.com/translator/business/languages/) 이상으로 변환할 수 있습니다. 이러한 언어의 하위 집합을 [음성 합성](language-support.md#text-languages)에 사용할 수 있습니다. 

### <a name="speech-languages"></a>음성 언어

| 음성 언어   | 언어 코드 |
|:----------- |-|
| 아랍어(최신 표준)      | `ar` |
| 중국어(북경어)      | `zh` |
| 영어      | `en` |
| 프랑스어      | `fr` |
| 독일어      | `de` |
| 이탈리아어      | `it` |
| 일본어      | `jp` |
| 포르투갈어(브라질어)     | `pt` |
| 러시아어      | `ru` |
| 스페인어      |  `es` |

### <a name="text-languages"></a>텍스트 언어

| 텍스트 언어    | 언어 코드 |
|:----------- |:-------------:|
| 아프리칸스어      | `af`          |
| 아랍어       | `ar`          |
| 벵골어      | `bn`          |
| 보스니아어(라틴 문자)      | `bs`          |
| 불가리아어      | `bg`          |
| 광둥어(번체)      | `yue`          |
| 카탈로니아어      | `ca`          |
| 중국어 간체      | `zh-Hans`          |
| 중국어 번체      | `zh-Hant`          |
| 크로아티아어      | `hr`          |
| 체코어      | `cs`          |
| 덴마크어      | `da`          |
| 네덜란드어      | `nl`          |
| 영어      | `en`          |
| 에스토니아어      | `et`          |
| 피지어      | `fj`          |
| 필리핀어      | `fil`          |
| 핀란드어      | `fi`          |
| 프랑스어      | `fr`          |
| 독일어      | `de`          |
| 그리스어      | `el`          |
| 아이티 크리올      | `ht`          |
| 히브리어      | `he`          |
| 힌디어      | `hi`          |
| 몽 다오어      | `mww`          |
| 헝가리어      | `hu`          |
| 인도네시아어      | `id`          |
| 이탈리아어      | `it`          |
| 일본어      | `ja`          |
| 스와힐리어      | `sw`          |
| 클링곤어      | `tlh`          |
| 클링곤어(plqaD)      | `tlh-Qaak`          |
| 한국어      | `ko`          |
| 라트비아어      | `lv`          |
| 리투아니아어      | `lt`          |
| 마다가스카르어      | `mg`          |
| 말레이어      | `ms`          |
| 몰타어      | `mt`          |
| 노르웨이어      | `nb`          |
| 페르시아어      | `fa`          |
| 폴란드어      | `pl`          |
| 포르투갈어      | `pt`          |
| 케레타로 오토미어      | `otq`          |
| 루마니아어      | `ro`          |
| 러시아어      | `ru`          |
| 사모아어      | `sm`          |
| 세르비아어(키릴자모)      | `sr-Cyrl`          |
| 세르비아어(라틴 문자)      | `sr-Latn`          |
| 슬로바키아어     | `sk`          |
| 슬로베니아어      | `sl`          |
| 스페인어      | `es`          |
| 스웨덴어      | `sv`          |
| 타히티어      | `ty`          |
| 타밀어      | `ta`          |
| 태국어      | `th`          |
| 통가어      | `to`          |
| 터키어      | `tr`          |
| 우크라이나어      | `uk`          |
| 우르두어      | `ur`          |
| 베트남어      | `vi`          |
| 웨일스어      | `cy`          |
| 유카텍 마야어      | `yua`          |


## <a name="next-steps"></a>다음 단계

* [음성 평가판 구독 가져오기](https://azure.microsoft.com/try/cognitive-services/)
* [C#에서 음성을 인식하는 방법 참조](quickstart-csharp-dotnet-windows.md)
