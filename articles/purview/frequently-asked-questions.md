---
title: 질문과 대답(FAQ)
description: 이 문서에서는 Azure 부서의 범위에 대 한 자주 묻는 질문에 답변 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 94b765cbcbdd81505b08052845207ee1d93a28d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667805"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Azure 부서의 범위에 대 한 FAQ (질문과 대답)

## <a name="overview"></a>개요

많은 조직에서 데이터를 전체적으로 이해 하지 못하는 경우 존재 하는 데이터, 데이터의 위치 및 관련 데이터를 찾아서 액세스 하는 방법을 이해 하는 것은 어렵습니다. 데이터에는 계보, 분류, 포괄적인 메타 데이터 등의 컨텍스트가 없으므로 비즈니스 사용자가 적절 한 데이터를 검색 하 여 해당 데이터를 적절 하 게 사용 하기 어려워집니다. 따라서 수집 된 데이터 중 일부만 비즈니스 의사 결정을 알리는 데 사용 됩니다. 마지막으로 데이터 보안 문제를 식별 하 고 중요 한 데이터를 보호 하는 것은 일관 되지 않습니다. 특히 데이터 민첩성을 유지 하면서 지속적인 시간과 노력이 필요 합니다.

Azure 부서의 범위는 데이터 거 버 넌 스 솔루션입니다. 이를 통해 고객은 사용에 대 한 제어를 유지 하면서 모든 데이터에 대 한 심층 지식을 얻을 수 있습니다. Azure 부서의 범위를 사용 하 여 조직은 데이터를 검색 하 고 curate. 데이터 공간에 대 한 통찰력을 얻고 중앙에서 데이터에 대 한 액세스를 제어 합니다.

## <a name="purpose-of-this-faq"></a>이 FAQ의 목적

이 FAQ는 고객과 현장 팀에서 자주 묻는 일반적인 질문에 답변 합니다. Azure Data Catalog (ADC) Gen 2 (사용 되지 않음) 및 Azure Information Protection와 같은 Azure 부서의 범위 및 관련 솔루션에 대 한 질문을 명확히 설명 하기 위한 것입니다.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>메타 데이터 검색 및 분류에 사용할 수 있는 원본 유형은 무엇 인가요?

