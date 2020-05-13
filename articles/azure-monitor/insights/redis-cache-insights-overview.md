---
title: Redis 용 Azure Cache (미리 보기)에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 성능 및 사용률 문제를 신속 하 게 파악 하 여 Redis 소유자를 위한 Azure Cache를 제공 하는 Redis Cache 기능에 대해 Azure Monitor 설명 합니다.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/07/2020
ms.openlocfilehash: af32459ffcb50c9f1cfdc59d2c8d355d3551e230
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83210975"
---
# <a name="explore-azure-monitor-for-azure-cache-for-redis-preview"></a>Redis 용 Azure Cache (미리 보기)에 대 한 Azure Monitor 살펴보기

Redis 용 Azure Cache (미리 보기)에 대 한 Azure Monitor는 통합 된 대화형 환경에서 Redis 리소스에 대 한 모든 Azure Cache의 전반적인 성능, 오류, 용량 및 작업 상태를 보여 줍니다. 이 문서는이 새로운 모니터링 환경의 이점과 조직의 고유한 요구 사항에 맞게 환경을 수정 하 고 조정 하는 방법을 이해 하는 데 도움이 됩니다.

## <a name="introduction"></a>소개

경험을 살펴보기 전에 정보를 제공 하 고 시각화 하는 방법을 이해 해야 합니다.

다음을 제공 합니다.

* 단일 위치에 있는 모든 구독에서 리소스를 Redis 하는 Azure 캐시의 **규모 측면** 에서 평가 하려는 구독 및 리소스에 대 한 선택적 범위를 선택할 수 있습니다.

* 특정 Azure Cache for Redis 리소스를 **드릴 다운 분석** 하 여 문제를 진단 하거나 범주 사용률, 실패, 용량 및 작업을 기준으로 상세 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택 하면 관련 된 자세한 보기가 제공 됩니다.  

* **사용자 지정 가능** -이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 표시 되는 메트릭을 변경 하 고, 제한과 일치 하는 임계값을 수정 하거나 설정 하 고, 사용자 지정 통합 문서에 저장할 수 있습니다. 그러면 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능을 사용 하도록 설정 하거나 구성 하지 않아도 되므로 Redis 용 Azure 캐시가 기본적으로 수집 됩니다.

