---
title: Cosmos DB에 대 한 Azure Monitor를 사용 하 여 Azure Cosmos DB 모니터링 (미리 보기) | Microsoft Docs
description: 이 문서에서는 Cosmos DB 소유자에 게 CosmosDB 계정에 대 한 성능 및 사용 문제를 신속 하 게 이해 하는 Cosmos DB 기능을 제공 하는 Azure Monitor에 대해 설명 합니다.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 10/27/2019
ms.openlocfilehash: d28db9907094a651835078f4459a985b9d15e589
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/27/2020
ms.locfileid: "77657387"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Cosmos DB에 대 한 Azure Monitor 살펴보기 (미리 보기)

Azure Cosmos DB (미리 보기)에 대 한 Azure Monitor는 통합 된 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전반적인 성능, 오류, 용량 및 운영 상태를 보여 줍니다. 이 문서는이 새로운 모니터링 환경의 이점과 조직의 고유한 요구 사항에 맞게 환경을 수정 하 고 조정 하는 방법을 이해 하는 데 도움이 됩니다.   

## <a name="introduction"></a>소개

경험을 살펴보기 전에 정보를 제공 하 고 시각화 하는 방법을 이해 해야 합니다. 

다음을 제공 합니다.

* 단일 위치에 있는 모든 구독에 대 한 Azure Cosmos DB 리소스의 **규모 관점** 에서 원하는 구독 및 리소스에 대 한 범위를 선택적으로 선택할 수 있습니다.

* 특정 Azure CosmosDB 리소스를 **드릴 다운 분석** 하 여 문제를 진단 하거나 범주 사용률, 실패, 용량 및 작업을 통해 자세한 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택 하면 관련 Azure Cosmos DB 메트릭을 자세히 확인할 수가 제공 됩니다.  

* **사용자 지정 가능** -이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 표시 되는 메트릭을 변경 하 고, 제한과 일치 하는 임계값을 수정 하거나 설정 하 고, 사용자 지정 통합 문서에 저장할 수 있습니다. 그러면 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능을 사용 하도록 설정 하거나 구성할 필요가 없습니다. 이러한 Azure Cosmos DB 메트릭은 기본적으로 수집 됩니다.

