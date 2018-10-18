---
title: 지원되는 언어 - Translator Speech API
titlesuffix: Azure Cognitive Services
description: Translator Speech API에서 지원되는 언어를 봅니다.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-speech
ms.topic: conceptual
ms.date: 3/5/2018
ms.author: v-jansko
ms.openlocfilehash: dd1c60a2c2ad88abf686e89972d29768ef600e4e
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2018
ms.locfileid: "49344560"
---
# <a name="languages-supported-by-the-translator-speech-api"></a>Translator Speech API에서 지원되는 언어

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-translator-speech-deprecation-note.md)]

음성 번역에 다음 언어가 지원됩니다. 두 언어가 음성 번역에 지원되는 경우 음성 - 음성 변환 또는 음성 - 텍스트 변환이 지원됩니다. 대상 언어가 음성 번역을 지원하지 않으면 음성 - 텍스트 변환만 지원됩니다. 

| 음성 언어    |
|:----------- |
| 아랍어(최신 표준)      |
| 중국어(북경어)      |
| 영어      |
| 프랑스어      |
| 독일어      |
| 이탈리아어      |
| 일본어      |
| 포르투갈어(브라질어)     |
| 러시아어      |
| 스페인어      | 

Translator Speech API는 다음 언어를 음성 텍스트 변환 번역에 대한 대상 언어로 지원합니다. 

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
|아이슬란드어|`is`          |
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

## <a name="access-the-list-programmatically"></a>목록에 프로그래밍 방식으로 액세스

언어 리소스를 사용하여 지원되는 언어 목록에 프로그래밍 방식으로 액세스할 수 있습니다. 목록에서는 영어 또는 지원되는 다른 언어의 언어 이름뿐만 아니라 언어 코드도 제공합니다. 이 목록은 새 언어를 사용할 수 있을 때 Translator Speech 서비스에서 자동으로 업데이트됩니다.

언어 리소스는 음성, 텍스트 및 텍스트 음성 변환에 지원되는 언어 목록을 반환합니다. 언어 리소스에는 인증이 필요하지 않습니다.

[API 참조를 방문하여 언어 메서드 사용](languages-reference.md)

## <a name="access-the-list-on-the-microsoft-translator-website"></a>Microsoft Translator 웹 사이트에서 목록에 액세스

언어를 빠르게 확인하려는 경우 Microsoft Translator 웹 사이트는Translator Text 및 Speech API에서 지원하는 모든 언어를 표시합니다. 이 목록에는 언어 코드와 같은 개발자별 정보가 포함되지 않습니다.

[언어 목록을 참조하세요](https://www.microsoft.com/translator/languages.aspx). 
