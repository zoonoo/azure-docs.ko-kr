---
title: Azure 부서의 범위에서 데이터에 대 한 정보 검색
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 Insights 스캔 보고를 보고 사용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 7807659a30127f39bb79ad99bdb733c12eb1d25d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "100548691"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 정보 검색

이 방법 가이드에서는 데이터에 대 한 Azure 부서의 범위 스캔 정보 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 부서의 범위 계정에서 정보를 봅니다.
> * 검색에 대 한 사람의 눈 보기를 가져옵니다.

## <a name="prerequisites"></a>필수 구성 요소

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

* Azure 리소스를 설정 하 고 계정을 데이터로 채웁니다.
* 데이터 원본에 대 한 검색을 설정 하 고 완료 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-scan-insights"></a>부서의 범위 Scan Insights 사용

Azure 부서의 범위에서 원본 유형을 등록 하 고 검색할 수 있습니다. 검색 정보에서 시간별 검색 상태를 볼 수 있습니다. 정보는 특정 기간 내에 실패, 성공 또는 취소 된 검색 수를 알려 줍니다.

### <a name="view-scan-insights"></a>검사 인사이트 보기

1. Azure Portal **Azure 부서의 범위** 인스턴스 화면으로 이동 하 여 부서의 범위 계정을 선택 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Open 부서의 범위 Studio** 타일을 선택 합니다.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure Portal에서 부서의 범위 시작":::

1. 부서의 범위 **홈** 페이지에서 insights **보기** 타일을 선택 하 여 **정보** 영역에 액세스 :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 합니다.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure Portal에서 정보 보기":::

1. **정보** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 영역에서 **검색** 을 선택 하 여 부서의 범위 **Scan Insights** 보고서를 표시 합니다.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>개략적인 Kpi를 보고 상태별 검색 수를 표시 하 고 각 검색에 심층 검색
 
1. 상위 수준 Kpi는 일정 기간 내에 실행 되는 총 검색을 표시 합니다. 기간은 지난 30 일 동안 기본값으로 설정 됩니다. 그러나 지난 7 일을 선택할 수도 있습니다. 시간 필터에 따라 KPI 값은 검사 수를 적절 하 게 반영 합니다.


1. 선택한 시간 필터 값에 따라 주 또는 그래프의 날짜별로 성공, 실패 및 취소 된 검색의 분포를 볼 수 있습니다.

1. 그래프의 아래쪽에는 자세히 살펴볼 수 있는 **보기 추가** 링크가 있습니다. 이 링크를 통해 스캔 정보 활용 환경 내에서  **검색 상태** 페이지가 열립니다. 여기에서 최근 30 일 동안 검사 이름 및 성공, 실패 또는 취소 횟수를 확인할 수 있습니다.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="시간별 검색 상태 보기":::

4. Azure 부서의 범위의 **원본** 환경 내에서 검색 기록과 연결 되는 **검색 이름을** 클릭 하 여 특정 검색을 추가로 탐색할 수 있습니다. 실행 기록 페이지에서 추가 오류 조사에 도움이 되는 실행 ID를 가져올 수 있습니다.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="검사 세부 정보 보기":::

5. 마지막으로, 실행 기록 페이지의 왼쪽 위 모서리에 있는 이동 crumb 따라 검색 Insights **검색 상태** 페이지로 돌아갈 수 있습니다.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="검색 기록 보기"::: 

## <a name="next-steps"></a>다음 단계

* Azure 부서의 범위 **insights** 에 대 한 자세한 내용은 [데이터 통찰력](./concept-insights.md)

* [데이터 원본 관리](./manage-data-sources.md) 를 사용 하 여 Azure 부서의 범위의 **원본** 환경에 대 한 자세한 정보