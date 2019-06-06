---
title: Azure Maps에서 모바일 서비스 데이터 구조 | Microsoft Docs
description: Azure Maps Mobility Service의 데이터 구조
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 29e8a9d7555ca836b6266879f3b3c1e32ffd3980
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2019
ms.locfileid: "66735558"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps Mobility Service의 데이터 구조

이 문서에서는 Metro 영역의 개념을 소개 [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService) 공통 필드의 일부 반환 되 고 서비스를 통해 쿼리 되는 경우 공용 전송에 대 한 중지 줄. Mobility Service Api를 시작 하기 전에이 문서를 진행 하는 것이 좋습니다. 이러한 공통 필드 아래 설명합니다.

## <a name="metro-area"></a>Metro 영역

모바일 서비스 데이터는 지원 되는 metro 영역으로 분할 됩니다. Metro 영역 city 경계를 따르지 않는, metro 영역 예제, 조밀 하 게 채워진된 도시 및 해당 주변 도시;에 대 한 여러 도시를 포함할 수 있습니다. 및 국가/지역 metro 영역 중 하나를 수 있습니다. 

`metroID` 호출 하는 metro 영역을 id는 [Metro 영역 정보 API 가져오기](https://aka.ms/AzureMapsMobilityMetroAreaInfo) 지원 요청 전송 형식 및 전송 기관 및 활성 경고와 같은 metro 영역에 대 한 추가 세부 정보. 지원 되는 metro 영역과 metroIDs를 요청 하려면 Azure Maps 가져오기 Metro API를 사용할 수 있습니다. Metro 영역 Id는 변경 될 수 있습니다.

**metroID:** 522 **이름:** Seattle-Tacoma-Bellevue

![시애틀-metro-영역](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Id를 중지 합니다.

전송 중지 두 가지 유형의 Id 참조할 수 있습니다 합니다 [일반 전송 피드 사양 (GFTS)](https://gtfs.org/) ID (stopKey 라고도 함) 및 Azure Maps ID (stopId 라고도 함)를 중지 합니다. 시간이 지남에 따라 중지를 참조할 때이 ID는 보다 안정적이 고 실제 중지 존재할 가능성이 변경 되지 것입니다 Azure Maps 중지 ID를 사용 하도록이 좋습니다. GTFS 중지 ID는 자주 업데이트, 예를 들어 GTFS 공급자를 변경 해야 합니다. 또는 새 GTFS 버전이 출시 되는 경우 실제 중지 있었지만 변경 되지 않습니다.

시작을 요청할 수 있습니다 사용 하 여 주변 전송 중지 [주변 전송 API 가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit)합니다.

## <a name="line-groups-and-lines"></a>행 그룹 및 선

모바일 서비스 줄에 대 한 병렬 데이터 모델을 사용 하 고 변경 내용을 더 다룰 줄 그룹에서 상속 [GTFS](https://gtfs.org/) 경로 및 여정 데이터 모델입니다.


### <a name="line-groups"></a>줄 그룹

줄 그룹은 논리적으로 동일한 그룹의 일부인 모든 줄 그룹화는 엔터티에서. 일반적으로 줄 그룹을 두 줄, 하나의 것인지 지점 A에서 B, 다른 지점 B에서 A, 동일한 공용 전송 에이전시에 모두 속하는으로 반환 및 동일한 줄 번호가 포함 됩니다. 그러나 줄 그룹 두 개 이상의 줄 또는 줄 내에 지정 된 경우가 있을 수 있습니다.


### <a name="lines"></a>선

위에서 설명 했 듯이 각 줄 그룹 일련의 선으로 구성 됩니다. 각 줄 방향 및 각 줄을 설명 하는 종종 두 줄 그룹 구성 됩니다. 개 하는 경우에 따라 우회는 특정 환경 및 경우에 따라 줄이 있는 줄이 더 이상에서 그룹을 구성 줄, 예를 들어 있는 경우 않을 두 경우 모두 동일한 줄 번호에서 운영 하 고 있는 다른 경우도 있습니다 g 줄 예를 들어 단일 방향으로 순환 줄 한 줄의 그룹으로 구성 됩니다.

시작 줄 그룹 사용 하 여 요청할 수 있습니다 합니다 [전송 줄 API 가져오기](https://aka.ms/AzureMapsMobilityTransitLine) 및 줄 아래로 이상 드릴 합니다.


## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 전송 중인 데이터를 요청 하는 방법에 알아봅니다.

> [!div class="nextstepaction"]
> [전송 중 데이터를 요청 하는 방법](how-to-request-transit-data.md)

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법에 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Service API 설명서](https://aka.ms/AzureMapsMobilityService)
