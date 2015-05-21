<properties 
	pageTitle="Azure 데이터 팩터리를 사용하여 데이터 복사" 
	description="Azure 데이터 팩터리에서 작업 복사를 사용하여 데이터 원본 간에 데이터를 복사하는 방법에 대해 알아봅니다." 
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
	ms.date="01/08/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리를 사용하여 데이터 복사(복사 작업)
파이프라인에서 **복사 작업**을 사용하여 원본에서 배치의 싱크(대상)로 데이터를 복사할 수 있습니다. 복사 작업은 다음과 같은 시나리오에서 사용할 수 있습니다.

- **Azure에 수신**. 이 시나리오에서는 데이터를 온-프레미스 데이터 원본(예: SQL Server)에서 Azure 데이터 저장소(예: Azure Blob, Azure 테이블 또는 Azure SQL 데이터베이스)에 복사하여 다음과 같은 하위 시나리오에 사용합니다.
	- 추가 처리를 위해 Azure의 중앙 위치에 데이터를 수집합니다.
	- 온-프레미스 또는 Azure가 아닌 클라우드 플랫폼의 데이터를 Azure로 마이그레이션합니다.
	- 데이터를 Azure에 보관하거나 백업하여 비용 효율적이고 계층화된 저장소를 만듭니다.
- **Azure에서 송신**. 이 시나리오에서는 데이터를 Azure(예: Azure Blob, Azure 테이블 또는 Azure SQL 데이터베이스)에서 온-프레미스 데이터 마트 및 데이터 웨어하우스(예: SQL Server)로 복사하여 다음과 같은 하위 시나리오에 사용합니다.
	- 클라우드 데이터 웨어하우스 지원 부족으로 인해 데이터를 온-프레미스로 전송합니다.
	- 데이터를 온-프레미스로 전송하여 기존 온-프레미스 솔루션 또는 보고 인프라를 활용합니다.
	- 데이터를 온-프레미스에 보관 또는 백업하여 계층화된 저장소를 만듭니다.
- **Azure 간 복사**. 이 시나리오에서는 Azure 데이터 원본 전체에 분산된 데이터를 중앙의 Azure 데이터 저장소에 집계합니다. 예제: Azure 테이블-Azure Blob, Azure Blob-Azure 테이블, Azure 테이블-Azure SQL, Azure Blob-Azure SQL

복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 방법을 보여 주는 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted](영문)을 참조하세요. 복사 작업을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 주는 연습은 [파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문)을 참조하세요.


## 복사 작업 - 구성 요소
복사 작업에는 다음과 같은 구성 요소가 포함됩니다. 

- **입력 테이블**. 테이블은 스키마가 있는 데이터 집합이며 사각형입니다. 입력 테이블 구성 요소는 
- 테이블 이름, 테이블 유형 및 데이터 원본을 참조하는 연결된 서비스를 포함하는 작업의 입력 데이터를 설명하며, 입력 데이터를 포함합니다.
- **출력 테이블**. 출력 테이블은 테이블 이름, 테이블 유형 및 데이터 원본을 참조하는 연결된 서비스를 포함하는 작업의 출력 데이터를 설명하며, 출력 데이터를 포함합니다.
- **변환 규칙**. 변환 규칙은 원본에서 입력 데이터를 추출하는 방법 및 출력 데이터를 싱크 등으로 로드하는 방법을 지정합니다.
 
복사 작업에는 하나의 **입력 테이블**과 하나의 **출력 테이블**이 있을 수 있습니다.

## 복사 작업에 대한 JSON
파이프라인은 하나 이상의 작업으로 구성됩니다. 파이프라인의 작업은 **activities []** 섹션에 정의합니다. 파이프라인에 대한 JSON은 다음과 같습니다.
         
	{
		"name": "PipelineName",
		"properties": 
    	{
        	"description" : "pipeline description",
        	"activities":
        	[
	
    		]
		}
	}

**activities** 섹션 내에 있는 각 작업의 최상위 구조는 다음과 같습니다. **type** 속성은 **CopyActivity**로 설정해야 합니다. 복사 작업에는 하나의 입력 테이블과 하나의 출력 테이블만 있을 수 있습니다.
         

	{
		"name": "ActivityName",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [],
		"outputs":  [],
		"transformation":
		{

		},
		"policy":
		{
		
		}
	}

