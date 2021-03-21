---
title: 부서의 범위 Insights를 사용 하 여 Azure 부서의 범위에서 데이터에 대 한 민감도 레이블 보고
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 민감도 레이블 보고를 보고 사용 하는 방법을 설명 합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 01/17/2021
ms.openlocfilehash: bb8ac82b2e59ec86db89c7eba0ce607fcfc0ac2d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101676558"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Azure 부서의 범위에서 데이터에 대 한 민감도 레이블 정보

이 방법 가이드에서는 데이터에 적용 된 민감도 레이블로 제공 된 보안 정보에 액세스 하 고, 확인 하 고, 필터링 하는 방법을 설명 합니다.

지원 되는 데이터 원본에는 Azure Blob Storage, Azure Data Lake Storage (ADLS) GEN 1, Azure Data Lake Storage (ADLS) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance, Amazon S3 버킷이 포함 됩니다.

이 방법 가이드에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> - Azure에서 부서의 범위 계정을 시작 합니다.
> - 데이터에 대 한 민감도 레이블 지정 정보 보기
> - 데이터에 대 한 더 많은 민감도 레이블 지정 정보를 드릴 다운 합니다.

> [!NOTE]
> 부서의 범위에서 검사 하는 [Power BI 자산](register-scan-power-bi-tenant.md) 에 있는 민감도 레이블은 현재 민감도 레이블 지정 정보 보고서에 표시 되지 않습니다. 
>
> Power BI 자산의 민감도 레이블을 보려면 [부서의 범위 Data Catalog](how-to-search-catalog.md)에서 자산을 확인 하세요.
> 
## <a name="prerequisites"></a>필수 구성 요소

부서의 범위 insights를 시작 하기 전에 다음 단계를 완료 했는지 확인 합니다.

- Azure 리소스를 설정 하 고 테스트 데이터를 사용 하 여 관련 계정 채우기

- [Azure 부서의 범위의 자산에 대 한 확장 된 Microsoft 365 민감도 레이블](create-sensitivity-label.md)및 데이터에 적용 하려는 레이블을 만들거나 선택 합니다.

