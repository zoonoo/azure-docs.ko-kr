---
title: Data Catalog 계보 사용자 가이드 (미리 보기)
description: 이 문서에서는 Azure 부서의 범위의 카탈로그 계보 기능에 대 한 개요를 제공 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/29/2020
ms.openlocfilehash: 47c5ad18f50bd25dd3bf351090ffc9ef5d54f0b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "102202912"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Azure 부서의 범위 Data Catalog 계보 사용자 가이드

이 문서에서는 Azure 부서의 범위 Data Catalog의 데이터 계보 기능에 대 한 개요를 제공 합니다.

## <a name="background"></a>배경

Azure 부서의 범위의 플랫폼 기능 중 하나는 데이터 프로세스에서 만든 데이터 집합 간의 계보를 표시 하는 기능입니다. Data Factory, 데이터 공유 및 Power BI 데이터의 계보를 캡처하는 것과 같은 시스템입니다. 사용자 지정 계보 보고는 Atlas 후크 및 REST API 통해서도 지원 됩니다.

## <a name="lineage-collection"></a>계보 컬렉션 
 엔터프라이즈 데이터 시스템에서 Azure 부서의 범위에 수집 된 메타 데이터는 종단 간 데이터 계보를 표시 하도록 연결 되어 됩니다. 부서의 범위로 계보를 수집 하는 데이터 시스템은 다음과 같은 세 가지 유형으로 광범위 하 게 분류 됩니다.

### <a name="data-processing-system"></a>데이터 처리 시스템
데이터 통합 및 ETL 도구는 실행 시 Azure 부서의 범위에 계보를 푸시할 수 있습니다. Data Factory, 데이터 공유, Synapse, Azure Databricks 등의 도구는이 데이터 시스템 범주에 속합니다. 데이터 처리 시스템은 다른 데이터베이스 및 저장소 솔루션의 원본으로 데이터 집합을 참조 하 여 대상 데이터 집합을 만듭니다. 현재 계보에 대해 부서의 범위와 통합 된 데이터 처리 시스템의 목록은 아래 표에 나와 있습니다.


