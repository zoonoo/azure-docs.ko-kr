---
title: Azure 데이터 탐색기 (미리 보기)에 대 한 Azure Monitor | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기 클러스터에 대 한 Azure Monitor 정보를 설명 합니다.
services: azure-monitor
ms.topic: conceptual
ms.date: 01/05/2021
author: lgayhardt
ms.author: lagayhar
ms.openlocfilehash: 6f25ff02dee16812898d77d0e801f927b354dc78
ms.sourcegitcommit: 67b44a02af0c8d615b35ec5e57a29d21419d7668
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/06/2021
ms.locfileid: "97918210"
---
# <a name="azure-monitor-for-azure-data-explorer-preview"></a>Azure 데이터 탐색기 Azure Monitor (미리 보기)

Azure 데이터 탐색기 (미리 보기)에 대 한 Azure Monitor는 클러스터 성능, 작업, 사용 및 오류에 대 한 통합 보기를 제공 하 여 클러스터에 대 한 포괄적인 모니터링을 제공 합니다.
이 문서는 Azure 데이터 탐색기 (미리 보기)에 대 한 Azure Monitor를 등록 하 고 사용 하는 방법을 이해 하는 데 도움이 됩니다.

## <a name="introduction-to-azure-monitor-for-azure-data-explorer-preview"></a>Azure 데이터 탐색기에 대 한 Azure Monitor 소개 (미리 보기)

이 환경을 살펴보기 전에, 이 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다.
-    **규모 관점에서** 클러스터의 기본 메트릭에 대 한 스냅숏 보기를 표시 하 여 쿼리, 수집 및 내보내기 작업의 성능을 쉽게 추적할 수 있습니다.
-   세부적인 분석을 수행 하는 데 도움이 되는 특정 Azure 데이터 탐색기 클러스터의 **드릴 다운 분석**
-    **사용자 지정 가능** -표시 하려는 메트릭을 변경 하 고, 제한과 일치 하는 임계값을 수정 하거나 설정 하 고, 사용자 지정 통합 문서를 저장할 수 있습니다. 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.

## <a name="view-from-azure-monitor-at-scale-perspective"></a>Azure Monitor에서 보기 (배율 측면에서)

Azure Monitor에서 구독의 여러 클러스터에서 쿼리, 수집 및 내보내기 작업에 대 한 메트릭을 포함 하 여 클러스터에 대 한 주요 성능 메트릭을 확인 하 고 성능 문제를 쉽게 파악할 수 있습니다.

모든 구독에서 클러스터의 성능을 보려면 다음 단계를 수행 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure Portal의 왼쪽 창에서 **모니터** 를 선택 하 고, Insights 허브 섹션 아래에서 **Azure 데이터 탐색기 클러스터 (미리 보기)** 를 선택 합니다.

![여러 그래프가 있는 개요 환경의 스크린샷](./media/data-explorer/insights-hub.png)

### <a name="overview-tab"></a>개요 탭

선택한 구독에 대 한 **개요** 탭의 테이블에는 구독 내에 그룹화 된 Azure 데이터 탐색기 클러스터의 대화형 메트릭이 표시 됩니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* 구독-Azure 데이터 탐색기 클러스터가 있는 구독만 나열 됩니다.

* Azure 데이터 탐색기 클러스터 – 기본적으로 최대 5 개의 클러스터만 미리 선택 됩니다. 범위 선택기에서 클러스터를 모두 또는 여러 개 선택 하는 경우 최대 200 클러스터가 반환 됩니다.

* 시간 범위 - 기본적으로는 선택한 항목을 기준으로 지난 24시간의 정보를 표시합니다.

카운터 타일은 드롭다운 목록 아래에서 선택한 구독의 총 Azure 데이터 탐색기 클러스터 수를 롤업 하 고 선택 된 항목 수를 반영 합니다. 열에 대 한 조건부 색 codings 있습니다 (연결 유지, CPU, 수집 사용률 및 캐시 사용률). 주황색으로 구분 된 셀에는 클러스터에 사용할 수 없는 값이 있습니다. 

