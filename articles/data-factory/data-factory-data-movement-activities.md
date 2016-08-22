<properties 
	pageTitle="복사 작업을 사용하여 데이터 이동 | Microsoft Azure" 
	description="데이터 팩터리 파이프라인에서 데이터 이동에 대해 알아보기: 클라우드 저장소 간 및 온-프레미스 및 클라우드 간의 데이터 마이그레이션. 복사 작업 사용" 
	keywords="데이터 복사, 데이터 이동, 데이터 마이그레이션, 데이터 전송"
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
	ms.date="08/08/2016" 
	ms.author="spelluru"/>

# 복사 작업을 사용하여 데이터 이동

## 개요
Azure Data Factory를 통해 복사 작업을 사용하면 다양한 온-프레미스와 클라우드 데이터 원본에서 Azure로 여러 형태의 데이터를 복사하여 추가로 변환하고 분석할 수 있습니다. 복사 작업은 BI(비즈니스 인텔리전스) 및 응용 프로그램 사용에 대한 변환 및 분석 결과를 게시하는 데 사용할 수도 있습니다.

![복사 작업의 역할](media/data-factory-data-movement-activities/copy-activity.png)

안전하고 믿을 수 있으며 확장성이 있고 [전역적으로 사용할 수 있는 서비스](#global)에 의해 복사 작업을 사용합니다. 이 문서는 Data Factory 및 복사 작업에서 데이터 이동에 대한 세부 정보를 제공합니다. 먼저 두 개의 클라우드 데이터 저장소 간 그리고 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 이루어지는 데이터 마이그레이션 방식에 대해 살펴보겠습니다.

> [AZURE.NOTE] 일반적인 작업에 대해 알아보려면 [파이프라인 및 작업 이해](data-factory-create-pipelines.md) 문서를 참조하세요.

### 두 클라우드 데이터 저장소 간의 데이터 복사
원본 및 싱크(대상) 데이터 저장소가 모두 클라우드에 있는 경우 복사 작업은 다음 단계를 통해 원본에서 싱크로 데이터를 복사/이동합니다. 복사 작업을 구동하는 서비스는 다음을 수행합니다.

1. 원본 데이터 저장소에서 데이터 읽기
2. 입력 데이터 집합, 출력 데이터 집합 및 복사 작업의 구성을 기준으로 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환 수행
3.	대상 데이터 저장소에 데이터 기록

서비스는 자동으로 데이터 이동을 수행할 최적의 영역을 선택하며 일반적으로 싱크 데이터 저장소에 가장 가까운 지역입니다.

![클라우드-클라우드 복사](./media/data-factory-data-movement-activities/cloud-to-cloud.png)


### 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간 데이터 복사
회사 방화벽 뒤의 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간에 안전하게 데이터를 이동하려면 하이브리드 데이터 이동 및 처리를 돕는 에이전트인 데이터 관리 게이트웨이를 온-프레미스 컴퓨터에 설치해야 합니다. 데이터 관리 게이트웨이는 데이터 저장소와 같은 컴퓨터에 설치하거나 데이터 저장소에 액세스할 수 있는 별도 컴퓨터에 설치할 수 있습니다. 이 시나리오에서는 직렬화/역직렬화, 압축/압축 해제, 열 매핑 및 형식 변환이 데이터 관리 게이트웨이에 의해 수행됩니다. Azure 데이터 팩터리 서비스를 통해 이동하지 않는 서비스가 이러한 경우입니다. 데이터 관리 게이트웨이는 데이터를 대상 저장소에 직접 씁니다.

![온-프레미스-클라우드 복사](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

소개 및 연습은 [온-프레미스 및 클라우드 데이터 저장소 간 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md) 문서를 참조하고 데이터 관리 게이트웨이에 대한 자세한 정보는 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md) 문서를 참조하세요.

데이터 관리 게이트웨이를 사용하여 Azure IaaS VM(Infrastructure-as-a-Service 가상 컴퓨터)에서 호스트되는 지원되는 데이터 저장소에서/로 데이터를 이동할 수도 있습니다. 이 경우 데이터 관리 게이트웨이는 데이터 저장소와 같은 Azure VM에 설치하거나 데이터 저장소에 액세스할 수 있는 별도 VM에 설치할 수 있습니다.

## 지원되는 데이터 저장소 및 형식
복사 작업은 **원본** 데이터 저장소의 데이터를 **싱크** 데이터 저장소로 복사합니다. Data Factory는 다음과 같은 데이터 저장소를 지원하고 **모든 원본의 데이터를 모든 싱크에 쓸 수** 있습니다. 해당 저장소에서 데이터를 복사하는 방법에 알아보려면 데이터 저장소를 클릭합니다.

Category | 데이터 저장소 | 원본으로서 지원 | 싱크로서 지원
:------- | :--------- | :------------------ | :-----------------
Azure | [Azure Blob](data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](data-factory-azure-datalake-connector.md) <br/> [ Azure SQL 데이터베이스](data-factory-azure-sql-connector.md) <br/> [Azure SQL 데이터 웨어하우스](data-factory-azure-sql-data-warehouse-connector.md) <br/> [Azure 테이블](data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](data-factory-azure-documentdb-connector.md) <br/> | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ 
데이터베이스 | [SQL Server](data-factory-sqlserver-connector.md)* <br/> [Oracle](data-factory-onprem-oracle-connector.md)* <br/> [MySQL](data-factory-onprem-mysql-connector.md)* <br/> [DB2](data-factory-onprem-db2-connector.md)* <br/> [Teradata](data-factory-onprem-teradata-connector.md)* <br/> [PostgreSQL](data-factory-onprem-postgresql-connector.md)* <br/> [Sybase](data-factory-onprem-sybase-connector.md)* <br/>[Cassandra](data-factory-onprem-cassandra-connector.md)* <br/>[MongoDB](data-factory-on-premises-mongodb-connector.md)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓<br/> ✓ <br/> ✓ <br/> ✓ | ✓ <br/> ✓ <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; 
파일 | [파일 시스템](data-factory-onprem-file-system-connector.md)* <br/> [HDFS(Hadoop Distributed File System)](data-factory-hdfs-connector.md)* | ✓ <br/> ✓ <br/> | ✓ <br/> &nbsp; 
기타 | [Salesforce](data-factory-salesforce-connector.md)<br/> [일반 ODBC](data-factory-odbc-connector.md)* <br/> [일반 OData](data-factory-odata-connector.md) <br/> [웹 테이블(HTML의 테이블)](data-factory-web-table-connector.md) <br/> [GE Historian](data-factory-odbc-connector.md#ge-historian-store)* | ✓ <br/> ✓ <br/> ✓ <br/> ✓ <br/> ✓ | &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;

> [AZURE.NOTE] *가 있는 데이터 저장소는 온-프레미스 또는 Azure IaaS에 있을 수 있으며 온-프레미스/Azure IaaS 컴퓨터에 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)를 설치해야 합니다.

**복사 작업**에서 지원되지 않는 데이터 저장소에서 다른 위치로 또는 그 반대로 데이터를 이동해야 하는 경우 데이터 복사/이동에 대한 사용자 고유의 논리로 Data Factory의 **사용자 지정 작업**을 사용할 수 있습니다. 사용자 지정 작업을 만들고 사용하는 방법에 대한 자세한 내용은 [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용](data-factory-use-custom-activities.md)을 참조하세요.

### 지원 파일 형식
복사 작업은 Azure Blob, 파일 시스템 및 HDFS(File System, and Hadoop Distributed File System)와 같은 두 파일 기반 데이터 저장소 사이에서 파일을 그대로 복사할 수 있습니다. 이렇게 하려면 입력 및 출력 데이터 집합의 [형식 섹션](data-factory-create-datasets.md)을 건너뛸 수 있고 직렬화/역직렬화 과정 없이 데이터가 복사됩니다.

복사 작업은 또한 텍스트, Avro, ORC 및 JSON 등 지정된 형식의 파일을 읽고 씁니다. 수행할 수 있는 복사 작업의 몇 가지 예는 다음과 같습니다.

-	Azure Blob에서 텍스트(CSV) 형식의 데이터를 복사하여 Azure SQL에 기록
-	파일 시스템 온-프레미스에서 텍스트(CSV) 형식의 파일을 복사하여 Avro 형식의 Azure Blob에 기록
-	Azure SQL 데이터베이스의 데이터를 복사하여 ORC 형식의 HDFS 온-프레미스에 기록



## <a name="global"></a>전역적으로 사용 가능한 데이터 이동
Azure Data Factory 자체는 미국 서부, 미국 동부 및 북유럽 지역에서만 사용할 수 있지만, 다음과 같은 지역 및 지리에서는 복사 작업을 지원하는 서비스를 전역적으로 사용할 수 있습니다. 전역적으로 사용 가능한 토폴로지는 대개 영역 간 홉을 방지하는 효율적인 데이터 이동을 보장합니다. 지역에서 Data Factory 서비스와 데이터 이동의 가용성은 [지역별 서비스](https://azure.microsoft.com/regions/#services)를 참조하세요.

### 클라우드 데이터 저장소 간의 데이터 복사
원본 및 싱크 데이터 저장소가 모두 클라우드에 있는 경우, Azure Data Factory는 데이터 이동을 수행하기 위해 동일한 지역의 싱크 위치에 가장 가까운 지역에서 서비스 배포를 사용합니다. 매핑은 다음 표를 참조하세요.

대상 데이터 저장소의 지역 | 데이터 이동에 사용되는 지역
:----------------------------------- | :----------------------------
미국 동부 | 미국 동부
미국 동부 2 | 미국 동부 2
미국 중부 | 미국 중부
미국 서부 | 미국 서부
미국 중북부 | 미국 중북부
미국 중남부 | 미국 중남부
북유럽 | 북유럽
서유럽 | 서유럽
동남아시아 | 동남아시아
동아시아 | 동남아시아
일본 동부 | 일본 동부
일본 서부 | 일본 동부
브라질 남부 | 브라질 남부
오스트레일리아 동부 | 오스트레일리아 동부
오스트레일리아 남동부 | 오스트레일리아 남동부


> [AZURE.NOTE] 대상 데이터 원본의 지역이 위의 목록에 없는 경우 대체 지역을 탐색하는 대신에 복사 작업이 실패합니다.

### 온-프레미스 데이터 저장소와 클라우드 데이터 저장소 간 데이터 복사
온-프레미스(또는 Azure IaaS VM)와 클라우드 간에 데이터를 복사하는 경우, 데이터 이동은 온-프레미스 컴퓨터 또는 Azure IaaS VM의 [데이터 관리 게이트웨이](data-factory-data-management-gateway.md)에 의해 수행됩니다. 데이터가 싱크 데이터 저장소에 기록되기 전에 준비 중인 Azure Blob 저장소를 통해 전달되는 경우 [준비된 복사](data-factory-copy-activity-performance.md#staged-copy) 기능을 사용하지 않으면 데이터는 클라우드의 서비스를 통해 전달되지 않습니다.


## 복사 작업을 포함하는 파이프라인 만들기 
두 가지 방법으로 복사 작업을 포함하는 파이프라인을 만들 수 있습니다.

### 복사 마법사 사용
**Data Factory 복사 마법사**를 사용하면 연결된 서비스, 데이터 집합 및 파이프라인에 대한 **JSON 정의를 작성하지 않고도** 지원되는 원본에서 대상으로 데이터를 복사하기 위해 복사 작업을 포함하는 파이프라인을 만들 수 있습니다. 마법사에 대한 자세한 내용은 [Data Factory 복사 마법사](data-factory-copy-wizard.md) 자습서를 참조하세요.

### JSON 스크립트 사용
복사 작업을 포함하는 파이프라인에 대한 JSON 정의를 생성하고 배포하여 Data Factory에서 파이프라인을 만들기 위해 Azure 포털 (또는) Visual Studio (또는) Azure PowerShell에서 Data Factory 편집기를 사용할 수 있습니다. 단계별 지침이 있는 자습서인 [자습서: Azure Data Factory 파이프라인에서 복사 작업 사용](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)을 참조하세요.

이름, 설명, 입력 및 출력 테이블, 다양한 정책 등과 같은 JSON 속성은 모든 유형의 활동에 사용할 수 있습니다. 반면 활동의 **typeProperties** 섹션에서 사용할 수 있는 속성은 각 활동 형식에 따라 다릅니다.

복사 작업의 경우 **typeProperties** 섹션은 원본 및 싱크의 형식에 따라 달라집니다. 해당 데이터 저장소에 대한 복사 작업에서 지원하는 형식 속성에 대해 알아보려면 [지원되는 원본/싱크](#supported-data-stores) 섹션에서 원본/싱크를 클릭하세요.

샘플 JSON 정의는 다음과 같습니다.

	{
	  "name": "ADFTutorialPipeline",
	  "properties": {
	    "description": "Copy data from Azure blob to Azure SQL table",
	    "activities": [
	      {
	        "name": "CopyFromBlobToSQL",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "InputBlobTable"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OutputSQLTable"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource"
	          },
	          "sink": {
	            "type": "SqlSink",
	            "writeBatchSize": 10000,
	            "writeBatchTimeout": "60:00:00"
	          }
	        },
	        "Policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	    ],
	    "start": "2016-07-12T00:00:00Z",
	    "end": "2016-07-13T00:00:00Z"
	  }
	} 

출력 데이터 집합에 정의된 일정은 활동이 실행될 때 결정됩니다(예를 들어 **매일**: 빈도: 일 및 간격: 1). 작업은 한 입력 데이터 집합(**원본**)에서 한 출력 데이터 집합(**싱크**)으로 데이터를 복사합니다. 복사 작업에 하나 이상의 입력 데이터 집합을 지정할 수 있으며 활동이 실행되기 전에 종속성을 확인하는 데 사용되지만, 첫 번째 데이터 집합의 데이터만 대상 데이터 집합에 복사됩니다. 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md)을 참조하세요.

## 성능 및 튜닝 
Azure Data Factory의 데이터 이동(복사 작업) 성능에 영향을 주는 주요 요소에 대해 설명하는 [복사 작업 성능 및 조정 가이드](data-factory-copy-activity-performance.md) 문서를 참조하세요. 또한 내부 테스트 중에 관측된 성능을 나열하고 복사 작업의 성능을 최적화하는 다양한 방법을 설명합니다.

## 예약 및 순차 복사
Data Factory에서 예약 및 실행의 작동 방식에 대한 자세한 내용은 [예약 및 실행](data-factory-scheduling-and-execution.md) 문서를 참조하세요.

순차/순서가 지정된 방식으로 하나씩 여러 복사 작업을 실행하는 것이 가능합니다. 문서의 [순서가 지정된 복사](data-factory-scheduling-and-execution.md#ordered-copy) 섹션을 참조하세요.

## 형식 변환 
다른 데이터 저장소에는 서로 다른 네이티브 형식 시스템이 있습니다. 복사 작업은 다음 2단계 접근 방법으로 원본 형식에서 싱크 형식으로 자동 형식 변환을 수행합니다.

1. 네이티브 원본 형식에서 .NET 형식으로 변환
2. .NET 형식에서 네이티브 싱크 형식으로 변환

각 데이터 저장소별 문서에서 데이터 저장소용 .NET에 지정된 네이티브 형식 시스템에 대한 매핑을 찾을 수 있습니다([지원되는 데이터 저장소](#supported-data-stores) 표에 있는 각 링크 클릭). 테이블을 만드는 동안 적절한 형식을 결정하는 이러한 매핑을 사용할 수 있으므로 복사 작업 시 오른쪽 변환이 수행 됩니다.

 
## 다음 단계
- 일반적으로 복사 작업을 사용하여 원본 데이터 저장소에서 싱크 데이터 저장소에 데이터로 이동하는 방법에 대해 자세히 알아보려면 [Azure Blob에서 Azure SQL로 데이터 복사](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)를 참조하세요.
- 온-프레미스 데이터 저장소에서 클라우드 데이터 저장소에 데이터를 이동하는 방법에 대한 자세한 내용은 [온-프레미스에서 클라우드 데이터 저장소로 데이터 이동](data-factory-move-data-between-onprem-and-cloud.md)을 참조하세요.
 

<!---HONumber=AcomDC_0810_2016-->