---
title: Bing Local Business Search API란?
titleSuffix: Azure Cognitive Services
description: Bing Local Business Search API는 애플리케이션에서 검색 쿼리를 기반으로 로컬 장소 및 비즈니스에 대한 정보를 찾을 수 있게 해주는 RESTful 서비스입니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: overview
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: 6b715d4b801159b451957c30ee9ef7b406f3a2f4
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095376"
---
# <a name="what-is-bing-local-business-search"></a>Bing Local Business Search란?

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](https://aka.ms/cogsvcs/bingmove)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](https://aka.ms/cogsvcs/bingmigration)를 참조하세요.
Bing Local Business Search API는 애플리케이션에서 검색 쿼리를 기반으로 로컬 비즈니스에 대한 정보를 찾을 수 있게 해주는 RESTful 서비스입니다. 예를 들어 `q=<business-name> in Redmond, Washington` 또는 `q=Italian restaurants near me`입니다. 

## <a name="features"></a>기능
| 기능 | Description |  
| -- | -- | 
| [로컬 비즈니스 및 위치 찾기](quickstarts/local-quickstart.md) | Bing Local Business Search API는 쿼리에서 지역화된 결과를 가져옵니다. 결과에는 비즈니스 웹 사이트 URL과 표시 텍스트, 전화 번호 및 지리적 위치가 포함됩니다. GPS 좌표, 도시, 주소 |  
| [지리적 경계를 사용하여 로컬 결과 필터링](specify-geographic-search.md) | 검색 매개 변수로 좌표를 추가하여 원형 영역 또는 사각형 경계 상자로 지정된 특정 지리적 영역으로 결과를 제한합니다. | 
| [로컬 비즈니스 결과를 범주별로 필터링](local-categories.md) | 로컬 비즈니스 결과를 범주별로 검색합니다. 이 옵션은 호출자의 GPS 좌표 또는 역방향 IP 위치를 사용하여 다양한 비즈니스 범주에서 지역화된 결과를 반환합니다.|

## <a name="workflow"></a>워크플로
HTTP 요청을 수행하고 JSON 응답을 구문 분석할 수 있는 모든 프로그래밍 언어에서 Bing Local Business Search API를 호출합니다. 이 서비스는 REST API를 사용하여 액세스할 수 있습니다.
 
1. Bing Search API에 대한 액세스 권한이 있는 [Cognitive Services API 계정](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)을 만듭니다. Azure 구독이 없는 경우 [체험 계정을 만들 수 있습니다](https://azure.microsoft.com/free/cognitive-services/).   
2. URL은 `q=""` 쿼리 매개 변수에 대한 검색어를 인코딩합니다. 예를 들어 `q=nearby+restaurant` 또는 `q=nearby%20restaurant`입니다. 필요한 경우 페이지 매김도 설정합니다. 
3. [Bing Local Business Search API로 요청](quickstarts/local-quickstart.md)을 보냅니다. 
4. JSON 응답 구문 분석 

> [!NOTE]
> 현재 Local Business Search: 
> * `en-US` 시장만 지원합니다. 
> * Bing Autosuggest를 지원하지 않습니다. 

## <a name="next-steps"></a>다음 단계
- [쿼리 및 응답](local-search-query-response.md)
- [Local Business Search 빠른 시작](quickstarts/local-quickstart.md)
- [Local Business Search API 참조](local-search-reference.md)
- [사용 및 표시 요구 사항](use-display-requirements.md)