- 각 데이터 원본의 테스트 데이터에 대 한 검색을 설정 하 고 완료 했습니다. 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md) 및 [스캔 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조 하세요.

- [데이터 판독기 또는 데이터 큐레이터 역할이](catalog-permissions.md#azure-purviews-pre-defined-data-plane-roles)있는 계정을 사용 하 여 부서의 범위에 로그인 했습니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리 (미리 보기)](manage-data-sources.md) 및 [azure 부서의 범위에서 데이터에 자동으로 레이블](create-sensitivity-label.md)표시를 참조 하세요.

## <a name="use-purview-sensitivity-labeling-insights"></a>부서의 범위 민감도 레이블 지정 정보 사용

부서의 범위에서 분류는 subject 태그와 비슷하며 검색 하는 동안 데이터 공간 내에 있는 특정 유형의 데이터를 표시 하 고 식별 하는 데 사용 됩니다.

민감도 레이블을 사용 하면 조직에서 중요 한 특정 데이터의 상태를 확인할 수 있습니다. 예를 들어 조직 내에서 특정 프로젝트 이름에 대 한 기밀을 유지 하는 반면 동일한 용어는 다른 조직에 대 한 기밀은 아닙니다. 

분류는 **주민 등록** 번호를 분류 하는 주민 등록 번호와 같이 직접 일치 합니다. 

이와 달리 민감도 레이블은 하나 이상의 분류 및 조건을 함께 발견할 경우 적용 됩니다. 이 컨텍스트에서 [조건은](/microsoft-365/compliance/apply-sensitivity-label-automatically) **다른 분류에 근접** 한 경우와 같이 비구조적 데이터에 대해 정의할 수 있는 모든 매개 변수를 **나타냅니다.** 

부서의 범위는 Microsoft 365 같이 [중요 한 정보 형식이](/microsoft-365/compliance/sensitive-information-type-entity-definitions)라고도 하는 동일한 분류를 사용 합니다. 이렇게 하면 Azure 부서의 범위 자산에서 기존 민감도 레이블을 확장할 수 있습니다.

> [!NOTE]
> 원본 유형을 검사 한 후에는 새 자산을 반영 하는 두 시간으로 **민감도 레이블** 지정 정보를 제공 합니다.

**민감도 레이블 지정 정보를 보려면:**

1. **Azure 부서의 범위** 홈 페이지로 이동 합니다.

1. **개요** 페이지의 **시작** 섹션에서 **부서의 범위 계정 시작** 타일을 선택 합니다.

1. 부서의 범위에서 왼쪽의 **insights** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택 하 여 **정보** 영역에 액세스 합니다.

1. **정보** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 영역에서 **민감도 레이블** 을 선택 하 여 부서의 범위 민감도 레이블 **정보** 보고서를 표시 합니다.

    > [!NOTE]
    > 이 보고서가 비어 있는 경우 민감도 레이블을 Azure 부서의 범위 확장 하지 않았을 수 있습니다. 자세한 내용은 [Azure 부서의 범위에서 데이터에 자동으로 레이블](create-sensitivity-label.md)표시를 참조 하세요.

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="민감도 레이블 지정 정보" lightbox="media/insights/sensitivity-labeling-insights.png":::

   주요 **민감도 레이블 정보** 페이지에는 다음과 같은 영역이 표시 됩니다.

   |영역  |설명  |
   |---------|---------|
   |**민감도 레이블이 있는 원본 개요**     |다음을 제공 하는 타일을 표시 합니다. <br>-데이터에서 발견 된 구독 수입니다. <br>-데이터에 적용 된 고유 민감도 레이블 수 <br>-민감도 레이블이 적용 된 원본 수 <br>-민감도 레이블이 적용 된 파일 및 테이블의 수|
   |**레이블이 지정 된 데이터를 포함 하는 상위 원본 (지난 30 일)**     | 민감도 레이블이 적용 된 원본 수의 지난 30 일 동안의 추세를 보여 줍니다.       |
   |**소스 전체에 적용 되는 상위 레이블**     |모든 부서의 범위 데이터 리소스에서 적용 되는 최상위 레이블을 표시 합니다. |
   |**파일에 적용 된 상위 레이블**     |데이터의 파일에 적용 되는 상위 민감도 레이블을 표시 합니다.          |
   |**테이블에 적용 되는 상위 레이블**     | 데이터의 데이터베이스 테이블에 적용 되는 상위 민감도 레이블을 표시 합니다. |   
   |  **레이블 작업**  |  파일 및 테이블에 대 한 별도의 그래프를 표시 합니다. 각 그래프에는 선택한 시간 프레임 위에 레이블이 지정 된 파일 또는 테이블의 수가 표시 됩니다. <br>**기본값**: 30 일<br>그래프 위에 있는 **시간** 필터를 선택 하 여 표시할 다른 시간 프레임을 선택 합니다.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>민감도 레이블 지정 정보 드릴 다운

다음 **민감도 레이블 지정 정보** 그래프에서 자세히 보기 링크를 선택 하 여 자세한 내용을 **확인** 합니다.

- **소스 전체에 적용 되는 상위 레이블**
- **파일에 적용 된 상위 레이블**
- **테이블에 적용 되는 상위 레이블**
- **레이블 작업 > 레이블 데이터**

예를 들면 다음과 같습니다.

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="민감도 레이블 드릴 다운" lightbox="media/insights/sensitivity-label-drilldown.png":::

자세한 내용을 보려면 다음 중 하나를 수행 합니다.

|옵션  |설명  |
|---------|---------|
|**데이터 필터링**     |  레이블 이름, 구독 이름 또는 원본 유형을 포함 하 여 표시 되는 데이터를 필터링 하려면 표 위의 필터를 사용 합니다. <br><br>정확한 레이블 이름을 잘 모르는 경우 **키워드로 필터링** 상자에 이름의 일부 또는 전체를 입력할 수 있습니다.       |
|**표 정렬** |해당 열을 기준으로 표를 정렬 하려면 열 머리글을 선택 합니다. | 
|**열 편집**     |  표 형태 창에 더 많거나 더 작은 열을 표시 하려면 **열 편집** :::image type="icon" source="media/insights/ico-columns.png" border="false"::: 을 선택 하 고 순서를 보거나 변경 하려는 열을 선택 합니다.    <br><br>해당 열을 기준으로 표를 정렬 하려면 열 머리글을 선택 합니다.   |
|**자세히 드릴 다운**     | 특정 레이블로 드릴 다운 하려면 **민감도 레이블** 열에서 이름을 선택 하 여 **원본 보고서로 레이블을** 표시 합니다. <br><br>이 보고서는 원본 이름, 원본 유형, 구독 ID 및 분류 된 파일 및 테이블 수를 포함 하 여 선택한 레이블에 대 한 데이터를 표시 합니다.      |
|**자산 찾아보기**     |  특정 레이블이나 원본으로 찾은 자산을 찾아보려면 표시 하는 보고서에 따라 하나 이상의 레이블이나 원본을 선택 하 고 필터 위의 **자산 찾아보기** 를 선택 :::image type="icon" source="media/insights/ico-browse-assets.png" border="false"::: 합니다. <br><br>검색 결과는 선택한 필터에 대해 찾은 레이블이 지정 된 모든 자산을 표시 합니다.  자세한 내용은 [Azure 부서의 범위 Data Catalog 검색](how-to-search-catalog.md)을 참조 하세요.       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Microsoft 365 준수와의 민감도 레이블 통합

Microsoft 365에서 제공 하는 [Microsoft Information Protection](/microsoft-365/compliance/information-protection) 와의 통합을 통해 부서의 범위를 통해 데이터 공간에 대 한 가시성을 직접 확장 하 고 데이터를 분류 하 고 레이블을 지정할 수 있습니다.

Azure 부서의 범위에서 자산에 대 한 Microsoft 365 민감도 레이블을 확장 하려면 Microsoft 365 준수 센터에서 Azure 부서의 범위에 대 한 Information Protection를 적극적으로 설정 해야 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터에 자동으로 레이블](create-sensitivity-label.md)표시를 참조 하세요.

## <a name="next-steps"></a>다음 단계

이러한 Azure 부서의 범위 통찰력 보고서에 대해 자세히 알아보세요.

- [용어집 정보](glossary-insights.md)
- [검색 정보](scan-insights.md)
- [분류 정보](./classification-insights.md)
- [파일 확장명 정보](file-extension-insights.md)
