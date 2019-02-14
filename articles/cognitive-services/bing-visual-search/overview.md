---
title: Bing Visual Search란?
titleSuffix: Azure Cognitive Services
description: Bing Visual Search는 유사한 이미지 또는 쇼핑 소스와 같은 이미지에 대한 세부 정보 또는 인사이트를 제공합니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: overview
ms.date: 04/10/2018
ms.author: scottwhi
ms.openlocfilehash: 9e323e9ec928d9f8b0592850adcd088a589ebf28
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55863094"
---
# <a name="what-is-the-bing-visual-search-api"></a>Bing Visual Search API란?

Bing Visual Search API는 Bing.com/images에 표시된 것에 대한 유사한 이미지 세부 정보를 제공합니다. 이미지를 업로드하거나 URL을 제공하여 이 API는 시각적으로 유사한 이미지, 쇼핑 소스, 이미지를 포함하는 웹 페이지 등을 포함하여 다양한 세부 정보를 식별할 수 있습니다. [Bing Image Search API](../bing-image-search/overview.md)를 사용하는 경우 이미지를 업로드하는 대신 API의 검색 결과에 연결된 인사이트 토큰을 사용할 수 있습니다.

## <a name="insights"></a>자세한 정보

다음은 Visual Search를 통해 검색할 수 있는 인사이트입니다.

| 인사이트                              | 설명 |
|--------------------------------------|-------------|
| 시각적으로 유사한 이미지              | 입력 이미지와 시각적으로 유사한 이미지 목록 |
| 시각적으로 유사한 제품            | 표시된 제품과 시각적으로 유사한 제품            |
| 쇼핑 소스                     | 입력 이미지에 표시된 항목을 구입할 수 있는 장소 목록            |
| 관련 검색                     | 다른 사람이 수행했거나 이미지 콘텐츠를 기반으로 하는 관련 검색 목록            |
| 이미지를 포함하는 웹 페이지     | 입력 이미지를 포함하는 웹 페이지            |
| 레시피                              | 입력 이미지에 표시된 요리를 만들기 위한 조리법이 포함된 웹 페이지            |

이러한 인사이트 외에도 Visual Search는 입력 이미지에서 파생된 다양한 용어(태그) 집합도 반환합니다. 이러한 태그를 사용하여 이미지에서 찾은 개념을 탐색할 수 있습니다. 예를 들어 입력 이미지가 유명 운동 선수인 경우 태그 중 하나는 운동 선수의 이름이고, 다른 태그는 스포츠일 수 있습니다. 또는 입력 이미지가 애플 파이인 경우 태그는 애플 파이, 파이, 디저트일 수 있으며, 사용자가 관련 개념을 탐색할 수 있습니다.

Visual Search 결과에는 이미지의 관심 영역에 대한 경계 상자도 포함됩니다. 예를 들어 이미지에 유명인이 여러 명 포함된 경우 결과에 이미지에서 인식된 각 유명인의 경계 상자가 포함될 수 있습니다. 또는 Bing이 이미지에서 제품 또는 의류를 인식하는 경우 결과에 인식된 제품 또는 의류 항목의 경계 상자가 포함될 수 있습니다.

> [!IMPORTANT]
> Bing Image Search API를 사용하여 이미지 인사이트를 가져오는 경우 더 포괄적인 인사이트를 제공하는 Bing Visual Search API로 전환하는 것이 좋습니다.

## <a name="workflow"></a>워크플로

Bing Visual Search API는 RESTful 웹 서비스이며, HTTP 요청을 수행하고 JSON을 구문 분석할 수 있는 프로그래밍 언어에서 쉽게 호출할 수 있습니다. REST API 또는 SDK를 사용하여 서비스를 사용할 수 있습니다.

1. Bing Search API에 대한 액세스 권한이 있는 Cognitive Services API 계정을 만듭니다. Azure 구독이 없는 경우 체험 계정을 만들 수 있습니다.
2. 유효한 검색 쿼리를 사용하여 API에 요청을 보냅니다.
3. 반환된 JSON 메시지를 구문 분석하여 API 응답을 처리합니다.


## <a name="next-steps"></a>다음 단계

먼저 Bing Image Search API [대화형 데모](https://azure.microsoft.com/services/cognitive-services/bing-visual-search/)를 사용해 보세요.
이 데모에서는 검색 쿼리를 빠르게 사용자 지정하고 웹에서 이미지를 샅샅이 검색하는 방법을 보여 줍니다.

API를 호출할 준비가 되면 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [무료 계정](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api)을 만들 수 있습니다.

첫 번째 요청으로 신속하게 시작하려면 빠른 시작: [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)을 참조하세요.


## <a name="see-also"></a>참고 항목

* [Bing Visual Search 참조](https://docs.microsoft.com/rest/api/cognitiveservices/bingvisualsearch/images/visualsearch) 문서에는 이미지 기반 검색 결과를 요청하는 데 사용할 수 있는 엔드포인트, 헤더, API 응답 및 쿼리 매개 변수에 대한 정의 및 정보가 있습니다.

* [Bing 사용 및 표시 요구 사항](./use-and-display-requirements.md)에서는 Bing 검색 API를 통해 획득한 콘텐츠와 정보의 허용 가능한 용도를 지정하고 있습니다.
