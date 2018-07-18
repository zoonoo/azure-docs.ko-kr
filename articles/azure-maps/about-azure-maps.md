---
title: Azure Maps 개요 | Microsoft Docs
description: Azure Maps 소개
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: overview
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 239134e7665b80ef78b6a3df12c14156bff9ae29
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/22/2018
ms.locfileid: "36317061"
---
# <a name="an-introduction-to-azure-maps"></a>Azure Maps 소개
Azure Maps는 지도, 검색, 경로, 트래픽 및 표준 시간대를 포함하는 지리 공간 서비스 포트폴리오입니다. 서비스 포트폴리오를 사용하면 익숙한 도구를 사용하여 위치 정보를 Azure 솔루션에 통합하는 솔루션을 신속하게 개발하고 크기를 조정할 수 있습니다. Azure Maps는 모든 산업의 개발자에게 지리적 컨텍스트를 웹 및 모바일 응용 프로그램에 제공하는 데 필수적인 최신 지도 데이터가 포함된 강력한 지리 공간적 기능을 제공합니다. Azure Maps는 여러 미디어를 통한 개발을 쉽고 유연하며 이식 가능하도록 웹 기반 JavaScript 제어와 함께 제공되는 REST API 집합입니다. 

다음 비디오는 Azure Maps를 소개합니다.

<iframe src="https://channel9.msdn.com/Shows/Azure-Friday/Azure-Location-Based-Services/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Azure Maps는 지리적 컨텍스트가 필요한 Azure 응용 프로그램을 강화하는 5개 기본 서비스로 구성됩니다. 각 서비스는 자세히 설명되어 있습니다.

**Render Service**는 개발자가 매핑을 중심으로 웹 및 모바일 응용 프로그램을 만들 수 있도록 설계되었습니다. 이 서비스는 19단계 확대/축소 수준에서 사용할 수 있는 고품질 래스터 그래픽 이미지 또는 모든 것을 사용자 지정할 수 있는 벡터 형식 맵 이미지를 사용합니다.

![Azure Maps Map.png](media/about-azure-maps/Introduction_Map.png)

**Route Service**는 강력한 실제 인프라 기하 도형 계산 및 여러 운송 모드 방향을 포함합니다. 이 서비스는 개발자가 다양한 이동 모드(예: 자동차, 트럭, 자전거 또는 도보)에서 방향을 계산할 수 있도록 합니다. 또한 교통 상황, 무게 제한 또는 위해 물질 운송 등의 입력도 고려할 수 있습니다.

![Azure Maps Route.png](media/about-azure-maps/Introduction_Route.png)

**Search Service**는 개발자가 주소, 위치, 비즈니스 목록을 이름 또는 범주와 기타 지리 정보 기준으로 검색할 수 있도록 설계되었습니다. 또한 Search Service는 위도/경도를 기반으로 주소 및 교차로를 [역방향으로 지오코딩](https://en.wikipedia.org/wiki/Reverse_geocoding)할 수 있습니다. 

![Azure Maps Search.png](media/about-azure-maps/Introduction_Search.png)

**Time Zone Service**를 사용하면 위도-경도 쌍 또는 [IANA ID](http://www.iana.org/)를 사용하여 현재, 과거 및 미래의 표준 시간대 정보를 쿼리할 수 있습니다. 또한 Time Zone 서비스는 Microsoft Windows 표준 시간대 ID를 IANA 표준 시간대로 변환하고, 표준 시간대 오프셋을 UTC로 페치하여 해당 표준 시간대의 현재 시간을 가져올 수 있습니다. Time Zone Service에 대한 쿼리의 일반적인 JSON 응답은 다음 샘플과 같습니다.

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

**Traffic Service**는 개발자가 트래픽을 요구하는 웹 및 모바일 응용 프로그램을 만들 수 있도록 설계된 웹 서비스 제품군입니다. 이 서비스는 다음 두 가지 데이터 형식을 제공합니다.
* Traffic Flow - 네트워크의 모든 주요 도로에 대해 실시간으로 관찰된 속도와 여행 시간 정보 
* Traffic Incidents - 도로 네트워크와 관련된 교통 혼잡 및 사고에 대한 정확한 보기

![Azure Maps 트래픽](media/about-azure-maps/Introduction_Traffic.png)

Azure Maps는 모바일을 위해 개발되었으며 프로그래밍 모델에 구애받지 않고 REST API를 통해 JSON 출력을 지원하므로 플랫폼 간 응용 프로그램을 만들 수 있습니다. 또한 Azure Maps는 웹 및 모바일 응용 프로그램을 쉽고 빠르게 개발할 수 있는 간단한 프로그래밍 모델이 포함된 편리한 JavaScript 지도 컨트롤을 제공합니다. 

Azure Maps는 키 기반 인증 체계를 사용하므로 서비스에 액세스하려면 [Azure Portal](http://portal.azure.com)로 이동하여 Azure Maps 계정을 만들어야 합니다. 계정에는 미리 생성된 두 개의 키가 함께 제공됩니다. 두 키 중 하나를 Azure Maps 요청에 사용하여 이러한 위치 기능을 응용 프로그램에 직접 통합하세요.

## <a name="unsupported-regions"></a>지원되지 않는 지역
Azure Maps API는 현재 일부 국가에서 사용할 수 없습니다. 현재 IP 주소를 확인하고 IP 주소 위치가 지원되지 않는 아래 국가 중 하나에 있지 않은지 확인하세요.

* 아르헨티나
* 중국
* 인도
* 모로코
* 파키스탄
* 대한민국

## <a name="next-steps"></a>다음 단계

이제 Azure Maps를 대략적으로 이해하게 되었을 것입니다. 다음 단계에서는 서비스를 표시하는 샘플 앱을 사용해봅니다.

> [!div class="nextstepaction"]
> [데모 대화형 검색 맵 시작](quick-demo-map-app.md)
