---
title: Azure 지도의 모빌리티 서비스 데이터 구조 | 마이크로소프트 Azure 지도
description: 이 문서에서는 Microsoft Azure 지도 이동성 서비스를 통해 반환되는 일반적인 필드 및 데이터 구조에 대해 알아봅니다.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334439"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure 지도 모빌리티 서비스의 데이터 구조

이 문서에서는 [Azure Maps 이동성 서비스에서](https://aka.ms/AzureMapsMobilityService)메트로 영역의 개념을 소개합니다. 이 서비스가 대중 교통 정류장 및 회선에 대해 쿼리될 때 반환되는 몇 가지 공통 필드에 대해 설명합니다. 모빌리티 서비스 API를 개발하기 전에 이 문서를 읽는 것이 좋습니다.

## <a name="metro-area"></a>메트로 지역

모빌리티 서비스 데이터는 지원되는 메트로 지역으로 그룹화됩니다. 메트로 지역은 도시 경계를 따르지 않습니다. 메트로 지역에는 여러 도시, 인구 밀도가 높은 도시 및 주변 도시가 포함될 수 있습니다. 사실, 국가/지역은 하나의 대도시 지역이 될 수 있습니다. 

는 `metroID` 메트로 지역 [정보 API를](https://aka.ms/AzureMapsMobilityMetroAreaInfo)호출하는 데 사용할 수있는 메트로 지역의 ID입니다. Azure Maps의 "Metro 가져오기" API를 사용하여 선택한 지하철에 대한 대중 교통 유형, 대중 교통 기관, 활성 경고 및 추가 세부 정보를 요청합니다. 또한 지원되는 지하철 지역과 메트로ID를 요청할 수 있습니다. 메트로 지역 아이디는 변경될 수 있습니다.

**메트로ID:** 522 **이름:** 시애틀-타코마-벨뷰

![시애틀-메트로 지역](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>중지 아이디

대중 교통 정류장은 [GFTS(일반 대중 교통 피드 사양)](https://gtfs.org/) ID와 Azure Maps 중지 ID의 두 가지 유형의 ID로 참조할 수 있습니다. GFTS ID를 stopKey라고 하며 Azure Maps 중지 ID를 stopID라고 합니다. 대중 교통 정류장을 자주 참조할 때는 Azure Maps 중지 ID를 사용하는 것이 좋습니다. stopID는 물리적 정지가 존재하는 한 더 안정적이고 동일하게 유지될 가능성이 높습니다. GTFS 중지 ID가 더 자주 업데이트됩니다. 예를 들어 GTFS 중지 ID는 GTFS 공급자 요청또는 새 GTFS 버전이 릴리스될 때 업데이트할 수 있습니다. 물리적 정지는 변경되지 않았지만 GTFS 중지 ID는 변경될 수 있습니다.

시작하려면 근처 대중 교통 [API를](https://aka.ms/AzureMapsMobilityNearbyTransit)사용하여 근처의 대중 교통 정류장을 요청할 수 있습니다.

## <a name="line-groups-and-lines"></a>선 그룹 및 선

모빌리티 서비스는 선 및 선 그룹에 대한 병렬 데이터 모델을 사용합니다. 이 모델은 [GTFS](https://gtfs.org/) 경로 및 이동 데이터에서 상속된 변경 내용을 보다 잘 처리하는 데 사용됩니다.


### <a name="line-groups"></a>선 그룹

라인 그룹은 논리적으로 동일한 그룹의 일부인 모든 줄을 함께 그룹화하는 엔터티입니다. 일반적으로 선 그룹에는 점 A에서 B로, 다른 하나는 B 점에서 A로 돌아오는 두 개의 선이 포함됩니다. 두 노선 모두 동일한 대중 교통 기관에 속하며 동일한 노선 번호를 갖습니다. 그러나 선 그룹에 두 개 이상의 줄이 있거나 줄 내에 한 줄만 있는 경우가 있을 수 있습니다.


### <a name="lines"></a>선

위에서 설명한 대로 각 줄 그룹은 일련의 선으로 구성됩니다. 각 선 그룹은 두 개의 선으로 구성되며 각 선은 방향을 설명합니다.  그러나 더 많은 선이 라인 그룹을 구성하는 경우가 있습니다. 예를 들어, 때로는 특정 동네를 우회하이고 때로는 그렇지 않은 선이 있습니다. 두 경우 모두 동일한 줄 번호로 작동합니다. 또한 선 그룹은 한 줄로 구성될 수 있습니다. 한 방향이 있는 원형 선은 한 줄이 있는 링 그룹입니다.

먼저 전송 라인 [받기 API를](https://aka.ms/AzureMapsMobilityTransitLine)사용하여 회선 그룹을 요청할 수 있습니다.


## <a name="next-steps"></a>다음 단계

모빌리티 서비스를 사용하여 대중교통 데이터를 요청하는 방법에 대해 알아보십시오.

> [!div class="nextstepaction"]
> [대중 교통 데이터를 요청하는 방법](how-to-request-transit-data.md)

모빌리티 서비스를 사용하여 실시간 데이터를 요청하는 방법에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청하는 방법](how-to-request-real-time-data.md)

Azure 지도 모빌리티 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모빌리티 서비스 API 문서](https://aka.ms/AzureMapsMobilityService)
