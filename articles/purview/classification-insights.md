---
title: 부서의 범위 Insights를 사용 하 여 Azure 부서의 범위에서 데이터에 대 한 분류 보고
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 분류 보고를 보고 사용 하는 방법을 설명 합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: b6ea3e762ad098e373baa8487d8926105820f226
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101666523"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 분류 통찰력

이 방법 가이드에서는 데이터에 대 한 부서의 범위 분류 정보 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

지원 되는 데이터 원본에는 다음이 포함 됩니다. Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (이전의 SQL DW), Azure SQL Database, Azure SQL Managed Instance, SQL Server, Amazon S3 버킷

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure에서 부서의 범위 계정 시작
> - 데이터에 대 한 분류 정보 보기
> - 드릴 다운 하 여 데이터에 대 한 자세한 분류 정보

## <a name="prerequisites"></a>필수 구성 요소

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

- Azure 리소스를 설정 하 고 테스트 데이터를 사용 하 여 관련 계정 채우기

- 각 데이터 원본의 테스트 데이터에 대 한 검색을 설정 하 고 완료 했습니다. 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md) 및 [스캔 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조 하세요.

- [데이터 판독기 또는 데이터 큐레이터 역할이](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)있는 계정을 사용 하 여 부서의 범위에 로그인 했습니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-classification-insights"></a>부서의 범위 분류 정보 사용

Azure 부서의 범위에서 분류는 subject 태그와 비슷하며 검색 중에 데이터 공간 내에 있는 특정 유형의 데이터를 표시 하 고 식별 하는 데 사용 됩니다.

부서의 범위는 Microsoft 365와 동일한 중요 한 정보 유형을 사용 하 여 기존 보안 정책과 전체 데이터 공간에 대 한 보호를 확장할 수 있습니다.

> [!NOTE]
> 원본 유형을 검사 한 후에는 새 자산을 반영 하는 데 몇 시간 정도 **분류** 정보를 제공 합니다.

**분류 정보를 보려면:**

1. Azure Portal **Azure 부서의 범위** [인스턴스 화면](https://aka.ms/purviewportal) 으로 이동 하 여 부서의 범위 계정을 선택 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **부서의 범위 계정 시작** 타일을 선택 합니다.

1. 부서의 범위에서 왼쪽의 **insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택 하 여 **정보** 영역에 액세스 합니다.

1. **정보** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 영역에서 **분류** 를 선택 하 여 부서의 범위 **분류 정보** 보고서를 표시 합니다.

   :::image type="content" source="./media/insights/select-classification-labeling-small.png" alt-text="분류 정보 보고서" lightbox="media/insights/select-classification-labeling.png":::

   기본 **분류 정보** 페이지에는 다음과 같은 영역이 표시 됩니다.

   |영역  |설명  |
   |---------|---------|
   |**분류가 포함 된 원본 개요**     |다음을 제공 하는 타일을 표시 합니다. <br>-데이터에서 발견 된 구독 수 <br>-데이터에서 발견 된 고유 분류의 수 <br>-검색 된 분류 된 소스 수 <br>-검색 된 분류 된 파일 수 <br>-검색 된 분류 테이블 수         |
   |**분류 된 데이터를 포함 하는 상위 원본 (지난 30 일)**     |지난 30 일 동안의 추세를 분류 된 데이터로 찾은 원본 수의 추세를 보여 줍니다.            |
   |**원본 별 상위 분류 범주**     |**재무** 또는 **정부** 와 같이 분류 범주별로 찾은 원본 수를 표시 합니다.      |
   |**파일에 대 한 상위 분류**     |신용 카드 번호 또는 국가 식별 번호와 같이 데이터의 파일에 적용 되는 상위 분류를 표시 합니다.         |
   |**테이블에 대 한 상위 분류**     | 개인 식별 정보와 같이 데이터의 테이블에 적용 되는 상위 분류를 표시 합니다. |   
   |  **분류 작업** <br>(파일 및 테이블) |  파일 및 테이블에 대 한 별도의 그래프를 표시 합니다. 각 그래프에는 선택한 기간 동안 분류 되는 파일 또는 테이블의 수가 표시 됩니다. <br>**기본값**: 30 일<br>그래프 위에 있는 **시간** 필터를 선택 하 여 표시할 다른 시간 프레임을 선택 합니다.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>분류 정보 드릴 다운

다음 **분류 insights** 그래프에서 자세히 보기 링크를 선택 하 여 자세한 내용을 **확인** 합니다.

- **원본 별 상위 분류 범주**
- **파일에 대 한 상위 분류**
- **테이블에 대 한 상위 분류**
- **분류 작업 > 분류 데이터**

예를 들면 다음과 같습니다.

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="모든 분류 보기" lightbox="media/insights/view-classifications.png":::

자세한 내용을 보려면 다음 중 하나를 수행 합니다.

|옵션  |설명  |
|---------|---------|
|**데이터 필터링**     |  표 위의 필터를 사용 하 여 분류 이름, 구독 이름 또는 원본 유형을 포함 하 여 표시 된 데이터를 필터링 합니다. <br><br>정확한 분류 이름을 잘 모르는 경우 **키워드로 필터링** 상자에 이름의 일부 또는 전체를 입력할 수 있습니다.       |
|**표 정렬** |해당 열을 기준으로 표를 정렬 하려면 열 머리글을 선택 합니다. | 
|**열 편집**     |  표 형태 창에 더 많거나 더 작은 열을 표시 하려면 **열 편집** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: 을 선택 하 고 순서를 보거나 변경 하려는 열을 선택 합니다.   |
|**자세히 드릴 다운**     | 특정 분류로 드릴 다운 하려면 **분류** 열에서 이름을 선택 하 여 **원본 보고서 별로 분류** 를 표시 합니다. <br><br>이 보고서는 원본 이름, 원본 유형, 구독 ID 및 분류 된 파일 및 테이블 수를 포함 하 여 선택한 분류에 대 한 데이터를 표시 합니다.      |
|**자산 찾아보기**     |  특정 분류가 나 원본에서 발견 된 자산을 찾아보려면 표시 되는 보고서에 따라 분류 또는 원본을 선택 하 고 필터 위의 **자산 찾아보기** 를 선택 :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: 합니다. <br><br>검색 결과에는 선택한 필터에 대해 찾은 분류 된 자산이 모두 표시 됩니다.  자세한 내용은 [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)을 참조 하세요.       |
| | |

## <a name="next-steps"></a>다음 단계

Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보기
> [!div class="nextstepaction"]
> [용어집 정보](glossary-insights.md)

> [!div class="nextstepaction"]
> [검색 정보](scan-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 지정 정보](./sensitivity-insights.md)

> [!div class="nextstepaction"]
> [파일 확장명 정보](file-extension-insights.md)