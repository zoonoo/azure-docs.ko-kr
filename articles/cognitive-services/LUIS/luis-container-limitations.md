---
title: 컨테이너 제한 사항-LUIS
titleSuffix: Azure Cognitive Services
description: 지원 되는 LUIS 컨테이너 언어입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 2061d69fdfd13683ee722951cc7aaedcb1e1750a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745364"
---
# <a name="language-understanding-luis-container-limitations"></a>LUIS (Language Understanding) 컨테이너 제한 사항

LUIS 컨테이너에는 몇 가지 주목할 만한 제한 사항이 있습니다. 지원 되지 않는 종속성부터 지원 되는 언어의 하위 집합까지이 문서에서는 이러한 제한 사항에 대해 자세히 설명 합니다.

## <a name="supported-dependencies-for-latest-container"></a>컨테이너에 대해 지원 되는 종속성 `latest`

최신 LUIS 컨테이너는 다음을 지원 합니다.

* [새 미리](luis-reference-prebuilt-domains.md)작성 된 도메인: 이러한 엔터프라이즈 중심 도메인에는 엔터티, 예 길이 발언 및 패턴이 포함 됩니다. 사용자 용도에 맞게 이러한 도메인을 확장 합니다.

## <a name="unsupported-dependencies-for-latest-container"></a>컨테이너에 대해 지원 되지 않는 종속성 `latest`

[컨테이너에 대해 내보내려면](luis-container-howto.md#export-packaged-app-from-luis)LUIS 앱에서 지원 되지 않는 종속성을 제거 해야 합니다. 컨테이너에 대해 내보내기를 시도 하면 LUIS 포털에서 제거 해야 하는 지원 되지 않는 이러한 기능을 보고 합니다.

다음 종속성 중 하나도 **포함하지 않는** 경우 LUIS 애플리케이션을 사용할 수 있습니다.

지원되지 않는 앱 구성|세부 정보|
|--|--|
|지원되지 않는 컨테이너 문화권| 네덜란드어 ( `nl-NL` ), 일본어 ( `ja-JP` ) 및 독일어 ( `de-DE` ) 언어는 [1.0.2 토크](luis-language-support.md#custom-tokenizer-versions)에서 유일 하 게 지원 됩니다.|
|모든 문화권에 지원되지 않는 엔터티|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) 모든 문화권에 미리 빌드된 엔터티|
|지원 되지 않는 영어 ( `en-US` ) 문화권 엔터티|[GeographyV2](luis-reference-prebuilt-geographyV2.md) 미리 빌드된 엔터티|
|음성 초기화|컨테이너에서 외부 종속성은 지원되지 않습니다.|
|감정 분석|컨테이너에서 외부 종속성은 지원되지 않습니다.|
|Bing 맞춤법 검사|컨테이너에서 외부 종속성은 지원되지 않습니다.|

## <a name="languages-supported"></a>지원되는 언어

LUIS 컨테이너는 LUIS에서 [지 원하는 언어](luis-language-support.md#languages-supported) 의 하위 집합을 지원 합니다. LUIS 컨테이너는 길이 발언를 이해 하는 데 사용할 수 있는 언어는 다음과 같습니다.

| 언어 | Locale | 미리 빌드된 도메인 | 미리 빌드된 엔터티 | 구 목록 권장 사항 | **[텍스트 분석](../text-analytics/language-support.md)<br>(감정 및<br>키워드)|
|--|--|:--:|:--:|:--:|:--:|
| 영어(미국) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[중국어](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| 프랑스어(프랑스) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| 프랑스어(캐나다) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| 독일어 |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| 힌디어 | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| 이탈리아어 |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| 한국어 |`ko-KR` | ✔️ | ❌ | ❌ | *키 문구* 만 |
| 포르투갈어(브라질) |`pt-BR` | ✔️ | ✔️ | ✔️ | 일부 하위 문화권은 아님 |
| 스페인어(스페인) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| 스페인어(멕시코)|`es-MX` | ❌ | ❌ |✔️|✔️|
| 터키어 | `tr-TR` |✔️| ❌ | ❌ | *감정* 만 |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]