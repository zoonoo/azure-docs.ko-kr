---
title: Azure Maps를 사용하여 검색 결과 표시 | Microsoft Docs
description: Azure Maps로 검색 요청을 수행한 후 Javascrip 지도에 결과를 표시하는 방법
author: jingjing-z
ms.author: jinzh
ms.date: 08/26/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen
ms.openlocfilehash: 540faa04573f7fa66b574f6743d42adfe855c571
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2018
ms.locfileid: "43095258"
---
# <a name="show-search-results-on-the-map"></a>지도에 검색 결과 표시

이 문서에서는 관심 있는 위치를 검색하고 지도에 검색 결과를 표시하는 방법을 보여줍니다. 

## <a name="understand-the-code"></a>코드 이해

<iframe height='305' scrolling='no' title='지도에 검색 결과 표시(서비스 모듈)' src='//codepen.io/azuremaps/embed/zLdYEB/?height=265&theme-id=0&default-tab=js,result&embed-version=2' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)로 펜 <a href='https://codepen.io/azuremaps/pen/zLdYEB/'>지도에 검색 결과 표시</a>(서비스 모듈)를 참조하세요.
</iframe>

위의 코드에서 첫 번째 코드 블록은 맵 개체를 생성하고 클라이언트 서비스를 인스턴스화합니다. 지침은 [지도 만들기](./map-create.md)를 참조하세요.

두 번째 코드 블록은 유사 항목 검색 [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)를 사용하여 관심 지점을 검색합니다. 유사 항목 검색 API는 모든 조합의 유사 항목 입력을 처리할 수 있습니다. 유사 항목 검색 서비스의 응답이 GeoJSON 형식으로 구문 분석되고 관심 지점을 지도에 표시하도록 맵에 핀이 추가됩니다. 

마지막 코드 블록은 Map의 [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-control/models.cameraboundsoptions?view=azure-iot-typescript-latest) 속성을 사용하여 지도에 대한 카메라 한도를 추가합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다. 

* [Azure Maps 유사 항목 검색 API](https://docs.microsoft.com/rest/api/maps/search/getsearchfuzzy)
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.map?view=azure-iot-typescript-latest#addpins)
    
맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요. 
* [좌표에서 정보 가져오기](./map-get-information-from-coordinate.md)
* [A에서 B로의 방향 표시](./map-route.md)
