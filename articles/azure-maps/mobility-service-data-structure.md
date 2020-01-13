---
title: Azure Maps의 모바일 서비스 데이터 구조 Microsoft Azure 맵
description: 이 문서에서는 Microsoft Azure 지도 모바일 서비스를 통해 반환 되는 공통 필드 및 데이터 구조에 대해 설명 합니다.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910715"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Azure Maps 모바일 서비스의 데이터 구조

이 문서에서는 공용 전송 중지 및 줄을 쿼리 하는 경우 서비스를 통해 반환 되는 일반 필드와 [모바일 서비스 Azure Maps](https://aka.ms/AzureMapsMobilityService) 의 Metro 영역에 대 한 개념을 소개 합니다. 모바일 서비스 Api를 시작 하기 전에이 문서를 진행 하는 것이 좋습니다. 아래의 일반적인 필드에 대해 설명 합니다.

## <a name="metro-area"></a>Metro 영역

모바일 서비스 데이터는 지원 되는 metro 영역으로 분할 됩니다. Metro 영역에서 도시 경계를 따르지 않는 경우 metro 영역에는 조밀 채워진 도시와 주변 도시와 같은 여러 도시가 포함 될 수 있습니다. 그리고 국가/지역은 하나의 지하철 영역 일 수 있습니다. 

`metroID`은 metro 영역 [정보 API](https://aka.ms/AzureMapsMobilityMetroAreaInfo) 를 호출 하 여 지원 되는 전송 형식 및 전송 기관과 활성 경고와 같은 metro 영역에 대 한 추가 세부 정보를 요청 하는 데 사용할 수 있는 METRO 영역 ID입니다. Azure Maps Metro API 가져오기를 사용 하 여 지원 되는 metro 영역 및 metroIDs을 요청할 수 있습니다. Metro 영역 Id는 변경 될 수 있습니다.

**metroID:** 522 **이름:** 시애틀-Tacoma-Bellevue

![시애틀-지하철-지역](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Id 중지

전송 중지는 두 가지 유형의 Id, [일반 전송 피드 사양 (GFTS)](https://gtfs.org/) Id (stopkey 라고도 함) 및 AZURE MAPS 중지 Id (stopkey 라고 함)로 참조할 수 있습니다. 시간 경과에 따라 중지를 참조 하는 경우이 ID는 안정적이 고 실제 중지가 존재 하는 동안 변경 될 수 없으므로 Azure Maps 중지 ID를 사용 하는 것이 좋습니다. Gtfs 중지 ID는 더 자주 업데이트 됩니다. 예를 들어 GTFS 공급자가 변경 해야 하거나 새로운 GTFS 버전이 해제 된 경우에도 물리적 중지는 변경 되지 않은 경우입니다.

시작 하려면 [주변 전송 API 가져오기](https://aka.ms/AzureMapsMobilityNearbyTransit)를 사용 하 여 가까운 전송 중지를 요청할 수 있습니다.

## <a name="line-groups-and-lines"></a>줄 그룹 및 선

모바일 서비스는 선 및 선 그룹에 병렬 데이터 모델을 사용 하 여 [Gtfs](https://gtfs.org/) 경로 및 여행 데이터 모델에서 상속 된 변경 내용을 더 효과적으로 처리 합니다.


### <a name="line-groups"></a>줄 그룹

줄 그룹은 동일한 그룹의 논리적 부분인 모든 줄을 그룹화 하는 엔터티입니다. 일반적으로 선 그룹에는 두 줄이 포함 됩니다. 한 줄은 A에서 B로, 다른 하나는 동일한 공용 전송 에이전시에 속하고 동일한 줄 번호를 가진 두 줄을 포함 합니다. 그러나 줄 그룹에는 줄이 세 개 이상 있거나 한 줄만 있는 경우가 있을 수 있습니다.


### <a name="lines"></a>선

위에서 설명한 것 처럼 각 줄 그룹은 일련의 선으로 구성 됩니다. 각 줄이 방향을 설명 하 고 각 줄 그룹은 두 줄로 구성 되는 경우가 많습니다. 그러나 줄 그룹을 구성 하는 줄이 더 많은 경우가 있습니다. 예를 들어 때로는 특정 환경에서 우회 하는 줄이 있고 경우에 따라 그렇지 않고 동일한 줄 번호의 두 경우 모두에서 작동 하는 경우가 있습니다 .이 경우에는 줄 g r) 단일 선으로 구성 됩니다 (예: 단일 방향의 원형 선).

시작 하려면 [전송 라인 API 가져오기](https://aka.ms/AzureMapsMobilityTransitLine) 를 사용 하 여 줄 그룹을 요청 하 고 나중에 줄로 드릴 다운할 수 있습니다.


## <a name="next-steps"></a>다음 단계

모바일 서비스를 사용 하 여 전송 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [전송 데이터를 요청 하는 방법](how-to-request-transit-data.md)

모바일 서비스를 사용 하 여 실시간 데이터를 요청 하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청 하는 방법](how-to-request-real-time-data.md)

Azure Maps 모바일 서비스 API 설명서 살펴보기

> [!div class="nextstepaction"]
> [모바일 서비스 API 설명서](https://aka.ms/AzureMapsMobilityService)