다음 표에서는 작업 섹션에서 사용되는 태그에 대해 설명합니다. 

<table border="1">	
	<tr>
		<th align="left">태그</th>
		<th align="left">설명</th>
		<th align="left">필수</th>
	</tr>	

	<tr>
		<td>name</td>
		<td>작업의 이름입니다.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>description</td>
		<td>작업이 무엇에 사용되는지 설명하는 텍스트입니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>type</td>
		<td>작업의 유형을 지정합니다. <br/><br/> <b>type</b> 을 <b>CopyActivity</b>로 설정해야 합니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>inputs</td>
		<td>작업에 사용되는 입력 테이블입니다.  복사 작업의 입력 테이블은 하나만 지정합니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>outputs</td>
		<td>작업에 사용되는 출력 테이블입니다.  복사 작업의 출력 테이블은 하나만 지정합니다.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>transformation</td>
		<td>변환의 속성은 유형에 따라 다릅니다.   <b>복사 작업</b> 을 사용하려면 <b>원본</b> 및 <b>싱크</b> 섹션을 <b>transformation</b> 섹션 내에 지정해야 합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. </td>
		<td>Y</td>
	</tr>

	<tr>
		<td>policy</td>
		<td>작업의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본값이 사용됩니다.</td>
		<td>N</td>
	</tr>


</table>

JSON 속성/태그에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference](영문)를 참조하세요.

## 복사 작업 - 예제
이 예제에서는 입력 테이블과 출력 테이블을 정의하고 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 파이프라인 내의 복사 작업에서 이 테이블을 사용합니다.

**가정**
다음에 나오는 샘플 JSON 스크립트에서는 다음과 같은 Azure 데이터 팩터리 아티팩트가 참조됩니다.

* **ADF**라는 리소스 그룹
* **CopyFactory**라는 Azure 데이터 팩터리
* 온-프레미스 SQL Server 데이터베이스를 가리키는 **MyOnPremisesSQLDB**라는 연결된 서비스
* Azure Blob 저장소를 가리키는 **MyAzureStorage**라는 연결된 서비스

### 입력 테이블 JSON
다음 JSON 스크립트는 ****MyOnPremisesSQLDB** 연결된 서비스에서 정의하는 온-프레미스 SQL Server 데이터베이스의 SQL 테이블 MyTable**을 참조하는 입력 테이블을 정의합니다. **name**은 Azure 데이터 팩터리 테이블의 이름이고 **tableName**은 SQL Server 데이터베이스에 있는 SQL 테이블의 이름입니다.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

다음 샘플 Azure PowerShell 명령은 위의 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryTable**을 사용하여 테이블(**MyOnPremTable**)을 Azure 데이터 팩터리 **CopyFactory**에 만듭니다.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -Name MyOnPremTable -DataFactoryName CopyFactory -File <Filepath>\MyOnPremTable.json.

데이터 팩터리 cmdlet에 대한 자세한 내용은 [Cmdle 참조][cmdlet-reference]를 참조하세요. 

### 출력 테이블 JSON
다음 JSON 스크립트는 출력 테이블 **MyDemoBlob**을 정의합니다. 이 테이블은 Azure Blob **MyBlob**을 참조하며, 이 Blob은 Blob 폴더 **MySubFolder**에 있고, 이 폴더는 Blob 컨테이너 **MyContainer**에 있습니다.
         
	{
   		"name": "MyDemoBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

다음 샘플 Azure PowerShell 명령은 위의 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryTable**을 사용하여 테이블(**MyDemoBlob**)을 Azure 데이터 팩터리 **CopyFactory**에 만듭니다.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>


### 파이프라인(복사 작업 포함) JSON
이 예제에서는 파이프라인 **CopyActivityPipeline**을 다음 속성을 사용하여 정의합니다. 

- **type** 속성을 **CopyActivity**로 설정합니다.
- **MyOnPremTable**을 입력으로 지정합니다(**inputs** 태그).
- **MyAzureBlob**을 출력으로 지정합니다(**outputs** 태그).
- **Transformation** 섹션에는 두 하위 섹션 **원본** 및 **싱크**가 포함됩니다. 원본의 유형을 **SqlSource**로 설정하고 싱크의 유형을 **BlobSink**로 설정합니다. **sqlReaderQuery**는 원본에 대해 수행할 변환(프로젝션)을 정의합니다. 모든 속성에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference](영문)를 참조하세요.

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
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
        		]
    		}
		}


 다음 샘플 Azure PowerShell 명령은 위의 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryPipeline**을 사용하여 파이프라인(**CopyActivityPipeline**)을 Azure 데이터 팩터리 **CopyFactory**에 만듭니다.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName CopyFactory -File <Filepath>

