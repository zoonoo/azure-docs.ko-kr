---
title: '방법: Data Catalog 찾아보기'
description: 이 문서에서는 자산 유형별로 Azure Purview Data Catalog를 찾아보는 방법에 대한 개요를 제공합니다
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 05/21/2021
ms.openlocfilehash: cf836be45374b881945a80f998d851c95c7a5abd
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/26/2021
ms.locfileid: "110474186"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog 찾아보기

이 문서에서는 데이터 원본 계층 구조 네임스페이스를 사용하여 Azure Purview Data Catalog에서 데이터를 검색하는 방법을 설명합니다.

## <a name="browse-experience"></a>찾아보기 환경

데이터 소비자는 탐색기 보기를 통해 각 데이터 원본의 친숙한 계층 구조 네임스페이스를 사용하여 데이터를 검색할 수 있습니다. 데이터 원본이 등록되고 검색되고 나면, 데이터 맵은 데이터 원본의 구조(계층 구조 네임스페이스)에 대한 정보를 추출합니다. 이 정보는 데이터 검색을 위한 검색 환경을 구축하는 데 사용됩니다.

예를 들어, Azure Data Lake Storage Gen 2의 *Dimensions* 이라는 폴더 아래에서 *DateDimension* 이라는 데이터 세트를 쉽게 찾을 수 있습니다. ‘자산 유형별 찾아보기’ 환경을 사용하여 ADLS Gen 2 스토리지 계정으로 이동한 다음, 서비스 > 컨테이너 > 폴더로 이동하여 특정 *Dimensions* 폴더에 들어가서 *DateDimension* 테이블을 볼 수 있습니다.

해당하는 각 데이터 원본에 대해 계층 구조 네임스페이스를 사용하여 네이티브 검색 환경을 제공합니다.

> [!NOTE]
> 성공적으로 스캔한 후에는 새로 스캔한 자산이 찾아보기 환경에 표시되기 전에 지연이 발생할 수 있습니다. 이 지연은 몇 시간이 걸릴 수 있습니다.

## <a name="browse-the-data-catalog-by-asset-type"></a>자산 유형별 Data Catalog 찾아보기

1. 홈페이지에서 **자산 유형별 찾아보기** 를 선택하여 데이터 자산을 찾아볼 수 있습니다.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="Purview 홈페이지" border="true":::

1. **자산 유형 찾아보기** 페이지에서 타일은 데이터 원본으로 분류됩니다. 각 데이터 원본의 자산을 추가로 탐색하려면 해당 타일을 선택합니다.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="자산 유형 찾아보기 페이지" border="true":::

> [!TIP]
> 특정 타일은 데이터 원본의 컬렉션을 그룹화한 것입니다. 예를 들어, Azure Storage 계정 타일에는 모든 Azure Blob Storage 및 Azure Data Lake Storage Gen2 계정이 포함되어 있습니다. Azure SQL Server 타일에는 Azure SQL Database 및 수집된 Azure Dedicated SQL Pool 인스턴스를 포함하는 Azure SQL Server 자산이 카탈로그에 표시됩니다. 

1. 다음 페이지에서는 선택한 데이터 형식 아래에 최상위 자산이 나열됩니다. 자산 중 하나를 선택해 내용을 추가로 탐색합니다.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="자산 유형별 찾아보기 페이지입니다. 예시는 Azure Storage 계정입니다." border="true":::

1. 탐색기 보기가 열립니다. 왼쪽 패널에서 자산을 선택하여 검색을 시작합니다. 하위 자산은 페이지의 오른쪽 패널에 나열됩니다.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="탐색기 보기" border="true":::

1. 자산의 세부 정보를 보려면 맨 오른쪽에 있는 이름 또는 줄임표 단추를 선택합니다.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="자산 세부 정보 페이지를 보려면 줄임표 단추를 선택합니다." border="true":::

## <a name="view-related-data-assets"></a>관련된 데이터 자산 보기

자산 세부 정보 페이지에서 다른 관련 데이터 자산을 볼 수도 있습니다. 예를 들어, *DateDimension* 의 상위 폴더로 이동하여 *Dimensions* 폴더를 보거나, 컨테이너로 이동하여 특정 계층 아래의 자산을 볼 수 있습니다.

1. 자산 세부 정보 페이지에서 **관련 항목** 탭을 선택하여 다른 관련 자산을 확인합니다.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="시작 관련 항목 탭" border="true":::

1. 현재 자산의 전체 계층이 왼쪽에 나열됩니다.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="계층 구조" border="true":::

1. 계층의 모든 수준을 선택하여 해당 수준에서 직접 자산을 나열합니다.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="다른 계층 선택" border="true":::

## <a name="next-steps"></a>다음 단계

- [용어집 용어를 만들고, 가져오며, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대한 용어 템플릿을 관리하는 방법](how-to-manage-term-templates.md)
