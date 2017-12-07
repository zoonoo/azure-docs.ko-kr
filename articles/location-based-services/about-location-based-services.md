---
title: "Azure Location Based Services 개요 | Microsoft Docs"
description: "Azure Location Based Services(미리 보기) 소개"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: article
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: c8ebce06a72bcaf769a11ec954702463d7489aa0
ms.sourcegitcommit: 310748b6d66dc0445e682c8c904ae4c71352fef2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2017
---
# <a name="an-introduction-to-azure-location-based-services-preview"></a>Azure Location Based Services(미리 보기) 소개
Azure Location Based Services는 맵, 검색, 경로, 트래픽 및 표준 시간대를 포함하는 지리 공간 서비스 포트폴리오입니다. Azure OneAPI 규격 서비스를 사용하면 익숙한 개발자 도구를 사용하여 위치 정보를 Azure 솔루션에 통합하는 솔루션을 신속하게 개발하고 크기를 조정할 수 있습니다. Azure Location Based Services는 모든 산업의 개발자에게 지리적 컨텍스트를 웹 및 모바일 응용 프로그램에 제공하는 데 필수적인 최신 매핑 데이터를 탑재한 강력한 지리 공간 기능을 제공합니다. Azure Location Based Services는 여러 미디어를 통한 개발을 매우 쉽고 유연하며 이식 가능하도록 웹 기반 JavaScript 제어와 함께 제공되는 REST API의 Azure One API 규격 집합입니다. 

Azure Location Based Services는 지리적 컨텍스트가 필요한 Azure 응용 프로그램을 강화하는 5개 기본 서비스로 구성됩니다. 각 서비스는 아래에 자세히 설명되어 있습니다.

**Render Service** – Render Service는 개발자가 매핑을 중심으로 웹 및 모바일 응용 프로그램을 만들 수 있도록 설계되었습니다. 이 서비스는 19단계 확대/축소 수준에서 사용할 수 있는 고품질 래스터 그래픽 이미지 또는 모든 것을 사용자 지정할 수 있는 벡터 형식 맵 이미지를 사용합니다.

![Azure Location Based Services Map.png](media/about-location-based-services/Introduction_Map.png)

**Route Service** – Route Service는 강력한 실제 인프라 기하 도형 계산 및 여러 운송 모드 방향으로 구성됩니다. 이 서비스를 사용하면 개발자는 자동차, 트럭, 자전거 또는 도보 같은 다양한 여행 모드뿐 아니라 트래픽 조건, 가중치 제한, 위험 자재 운송 등의 다양한 입력에 대한 방향을 계산할 수 있습니다.

![Azure Location Based Services Route.png](media/about-location-based-services/Introduction_Route.png)

**Search Service** - Search Service는 개발자가 주소, 위치, 비즈니스 목록을 이름 또는 범주와 기타 지리 정보 기준으로 검색할 수 있도록 설계되었습니다. 또한 Search Service는 위도/경도를 기반으로 주소 및 교차로를 [역방향으로 지오코딩](https://en.wikipedia.org/wiki/Reverse_geocoding)할 수 있습니다. 

![Azure Location Based Services Search.png](media/about-location-based-services/Introduction_Search.png)

**Time Zone Service** - Time Zone Service를 사용하면 위도-경도 쌍 또는 [IANA ID](http://www.iana.org/)를 사용하여 현재, 과거 및 미래의 표준 시간대 정보를 쿼리할 수 있습니다. 또한 Time Zone 서비스는 Microsoft Windows 표준 시간대 ID를 IANA 표준 시간대로 변환하고, 표준 시간대 오프셋을 UTC로 페치하여 해당 표준 시간대의 현재 시간을 가져올 수 있습니다. Time Zone Service에 대한 쿼리의 일반적인 JSON 응답은 다음과 같습니다.

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

**Traffic Service** – Traffic Service는 개발자가 트래픽을 요구하는 웹 및 모바일 응용 프로그램을 만들 수 있도록 설계된 웹 서비스 제품군입니다. 이 서비스는 다음과 같이 분류됩니다.
1. Traffic Flow - 네트워크의 모든 주요 도로에 대해 실시간으로 관찰된 속도와 여행 시간 정보를 제공합니다. 
2. Traffic Incidents - 도로 네트워크와 관련된 교통 혼잡 및 사고에 대한 정확한 보기를 제공합니다.

![Azure Location Based Services Traffic](media/about-location-based-services/Introduction_Traffic.png)

Azure Location Based Services는 모바일을 위해 개발되었으며 프로그래밍 모델에 구애받지 않고 REST API를 통해 JSON 출력을 지원하므로 플랫폼 간 응용 프로그램을 만들 수 있습니다. 뿐만 아니라 Azure LBS는 웹 및 모바일 응용 프로그램을 쉽고 빠르게 개발할 수 있는 간단한 프로그래밍 모델이 포함된 편리한 JavaScript 맵 컨트롤을 제공합니다. 

Azure Location Based Services는 키 기반 인증 체계를 사용하므로 서비스에 액세스하려면 [Azure Portal](http://portal.azure.com)로 이동하여 Azure Location Based Services 계정을 만들어야 합니다. 계정에는 미리 생성된 두 개의 키가 함께 제공됩니다. 두 키 중 하나를 Azure Location Based Services 요청에 사용하여 이러한 위치 기능을 응용 프로그램에 직접 통합하세요.

[지금 Azure Location Based Services 계정에 등록하세요!](http://aka.ms/azurelbsportal)

## <a name="next-steps"></a>다음 단계

Azure Location Based Services(미리 보기)의 개요를 살펴보았습니다. 다음 단계는 Location Based Services를 보여 주는 샘플 앱을 사용해 보고 웹 응용 프로그램에서 종합적인 시나리오를 만드는 것입니다.

> [!div class="nextstepaction"]
> [Azure Location Based Services(미리 보기)를 사용하여 데모 대화형 맵 검색 시작](quick-demo-map-app.md)
> [Azure Location Based Services를 사용하여 주변 관심 지점 검색](tutorial-search-location.md)