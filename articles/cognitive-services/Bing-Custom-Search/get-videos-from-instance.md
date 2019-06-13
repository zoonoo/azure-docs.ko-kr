---
title: 사용자 지정 보기에서 비디오 가져오기 - Bing Custom Search
titlesuffix: Azure Cognitive Services
description: Bing Custom Search를 사용하여 웹의 사용자 지정 보기에서 비디오를 가져오는 방법에 대한 간략한 개요입니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: scottwhi
ms.openlocfilehash: 9ddf89ab0c4c14743206b2baf207ec10fee033b4
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390312"
---
# <a name="get-videos-from-your-custom-view"></a>사용자 지정 보기에서 비디오 가져오기

Bing Custom Videos Search를 통해 비디오를 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 웹 결과와 마찬가지로 사용자 지정 검색은 인스턴스의 웹 사이트 목록에서 비디오 검색을 지원합니다. Bing Custom Videos Search API를 사용하거나 호스트된 UI 기능을 통해 비디오를 가져올 수 있습니다. 호스트된 UI 기능은 간단하게 사용할 수 있고 검색 환경을 빨리 시작하고 실행할 수 있는 좋은 방법입니다. 비디오를 포함하도록 호스트된 UI를 구성하는 방법에 대한 자세한 내용은 [호스트된 UI 환경 구성](hosted-ui.md)을 참조하세요.

검색 결과 표시 방법을 더 구체적으로 제어하려면 Bing의 Custom Videos Search API를 사용하면 됩니다. API 호출은 Bing Video Search API 호출과 유사하므로 API를 호출하는 예제를 보려면 [Bing Video Search](../Bing-Video-Search/search-the-web.md)를 구매하세요. 하지만 구매하기 전에 [Custom Videos Search API 참조](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference) 콘텐츠를 숙지합니다. 기본 차이점은 지원되는 쿼리 매개 변수(customConfig 쿼리 매개 변수를 포함해야 함) 및 요청을 보낼 엔드포인트입니다.

<!--
## Next steps

[Call your custom view](search-your-custom-view.md)
-->
