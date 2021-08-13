---
title: 데이터에 자동으로 민감도 레이블 적용
description: 민감도 레이블을 만들고 검사하는 동안 자동으로 데이터에 적용하는 방법을 알아봅니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: 5f6e2474a533f5619d5544b674a87b6412323cc3
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2021
ms.locfileid: "106166737"
---
# <a name="automatically-label-your-data-in-azure-purview"></a>Azure Purview에서 자동으로 데이터 레이블 지정

이 문서에서는 MIP(Microsoft Information Protection) 민감도 레이블을 만들고 Azure Purview의 Azure 자산에 자동으로 적용하는 방법을 설명합니다.

## <a name="what-are-sensitivity-labels"></a>민감도 레이블이란? 

작업을 완료하기 위해 조직의 사람들은 조직 내부 및 외부의 다른 사람들과 공동 작업 수행합니다. 데이터가 항상 클라우드에 유지되는 것은 아니며 디바이스, 앱 및 서비스 간에 모든 곳에서 로밍되는 경우가 많습니다. 

데이터가 로밍되면 조직의 비즈니스 및 규정 준수 정책을 충족하는 안전하고 보호된 방식으로 데이터를 로밍하려고 합니다.

민감도 레이블을 적용하면 조직에서 중요한 특정 데이터의 상태를 확인할 수 있습니다. 예를 들어 조직 내에서 특정 프로젝트 이름에 대한 기밀을 유지하는 반면 동일한 용어는 다른 조직에 대한 기밀은 아닙니다. 

### <a name="sensitivity-labels-in-azure-purview"></a>Azure Purview의 민감도 레이블

Purview에서 분류는 주제 태그와 비슷하며 검사하는 동안 데이터 자산 내에 있는 특정 유형의 데이터를 표시하고 식별하는 데 사용됩니다.

Purview는 Microsoft 365와 같이 중요한 정보 형식이라고도 하는 동일한 분류를 사용합니다.  MIP 민감도 레이블은 Microsoft 365 SCC(보안 및 규정 준수 센터)에서 생성됩니다. 이렇게 하면 Azure Purview 자산에서 기존 민감도 레이블을 확장할 수 있습니다.

**분류** 는 **사회 보장 번호** 의 분류가 있는 사회 보장 번호와 같이 직접 일치합니다. 

이와 달리 **민감도 레이블** 은 하나 이상의 분류 및 조건을 함께 발견할 경우 적용됩니다. 이 컨텍스트에서 [조건](/microsoft-365/compliance/apply-sensitivity-label-automatically)은 *다른 분류에 근접* 및 *% 신뢰도* 와 같이 비정형 데이터에 대해 정의할 수 있는 모든 매개 변수를 나타냅니다. 

Azure Purview의 민감도 레이블을 사용하여 파일 및 데이터베이스 열에 레이블을 자동으로 적용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

