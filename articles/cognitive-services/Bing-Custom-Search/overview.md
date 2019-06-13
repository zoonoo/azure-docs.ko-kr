---
title: Bing Custom Search API란?
titlesuffix: Azure Cognitive Services
description: Bing Custom Search API를 사용하면 관심 있는 토픽에 대한 맞춤형 검색 경험을 만들 수 있습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: overview
ms.date: 03/4/2019
ms.author: aahi
ms.openlocfilehash: 93af1cce37c69691fb39f62270f4de6c9c73cf32
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388595"
---
# <a name="what-is-the-bing-custom-search-api"></a>Bing Custom Search API란?

Bing Custom Search API를 사용하면 관심 있는 토픽에 대한 맞춤형 광고 없는 검색 경험을 만들 수 있습니다. Bing으로 검색할 도메인 및 웹 페이지를 지정하고 특정 콘텐츠를 고정, 올리거나 내려 웹의 사용자 지정 보기를 만들고 사용자가 관련 검색 결과를 신속하게 찾을 수 있도록 합니다. 

## <a name="features"></a>기능

|기능  |설명  |
|---------|---------|
|[사용자 지정 실시간 검색 제안](define-custom-suggestions.md)     | 사용자 유형의 드롭다운 목록으로 표시할 수 있는 검색 제안을 제공합니다.       | 
|[사용자 지정 이미지 검색 환경](get-images-from-instance.md)     | 사용자가 사용자 지정 검색 인스턴스에 지정된 도메인 및 웹 사이트에서 이미지를 검색할 수 있습니다.        |        
|[사용자 지정 비디오 검색 환경](get-videos-from-instance.md)     | 사용자가 사용자 지정 검색 인스턴스에 지정된 도메인 및 사이트에서 비디오를 검색할 수 있습니다.        |    
|[사용자 지정 검색 인스턴스 공유](share-your-custom-search.md)     | 팀 멤버와 공유하여 검색 인스턴스를 공동으로 편집하고 테스트합니다.        | 
|[애플리케이션 및 웹 사이트에 대한 UI 구성](hosted-ui.md)     | 팀 멤버와 공유하여 검색 인스턴스를 공동으로 편집하고 테스트합니다.        | 
## <a name="workflow"></a>워크플로

[Bing Custom Search 포털](https://customsearch.ai)을 사용하여 사용자 지정된 검색 인스턴스를 만들 수 있습니다. 포털을 사용하면 Bing에서 검색 또는 검색하지 않으려는 도메인, 웹 사이트 및 웹 페이지를 지정하는 사용자 지정 검색 인스턴스를 만들 수 있습니다. 또한 포털을 사용하여 검색 환경을 미리 보고 API가 제공하는 검색 순위를 조정하며 필요할 경우 검색 가능한 사용자 인터페이스를 구성하여 웹 사이트 및 애플리케이션에서 렌더링할 수 있습니다.

검색 인스턴스를 만든 후 Bing Custom Search API를 호출하여 웹 사이트 또는 애플리케이션에 검색 인스턴스(및 필요할 경우 사용자 인터페이스)를 통합할 수 있습니다.

![API를 통해 Bing Custom Search에 연결할 수 있음을 보여 주는 이미지](media/BCS-Overview.png "Bing Custom Search 작동 방식")


## <a name="next-steps"></a>다음 단계

빠르게 시작하려면 [처음으로 Bing Custom Search 인스턴스 만들기](quick-start.md)를 참조하세요.

검색 인스턴스를 사용자 지정하는 방법에 대한 자세한 내용은 [사용자 지정 검색 인스턴스 정의](define-your-custom-view.md)를 참조하세요.

서비스 및 애플리케이션에서 결과를 사용하려면 [Bing 사용 및 표시 요구 사항](./use-and-display-requirements.md)을 반드시 읽어보세요.

각 사용자 지정 검색 엔드포인트에 대한 참조 콘텐츠를 숙지합니다. 참조에는 검색 결과를 요청하는 데 사용할 엔드포인트, 헤더 및 쿼리 매개 변수가 포함됩니다. 응답 개체의 정의도 포함됩니다.

- [Custom Search API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference)
- [Custom Image API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-images-api-v7-reference)
- [Custom Video API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-videos-api-v7-reference)
- [Custom Autosuggest API](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-autosuggest-api-v7-reference)

