---
title: Azure 부서의 범위에서 데이터에 대 한 정보 검색
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 Insights 스캔 보고를 보고 사용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ea12bf8a8d93f14c5364864b97d1173fe8602765
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554430"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 정보 검색

이 방법 가이드에서는 데이터에 대 한 Azure 부서의 범위 스캔 정보 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

* 부서의 범위 계정에서 정보를 봅니다.
* 검색에 대 한 사람의 눈 보기를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

* Azure 리소스를 설정 하 고 계정을 데이터로 채웁니다.
* 데이터 원본에 대 한 검색을 설정 하 고 완료 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-scan-insights"></a>부서의 범위 Scan Insights 사용

Azure 부서의 범위에서 원본 유형을 등록 하 고 검색할 수 있습니다. 검색 정보에서 시간별 검색 상태를 볼 수 있습니다. 정보는 특정 기간 내에 실패, 성공 또는 취소 된 검색 수를 알려 줍니다.

### <a name="view-scan-insights"></a>스캔 정보 보기

1. Azure Portal **Azure 부서의 범위** 인스턴스 화면으로 이동 하 여 부서의 범위 계정을 선택 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Open 부서의 범위 Studio** 타일을 선택 합니다.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure Portal에서 부서의 범위 시작":::

1. 부서의 범위 **홈** 페이지에서 insights **보기** 타일을 선택 하 여 **정보** 영역에 액세스 :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 합니다.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure Portal에서 정보 보기":::

1. **정보** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 영역에서 **검색** 을 선택 하 여 부서의 범위 **Scan Insights** 보고서를 표시 합니다.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status"></a>상태별 검색 수를 표시 하는 상위 수준의 Kpi 보기
 
상위 수준 Kpi는 일정 기간 내에 실행 되는 총 검색을 표시 합니다. 기간은 지난 30 일 동안 기본값으로 설정 됩니다. 그러나 지난 7 일을 선택할 수도 있습니다. 시간 필터에 따라 KPI 값은 검사 수를 적절 하 게 반영 합니다.


선택한 시간 필터 값에 따라 주 또는 그래프의 날짜별로 성공, 실패 및 취소 된 검색의 분포를 볼 수 있습니다.

   :::image type="content" source="./media/scan-insights/scan-insights.png" alt-text="스캔 정보 보기":::

## <a name="next-steps"></a>다음 단계

[Asset Insights](./asset-insights.md) 를 사용 하 여 Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보기
