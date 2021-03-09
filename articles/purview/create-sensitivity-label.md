---
title: 데이터에 민감도 레이블을 자동으로 적용
description: 민감도 레이블을 만들고 검색 하는 동안 데이터에 자동으로 적용 하는 방법에 대해 알아봅니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 37ac292acc76c681ea38b2ae881ff8cd2ae5ec3c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/09/2021
ms.locfileid: "102502449"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Azure 부서의 범위에서 데이터에 자동으로 레이블

이 문서에서는 Microsoft Information Protection (밉) 민감도 레이블을 만들고 Azure 부서의 범위의 Azure 자산에 자동으로 적용 하는 방법을 설명 합니다.

## <a name="what-are-sensitivity-labels"></a>민감도 레이블 이란? 

작업을 완료 하기 위해 조직의 사용자는 조직 내부 및 외부의 다른 사용자와 공동 작업을 수행 합니다. 데이터는 항상 클라우드에 유지 되지 않으며 장치, 앱 및 서비스에서 어디에서 나 로밍 하기도 합니다. 

데이터를 로밍 하는 경우 조직의 비즈니스 및 규정 준수 정책을 충족 하는 안전 하 고 보호 된 방식으로이 작업을 수행 하려고 합니다.

민감도 레이블을 적용 하면 조직에서 중요 한 특정 데이터의 상태를 확인할 수 있습니다. 예를 들어 조직 내에서 특정 프로젝트 이름에 대 한 기밀을 유지 하는 반면 동일한 용어는 다른 조직에 대 한 기밀은 아닙니다. 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure 부서의 범위의 민감도 레이블

부서의 범위에서 분류는 subject 태그와 비슷하며 검색 하는 동안 데이터 공간 내에 있는 특정 유형의 데이터를 표시 하 고 식별 하는 데 사용 됩니다.

부서의 범위는 Microsoft 365 같이 중요 한 정보 형식이 라고도 하는 동일한 분류를 사용 합니다.  밉 민감도 레이블은 Microsoft 365 보안 및 규정 준수 센터 (SCC)에서 생성 됩니다. 이렇게 하면 Azure 부서의 범위 자산에서 기존 민감도 레이블을 확장할 수 있습니다.

**분류는** **주민 등록** 번호를 분류 하는 주민 등록 번호와 같이 직접 일치 합니다. 

이와 달리 **민감도 레이블은** 하나 이상의 분류 및 조건을 함께 발견할 경우 적용 됩니다. 이 컨텍스트에서 [조건은](/microsoft-365/compliance/apply-sensitivity-label-automatically) *다른 분류에 근접* 한 경우와 같이 비구조적 데이터에 대해 정의할 수 있는 모든 매개 변수를 *나타냅니다.* 

