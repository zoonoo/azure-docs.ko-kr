---
title: Bing 인사이트의 예 - Bing Visual Search
titleSuffix: Azure Cognitive Services
description: Bing.com에 표시된 이미지 인사이트의 예를 보여 줍니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: scottwhi
ms.openlocfilehash: 94f8d4ea98f42ad919597ef53dc63281825f0e6b
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/10/2019
ms.locfileid: "59470137"
---
# <a name="examples-of-bing-insights-usage"></a>Bing 인사이트 사용 예

이 문서에는 Bing이 사용하고 Bing.com에 이미지 인사이트를 표시할 수 있는 방법의 예제가 포함되어 있습니다.

## <a name="pagesincluding-insight-example"></a>PagesIncluding 인사이트 예제

다음 링크는 첫 번째 웹 페이지를 표시 하 고 사용자를 확장 하 고 이미지를 포함 하는 다른 웹 페이지의 목록을 축소:

![다음과 같은 확장된 페이지](./media/pages-including.PNG)

## <a name="shoppingsources-insight-example"></a>ShoppingSources 인사이트 예제

다음 Bing 쇼핑 원본 이미지에 표시 되는 제품에 대 한 표시 될 수 있습니다 하는 방법을 보여 줍니다.

![쇼핑 소스](./media/shopping-sources.PNG)

## <a name="visualsearch-insight-example"></a>VisualSearch 인사이트 예제

다음 Bing에서 시각적으로 유사한 이미지를 표시할 수는 방법을 보여 줍니다 (참조 **관련 이미지** 예제에서):

![시각적으로 유사한 이미지](./media/similar-images.PNG)

## <a name="recipes-insight-example"></a>Recipes 인사이트 예제

다음은 Bing이 이미지에 표시된 음식의 조리법을 표시할 수 있는 방법을 보여 줍니다. 예제 사용자를 알고 있는 작성법을 사용할 수 있습니다.

![다음과 같은 조리법 및 페이지](./media/recipes-pages-including.PNG)

 사용자는 목록을 확장 하는 경우 작성법에 대 한 링크를 제공 합니다.

![다음과 같은 확장된 조리법 페이지](./media/expanded-recipes-pages-including.PNG)

## <a name="relatedsearches-insight-example"></a>RelatedSearches 인사이트 예제

다음은 Bing이 다른 사용자가 수행한 이미지의 관련 검색을 표시할 수 있는 방법을 보여 줍니다. 사용자가 이미지를 클릭하면 해당 관련 쿼리에 대한 Bing.com/Images 검색 결과 페이지로 이동됩니다.

![이미지의 관련 검색](./media/bordered-related-searches.PNG)

## <a name="entity-insight-example"></a>Entity 인사이트 예제

다음은 Bing이 이미지에 표시된 엔터티(사람, 장소 또는 사물)에 대한 정보를 표시할 수 있는 방법을 보여 줍니다. 사용자가 엔터티 링크를 클릭 하면 사용자는 엔터티에 대 한 Bing.com 검색 결과 페이지로 이동:

![이미지에 표시된 엔터티](./media/entity.PNG)

## <a name="displaying-other-insights-that-the-user-might-explore"></a>사용자가 탐색할 수 있는 다른 인사이트 표시

다음은 Bing이 사용자가 탐색할 수 있는 이미지에 대한 기타 정보를 표시할 수 있는 방법을 보여 줍니다.

![이미지에 대한 다른 인사이트 탐색](./media/apple-pie-more-tags.PNG)

## <a name="bounding-boxes-and-hot-spots"></a>경계 상자 및 핫 스폿

비기본 태그에는 태그가 적용되는 이미지의 관심 영역을 식별하는 경계 상자가 포함됩니다. 경계 상자가 전체 이미지를 식별하지 않는 경우, 경계 상자를 사용하여 이미지에 핫 스폿을 만듭니다. 사용자는 핫 스폿을 클릭하여 핫 스폿(또는 사각형)에서 찾은 콘텐츠와 관련된 정보를 가져올 수 있습니다. 예를 들어, 높은 방식으로 이미지를 이미지 이면 결과 및 포함할 수 있습니다 태그 (경계 상자) accessories에 대 한 이미지에 표시 된를 지갑, 보석, scarfs, 등입니다. 다음 예제에서는 이미지에서 보여준 선글라스에 대 한 핫 스폿 사각형을 보여 줍니다.

![경계 상자 및 핫 스폿](./media/click-to-search.PNG)

## <a name="next-steps"></a>다음 단계

첫 번째 요청을 사용 하 여 시작 하려면 빠른 시작을 참조 하세요. [C#](quickstarts/csharp.md) | [Java](quickstarts/java.md) | [node.js](quickstarts/nodejs.md) | [Python](quickstarts/python.md)을 참조하세요.





