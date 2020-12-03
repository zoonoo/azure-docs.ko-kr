---
title: 리소스 및 할당량 관리
titleSuffix: Azure Purview
description: Azure 부서의 범위의 리소스에 대 한 할당량 및 제한 및 할당량 향상을 요청 하는 방법에 대해 알아봅니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/12/2020
ms.openlocfilehash: 3b0a413db304b4f9d2c50a3d221c480f1e9dc37a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553712"
---
# <a name="manage-and-increase-quotas-for-resources-with-azure-purview"></a>Azure 부서의 범위를 사용 하 여 리소스에 대 한 할당량 관리 및 늘리기
 
Azure 부서의 범위는 데이터 사용자가 사용할 수 있는 클라우드 서비스입니다. Azure 부서의 범위를 사용 하 여 클라우드 및 온-프레미스 환경에 걸친 데이터 공간 전체의 데이터 관리를 중앙에서 관리 합니다. 비즈니스 분석가는이 서비스를 통해 의미 있는 비즈니스 용어를 사용 하 여 관련 데이터를 검색할 수 있습니다. 구독에 대한 최대 제한 값을 높이려면 고객 지원에 문의하세요.
 
## <a name="azure-purview-limits"></a>Azure 부서의 범위 제한
 
|**리소스**|  **기본 제한**  |**최대 제한**|
|---|---|---|
|테 넌 트 당 부서의 범위 계정 (모든 구독 결합)|3|지원에 문의|
|계정 당 검색에 사용할 수 있는 vCores *|160|160|
|지정 된 지점에 있는 계정 당 동시 검색. 이 제한은 검색 된 데이터 원본의 유형에 따라 달라 집니다.|5 | 10 |
|검색을 실행할 수 있는 최대 시간|7 일|7 일|
|API 호출, 계정 당|4 개의 용량 단위 플랫폼 크기에 대 한 10M Api/월. <br>16 개 용량 단위 플랫폼 크기의 경우 40M Api/월 |4 개의 용량 단위 플랫폼 크기에 대 한 10M Api/월. <br>16 개 용량 단위 플랫폼 크기의 경우 40M Api/월|
|저장소, 계정 당|4gb는 용량 단위 플랫폼 크기에 대해 10gb입니다. <br>16 개의 용량 단위 플랫폼 크기의 40 GB |4gb는 용량 단위 플랫폼 크기에 대해 10gb입니다. <br> 16 개의 용량 단위 플랫폼 크기의 40 GB |
|계정 당 자산의 크기|100M 물리적 자산 |지원에 문의|
|카탈로그에 있는 자산의 최대 크기|2MB|2MB|
|자산 이름 및 분류 이름의 최대 길이|4KB|4KB|
|자산 속성 이름 및 값의 최대 길이입니다.|32KB|32KB|
|분류 특성 이름 및 값의 최대 길이입니다.|32KB|32KB|
|계정 당 최대 용어 용어 수|100K|100K|
 
* 자체 호스팅 통합 런타임 시나리오는 위의 표에 정의 된 제한 범위를 벗어납니다. 
 
## <a name="next-steps"></a>다음 단계
 
> [!div class="nextstepaction"]
>[자습서: Azure 부서의 범위를 사용 하 여 데이터 검색](tutorial-scan-data.md)

> [!div class="nextstepaction"]
>[자습서: 홈 페이지를 탐색 하 고 자산을 검색 합니다.](tutorial-asset-search.md)

> [!div class="nextstepaction"]
>[자습서: 자산 찾아보기 및 계보 보기](tutorial-browse-and-view-lineage.md)