## 지원되는 입력 및 출력
위의 예제에서는 transformation 섹션에서 SqlSource를 원본으로, BlobSink를 싱크로 사용했습니다. 다음 표에는 복사 작업에서 지원하는 원본 및 싱크가 나와 있습니다. 

<table border="1">	
	<tr>
		<th><i>싱크/원본<i></th>
		<th>Azure Blob</th>
		<th>Azure 테이블</th>
		<th>Azure SQL 데이터베이스</th>
		<th>온-프레미스 SQL Server</th>
		<th>IaaS의 SQL Server</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
	</tr>

	<tr>
		<td><b>Azure 테이블</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>	

	<tr>
		<td><b>Azure SQL 데이터베이스</b></td>
		<td>X</td>
		<td>X</td>
		<td>X</td>
		<td></td>
		<td></td>
	</tr>


	<tr>
		<td><b>온-프레미스 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

	<tr>
		<td><b>IaaS의 SQL Server</b></td>
		<td>X</td>
		<td></td>
		<td></td>
		<td></td>
		<td></td>
	</tr>

</table>


다음 표에는 복사 작업을 포함하는 파이프라인에 대한 JSON 파일에서 사용할 수 있는 원본 유형과 싱크 유형이 나와 있습니다.


<table border="1">	
	<tr>
		<th></th>
		<th align="left">원본 유형</th>
		<th align="left">싱크 유형</th>
	</tr>	

	<tr>
		<td><b>Azure Blob</b></td>
		<td>BlobSource</td>
		<td>BlobSink</td>
	</tr>

	<tr>
		<td><b>Azure 테이블</b></td>
		<td>AzureTableSource</td>
		<td>AzureTableSink</td>
	</tr>

	<tr>
		<td><b>Azure SQL 데이터베이스</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>온-프레미스 SQL Server</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>

	<tr>
		<td><b>IaaS의 SQL</b></td>
		<td>SqlSource</td>
		<td>SqlSink</td>
	</tr>
</table>

다음 표에는 이러한 원본 및 싱크에서 지원하는 속성이 나와 있습니다.

