<properties 
	pageTitle="데이터 팩터리 - 릴리스 정보 | Azure" 
	description="데이터 팩터리 릴리스 정보" 
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
	ms.date="07/16/2015" 
	ms.author="spelluru"/>

# Azure Data Factory 릴리스 정보

## 데이터 팩터리의 2015/07/17 릴리스 정보
다음 JSON 변경 사항이 Azure PowerShell 2015년 7월 릴리스에서 도입되었습니다.

## 연결된 서비스, 테이블 및 작업의 형식에 대한 업데이트
리소스 종류 | JSON에서 현재 이름 | JSON에서 새 이름
------------- | -------------------- | ----------------
연결된 서비스(데이터 원본) | AzureSqlLinkedService | AzureSqlDatabase
연결된 서비스(데이터 원본) | AzureStorageLinkedService | AzureStorage
연결된 서비스(데이터 원본) | DocumentDbLinkedService | DocumentDB
연결된 서비스(데이터 원본) | OnPremisesFileSystemLinkedService | OnPremisesFileServer
연결된 서비스(데이터 원본) | OnPremisesOracleLinkedService | OnPremisesOracle
연결된 서비스(데이터 원본) | OnPremisesSqlLinkedService | OnPremisesSqlServer
연결된 서비스(데이터 원본) | OnPremisesMySqlLinkedService | OnPremisesMySql
연결된 서비스(데이터 원본) | OnPremisesDb2LinkedService | OnPremisesDb2
연결된 서비스(데이터 원본) | OnPremisesTeradataLinkedService | OnPremisesTeradata
연결된 서비스(데이터 원본) | OnPremisesSybaseLinkedService | OnPremisesSybase
연결된 서비스(데이터 원본) | OnPremisesPostgreSqlLinkedService | OnPremisesPostgreSql
연결된 서비스(계산) | AzureMlLinkedService | AzureML
연결된 서비스(계산) | HDInsightBYOCLinkedService | HDInsight
연결된 서비스(계산) | HDInsightOnDemandLinkedService | HDInsightOnDemand
연결된 서비스(계산) | AzureBatchLinkedService | AzureBatch
데이터 집합 | AzureBlobLocation | AzureBlob
데이터 집합 | AzureTableLocation | AzureTable
데이터 집합 | AzureSqlTableLocation | AzureSqlTable
데이터 집합 | DocumentDbCollectionLocation | DocumentDbCollection 
데이터 집합 | OnPremisesFileSystemLocation | FileShare
데이터 집합 | OnPremisesOracleTableLocation | OracleTable
데이터 집합 | OnPremisesSqlServerTableLocation | SqlServerTable
데이터 집합 | RelationTableLocation | RelationalTable
작업 | CopyActivity | 복사
작업 | HDInsightActivity(Hive 변환) | HDInsightHive
작업 | HDInsightActivity(Pig 변환) | HDInsightPig
작업 | HDInsightActivity(MapReduce 변환) | HDInsightMapReduce
작업 | HDInsightActivity(스트리밍) | HDInsightHadoopStreaming
작업 | AzureMLBatchScoringActivity | AzureMLBatchScoring
작업 | StoredProcedureActivity | SqlServerStoredProcedure

## 새 typeProperties 요소
새 **typeProperties** 요소가 연결된 서비스/테이블/작업에 대한 형식 관련 속성을 포함합니다.

### 이전 연결된 서비스 JSON
	{
	    "name": "StorageLinkedService",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

### 새 연결된 서비스 JSON
	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

다음을 확인합니다.

- **type** 속성이 한 수준 위로 이동하고 **AzureStorage**로 설정되었습니다(**AzureStorageLinkedService**에서 **AzureStorage**로 변경됨). 
- 새 **typeProperties** 요소가 Azure 저장소 연결된 서비스에서 지원하는 속성(이 예에서는 **connectionString**)을 포함합니다.  

### 이전 데이터 집합 JSON
	{
	    "name": "EmpTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureTableLocation",
	            "tableName": "myazuretable",
	            "linkedServiceName": "MyLinkedService"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}

### 새 데이터 집합 JSON

	{
	    "name": "EmpTable",
	    "properties": {
	        "type": "AzureTable",
	        "linkedServiceName": "MyLinkedServiceName",
	        "typeProperties": {
	            "tableName": "myazuretable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": "1"
	        }
	    }
	}

다음을 확인합니다.

- **type** 속성이 한 수준 위로 이동하고 형식 이름 **AzureTableLocation**이 **AzureTable**로 변경되었습니다.
- **linkedServiceName**이 한 수준 위로 이동했습니다. 
- 이제 **location** 요소가 제거되고 **location** 섹션에서 지정된 **tableName** 같은 형식 관련 속성이 새 **typeProperties** 섹션에서 지정됩니다.  

### 이전 작업 JSON

	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "CopyActivity",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "transformation":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}   