- Microsoft 365 설명서의 [민감도 레이블 알아보기](/microsoft-365/compliance/sensitivity-labels)를 참조하세요.
- [자동 레이블 지정 규칙이란?](#what-are-auto-labeling-rules)
- [Azure Purview에서 민감도 레이블에 대해 지원되는 데이터 형식](#supported-data-types-for-sensitivity-labels-in-azure-purview)
- [SQL 데이터베이스 열에 대한 레이블 지정](#labeling-for-sql-database-columns)

#### <a name="what-are-auto-labeling-rules"></a>자동 레이블 지정 규칙이란?

데이터는 지속적으로 증가하고 변경됩니다. 현재 레이블이 지정되지 않은 데이터를 추적하고 레이블을 수동으로 적용하는 조치를 취하는 것은 번거로울 뿐만 아니라 불필요한 골칫거리이기도 합니다. 

자동 레이블 지정 규칙은 특정 레이블을 적용해야 하는 시기를 지정하는 조건입니다. 이러한 조건이 충족되면 레이블이 데이터에 자동으로 할당되어 대규모 데이터에 일관된 민감도 레이블을 유지합니다.

레이블을 만들 때 [파일](#define-auto-labeling-rules-for-files) 및 [데이터베이스 열](#define-auto-labeling-rules-for-database-columns) 모두에 대해 자동 레이블 지정 규칙을 정의하여 각 데이터 검색에 레이블을 자동으로 적용해야 합니다. 

Purview에서 데이터를 검색하면 Purview 카탈로그 및 Insight 보고서에 자동으로 적용된 레이블을 볼 수 있습니다.
#### <a name="supported-data-types-for-sensitivity-labels-in-azure-purview"></a>Azure Purview에서 민감도 레이블에 대해 지원되는 데이터 형식

민감도 레이블은 다음 데이터 형식에 대해 Azure Purview에서 지원됩니다.

|데이터 형식  |원본  |
|---------|---------|
|파일에 대한 자동 레이블 지정     |      - Azure Blob Storage  </br>Azure Data Lake Storage Gen 1 및 Gen 2  |
|데이터베이스 열에 대한 자동 레이블 지정     |  - SQL Server </br>- Azure SQL Database </br>- Azure SQL Database Managed Instance   <br> - Azure Synapse  <br> - Azure Cosmos DB <br><br>자세한 내용은 아래의 [SQL Database 열에 대한 레이블 지정](#labeling-for-sql-database-columns)을 참조하세요.  |
| | |

#### <a name="labeling-for-sql-database-columns"></a>SQL 데이터베이스 열에 대한 레이블 지정

Microsoft는 데이터베이스 열에 대한 Purview 레이블 지정 외에도 [SSMS(SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)에서 SQL 데이터 분류를 사용하여 SQL Database 열에 대한 레이블 지정을 지원합니다. Purview는 글로벌 [MIP 민감도 레이블](/microsoft-365/compliance/sensitivity-labels)을 사용하지만 SSMS는 로컬로 정의된 레이블만 사용합니다.

Purview의 레이블 지정과 SSMS의 레이블 지정은 현재 서로 상호 작용하지 않는 별도의 프로세스입니다. 따라서 SSMS에 적용된 레이블은 Purview에 표시되지 않으며 그 반대의 경우도 마찬가지입니다. 여러 플랫폼에 적용할 수 있는 글로벌 MIP 레이블을 사용하므로 SQL Database에 레이블을 지정하는 데 Azure Purview를 사용하는 것이 좋습니다.

자세한 내용은 [SQL 데이터 검색 및 분류 설명서](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하세요.

## <a name="how-to-create-sensitivity-labels-in-microsoft-365"></a>Microsoft 365에서 민감도 레이블을 만드는 방법

민감도 레이블이 아직 없는 경우 해당 레이블을 만들어 Azure Purview에서 사용할 수 있도록 해야 합니다. 기존 민감도 레이블을 수정하여 Azure Purview에 사용하도록 할 수도 있습니다.

자세한 내용은 다음을 참조하세요.

- [라이선싱 요구 사항](#licensing-requirements)
- [민감도 레이블을 Azure Purview로 확장](#extending-sensitivity-labels-to-azure-purview)
- [새 민감도 레이블 만들기 또는 기존 레이블 수정](#creating-new-sensitivity-labels-or-modifying-existing-labels)
### <a name="licensing-requirements"></a>라이선싱 요구 사항

MIP 민감도 레이블은 Microsoft 365 보안 및 규정 준수 센터에서 생성됩니다. Azure Purview에서 사용할 민감도 레이블을 만들려면 활성 Microsoft 365 E5 라이선스가 있어야 합니다.

필요한 라이선스가 아직 없는 경우 [Microsoft 365 E5](https://www.microsoft.com/microsoft-365/business/compliance-solutions#midpagectaregion) 평가판에 등록하면 됩니다.

### <a name="extending-sensitivity-labels-to-azure-purview"></a>민감도 레이블을 Azure Purview로 확장

기본적으로 MIP 민감도 레이블은 파일 및 메일에 적용할 수 있는 Microsoft 365 자산에만 사용할 수 있습니다.

Azure Purview의 Azure 자산에 MIP 민감도 레이블을 적용하려면 레이블 확장에 명시적으로 동의하고 Purview에서 사용할 수 있는 특정 레이블을 선택해야 합니다.

이제 조직은 Azure Purview를 통해 MIP의 민감도 레이블을 확장하여 광범위한 데이터 원본에서 민감도를 검색, 분류 및 파악하여 규정 준수 위험을 최소화할 수 있습니다.

> [!NOTE]
> Microsoft 365 및 Azure Purview는 별도의 서비스이므로 다른 지역에 배포될 가능성이 있습니다. 레이블 이름 및 사용자 지정 중요한 정보 유형 이름은 고객 데이터로 간주되며, 데이터의 민감도를 보호하고 개인 정보 규정을 준수하기 위해 기본적으로 동일한 지리적 위치 내에 유지됩니다.
>
> 이러한 이유로 레이블 및 사용자 지정 중요한 정보 유형은 기본적으로 Azure Purview에 공유되지 않으며, 사용자는 Azure Purview에서 이를 사용하는 데 동의해야 합니다.

**민감도 레이블을 Purview로 확장하려면 다음을 수행합니다.**

다음 단계를 통해 Azure Purview에서 민감도 레이블을 사용할 수 있습니다. 여기서는 SQL 열, Azure Blob Storage 파일 등의 자산에 민감도 레이블을 적용할 수 있습니다.

1. Microsoft 365에서 **Information Protection** 페이지로 이동합니다. 
1. **Azure Purview의 자산으로 레이블 지정 확장** 에서 **켜기** 단추를 선택한 다음, 표시되는 확인 대화 상자에서 **예** 를 선택합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/create-sensitivity-label/extend-sensitivity-labels-to-purview-small.png" alt-text="**켜기**를 선택하여 민감도 레이블을 Purview로 확장" lightbox="media/create-sensitivity-label/extend-sensitivity-labels-to-purview.png":::
 
Azure Purview의 자산으로 레이블 지정을 확장한 후에는 Purview에서 사용하도록 할 레이블을 선택할 수 있습니다. 자세한 내용은 [새 민감도 레이블 만들기 또는 기존 레이블 수정](#creating-new-sensitivity-labels-or-modifying-existing-labels)을 참조하세요.
### <a name="creating-new-sensitivity-labels-or-modifying-existing-labels"></a>새 민감도 레이블 만들기 또는 기존 레이블 수정

Windows, macOS, iOS 및 Android의 Office 앱에 민감도 레이블을 사용하는 경우 사용자는 4시간 이내에 새 레이블을, 웹용 Office에서는 1시간 이내에 새 레이블을 볼 수 있습니다. 그러나 변경 내용이 모든 앱 및 서비스에 복제되는 데 최대 24시간이 소요될 수 있습니다.

> [!IMPORTANT]
> 사용자에게 미치는 영향을 파악하지 못한 경우에는 레이블을 삭제하지 마세요. 자세한 내용은 Microsoft 365 설명서의 [레이블 제거 및 삭제](/microsoft-365/compliance/create-sensitivity-labels#removing-and-deleting-labels)를 참조하세요.
>

**새 민감도 레이블을 만들거나 기존 레이블을 수정하려면 다음을 수행합니다.**

1. [Microsoft 365 보안 및 규정 준수 센터](https://protection.office.com/homepage)를 엽니다. 

1. **솔루션** 에서 **Information Protection** 을 선택한 다음, **레이블 만들기** 를 선택합니다. 

    :::image type="content" source="media/create-sensitivity-label/create-sensitivity-label-full-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 민감도 레이블 만들기" lightbox="media/create-sensitivity-label/create-sensitivity-label-full.png":::

1. 레이블 이름을 지정합니다. 그런 다음, **이 레이블의 범위 정의** 에서 다음을 수행합니다.

    - 모든 경우에 **Azure Purview 자산** 을 선택합니다.
    - 파일에 레이블을 지정하려면 **파일 및 메일** 도 선택합니다. 이 옵션은 데이터베이스 자산에만 레이블을 지정하는 경우에는 필요하지 않습니다. 
    
    :::image type="content" source="media/create-sensitivity-label/create-label-scope-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 레이블 만들기" lightbox="media/create-sensitivity-label/create-label-scope.png":::

1. 레이블 설정에 대한 마법사의 나머지 지시를 따릅니다. 

    특히 파일 및 데이터베이스 열에 대한 자동 레이블 지정 규칙을 정의합니다.

    - [파일에 대한 자동 레이블 지정 규칙 정의](#define-auto-labeling-rules-for-files)
    - [데이터베이스 열에 대한 자동 레이블 지정 규칙 정의](#define-auto-labeling-rules-for-database-columns)

    마법사 옵션에 대한 자세한 내용은 Microsoft 365 설명서의 [민감도 레이블이 수행하는 작업](/microsoft-365/compliance/sensitivity-labels#what-sensitivity-labels-can-do)을 참조하세요.

1. 더 많은 레이블을 만들려면 위에 나온 단계를 반복합니다. 

    하위 레이블을 만들려면 상위 레이블 > **...**  > **추가 작업** > **하위 레이블 추가** 을 선택합니다.

1. 기존 레이블을 수정하려면 **Information Protection** > , **레이블** 을 찾아서 레이블을 선택합니다. 

    그런 다음, **레이블 편집** 을 선택하고 레이블을 만들 때 정의한 모든 설정을 사용하여 **민감도 레이블 편집** 마법사를 다시 엽니다.

    :::image type="content" source="media/create-sensitivity-label/edit-sensitivity-label-full-small.png" alt-text="기존 민감도 레이블 편집" lightbox="media/create-sensitivity-label/edit-sensitivity-label-full.png":::

1. 모든 레이블을 만든 후에는 레이블 순서를 확인하고 필요에 따라 순서를 다시 지정해야 합니다. 

    레이블의 순서를 변경하려면 **...** **> 추가 작업** > **위로 이동** 또는 **아래로 이동을 선택합니다.** 

    자세한 내용은 Microsoft 365 설명서에서 [레이블 우선 순위(순서 문제)](/microsoft-365/compliance/sensitivity-labels#label-priority-order-matters)를 참조하세요.


계속해서 [데이터를 검색하여 레이블을 자동으로 적용](#scan-your-data-to-apply-labels-automatically)한 후 다음을 수행합니다.

- [자산에 대한 레이블 보기](#view-labels-on-assets)
- [분류 및 민감도 레이블에 대한 인사이트 보고서 보기](#view-insight-reports-for-the-classifications-and-sensitivity-labels)

#### <a name="define-auto-labeling-rules-for-files"></a>파일에 대한 자동 레이블 지정 규칙 정의

레이블을 만들거나 편집할 때 마법사에서 파일에 대한 자동 레이블 지정 규칙을 정의합니다. 

**Office 앱에 대한 자동 레이블 지정** 페이지에서 **Office 앱에 대한 자동 레이블 지정** 을 사용하도록 설정한 다음, 레이블을 데이터에 자동으로 적용할 조건을 정의합니다.

예를 들면 다음과 같습니다.

:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-files-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 파일에 대한 자동 레이블 지정 규칙 정의" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-files.png":::
 
자세한 내용은 Microsoft 365 설명서에서 [데이터에 자동으로 민감도 레이블 적용](/microsoft-365/compliance/apply-sensitivity-label-automatically#how-to-configure-auto-labeling-for-office-apps)을 참조하세요. 

#### <a name="define-auto-labeling-rules-for-database-columns"></a>데이터베이스 열에 대한 자동 레이블 지정 규칙 정의

레이블을 만들거나 편집할 때 마법사에서 데이터베이스 열에 대한 자동 레이블 지정 규칙을 정의합니다. 

**Azure Purview 자산(미리 보기)** 옵션에서 다음을 수행합니다.

1. **데이터베이스 열에 대 한 자동 레이블 지정** 슬라이더를 선택합니다.

1. **중요한 정보 유형 확인** 을 선택하여 레이블에 적용할 중요한 정보 유형을 선택합니다.

예를 들면 다음과 같습니다.
        
:::image type="content" source="media/create-sensitivity-label/create-auto-labeling-rules-db-columns-small.png" alt-text="Microsoft 365 보안 및 규정 준수 센터에서 SQL 열에 대한 자동 레이블 지정 규칙 정의" lightbox="media/create-sensitivity-label/create-auto-labeling-rules-db-columns.png":::

## <a name="scan-your-data-to-apply-labels-automatically"></a>데이터를 검색하여 자동으로 레이블 적용

Azure Purview에서 데이터를 검색하여 정의한 자동 레이블 지정 규칙에 따라 생성한 레이블을 자동으로 적용합니다. 

Azure Purview에서 다양한 자산 검색을 설정하는 방법에 대한 자세한 내용은 다음을 참조하세요.

|원본  |참조  |
|---------|---------|
|**Azure Blob Storage**     |[Azure Blob Storage 등록 및 검색](register-scan-azure-blob-storage-source.md)         |
|**Azure Data Lake Storage**     |[Azure Data Lake Storage Gen1 등록 및 검색](register-scan-adls-gen1.md) </br>[Azure Data Lake Storage Gen2 등록 및 검색](register-scan-adls-gen2.md)         |
|**Azure SQL Databases**|[Azure SQL Database 등록 및 검사](register-scan-azure-sql-database.md) </br>[Azure SQL Database Managed Instance 등록 및 검사](register-scan-azure-sql-database-managed-instance.md)|
| | |

## <a name="view-labels-on-assets"></a>자산에 대한 레이블 보기

Microsoft 365에서 레이블에 대한 자동 레이블 지정 규칙을 정의하고 Azure Purview에서 데이터를 검색하면 레이블이 자산에 자동으로 적용됩니다. 

**Azure Purview 카탈로그에서 자산에 적용된 레이블을 보려면 다음을 수행합니다.**

Azure Purview 카탈로그에서 **레이블** 필터링 옵션을 사용하여 특정 레이블만 있는 파일을 표시합니다. 예를 들면 다음과 같습니다. 

:::image type="content" source="media/create-sensitivity-label/filter-search-results-small.png" alt-text="레이블별 자산 검색" lightbox="media/create-sensitivity-label/filter-search-results.png":::

예를 들면 다음과 같습니다.

:::image type="content" source="media/create-sensitivity-label/view-labeled-files-blob-storage-small.png" alt-text="Azure Blob Storage에 있는 파일의 민감도 레이블 보기" lightbox="media/create-sensitivity-label/view-labeled-files-blob-storage.png":::

## <a name="view-insight-reports-for-the-classifications-and-sensitivity-labels"></a>분류 및 민감도 레이블에 대한 인사이트 보고서 보기

**분류** 및 **민감도 레이블 지정** 보고서를 사용하여 Azure Purview에서 분류되고 레이블이 지정되는 데이터에 대한 인사이트를 찾습니다.

> [!div class="nextstepaction"]
> [분류 인사이트](./classification-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 인사이트](sensitivity-insights.md)