<table border="1">

	<tr>
	<th align="left">원본/싱크</th>
	<th align="left">지원되는 속성</th>
	<th align="left">설명</th>
	<th align="left">허용되는 값</th>
	<th align="left">필수</th>
	</tr>

	<tr>
		<tr>
		<td><b>BlobSource</b></td>
		<td>BlobSourceTreatEmptyAsNull</td>
		<td>Null 또는 빈 문자열을 null 값으로 처리할지 여부를 지정합니다.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
		</tr>
	</tr>

	<tr>
		<td></td>
		<td>BlobSourceSkipHeaderLineCount</td>
		<td>건너뛰어야 하는 줄 수를 나타냅니다.</td>
		<td>0에서 Max 사이의 정수입니다.</td>
		<td>N</td>
	</tr>

	<tr>
		<td><b>AzureTableSource</b></td>
		<td>AzureTableSourceQuery</td>
		<td>사용자 지정 쿼리를 사용하여 데이터를 읽습니다.</td>
		<td>Azure 테이블 쿼리 문자열.<br/>샘플: "ColumnA eq ValueA"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>AzureTableSourceIgnoreTableNotFound</td>
		<td>"테이블이 없습니다" 예외를 무시할지 여부를 나타냅니다.</td>
		<td>TRUE<br/>FALSE</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>SqlSource</b></td>
		<td>sqlReaderQuery</td>
		<td>사용자 지정 쿼리를 사용하여 데이터를 읽습니다.</td>
		<td>SQL 쿼리 문자열.<br/><br/> 예제: "Select * from MyTable".<br/><br/> 지정하지 않은 경우 <columns defined in structure> from MyTable 쿼리를 선택합니다.</td>
		<td>N</td>
	</tr>


	<tr>
		<td><b>AzureTableSink</b></td>
		<td>azureTableDefaultPartitionKeyValue</td>
		<td>싱크에서 사용할 수 있는 기본 파티션 키 값입니다.</td>
		<td>문자열 값</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTablePartitionKeyName</td>
		<td>사용자 지정 열 이름으로, 해당 열 값이 파티션 키로 사용됩니다.<br/><br/> 지정하지 않으면 AzureTableDefaultPartitionKeyValue가 파티션 키로 사용됩니다.</td>
		<td>열 이름</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableRowKeyName</td>
		<td>행 키로 사용할 지정된 열 이름의 열 값입니다.<br/><br/>지정하지 않으면 각 행에 GUID가 사용됩니다.</td>
		<td>열 이름</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>azureTableInsertType</td>
		<td>Azure 테이블에 데이터를 삽입하는 모드입니다.</td>
		<td>"merge"<br/><br/>"replace"</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchSize</td>
		<td>WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다.</td>
		<td>1에서 100 사이의 정수(단위 = 행 수)</td>
		<td>N<br/><br/>(기본값 = 100)</td>
	</tr>

	<tr>
		<td></td>
		<td>writeBatchTimeout</td>
		<td>WriteBatchSize 또는 writeBatchTimeout에 도달하면 Azure 테이블에 데이터를 삽입합니다.</td>
		<td>(단위 = timespan)<br/><br/>샘플: "00:20:00"(20분)</td>
		<td>N<br/><br/>(저장소 클라이언트 기본 시간 제한 값 90초로 기본 설정됨)</td>
	</tr>

	<tr>
		<td><b>SqlSink</b></td>
		<td>SqlWriterStoredProcedureName</td>
		<td>대상 테이블에 데이터를 upsert(업데이트/삽입)하는 저장 프로시저의 이름을 지정합니다.</td>
		<td>저장 프로시저 이름</td>
		<td>N</td>
	</tr>

	<tr>
		<td></td>
		<td>SqlWriterTableType</td>
		<td>위의 저장 프로시저에서 사용할 테이블 유형을 지정합니다.</td>
		<td>테이블 유형 이름</td>
		<td>N</td>
	</tr>
</table>

### IaaS(Infrastructure-as-a-Service)의 SQL
IaaS에 있는 SQL의 경우 Azure를 IaaS 공급자로 사용할 수 있습니다. 다음과 같은 네트워크 및 VPN 토폴로지가 지원됩니다. 사례 #2 및 #3에는 데이터 관리 게이트웨이가 필요하지만 사례 #1에는 필요하지 않습니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [파이프라인에서 온-프레미스 데이터에 액세스할 수 있도록 설정][use-onpremises-datasources](영문)을 참조하세요.

1.	공용 DNS 이름 및 고정 공용 포트 : 개인 포트 매핑을 사용하는 VM
2.	표시된 SQL 끝점 없이 공용 DNS 이름을 사용하는 VM
3.	가상 네트워크
	<ol type='a'>
	<li>Azure Cloud VPN with following topology at the end of the list. </li>	
	<li>VM with onpremises-to-cloud site-to-site VPN using Azure Virtual Network.</li>	
	</ol>  
	![Data Factory with Copy Activity][image-data-factory-copy-actvity]

## Column filtering using structure definition
Depending on the type of Table, it is possible to specify a subset of the columns from the source by specifying fewer columns in the **Structure** definition of the table definition than the ones that exist in the underlying data source. The following table provides information about column filtering logic for different types of table. 

<table>

	<tr>
		<th align="left">테이블 유형</th>
		<th align="left">열 필터링 논리</th>
	<tr>

	<tr>
		<td>AzureBlobLocation</td>
		<td>JSON 테이블의 <b>구조</b> 정의가 Blob의 구조와 일치해야 합니다.  열의 하위 집합을 선택하려면 다음 섹션: 변환 규칙 - 열 매핑에 설명된 열 매핑 기능을 사용합니다.</td>
	<tr>

	<tr>
		<td>AzureSqlTableLocation 및 OnPremisesSqlServerTableLocation</td>
		<td align="left">
			<p>속성 <b>SqlReaderQuery</b> 가 복사 작업 정의의 일부로 지정된 경우 테이블의 <b>구조</b> 정의를 쿼리에서 선택한 열에 맞춰야 합니다.</p>
			<p>속성 <b>SqlReaderQuery</b> 를 지정하지 않으면 복사 작업이 테이블 정의의 <b>구조</b> 정의에 지정된 열을 기반으로 SELECT 쿼리를 자동으로 생성합니다.</p>
		</td>
	<tr>

	<tr>
		<td>AzureTableLocation</td>
		<td>
			테이블 정의의 <b>구조</b> 섹션은 기본 Azure 테이블에 있는 열의 전체 집합 또는 하위 집합을 포함할 수 있습니다.
		</td>
	<tr>

