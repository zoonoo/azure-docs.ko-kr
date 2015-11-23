<properties 
	pageTitle="데이터 이동 활동" 
	description="데이터를 이동하기 위해 데이터 팩터리 파이프라인에서 사용할 수 있는 데이터 팩터리 엔터티를 알아봅니다." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# 데이터 이동 활동
데이터 팩터리는 [전역적으로 사용할 수 있는 서비스](#global)을 사용하여 아래에 나열된 다양한 데이터 저장소에 걸쳐 [복사 작업](#copyactivity)으로 데이터 이동을 지원합니다. 데이터 팩터리는 데이터 관리 게이트웨이를 사용하는 [온-프레미스 위치와 클라우드 간의 데이터를 안전하게 이동](#moveonpremtocloud)하도록 기본적으로 지원합니다.

복사 작업 사용에 대한 빠른 자습서는 [자습서: Azure Data Factory 파이프라인에서 복사 작업 사용](data-factory-get-started.md)을 참조하세요. 자습서에서는 복사 작업을 사용하여 Azure Blob 저장소의 데이터를 Azure SQL 데이터베이스에 복사합니다. 다음 섹션에는 복사 작업에서 지원하는 모든 원본 및 싱크가 나열되어 있습니다.


## 복사 작업을 지원하는 데이터 저장소
복사 작업은 **원본** 데이터 저장소의 데이터를 **싱크** 데이터 저장소로 복사합니다. 데이터 팩터리는 다음의 데이터 저장소 및 원본, 싱크 조합을 지원합니다. 해당 저장소에서 데이터를 복사하는 방법에 알아보려면 데이터 저장소를 클릭합니다.

| **원본** | **싱크** |
| ------ | ---- |
| [Azure Blob](data-factory-azure-blob-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스에, 온-프레미스 SQL Server, laaS의 SQL Server, Azure DocumentDB, 온-프레미스 파일 시스템, Azure 데이터 레이크 스토어 |
| [Azure 테이블](data-factory-azure-table-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure DocumentDB, Azure 데이터 레이크 저장소 |
| [Azure SQL 데이터베이스](data-factory-azure-sql-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure DocumentDB, Azure 데이터 레이크 저장소 |
| [Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure DocumentDB, Azure 데이터 레이크 저장소 |
| [Azure DocumentDB](data-factory-azure-documentdb-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, Azure 데이터 레이크 저장소 |
| [Azure 데이터 레이크 저장소](data-factory-azure-datalake-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스에, 온-프레미스 SQL Server, laaS의 SQL Server, Azure DocumentDB, 온-프레미스 파일 시스템, Azure 데이터 레이크 스토어 | 
| [IaaS의 SQL Server](data-factory-sqlserver-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 파일 시스템](data-factory-onprem-file-system-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스에, 온-프레미스 SQL Server, laaS의 SQL Server, 온-프레미스 파일 시스템, Azure 데이터 레이크 스토어 |
| [온-프레미스 SQL Server](data-factory-sqlserver-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 Oracle 데이터베이스](data-factory-onprem-oracle-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 MySQL 데이터베이스](data-factory-onprem-mysql-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 DB2 데이터베이스](data-factory-onprem-db2-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 Teradata 데이터베이스](data-factory-onprem-teradata-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 Sybase 데이터베이스](data-factory-onprem-sybase-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |
| [온-프레미스 PostgreSQL 데이터베이스](data-factory-onprem-postgresql-connector.md) | Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, 온-프레미스 SQL Server, IaaS의 SQL Server, Azure 데이터 레이크 저장소 |

## <a name="copyactivity"></a>복사 작업
복사 작업은 하나의 입력 데이터 집합(**원본**)을 가지고 한 출력 데이터 집합 (**싱크**)에 활동 구성 당 데이터를 복사합니다. 데이터 복사는 작업에 지정된 일정에 따라 일괄 처리 방식으로 수행됩니다.

> [AZURE.NOTE]모든 활동에서 사용할 수 있는 다양한 JSON 섹션 및 속성과 같은 높은 수준의 일반적 활동을 정의에 대해 알아보려면 [파이프라인 및 활동 이해](data-factory-create-pipelines.md) 문서를 참조하세요.

복사 작업은 다음 기능을 제공합니다.

### <a name="global"></a>전역적으로 사용 가능한 데이터 이동
자체 Azure 데이터 팩터리가 미국 서부 지역에서만에서 사용할 수 있지만, 다음과 같은 영역 및 지역에서는 복사 활동을 지원하는 데이터 이동 서비스를 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지는 대부분의 경우에서 영역 간 홉을 방지하는 효율적인 데이터 이동을 보장합니다.


클라우드 소스에서 클라우드 대상으로 복사하는 경우(예: Azure Blob -> Azure SQL), 데이터 이동 서비스는 전송하기 위해 싱크 위치에 가장 가까운 배포를 선택합니다. 데이터를 온-프레미스 데이터 원본에서 클라우드에 또는 그 반대로 복사하는 경우(예: 온-프레미스 SQL Server -> Azure Blob), 데이터 이동은 데이터 이동 서비스를 포함하지 않은 데이터 관리 게이트웨이에 의해 실제로 수행됩니다.

| 지역 | Geography |
| ------ | --------- | 
| 미국 중부 | US |
| 미국 동부 | US |
| 미국 동부2 | US |
| 미국 중북부 | US |
| 미국 중남부 | US |
| 미국 서부 | US |
| 브라질 남부 | LATAM |
| 북유럽 | EMEA |
| 서유럽 | EMEA |
| 동남아시아 | APAC |
| 일본 동부 | APAC |



### <a name="moveonpremtocloud"></a>온-프레미스 위치와 클라우드 간에 데이터를 안전하게 이동
최신 데이터 통합에 대한 도전 과제 중 하나는 온-프레미스와 클라우드에서 원활한 데이터 이동입니다. 데이터 관리 게이트웨이는 하이브리드 데이터 파이프라인을 사용하도록 온-프레미스를 설치할 수 있는 에이전트입니다.

데이터 게이트웨이는 다음 기능을 제공합니다.

1.	온-프레미스 데이터 저장소에 액세스를 안전하게 관리합니다.
2.	데이터 팩터리 내 온-프레미스 데이터 저장소 및 클라우드 데이터 저장소를 모델로 데이터를 이동합니다.
3.	대시보드에 기반하는 데이터 팩터리 클라우드를 사용하여 게이트웨이 상태에 대한 가시성을 사용하는 모니터링 및 관리를 위한 유리의 단일 창이 있습니다.


자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

### 안정적이고 비용 효율적인 데이터 이동
복사 작업은 안전한 방법으로 많은 양의 데이터를 이동하도록 설계되었으며 많고 다양한 데이터 원본에 걸친 일시적인 오류를 방어합니다. 옵션을 사용하여 비용 효율적인 방식으로 데이터를 복사하여 네트워크를 통해 압축을 사용하도록 설정합니다.

### 다양한 형식 시스템에서 형식 변환
다른 데이터 저장소에는 서로 다른 네이티브 형식 시스템이 있습니다. 복사 작업은 다음 2단계 접근 방법으로 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

각 데이터 저장소 커넥터 문서에서 데이터 저장소용 .NET에 지정된 네이티브 형식 시스템에 대한 매핑을 찾을 수 있습니다. 테이블을 만드는 동안 적절한 형식을 결정하는 이러한 매핑을 사용할 수 있으므로 복사 작업 시 오른쪽 변환이 수행 됩니다.

### 다른 파일 형식으로 작업
파일 기반 원본 복사 작업이 이진, 텍스트 및 Avro 형식을 포함하는 다양한 파일 형식을 지원합니다. 복사 작업을 사용하여 한 형식에서 다른 형식으로 데이터를 변환할 수 있습니다. 예: 텍스트(CSV)에서 Avro로 변환

### 복사 작업 속성
이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다. 반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다.

복사 작업의 경우 **typeProperties** 섹션은 원본 및 싱크의 형식에 따라 달라집니다. 각 페이지에 특정 데이터 저장소의 위에 나열 된 문서 이러한 속성을 데이터 저장소 형식에 따라 다릅니다.

<!---HONumber=Nov15_HO3-->