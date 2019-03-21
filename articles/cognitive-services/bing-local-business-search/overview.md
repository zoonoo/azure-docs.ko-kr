---
title: Bing Local Business Search API란? | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: Bing Local Business Search API는 애플리케이션에서 검색 쿼리를 기반으로 로컬 장소 및 비즈니스에 대한 정보를 찾을 수 있게 해주는 RESTful 서비스입니다.
services: cognitive-services
author: mikedodaro
manager: rosh
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: fb3b375a66ac9b05ad26287b7c39ce8e748ad699
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780323"
---
# <a name="what-is-bing-local-business-search"></a>Bing Local Business Search란?
Bing Local Business Search API는 애플리케이션에서 검색 쿼리를 기반으로 로컬 비즈니스에 대한 정보를 찾을 수 있게 해주는 RESTful 서비스입니다. 예를 들어 `q=<business-name> in Redmond, Washington` 또는 `q=Italian restaurants near me`입니다. 

## <a name="features"></a>기능
| 기능 | 설명 |  
| -- | -- | 
| [로컬 비즈니스 및 위치 찾기](quickstarts/local-quickstart.md) | Bing Local Business Search API는 쿼리에서 지역화된 결과를 가져옵니다. 결과에는 비즈니스 웹 사이트 URL과 표시 텍스트, 전화 번호 및 지리적 위치가 포함됩니다. GPS 좌표, 도시, 주소 |  
| [지리적 경계를 사용하여 로컬 결과 필터링](specify-geographic-search.md) | 검색 매개 변수로 좌표를 추가하여 원형 영역 또는 사각형 경계 상자로 지정된 특정 지리적 영역으로 결과를 제한합니다. | 
| [로컬 비즈니스 결과를 범주별로 필터링](local-categories.md) | 로컬 비즈니스 결과를 범주별로 검색합니다. 이 옵션은 호출자의 GPS 좌표 또는 역방향 IP 위치를 사용하여 다양한 비즈니스 범주에서 지역화된 결과를 반환합니다.|

## <a name="workflow"></a>워크플로
HTTP 요청을 수행하고 JSON 응답을 구문 분석할 수 있는 모든 프로그래밍 언어에서 Bing Local Business Search API를 호출합니다. 이 서비스는 REST API를 사용하여 액세스할 수 있습니다.
 
1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api).   
2. URL은 `q=""` 쿼리 매개 변수에 대한 검색어를 인코딩합니다. 예를 들면 `q=nearby+restaurant` 또는 `q=nearby%20restaurant`과 같습니다. 필요한 경우 페이지 매김도 설정합니다. 
3. [Bing Local Business Search API로 요청](quickstarts/local-quickstart.md)을 보냅니다. 
4. JSON 응답을 구문 분석합니다. 

> [!NOTE]
> 현재 Local Business Search는 `en-US` 시장만 지원합니다. 
> [!NOTE]
> 현재 Local Business Search는 Autosuggest를 지원하지 않습니다. 

## <a name="next-steps"></a>다음 단계
- [쿼리 및 응답](local-search-query-response.md)
- [Local Business Search 빠른 시작](quickstarts/local-quickstart.md)
- [Local Business Search API 참조](local-search-reference.md)
- [사용 및 표시 요구 사항](use-display-requirements.md)