</table> 

## 변환 규칙 - 열 매핑
열 매핑을 사용하여 원본 테이블의 열이 싱크 테이블의 열에 매핑되는 방법을 지정할 수 있습니다. 다음과 같은 시나리오가 지원됩니다.

- 원본 테이블 "structure"에 이 있는 모든 열을 대상 테이블 "structure"에 매핑합니다.
- 원본 테이블 "structure"에 있는 열의 하위 집합이 모든 대상 테이블 "structure"에 매핑됩니다.

다음은 지원되지 않으면 예외가 throw됩니다. 

- 대상에 열이 더 적거나 많음
- 중복 매핑
- SQL 쿼리 결과에 열 이름 없음

#### 샘플 1 - SQL Server에서 Azure Blob으로의 열 매핑
이 샘플에서는 **입력 테이블**을 다음과 같이 정의합니다. 입력 테이블에는 구조가 있으며 구조에서는 SQL Server 데이터베이스에 있는 SQL 테이블을 가리킵니다.
         
		{
		    "name": "MyOnPremTable",
    		"properties":
    		{
				"structure": 
				[
            		{ "name": "userid", "type": "String"},
            		{ "name": "name", "type": "String"},
            		{ "name": "group", "type": "Decimal"}
				],
				"location":
				{
					"type": "OnPremisesSqlServerTableLocation",
					"tableName": "MyTable",
					"linkedServiceName": "MyOnPremisesSQLDB"
				},
				"availability":	
				{
    				"frequency": "Hour",
    				"interval": 1
				}
     		}
		}

이 샘플에서는 **출력 테이블**을 다음과 같이 정의합니다. 출력 테이블에는 구조가 있으며 구조에서는 Azure Blob 저장소에 있는 Blob을 가리킵니다.
         
		
	{
		"name": "MyDemoBlob",
		"properties":
		{
    		"structure":
			[
        	    { "name": "myuserid", "type": "String"},
        	    { "name": "mygroup", "type": "String"},
        	    { "name": "myname", "type": "Decimal"}
			],
			"location":
    		{
    	    	"type": "AzureBlobLocation",
		        "folderPath": "MyContainer/MySubFolder",
				"fileName": "MyBlobName",
    	    	"linkedServiceName": "MyLinkedService",
    	    	"format":
    	    	{
    	        	"type": "TextFormat",
		            "columnDelimiter": ",",
    		        "rowDelimiter": ";",
    		        "EscapeChar": "$",
    		        "NullValue": "NaN"
    		    }
			},
			"availability":
			{
    			"frequency": "Hour",
    			"interval": 1
			}
		}
	}	

**입력 테이블**의 **fileName**을 지정하지 않는 경우 입력 폴더(**folderPath**)의 모든 파일/Blob이 입력으로 간주됩니다. JSON에서 fileName을 지정하는 경우에는 지정한 파일/Blob만 입력으로 간주됩니다. 예제는 [자습서][adf-tutorial]의 샘플 파일을 참조하세요.

**출력 테이블**의 **fileName**을 지정하지 않는 경우 **folderPath**에 생성되는 파일은 Data.<GUID>.txt 형식으로 이름이 지정됩니다(예: : Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt.).

**SliceStart** 시간을 기반으로 **folderPath** 및 **fileName**을 동적으로 설정하려면 **partitionedBy** 속성을 사용합니다. 다음 예제에서 **folderPath**는 SliceStart(처리 중인 조각의 시작 시간)의 Year, Month 및 Day를 사용하고 fileName은 SliceStart의 Hour를 사용합니다. 예를 들어 조각이 2014-10-20T08:00:00에 생성되는 경우 folderName은 wikidatagateway/wikisampledataout/2014/10/20으로 설정되고 fileName은 08.csv로 설정됩니다. 

  	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy": 
    [
    	{ "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
    ],

이 샘플에서는 파이프라인의 작업을 다음과 같이 정의합니다. 원본의 열과 싱크(**columnMappings**)의 열 간 매핑은 **Translator** 속성을 사용하여 이루어집니다.

##### 샘플 - 열 매핑 정의

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "MyOnPremTable"  } ],
		"outputs":  [ { "name": "MyDemoBlob" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource"
    		},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup, Name: MyName"
    		}
		}
	}

