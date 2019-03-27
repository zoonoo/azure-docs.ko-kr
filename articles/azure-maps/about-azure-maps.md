---
title: Azure Maps 개요 | Microsoft Docs
description: Azure Maps 소개
author: walsehgal
ms.author: v-musehg
ms.date: 02/04/2019
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 023f5ed43453d7fb20dec5d91651230aa8fb4370
ms.sourcegitcommit: dd1a9f38c69954f15ff5c166e456fda37ae1cdf2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/07/2019
ms.locfileid: "57571306"
---
# <a name="what-is-azure-maps"></a>Azure Maps란?

Azure Maps는 최신 매핑 데이터를 사용하여 웹 및 모바일 애플리케이션에 정확한 지리적 컨텍스트를 제공하는 지리 공간적 서비스 컬렉션입니다. Azure Maps는 여러 스타일의 **맵** 및 위성 이미지를 렌더링하고, 전 세계의 주소, 장소 및 관심 지점을 **검색**하고, 포인트 간, 다중 포인트, 다중 포인트 최적화, 등시선, 상용 자동차, 교통 영향, 매트릭스 경로를 **라우팅**하고, 최적의 교통 흐름 및 인시던트를 살펴보고, **지리적 위치**를 통해 사용자 위치를 설정하고, 위치를 **표준 시간대**로 변환하고, 위치에서 시간을 가져오는 여러 REST API로 구성됩니다. 또한 Azure Maps는 **지오펜싱**, Azure에 위치 정보를 호스팅하는 맵 **데이터** 스토리지, 지리 공간적 분석을 통해 위치 인텔리전스를 제공하는 **공간 작업**을 위한 서비스를 제공합니다. Azure Maps 서비스는 REST API로 직접 사용할 수도 있고 강력한 **Web SDK** 또는 **Android SDK**를 통해 사용할 수도 있습니다. 개발자는 Azure 클라우드 내에서 이러한 도구를 사용하여 위치 정보를 Azure 솔루션에 통합하는 솔루션을 신속하게 개발하고 크기를 조정할 수 있습니다. 지금 바로 [Azure Maps 체험 계정](https://azure.microsoft.com/services/azure-maps/)에 가입하여 개발을 시작하세요!

다음 비디오는 Azure Maps를 자세히 설명합니다.

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>맵 컨트롤

### <a name="web-sdk"></a>웹 SDK

Azure Maps 웹 SDK를 사용하면 웹 또는 모바일 애플리케이션에 표시할 자체 콘텐츠와 이미지를 통해 대화형 지도를 사용자 지정할 수 있습니다. 이 컨트롤을 통해 WebGL을 사용하여 성능이 높은 대형 데이터 집합을 렌더링할 수 있습니다. JavaScript 또는 TypeScript를 사용하여 SDK에서 개발합니다.

![Azure Maps 웹 SDK](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK를 사용하여 강력한 모바일 매핑 애플리케이션을 만들 수 있습니다. 

![Azure Maps Android SDK](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Maps의 서비스

Azure Maps는 Azure 애플리케이션에 지리적 컨텍스트를 제공할 수 있는 다음과 같은 6개의 서비스로 구성됩니다.

### <a name="render-service"></a>Render Service

Render Service는 개발자가 매핑을 중심으로 웹 및 모바일 애플리케이션을 만들 수 있도록 설계되었습니다. 이 서비스는 19단계 확대/축소 수준에서 사용할 수 있는 고품질 래스터 그래픽 이미지 또는 모든 것을 사용자 지정할 수 있는 벡터 형식 맵 이미지를 사용합니다.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

Render Service는 현재 개발자가 위성 이미지를 사용하여 작업할 수 있도록 미리 보기 API를 제공합니다. 자세한 내용은 [Azure Maps Render API](https://docs.microsoft.com/rest/api/maps/render)를 참조하세요.

### <a name="route-service"></a>Route Service

Route Service는 여러 운송 모드의 실제 인프라 및 방향에 대한 강력한 기하 도형 계산을 포함합니다. 이 서비스는 개발자가 다양한 이동 모드(예: 자동차, 트럭, 자전거 또는 도보)에서 방향을 계산할 수 있도록 합니다. 또한 교통 상황, 무게 제한 또는 위해 물질 운송 등의 입력도 고려할 수 있습니다.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

Route Service는 현재 여러 경로 요청의 일괄 처리, 이동 시간과 출발지와 도착지 집합 간 거리의 행렬, 시간 또는 연료 요구 사항에 따라 이동할 수 있는 경로 또는 거리 찾기와 같은 고급 기능의 미리 보기를 제공합니다. 라우팅 기능에 대한 자세한 내용은 [Azure Maps Route API](https://docs.microsoft.com/rest/api/maps/route)를 참조하세요.

### <a name="search-service"></a>Search 서비스

Search Service는 개발자가 주소, 위치, 비즈니스 목록을 이름 또는 범주와 기타 지리 정보 기준으로 검색할 수 있도록 설계되었습니다. 또한 Search Service는 위도 및 경도를 기반으로 주소 및 교차로를 [역방향으로 지오코딩](https://en.wikipedia.org/wiki/Reverse_geocoding)할 수 있습니다.

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

Search Service는 경로에 따른 검색, 더 넓은 영역 내 검색, 검색 요청의 그룹 일괄 처리 및 위치 지점 대신 더 큰 영역 검색과 같은 고급 기능도 제공합니다. 일괄 처리 및 영역 검색에 대한 API는 현재 미리 보기에 있습니다. 검색 기능에 대한 자세한 내용은 [Azure Maps Search API](https://docs.microsoft.com/rest/api/maps/search) 페이지를 참조하세요.

### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service를 사용하면 위도-경도 쌍 또는 [IANA ID](https://www.iana.org/)를 사용하여 현재, 과거 및 미래의 표준 시간대 정보를 쿼리할 수 있습니다. 또한 Time Zone 서비스는 Microsoft Windows 표준 시간대 ID를 IANA 표준 시간대로 변환하고, 표준 시간대 오프셋을 UTC로 페치하여 해당 표준 시간대의 현재 시간을 가져올 수 있습니다. Time Zone Service에 대한 쿼리의 일반적인 JSON 응답은 다음 샘플과 같습니다.

```JSON
{
    "Version": "2017c",
    "ReferenceUtcTimestamp": "2017-11-20T23:09:48.686173Z",
    "TimeZones": [{
        "Id": "America/Los_Angeles",
        "ReferenceTime": {
            "Tag": "PST",
            "StandardOffset": "-08:00:00",
            "DaylightSavings": "00:00:00",
            "WallTime": "2017-11-20T15:09:48.686173-08:00",
            "PosixTzValidYear": 2017,
            "PosixTz": "PST+8PDT,M3.2.0,M11.1.0"
        }
    }]
}
```

이 서비스에 대한 자세한 내용은 [Azure Maps Timezone API](https://docs.microsoft.com/rest/api/maps/timezone) 페이지를 방문하세요.

### <a name="traffic-service"></a>Traffic Service

Traffic Service는 개발자가 트래픽을 요구하는 웹 및 모바일 애플리케이션을 만들 수 있도록 설계된 웹 서비스 제품군입니다. 이 서비스는 다음 두 가지 데이터 형식을 제공합니다.

* Traffic Flow - 네트워크의 모든 주요 도로에 대해 실시간으로 관찰된 속도와 여행 시간 정보
* Traffic Incidents - 도로 네트워크와 관련된 교통 혼잡 및 사고에 대한 정확한 보기

![Azure Maps 트래픽](media/about-azure-maps/Introduction_Traffic.png)

자세한 내용은 [Azure Maps Traffic API](https://docs.microsoft.com/rest/api/maps/traffic) 페이지를 방문하세요.

### <a name="ip-to-location"></a>IP to Location

IP to Location 서비스를 사용하여 제공된 IP 주소의 검색된 두 글자 국가 번호를 미리 볼 수 있습니다. 이 서비스는 지리적 위치를 기반으로 사용자 지정 애플리케이션 콘텐츠를 제공하여 사용자 환경을 조정하고 개선하는 데 도움을 줄 수 있습니다.

IP to Location 서비스용 REST API에 대한 정보는 [Azure Maps Geolocation API](https://docs.microsoft.com/rest/api/maps/geolocation) 페이지를 참조하세요.

## <a name="programming-model"></a>프로그래밍 모델

Azure Maps는 이동성을 위해 빌드되었으며 플랫폼 간 애플리케이션을 지원할 수 있습니다. 언어에 구애 받지 않는 프로그래밍 모델을 사용하며 [REST API](https://docs.microsoft.com/rest/api/maps/)를 통해 JSON 출력을 지원합니다.

또한 Azure Maps는 웹 및 모바일 애플리케이션을 쉽고 빠르게 개발할 수 있는 간단한 프로그래밍 모델이 포함된 편리한 [JavaScript 지도 컨트롤](https://docs.microsoft.com/javascript/api/azure-maps-control)을 제공합니다.

## <a name="usage"></a>사용 현황

Maps 서비스에 액세스하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Azure Maps 계정을 만들어야 합니다.

Azure Maps는 키 기반 인증 체계를 사용합니다. 계정에는 미리 생성된 두 개의 키가 함께 제공됩니다. 하나의 키를 사용하고 Azure Maps 서비스에 대한 요청을 만들어 이러한 위치 기능을 애플리케이션에 통합하는 작업을 시작합니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Maps API는 현재 다음을 제외한 모든 국가에서 제공됩니다.

* 아르헨티나
* 중국
* 인도
* 모로코
* 파키스탄
* 대한민국

현재 IP 주소의 위치가 위의 지원되지 않는 국가 중 하나에 포함되지 않는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Maps의 새로운 기능에 대한 자세한 내용:

> [!div class="nextstepaction"]
> [경로 행렬, 등시성, IP 조회 등](https://azure.microsoft.com/blog/route-matrix-isochrones-ip-lookup-and-more-added-to-azure-maps/)

서비스를 표시하는 샘플 앱을 사용해보도록 진행합니다.

> [!div class="nextstepaction"]
> [데모 대화형 검색 맵 시작](quick-demo-map-app.md)
