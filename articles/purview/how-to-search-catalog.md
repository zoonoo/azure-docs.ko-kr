---
title: '방법: Data Catalog 검색'
description: 이 문서에서는 데이터 카탈로그를 검색 하는 방법에 대 한 개요를 제공 합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/15/2020
ms.openlocfilehash: 87ca3c49d6a96f083cc50f01f5b9a4a739d688a1
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554025"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure 부서의 범위 Data Catalog 검색

이 문서에서는 Azure 부서의 범위 Data Catalog에서 다양 한 검색 기능을 사용 하는 방법을 설명 합니다.

## <a name="search-the-catalog-for-assets"></a>카탈로그에서 자산 검색

자산 검색을 수행 하는 단계는 다음과 같습니다.

1. **검색 카탈로그** 를 선택 하 여 [자산 검색 대화 상자를 엽니다](#open-the-asset-search-dialog) .
1. 용어와 일치 하는 특성을 가진 자산을 찾으려면 [검색 용어를 입력](#enter-search-terms) 합니다.
1. [빠른 필터를 설정](#set-quick-filters) 하 여 검색 범위를 좁힙니다.
1. [검색을 시작](#start-the-search) 하 고 검색 결과로 이동 합니다.

검색 용어를 입력 하기 전이나 후에 빠른 필터를 설정 하는 것은 중요 하지 않습니다.

검색 용어가 없고 필터는 없는 경우 검색 결과에 모든 자산이 포함 됩니다.

### <a name="open-the-asset-search-dialog"></a>자산 검색 대화 상자 열기

**검색 카탈로그** 를 선택 하 여 자산 검색 대화 상자를 엽니다.

:::image type="content" source="./media/how-to-search-catalog/search-catalog.png" alt-text="' 검색 카탈로그 ' 아래에는 검색 필터가 있는 왼쪽 창이 있고 최신 검색을 포함 하는 오른쪽 창이 있습니다." border="true":::

검색 대화 상자에는 빠른 필터, 검색 기록 및 최근에 액세스 한 자산 목록이 표시 됩니다.

:::image type="content" source="./media/how-to-search-catalog/asset-search-dialog.png" alt-text="검색 목록은 오른쪽 창의 ' 검색 카탈로그 ' 아래에 있습니다." border="true":::

### <a name="enter-search-terms"></a>검색 용어 입력

**검색 카탈로그** 에 검색 용어를 하나 이상 입력 합니다. 입력 하면 최근 검색에서 일치 하는 검색 단어가 **최근** 검색에 나열 되 고, 제안 된 일치 검색 단어가 **검색 제안** 에 나열 되며, 일치 하는 데이터 자산이 **자산 제안** 에 나열 됩니다.

:::image type="content" source="./media/how-to-search-catalog/enter-search-terms.png" alt-text="검색 카탈로그 상자에 입력 한 검색 결과를 보여 주는 스크린샷":::

검색 결과에는 검색 용어와 일치 하는 특성이 하나 이상 포함 된 자산만 포함 됩니다. 이러한 특성에는 자산 이름, 자산 유형, 분류 및 연락처가 포함 됩니다.

#### <a name="types-of-search-criteria"></a>검색 조건 유형

Azure 부서의 범위는 다음과 같은 유형의 검색 조건을 지원 합니다.

> [!Note]
> 항상 모든 대문자에 부울 연산자 (**AND**, **OR**, **NOT**)를 지정 합니다. 그렇지 않으면 대/소문자는 중요 하지 않으며 추가 공백은 수행 하지 않습니다.

- **hive**: **hive** 를 포함 하는 문서를 찾습니다.
- **hive 데이터베이스**: 정확히 **hive 데이터베이스** 를 포함 하는 문서를 찾습니다.
- **HIVE 또는 데이터베이스**: **hive** 또는 **데이터베이스** 를 포함 하거나 둘 다 포함 하는 문서를 찾습니다.
- hive **및 데이터베이스**, **hive && 데이터베이스**: **hive** 와 **데이터베이스** 를 모두 포함 하는 문서를 찾습니다.
- **HIVE 및 (데이터베이스 또는 웨어하우스)**: **hive** 와 **데이터베이스나** **웨어하우스** 중 하나 또는 둘 다를 포함 하는 문서를 찾습니다.
- **HIVE not database**: **데이터베이스** 를 제외 하 고 **hive** 를 포함 하는 문서를 찾습니다.
- **하이브**: **하이브** 로 시작 하는 단어를 포함 하는 문서를 찾습니다. 예를 들어 **하이브**, **hive**, 원하는 **vbar** (*는 문자 수와 일치 하는 와일드 카드)입니다.

### <a name="set-quick-filters"></a>빠른 필터 설정

검색 결과 목록은 **검색 카탈로그** 에 입력 한 검색 용어 및 빠른 필터에 대해 선택한 값을 기준으로 합니다.

빠른 필터는 검색 결과 목록을 특성의 선택 된 값을 가진 자산으로 제한 합니다. 필터에는 드롭다운 목록과 입력란이 있습니다. 드롭다운 목록에는 *현재* 검색 결과에 있는 특성의 값이 표시 됩니다. 목록의 각 값 옆에는 해당 값이 있는 현재 검색 결과의 자산 수가 표시 됩니다. 목록에서 값을 선택 하는 경우 검색 결과는 해당 값이 있는 자산으로 제한 됩니다. 값은 하나만 선택할 수 있습니다.

드롭다운 목록을 형성 하는 데 사용 되는 현재 검색 결과는 다음에 의해 결정 됩니다.

- **검색 카탈로그** 에 입력 된 검색 용어입니다. 
- 빠른 필터에서 선택 된 값입니다.

"자산 유형" 빠른 필터의 예는 다음과 같습니다.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="자산 유형 빠른 필터 예제입니다." border="true":::

텍스트 상자에 텍스트를 입력 하 여 드롭다운 목록의 값을 텍스트와 일치 하거나 부분적으로 일치 하는 값으로 제한할 수 있습니다. 텍스트 상자 사용에 대 한 예제는 [검색 빠른 필터: 자산 유형별 필터링](#search-quick-filter-filter-by-asset-type)및 [검색 빠른 필터: 분류로 필터링](#search-quick-filter-filter-by-classification)을 참조 하세요.

#### <a name="search-quick-filter-filter-by-asset-type"></a>빠른 필터 검색: 자산 유형별 필터링

자산 유형별로 필터링 하려면 **자산 유형** 빠른 필터를 사용 합니다. 드롭다운 목록에는 검색 용어 및 빠른 필터에 따라 현재 검색 결과에서 발견 된 자산 유형이 표시 됩니다. 각 형식에 대해 해당 형식의 자산 수가 표시 됩니다.

:::image type="content" source="./media/how-to-search-catalog/asset-type-quick-filter.png" alt-text="자산 유형 빠른 필터가 강조 표시 됩니다. 자산 유형과 각각의 수를 표시 합니다." border="true":::

자산 유형을 선택 하 여 해당 유형의 자산으로 검색 결과를 제한할 수 있습니다. 한 가지 유형만 선택할 수 있습니다.

이름이 문자열과 일치 하는 자산 유형만 표시 하려면 텍스트 상자에 문자열을 입력 합니다. 예를 들어 이름에 **sql** 이 있는 자산 유형만 표시 하려면 **sql** 을 입력 합니다.

:::image type="content" source="./media/how-to-search-catalog/filter-asset-types.png" alt-text="빠른 필터 창에는 ' 자산 유형 ' 용 sql이 있습니다. Sql이 포함 된 자산 목록은 세 개의 항목을 표시 합니다." border="true":::

자산 유형을 선택 하 여 해당 유형의 자산으로 검색 결과를 제한할 수 있습니다. 한 가지 유형만 선택할 수 있습니다.

#### <a name="search-quick-filter-filter-by-classification"></a>빠른 필터 검색: 분류 별 필터링

자산 분류를 기준으로 필터링 하려면 **분류** 빠른 필터를 사용 합니다. 드롭다운 목록에는 검색 용어 및 빠른 필터에 따라 현재 검색 결과에서 하나 이상의 자산에 할당 된 분류가 표시 됩니다. 각 분류에 대해 해당 분류에 할당 된 자산 수가 표시 됩니다.

:::image type="content" source="./media/how-to-search-catalog/classification-quick-filter.png" alt-text="분류 빠른 필터가 강조 표시 됩니다." border="true":::

분류를 선택 하 여 해당 분류에 할당 된 자산으로 검색 결과를 제한할 수 있습니다. 분류는 하나만 선택할 수 있습니다.

문자열과 일치 하는 이름을 가진 분류만 표시 하려면 텍스트 상자에 문자열을 입력 합니다. 예를 들어 이름에 **숫자가** 있는 분류만 표시 하려면 **숫자** 를 입력 합니다.

:::image type="content" source="./media/how-to-search-catalog/filter-classifications.png" alt-text="빠른 필터 창에서 분류는 ' bank '이 고 나열 된 분류는 모두 해당 값을 포함 합니다." border="true":::

분류를 선택 하 여 해당 분류가 할당 된 자산으로 검색 결과를 제한할 수 있습니다. 둘 이상의 분류를 선택할 수 없습니다.

#### <a name="search-quick-filter-filter-by-contacts"></a>빠른 필터 검색: 연락처를 기준으로 필터링

*연락처* 는 소유자 또는 전문가로 자산에 할당 된 사람입니다. 자산 세부 정보를 볼 때 **연락처 탭에 연락처가** 표시 됩니다.

특정 연락처가 할당 된 자산을 검색 하는 방법에는 두 가지가 있습니다.

- **검색 카탈로그** 에서 연락처 이름의 전부 또는 일부를 입력 하 고 검색을 수행 합니다. 검색 결과에는 이름이 검색 용어와 일치 하는 연락처가 있는 자산이 포함 됩니다.
- **연락처** 빠른 필터에서 관심 있는 연락처를 선택 하 고 검색을 수행 합니다.

:::image type="content" source="./media/how-to-search-catalog/contact-quick-filter.png" alt-text="빠른 필터 창의 Person 값은 ' darren '입니다. 제안 창에는 세 가지 제안이 있습니다." border="true":::

## <a name="search-example"></a>검색 예제

검색 용어 및 빠른 필터를 사용 하 여 검색 결과를 확인 하는 방법을 확인 하는 가상의 예제를 살펴보겠습니다. 특히 **Azure Blob Storage** 자산 유형 수를 모니터링 합니다.

- 검색 단어가 입력 되지 않은 첫 번째 검색을 수행 하 고 빠른 필터에서 값을 선택 하지 않았습니다. 검색은 카탈로그에서 모든 자산을 찾습니다. 검색 결과 목록 및 **자산 유형** 빠른 필터는 다음을 표시 합니다.

    - 검색 결과 목록에는 카탈로그의 모든 자산에 해당 하는 164230 자산이 있습니다.
    - **자산 유형** 드롭다운 목록에는 43 개의 항목이 있습니다. 이는 모두 카탈로그의 자산 유형입니다. 모든 자산은 하나의 유형 이므로 43 자산 유형의 개수 합계는 164230입니다.
    - 자산 **유형** 빠른 필터 드롭다운 목록의 첫 번째 항목은 **Azure Blob Storage** 자산 유형입니다. 값은 count로 가장 큰 순서로 정렬 되므로 **Azure Blob Storage** 가장 일반적인 자산 유형입니다. 개수는 118174입니다.

- 이제 **검색 카탈로그** 에 **parquet** 를 입력 하 고 다른 검색을 수행 합니다. 검색 결과에는 **parquet** 와 일치 하는 특성을 가진 자산만 포함 됩니다. 그러면 다음과 같이 모든 수가 줄어듭니다.

    - 검색 결과 목록에는 493 자산이 있습니다. 카탈로그에 있는 164230 자산의 493만 "parquet"와 일치 하는 특성을가지고 있습니다.
    - **자산 유형** 드롭다운 목록에는 15 개의 항목이 있습니다. 493 자산 중 하나는 이러한 15 가지 유형 중 하나이 고 15 가지 유형 개수의 합계는 493입니다.
    - **Azure Blob Storage** 유형의 자산 456이 있습니다. 다른 37 (493 빼기 456) 자산은 다른 14 가지 형식 중 하나입니다.

- 이제 다른 빠른 필터의 드롭다운 목록을 살펴보겠습니다. **분류** 는 다음과 같습니다.

    - 검색 결과 목록에는 493 자산에 대 한 12 개의 분류가 있습니다. 자산에 대 한 분류 수를 할당할 수 있으므로 493 자산의 개수는 493이 하 여야 합니다.
    - **사용자의 이름** 분류는 다른 분류 보다 36 자산에 할당 됩니다.

- **사용자의 이름** 분류를 선택 합니다. **사용자 이름의** 수가 36 이기 때문에 검색 결과 목록이 예상 대로 36 자산에 삭제 됩니다. 이러한 결과는 **Azure Blob Storage** 유형이 아닙니다.

**Parquet** 와 일치 하 고 **사람의 이름** 분류를 포함 하는 **Azure Blob Storage** 형식이 있는 자산이 없다는 결론을 완료할 수 있습니다.

## <a name="start-the-search"></a>검색 시작

검색할 때 검색 **카탈로그** 에 입력 하는 검색 단어는 자산 특징과 일치 합니다. 이러한 특성에는 이름, 형식, 분류 및 연락처가 포함 됩니다. 빠른 필터로 제외 하지 않으면 일치 하는 특성이 있는 자산이 검색 결과 목록에 표시 됩니다.

검색 용어를 입력 하 고 빠른 필터를 설정한 후 다음 방법 중 하나를 사용 하 여 검색을 시작 합니다.

- 입력 한 용어를 기준으로 검색 하려면 검색 아이콘 ()을 선택 하 고 :::image type="icon" source="./media/how-to-search-catalog/search-icon.png"::: **enter** 키를 누르거나 **검색 결과 보기** 를 선택 합니다.
- 이전 검색에서 용어를 사용 하 여 검색 하려면 **최근** 검색에서 해당 용어를 선택 합니다.
- 제안 된 용어를 사용 하 여 검색 하려면 **검색 제안** 에서 선택 합니다.

자산 **제안** 에서 자산을 선택 하 여 자산에 대 한 세부 정보 페이지로 직접 이동 합니다. 검색을 수행 하지 않습니다.

제안 및 사용자 검색에 대 한 결과 목록은 약간 다를 수 있습니다. 제안 목록의 결과는 유사 항목 일치를 기반으로 하며 사용자가 시작한 검색 결과는 정확히 일치 하는 항목을 기반으로 합니다.

검색 하면 검색 **결과** 페이지가 나타나고 검색에서 찾은 자산이 나열 됩니다.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="Contoso의 검색 값에 대 한 검색 결과를 보여 주는 스크린샷":::

자산 정보를 보려면 자산 이름을 선택 합니다.

검색 결과 페이지의 아래쪽에 있는 컨트롤을 사용 하 여 다른 검색 결과 페이지로 이동 합니다.

:::image type="content" source="./media/how-to-search-catalog/page-navigation.png" alt-text="검색 결과 페이지를 통해 탐색 하는 방법을 보여 주는 스크린샷":::

### <a name="sort-search-results"></a>검색 결과 정렬

**Sort by** 를 사용 하 여 **관련성** 또는 **이름을** 기준으로 검색 결과를 정렬 합니다.

:::image type="content" source="./media/how-to-search-catalog/sort-by.png" alt-text="검색 결과를 정렬 하는 방법을 보여 주는 스크린샷 이 예에서는 정렬 기준 드롭다운 목록이 관련성으로 설정 되어 있습니다.":::

### <a name="search-results-dynamic-filters"></a>검색 결과 동적 필터

**검색 결과** 페이지의 **필터** 창에는 검색 결과 목록에서 자산에 대 한 동적 필터링을 제공 하는 필터가 있습니다. 필터링은 필터 선택에 따라 추가 필터가 표시 될 수 있다는 것을 동적으로 합니다.

동적 필터에는 드롭다운 목록에 있는 각 값의 확인란이 있습니다. 이 확인란을 사용 하 여 원하는 수의 값을 필터링 할 수 있습니다.

#### <a name="search-results-dynamic-filter-filter-by-asset-type"></a>검색 결과 동적 필터: 자산 유형별 필터링

**자산 유형** 드롭다운 목록에서 자산 유형을 선택 하는 경우 검색 결과의 범위를 좁힐 수 있는 추가 방법을 제공 하는 동적 필터가 표시 됩니다. 필터는 선택한 자산 유형에 따라 달라 집니다. 예를 들어 **Azure SQL Database** 를 선택 하면 서버, 데이터베이스 및 스키마에 대 한 동적 필터가 표시 됩니다. 이러한 필터의 값은 선택한 유형의 검색 결과에 있는 자산에서 가져온 것입니다.

:::image type="content" source="./media/how-to-search-catalog/asset-type-dynamic-filter.png" alt-text="Azure SQL Database 필터 항목은 선택한 유일한 ' 자산 유형 ' 항목입니다. 해당 자산 유형의 검색 결과도 강조 표시 됩니다." border="true":::

#### <a name="search-results-dynamic-filter-filter-by-classification"></a>검색 결과 동적 필터: 분류 별로 필터링

**분류** 목록의 각 분류는 검색 결과 목록에 있는 하나 이상의 항목에 적용 됩니다. 선택한 분류의 자산으로 검색 결과의 범위를 좁히려면 하나 이상의 분류를 선택 합니다.

:::image type="content" source="./media/how-to-search-catalog/classification-dynamic-filter.png" alt-text="' 검색 결과 '의 분류 필터가 강조 표시 됩니다." border="true":::

#### <a name="edit-and-delete-search-results-filters"></a>검색 결과 필터 편집 및 삭제

필터를 제거 하려면 필터 이름 옆에 있는 확인란의 선택을 취소 합니다.

### <a name="recently-accessed-assets"></a>최근에 액세스 한 자산

확장 된 검색 상자의 **최근에 액세스** 한 섹션에는 가장 최근에 액세스 한 자산 (있는 경우)이 표시 됩니다.

- 최근에 액세스 한 자산의 전체 목록을 보려면 **최근에 액세스** 한 섹션에서 **모두 보기** 를 선택 합니다.

   :::image type="content" source="./media/how-to-search-catalog/get-to-recent-view.png" alt-text="확장 된 검색 상자의 최근에 액세스 한 섹션을 보여 주는 스크린샷":::

   최근에 액세스 한 자산 목록이 표시 됩니다.

   :::image type="content" source="./media/how-to-search-catalog/recent.png" alt-text="최근에 액세스 한 자산 목록을 보여 주는 스크린샷":::

- 이름을 기준으로 필터링 하려면 **이름으로 필터** 에 검색 문자열을 입력 합니다.

- 목록에서 항목을 제거 하려면 해당 확인란을 선택 하 고 **제거** 를 선택 합니다.

   :::image type="content" source="./media/how-to-search-catalog/remove-from-recent-view.png" alt-text="최근에 액세스 한 자산 목록에서 항목을 제거 하는 방법을 보여 주는 스크린샷":::

- 전체 목록을 지우려면 **지우기** 를 선택 합니다.

   :::image type="content" source="./media/how-to-search-catalog/clear-recent-view-selections.png" alt-text="최근에 액세스 한 자산 목록을 지우는 방법을 보여 주는 스크린샷":::

### <a name="search-assets"></a>자산 검색

**홈** 이 아닌 많은 페이지는 위쪽에 **자산 검색** 상자를 포함 합니다. 예를 들어 **검색 자산이** 강조 표시 된 자산 정보 페이지는 다음과 같습니다.

:::image type="content" source="./media/how-to-search-catalog/search-assets.png" alt-text="검색 자산이 강조 표시 된 자산 정보 페이지를 보여 주는 스크린샷":::

**자산 검색** 을 선택 하 여 **홈** 의 **검색 카탈로그** 에서 가져온 것과 같은 기능을 사용 하 여 검색 상자를 시작 합니다.

:::image type="content" source="./media/how-to-search-catalog/search-assets-dialog.png" alt-text="확장 된 검색 자산 상자를 보여 주는 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [용어를 만들고, 가져오고, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법](how-to-manage-term-templates.md)
