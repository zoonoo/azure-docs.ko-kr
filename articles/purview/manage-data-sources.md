---
title: Azure 부서의 범위 (미리 보기)에서 데이터 원본 관리
description: Azure 부서의 범위 (미리 보기)에서 새 데이터 원본을 등록 하 고, 데이터 원본의 컬렉션을 관리 하 고, 소스를 보는 방법에 대해 알아봅니다.
author: mamccrea
ms.author: mamccrea
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/25/2020
ms.openlocfilehash: ef54c40715c874364beb188d8850a9ce953aa112
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96554625"
---
# <a name="manage-data-sources-in-azure-purview-preview"></a>Azure 부서의 범위 (미리 보기)에서 데이터 원본 관리

이 문서에서는 Azure 부서의 범위 (미리 보기)에서 새 데이터 원본을 등록 하 고, 데이터 원본의 컬렉션을 관리 하 고, 소스를 보는 방법에 대해 알아봅니다. Azure 부서의 범위는 다음과 같은 데이터 원본을 지원 합니다.

* SQL server 온-프레미스
* Azure Data Lake Storage Gen1 
* Azure Data Lake Storage Gen2
* Azure Blob Storage
* Azure Data Explorer
* Azure SQL DB
* Azure SQL DB Managed Instance
* Azure Synapse Analytics(이전의 SQL DW)
* Azure Cosmos DB
* Power BI

## <a name="register-a-new-source"></a>새 원본 등록

다음 단계를 사용 하 여 새 원본을 등록 합니다.

1. 부서의 범위 Studio를 열고 **소스 등록** 타일을 선택 합니다.

   :::image type="content" source="media/manage-data-sources/purview-studio.png" alt-text="Azure 부서의 범위 Studio":::

1. **등록** 을 선택한 후 원본 유형을 선택 합니다. 이 예제에서는 Azure Blob Storage를 사용 합니다. **계속** 을 선택합니다.

   :::image type="content" source="media/manage-data-sources/select-source-type.png" alt-text="원본 등록 페이지에서 데이터 원본 유형을 선택 합니다.":::

1. **소스 등록** 페이지의 양식을 작성 합니다. 원본 이름을 선택 하 고 관련 정보를 입력 합니다. 계정 선택 방법으로 **Azure 구독에서** 선택 하는 경우 구독의 소스가 드롭다운 목록에 표시 됩니다. 또는 원본 정보를 수동으로 입력할 수 있습니다.

   :::image type="content" source="media/manage-data-sources/register-sources-form.png" alt-text="데이터 원본 정보에 대 한 양식":::

1. **마침** 을 선택합니다.

## <a name="view-sources"></a>원본 보기

Azure 부서의 범위 Studio의 **원본** 탭에서 등록 된 모든 원본을 볼 수 있습니다. 지도 보기와 목록 보기의 두 가지 보기 유형이 있습니다.

### <a name="map-view"></a>맵 보기

지도 보기에서 모든 원본 및 컬렉션을 볼 수 있습니다. 다음 그림에는 Azure Blob Storage 원본이 하나 있습니다. 각 원본 타일에서 소스를 편집 하거나, 새 검색을 시작 하거나, 소스 세부 정보를 볼 수 있습니다.

:::image type="content" source="media/manage-data-sources/map-view.png" alt-text="Azure 부서의 범위 데이터 원본 맵 뷰":::

### <a name="list-view"></a>목록 뷰

목록 뷰에서 정렬 가능한 원본 목록을 볼 수 있습니다. 편집, 새 검색 시작 또는 삭제 옵션의 원본을 마우스로 가리킵니다.

:::image type="content" source="media/manage-data-sources/list-view.png" alt-text="Azure 부서의 범위 데이터 원본 목록 뷰":::

## <a name="manage-collections"></a>컬렉션 관리

데이터 원본을 컬렉션으로 그룹화 할 수 있습니다. 새 컬렉션을 만들려면 Azure 부서의 범위 Studio의 *원본* 페이지에서 **+ 새 컬렉션** 을 선택 합니다. 컬렉션에 이름을 지정 하 고 부모로 *없음* 을 선택 합니다. 새 컬렉션이 지도 보기에 나타납니다.

컬렉션에 원본을 추가 하려면 소스에서 **Edit** 연필을 선택 하 고 **컬렉션 선택** 드롭다운 메뉴에서 컬렉션을 선택 합니다.

컬렉션의 계층 구조를 만들려면 상위 수준 컬렉션의 상위 수준 컬렉션에 상위 수준 컬렉션을 할당 합니다. 다음 그림에서 *Fabrikam* 은 Azure Blob Storage 데이터 원본을 포함 하는 *재무* 컬렉션의 부모입니다. 수준 사이에 화살표에 연결 된 원을 클릭 하 여 컬렉션을 축소 하거나 확장할 수 있습니다.

:::image type="content" source="media/manage-data-sources/collections.png" alt-text="Azure 부서의 범위 Studio에서 컬렉션의 계층 구조":::

부모에 대해 *없음* 을 선택 하 여 계층에서 원본을 제거할 수 있습니다. Unparented 원본은 지도 보기의 점선 상자에 그룹화 되며, 부모에 연결 하는 화살표는 표시 되지 않습니다.

## <a name="next-steps"></a>다음 단계

다양 한 데이터 원본을 등록 하 고 검색 하는 방법을 알아봅니다.

* [Azure Data Lake Storage Gen 2](register-scan-adls-gen2.md)
* [Power BI 테넌트](register-scan-power-bi-tenant.md)
* [Azure SQL Database](register-scan-azure-sql-database.md)
