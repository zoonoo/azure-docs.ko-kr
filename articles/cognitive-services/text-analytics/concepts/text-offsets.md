---
title: 텍스트 분석 API의 텍스트 오프셋
titleSuffix: Azure Cognitive Services
description: 다국어 및 이모티콘 인코딩으로 인한 오프셋에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79221192"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>텍스트 분석 API 출력의 텍스트 오프셋

다국어 및 이모티콘 지원으로 인해 유니코드 인코딩은 두 개 이상의 [코드 포인트를](https://wikipedia.org/wiki/Code_point) 사용하여 그래프라고 하는 단일 표시된 문자를 나타냅니다. 예를 들어 🌷 같은 👍 이모티콘은 여러 문자를 사용하여 피부톤과 같은 시각적 특성에 대한 추가 문자로 모양을 구성할 수 있습니다. 마찬가지로 힌디어 단어는 `अनुच्छेद` 다섯 글자와 세 개의 결합 마크로 인코딩됩니다.

가능한 다국어 및 이모티콘 인코딩의 길이가 다르기 때문에 텍스트 분석 API는 응답에서 오프셋을 반환할 수 있습니다.

## <a name="offsets-in-the-api-response"></a>API 응답의 오프셋입니다. 

오프셋이 반환될 때마다 [명명된 엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md) 또는 [감정 분석과](../how-tos/text-analytics-how-to-sentiment-analysis.md)같은 API 응답은 다음을 기억합니다.

* 응답의 요소는 호출된 끝점에 따라 달라질 수 있습니다. 
* HTTP POST/GET 페이로드는 [UTF-8로](https://www.w3schools.com/charsets/ref_html_utf8.asp)인코딩되며, 이는 클라이언트 측 컴파일러 또는 운영 체제의 기본 문자 인코딩일 수도 있고 아닐 수도 있습니다.
* 오프셋은 문자 수가 아닌 [유니코드 8.0.0](https://unicode.org/versions/Unicode8.0.0) 표준을 기반으로 하는 그래핀 수를 참조합니다.

## <a name="extracting-substrings-from-text-with-offsets"></a>오프셋이 있는 텍스트에서 하위 문자열 추출

오프셋은 문자 기반 하위 문자열 메서드(예: .NET [substring()](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8) 메서드를 사용할 때 문제가 발생할 수 있습니다. 한 가지 문제는 오프셋으로 인해 하위 문자열 메서드가 끝이 아닌 다중 문자 그래핀 인코딩 중간에 끝날 수 있다는 것입니다.

.NET에서 [StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8) 클래스를 사용 하 여 개별 문자 개체 가 아니라 일련의 텍스트 요소로 문자열을 사용 하는 것이 좋습니다. 원하는 소프트웨어 환경에서 그래핀 스플리터 라이브러리를 찾을 수도 있습니다. 

텍스트 분석 API는 편의를 위해 이러한 텍스트 요소도 반환합니다.

## <a name="see-also"></a>참조

* [Text Analytics 개요](../overview.md)
* [감정 분석](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [엔터티 인식](../how-tos/text-analytics-how-to-entity-linking.md)
* [언어 검색](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [언어 인식](../how-tos/text-analytics-how-to-language-detection.md)