### 새 작업 JSON
	
	{
	    "name": "CopyFromSQLtoBlob   ",
	    "description": "description", 
	    "type": "Copy",
	    "inputs":  [ { "name": "InputSqlDA"  } ],
	    "outputs":  [ { "name": "OutputBlobDA" } ],
	    "typeProperties":
	    {
	        "source":
	        {
	            "type": "SqlSource",
	            "sqlReaderQuery": "select * from MyTable"
	        },
	        "sink":
	        {
	            "type": "BlobSink"
	        }
	    }
	}

다음을 확인합니다.

- **transformation** 요소가 새 **typeProperties** 요소로 바뀌었습니다.

## waitOnExternal 요소가 제거됨
**waitOnExternal** 요소가 새 **external** 및 **externalData** 속성으로 바뀌었습니다.

### 이전 JSON
	{
	    "name": "EmpTableFromBlob",
	    "properties":
	    {
	        "location": 
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "adftutorial/",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	            "linkedServiceName": "StorageLinkedService"
	        },
	        "availability": 
	        {
	            "frequency": "hour",
	            "interval": 1,
                "waitOnExternal": 
				{
			        "retryInterval": "00:01:00",
			        "retryTimeout": "00:10:00",
			        "maximumRetry": "3"			
				}
	        }
	    }
	} 

### 새 JSON
	{
	  "name": "EmpTableFromBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "adftutorial/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ","
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": "3"
	      }
	    }
	  }
	}

다음을 확인합니다.

- **availability** 섹션에서 **waitOnExternal** 속성이 제거되었습니다. 
- 새 **external** 속성이 한 수준 위로 추가되고 외부 테이블에 대해 **true**로 설정되었습니다. 
- **retryInterval** 같은 **waitOnExternal** 요소의 속성이 **Policy** 요소의 새 **externalData** 섹션에 추가되었습니다.
- **externalData** 요소는 선택적 요소입니다. 
- **externalData** 요소를 사용하는 경우 **external** 속성을 **true**로 설정해야 합니다. 
 

## BlobSink의 새 copyBehavior 속성
**BlobSink**는 **copyBehavior**라는 새 속성을 지원합니다. 이 속성은 원본이 **BlobSource** 또는 **FileSystem**인 경우 복사 동작을 정의합니다. **copyBehavior** 속성에 대해 세 가지 값이 가능합니다.

**PreserveHierarchy**: 대상 폴더에서 파일 계층을 유지합니다. 즉, 원본 폴더에 대한 원본 파일의 상대 경로가 대상 폴더에 대한 대상 파일의 상대 경로와 동일합니다.


**FlattenHierarchy**: 원본 폴더의 모든 파일은 대상 폴더의 첫 번째 수준에 있게 됩니다. 대상 파일은 자동 생성된 이름을 갖습니다.


**MergeFiles**: 원본 폴더의 모든 파일을 하나의 파일로 병합합니다. 파일/Blob 이름이 지정된 경우 지정된 이름이 병합된 파일 이름이 됩니다. 그렇지 않으면 자동 생성된 파일 이름이 병합된 파일 이름이 됩니다.
 
## 모든 HDInsight 작업에 대한 새 getDebugInfo 속성
HDInsight 작업(Hive, Pig, MapReduce, Hadoop 스트리밍)은 새 속성인 **getDebugInfo** 속성을 지원합니다. **getDebugInfo** 속성은 선택적 요소입니다. **Failure**로 설정되면 실행이 실패한 경우에만 로그가 다운로드됩니다. **All**로 설정되면 실행 상태에 관계 없이 로그가 항상 다운로드됩니다. **None**으로 설정되면 로그가 다운로드되지 않습니다.

  
     