|Azure|비 Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics(SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata (2020의 끝에서 사용 가능)|
|Azure SQL Managed Instance|SAP ECC (2020의 끝에서 사용 가능)|
|Azure 데이터 탐색기|SAP S/4 HANA (2020의 끝에서 사용 가능)|
|Azure Data Lake Storage Gen1|Hive Metastore (2020의 끝에서 사용 가능)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure 파일|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>연결 하 고 계보를 얻을 수 있는 데이터 시스템/프로세서는 무엇 인가요?

|데이터 시스템/프로세서 
|---------
|Azure Data Factory: 복사 작업, 데이터 흐름 작업 
|사용자 지정 계보   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC Gen 2, Azure Information Protection 및 Azure 부서의 범위 관련 된 방법은 무엇 인가요?

Azure 부서의 범위는 원래 ADC Gen 2로 시작 했지만 범위에 넓혔습니다가 있기 때문입니다. 이제 Azure Information Protection의 데이터 분류, 레이블 지정 및 규정 준수 정책 적용 기능과 결합 된 ADC Gen 2의 고급 카탈로그 기능을 채택 합니다. 오늘날 데이터 거 버 넌 스의 광범위 한 업계 정의에 더 가깝게 맞춥니다.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>ADC Gen 1을 사용 하는 고객은 어떻게 되나요?

Azure 부서의 범위는 Microsoft의 카탈로그 솔루션 공간에서 모든 제품 혁신의 중심입니다. ADC Gen 1은 계속 지원 됩니다.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>고객이 동일한 구독에서 여러 Azure 부서의 범위 계정을 가질 수 있나요?

예, 구독 및 테 넌 트 당 여러 Azure 부서의 범위 계정을 지원 합니다.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>ADC Gen 1 및 Azure 부서의 범위를 병렬로 실행할 수 있나요?

예. 둘 다 독립적인 서비스입니다.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>기존 ADC Gen 1 데이터 자산을 Azure 부서의 범위로 마이그레이션할 어떻게 할까요? 있나요?

Azure 부서의 범위 Api를 사용 하 여 ADC Gen 1에서 추출 하 고 Azure 부서의 범위에 수집 합니다. 용어집의 경우 CSV 기반 대량 도구를 지원 합니다.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Azure 부서의 범위를 사용 하 여 SQL 테이블의 중요 한 데이터를 암호화 어떻게 할까요??

데이터 암호화는 데이터 원본 수준에서 수행 됩니다. Azure 부서의 범위는 메타 데이터만 저장 합니다. 데이터를 미리 볼 수 없습니다.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>ADC Gen 2의 모든 기능이 Azure 부서의 범위에 존재 하나요?

예.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>용어집과 분류의 차이점은 무엇 인가요?

용어집에서는 데이터 소비자 라고도 하는 데이터의 기술/비즈니스 사용자가 아닌 명명 규칙을 사용 합니다. 이러한 종류의 사람들은 비즈니스 분석가 또는 Azure 부서의 범위를 사용 하 여 비즈니스 사용을 기반으로 특정 유형의 데이터를 검색 하는 데이터 과학자. 예를 들어 공급망 분석가는 *SKU 유형* 및 *배송 세부 정보* 를 검색 해야 할 수 있습니다. 이러한 용어에 대 한 용어집을 검색 하 여 관련 데이터를 찾을 수 있습니다.
분류는 자산에 존재 하는 데이터를 식별 하는 테이블, 열 또는 파일 수준에서 데이터 자산에 적용 되는 태그입니다. 검색 된 데이터의 유형에 따라 분류를 자동 또는 수동으로 적용할 수 있습니다. 일반적으로 분류 태그를 사용 하 여 자산에 중요 한 데이터가 포함 되어 있는지 여부와 중요 한 데이터 형식을 확인할 수 있습니다.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure 부서의 범위는 내 Sharepoint 및 OneDrive에서 전자 메일, Pdf 등을 검색 하 고 분류 하나요?

온-프레미스 SharePoint 사이트 및 라이브러리에 대 한 검색은 Azure Information Protection 스캐너를 통해 제공 됩니다. 다음 Sku를 사용 하는 고객의 Microsoft 365 구독을 통해 스캐너를 사용할 수 있습니다. AIP P1, EMS E3 및 M365 E3. 이러한 Sku 중 하나를 사용 하는 경우 Azure Information Protection 스캐너 사용을 시작 하려면 올바른 권한이 있어야 합니다.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>검색에 사용 되는 계산은 무엇 인가요?
Microsoft에서 관리 하는 검색 인프라가 있습니다. 지원 되는 대부분의 Azure/AWS 리소스의 경우 검색 인프라를 배포할 필요가 없습니다.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>ARM (Azure Resource Manager) 템플릿/m s/PowerShell을 통해 Azure 부서의 범위를 프로 비전 하는 방법이 있나요?

예, ARM 템플릿을 사용할 수 있습니다.

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>이미 Atlas를 사용 하 고 있습니다. Azure 부서의 범위로 쉽게 이동할 수 있나요?

Azure 부서의 범위는 Atlas API와 호환 됩니다. Atlas에서 마이그레이션하는 경우 먼저 Azure 부서의 범위를 사용 하 여 데이터 원본을 검색 하는 것이 좋습니다. 계정에서 자산을 사용할 수 있게 되 면 비슷한 Atlas Api를 사용 하 여 자산 업데이트 또는 사용자 지정 계보 추가와 같이 통합할 수 있습니다. Azure 부서의 범위는 Azure Search 사용 하도록 검색 API를 수정 하므로 고급 검색을 사용할 수 있습니다.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>내 테 넌 트에서 여러 카탈로그를 만들 수 있나요?

예, 구독 및 테 넌 트 당 여러 Azure 부서의 범위 계정을 만들 수 있습니다. 제한 페이지에서 [Azure 부서의 범위를 사용 하 여 리소스에 대 한 할당량 관리 및 증가](how-to-manage-quotas.md)를 검토할 수 있습니다.

여러 계정을 만들어야 하는 경우에 대 한 추가 권장 사항은 [Azure 부서의 범위 deployment 모범 사례](deployment-best-practices.md)에 설명 되어 있습니다.

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>단일 Azure 부서의 범위 계정 내에서 여러 테 넌 트를 등록할 수 있나요?

아니요. 현재 다른 테 넌 트의 데이터 원본을 검색 하려면 해당 테 넌 트에 별도의 Azure 부서의 범위 계정을 만들어야 합니다.

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure 부서의 범위는 열 수준 계보를 지원 하나요?

예, Azure 부서의 범위는 열 수준 계보를 지원 합니다.