![Column Mapping][image-data-factory-column-mapping-1]

##### 예제 2 - SQL 쿼리를 사용하여 SQL Server에서 Azure Blob으로 열 매핑
이 샘플에서는 이전 샘플의 테이블이 아니라 SQL 쿼리를 사용하여 온-프레미스 SQL Server에서 데이터를 추출하며 쿼리 결과의 열이 원본 아티팩트에 매핑된 다음 대상 아티팩트 매핑됩니다. 이 샘플의 목적에 맞게 쿼리에서는 5개 열을 반환합니다.

	{
		"name": "CopyActivity",
		"description": "description", 
		"type": "CopyActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"transformation":
		{
			"source":
			{
				"type": "SqlSource",
				"SqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartDateTime = \\'{0:yyyyMMdd-HH}\\'', Time.AddHours(SliceStart, 0))"
			},
			"sink":
			{
            	"type": "BlobSink"
			},
			"Translator": 
			{
      			"type": "TabularTranslator",
      			"ColumnMappings": "UserId: MyUserId, Group: MyGroup,Name: MyName"
    		}
		}
	}

![Column Mapping 2][image-data-factory-column-mapping-2]

#### 복사 작업에서 데이터 형식 처리

테이블 정의의 Structure 섹션에 지정된 데이터 형식은 **BlobSource**에 대해서만 적용됩니다.  아래 표에서는 다른 유형의 원본 및 싱크에 대해 데이터 형식이 어떻게 처리되는지 설명합니다.

<table>	
	<tr>
		<th align="left">원본/싱크</th>
		<th align="left">데이터 형식 처리 논리</th>
	</tr>	

	<tr>
		<td>SqlSource</td>
		<td>테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.  기본 SQL 데이터베이스에 정의된 데이터 형식은 복사 작업 중에 데이터 추출에 사용됩니다.</td>
	</tr>

	<tr>
		<td>SqlSink</td>
		<td>테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.  기본 원본 및 대상의 데이터 형식이 비교되며, 형식 불일치가 있는 경우 암시적 형식 변환이 수행됩니다.</td>
	</tr>

	<tr>
		<td>BlobSource</td>
		<td><p> <b>BlobSource</b> 에서 <b>BlobSink</b>로 전송하는 경우 형식 변환이 없습니다. 테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.   <b>BlobSink</b>이외의 대상의 경우 테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식이 적용됩니다.</p>
		<p>
		테이블 정의에  <b>구조</b> 가 지정되지 않은 경우 형식 처리는 <b></b>  <b>BlobSink</b>의 format 속성에 따라 다릅니다.
		</p>
		<ul>
			<li> <b>TextFormat:</b> 모든 열 형식이 문자열로 처리되고 모든 열 이름이 "Prop_<0-N>"으로 설정됩니다.</li> 
			<li><b>AvroFormat:</b> Avro 파일에 기본적으로 제공되는 열 형식 및 이름을 사용합니다.</li> 
			<li><b>JsonFormat:</b> 모든 열 형식이 문자열로 처리되고 Json 파일에 기본적으로 적용되는 열 이름을 사용합니다.</li>
		</ul>
		</td>
	</tr>

	<tr>
		<td>BlobSink</td>
		<td>입력 테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.  기본 입력 데이터 저장소에 정의된 데이터 형식이 사용됩니다.  Avro 직렬화에 대해서는 열이 Null 허용으로 지정됩니다.</td>
	</tr>

	<tr>
		<td>AzureTableSource</td>
		<td>테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.  기본 Azure 테이블에 정의된 데이터 형식이 사용됩니다.</td>
	</tr>

	<tr>
		<td>AzureTableSink</td>
		<td>테이블 정의의 <b>구조</b> 섹션에 정의된 데이터 형식은 무시됩니다.  기본 입력 데이터 저장소에 정의된 데이터 형식이 사용됩니다.</td>
	</tr>

</table>


## 연습
복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 방법을 보여 주는 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted](영문)을 참조하세요.
 
복사 작업을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 주는 연습은 [파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문)을 참조하세요.



[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
