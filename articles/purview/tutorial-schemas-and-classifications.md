---
title: '자습서: Azure Purview(미리 보기)에서 리소스 집합, 세부 정보, 스키마 및 분류 살펴보기'
description: 이 자습서에서는 리소스 집합, 자산 세부 정보, 스키마 및 분류를 사용하는 방법을 설명합니다.
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: c39ed746630c646b0ce305a9535ae6d610f5161c
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549763"
---
# <a name="tutorial-explore-resource-sets-details-schemas-and-classifications-in-azure-purview-preview"></a>자습서: Azure Purview(미리 보기)에서 리소스 집합, 세부 정보, 스키마 및 분류 살펴보기

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기로 제공됩니다. [Microsoft Azure Preview용 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

이 자습서에서는 리소스 집합, 자산 세부 정보, 스키마 및 분류 등 카탈로그의 주요 구성 요소를 살펴봅니다.

이 자습서는 Azure Purview를 사용하여 데이터 자산 전체에서 데이터 거버넌스를 관리하는 방법에 대한 기본 사항을 학습하는 *5부로 구성된 자습서 시리즈 중 4부* 입니다. 이 자습서는 [3부: Azure Purview(미리 보기)에서 자산 찾아보기 및 해당 계보 보기](tutorial-browse-and-view-lineage.md)에서 완료한 작업을 기반으로 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 리소스 집합을 봅니다.
> * 자산 세부 정보를 봅니다.
> * 스키마를 편집하고 분류를 추가합니다.

## <a name="prerequisites"></a>필수 조건

* [자습서:  Azure Purview(미리 보기)에서 자산 찾아보기 및 해당 계보 보기](tutorial-browse-and-view-lineage.md)에서 완료한 작업을 기반으로 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="view-resource-sets"></a>리소스 집합 보기

리소스 집합은 스토리지에 있는 여러 물리적 개체를 나타내는 카탈로그의 단일 개체입니다. 일반적으로 개체는 공통 스키마를 공유하며 대부분의 경우 명명 규칙 또는 폴더 구조를 공유합니다. 예를 들어 날짜 형식은 *yyyy/mm/dd* 입니다. 리소스 집합에 대한 자세한 내용은 [리소스 집합 이해](concept-resource-sets.md)를 참조하세요.

1. Azure Portal에서 Azure Purview 리소스로 이동하여 **Purview Studio 열기** 를 선택합니다. Purview Studio의 홈 페이지로 자동으로 이동합니다.

2. **자산 검색** 상자에 **contoso_staging_positivecashflow_ n** 을 입력한 다음, 검색 결과에서 **Contoso_Staging_PositiveCashFlow_{N}.csv** 를 선택합니다.

## <a name="view-asset-details"></a>자산 세부 정보 보기

1. **개요** 탭에는 **qualifiedName** 과 같은 **설명**, **용어집** 및 **속성** 이 표시됩니다.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/overview-tab.png" alt-text="리소스 집합 자산 페이지의 개요 탭을 보여주는 스크린샷.":::

1. **속성** 에서 다음 두 필드를 확인합니다.

   * **partitionCount**: 이 리소스 집합에 연결된 실제 파일 수를 나타냅니다.
   * **schemaCount**: 이 리소스 집합 내에서 발견된 스키마의 변형 수를 나타냅니다.

   Azure Purview는 [이 자습서 시리즈의 1부](tutorial-scan-data.md)에서 검색을 완료한 후 24시간 이내에 이러한 속성을 채웁니다.

1. **연락처** 탭을 선택하여 **전문가** 및 **소유자** 값을 검토합니다.

## <a name="edit-the-schema-and-add-classifications"></a>스키마 편집 및 분류 추가

1. **스키마** 탭을 선택합니다. 열 이름과 관련된 분류를 확인합니다. 검사가 속성을 자동으로 채웠습니다.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/schema-tab.png" alt-text="스키마 탭을 보여주는 스크린샷.":::

1. 자산을 편집하려면 왼쪽 상단에 있는 **편집** 단추를 선택합니다. 그런 다음, **스키마** 탭을 선택합니다.

1. 열에 대한 **설명** 을 추가하거나 열 이름을 보다 알기 쉬운 이름으로 바꿉니다.

1. 설정된 분류가 없는 열 이름에 대해 **열 수준 분류** 드롭다운을 선택하여 자산 수준에서 분류를 추가합니다.

   :::image type="content" source="./media/tutorial-schemas-and-classifications/edit-schema.png" alt-text="스키마 편집 페이지":::

1. 변경을 마쳤으면 **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 리소스 집합을 봅니다.
> * 자산 세부 정보를 봅니다.
> * 스키마를 편집하고 분류를 추가합니다.

다음 자습서로 이동하여 용어집 및 자산에 대한 새 용어를 정의하고 가져오는 방법에 대해 알아보세요.

> [!div class="nextstepaction"]
> [용어집 만들기 및 가져오기](tutorial-import-create-glossary-terms.md)