## 데이터 팩터리의 2015/04/10 릴리스 정보
이제 **테이블** 블레이드에 **Recently updated slices(최근에 업데이트된 조각)** 및 **Recently failed slices(최근에 실패한 조각)** 목록이 표시됩니다. 이러한 목록은 조각의 업데이트 시간을 기준으로 정렬됩니다. 조각의 업데이트 시간은 다음과 같은 상황에서 변경됩니다.

-  **Set-AzureDataFactorySliceStatus**를 사용하거나 조각의 **조각** 블레이드에서 **실행**을 클릭하여 수동으로 조각 상태를 업데이트합니다.
-  실행(예: 실행 시작, 실행 종료 및 실패, 실행 종료 및 성공 등)으로 인해 조각 상태가 변경됩니다.

목록의 제목 또는 **...(줄임표)**을 클릭하여 더 큰 조각 목록을 표시합니다. 도구 모음의 **필터**를 클릭하여 조각을 필터링합니다.
 
**데이터 조각(조각 시간별)** 타일을 클릭하면 조각 시간을 기준으로 정렬된 조각을 볼 수 있습니다. 해당 컬렉션의 조각은 조각 시간을 기준으로 정렬됩니다. 예를 들어 매시간 일정인 경우 조각은 다음과 같습니다. - 4/4/2015 5pm In progress - 4/4/2015 4pm Succeeded - 4/4/2015 3pm Failed

그러나 이전 조각을 다시 실행하는 경우 사용자가 더 관심을 가진 조각인 경우에도 이 목록의 맨 위에 표시되지 않습니다.

