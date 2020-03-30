---
title: 로그 분석 작업 영역에서 Azure 활동 로그 수집
description: Azure 모니터 로그에 있는 Azure 활동 로그를 수집하고 모니터링 솔루션을 사용하여 모든 Azure 구독에서 Azure 활동 로그를 분석하고 검색합니다.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/30/2019
ms.openlocfilehash: 407bff10e2480c5210d3057bcccd6c60e591c165
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055317"
---
# <a name="collect-and-analyze-azure-activity-logs-in-log-analytics-workspace-in-azure-monitor"></a>Azure 모니터의 로그 분석 작업 영역에서 Azure 활동 로그 수집 및 분석

> [!WARNING]
> 이제 리소스 로그를 수집하는 방법과 유사한 진단 설정을 사용하여 활동 로그를 Log Analytics 작업 영역에 수집할 수 있습니다. Azure [모니터의 로그 분석 작업 영역에서 Azure 활동 로그 수집 및 분석](diagnostic-settings-legacy.md)

[Azure 활동 로그는](platform-logs-overview.md) Azure 구독에서 발생한 구독 수준 이벤트에 대한 통찰력을 제공합니다. 이 문서에서는 활동 로그를 Log Analytics 작업 영역으로 수집하는 방법과 이 데이터를 분석하기 위한 로그 쿼리 및 보기를 제공하는 활동 로그 분석 [모니터링 솔루션을](../insights/solutions.md)사용하는 방법에 대해 설명합니다. 

활동 로그를 로그 분석 작업 영역에 연결하면 다음과 같은 이점이 있습니다.

- 여러 Azure 구독에서 활동 로그를 분석을 위해 한 위치로 통합합니다.
- 90일 이상 활동 로그 항목을 저장합니다.
- 활동 로그 데이터를 Azure Monitor에서 수집한 다른 모니터링 데이터와 상호 연관시다.
- [로그 쿼리를](../log-query/log-query-overview.md) 사용하여 복잡한 분석을 수행하고 활동 로그 항목에 대한 심층적인 통찰력을 얻을 수 있습니다.

## <a name="connect-to-log-analytics-workspace"></a>로그 분석 작업 영역에 연결
단일 작업 영역을 동일한 Azure 테넌트의 여러 구독에 대한 활동 로그에 연결할 수 있습니다. 여러 테넌티에서 수집하는 경우 [다른 Azure Active Directory 테넌의 구독에서 로그 분석 작업 영역으로 Azure 활동 로그 수집을](activity-log-collect-tenants.md)참조하십시오.

> [!IMPORTANT]
> Microsoft.OperationsInsights 및 Microsoft.OperationsManagement 리소스 공급자가 구독에 등록되지 않은 경우 다음 절차에 오류가 발생할 수 있습니다. 이러한 [공급자를](../../azure-resource-manager/management/resource-providers-and-types.md) 등록하려면 Azure 리소스 공급자 및 형식을 참조하세요.

다음 절차를 사용하여 활동 로그를 로그 분석 작업 영역에 연결합니다.

1. Azure 포털의 **로그 분석 작업 영역** 메뉴에서 작업 영역을 선택하여 활동 로그를 수집합니다.
1. 작업 **영역** 의 메뉴의 작업 영역 데이터 원본 섹션에서 Azure **활동 로그를**선택합니다.
1. 연결할 구독을 클릭합니다.

    ![작업 영역](media/activity-log-export/workspaces.png)

1. **연결을** 클릭하여 구독의 활동 로그를 선택한 작업 영역에 연결합니다. 구독이 이미 다른 작업 영역에 연결되어 있는 경우 먼저 **연결을** 끊고 연결을 끊습니다.

    ![작업 영역 연결](media/activity-log-export/connect-workspace.png)