>[!NOTE]
>이 기능에 액세스 하는 것은 무료로 제공 되며 [Azure Monitor 가격 책정 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명 된 대로 구성 하거나 사용 하도록 설정한 Azure Monitor 필수 기능에 대해서만 요금이 부과 됩니다.

## <a name="view-utilization-and-performance-metrics-for-azure-cache-for-redis"></a>Redis 용 Azure Cache의 사용률 및 성능 메트릭 보기

모든 구독에서 저장소 계정의 사용률 및 성능을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모니터** 를 검색 하 고 **모니터**를 선택 합니다.

    !["Monitor" 라는 단어가 포함 된 검색 상자 및 속도계 스타일 이미지를 사용 하는 "Monitor" 서비스 라는 드롭다운](./media/cosmosdb-insights-overview/search-monitor.png)

3. **Azure Cache For Redis (미리 보기)** 를 선택 합니다.

이 옵션이 표시 되지 않으면 **자세히** 를 클릭 하 고 **Redis에 대 한 Azure Cache**를 선택 합니다.

### <a name="overview"></a>개요

**개요**에서 테이블은 Redis 메트릭에 대 한 대화형 Azure 캐시를 표시 합니다. 다음 드롭다운 목록에서 선택한 옵션을 기준으로 결과를 필터링 할 수 있습니다.

* **구독** -Azure Cache for Redis 리소스를 포함 하는 구독만 나열 됩니다.  

* **Redis 용 Azure cache** -Redis 리소스에 대해 모든, 하위 집합 또는 단일 Azure 캐시를 선택할 수 있습니다.

* **시간 범위** -기본적으로는 선택한 항목을 기준으로 지난 4 시간 동안의 정보를 표시 합니다.

드롭다운 목록 아래의 카운터 타일은 선택 된 구독에 있는 Redis 리소스에 대 한 총 Azure 캐시 수를 롤업 합니다. 통합 문서에서 트랜잭션 메트릭을 보고 하는 열에 대 한 조건적 색 구분 또는 열 지도이 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다.

Redis 리소스에 대 한 Azure Cache 옆의 드롭다운 화살표를 선택 하면 개별 리소스 수준에서 성능 메트릭에 대 한 분석 결과가 표시 됩니다.

![개요 환경의 스크린샷](./media/redis-cache-insights-overview/overview.png)

Blue로 강조 표시 된 Redis 용 Azure Cache 리소스 이름을 선택 하면 연결 된 계정에 대 한 기본 **개요** 로 이동 합니다. 여기에는,,,,,,, 등이 표시 `Used Memory` `Used Memory Percentage` `Server Load` `Server Load Timeline` `CPU` `Connected Clients` `Cache Misses` `Errors (Max)` 됩니다.

### <a name="operations"></a>작업

페이지 위쪽에서 **작업** 을 선택 하면 통합 문서 템플릿의 **작업** 부분이 열립니다. ,,,,를 표시 `Total Operations` `Total Operations Timeline` `Operations Per Second` `Gets` `Sets` 합니다.

![개요 환경의 스크린샷](./media/redis-cache-insights-overview/operations.png)

### <a name="usage"></a>사용

페이지 위쪽에서 **사용** 을 선택 하면 통합 문서 템플릿의 **사용** 부분이 열립니다. ,,,,를 표시 `Cache Read` `Cache Read Timeline` `CacheWrite` `CacheHits` `Cache Misses` 합니다.

![개요 환경의 스크린샷](./media/redis-cache-insights-overview/usage.png)

페이지 맨 위에서 **실패** 를 선택 하면 통합 문서 템플릿의 **실패** 부분이 열립니다. 여기에는,,,,,,, 등이 표시 `Total Errors` `Failover/Errors` `UnresponsiveClient/Errors` `RDB/Errors` `AOF/Errors` `Export/Errors` `Dataloss/Errors` `Import/Errors` 됩니다.

![HTTP 요청 유형별 분석 오류 스크린샷](./media/redis-cache-insights-overview/failures.png)

### <a name="metric-definitions"></a>메트릭 정의

이러한 통합 문서를 구성 하는 메트릭 정의의 전체 목록은 [사용 가능한 메트릭 및 보고 간격 문서를 참조](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-monitor#available-metrics-and-reporting-intervals)하세요.

## <a name="pin-export-and-expand"></a>고정, 내보내기 및 확장

섹션의 오른쪽 위에 있는 압정 아이콘을 선택 하 여 메트릭 섹션 중 하나를 [Azure 대시보드에](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) 고정할 수 있습니다.

![대시보드 섹션 대시보드에 고정 예](./media/cosmosdb-insights-overview/pin.png)

데이터를 Excel 형식으로 내보내려면 압정 아이콘 왼쪽에 있는 아래쪽 화살표 아이콘을 선택 합니다.

![통합 문서 내보내기 아이콘](./media/cosmosdb-insights-overview/export.png)

통합 문서의 모든 드롭다운 뷰를 확장 하거나 축소 하려면 내보내기 아이콘 왼쪽에 있는 확장 아이콘을 선택 합니다.

![통합 문서 확장 아이콘](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cache-for-redis-preview"></a>Redis 용 Azure 캐시에 대 한 Azure Monitor 사용자 지정 (미리 보기)

이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 사용자 **지정 하 여**  >  **Edit** 수정 된 버전의 복사본을 사용자 지정 통합 문서에 **저장할** 수 있습니다. 

![사용자 지정 막대](./media/cosmosdb-insights-overview/customize.png)

통합 문서는 사용자가 개인적으로 사용 하는 **내 보고서** 섹션 또는 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에서 리소스 그룹 내에 저장 됩니다. 사용자 지정 통합 문서를 저장 한 후에는 통합 문서 갤러리로 이동 하 여 시작 해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 [서비스 상태 알림을](../../service-health/alerts-activity-log-service-notifications.md) 구성 하 여 문제를 검색 하는 데 도움이 되는 자동화 된 경고를 설정 합니다.

* 통합 문서가 지원 되도록 설계 된 시나리오, 새 보고서를 작성 하 고 기존 보고서를 사용자 지정 하는 방법 등에 대해 알아보고 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](../app/usage-workbooks.md)를 검토 하세요.
