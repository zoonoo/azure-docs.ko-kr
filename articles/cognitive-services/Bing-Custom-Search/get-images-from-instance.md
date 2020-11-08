---
title: 사용자 지정 보기에서 이미지 가져오기 - Bing Custom Search
titleSuffix: Azure Cognitive Services
description: Bing Custom Search를 사용하여 웹의 사용자 지정 보기에서 이미지를 가져오는 방법에 대한 간략한 개요입니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: c29e83f7851bc9be1688f3828373942c0e31f4ef
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2020
ms.locfileid: "94367474"
---
# <a name="get-images-from-your-custom-view"></a>사용자 지정 보기에서 이미지 가져오기

> [!WARNING]
> Bing Search API Cognitive Services에서 Bing Search 서비스로 이동 합니다. **2020 년 10 월 30 일부 터** [여기](https://aka.ms/cogsvcs/bingmove)에 설명 된 프로세스에 따라 Bing Search의 새 인스턴스를 프로 비전 해야 합니다.
> Cognitive Services를 사용 하 여 프로 비전 된 Bing Search API는 향후 3 년 동안 또는 기업계약 종료 될 때까지 먼저 발생 합니다.
> 마이그레이션 지침은 [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)를 참조 하십시오.

Bing Custom Images Search를 통해 이미지를 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 웹 결과와 마찬가지로 사용자 지정 검색은 인스턴스의 웹 사이트 목록에서 이미지 검색을 지원합니다. Bing Custom Images Search API를 사용하거나 호스트된 UI 기능을 통해 이미지를 가져올 수 있습니다. 호스트된 UI 기능은 간단하게 사용할 수 있고 검색 환경을 빨리 시작하고 실행할 수 있는 좋은 방법입니다.  이미지를 포함하도록 호스트된 UI를 구성하는 방법에 대한 자세한 내용은 [호스트된 UI 환경 구성](hosted-ui.md)을 참조하세요.

검색 결과 표시 방법을 더 구체적으로 제어하려면 Bing의 Custom Images Search API를 사용하면 됩니다. API 호출은 Bing Image Search API 호출과 유사하므로 API를 호출하는 예제를 보려면 [Bing Image Search](../Bing-Image-Search/overview.md)를 구매하세요. 하지만 구매하기 전에 [Custom Images Search API 참조](/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference) 콘텐츠를 숙지합니다. 기본 차이점은 지원되는 쿼리 매개 변수(customConfig 쿼리 매개 변수를 포함해야 함) 및 요청을 보낼 엔드포인트입니다.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->