| 데이터 처리 시스템 | 지원 되는 범위 |
| ---------------------- | ------------|
| Azure 데이터 팩터리 | [복사 활동](how-to-link-azure-data-factory.md#data-factory-copy-activity-support) <br> [데이터 흐름 작업](how-to-link-azure-data-factory.md#data-factory-data-flow-support) <br> [SSIS 패키지 실행 작업](how-to-link-azure-data-factory.md#data-factory-execute-ssis-package-support) |
| Azure Data Share | [스냅숏 공유](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>데이터 저장소 시스템
SQL Server, Teradata, SAP 등의 데이터베이스 & 저장소 솔루션에는 스크립팅 언어를 사용 하 여 데이터를 변환 하는 쿼리 엔진이 있습니다. 저장 프로시저의 데이터 계보는에서 다른 시스템의 계보를 사용 하 여 부서의 범위 및 연결 되어으로 수집 됩니다.

| 데이터 저장소 시스템 | 지원 되는 범위 |
| ---------------------- | ------------|
| Teradata | 저장 프로시저

### <a name="data-analytics--reporting-systems"></a>데이터 분석 & 보고 시스템
Azure ML과 같은 데이터 시스템 및 Azure 부서의 범위에 계보를 Power BI 합니다. 이러한 시스템은 저장소 시스템의 데이터 집합을 사용 하 고 메타 모델을 통해 처리 하 여 BI 대시보드, ML 실험 등을 만듭니다.

| 데이터 분석 & 보고 시스템 | 지원 되는 범위 |
| ---------------------- | ------------|
| Power BI | [데이터 집합, 데이터 흐름, 보고서 & 대시보드](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>계보 시작

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE1XVQS]

부서의 범위의 계보에는 데이터 집합 및 프로세스가 포함 됩니다. 데이터 집합은 노드 라고도 하며, 프로세스를 가장자리 라고도 합니다.

* **Dataset (Node)**: 프로세스에 대 한 입력으로 제공 되는 데이터 집합 (구조화 또는 비구조적)입니다. 예를 들어 SQL 테이블, Azure blob 및 파일 (예: .csv 및 .xml)은 모두 데이터 집합으로 간주 됩니다. 부서의 범위의 계보 섹션에서 데이터 집합은 사각형 상자로 표시 됩니다.

* **프로세스 (가장자리)**: 데이터 집합에 대해 수행 되는 작업 또는 변환을 프로세스 라고 합니다. 예를 들어 ADF 복사 작업, 데이터 공유 스냅숏 등이 있습니다. 부서의 범위의 계보 섹션에서 프로세스는 모서리가 둥근 상자로 표시 됩니다.

부서의 범위의 자산에 대 한 계보 정보에 액세스 하려면 다음 단계를 수행 합니다.

1. Azure Portal에서 [Azure 부서의 범위 계정 페이지로](https://aka.ms/purviewportal)이동 합니다.

1. 목록에서 Azure 부서의 범위 계정을 선택 하 고 **개요** 페이지에서 **부서의 범위 계정 시작** 을 선택 합니다.

1. Azure 부서의 범위 **홈** 페이지에서 데이터 집합 이름 또는 프로세스 이름 (예: ADF 복사 또는 데이터 흐름 작업)을 검색 합니다. Enter 키를 누릅니다.

1. 검색 결과에서 자산을 선택 하 고 해당 **계보** 탭을 선택 합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="계보 탭을 선택 하는 방법을 보여 주는 스크린샷" border="true":::

## <a name="asset-level-lineage"></a>자산 수준 계보

Azure 부서의 범위는 데이터 집합 및 프로세스에 대 한 자산 수준 계보를 지원 합니다. 자산 수준 계보를 보려면 카탈로그에서 현재 자산의 **계보** 탭으로 이동 합니다. 현재 데이터 집합 자산 노드를 선택 합니다. 기본적으로 데이터에 속하는 열 목록은 왼쪽 창에 표시 됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="계보 페이지에서 열 보기를 선택 하는 방법을 보여 주는 스크린샷" border="true":::

## <a name="dataset-column-lineage"></a>데이터 집합 열 계보

데이터 집합의 열 수준 계보를 보려면 카탈로그에서 현재 자산의 **계보** 탭으로 이동 하 고 아래 단계를 수행 합니다.

1. 계보 탭의 왼쪽 창에서 데이터 계보에 표시 하려는 각 열 옆의 확인란을 선택 합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="계보 페이지에서 표시할 열을 선택 하는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. 왼쪽 창이 나 계보 캔버스의 데이터 집합에서 선택한 열을 마우스로 가리키면 열 매핑이 표시 됩니다. 모든 열 인스턴스가 강조 표시 됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="열 이름을 마우스로 가리키면 데이터 계보 경로에서 열 흐름이 강조 표시 되는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. 열 수가 왼쪽 창에 표시할 수 있는 크기 보다 큰 경우 필터 옵션을 사용 하 여 이름별로 특정 열을 선택 합니다. 또는 마우스를 사용 하 여 목록을 스크롤할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="계보 페이지에서 열 이름별로 열을 필터링 하는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. 계보 캔버스에 더 많은 노드와 가장자리가 포함 된 경우 필터를 사용 하 여 데이터 자산을 선택 하거나 이름을 기준으로 노드를 처리 합니다. 또는 마우스를 사용 하 여 계보 창 주위로 이동할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="계보 페이지에서 이름별로 데이터 자산 노드를 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. 왼쪽 창의 설정/해제를 사용 하 여 계보 캔버스의 데이터 집합 목록을 강조 표시 합니다. 토글을 해제 하면 선택한 열 중 하나 이상이 포함 된 자산이 모두 표시 됩니다. 설정/해제를 켜면 모든 열을 포함 하는 데이터 집합만 표시 됩니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="설정/해제를 사용 하 여 계보 페이지의 노드 목록을 필터링 하는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>열 계보 처리
데이터 프로세스는 하나 이상의 입력 데이터 집합을 사용 하 여 하나 이상의 출력을 생성할 수 있습니다. 부서의 범위에서는 프로세스 노드에 대해 열 수준 계보를 사용할 수 있습니다. 
1. 입력 및 출력 데이터 집합을 열 패널의 드롭다운에서 전환 합니다.
2. 하나 이상의 테이블에서 열을 선택 하 여 입력 데이터 집합에서 해당 출력 데이터 집합으로 흐르는 계보를 확인 합니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="프로세스 노드의 열 계보를 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>계보에서 자산 찾아보기
1. 자산에 대 한 **자산으로 전환** 을 선택 하 여 계보 뷰에서 해당 하는 메타 데이터를 봅니다. 이렇게 하면 계보 뷰에서 카탈로그의 다른 자산을 검색 하는 효과적인 방법입니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="스크린샷 계보 데이터 자산의 자산으로 전환을 선택 하는 방법입니다." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. 계보 캔버스는 인기 있는 데이터 집합에 복잡할 수 있습니다. 혼란을 피하기 위해 기본 보기에는 자산에 대 한 5 가지 수준의 계보만 포커스가 표시 됩니다. 계보 캔버스의 거품을 클릭 하 여 나머지 계보를 확장할 수 있습니다. 데이터 소비자는 캔버스에서 관심이 없는 자산을 숨길 수도 있습니다. 더 간단 하 게 줄이려면 계보 캔버스의 위쪽에서 **추가 계보** 설정/해제를 해제 합니다. 이 작업을 수행 하면 계보 캔버스의 모든 거품이 숨겨집니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="더 많은 계보를 전환 하는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. 계보 캔버스의 스마트 단추를 사용 하 여 계보의 최적 뷰를 가져옵니다. 카탈로그의 몰입 형 계보 환경에서 자동 레이아웃, 크기에 맞게, 확대/축소, 전체 화면 및 탐색 맵을 사용할 수 있습니다.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="계보 스마트 단추를 선택 하는 방법을 보여 주는 스크린샷" lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>다음 단계

* [계보에 대 한 Azure Data Factory 링크](how-to-link-azure-data-factory.md)
* [계보 용 Azure 데이터 공유에 대 한 링크](how-to-link-azure-data-share.md)