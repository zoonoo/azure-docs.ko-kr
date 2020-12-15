---
title: '자습서: Azure Purview에서 용어집 용어 만들기 및 가져오기(미리 보기)'
description: 이 자습서에서는 용어집 용어를 만들고, 자산에 추가하고, 가져오는 방법을 설명합니다.
author: hophan
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 3a81d9480dfb07703d5f1ccfb495d069f140dd71
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96549833"
---
# <a name="tutorial-create-and-import-glossary-terms-in-azure-purview-preview"></a>자습서: Azure Purview에서 용어집 용어 만들기 및 가져오기(미리 보기)

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기에 있습니다. [Microsoft Azure 미리 보기에 대한 추가 사용 조건](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기에 있거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

용어집은 카탈로그를 유지 관리하고 구성하는 데 중요한 도구입니다. 새 용어를 정의하거나 용어 목록을 가져온 다음, 해당 용어를 자산에 적용하여 용어집을 작성합니다.

이 자습서는 Azure Purview를 사용하여 데이터 자산 전체에서 데이터 거버넌스를 관리하는 방법에 대한 기본 사항을 알아볼 수 있는 *5부로 구성된 자습서 시리즈 중 5부* 입니다. 이 자습서는 [4부: Azure Purview에서 리소스 집합, 세부 정보, 스키마 및 분류 살펴보기(미리 보기)](tutorial-schemas-and-classifications.md)에서 완료한 작업을 기반으로 합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
>
> * 용어집 용어를 만듭니다.
> * 용어집 용어를 자산에 추가합니다.
> * 용어집 용어를 가져옵니다.

## <a name="prerequisites"></a>필수 조건

* [자습서: Azure Purview에서 리소스 집합, 세부 정보, 스키마 및 분류 살펴보기(미리 보기)](tutorial-schemas-and-classifications.md)를 완료합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-glossary-terms"></a>용어집 용어 만들기

용어집에서 비즈니스 또는 기술 용어를 만들 수 있습니다. 또한 용어를 적용하여 자산에 주석을 달 수 있습니다.

**용어집 용어** 페이지의 가장 일반적인 필드 중 일부는 다음과 같이 요약됩니다.

* **상태**: 상태(**초안**, **승인됨**, **만료됨**, **경고**)를 용어에 할당합니다.

* **정의**: 용어에 대한 정의를 입력합니다.

* **머리글자어**: 각 단어의 첫 문자를 사용하여 용어의 약어 버전을 입력합니다.

* **동의어**: 정의가 같거나 비슷한 다른 용어를 선택합니다.

* **관련 용어**: 용어집에서 이 용어와 관련이 있지만 정의가 다른 용어를 선택합니다. 예를 들어 비즈니스 용어, 코드 이름 또는 해당 용어와 연관되어야 하는 기타 용어와 관련된 기술 용어가 있습니다.

다음 단계에 따라 용어집 용어를 만듭니다.

1. Azure Portal에서 Azure Purview 리소스로 이동하여 **Purview Studio 열기** 를 선택합니다. Purview Studio 홈 페이지로 자동으로 이동합니다.

1. 왼쪽 창에서 **용어집** 을 선택하여 **용어집 용어** 페이지를 엽니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/glossary-terms-page.png" alt-text="용어집 용어 페이지를 보여 주는 스크린샷":::

1. **새 용어** > **시스템 기본값** > **계속** 을 차례로 선택합니다.

1. **새 용어** 페이지의 **개요** 탭에서 새 용어의 **이름**(예: *재무*)을 입력합니다.

1. **정의**(예: *이 용어는 Contoso Inc.의 재무 정보를 나타냅니다.* )를 입력합니다.

1. **상태** 드롭다운 목록에서 **승인됨** 을 선택합니다.

1. 작업이 완료되면 **만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/enter-new-glossary-term.png" alt-text="새 용어집 용어를 만드는 방법을 보여 주는 스크린샷":::

## <a name="add-glossary-terms-to-an-asset"></a>자산에 용어집 용어 추가

1. 이 [자습서 시리즈의 1부](tutorial-scan-data.md)에서 학습한 단계를 사용하여 자산을 찾습니다. 예를 들어 **Contoso_CashFlow_{N}.csv** 입니다.

1. 자산 세부 정보 페이지에서 **편집** 을 선택합니다.

1. **개요** 탭의 **용어집 용어** 드롭다운 목록에서 **재무** 를 선택한 다음, **저장** 을 선택합니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-glossary-term-to-asset.png" alt-text="용어집 용어를 자산에 추가하는 방법을 보여 주는 스크린샷":::

1. **개요** 탭의 **용어집 용어** 섹션으로 이동하여 *재무* 라는 용어가 자산에 적용되었는지 확인합니다.

## <a name="import-glossary-terms"></a>용어집 용어 가져오기

대량으로 용어를 가져오거나 기존 용어를 업데이트하려면 미리 채워진 템플릿을 용어집에 업로드합니다.

다음 절차에서는 .csv 파일을 통해 용어집 용어를 가져옵니다.

1. [이 자습서 시리즈의 1부](tutorial-scan-data.md)에서 다운로드한 시작 키트의 일부인 *StarterKitTerms.csv* 라는 파일이 저장된 위치를 확인합니다.

   이 파일에는 데이터 자산에 해당하는 미리 채워진 용어 목록이 포함되어 있습니다.

1. 가져오기를 시작하려면 **용어집** 을 선택한 다음, **용어 가져오기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-import-create-glossary-terms/import-glossary-terms-select.png" alt-text="용어집 용어를 가져오는 방법을 보여 주는 스크린샷":::

1. **용어 가져오기** 페이지에서 **시스템 기본값** 을 선택한 다음, **계속** 을 선택합니다.

1. **찾아보기** 를 선택하고, *StarterKitTerms.csv* 를 다운로드한 위치로 이동한 다음, **열기** 를 선택합니다.

1. **확인** 을 선택하여 용어 가져오기를 시작합니다.

   가져오기가 완료되면 **용어집 용어** 페이지에 새 용어집 용어가 표시됩니다.

1. 새로 가져온 각 용어를 확인하여 정의된 방법을 확인합니다.

## <a name="create-custom-term-templates"></a>사용자 지정 용어 템플릿 만들기

이 섹션에서는 사용자 지정 특성이 포함된 사용자 지정 용어 템플릿을 만들고, 템플릿 csv 파일을 사용하여 데이터를 가져오는 방법에 대해 알아봅니다.

**용어집** > **용어 템플릿 관리** > **시스템** 을 차례로 선택하여 볼 수 있는 몇 가지 기존 시스템 용어 템플릿이 있습니다.

:::image type="content" source="./media/tutorial-import-create-glossary-terms/system-term-templates.png" alt-text="시스템 용어 템플릿":::

새 사용자 지정 용어 템플릿을 만들려면 다음 단계를 수행합니다.

1. 왼쪽 메뉴에서 **용어집** 을 선택합니다.
1. **용어 템플릿 관리** > **사용자 지정** > **새 용어 템플릿** 을 차례로 선택합니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-new-custom-term-template.png" alt-text="새 사용자 지정 용어 템플릿 만들기":::

**새 용어 템플릿** 화면에서 다음 단계를 수행합니다.

1. **템플릿 이름**(예: `Sensitivity level`)을 입력합니다.
1. 원하는 설명(예: `Indicates the level of sensitivity for this data.`)을 입력합니다.
1. **+ 새 특성** 을 선택하여 특성 추가 대화 상자를 엽니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/new-term-template-screen-start.png" alt-text="새 용어 템플릿 화면 시작":::

1. **새 특성** 화면에서 다음 매개 변수를 입력합니다.

   |설정|제안 값|
   |---------|-----------|
   |특성 이름|중요한 정보|
   |필드 형식|드롭다운|단일 선택|
   |필수로 표시|이 확인란을 선택합니다.|
   |+ 선택 항목 추가| 두 가지 선택 항목("예" 및 "아니요")을 추가합니다.|

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/add-new-attribute.png" alt-text="새 특성 추가.":::

1. 이전 단계를 반복하여 이름이 `can be shared externally`인 다른 특성을 추가합니다. 두 특성이 모두 추가되면 마지막으로 **만들기** 를 선택합니다.

## <a name="import-terms-from-a-template"></a>템플릿에서 용어 가져오기

다음으로, 사용자가 만든 **민감도 수준** 템플릿을 사용하여 새 용어를 가져옵니다. 

1. **용어집 용어** 화면에서 **용어 가져오기** 를 선택합니다.

1. **용어 가져오기** 화면에서 **민감도 수준** 을 선택합니다. **계속** 을 선택합니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/select-sensitivity-level.png" alt-text="민감도 수준 선택":::

1. **민감도 수준** 에 대한 용어 템플릿에는 기본 시스템 특성 및 추가한 새 특성(`can be shared externally` 및 `is sensitive information`)이 포함되어 있습니다. **샘플 .CSV 파일 다운로드** 를 선택합니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/download-sample-csv-file.png" alt-text="샘플 csv 파일 다운로드":::

1. 고객 특성이 포함된 용어집 용어를 편집하고 업로드하기 위한 템플릿 파일이 다운로드됩니다. 다운로드한 CSV 파일을 엽니다. CSV 파일에서 새 용어와 해당 값을 새 행으로 추가합니다.

   :::image type="content" source="./media/tutorial-import-create-glossary-terms/create-term-in-csv.png" alt-text="csv 파일에서 용어 만들기":::

   파일이 업로드되면 아직 존재하지 않는 **관련 용어** 또는 **동의어** 열에 나열된 모든 용어가 만들어집니다. 이는 **시스템 기본값** 템플릿을 사용하여 만들어집니다.

1. 파일을 업로드하려면 **용어 가져오기** 화면으로 돌아가서 **업로드할 전체 .CSV 파일 선택** 상자 옆에 있는 **찾아보기** 를 선택합니다. 열리는 대화 상자에서 파일을 선택한 다음, **확인** 을 선택합니다.

1. 이제 새 용어가 **용어집 용어** 화면에 나열됩니다. 목록에서 용어 이름을 클릭하여 새 용어에 대한 세부 정보를 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 용어집 용어를 만듭니다.
> * 용어집 용어를 자산에 추가합니다.
> * 용어집 용어를 가져옵니다.

다음 문서로 계속 진행하여 다양한 카탈로그 인사이트에 대해 알아보세요.

> [!div class="nextstepaction"]
> [Azure Purview의 인사이트 이해](concept-insights.md)
