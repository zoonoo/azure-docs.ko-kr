---
title: '방법: Data Catalog 찾아보기'
description: 이 문서에서는 자산 유형에 따라 Azure 부서의 범위 Data Catalog를 찾아보는 방법에 대 한 개요를 제공 합니다.
author: hrasheed-msft
ms.author: hrasheed
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/30/2020
ms.openlocfilehash: b8cdbbc29472ae10920c347dde308c352bf0b68a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553605"
---
# <a name="browse-the-azure-purview-data-catalog"></a>Azure 부서의 범위 Data catalog 찾아보기

이 문서에서는 데이터 원본 계층적 네임 스페이스를 사용 하 여 Azure 부서의 범위 Data Catalog에서 데이터를 검색 하는 방법을 설명 합니다.

## <a name="browse-experience"></a>환경 찾아보기

데이터 소비자는 탐색기 뷰를 사용 하 여 각 데이터 원본에 대 한 친숙 한 계층적 네임 스페이스를 사용 하 여 데이터를 검색할 수 있습니다. 데이터 원본이 등록 되 고 검색 되 면 데이터 맵은 데이터 원본의 구조 (계층 구조 네임 스페이스)에 대 한 정보를 추출 합니다. 이 정보는 데이터 검색을 위한 검색 환경을 구축 하는 데 사용 됩니다.

예를 들어 Azure Data lake Storage Gen 2의 *차원* 이라는 폴더 아래에서 *datedimension* 이라는 데이터 집합을 쉽게 찾을 수 있습니다. ' Asset 유형별 찾아보기 ' 환경을 사용 하 여 ADLS Gen 2 저장소 계정으로 이동한 다음 서비스 > 컨테이너 > 폴더로 이동 하 여 특정 *차원* 폴더에 연결 하 고 *datedimension* 테이블을 볼 수 있습니다.

해당 하는 각 데이터 소스에 대해 계층적 네임 스페이스를 사용 하 여 네이티브 검색 환경을 제공 합니다.

## <a name="browse-the-data-catalog-by-asset-type"></a>자산 유형별 Data Catalog 찾아보기

1. 홈페이지에서 **자산 형식 찾아보기** 를 선택 하 여 데이터 자산을 찾아볼 수 있습니다.

    :::image type="content" source="media/how-to-browse-catalog/studio-home-page.png" alt-text="부서의 범위 홈 페이지" border="true":::

1. **자산 유형 찾아보기** 페이지에서 타일은 데이터 원본으로 분류 됩니다. 각 데이터 원본의 자산을 추가로 탐색 하려면 해당 타일을 선택 합니다.

    :::image type="content" source="media/how-to-browse-catalog/browse-asset-types.jpg" alt-text="자산 유형 찾아보기 페이지" border="true":::

1. 다음 페이지에서는 선택한 데이터 형식 아래에 최상위 자산이 나열 됩니다. 자산 중 하나를 선택 하 여 해당 내용을 자세히 탐색 합니다.

    :::image type="content" source="media/how-to-browse-catalog/asset-type-specific-browse.jpg" alt-text="자산 유형 특정 찾아보기 페이지. 표시 된 예: Azure Storage 계정" border="true":::

1. 탐색기 뷰가 열립니다. 왼쪽 패널에서 자산을 선택 하 여 검색을 시작 합니다. 자식 자산은 페이지의 오른쪽 패널에 나열 됩니다.

    :::image type="content" source="media/how-to-browse-catalog/explorer-view.jpg" alt-text="탐색기 보기" border="true":::

1. 자산의 세부 정보를 보려면 맨 오른쪽에 있는 줄임표 단추 또는 이름을 선택 합니다.

    :::image type="content" source="media/how-to-browse-catalog/view-asset-detail-click-ellipses.jpg" alt-text="줄임표 단추를 선택 하 여 자산 세부 정보 페이지를 표시 합니다." border="true":::

## <a name="view-related-data-assets"></a>관련된 데이터 자산 보기

자산 세부 정보 페이지에서 다른 관련 데이터 자산을 볼 수도 있습니다. 예를 들어 *Datedimension* 의 부모 폴더로 이동 하 여 *차원* 폴더를 표시 하거나 컨테이너를 탐색 하 여 특정 계층의 자산을 확인할 수 있습니다.

1. 자산 정보 페이지에서 **관련** 탭을 선택 하 여 다른 관련 자산을 확인 합니다.

    :::image type="content" source="media/how-to-browse-catalog/launch-related-tab.jpg" alt-text="시작 관련 탭" border="true":::

1. 현재 자산의 전체 계층이 왼쪽에 나열 됩니다.

    :::image type="content" source="media/how-to-browse-catalog/hierarchical-structure.jpg" alt-text="계층 구조" border="true":::

1. 계층에서 수준을 선택 하 여 해당 수준 아래에 직접 자산을 나열 합니다.

    :::image type="content" source="media/how-to-browse-catalog/select-different-hierarchy.jpg" alt-text="다른 계층 선택" border="true":::

## <a name="next-steps"></a>다음 단계

- [용어를 만들고, 가져오고, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대 한 용어 템플릿을 관리 하는 방법](how-to-manage-term-templates.md)
