---
title: '방법: Data Catalog 검색'
description: 이 문서에서는 데이터 카탈로그를 검색 하는 방법에 대 한 개요를 제공 합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 7799266bf9cece1ed789d6fab64ec970a09fbfcb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588476"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog 검색

데이터 검색은 데이터 소비자를 위한 데이터 분석 또는 데이터 거 버 넌 스 작업에 대 한 첫 번째 단계입니다. 데이터 검색은 원하는 데이터의 위치를 알 수 없기 때문에 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰 하 고이에 대 한 종속성을 얻으려 하거나 여부를 확인할 수 있습니다.

Azure 부서의 범위에서 검색의 목표는 데이터 검색 프로세스를 가속화 하 여 중요 한 데이터를 신속 하 게 찾는 것입니다. 이 문서에서는 Azure 부서의 범위 data catalog를 검색 하 여 찾고 있는 데이터를 빠르게 찾는 방법을 설명 합니다.

## <a name="search-the-catalog-for-assets"></a>카탈로그에서 자산 검색

Azure 부서의 범위에서 검색 모음은 부서의 범위 studio UX의 맨 위에 있습니다.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure 부서의 범위 search 표시줄의 위치를 보여 주는 스크린샷" border="true":::

검색 창을 클릭 하면 최근 검색 기록과 최근에 액세스 한 자산을 볼 수 있습니다. 최근 본 자산을 모두 보려면 "모두 보기"를 선택 합니다.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="키워드를 입력 하기 전의 검색 표시줄을 보여 주는 스크린샷" border="true":::

이름, 데이터 형식, 분류, 용어 등의 자산을 식별 하는 데 도움이 되는 키워드를 입력 합니다. 원하는 자산과 관련 된 키워드에 입력 하는 경우 Azure 부서의 범위는 검색 및 잠재적인 자산 일치에 대 한 제안을 표시 합니다. 검색을 완료 하려면 "검색 결과 보기"를 클릭 하거나 "Enter"를 누릅니다.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="사용자가 키워드로 입력 한 검색 창을 보여 주는 스크린샷" border="true":::

검색 결과 페이지에는 관련성이 높은 순서로 제공 된 키워드와 일치 하는 자산 목록이 표시 됩니다. 자산의 관련성 점수에 영향을 줄 수 있는 다양 한 요인이 있습니다. 원하는 자산에 적용 되는 특정 데이터 저장소, 분류, 연락처, 레이블 및 용어집 용어를 선택 하 여 목록을 더 자세히 필터링 할 수 있습니다.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="검색 결과를 보여 주는 스크린샷" border="true":::

 스키마, 계보 및 자산 소유자를 비롯 한 속성을 볼 수 있는 자산 세부 정보 페이지를 보려면 원하는 자산을 클릭 합니다.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="자산 정보 페이지를 보여 주는 스크린샷" border="true":::

## <a name="search-query-syntax"></a>검색 쿼리 구문

모든 검색 쿼리는 키워드와 연산자로 구성 됩니다. 키워드는 자산의 속성에 포함 되는 항목입니다. 잠재적 키워드는 분류, 용어집 용어, 자산 설명 또는 자산 이름일 수 있습니다. 키워드는 일치 여부를 찾고 있는 속성의 일부일 수 있습니다. 아래에 나열 된 키워드 및 연산자를 사용 하 여 Azure 부서의 범위에서 찾고 있는 자산을 반환 하는지 확인 합니다. 

검색 쿼리를 작성 하는 데 사용할 수 있는 연산자는 다음과 같습니다. 연산자는 단일 쿼리에 필요한 만큼 여러 번 조합할 수 있습니다.

| 연산자 | 정의 | 예제 |
| -------- | ---------- | ------- |
| 또는 | 자산이 두 키워드 중 하나 이상을 포함 하도록 지정 합니다. 모두 대문자 여야 합니다. 공백은 또는 연산자 이기도 합니다.  | 이 쿼리는 `hive OR database` ' hive ' 또는 ' database ' 또는 둘 다를 포함 하는 자산을 반환 합니다. |
| AND | 자산이 두 키워드를 모두 포함 하도록 지정 합니다. 모두 대문자 여야 합니다. | 이 쿼리는 `hive AND database` ' hive ' 및 ' database '를 모두 포함 하는 자산을 반환 합니다. |
| NOT | 자산이 NOT 절 오른쪽의 키워드를 포함할 수 없도록 지정 합니다. | 이 쿼리는 ' `hive NOT database` hive '가 포함 된 자산을 반환 하지만 ' 데이터베이스 '는 반환 하지 않습니다. |
| () | 키워드 및 연산자 집합을 그룹화 합니다. 여러 연산자를 결합할 때 괄호는 연산 순서를 지정 합니다. | 이 쿼리는 `hive AND (database OR warehouse)` ' hive ' 및 ' database ' 또는 ' warehouse ' 또는 둘 다를 포함 하는 자산을 반환 합니다. |
| "" | 쿼리에서 일치 해야 하는 구의 정확한 내용을 지정 합니다. | 이 쿼리는 `"hive database"` 속성에 "hive 데이터베이스" 라는 구가 포함 된 자산을 반환 합니다. |
| * | 한 개에서 여러 문자에 일치 하는 와일드 카드입니다. 키워드의 첫 번째 문자일 수 없습니다. | 쿼리 *는 ' `hiv\` hive ' 또는 ' hive '와 같이 ' 하이브 '로 시작 하는 속성을 포함 하는 자산을 반환 합니다. |
| ? | 단일 문자와 일치 하는 와일드 카드입니다. 키워드의 첫 번째 문자일 수 없습니다. | 이 쿼리는 `hiv?` ' 하이브 '로 시작 하는 속성을 포함 하는 자산을 반환 하 고 ' hive ' 또는 ' s e a p '와 같은 4 개의 문자를 반환 합니다. |

> [!Note]
> 항상 모든 대문자에 부울 연산자 (**AND**, **OR**, **NOT**)를 지정 합니다. 그렇지 않으면 대/소문자는 중요 하지 않으며 추가 공백은 수행 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [용어를 만들고, 가져오고, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법](how-to-manage-term-templates.md)
