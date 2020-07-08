---
title: Cosmos DB용 Azure Monitor를 사용하여 Azure Cosmos DB 모니터링 | Microsoft Docs
description: 이 문서에서는 Cosmos DB 소유자가 CosmosDB 계정의 성능 및 사용 문제를 신속하게 이해하도록 도와주는 Cosmos DB용 Azure Monitor 기능에 대해 설명합니다.
ms.subservice: ''
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/11/2020
ms.openlocfilehash: 3fd4bcd13b4d92d2c6e61d10ae9e62996b5dfcf5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84945497"
---
# <a name="explore-azure-monitor-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Monitor 살펴보기

Azure Cosmos DB용 Azure Monitor는 통합 대화형 환경에서 모든 Azure Cosmos DB 리소스의 전체 성능, 실패, 용량 및 운영 상태를 보여줍니다. 이 문서는 새로운 모니터링 환경의 이점과 조직의 고유한 요구 사항에 맞게 환경을 수정하고 조정 하는 방법을 이해하는 데 도움이 됩니다.   

## <a name="introduction"></a>소개

환경을 살펴보기 전에, 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다. 

다음을 제공합니다.

* 단일 위치의 모든 구독에 대한 Azure Cosmos DB 리소스의 **규모에 따른 관점** - 평가하려는 구독과 리소스로 범위를 좁힐 수 있습니다.

* 특정 Azure CosmosDB 리소스의 **드릴다운 분석** - 문제를 진단하거나 범주(사용률, 실패, 용량 및 작업)별로 자세한 분석을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택하면 관련 Azure Cosmos DB 메트릭을 자세히 볼 수 있습니다.  

* **사용자 지정** - 이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 표시할 메트릭을 변경하고, 제한과 일치하는 임계값을 수정하거나 설정한 다음, 사용자 지정 통합 문서에 저장할 수 있습니다. 그런 다음, 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

Azure Cosmos DB 메트릭은 기본적으로 수집되므로 아무것도 설정하거나 구성할 필요가 없습니다.

