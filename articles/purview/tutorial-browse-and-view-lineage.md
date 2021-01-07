---
title: '자습서: Azure Purview에서 자산 찾아보기 및 해당 계보 보기'
description: 이 자습서에서는 카탈로그에서 자산을 찾고 데이터 계보를 확인하는 방법을 설명합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: tutorial
ms.date: 12/01/2020
ms.openlocfilehash: 7ffbe2ded44ded4f580655f6ae9e98391490f94a
ms.sourcegitcommit: b6267bc931ef1a4bd33d67ba76895e14b9d0c661
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/19/2020
ms.locfileid: "97696099"
---
# <a name="tutorial-browse-assets-in-azure-purview-preview-and-view-their-lineage"></a>자습서: Azure Purview(미리 보기)에서 자산 찾아보기 및 해당 계보 보기

> [!IMPORTANT]
> Azure Purview는 현재 미리 보기로 제공됩니다. [Microsoft Azure Preview용 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.

이 자습서에서는 Azure Purview에서 자산을 찾아보고 계보와 같은 중요한 세부 정보를 보는 방법을 보여줍니다.

이 자습서는 Azure Purview를 사용하여 데이터 자산 전체에서 데이터 거버넌스를 관리하는 방법에 대한 기본 사항을 학습하는 *5부로 구성된 자습서 시리즈 중 3부* 입니다. 이 자습서는 [2부: Azure Purview(미리 보기) 홈 페이지 탐색 및 자산 검색](tutorial-asset-search.md)에서 완료한 작업을 기반으로 합니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
>
> * 카탈로그에서 자산을 찾습니다.
> * 자산의 계보를 봅니다.

## <a name="prerequisites"></a>필수 조건

* [자습서: Azure Purview(미리 보기) 홈 페이지 탐색 및 자산 검색](tutorial-asset-search.md)에서 완료한 작업을 기반으로 합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="browse-for-assets-in-the-catalog"></a>카탈로그에서 자산 찾아보기

이전 자습서에서는 검색을 통해 Azure Purview 카탈로그의 자산을 검색하는 방법을 알아보았습니다. 카탈로그에서 자산을 검색하는 또 다른 방법은 카탈로그의 검색 환경을 사용하는 것입니다.

이 섹션에서는 Azure Purview 카탈로그를 검색하는 방법을 보여줍니다.

1. Azure Portal에서 Azure Purview 리소스로 이동하여 **Purview Studio 열기** 를 선택합니다. Purview Studio의 홈 페이지로 자동으로 이동합니다.

1. **홈** 페이지에서 **자산 찾아보기** 를 선택합니다.

   카탈로그에 있는 모든 자산 유형의 요약을 표시하는 **자산 찾아보기** 페이지가 나타납니다.

1. 카탈로그에서 사용할 수 있는 다양한 Azure Data Lake Gen2 유형 자산을 살펴보려면 **Azure Data Lake Gen2** 타일을 선택합니다.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/browse-by-asset-type.png" alt-text="Azure Data Lake Gen2가 선택된 자산 유형별 찾아보기 페이지를 보여주는 스크린샷.":::

1. 자산이 여러 개인 경우 **이름** 열 헤더를 선택하여 자산을 사전순으로 정렬할 수 있습니다. 이 자습서에서는 Azure Data Lake Storage Gen2 자산이 하나만 있습니다.

1. 자산의 이름을 선택합니다.

1. **Contoso_GrossProfit_{N}.ssv** 리소스 집합을 선택합니다. 이 자산이 카탈로그에 없으면 다른 자산을 선택합니다.

   :::image type="content" source="media/tutorial-browse-and-view-lineage/view-adls-assets.png" alt-text="Azure Data Lake Storage Gen2 리소스 목록":::

## <a name="view-the-lineage-of-assets"></a>자산의 계보 보기

자산 세부 정보 페이지에서 데이터 원본을 탐색합니다.

1. **Contoso_GrossProfit_{N}.ssv** 리소스 집합의 **계보** 탭을 선택합니다.

   선택한 자산이 표시됩니다. 표시되는 계보 정보에서는 이 리소스 집합이 Azure Blob Storage 계정에서 Azure Data Lake Storage Gen2로 복사되었음을 보여줍니다.

   :::image type="content" source="./media/tutorial-browse-and-view-lineage/view-lineage.png" alt-text="자산에 대한 계보 보기를 보여주는 스크린샷.":::

1. 이 페이지에서 Blob 자산을 선택하고 **자산으로 전환** 링크를 선택합니다.

   창이 원래 Azure Data Lake Storage Gen2의 원본인 Azure Blob 리소스 집합으로 전환되었는지 확인합니다.

1. **개요** 탭을 선택하여 자산을 조사하고 세부 정보를 확인합니다.

Azure Blob과 Azure Data Lake Storage Gen2 리소스 집합 간에 Azure Data Factory 데이터 흐름 작업을 만들고 계보를 관찰하는 방법에 대한 자세한 내용은 [Azure Data Factory 데이터 흐름 작업](../data-factory/concepts-data-flow-overview.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * 카탈로그에서 자산을 찾습니다.
> * 자산의 계보를 봅니다.

리소스 집합, 자산 세부 정보, 스키마 및 분류에 대해 알아보려면 다음 자습서로 이동합니다.

> [!div class="nextstepaction"]
> [리소스 집합, 자산 세부 정보, 스키마 및 분류](tutorial-schemas-and-classifications.md)
