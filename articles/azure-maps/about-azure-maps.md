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
ms.openlocfilehash: 5f41263113568cf9f3771119135be8db37119181
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2019
ms.locfileid: "67442953"
---
# <a name="what-is-azure-maps"></a>Azure Maps란?

Azure Maps는 웹 및 모바일 애플리케이션에 정확한 지리적 컨텍스트를 제공하기 위해 최신 매핑 데이터를 사용하는 지리 공간적 서비스의 컬렉션입니다. Azure Maps는 다음을 제공합니다.

* 맵을 여러 스타일과 위성 이미지로 렌더링하기 위한 REST API.
* 전 세계의 주소, 장소 및 관심 지점을 검색.
* 지점 간 라우팅, 다중 지점, 다중 지점 최적화, 등시선, 상용차, 교통 영향 및 매트릭스 라우팅, 교통 흐름 및 사고 표시.
* 대중교통 및 대체 교통수단(자전거 공유, 스쿠터 공유 및 자동차 공유)을 요청하고 실시간으로 경로를 계획하기 위한 Mobility Service. 
* 지리적 위치를 통한 사용자 위치 설정 및 위치를 표준 시간대로 전환. 
* 위치 정보가 Azure에서 호스팅되는 지오펜싱 및 지도 데이터 스토리지를 위한 서비스. 
* 지리 공간 분석을 통한 위치 인텔리전스. 

REST API 외에도 웹 SDK 또는 Android SDK를 통해 Azure Maps 서비스를 이용할 수 있습니다. 이러한 도구는 개발자들이 위치 정보를 Azure 솔루션으로 통합하는 솔루션을 신속하게 개발하고 확장하는 데 유용합니다. 

