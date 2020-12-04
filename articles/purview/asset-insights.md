---
title: Azure 부서의 범위 (미리 보기)에서 데이터에 대 한 Asset insights
description: 이 방법 가이드에서는 데이터에서 부서의 범위 Insights 자산 보고를 보고 사용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: ac93d835b8dbdd5a12d031825dcb879160df5e95
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575349"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 Asset insights

이 방법 가이드에서는 데이터에 대 한 부서의 범위 Asset insights 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 부서의 범위 계정에서 정보를 봅니다.
> * 데이터의 멋진 뷰를 가져옵니다.
> * 드릴 다운 하 여 더 많은 자산 개수 정보를 확인 합니다.

## <a name="prerequisites"></a>필수 조건

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

* Azure 리소스를 설정 하 고 계정을 데이터로 채웁니다.

* 원본 유형에 대 한 검색을 설정 하 고 완료 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-asset-insights"></a>부서의 범위 Asset Insights 사용

Azure 부서의 범위에서 원본 유형을 등록 하 고 검색할 수 있습니다. 검색이 완료 되 면 자산 정보에서 자산 배포를 볼 수 있습니다. 그러면 분류 및 리소스 집합을 기준으로 데이터 공간 상태를 알 수 있습니다. 데이터 크기가 변경 되었는지 여부도 알려줍니다.

> [!NOTE]
> 원본 유형을 검사 한 후에는 새 자산을 반영 하기 위해 자산 정보를 최대 1 시간까지 제공 합니다.

1. Azure Portal에서 Azure 부서의 범위 리소스로 이동 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Open 부서의 범위 Studio** 타일을 선택 합니다.

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Azure Portal에서 부서의 범위 시작":::

1. 부서의 범위 **홈** 페이지에서 insights **보기** 타일을 선택 하 여 **정보** 영역에 액세스 :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 합니다.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Azure Portal에서 정보 보기":::

1. **정보** :::image type="icon" source="media/asset-insights/ico-insights.png" border="false"::: 영역에서 **자산** 을 선택 하 여 부서의 범위 **Asset Insights** 보고서를 표시 합니다.

### <a name="view-asset-insights"></a>Asset Insights 보기

1. 기본 **Asset Insights** 페이지에는 다음과 같은 영역이 표시 됩니다.

2. 원본 유형, 분류 된 자산 및 검색 된 자산을 표시 하는 높은 수준의 KPI
 
3. 첫 번째 그래프는 **원본 유형별 자산** 을 보여 줍니다.

4. 원본 유형별 자산 분포를 확인 합니다. 원본 유형별 자산 분포를 보려면 분류 또는 전체 분류 범주를 선택 합니다. 
 
5. 자세히 보려면 원본 유형 및 자산 수의 표 형식으로 표시 되는 **자세히 보기** 를 선택 합니다. 분류 필터는이 페이지로 전달 됩니다.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Asset Insights에서 Kpi 및 그래프 보기":::
 
6. 자산 수가 포함 된 상위 폴더를 보려는 특정 원본을 선택 하세요. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="원본 유형 선택":::
 
7. 위에서 선택한 원본 유형 내의 최상위 폴더에 대 한 총 자산을 선택 합니다.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="파일 경로 보기":::

8. 폴더 내의 파일 목록을 봅니다. 이동 crumb를 사용 하 여 정보로 다시 이동 합니다.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="자산 목록 보기":::  

### <a name="file-based-source-types"></a>파일 기반 소스 형식
Asset Insights의 다음 두 그래프는 파일 기반 원본 유형의 분포를 보여 줍니다. **원본 형식 내에서 파일 형식의 크기 추세 (GB)** 라고 하는 첫 번째 그래프는 지난 30 일 동안의 상위 파일 형식 크기 추세를 표시 합니다. 
 
1. 원본 내에서 파일 형식을 보려면 원본 형식을 선택 합니다. 
 
1. **자세히 보기** 를 선택 하 여 현재 데이터 크기, 크기 변경, 현재 자산 수 및 자산 수 변화를 확인 합니다.
 
   > [!NOTE]
   > 최근 30 일 동안 검색을 한 번만 실행 하거나 분류 추가/제거와 같은 카탈로그 변경이 30 일 동안 한 번만 발생 한 경우 위의 변경 정보는 빈 상태로 표시 됩니다.

1. 원본 유형을 클릭 하면 주요 자산 수가 변경 된 상위 폴더가 표시 됩니다.

1. 경로를 선택 하 여 자산 목록을 표시 합니다.

파일 기반 소스 형식의 두 번째 그래프는 **_리소스 집합과 연결 되지 않은 파일_** 입니다. 모든 파일이 리소스 집합에 롤업되는 것으로 간주 되는 경우이 그래프는 롤업되는 자산이 무엇 인지 파악 하는 데 도움이 될 수 있습니다. 누락 된 자산은 폴더의 잘못 된 파일 패턴을 나타낼 수 있습니다. 다른 그래프의 경우와 동일한 단계를 수행 하 여 파일에 대 한 자세한 내용을 확인 합니다.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="파일 기반 자산 보기":::  

## <a name="next-steps"></a>다음 단계

[스캔 정보](./scan-insights.md) 를 사용 하 여 Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보기