각 메트릭이 나타내는 사항을 더 잘 이해 하려면 [Azure 데이터 탐색기 메트릭에](https://docs.microsoft.com/azure/data-explorer/using-metrics#cluster-metrics)대 한 설명서를 참조 하는 것이 좋습니다.

### <a name="query-performance-tab"></a>쿼리 성능 탭

이 탭에는 쿼리 기간, 총 동시 쿼리 수 및 제한 된 쿼리의 총 수가 표시 됩니다.

![쿼리 성능 탭의 스크린샷](./media/data-explorer/query-performance.png)

### <a name="ingestion-performance-tab"></a>수집 성능 탭

이 탭은 수집 대기 시간, 성공 수집 결과, 실패 한 수집 결과, 수집 볼륨 및 이벤트/IoT Hub에 대해 처리 된 이벤트를 표시 합니다.

[![수집 성능 탭의 스크린샷](./media/data-explorer/ingestion-performance.png)](./media/data-explorer/ingestion-performance.png#lightbox)

### <a name="streaming-ingest-performance-tab"></a>스트리밍 수집 성능 탭

이 탭에는 평균 데이터 요금, 평균 기간 및 요청 률에 대 한 정보가 제공 됩니다.

### <a name="export-performance-tab"></a>내보내기 성능 탭

이 탭은 연속 내보내기 작업의 내보낸 레코드, 지연, 보류 중인 개수 및 사용률 비율에 대 한 정보를 제공 합니다.

## <a name="view-from-an-azure-data-explorer-cluster-resource-drill-down-analysis"></a>Azure 데이터 탐색기 클러스터 리소스에서 보기 (드릴 다운 분석)

Azure 데이터 탐색기 클러스터에서 직접 Azure 데이터 탐색기 클러스터에 대 한 Azure Monitor에 액세스 하려면 다음을 수행 합니다.

1. Azure Portal에서 **Azure 데이터 탐색기 클러스터** 를 선택 합니다.

2. 목록에서 Azure 데이터 탐색기 클러스터를 선택 합니다. 모니터링 섹션에서 **Insights (미리 보기)** 를 선택 합니다.

이러한 보기에는 Azure Monitor insights 보기에서 Azure 데이터 탐색기 클러스터의 리소스 이름을 선택 하 여 액세스할 수도 있습니다.

Azure 데이터 탐색기 Azure Monitor는 로그와 메트릭을 결합 하 여 글로벌 모니터링 솔루션을 제공 합니다. 로그 기반 시각화를 포함 하 여 사용자는 [Azure 데이터 탐색기 클러스터의 진단 로깅을 사용 하도록 설정 하 고 Log Analytics 작업 영역으로 보내야](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs)합니다. 사용 해야 하는 진단 로그는 **Command**, **Query**, **tabledetails** 및 **TableUsageStatistics** 입니다.

!["모니터링에 대 한 로그 사용" 텍스트를 표시 하는 파란색 단추의 스크린샷](./media/data-explorer/enable-logs.png)


 **개요** 탭에는 다음이 표시 됩니다.

- 메트릭 타일은 클러스터의 가용성 및 전반적인 상태를 강조 표시 하 여 상태를 빠르게 평가 합니다.

- 활성 [Advisor 권장 사항](https://docs.microsoft.com/azure/data-explorer/azure-advisor) 및 [리소스 상태](https://docs.microsoft.com/azure/data-explorer/monitor-with-resource-health) 에 대 한 요약입니다.

- 상위 CPU 및 메모리 소비자와 시간이 지남에 따라 고유한 사용자 수를 보여 주는 차트


[![Azure 데이터 탐색기 클러스터 리소스에서 보기의 스크린샷](./media/data-explorer/overview.png)](./media/data-explorer/overview.png#lightbox)

**키 메트릭** 탭에는 일반 메트릭, 쿼리 관련, 수집 관련 및 스트리밍 수집 관련 메트릭에 따라 그룹화 된 클러스터의 일부 메트릭에 대 한 통합 보기가 표시 됩니다.

[![실패 보기의 스크린샷](./media/data-explorer/key-metrics.png)](./media/data-explorer/key-metrics.png#lightbox)

사용자는 **사용 현황** 탭에서 클러스터의 명령 및 쿼리 성능에 대해 자세히 알아볼 수 있습니다. 이 페이지에서 다음을 수행할 수 있습니다.
 
 - 가장 많은 쿼리를 보내고 가장 많은 CPU와 메모리를 사용 하는 사용자 및 응용 프로그램을 확인 합니다. 따라서 클러스터에서 처리할 가장 많은 쿼리를 전송 하는 사용자를 이해할 수 있습니다.
 - 실패 한 쿼리의 상위 사용자 및 응용 프로그램을 식별 합니다.
 - 사용자 및 응용 프로그램을 기준으로 지난 16 일간의 일별 평균 기록에 비해 쿼리 수의 최근 변경 내용을 확인 합니다.
 - 사용자, 응용 프로그램 및 명령 유형별로 쿼리, 메모리 및 CPU 소비 수의 추세 및 최대 수를 식별 합니다.

[![명령 및 쿼리 수를 기준으로 상위 응용 프로그램의 도넛형 차트, 명령 및 쿼리 수 별 상위 주체, 명령 유형별 상위 명령 등의 작업 보기 스크린샷](./media/data-explorer/usage.png)](./media/data-explorer/usage.png#lightbox)

[![응용 프로그램별 쿼리 수, 응용 프로그램별 총 메모리 및 응용 프로그램별 총 CPU 수의 꺾은선형 차트가 포함 된 작업 보기의 스크린샷](./media/data-explorer/usage-2.png)](./media/data-explorer/usage-2.png#lightbox)

**테이블** 탭은 클러스터에 있는 테이블의 최신 및 기록 속성을 표시 합니다. 가장 많은 공간을 사용 하는 테이블을 확인 하 고 테이블 크기, 핫 데이터 및 시간별 행 수를 기준으로 증가 기록을 추적할 수 있습니다.

사용자는 **캐시** 탭을 사용 하 여 실제 쿼리의 검색 백 패턴을 분석 하 고 각 테이블에 대해 구성 된 캐시 정책과 비교할 수 있습니다. 쿼리가 아닌 대부분의 쿼리 및 테이블에서 사용 하는 테이블을 식별 하 고 그에 따라 캐시 정책을 적용할 수 있습니다. Azure Advisor의 특정 테이블에 대 한 특정 캐시 정책 권장 사항을 확인할 수 있습니다. 현재는 최근 쿼리를 기준으로 최근 30 일 동안의 조회를 확인 하 고 최소 95%의 쿼리는 최적화 되지 않은 캐시 정책을 기반으로 하 여 [기본 Azure Advisor 대시보드에서](https://docs.microsoft.com/azure/data-explorer/azure-advisor#use-the-azure-advisor-recommendations)만 캐시 권장 사항을 사용할 수 있습니다. Azure Advisor의 캐시 감소 권장 사항은 클러스터가 낮은 CPU와 낮은 수집 사용률을 포함 하는 클러스터에 사용할 수 있습니다. 즉, 데이터 용량이 높아 클러스터를 확장 하거나 축소할 수 없습니다.

[![캐시 세부 정보의 스크린샷](./media/data-explorer/cache-tab.png)](./media/data-explorer/cache-tab.png#lightbox)

## <a name="pin-to-azure-dashboard"></a>Azure 대시보드에 고정

섹션의 오른쪽 위에 있는 압정 아이콘을 선택 하 여 "크기 조정" 관점의 메트릭 섹션 중 하나를 Azure 대시보드에 고정할 수 있습니다.

![고정 아이콘이 선택된 스크린샷](./media/data-explorer/pin.png)

## <a name="customize-azure-monitor-for-azure-data-explorer-cluster"></a>Azure 데이터 탐색기 클러스터에 대 한 Azure Monitor 사용자 지정

이 섹션에서는 데이터 분석 요구 사항을 지원하도록 사용자 지정하기 위해 통합 문서를 편집하는 일반적인 시나리오를 중점적으로 설명합니다.
* 항상 특정 구독 또는 Azure 데이터 탐색기 클러스터를 선택 하도록 통합 문서 범위를 선택 합니다.
* 그리드에서 메트릭 변경
* 임계값 또는 색 렌더링/코딩 변경

위쪽 도구 모음에서 **사용자 지정** 단추를 선택하여 편집 모드를 사용하도록 설정하면 사용자 지정을 시작할 수 있습니다.

![사용자 지정 단추의 스크린샷](./media/data-explorer/customize.png)

사용자 지정 항목은 게시된 통합 문서에서 기본 구성을 덮어쓰지 않도록 사용자 지정 통합 문서에 저장됩니다. 통합 문서는 리소스 그룹 내에서 프라이빗 위치인 [내 보고서] 섹션 또는 리소스 그룹에 대한 액세스 권한을 갖고 있는 모든 사용자가 액세스할 수 있는 [공유 보고서] 섹션에 저장됩니다. 사용자 지정 통합 문서를 저장한 후에는 통합 문서 갤러리로 이동하여 통합 문서를 시작해야 합니다.

![통합 문서 갤러리의 스크린샷](./media/data-explorer/gallery.png)

## <a name="troubleshooting"></a>문제 해결

일반적인 문제 해결 지침은 전용 통합 문서 기반 insights [문제 해결 문서](troubleshoot-workbooks.md)를 참조 하세요.

이 섹션은 Azure 데이터 탐색기 클러스터 (미리 보기)에 대 한 Azure Monitor를 사용할 때 발생할 수 있는 일반적인 문제 중 일부에 대 한 진단 및 문제 해결에 도움이 됩니다. 아래 목록에서 특정 문제와 관련된 정보를 찾을 수 있습니다.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>구독 선택기에 내 구독이 전부 표시되지 않는 이유는 무엇인가요?

Azure 데이터 탐색기 클러스터가 포함 된 구독만 표시 됩니다. 선택 된 구독 필터는 Azure Portal 헤더의 "디렉터리 + 구독"에서 선택 합니다.

![구독 필터의 스크린샷](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="why-do-i-not-see-any-data-for-my-azure-data-explorer-cluster-under-the-usage-tables-or-cache-sections"></a>Azure 데이터 탐색기 클러스터에 대 한 데이터가 사용, 테이블 또는 캐시 섹션에 표시 되지 않는 이유는 무엇 인가요?

로그 기반 데이터를 보려면 모니터링 하려는 각 Azure 데이터 탐색기 클러스터에 대해 [진단 로그를 사용 하도록 설정](https://docs.microsoft.com/azure/data-explorer/using-diagnostic-logs?tabs=commands-and-queries#enable-diagnostic-logs) 해야 합니다. 각 클러스터에 대 한 진단 설정에서이 작업을 수행할 수 있습니다. Log Analytics 작업 영역으로 데이터를 전송 해야 합니다. 사용 해야 하는 진단 로그는 Command, Query, TableDetails 및 TableUsageStatistics입니다.

### <a name="i-have-already-enabled-logs-for-my-azure-data-explorer-cluster-why-am-i-still-unable-to-see-my-data-under-commands-and-queries"></a>Azure 데이터 탐색기 클러스터에 대 한 로그를 이미 사용 하도록 설정 했습니다. 여전히 명령 및 쿼리 아래의 데이터를 볼 수 없는 이유는 무엇입니까?

현재 진단 로그는 소급 작동 하지 않으므로 Azure 데이터 탐색기에 대 한 작업이 수행 된 후에만 데이터가 표시 되기 시작 합니다. 따라서 Azure 데이터 탐색기 클러스터가 활성화 된 방법에 따라 몇 시간에서 하루까지 시간이 걸릴 수 있습니다.


## <a name="next-steps"></a>다음 단계

[Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../platform/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
