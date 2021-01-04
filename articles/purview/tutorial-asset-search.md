---
title: '자습서: Azure Purview 홈 페이지 탐색 및 자산 검색'
description: 이 자습서에서는 Azure Purview 홈 페이지의 기능을 사용하고 카탈로그에서 검색하는 방법을 설명합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: f778a55c206e012884b44c1fea0a63e2de6b481a
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97693340"
---
# <a name="tutorial-navigate-the-azure-purview-preview-home-page-and-search-for-an-asset"></a>자습서: Azure Purview(미리 보기) 홈 페이지 탐색 및 자산 검색

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기에 있습니다. [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기에 있거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 자습서에서는 홈 페이지의 기능을 탐색하고 카탈로그에서 자산을 검색하여 Azure Purview에 대해 알아봅니다.

이 문서는 Azure Purview를 사용하여 데이터 자산 전체에서 데이터 거버넌스를 관리하는 방법에 대한 기본 사항을 알아볼 수 있는 *5부로 구성된 자습서 시리즈 중 2부* 입니다. 이 자습서는 [1부: Azure Purview를 사용하여 데이터 검사](tutorial-scan-data.md)에서 완료한 작업을 기반으로 합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> * Azure Purview 홈 페이지를 탐색합니다.
> * 자산을 검색합니다.
> * 자산 소유자를 추가합니다.

## <a name="prerequisites"></a>필수 조건

* [자습서: Azure Purview를 사용하여 데이터 검사](tutorial-scan-data.md)를 완료합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="navigate-the-azure-purview-home-page"></a>Azure Purview 홈 페이지 탐색

Azure Purview 홈 페이지를 안내하는 단계는 다음과 같습니다.

1. Azure Portal에서 Azure Purview 리소스로 이동하여 **Purview Studio 열기** 를 선택합니다. Purview Studio 홈 페이지로 자동으로 이동합니다.

   홈 페이지의 위쪽에 카탈로그 이름과 카탈로그 분석 세트가 표시되며, 여기에는 사용자, 데이터 자산 및 용어집 용어의 수가 포함됩니다. 요약에서 총 200개가 넘는 자산과 113개의 용어집 용어가 있음을 확인할 수 있습니다. 이 개수는 조직에서 검사하고 더 많은 용어를 Azure Purview에 추가함에 따라 업데이트됩니다.

   :::image type="content" source="./media/tutorial-asset-search/purview-home-page.png" alt-text="Azure Purview 홈 페이지를 보여 주는 스크린샷":::

1. **자산 찾아보기** 를 선택하여 모든 자산을 확인합니다.

## <a name="search-for-an-asset"></a>자산 검색

먼저 몇 가지 기본 용어에 대한 빠른 리프레셔를 살펴보겠습니다.

* **자산**: 데이터 자산의 엔터티에 대한 정의가 포함된 간결한 카탈로그의 단일 개체입니다. 엔터티의 예로 SQL 테이블, Power BI 보고서 및 데이터 팩터리 작업이 있습니다.
  
* **스키마**: 열 또는 특성이라고도 하는 스키마는 자산 또는 리소스 집합의 구조를 나타냅니다.

* **리소스 집합**: 스토리지에 있는 여러 물리적 개체를 나타내는 카탈로그의 단일 개체입니다. 이러한 개체는 일반적으로 공통 스키마 및 대부분의 경우 명명 규칙 또는 폴더 구조를 공유합니다. 예를 들어 날짜 형식은 *yyyy/mm/dd* 입니다. 자세한 내용은 [리소스 집합 이해](concept-resource-sets.md)를 참조하세요.

* **자산 유형**: 일반적으로 데이터 플랫폼의 이름에 매핑되는 논리적 이름에 따라 그룹화된 자산 및 리소스 집합입니다.

자산을 검색하고 자신을 소유자로 표시하려면 다음 단계를 수행합니다.

1. 홈 페이지의 **카탈로그 검색** 상자에서 데이터 자산이 포함된 리소스 그룹(이전 자습서에서 만든 리소스 그룹)의 이름을 입력합니다. 제안 목록이 표시됩니다.

1. **검색 결과 보기** 를 선택합니다. 모든 자산이 리소스 그룹의 이름으로 시작되므로 검색 결과에 모두 표시됩니다. 이 자습서의 외부에서 리소스 그룹이 아닌 특정 자산 이름을 검색합니다.

    :::image type="content" source="./media/tutorial-asset-search/search-suggestions.png" alt-text="카탈로그 검색 제안 목록을 보여 주는 스크린샷":::

1. 왼쪽 탐색 영역의 필터를 사용하여 자산 유형, 분류, 연락처, 레이블 및 용어집 용어를 기준으로 필터링할 수 있습니다.

1. 리소스 집합을 검색하려면 집합 이름 입력을 시작합니다. 올바른 키워드가 포함된 검색 제안 목록이 표시됩니다. 또한 절대 이름을 따옴표로 묶어 검색할 수도 있습니다.

   :::image type="content" source="media/tutorial-asset-search/keyword-search.png" alt-text="Azure Purview 키워드 자산 검색":::

## <a name="edit-an-asset"></a>자산 편집

1. 검색 결과에서 자산 중 하나를 선택합니다. 그런 다음, **편집** 을 선택합니다.

1. **개요** 탭에서 설명을 추가할 수 있습니다.

    :::image type="content" source="./media/tutorial-asset-search/overview-tab.png" alt-text="개요 탭의 설명 필드를 보여 주는 스크린샷":::

1. **연락처** 탭을 선택합니다. **소유자** 옆의 **사용자 또는 그룹 선택** 에서 회사 이메일 주소 입력을 시작합니다.

    :::image type="content" source="./media/tutorial-asset-search/contacts-tab.png" alt-text="채워진 필드를 보여 주는 스크린샷":::

    이름 제안이 자동으로 표시됩니다.

1. **전문가** 옆의 **사용자 또는 그룹 선택** 에서 이름(예: 관리자 이름)을 입력한 다음, **저장** 을 선택합니다.

    이제 설명, 소유자 이름 및 전문가 이름 필드가 채워집니다.

## <a name="next-steps"></a>다음 단계

이 시리즈의 다음 자습서로 이동하기 전에 약간의 시간을 들여 Azure Purview 홈 페이지를 직접 살펴보세요. 이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * Azure Purview 홈 페이지를 탐색합니다.
> * 자산을 검색합니다.
> * 자산 소유자를 추가합니다.

다음 자습서로 계속 진행하여 Azure Purview에서 자산을 검색하고 자산 계보를 검색하는 방법을 알아보세요.

> [!div class="nextstepaction"]
> [자산 찾아보기 및 계보 보기](tutorial-browse-and-view-lineage.md)
