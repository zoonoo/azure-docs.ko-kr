---
title: 코스모스 DB에 대한 Azure 모니터를 통해 Azure 코스모스 DB 모니터링(미리 보기)| 마이크로 소프트 문서
description: 이 문서에서는 Cosmos DB 소유자에게 CosmosDB 계정의 성능 및 사용률 문제를 빠르게 이해할 수 있는 Cosmos DB용 Azure 모니터 기능에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: 9a900a2f2e950fe9b9846ebcc047d7c344284948
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250671"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Azure 코스모스 DB용 Azure 모니터 탐색(미리 보기)

Azure Cosmos DB용 Azure 모니터(미리 보기)는 통합대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 실패, 용량 및 운영 상태를 볼 수 있습니다. 이 문서에서는 이 새로운 모니터링 환경의 이점과 조직의 고유한 요구에 맞게 환경을 수정하고 조정할 수 있는 방법을 이해하는 데 도움이 됩니다.   

## <a name="introduction"></a>소개

경험에 다이빙하기 전에, 당신은 그것이 정보를 제시하고 시각화하는 방법을 이해해야합니다. 

그것은 제공합니다 :

* 평가하려는 구독 및 리소스만 선택적으로 범위를 확장할 수 있는 기능을 통해 단일 위치에 있는 모든 구독에 걸쳐 Azure Cosmos DB 리소스의 **확장 관점에서** 볼 수 있습니다.

* 특정 Azure CosmosDB 리소스에 대한 **분석을 드릴다운하여** 문제를 진단하거나 범주별(사용률, 실패, 용량 및 운영)별로 자세한 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택하면 관련 Azure Cosmos DB 메트릭에 대한 심층적인 뷰를 볼 수 있습니다.  

* **사용자 지정 가능** - 이 환경은 Azure Monitor 통합 문서 템플릿 위에 구축되어 표시되는 메트릭을 변경하고 제한에 맞는 임계값을 수정 또는 설정한 다음 사용자 지정 통합 문서로 저장할 수 있습니다. 그러면 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능은 아무것도 활성화하거나 구성할 필요가 없으며 이러한 Azure Cosmos DB 메트릭은 기본적으로 수집됩니다.

