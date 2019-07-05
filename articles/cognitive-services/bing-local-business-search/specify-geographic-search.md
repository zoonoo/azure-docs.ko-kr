---
title: 지리적 경계를 사용하여 Bing Local Business Search API에서 결과를 필터링 | Microsoft Docs
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 Bing Local Business Search API에서 검색 결과를 필터링하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: article
ms.date: 11/01/2018
ms.author: rosh, v-gedod
ms.openlocfilehash: e87ef96184974d10f99028be13399d1422a1c774
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65796589"
---
# <a name="use-geographic-boundaries-to-filter-results-from-the-bing-local-business-search-api"></a>지리적 경계를 사용하여 Bing Local Business Search API에서 결과를 필터링

Bing Local Business Search API를 사용하면 `localCircularView` 또는 `localMapView` 쿼리 매개 변수를 사용하여 검색하려는 특정 지리적 영역에서 경계를 설정할 수 있습니다. 쿼리에서 하나의 매개 변수만 사용해야 합니다. 

검색어에 명시적인 지리적 위치가 포함되는 경우 Bing Local Business API는 자동으로 해당 위치를 사용하여 검색 결과에 대한 경계를 설정합니다. 예를 들어, 검색어가 `sailing in San Diego`인 경우 `San Diego`가 위치로 사용되고 쿼리 매개 변수 또는 사용자 헤더의 지정된 다른 위치는 무시됩니다. 

지리적 위치가 검색어에서 검색되지 않고 쿼리 매개 변수를 사용하여 지정된 지역적 위치가 없는 경우 Bing Local Business Search API는 요청의 `X-Search-ClientIP` 또는 `X-Search-Location` 헤더에서 위치를 확인하려고 시도합니다. 헤더가 모두 지정되지 않은 경우 API는 요청의 클라이언트 IP 또는 모바일 디바이스의 GPS 좌표에서 위치를 확인입니다.

## <a name="localcircularview"></a>localCircularView

`localCircularView` 매개 변수는 반경으로 정의된 위도/경도 좌표의 집합 주위에 원형 지리적 영역을 만듭니다. 이 매개 변수를 사용하면 검색 지역을 약간 벗어난 지역을 포함할 수 있는 `localMapView` 매개 변수와 달리, Bing Local Business Search API의 응답에는 이 원 내의 위치만 포함됩니다.

원형 지리적 검색 영역을 지정하려면 원의 중앙이 되는 위도와 경도 및 미터 단위의 반경을 선택합니다. 그런 다음, 이 매개 변수를 쿼리 문자열에 추가할 수 있습니다(예: `q=Restaurants&localCircularView=47.6421,-122.13715,5000`).

쿼리를 완료합니다.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localCircularView=47.6421,-122.13715,5000&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="localmapview"></a>localMapView

`localMapView` 매개 변수는 검색할 사각형 지리적 영역을 지정합니다. 두 가지 좌표 집합을 사용하여 해당 남동쪽 및 북서쪽 모퉁이를 지정합니다. 이 매개 변수를 사용하면 검색 지역 내 위치만 포함하는 `localCircularView` 매개 변수와 달리, Bing Local Business Search API의 응답에는 지정된 영역 내부 및 외부 위치가 포함될 수 있습니다.

사각형 검색 영역을 지정하려면 경계의 남동쪽 및 북서쪽 모퉁이가 되는 위도/경도 좌표를 선택합니다. 다음 예제와 같이 동남쪽 좌표를 먼저 정의해야 합니다. `localMapView=47.619987,-122.181671,47.6421,-122.13715`

쿼리를 완료합니다.

```
https://api.cognitive.microsoft.com/bing/v7.0/localbusinesses/search?q=restaurant&localMapView=47.619987,-122.181671,47.6421,-122.13715&appid=0123456789ABCDEF&mkt=en-us&form=monitr
```

## <a name="next-steps"></a>다음 단계
- [Local Business Search Java 빠른 시작](quickstarts/local-search-java-quickstart.md)
- [Local Business Search C# 빠른 시작](quickstarts/local-quickstart.md)
- [Local Business Search Node 빠른 시작](quickstarts/local-search-node-quickstart.md)
- [Local Business Search Python 빠른 시작](quickstarts/local-search-python-quickstart.md)
