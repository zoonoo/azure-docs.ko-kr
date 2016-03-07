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
	ms.date="02/03/2016" 
	ms.author="spelluru"/>

# 데이터 이동 활동
[복사 작업](#copyactivity)이 Azure Data Factory의 데이터 이동을 수행합니다. 이 작업은 다양한 데이터 저장소 간에 데이터를 안전하고, 안정적이고, 확장성 있는 방법으로 복사할 수 있는 [전역적으로 사용 가능한 서비스](#global)를 통해 이루어집니다. 이 서비스는 데이터 이동을 수행할 최적의 영역을 자동으로 선택합니다. 싱크 데이터 저장소에 가장 가까운 영역이 사용됩니다.
 
이 데이터 이동이 발생하는 다양한 시나리오를 살펴보겠습니다.

## 두 클라우드 데이터 저장소 간의 데이터 복사
원본 및 싱크(대상) 데이터 저장소가 모두 클라우드에 있는 경우 복사 작업은 다음 단계를 통해 원본에서 싱크로 데이터를 복사/이동합니다. 복사 작업을 구동하는 서비스는 다음을 수행합니다.

1. 원본 데이터 저장소에서 데이터 읽기
2.	입력 데이터 집합, 출력 데이터 집합 및 복사 작업의 구성을 기준으로 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환 수행 
3.	대상 데이터 저장소에 데이터 기록

![클라우드-클라우드 복사](.\media\data-factory-data-movement-activities\cloud-to-cloud.png)


## 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간 데이터 복사
[회사 방화벽 뒤의 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 안전하게 데이터를 이동하려면](#moveonpremtocloud) 하이브리드 데이터 이동 및 처리를 돕는 에이전트인 데이터 관리 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다. 데이터 관리 게이트웨이는 데이터 저장소와 같은 컴퓨터에 설치하거나 데이터 저장소에 액세스할 수 있는 별도 컴퓨터에 설치할 수 있습니다. 이 시나리오에서는 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환이 데이터 관리 게이트웨이에 의해 수행됩니다. Azure 데이터 팩터리 서비스를 통해 이동하지 않는 서비스가 이러한 경우입니다. 데이터 관리 게이트웨이는 데이터를 대상 저장소에 직접 씁니다.

![온-프레미스-클라우드 복사](.\media\data-factory-data-movement-activities\onprem-to-cloud.png)

## Azure Iaas VM의 데이터 저장소에서/로 데이터 복사 
데이터 관리 게이트웨이를 사용하여 Azure IaaS VM(Infrastructure-as-a-Service 가상 컴퓨터)에서 호스트되는 지원되는 데이터 저장소에서/로 데이터를 이동할 수도 있습니다. 이 경우 데이터 관리 게이트웨이는 데이터 저장소와 같은 Azure VM에 설치하거나 데이터 저장소에 액세스할 수 있는 별도 VM에 설치할 수 있습니다.

## 지원되는 데이터 저장소
복사 작업은 **원본** 데이터 저장소의 데이터를 **싱크** 데이터 저장소로 복사합니다. 데이터 팩터리는 다음과 같은 데이터 저장소를 지원하고 **모든 원본의 데이터를 모든 싱크에 쓸 수** 있습니다. 해당 저장소에서 데이터를 복사하는 방법에 알아보려면 데이터 저장소를 클릭합니다.

| 원본| 싱크 |
|:------- | :---- |
| <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 테이블](data-factory-azure-table-connector.md)</li><li>[Azure SQL 데이터베이스](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB(아래 참고 참조)](data-factory-azure-documentdb-connector.md)</li><li>[Azure 데이터 레이크 저장소](data-factory-azure-datalake-connector.md)</li><li>[SQL Server 온-프레미스/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[파일 시스템 온-프레미스/Azure IaaS](data-factory-onprem-file-system-connector.md)</li><li>[Oracle 데이터베이스 온-프레미스/Azure IaaS](data-factory-onprem-oracle-connector.md)</li><li>[MySQL 데이터베이스 온-프레미스/Azure IaaS ](data-factory-onprem-mysql-connector.md)</li><li>[DB2 데이터베이스 온-프레미스/Azure IaaS](data-factory-onprem-db2-connector.md)</li><li>[Teradata 데이터베이스 온-프레미스/Azure IaaS ](data-factory-onprem-teradata-connector.md)</li><li>[Sybase 데이터베이스 온-프레미스/Azure IaaS](data-factory-onprem-sybase-connector.md)</li><li>[PostgreSQL 데이터베이스 온-프레미스/Azure IaaS](data-factory-onprem-postgresql-connector.md)</li><li>[ODBC 데이터 원본 온-프레미스/Azure IaaS](data-factory-odbc-connector.md)</li><li>[HDFS(Hadoop 분산 파일 시스템) 온-프레미스/Azure IaaS](data-factory-hdfs-connector.md)</li><li>[OData 소스](data-factory-odata-connector.md)</li><li>[웹 테이블](data-factory-web-table-connector.md)</li></ul> | <ul><li>[Azure Blob](data-factory-azure-blob-connector.md)</li><li>[Azure 테이블](data-factory-azure-table-connector.md)</li><li>[Azure SQL 데이터베이스](data-factory-azure-sql-connector.md)</li><li>[Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md)</li><li>[Azure DocumentDB(아래 참고 참조)](data-factory-azure-documentdb-connector.md)</li><li>[Azure 데이터 레이크 저장소](data-factory-azure-datalake-connector.md)</li><li>[SQL Server 온-프레미스/Azure IaaS](data-factory-sqlserver-connector.md)</li><li>[파일 시스템 온-프레미스/Azure IaaS](data-factory-onprem-file-system-connector.md)</li></ul> |


> [AZURE.NOTE] Azure Blob, Azure 테이블, Azure SQL 데이터베이스, Azure SQL 데이터 웨어하우스, Azure DocumentDB 및 Azure 데이터 레이크 저장소 등 다른 Azure 서비스에서/Azure 서비스로 Azure DocumentDB로/Azure DocumentDB에서만 이동할 수 있습니다. 또한 Azure Document DB에 대한 전체 매트릭스도 곧 지원됩니다.

## 자습서
복사 작업 사용에 대한 빠른 자습서는 [자습서: Azure Data Factory 파이프라인에서 복사 작업 사용](data-factory-get-started.md)을 참조하세요. 자습서에서는 복사 작업을 사용하여 Azure Blob 저장소의 데이터를 Azure SQL 데이터베이스에 복사합니다.

## <a name="copyactivity"></a>복사 작업
복사 작업에는 하나의 입력 데이터 집합(**원본**)과 하나의 출력 데이터 집합(**싱크**)이 사용됩니다. 데이터 복사는 작업에 지정된 일정에 따라 일괄 처리 방식으로 수행됩니다. 일반적인 작업 정의에 대해 알려면 [파이프라인 및 작업 이해](data-factory-create-pipelines.md) 문서를 참조하세요.

복사 작업은 다음 기능을 제공합니다.

### <a name="global"></a>전역적으로 사용 가능한 데이터 이동
자체 Azure Data Factory 자체는 미국 서부 및 북유럽 지역에서만 사용할 수 있지만, 다음과 같은 지역 및 지리에서는 복사 작업을 지원하는 서비스를 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지는 대부분의 경우에서 영역 간 홉을 방지하는 효율적인 데이터 이동을 보장합니다.

**데이터 관리 게이트웨이** 또는 **Azure 데이터 팩터리**는 복사 작업에서 원본 및 대상 데이터의 위치를 기반으로 데이터 이동을 수행합니다. 자세한 내용은 다음 테이블을 참조하세요.

원본 데이터 저장소 위치 | 대상 데이터 저장소 위치 | 데이터 이동은  
-------------------------- | ------------------------------- | ----------------------------- 
온-프레미스/Azure VM(IaaS) | 클라우드 | **데이터 관리 게이트웨이** 온-프레미스 컴퓨터/Azure VM에 의해 수행됩니다. 데이터는 클라우드의 서비스를 통해 이동하지 않습니다. <p> 참고: 데이터 관리 게이트웨이는 데이터 원본과 같은 온-프레미스 컴퓨터/Azure VM 또는 두 데이터 저장소에 모두 연결할 수 있는 경우 다른 온-프레미스 컴퓨터/Azure VM에 있을 수 있습니다.</p>
클라우드 | 온-프레미스/Azure VM(IaaS) | 위와 동일합니다. 
온-프레미스/Azure VM(IaaS) | 온-프레미스/Azure VM | **소스와 연결된 데이터 관리 게이트웨이**. 데이터는 클라우드의 서비스를 통해 이동하지 않습니다. 위의 참고를 참조하세요.   
클라우드 | 클라우드 | <p>**복사 작업을 구동하는 클라우드 서비스**. Azure 데이터 팩터리는 같은 지리의 싱크 위치에 가장 가까운 지역에서 이 서비스의 배포를 사용합니다. 매핑은 다음 테이블을 참조하세요. </p><table><tr><th>대상 데이터 원본의 지역</th> <th>데이터 이동에 사용되는 지역</th></tr><tr><td>미국 동부 US</td><td>미국 동부</td></tr><tr><td>미국 동부 2</td><td>미국 동부 2</td><tr/><tr><td>미국 중부</td><td>미국 중부</td><tr/><tr><td>미국 서부</td><td>미국 서부</td></tr><tr><td>미국 북중부</td><td>미국 북중부</td></tr><tr><td>미국 남중부</td><td>미국 남중부</td></tr><tr><td>북유럽</td><td>북유럽</td></tr><tr><td>서유럽</td><td>서유럽</td></tr><tr><td>동남 아시아</td><td>동남 아시아</td></tr><tr><td>동아시아</td><td>동남 아시아</td></tr><tr><td>일본 동부</td><td>일본 동부</td></tr><tr><td>일본 서부</td><td>일본 서부</td></tr><tr><td>브라질 남부</td><td>브라질 남부</td></tr></table>


> [AZURE.NOTE] 대상 데이터 원본의 지역이 위의 목록에 없는 경우 대체 지역을 탐색하는 대신에 복사 작업이 실패합니다. 우리는 오스트레일리아 동부 및 오스트레일리아 남동부로 머지 않아 확장할 것입니다.



### <a name="moveonpremtocloud"></a>온-프레미스 위치와 클라우드 간에 데이터를 안전하게 이동
최신 데이터 통합에 대한 도전 과제 중 하나는 온-프레미스와 클라우드에서 원활한 데이터 이동입니다. 데이터 관리 게이트웨이는 하이브리드 데이터 파이프라인을 사용하도록 온-프레미스를 설치할 수 있는 에이전트입니다.

데이터 게이트웨이는 다음 기능을 제공합니다.

1.	온-프레미스 데이터 저장소에 액세스를 안전하게 관리합니다.
2.	데이터 팩터리 내 온-프레미스 데이터 저장소 및 클라우드 데이터 저장소를 모델로 데이터를 이동합니다.
3.	대시보드에 기반하는 데이터 팩터리 클라우드를 사용하여 게이트웨이 상태에 대한 가시성을 사용하는 모니터링 및 관리를 위한 유리의 단일 창이 있습니다.

**Express 경로** 및 **게이트웨이 사용**을 사용하는 경우 데이터 원본을 온-프레미스 데이터 원본(즉, 방화벽 뒤)으로 처리하여 서비스와 데이터 원본 간에 연결을 설정해야 합니다.

자세한 내용은 [온-프레미스 및 클라우드 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.

### 안정적이고 비용 효율적인 데이터 이동
복사 작업은 안전한 방법으로 많은 양의 데이터를 이동하도록 설계되었으며 많고 다양한 데이터 원본에 걸친 일시적인 오류를 방어합니다. 옵션을 사용하여 비용 효율적인 방식으로 데이터를 복사하여 네트워크를 통해 압축을 사용하도록 설정합니다.

### 다양한 형식 시스템에서 형식 변환
다른 데이터 저장소에는 서로 다른 네이티브 형식 시스템이 있습니다. 복사 작업은 다음 2단계 접근 방법으로 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

각 데이터 저장소 커넥터 문서에서 데이터 저장소용 .NET에 지정된 네이티브 형식 시스템에 대한 매핑을 찾을 수 있습니다. 테이블을 만드는 동안 적절한 형식을 결정하는 이러한 매핑을 사용할 수 있으므로 복사 작업 시 오른쪽 변환이 수행 됩니다.

### 다른 파일 형식으로 작업
파일 기반 원본 복사 작업이 이진, 텍스트 및 Avro 형식을 포함하는 다양한 파일 형식을 지원합니다. 복사 작업을 사용하여 한 형식에서 다른 형식으로 데이터를 변환할 수 있습니다. 예: 텍스트(CSV)에서 Avro로 변환 데이터가 구조화되지 않은 경우 [데이터 집합](data-factory-create-datasets.md)의 JSON 정의에서 **Structure** 속성을 생략할 수 있습니다.

### 복사 작업 속성
이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 속성은 모든 유형의 활동에 사용할 수 있습니다. 반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다.

복사 작업의 경우 **typeProperties** 섹션은 원본 및 싱크의 형식에 따라 달라집니다. 각 페이지에 특정 데이터 저장소의 위에 나열 된 문서 이러한 속성을 데이터 저장소 형식에 따라 다릅니다.


### 복사 작업 성능 및 조정 
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소에 대해 설명하는 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 문서를 참조하세요. 또한 내부 테스트 중에 관측된 성능을 나열하고 복사 작업의 성능을 최적화하는 다양한 방법을 설명합니다.

<!---HONumber=AcomDC_0224_2016-->