## <a name="analyze-in-log-analytics-workspace"></a>로그 분석 작업 영역에서 분석
활동 로그를 로그 분석 작업 영역에 연결하면 [로그 쿼리로](../log-query/log-query-overview.md)검색할 수 있는 **AzureActivity라는** 테이블에 항목이 작업 영역에 기록됩니다. 이 테이블의 구조는 [로그 항목의 범주에](activity-log-view.md#categories-in-the-activity-log)따라 다릅니다. 각 범주에 대한 설명은 [Azure 활동 로그 이벤트 스키마를](activity-log-schema.md) 참조하십시오.

## <a name="activity-logs-analytics-monitoring-solution"></a>활동 로그 분석 모니터링 솔루션
Azure Log Analytics 모니터링 솔루션에는 로그 분석 작업 영역의 활동 로그 레코드를 분석하기 위한 여러 로그 쿼리 및 보기가 포함됩니다.

### <a name="install-the-solution"></a>솔루션 설치
모니터링 솔루션 [설치시](../insights/solutions.md#install-a-monitoring-solution) 절차를 사용하여 **활동 로그 분석 솔루션을 설치합니다.** 추가 구성이 필요하지 않습니다.

### <a name="use-the-solution"></a>솔루션 사용
모니터링 솔루션은 Azure 포털의 **모니터** 메뉴에서 액세스됩니다. **인사이트** 섹션에서 **자세히 선택하여** 솔루션 타일로 개요 페이지를 **엽니다.** **Azure 활동 로그** 타일에는 작업 영역에 **AzureActivity** 레코드 수가 표시됩니다.

![Azure 활동 로그 타일](media/collect-activity-logs/azure-activity-logs-tile.png)


Azure **활동 로그** 타일을 클릭하여 Azure 활동 로그 보기를 **엽니다.** 뷰에는 다음 표의 시각화 부분이 포함됩니다. 각 부품에는 지정된 시간 범위에 대한 해당 부품의 조건과 일치하는 최대 10개의 항목이 나열됩니다. 부품 하단의 **모든 참조를** 클릭하여 일치하는 모든 레코드를 반환하는 로그 쿼리를 실행할 수 있습니다.

![Azure 활동 로그 대시보드](media/collect-activity-logs/activity-log-dash.png)

| 시각화 부분 | 설명 |
| --- | --- |
| Azure 활동 로그 항목 | 선택한 날짜 범위에 대한 상위 Azure 활동 로그 항목 레코드 합계의 막대 차트를 표시하고 상위 10개 활동 호출자의 목록을 표시합니다. 가로 막대형 차트를 클릭하면 `AzureActivity`에 대한 로그 검색이 실행됩니다. 호출자 항목을 클릭하여 해당 항목에 대한 모든 활동 로그 항목을 반환하는 로그 검색을 실행합니다. |
| 상태별 활동 로그 | 선택한 날짜 범위에 대한 Azure 활동 로그 상태에 대한 도넛형 차트와 상위 10개 상태 레코드 목록을 표시합니다. 차트를 클릭하여 에 대한 `AzureActivity | summarize AggregatedValue = count() by ActivityStatus`로그 쿼리를 실행합니다. 상태 항목을 클릭하여 해당 상태 레코드에 대한 모든 활동 로그 항목을 반환하는 로그 검색을 실행합니다. |
| 리소스별 활동 로그 | 활동 로그를 사용 하 여 리소스의 총 수를 표시 하 고 각 리소스에 대 한 레코드 수상위 10 리소스를 나열 합니다. 전체 영역을 클릭하여 `AzureActivity | summarize AggregatedValue = count() by Resource`에 대해 로그 검색을 실행하면 솔루션에 사용할 수 있는 모든 Azure 리소스가 표시됩니다. 리소스를 클릭하여 해당 리소스에 대한 모든 활동 레코드를 반환하는 로그 쿼리를 실행합니다. |
| 리소스 공급자별 활동 로그 | 활동 로그를 생성하는 총 리소스 공급자 수를 표시하고 상위 10개 목록을 표시합니다. 전체 영역을 클릭하여 모든 Azure `AzureActivity | summarize AggregatedValue = count() by ResourceProvider`리소스 공급자를 보여 주며 에 대한 로그 쿼리를 실행합니다. 리소스 공급자를 클릭하여 공급자에 대한 모든 활동 레코드를 반환하는 로그 쿼리를 실행합니다. |

## <a name="next-steps"></a>다음 단계

- [활동 로그에](platform-logs-overview.md)대해 자세히 알아보세요.
- Azure 모니터 [데이터 플랫폼에](data-platform.md)대해 자세히 알아봅니다.
- [로그 쿼리를](../log-query/log-query-overview.md) 사용하여 활동 로그에서 자세한 정보를 볼 수 있습니다.