Azure 부서의 범위의 민감도 레이블을 사용 하 여 파일 및 데이터베이스 열에 레이블을 자동으로 적용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- Microsoft 365 설명서의 [민감도 레이블에 대 한 자세한 정보](/microsoft-365/compliance/sensitivity-labels)
- [자동 레이블 지정 규칙 이란?](#what-are-auto-labeling-rules)
- [Azure 부서의 범위의 민감도 레이블에 대해 지원 되는 데이터 형식](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [SQL 데이터베이스 열에 대 한 레이블 지정](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>자동 레이블 지정 규칙 이란?

데이터는 지속적으로 증가 하 고 변화 하 고 있습니다. 현재 레이블이 없는 데이터를 추적 하 고 수동으로 레이블을 적용 하는 작업을 수행 하는 것은 번거로울 뿐 아니라 불필요 한 골칫거리 뿐입니다. 

자동 레이블 지정 규칙은 특정 레이블을 적용 해야 하는 경우를 지정 하는 조건입니다. 이러한 조건이 충족 되 면 레이블은 데이터에 자동으로 할당 되어 데이터에 일관 된 민감도 레이블을 유지 합니다.

레이블을 만들 때 [파일](#define-auto-labeling-rules-for-files) 및 [데이터베이스 열](#define-auto-labeling-rules-for-database-columns) 모두에 대 한 자동 레이블 지정 규칙을 정의 하 여 각 데이터 검색에 레이블을 자동으로 적용 해야 합니다. 

부서의 범위에서 데이터를 검사 한 후 부서의 범위 카탈로그 및 정보 보고서에서 자동으로 적용 되는 레이블을 볼 수 있습니다.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure 부서의 범위의 민감도 레이블에 대해 지원 되는 데이터 형식

민감도 레이블은 Azure 부서의 범위에서 다음 데이터 형식에 대해 지원 됩니다.

|데이터 형식  |원본  |
|---------|---------|
|파일에 대 한 자동 레이블 지정     |      - Azure Blob Storage  </br>-Azure Data Lake Storage Gen 1 및 Gen 2  |
|데이터베이스 열에 대 한 자동 레이블 지정     |  -SQL server </br>-Azure SQL database </br>-Azure SQL Database Managed Instance   <br> -Azure Synapse  <br> - Azure Cosmos DB <br><br>자세한 내용은 아래의 [SQL 데이터베이스 열에 대 한 레이블](#labeling-for-sql-database-columns) 지정을 참조 하세요.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>SQL 데이터베이스 열에 대 한 레이블 지정

데이터베이스 열에 대 한 부서의 범위 레이블 지정 외에도 Microsoft는 [SSMS (SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)에서 sql 데이터 분류를 사용 하 여 sql database 열에 대 한 레이블 지정을 지원 합니다. 부서의 범위는 글로벌 [밉 민감도 레이블을](/microsoft-365/compliance/sensitivity-labels)사용 하는 반면 SSMS는 로컬로 정의 된 레이블만 사용 합니다.

SSMS에서 부서의 범위 및 레이블 지정은 현재 서로 상호 작용 하지 않는 별도의 프로세스입니다. 따라서 SSMS에서 적용 되는 레이블은 부서의 범위에 표시 되지 않으며 그 반대의 경우도 마찬가지입니다. 여러 플랫폼에 적용 될 수 있는 글로벌 밉 레이블을 사용 하므로 SQL 데이터베이스에 레이블을 지정 하는 데 Azure 부서의 범위를 사용 하는 것이 좋습니다.

자세한 내용은 [SQL 데이터 검색 및 분류 설명서](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조 하세요.

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Microsoft 365에서 민감도 레이블을 만드는 방법

민감도 레이블이 아직 없는 경우 Azure 부서의 범위에 사용할 수 있도록 만들어야 합니다. Azure 부서의 범위에 사용할 수 있도록 기존 민감도 레이블을 수정할 수도 있습니다.

자세한 내용은 다음을 참조하세요.

- [라이선싱 요구 사항](#licensing-requirements)
- [Azure 부서의 범위에 대 한 민감도 레이블 확장](#extending-sensitivity-labels-to-azure-purview)
- [새 민감도 레이블 만들기 또는 기존 레이블 수정](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>라이선싱 요구 사항

밉 민감도 레이블은 Microsoft 365 보안 및 규정 준수 센터에서 만들어지고 관리 됩니다. Azure 부서의 범위에서 사용할 민감도 레이블을 만들려면 활성 Microsoft 365 E5 라이선스가 있어야 합니다.

필요한 라이선스가 아직 없는 경우 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion)의 평가판에 등록할 수 있습니다.

### <a name="extending-sensitivity-labels-to-azure-purview"></a>Azure 부서의 범위에 대 한 민감도 레이블 확장

기본적으로 밉 민감도 레이블은 Microsoft 365의 자산에만 사용할 수 있으며 파일 및 전자 메일에 적용할 수 있습니다.

Azure 부서의 범위의 Azure 자산에 밉 민감도 레이블을 적용 하려면 레이블 확장에 대해 명시적으로 동의 하 고 부서의 범위에서 사용할 수 있는 특정 레이블을 선택 해야 합니다.

이제 조직에서는 Azure 부서의 범위를 사용 하 여 밉 민감도 레이블을 확장 하 여 광범위 한 데이터 원본에서 민감도를 검색, 분류 및 파악 하 고 규정 준수 위험을 최소화할 수 있습니다.

> [!NOTE]
> Microsoft 365 및 Azure 부서의 범위는 별개의 서비스 이므로 다른 지역에 배포 될 가능성이 있습니다. 레이블 이름과 사용자 지정 중요 한 정보 형식 이름은 고객 데이터로 간주 되며, 기본적으로 데이터의 민감도를 보호 하 고 GDPR 법률을 방지 하기 위해 동일한 지리적 위치 내에 유지 됩니다.
>
> 이러한 이유로 레이블 및 사용자 지정 중요 정보 유형은 기본적으로 Azure 부서의 범위에 공유 되지 않으며 Azure 부서의 범위에서 사용 하는 데 동의 해야 합니다.

**민감도 레이블을 부서의 범위로 확장 하려면 다음을 수행 합니다.**

다음 단계는 Azure 부서의 범위에서 민감도 레이블을 사용할 수 있도록 허용 합니다. 여기에서 민감도 레이블을 SQL 열, Azure Blob Storage 파일 등의 자산에 적용할 수 있습니다.

1. Microsoft 365에서 **Information Protection** 페이지로 이동 합니다. 
1. **Azure에서 자산에 대 한 레이블 확장 부서의 범위** 에서 **켜기** 단추를 선택한 다음 표시 되는 확인 대화 상자에서 **예** 를 선택 합니다.

예를 들어:

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="민감도 레이블을 부서의 범위로 확장 하려면 * * 켜기 * *를 선택 합니다." lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Azure 부서의 범위에서 자산에 대 한 레이블 지정을 확장 한 후 부서의 범위에서 사용할 수 있도록 설정할 레이블을 선택할 수 있습니다. 자세한 내용은 [새 민감도 레이블 만들기 또는 기존 레이블 수정](#creating-new-sensitivity-labels-or-modifying-existing-labels)을 참조 하세요.
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>새 민감도 레이블 만들기 또는 기존 레이블 수정

Windows, macOS, iOS 및 Android에서 Office 앱에 대 한 민감도 레이블을 사용 하는 경우 사용자는 4 시간 이내에 새 레이블을 볼 수 있으며, 웹에서 Office의 경우 1 시간 이내입니다. 그러나 모든 앱 및 서비스에 변경 내용을 복제 하는 데 최대 24 시간까지 허용 합니다.

> [!IMPORTANT]
> 사용자에 대 한 영향을 이해 하지 않으면 레이블을 삭제 하지 마세요. 자세한 내용은 Microsoft 365 설명서에서 [레이블 제거 및 삭제](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels) 를 참조 하세요.
>

**새 민감도 레이블을 만들거나 기존 레이블을 수정 하려면**:

1. [Microsoft 365 보안 및 규정 준수 센터](https://protection.office.com/homepage)를 엽니다. 

1. **솔루션** 아래에서 **Information protection** 을 선택 하 고 **레이블 만들기** 를 선택 합니다. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 민감도 레이블 만들기" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. 레이블 이름을로 합니다. 그런 다음 **이 레이블의 범위 정의** 에서 다음을 수행 합니다.

    - 모든 경우에 **Azure 부서의 범위 자산** 을 선택 합니다.
    - 파일에 레이블을 보내려면 **파일 & 전자 메일** 도 선택 합니다. 이 옵션은 데이터베이스 자산에만 레이블을 추가할 필요는 없습니다. 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 레이블 만들기" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. 마법사에서 레이블 설정에 대 한 나머지 지시를 따릅니다. 

    특히 파일 및 데이터베이스 열에 대 한 자동 레이블 지정 규칙을 정의 합니다.

    - [파일에 대 한 자동 레이블 지정 규칙 정의](#define-auto-labeling-rules-for-files)
    - [데이터베이스 열에 대 한 자동 레이블 지정 규칙 정의](#define-auto-labeling-rules-for-database-columns)

    마법사 옵션에 대 한 자세한 내용은 Microsoft 365 설명서에서 [수행할 수 있는 민감도 레이블](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do) 을 참조 하십시오.

1. 위에 나열 된 단계를 반복 하 여 더 많은 레이블을 만듭니다. 

    하위 레이블을을 만들려면 부모 **레이블 >**  >  선택 합니다. **추가 작업**  >  **하위 레이블을 추가** 합니다.

1. 기존 레이블을 수정 하려면 **Information Protection**  >  **레이블로** 이동 하 여 레이블을 선택 합니다. 

    레이블 **편집** 을 선택 하 여 레이블 생성 시 정의한 모든 설정이 포함 된 **민감도 레이블 편집** 마법사를 다시 엽니다.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="기존 민감도 레이블 편집" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. 모든 레이블을 만든 후에는 레이블 순서를 확인 하 고 필요에 따라 순서를 변경 해야 합니다. 

    레이블 순서를 변경 하려면 **...** **> 추가 작업**  >  **이동** 또는 **아래로 이동** 을 선택 합니다. 

    자세한 내용은 Microsoft 365 설명서에서 [레이블 우선 순위 (순서 문제)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters) 를 참조 하세요.


데이터를 [검색 하 여 레이블을 자동으로 적용](#scan-your-data-to-apply-labels-automatically)한 후 다음을 수행 합니다.

- [자산에 대 한 레이블 보기](#view-labels-on-assets)
- [분류 및 민감도 레이블에 대 한 정보 보고서 보기](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>파일에 대 한 자동 레이블 지정 규칙 정의

레이블을 만들거나 편집할 때 마법사의 파일에 대 한 자동 레이블 지정 규칙을 정의 합니다. 

**Office 앱에 대 한 자동** 레이블 지정 페이지에서 **office 앱에 대 한 자동 레이블** 지정을 사용 하도록 설정 하 고 데이터에 레이블을 자동으로 적용 하려는 조건을 정의 합니다.

예를 들어:

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터의 파일에 대 한 자동 레이블 지정 규칙을 정의 합니다." lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
자세한 내용은 Microsoft 365 설명서에서 [데이터에 민감도 레이블 자동 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps) 을 참조 하세요. 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>데이터베이스 열에 대 한 자동 레이블 지정 규칙 정의

레이블을 만들거나 편집할 때 마법사의 데이터베이스 열에 대 한 자동 레이블 지정 규칙을 정의 합니다. 

**Azure 부서의 범위 자산 (미리 보기)** 옵션 아래에서 다음을 수행 합니다.

1. **데이터베이스 열에 대 한 자동 레이블** 지정 슬라이더를 선택 합니다.

1. **중요 한 정보 유형 확인** 을 선택 하 여 레이블에 적용할 중요 한 정보 유형을 선택 합니다.

예를 들어:
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 SQL 열에 대 한 자동 레이블 지정 규칙을 정의 합니다." lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>데이터를 검색 하 여 레이블을 자동으로 적용

사용자가 정의한 자동 레이블 지정 규칙에 따라 Azure 부서의 범위에서 데이터를 검색 하 여 만든 레이블을 자동으로 적용 합니다. 

Azure 부서의 범위의 다양 한 자산에 대 한 검색을 설정 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

|원본  |참조  |
|---------|---------|
|**Azure Blob Storage**     |[Azure Blob Storage 등록 및 검색](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Azure Data Lake Storage Gen1 등록 및 검색](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 등록 및 검색](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Azure SQL Database 등록 및 검사](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance 등록 및 검사](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>자산에 대 한 레이블 보기

Microsoft 365에서 레이블에 대 한 자동 레이블 지정 규칙을 정의 하 고 Azure 부서의 범위에서 데이터를 스캔 한 후에는 레이블이 자산에 자동으로 적용 됩니다. 

**Azure 부서의 범위 카탈로그에서 자산에 적용 되는 레이블을 보려면 다음을 수행 합니다.**

Azure 부서의 범위 카탈로그에서 **레이블** 필터링 옵션을 사용 하 여 특정 레이블만 있는 파일만 표시 합니다. 예를 들어: 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="레이블 별 자산 검색" lightbox="media/create-sensitivity-label/filter-search-results.png":::

예를 들어:

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Azure Blob Storage 파일의 민감도 레이블 보기" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>분류 및 민감도 레이블에 대 한 정보 보고서 보기

**분류** 및 **민감도 레이블** 지정 보고서를 사용 하 여 Azure 부서의 범위에서 분류 되 고 레이블이 지정 된 데이터에 대 한 통찰력을 찾습니다.

> [!div class="nextstepaction"]
> [분류 정보](./classification-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 정보](sensitivity-insights.md)