>[!NOTE]
>이 기능은 무료로 사용할 수 있으며 [Azure Monitor 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 것처럼 Azure Monitor 필수 기능을 구성하거나 설정하는 경우에만 요금이 부과됩니다.

## <a name="view-utilization-and-performance-metrics-for-azure-cosmos-db"></a>Azure Cosmos DB의 사용률 및 성능 메트릭 보기

모든 구독의 스토리지 계정 사용률 및 성능을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모니터**를 검색하고 **모니터**를 선택합니다.

    !["모니터"라는 단어가 포함된 검색 상자와 속도계 스타일 이미지를 사용하는 서비스 "모니터"라고 적힌 드롭다운](./media/cosmosdb-insights-overview/search-monitor.png)

3. **Cosmos DB**를 선택합니다.

    ![Cosmos DB 개요 통합 문서의 스크린샷](./media/cosmosdb-insights-overview/cosmos-db.png)

### <a name="overview"></a>개요

**개요**에서 표는 대화형 Azure Cosmos DB 메트릭을 보여줍니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* **구독** - Azure Cosmos DB 리소스가 있는 구독만 나열됩니다.  

* **Cosmos DB** - 모두, 하위 집합 또는 단일 Azure Cosmos DB 리소스를 선택할 수 있습니다.

* **시간 범위** - 기본적으로는 선택한 항목을 기준으로 지난 4시간의 정보를 표시합니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독에 있는 Azure Cosmos DB 리소스의 총 수를 롤업합니다. 통합 문서에는 트랜잭션 메트릭을 보고하는 열에 대한 조건부 컬러 코딩 또는 열 지도가 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다. 

Azure Cosmos DB 리소스 중 하나의 옆에 있는 드롭다운 화살표를 선택하면 다음과 같이 개별 데이터베이스 컨테이너 수준에서 성능 메트릭 분석 결과가 표시됩니다.

![개별 데이터베이스 컨테이너 및 관련 성능 분석 정보를 표시하는 확장된 드롭다운](./media/cosmosdb-insights-overview/container-view.png)

파란색으로 강조 표시된 Azure Cosmos DB 리소스 이름을 선택하면 연결된 Azure Cosmos DB 계정의 기본 **개요**로 이동됩니다. 

### <a name="failures"></a>오류

페이지 맨 위에서 **실패**를 선택하면 통합 문서 템플릿의 **실패** 부분이 열립니다. 이러한 요청을 구성하는 응답의 분포가 포함된 총 요청 수가 표시됩니다.

![HTTP 요청 유형별 분석이 포함된 실패 스크린샷](./media/cosmosdb-insights-overview/failures.png)

| 코드      |  Description       | 
|-----------|:--------------------|
| `200 OK`  | 다음 REST 작업 중 하나가 정상적으로 완료되었습니다. </br>- 리소스에 대한 GET </br> - 리소스에 대한 PUT </br> - 리소스에 대한 POST </br> - 저장 프로시저를 실행하기 위한 저장 프로시저에 대한 POST|
| `201 Created` | 리소스를 만들기 위한 POST 작업이 정상적으로 완료되었습니다. |
| `404 Not Found` | 더 이상 없는 리소스에 대해 작업을 수행했습니다. 리소스가 이미 삭제된 경우를 예로 들 수 있습니다. |

상태 코드의 전체 목록은 [Azure Cosmos DB HTTP 상태 코드 문서](https://docs.microsoft.com/rest/api/cosmos-db/http-status-codes-for-cosmosdb)를 참조하세요.

### <a name="capacity"></a>용량

페이지 맨 위에서 **용량**을 선택하면 통합 문서 템플릿의 **용량** 부분이 열립니다. 현재 보유한 문서 수, 시간에 따른 문서 증가 속도, 데이터 사용량, 남아 있는 사용 가능한 스토리지 양을 보여줍니다.  이를 사용하여 잠재적 스토리지 및 데이터 사용률 문제를 식별할 수 있습니다.

![용량 통합 문서](./media/cosmosdb-insights-overview/capacity.png) 

개요 통합 문서와 마찬가지로, **구독** 열의 Azure Cosmos DB 리소스 옆에 있는 드롭다운을 선택하면 데이터베이스를 구성하는 개별 컨테이너별로 분석 결과가 표시됩니다.

### <a name="operations"></a>작업 

페이지 맨 위에서 **작업**을 선택하면 통합 문서 템플릿의 **작업** 부분이 열립니다. 유형별로 세분화된 요청을 볼 수 있는 기능을 제공합니다. 

따라서 아래 예제에서 `eastus-billingint`는 주로 read 요청을 수신하지만, 약간의 upsert 및 create 요청도 수신하는 것을 볼 수 있습니다. 반면 `westeurope-billingint`는 적어도 시간 범위 매개 변수를 통해 통합 문서의 현재 범위로 지정된 지난 4시간 동안은 요청 관점에서 읽기 전용입니다.

![작업 통합 문서](./media/cosmosdb-insights-overview/operation.png) 

## <a name="pin-export-and-expand"></a>고정, 내보내기 및 확장

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 메트릭 섹션 중 하나를 [Azure 대시보드](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)에 고정할 수 있습니다.

![메트릭 섹션 대시보드에 고정 예제](./media/cosmosdb-insights-overview/pin.png)

데이터를 Excel 형식으로 내보내려면 압정 아이콘 왼쪽에 있는 아래쪽 화살표 아이콘을 선택합니다.

![통합 문서 내보내기 아이콘](./media/cosmosdb-insights-overview/export.png)

통합 문서의 모든 드롭다운 보기를 확장 또는 축소하려면 다음과 같이 내보내기 아이콘 왼쪽에 있는 확장 아이콘을 선택합니다.

![통합 문서 확장 아이콘](./media/cosmosdb-insights-overview/expand.png)

## <a name="customize-azure-monitor-for-azure-cosmos-db"></a>Azure Cosmos DB용 Azure Monitor 사용자 지정

이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 구축되었으므로 수정된 버전의 복사본을 **사용자 지정** > **편집** 및 사용자 지정 통합 문서에 **저장**하는 기능을 제공합니다. 

![사용자 지정 막대](./media/cosmosdb-insights-overview/customize.png)

통합 문서는 리소스 그룹 내에서 프라이빗 위치인 **내 보고서** 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 **공유 보고서** 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 통합 문서를 시작해야 합니다.

![명령 모음에서 통합 문서 갤러리 시작](./media/cosmosdb-insights-overview/gallery.png)

## <a name="troubleshooting"></a>문제 해결

문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../platform/alerts-metric.md) 및 [서비스 상태 알림](../../service-health/alerts-activity-log-service-notifications.md)을 구성하여 문제 발견에 도움이 되는 자동 경고를 설정합니다.

* [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