>[!NOTE]
>이 기능에 액세스하는 데는 요금이 부과되지 않으며 [Azure Monitor 가격 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 대로 구성하거나 활성화한 Azure Monitor 필수 기능에 대해서만 요금이 부과됩니다.

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Azure 코스모스 DB의 사용률 및 성능 메트릭 보기

모든 구독에서 저장소 계정의 사용률과 성능을 보려면 다음 단계를 수행합니다.

1. [Azure 포털에](https://portal.azure.com)로그인합니다.

2. **모니터를** 검색하고 **모니터를**선택합니다.

    !["모니터"라는 단어와 속도계 스타일 이미지가있는 서비스 "모니터"라는 드롭 다운이있는 검색 상자](./media/cosmosdb-insights-overview/search-monitor.png)

3. **코스모스 DB(미리 보기)를 선택합니다.**

    ![코스모스 DB 개요 통합 문서의 스크린샷](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>개요

**개요에서**테이블에는 대화형 Azure Cosmos DB 메트릭이 표시됩니다. 다음 드롭다운 목록에서 선택한 옵션에 따라 결과를 필터링할 수 있습니다.

* **구독** - Azure Cosmos DB 리소스가 있는 구독만 나열됩니다.  

* **코스모스 DB** - 모든, 하위 집합 또는 단일 Azure 코스모스 DB 리소스를 선택할 수 있습니다.

* **시간 범위** - 기본적으로 해당 선택 항목에 따라 지난 4시간의 정보가 표시됩니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독에 있는 Azure Cosmos DB 리소스의 총 수를 롤업합니다. 트랜잭션 메트릭을 보고하는 통합 문서의 열에 대한 조건부 색상 코딩 또는 히트맵이 있습니다. 가장 깊은 색상은 가장 높은 값을 가지며 밝은 색상은 가장 낮은 값을 기반으로 합니다. 

Azure Cosmos DB 리소스 중 하나 옆에 있는 드롭다운 화살표를 선택하면 개별 데이터베이스 컨테이너 수준에서 성능 메트릭에 대한 분석이 표시됩니다.

![개별 데이터베이스 컨테이너 및 관련 성능 분석 이 드러나는 확장된 드롭다운](./media/cosmosdb-insights-overview/container-view.png)

파란색으로 강조 표시된 Azure Cosmos DB 리소스 이름을 선택하면 연결된 Azure Cosmos DB 계정에 대한 기본 **개요로** 이동합니다. 

### <a name="failures"></a>오류

페이지 상단에서 **오류를** 선택하고 통합 문서 템플릿의 **실패** 부분이 열립니다. 이러한 요청을 구성하는 응답의 분포를 사용하여 총 요청을 보여 주며 다음과 같은 것을 보여 드립니다.

![HTTP 요청 유형별 분석이 발생한 오류 의 스크린샷](./media/cosmosdb-insights-overview/failures.png)

| 코드      |  설명       | 
|-----------|:--------------------|
| `200 OK`  | 다음 REST 작업 중 하나가 정상적으로 완료되었습니다. </br>- 자원에 GET. </br> - 자원에 넣어. </br> - 리소스에 게시합니다. </br> - 저장 프로시저 리소스에 게시하여 저장 프로시저를 실행합니다.|
| `201 Created` | 리소스를 만들기 위한 POST 작업이 정상적으로 완료되었습니다. |
| `404 Not Found` | 더 이상 없는 리소스에 대해 작업을 수행했습니다. 리소스가 이미 삭제된 경우를 예로 들 수 있습니다. |

상태 코드의 전체 목록은 [Azure Cosmos DB HTTP 상태 코드 문서를](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)참조하십시오.

### <a name="capacity"></a>용량

페이지 상단에서 **용량을** 선택하고 통합 문서 템플릿의 **용량** 부분이 열립니다. 사용 가능한 문서 수, 시간에 따라 문서 증가, 데이터 사용량 및 남은 사용 가능한 저장소의 총 양을 보여 줍니다.  잠재적인 저장소 및 데이터 사용률 문제를 식별하는 데 사용할 수 있습니다.

![용량 통합 문서](./media/cosmosdb-insights-overview/capacity.png) 

개요 통합 문서와 마찬가지로 **구독** 열의 Azure Cosmos DB 리소스 옆의 드롭다운을 선택하면 데이터베이스를 구성하는 개별 컨테이너에 의한 분석 이 표시됩니다.

### <a name="operations"></a>작업 

페이지 상단에서 **작업을** 선택하고 통합 문서 템플릿의 **작업** 부분이 열립니다. 요청 유형별로 세분화된 요청을 볼 수 있습니다. 

따라서 아래 예제에서는 주로 `eastus-billingint` 읽기 요청을 수신하지만 소수의 upsert 및 생성 요청을 받는 것을 볼 수 있습니다. 요청 `westeurope-billingint` 관점에서 읽기 전용인 반면, 적어도 지난 4시간 동안 통합 문서의 시간 범위 매개 변수를 통해 현재 범위가 조정됩니다.

![운영 통합 문서](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>핀, 내보내기 및 확장

섹션 오른쪽 상단에 있는 푸시핀 아이콘을 선택하여 메트릭 섹션 중 하나를 [Azure 대시보드에](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) 고정할 수 있습니다.

![대시보드 예제에 대한 메트릭 섹션 핀](./media/cosmosdb-insights-overview/pin.png)

데이터를 Excel 형식으로 내보내려면 푸시핀 아이콘 왼쪽에 있는 아래쪽 화살표 아이콘을 선택합니다.

![통합 문서 아이콘 내보내기](./media/cosmosdb-insights-overview/export.png)

통합 문서의 모든 드롭다운 보기를 확장하거나 축소하려면 내보내기 아이콘의 왼쪽에 있는 확장 아이콘을 선택합니다.

![통합 문서 아이콘 확장](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Azure 코스모스 DB에 대한 Azure 모니터 사용자 지정(미리 보기)

이 환경은 Azure Monitor 통합 문서 템플릿 위에 빌드되므로**편집을** **사용자 지정하고** > 수정된 버전의 복사본을 사용자 지정 통합 문서로 **저장할** 수 있습니다. 

![맞춤 설정 막대](./media/cosmosdb-insights-overview/customize.png)

통합 문서는 개인 정보 **섹션또는** 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션의 리소스 그룹 내에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 실행해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 서비스 [상태 알림을](../../service-health/alerts-activity-log-service-notifications.md) 구성하여 문제 감지에 도움이 될 자동 경고를 설정합니다.

* Azure Monitor 통합 문서를 사용하여 [대화형 보고서 만들기를](../app/usage-workbooks.md)검토하여 통합 문서를 지원하도록 설계된 시나리오, 새 보고서를 작성하고 사용자 지정하는 방법 등을 알아봅니다.
