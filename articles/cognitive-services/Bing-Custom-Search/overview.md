---
title: Bing Custom Search란?
titlesuffix: Azure Cognitive Services
description: Bing Custom Search에 대한 개괄적인 개요를 제공합니다.
services: cognitive-services
author: brapel
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-custom-search
ms.topic: overview
ms.date: 09/29/2017
ms.author: v-brapel
ms.openlocfilehash: f2946918f0c1a7a516788989042825e8f49d7b0b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49318727"
---
# <a name="what-is-bing-custom-search"></a>Bing Custom Search란?

Bing Custom Search를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다. 예를 들어 검색 환경을 제공하는 웹 사이트를 소유하고 있는 경우 Bing에서 검색하는 도메인, 웹 사이트 및 웹 페이지를 지정할 수 있습니다. 사용자는 관련 없는 콘텐츠가 포함된 검색 결과를 페이징할 필요 없이 관심 있는 콘텐츠에 맞게 조정된 검색 결과를 보게 됩니다.

웹의 사용자 지정 보기를 만들려면 Bing Custom Search [포털](https://customsearch.ai)을 사용합니다. 포털을 사용하면 Bing에서 검색하려는 도메인, 웹 사이트 및 웹 페이지와 Bing에서 검색하지 않으려는 웹 사이트를 지정하는 사용자 지정 검색 인스턴스를 만들 수 있습니다. 알고 있는 콘텐츠의 URL을 지정하는 것 외에도 포털을 사용하여 추가할 수 있는 관련 콘텐츠를 찾을 수 있습니다.

또한 포털을 사용하면 사용자가 특정 검색어를 입력할 경우 검색 결과의 맨 위에 특정 웹 페이지를 고정할 수 있습니다. 

인스턴스를 정의한 후 Custom Search API를 호출하여 사용자 지정 검색을 웹 사이트, 데스크톱 앱 또는 모바일 앱에 통합할 수 있습니다. 웹 기반 사이트 또는 응용 프로그램이 있는 경우 호스트된 UI가 검색 인터페이스를 렌더링하도록 할 수 있습니다.

다음 이미지는 사용자 지정 검색을 간단하게 통합할 수 있음을 보여 줍니다.

![대체 그림](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/cognitive-services/Bing-Custom-Search/media/BCS-Overview.png "Bing Custom Search의 작동 방식")

## <a name="adding-custom-search-box-suggestions"></a>사용자 지정 검색 상자 제안 추가

사용자 지정 검색 상자 제안을 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 이 기능을 사용하면 검색 환경과 관련된 사용자 지정 검색 제안을 제공할 수 있습니다. 사용자가 검색 상자에서 입력할 때 드롭다운 목록에는 사용자의 부분 쿼리 문자열을 기반으로 제안된 쿼리 문자열이 포함됩니다. 사용자 지정 제안만 반환할지 또는 Bing 제안도 포함할지를 지정할 수 있습니다. [자세히 알아보기](define-custom-suggestions.md).

## <a name="adding-custom-image-search-experience"></a>사용자 지정 이미지 검색 환경 추가

이미지를 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 웹 결과와 마찬가지로 사용자 지정 검색은 인스턴스의 웹 사이트 목록에서 이미지 검색을 지원합니다. [자세히 알아보기](get-images-from-instance.md).

## <a name="adding-custom-video-search-experience"></a>사용자 지정 비디오 검색 환경 추가

비디오를 사용하여 향상된 사용자 지정 검색 환경을 제공할 수 있습니다. 웹 결과와 마찬가지로 사용자 지정 검색은 인스턴스의 웹 사이트 목록에서 비디오 검색을 지원합니다. [자세히 알아보기](get-videos-from-instance.md).

## <a name="sharing-your-custom-search-instance-with-others"></a>사용자 지정 검색 인스턴스를 다른 사용자와 공유

팀 구성원들과 인스턴스를 공유하여 인스턴스 공동 편집 및 테스트를 쉽게 허용할 수 있습니다. [자세히 알아보기](share-your-custom-search.md).

## <a name="next-steps"></a>다음 단계

빠르게 시작하려면 [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.

검색 인스턴스를 사용자 지정하는 방법에 대한 자세한 내용은 [사용자 지정 검색 인스턴스 정의](define-your-custom-view.md)를 참조하세요.

각 사용자 지정 검색 엔드포인트에 대한 참조 콘텐츠를 숙지합니다. 참조에는 검색 결과를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수가 포함됩니다. 응답 개체의 정의도 포함됩니다.

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices/bing-custom-autosuggest-api-v7-reference)


검색 결과 사용에 대한 어떤 규칙도 위반하지 않도록 [Bing 사용 및 표시 요구 사항](./use-and-display-requirements.md)을 반드시 읽도록 합니다.
