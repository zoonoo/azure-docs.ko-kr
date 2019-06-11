---
title: 썸네일 이미지 크기 조정 및 자르기 - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API의 응답에 포함된 썸네일 이미지의 크기를 조정하고 자릅니다.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: F4FFAE91-A003-4F7C-8E60-83A142485E28
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.author: scottwhi
ms.custom: seodec2018
ms.openlocfilehash: 3e005677f6a21c0f795f649f43407b55bec2a40a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66383579"
---
# <a name="resize-and-crop-thumbnail-images"></a>썸네일 이미지 크기 조정 및 자르기

검색 쿼리가 처리되는 즉시, Bing은 [응답](https://docs.microsoft.com/azure/cognitive-services/bing-image-search/concepts/bing-image-search-get-images#bing-image-search-response-format)의 모든 이미지에 대한 썸네일 정보를 생성합니다. 이 정보는 반환되는 썸네일을 모두 또는 하위 집합을 표시하는 데 사용할 수 있습니다. 하위 집합을 표시 하는 경우 나머지 이미지를 볼 수 있는 옵션을 제공 합니다.


<!-- Removing image until we can replace it with a sanatized version.
![Expanded view of thumbnail image](../bing-web-search/media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

사용자가 썸네일을 클릭하는 경우 [contentUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-contenturl)을 사용하여 사용자에게 전체 크기 이미지를 표시할 수 있습니다. 이미지 특성을 확인합니다.

`shoppingSourcesCount` 또는 `recipeSourcesCount`가 0보다 큰 경우 이미지의 항목에 대해 쇼핑이나 레시피가 존재하는 것을 나타내려면 썸네일에 쇼핑 카트 같은 배지를 추가합니다.

<!-- this is a sanitized version but we're removing all images for now until everything is sanitized.
![Shopping sources badge](./media/cognitive-services-bing-images-api/bing-images-shopping-source.PNG)
-->

이미지에서 인식된 사람 또는 이미지를 포함하는 웹 페이지 같이 이미지에 대한 인사이트를 얻으려면 [imageInsightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken)을 사용합니다. 자세한 내용은 [이미지 인사이트](image-insights.md)를 참조합니다.

## <a name="resizing-and-cropping-thumbnails"></a>썸네일 크기 조정 및 자르기

사용자가 커서를 썸네일 위로 이동하는 경우처럼 썸네일의 크기를 조정하고 확장할 수도 있습니다.
> [!NOTE]
> 확대하는 경우 이미지의 특성을 확인합니다. 예를 들어 [hostPageDisplayUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-hostpagedisplayurl)에서 호스트를 추출하여 이미지 아래에 표시하여 확인합니다.

[!INCLUDE [cognitive-services-bing-resize-crop-thumbnails](../../../includes/cognitive-services-bing-resize-crop-thumbnails.md)]