## 데이터 팩터리의 2015/3/31 릴리스 정보
- 업데이트된 **데이터 관리 게이트웨이** 설치 패키지가 [Microsoft 다운로드 센터][adf-gateway-download]에 게시되었습니다.
- 이제 **온-프레미스 파일 시스템에서 Azure Blob으로 복사**가 지원됩니다. 자세한 내용은 다음 항목을 참조하십시오.
	-  [온-프레미스 파일 시스템 연결된 서비스](https://msdn.microsoft.com/library/dn930836.aspx)
	-  [테이블 JSON의 OnPremisesFileSystemLocation 속성](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem)
	-  [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) 업데이트된 복사 매트릭스 및 **FileSystemSource** 속성을 참조하십시오. 
-  이제 **온-프레미스 Oracle 데이터베이스에서 Azure Blob으로 복사**가 지원됩니다. 자세한 내용은 다음 항목을 참조하십시오. 
	-  [온-프레미스 Oracle 연결된 서비스](https://msdn.microsoft.com/library/dn948537.aspx)
	-  [테이블 JSON의 OnPremisesOracleTableLocation 속성](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) 
	-  [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) 업데이트된 복사 매트릭스 및 **OracleSource** 속성을 참조하십시오.
-  Azure Blob에서 텍스트 파일의 인코딩을 지정할 수 있습니다. 새 [encodingName 속성](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob)을 참조하십시오. 
- SQL 싱크로 복사하는 경우 추가 매개 변수가 있는 저장 프로시저를 호출할 수 있습니다. 자세한 내용은 [SQL 싱크에 대한 저장 프로시저 호출][adf-copy-advanced]을 참조하십시오.   

예제를 포함하여 자세한 내용은 Blob 게시물: [Azure Data Factory 업데이트 - 새 데이터 저장소](http://azure.microsoft.com/blog/2015/03/30/azure-data-factory-update-new-data-stores/)를 참조하십시오.

## 데이터 팩터리의 2015/2/27 릴리스 정보

### 새롭게 향상된 기능
- **Azure Data Factory 편집기**. Azure Preview 포털의 일부인 데이터 팩터리 편집기를 사용하면 연결된 서비스, 데이터 집합 및 파이프라인을 정의하는 JSON 파일을 만들고 편집 및 배포할 수 있습니다. 편집기의 주요 목표는 Azure PowerShell 설치 및 PowerShell cmdlet을 사용한 규모 확장을 요구하지 않고 Azure Data Factory 아티팩트를 만들기 위한 빠르고 간단한 UI(사용자 인터페이스)를 제공하는 것입니다. 데이터 팩터리 편집기에 대한 간략한 개요와 동영상은 [Azure Data Factory 편집기 - 경량 웹 편집기][adf-editor-blog](영문) 블로그 게시물을 참조하세요. 편집기에 대한 자세한 개요는 [데이터 팩터리 편집기][adf-editor] 문서를 참조하세요.          

### 변경 내용

## 데이터 팩터리의 2015/1/26 릴리스 정보 ##

### 변경 내용
- 업데이트된 **데이터 관리 게이트웨이** 설치 패키지가 [Microsoft 다운로드 센터][adf-gateway-download]에 게시되었습니다. 이 릴리스부터, 이 다운로드 위치에서 Azure Data Factory에 사용할 최신 데이터 관리 게이트웨이를 찾을 수 있습니다. 이 설치 패키지는 Azure Data Factory와 Office 365용 Power BI 서비스 둘 다를 제공합니다. 두 서비스를 모두 사용하는 경우 데이터 팩터리 및 Power BI의 게이트웨이를 서로 다른 컴퓨터에 설치하고, 데이터 팩터리 또는 Power BI 설명서의 지침에 따라 다르게 구성해야 합니다.
- 이제 **복사 작업**이 온-프레미스 SQL Server 데이터베이스와 Azure SQL 데이터베이스 간의 데이터 복사를 지원합니다. 자세한 내용은 [복사 작업][adf-copy-activity]을 참조하고, JSON 샘플은 [GitHub][adf-github-samples]를 참조하십시오.
- **SqlSink**가 새 속성 **WriteBatchTimeout**을 지원합니다. 이 속성은 작업이 시간 초과되기 전에 배치 삽입 작업이 완료되기를 기다리는 기간을 구성할 수 있는 유연성을 제공합니다. 하이브리드 복사(온-프레미스 데이터 원본과 클라우드 데이터 원본을 사용하는 복사 작업)의 경우 이 속성을 사용하려면 버전 1.4 이상의 게이트웨이가 있어야 합니다. 
- 이제 **SQL Server 연결된 서비스**가 **Windows 인증**을 지원합니다. 
	- 포털을 사용하여 SQL Server 연결된 서비스를 만드는 경우 이제 Windows 인증을 사용하도록 선택하고 적절한 자격 증명을 설정할 수 있습니다. 이 경우 버전 1.4 이상의 게이트웨이가 있어야 합니다. 
	- Azure PowerShell을 사용하여 SQL Server 연결된 서비스를 만드는 경우 일반 텍스트로 연결 정보를 지정하거나, 업데이트된 [New-AzureDataFactoryEncryptValue cmdlet][adf-encrypt-value-cmdlet]을 사용하여 연결 정보를 암호화한 다음 연결된 서비스 JSON 페이로드의 연결 문자열 속성에 암호화된 문자열을 사용할 수 있습니다. JSON에서 연결된 서비스를 정의하는 방법에 대한 자세한 내용은 [연결된 서비스][adf-msdn-linked-services]를 참조하십시오. 암호화 기능은 New-AzureDataFactoryEncryptValue cmdlet에서 아직 지원되지 않습니다. 

## 데이터 팩터리의 2014/12/11 릴리스 정보 ##

### 새롭게 향상된 기능

- Azure 기계 학습 통합
	- 이 릴리스의 Azure Data Factory 서비스를 통해 **AzureMLLinkedService** 및 **AzureMLBatchScoringActivity**를 사용하여 Azure Data Factory를 Azure ML(기계 학습)과 통합할 수 있습니다. 자세한 내용은 [데이터 팩터리 및 Azure 기계 학습을 사용하여 예측 파이프라인 만들기][adf-azure-ml]를 참조하십시오. 
- 게이트웨이 버전 상태가 제공됨
	- 현재 설치된 버전보다 최신 버전의 게이트웨이를 사용할 수 있는 경우 Azure 미리 보기 포털 및 Get-AzureDataFactoryGateway cmdlet의 출력에 "NewVersionAvailable" 상태가 표시됩니다. 그러면 포털 경로를 따라 새 설치 파일(.msi)을 다운로드한 다음 실행하여 최신 게이트웨이를 설치할 수 있습니다. 추가 구성은 필요하지 않습니다.

### 변경 내용

- HdInsightOnDemandLinkedService에서 JobsContainer가 제거되었습니다.
	- HDInsightOnDemandLinkedService에 대한 JSON 정의에서 더 이상 **jobsContainer** 속성을 지정할 필요가 없습니다. 주문형 연결된 서비스에 대해 지정된 속성이 있는 경우 해당 속성은 무시됩니다. 연결된 서비스에 대한 JSON 정의에서 이 속성을 제거하고 New-AzureDataFactoryLinkedService cmdlet을 사용하여 연결된 서비스 정의를 업데이트할 수 있습니다.
- HDInsightOnDemandLinkedService에 대한 선택적 구성 매개 변수
	- 이 릴리스에는 HDInsightOnDemandLinked(주문형 HDInsight 클러스터)에 대한 몇 가지 선택적 구성 매개 변수 지원이 도입되었습니다. 자세한 내용은 [ClusterCreateParameters 속성][on-demand-hdi-parameters](영문)을 참조하십시오.
- 게이트웨이 위치가 제거됨
	- 포털 또는 PowerShell(New-AzureDataFactoryGateway)을 통해 Azure Data Factory 게이트웨이를 만들 때 더 이상 게이트웨이의 위치를 지정할 필요가 없습니다. 데이터 팩터리 영역이 상속됩니다. 마찬가지로, JSON을 사용하여 SQL Server 연결된 서비스를 구성할 때 더 이상 "gatewayLocation" 속성이 필요하지 않습니다. 데이터 팩터리 .NET SDK도 이러한 변경 내용을 반영하도록 업데이트되었습니다.
	- 이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 계속 위치 설정을 제공해야 합니다.
 
     

#### 주요 변경 내용
	
- CustomActivity가 DotNetActivity로 변경됨
	- **ICustomActivity** 인터페이스 이름이 **IDotNetActivity**로 변경되었습니다. 데이터 팩터리 NuGet 패키지를 업데이트하고 사용자 지정 작업의 소스 코드에서 ICustomActivity를 IDotNetActivity로 변경해야 합니다.  
	- 사용자 지정 작업에 대한 JSON 정의에서 사용자 지정 작업의 형식을 **CustomActivity**에서 **DotNetActivity**로 변경해야 합니다. 
	- **CustomActivity** 및 **CustomActivityProperties** 클래스 이름이 **DotNetActivity** 및 **DotNetActivityProperties**로 변경되었으며 포함된 속성 집합은 동일합니다.

		이전 버전의 SDK 및 Azure PowerShell을 사용하는 경우 DotNetActivity 대신 CustomActivity를 계속 사용할 수 있습니다.
    
  		사용자 지정 작업을 만들어 Azure Data Factory 파이프라인에서 사용하는 방법에 대한 연습은 [Azure Data Factory 파이프라인에서 사용자 지정 작업 사용][adf-custom-activities](영문)을 참조하세요.

[adf-azure-ml]: data-factory-create-predictive-pipelines.md
[adf-custom-activities]: data-factory-use-custom-activities.md
[adf-copy-activity]: data-factory-copy-activity.md
[adf-editor]: data-factory-editor.md
[adf-copy-advanced]: data-factory-copy-activity-advanced.md

[adf-editor-video]: http://channel9.msdn.com/Blogs/Windows-Azure/New-Azure-Data-Factory-Editor-UI
[adf-editor-blog]: http://azure.microsoft.com/blog/2015/03/02/azure-data-factory-editor-a-light-weight-web-editor/
[on-demand-hdi-parameters]: http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clustercreateparameters_properties.aspx
[adf-gateway-download]: http://www.microsoft.com/download/details.aspx?id=39717
[adf-github-samples]: https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON
[adf-msdn-linked-services]: https://msdn.microsoft.com/library/dn834986.aspx
[adf-encrypt-value-cmdlet]: https://msdn.microsoft.com/library/dn834940.aspx



 

<!---HONumber=July15_HO4-->