지금 바로 [Azure Maps 계정](https://azure.microsoft.com/services/azure-maps/) 체험판에 가입하여 개발을 시작할 수 있습니다.

다음 비디오는 Azure Maps를 자세히 설명합니다.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Maps/player?format=ny" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="map-controls"></a>맵 컨트롤

### <a name="web-sdk"></a>웹 SDK

Azure Maps 웹 SDK를 사용하면 웹 또는 모바일 애플리케이션에 표시할 자체 콘텐츠와 이미지를 통해 대화형 지도를 사용자 지정할 수 있습니다. 이 컨트롤을 통해 WebGL을 사용하여 고성능의 큰 데이터 세트를 렌더링할 수 있습니다. JavaScript 또는 TypeScript를 사용하여 SDK에서 개발합니다.

![채우기 변경의 맵 예제](media/about-azure-maps/Introduction_WebMapControl.png)

### <a name="android-sdk"></a>Android SDK

Azure Maps Android SDK를 사용하여 모바일 매핑 애플리케이션을 만듭니다. 

![모바일 디바이스의 맵 예제](media/about-azure-maps/AndroidSDK.png)

## <a name="services-in-azure-maps"></a>Azure Maps의 서비스

Azure Maps는 Azure 애플리케이션에 지리적 컨텍스트를 제공할 수 있는 다음 9개의 서비스로 구성됩니다.

### <a name="data-service"></a>Data Service

데이터는 맵에 필수적입니다. Data Service를 사용하여 공간 작업 또는 이미지 구성에 사용할 지리 공간 데이터를 업로드하고 저장합니다.  고객 데이터를 Azure Maps 서비스에 더 가까이 가져오면 대기 시간이 줄어들고 생산성이 향상되며 애플리케이션에서 새 시나리오를 만들 수 있습니다. 이 서비스에 대해 자세히 알아보려면 [Data Service API 설명서](https://docs.microsoft.com/rest/api/maps/data)를 참조하세요.

### <a name="mobility-service"></a>모바일 서비스

Azure Maps Mobility Service를 사용하면 실시간 여행 계획이 가능합니다. 가능한 최선의 경로 옵션을 반환하고 다양한 여행 모드를 제공합니다. 지하철(도시) 지역의 경우, 이러한 모드에는 걷기, 자전거 타기 및 대중 교통 수단이 포함될 수 있습니다. 개발자는 선 기하학, 정지 목록, 예약 및 실시간 도착, 서비스 알림과 같은 교통 여행 일정 세부 정보를 요청할 수 있습니다.

또한 서비스를 통해 주변에서 공유 자전거, 스쿠터 또는 자동차와 같이 특정한 물체 유형을 검색할 수 있습니다. 사용자는 가장 가까운 선착장에 몇 대의 공유 자전거가 남아 있는지 요청할 수 있습니다. 사용 가능한 자동차 공유 차량을 검색하고 향후 이용 가능성과 현재의 연료 수준과 같은 세부 정보도 찾을 수 있습니다.

서비스에 대해 자세히 알아보려면 [Mobility API 설명서](https://docs.microsoft.com/rest/api/maps/mobility)를 참조하세요.

### <a name="render-service"></a>Render Service

Render Service는 개발자가 매핑을 중심으로 웹 및 모바일 애플리케이션을 만드는 데 도움을 줍니다. 이 서비스는 19단계 확대/축소 수준에서 사용할 수 있는 고품질 래스터 그래픽 이미지 또는 모든 것을 사용자 지정할 수 있는 벡터 형식 맵 이미지를 사용합니다.

![Render Service의 맵 예제](media/about-azure-maps/Introduction_Map.png)

Render Service는 현재 개발자가 위성 이미지를 사용하여 작업할 수 있도록 미리 보기 API를 제공합니다. 자세한 내용은 [Render API 설명서](https://docs.microsoft.com/rest/api/maps/render)를 읽어보세요.

### <a name="route-service"></a>Route Service

Route Service는 여러 운송 모드의 실제 인프라 및 방향에 대한 강력한 기하 도형 계산을 포함합니다. 이 서비스는 개발자가 다양한 이동 모드(예: 자동차, 트럭, 자전거 또는 도보)에서 방향을 계산할 수 있도록 합니다. 또한 교통 상황, 무게 제한 또는 위해 물질 운송 등의 입력도 고려할 수 있습니다.

![Route Service의 맵 예제](media/about-azure-maps/Introduction_Route.png)

Route Service는 다음과 같은 고급 기능의 미리 보기를 제공합니다. 

* 여러 경로 요청의 일괄 처리.
* 원본과 대상 세트 간 여행 시간 및 거리의 매트릭스.
* 시간 또는 연료 요구 사항에 기반한 사용자가 여행할 수 있는 경로 또는 거리 찾기. 

라우팅 기능에 대한 자세한 내용은 [Route API 설명서](https://docs.microsoft.com/rest/api/maps/route)를 읽어보세요.

### <a name="search-service"></a>Search 서비스

Search Service는 개발자가 주소, 장소, 비즈니스 목록을 이름이나 범주 및 기타 지리 정보 기준으로 검색하는 데 유용합니다. Search Service는 위도 및 경도를 기반으로 주소 및 교차로를 [역방향으로 지오코딩](https://en.wikipedia.org/wiki/Reverse_geocoding)할 수 있습니다.

![맵에서 검색 예제](media/about-azure-maps/Introduction_Search.png)

또한 Search Service는 다음과 같은 고급 기능을 제공합니다.

* 경로에 따른 검색.
* 더 넓은 영역 내에서 검색.
* 검색 요청의 그룹 일괄 처리.
* 위치 지점 대신 더 큰 영역 검색. 

일괄 처리 및 영역 검색에 대한 API는 현재 미리 보기에 있습니다. 검색 기능에 대한 자세한 내용은 [Search API 설명서](https://docs.microsoft.com/rest/api/maps/search)를 읽어보세요.

### <a name="spatial-operations-service"></a>Spatial Operations Service

Azure Maps Spatial Operations Service는 위치 정보를 사용하고 즉석에서 분석하여 고객들에게 시간 및 공간에서 발생하고 있는 실시간 이벤트를 알리는 데 유용합니다. 이벤트를 거의 실시간으로 분석하고 예측 모델링을 수행할 수 있습니다. 

서비스를 통해 고객은 가장 가까운 지점, 대권 거리 및 버퍼와 같은 일반적인 지리 공간적 수학 계산의 라이브러리를 통해 해당 위치 인텔리전스를 향상시킬 수 있습니다. 서비스와 다양한 기능에 대해 자세히 알아보려면 [Spatial Operations API 설명서](https://docs.microsoft.com/rest/api/maps/spatial)를 읽어보세요.

### <a name="time-zone-service"></a>Time Zone Service

Time Zone Service를 사용하면 위도/경도 쌍 또는 [IANA ID](https://www.iana.org/)를 사용하여 현재, 과거 및 미래의 표준 시간대 정보를 쿼리할 수 있습니다. Time Zone Service를 통해 다음을 수행할 수 있습니다.

* Microsoft Windows 표준 시간대 ID를 IANA 표준 시간대로 변환.
* 표준 시간대 오프셋을 UTC로 페치.
* 현재 시간을 표준 시간대로 가져오기. 

Time Zone Service에 대한 쿼리의 일반적인 JSON 응답은 다음 샘플과 같습니다.

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

이 서비스에 대한 자세한 내용은 [Time Zone API 설명서](https://docs.microsoft.com/rest/api/maps/timezone)를 읽어보세요.

### <a name="traffic-service"></a>Traffic Service

Traffic Service는 개발자가 교통 정보를 요구하는 웹 및 모바일 애플리케이션을 만드는 데 사용할 수 있는 웹 서비스 제품군입니다. 이 서비스는 다음 두 가지 데이터 형식을 제공합니다.

* 교통 흐름: 네트워크의 모든 주요 도로에 대해 실시간으로 관찰된 속도와 여행 시간.
* 교통 사고: 도로 네트워크와 관련된 교통 혼잡 및 사고에 대한 최신 보기.

![교통 정보를 사용한 맵의 예제](media/about-azure-maps/Introduction_Traffic.png)

자세한 내용은 [Traffic API 설명서](https://docs.microsoft.com/rest/api/maps/traffic)를 참조하세요.

### <a name="ip-to-location-service"></a>IP to Location Service

IP to Location Service를 사용하여 IP 주소의 검색된 두 글자 국가 번호를 미리 볼 수 있습니다. 이 서비스는 지리적 위치를 기반으로 사용자 지정된 애플리케이션 콘텐츠를 제공하여 사용자 환경을 조정하고 개선하는 데 도움을 줄 수 있습니다.

IP to Location Service에 대한 REST API에 대해 자세히 알아보려면 [Azure Maps Geolocation API 설명서](https://docs.microsoft.com/rest/api/maps/geolocation)를 읽어보세요.

## <a name="programming-model"></a>프로그래밍 모델

Azure Maps는 이동성을 위해 빌드되었으며 플랫폼 간 애플리케이션을 개발하는 데 유용할 수 있습니다. 언어에 구애 받지 않는 프로그래밍 모델을 사용하며 [REST API](https://docs.microsoft.com/rest/api/maps/)를 통해 JSON 출력을 지원합니다.

또한 Azure Maps는 웹 및 모바일 애플리케이션을 쉽고 빠르게 개발할 수 있는 간단한 프로그래밍 모델이 포함된 편리한 [JavaScript 지도 컨트롤](https://docs.microsoft.com/javascript/api/azure-maps-control)을 제공합니다.

## <a name="usage"></a>사용 현황

Azure Maps 서비스에 액세스하려면 [Azure Portal](https://portal.azure.com)로 이동하여 Azure Maps 계정을 만들어야 합니다.

Azure Maps는 키 기반 인증 체계를 사용합니다. 계정에는 미리 생성된 두 개의 키가 함께 제공됩니다. 하나의 키를 사용하고 Azure Maps 서비스에 대한 요청을 만들어 이러한 위치 기능을 애플리케이션에 통합하는 작업을 시작합니다.

## <a name="supported-regions"></a>지원되는 지역

Azure Maps API는 현재 다음 지역을 제외한 모든 국가/지역에서 제공됩니다.

* 아르헨티나
* 중국
* 인도
* 모로코
* 파키스탄
* 대한민국

현재 IP 주소의 위치가 지원되지 않는 국가/지역 중 하나에 포함되지 않는지 확인합니다.

## <a name="next-steps"></a>다음 단계

Azure Maps를 보여주는 샘플 앱을 사용해보세요.

> [!div class="nextstepaction"]
> [빠른 시작: 웹앱 만들기](quick-demo-map-app.md)

Azure Maps에서 데이터를 최신 상태로 유지하세요. 

> [!div class="nextstepaction"]
> [Azure Maps 블로그](https://azure.microsoft.com/blog/topics/azure-maps/)