>[!NOTE]
>이 기능에 액세스 하는 것은 무료로 제공 되며 [Azure Monitor 가격 책정 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명 된 대로 구성 하거나 사용 하도록 설정한 Azure Monitor 필수 기능에 대해서만 요금이 부과 됩니다.

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 작업 수준 메트릭 보기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 왼쪽 탐색 모음에서 **모니터** 를 선택 하 고 **메트릭**을 선택 합니다.

   ![Azure Monitor의 메트릭 창](./media/cosmosdb-insights-overview/monitor-metrics-blade.png)

1. **메트릭** 창에서 리소스 > **선택** 하 > 필요한 **구독**및 **리소스 그룹**을 선택 합니다. **리소스 종류**에 대해 **Azure Cosmos DB 계정**을 선택 하 고, 기존 Azure Cosmos 계정 중 하나를 선택 하 고, **적용**을 선택 합니다.

   ![메트릭을 볼 Cosmos DB 계정 선택](./media/cosmosdb-insights-overview/select-cosmosdb-account.png)

1. 다음으로 사용 가능한 메트릭 목록에서 메트릭을 선택할 수 있습니다. 요청 단위, 저장소, 대기 시간, 가용성, Cassandra 및 기타에 특정 한 메트릭을 선택할 수 있습니다. 이 목록에서 사용할 수 있는 모든 메트릭에 대해 자세히 알아보려면 [범주별 메트릭](../../cosmos-db/monitor-cosmos-db-reference.md) 문서를 참조 하세요. 이 예제에서는 **요청 단위** 및 **Avg** 를 집계 값으로 선택 하겠습니다.

   이러한 세부 정보 외에도 메트릭의 **시간 범위** 와 **시간 세분성** 을 선택할 수 있습니다. 최대는 지난 30 일 동안의 메트릭을 볼 수 있습니다.  필터를 적용 하면 필터를 기반으로 차트가 표시 됩니다. 선택한 기간에 분당 사용한 평균 요청 단위 수를 볼 수 있습니다.  

   ![Azure Portal에서 메트릭 선택](./media/cosmosdb-insights-overview/metric-types.png)

### <a name="add-filters-to-metrics"></a>메트릭에 필터 추가

특정 **CollectionName**, **DatabaseName**, **OperationType**, **Region**및 **StatusCode**에 의해 표시 되는 메트릭 및 차트를 필터링 할 수도 있습니다. 메트릭을 필터링 하려면 **필터 추가** 를 선택 하 고 **OperationType** 와 같은 필수 속성을 선택한 후 **쿼리**와 같은 값을 선택 합니다. 그러면 그래프는 선택한 기간에 대해 쿼리 작업에 사용 된 요청 단위를 표시 합니다. 저장 프로시저를 통해 실행 되는 작업은 기록 되지 않으므로 OperationType 메트릭 아래에서 사용할 수 없습니다.

![메트릭 세분성을 선택 하는 필터 추가](./media/cosmosdb-insights-overview/add-metrics-filter.png)

**분할 적용** 옵션을 사용 하 여 메트릭을 그룹화 할 수 있습니다. 예를 들어 다음 이미지에 표시 된 것 처럼 작업 유형별 요청 단위를 그룹화 하 고 모든 작업의 그래프를 한 번에 볼 수 있습니다.

![분할 필터 적용 추가](./media/cosmosdb-insights-overview/apply-metrics-splitting.png)

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB에 대 한 사용률 및 성능 메트릭 보기

모든 구독에서 저장소 계정의 사용률 및 성능을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모니터** 를 검색 하 고 **모니터**를 선택 합니다.

    !["Monitor" 라는 단어가 포함 된 검색 상자 및 속도계 스타일 이미지를 사용 하는 "Monitor" 서비스 라는 드롭다운](./media/cosmosdb-insights-overview/search-monitor.png)

3. **Cosmos DB (미리 보기)** 를 선택 합니다.

    ![Cosmos DB 개요 통합 문서 스크린샷](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>개요

**개요**의 표에는 대화형 Azure Cosmos DB 메트릭이 표시 됩니다. 다음 드롭다운 목록에서 선택한 옵션을 기준으로 결과를 필터링 할 수 있습니다.

* **구독** -Azure Cosmos DB 리소스를 포함 하는 구독만 나열 됩니다.  

* **Cosmos DB** -모두, 하위 집합 또는 단일 Azure Cosmos DB 리소스를 선택할 수 있습니다.

* **시간 범위** -기본적으로는 선택한 항목을 기준으로 지난 4 시간 동안의 정보를 표시 합니다.

드롭다운 목록 아래의 카운터 타일은 선택 된 구독에 있는 Azure Cosmos DB 리소스의 총 수를 롤업 합니다. 통합 문서에서 트랜잭션 메트릭을 보고 하는 열에 대 한 조건적 색 구분 또는 열 지도이 있습니다. 가장 높은 색은 가장 높은 값을 가지 며 가장 밝은 색은 가장 작은 값을 기준으로 합니다. 

Azure Cosmos DB 리소스 중 하나 옆의 드롭다운 화살표를 선택 하면 개별 데이터베이스 컨테이너 수준에서 성능 메트릭에 대 한 분석 결과가 표시 됩니다.

![개별 데이터베이스 컨테이너 및 관련 된 성능 분석 기능이 확장 된 드롭다운](./media/cosmosdb-insights-overview/container-view.png)

파란색으로 강조 표시 된 Azure Cosmos DB 리소스 이름을 선택 하면 연결 된 Azure Cosmos DB 계정의 기본 **개요** 로 이동 합니다. 

### <a name="failures"></a>오류

페이지 맨 위에서 **실패** 를 선택 하면 통합 문서 템플릿의 **실패** 부분이 열립니다. 이러한 요청을 구성 하는 응답의 분포를 포함 하는 총 요청 수를 표시 합니다.

![HTTP 요청 유형별 분석 오류 스크린샷](./media/cosmosdb-insights-overview/failures.png)

| 코드      |  Description       | 
|-----------|:--------------------|
| `200 OK`  | 다음 REST 작업 중 하나가 정상적으로 완료되었습니다. </br>-리소스를 가져옵니다. </br> -리소스에 추가 합니다. </br> -리소스에 게시 합니다. </br> -저장 프로시저를 실행 하기 위해 저장 프로시저 리소스에 게시 합니다.|
| `201 Created` | 리소스를 만들기 위한 POST 작업이 정상적으로 완료되었습니다. |
| `404 Not Found` | 더 이상 없는 리소스에 대해 작업을 수행했습니다. 리소스가 이미 삭제된 경우를 예로 들 수 있습니다. |

상태 코드의 전체 목록은 [AZURE COSMOS DB HTTP 상태 코드 문서](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조 하세요.

### <a name="capacity"></a>용량

페이지 맨 위에 있는 **용량** 을 선택 하면 통합 문서 템플릿의 **용량** 부분이 열립니다. 사용자가 보유 한 문서 수, 시간에 따른 문서 증가, 데이터 사용, 남은 사용 가능한 저장소의 총 크기를 보여 줍니다.  이를 사용 하 여 잠재적 저장소 및 데이터 사용률 문제를 식별할 수 있습니다.

![용량 통합 문서](./media/cosmosdb-insights-overview/capacity.png) 

개요 통합 문서와 마찬가지로 **구독** 열의 Azure Cosmos DB 리소스 옆에 있는 드롭다운을 선택 하면 데이터베이스를 구성 하는 개별 컨테이너의 분석 결과가 표시 됩니다.

### <a name="operations"></a>작업 

페이지 위쪽에서 **작업** 을 선택 하면 통합 문서 템플릿의 **작업** 부분이 열립니다. 요청 유형에 따라 세분화 된 요청을 볼 수 있는 기능을 제공 합니다. 

따라서 아래 예제에서 `eastus-billingint`는 주로 읽기 요청을 받고 있지만 소수의 upsert 및 create 요청을 사용 하는 것을 볼 수 있습니다. 그러나 `westeurope-billingint`은 요청 관점에서 읽기 전용 이지만 이전 4 시간 이상, 통합 문서는 현재 시간 범위 매개 변수를 통해 범위를 지정 합니다.

![운영 통합 문서](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>고정, 내보내기 및 확장

섹션의 오른쪽 위에 있는 압정 아이콘을 선택 하 여 메트릭 섹션 중 하나를 [Azure 대시보드에](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards) 고정할 수 있습니다.

![대시보드 섹션 대시보드에 고정 예](./media/cosmosdb-insights-overview/pin.png)

데이터를 Excel 형식으로 내보내려면 압정 아이콘 왼쪽에 있는 아래쪽 화살표 아이콘을 선택 합니다.

![통합 문서 내보내기 아이콘](./media/cosmosdb-insights-overview/export.png)

통합 문서의 모든 드롭다운 뷰를 확장 하거나 축소 하려면 내보내기 아이콘 왼쪽에 있는 확장 아이콘을 선택 합니다.

![통합 문서 확장 아이콘](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db-preview"></a>Azure Cosmos DB에 대 한 Azure Monitor 사용자 지정 (미리 보기)

이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 수정 된 버전의 복사본을 사용자 지정 통합 문서에 **편집** 하 고 **저장할** ** > 수** 있습니다. 

![사용자 지정 막대](./media/cosmosdb-insights-overview/customize.png)

통합 문서는 사용자가 개인적으로 사용 하는 **내 보고서** 섹션 또는 리소스 그룹에 액세스할 수 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에서 리소스 그룹 내에 저장 됩니다. 사용자 지정 통합 문서를 저장 한 후에는 통합 문서 갤러리로 이동 하 여 시작 해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/cosmosdb-insights-overview/gallery.png)

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 [서비스 상태 알림을](../../service-health/alerts-activity-log-service-notifications.md) 구성 하 여 문제를 검색 하는 데 도움이 되는 자동화 된 경고를 설정 합니다.

* 통합 문서가 지원 되도록 설계 된 시나리오, 새 보고서를 작성 하 고 기존 보고서를 사용자 지정 하는 방법 등에 대해 알아보고 [Azure Monitor 통합 문서를 사용 하 여 대화형 보고서 만들기](../app/usage-workbooks.md)를 검토 하세요.
