<properties 
	pageTitle="Azure Data Factory를 사용하여 데이터 복사" 
	description="Azure Data Factory에서 작업 복사를 사용하여 데이터 원본 간에 데이터를 복사하는 방법에 대해 알아봅니다." 
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
	ms.date="07/21/2015" 
	ms.author="spelluru"/>

# Azure Data Factory를 사용하여 데이터 복사(복사 작업)
## 개요
파이프라인에서 **복사 작업**을 사용하여 원본에서 배치의 싱크(대상)로 데이터를 복사할 수 있습니다. 복사 작업은 다음과 같은 시나리오에서 사용할 수 있습니다.

- **Azure에 수신**. 이 시나리오에서는 다음 하위 시나리오를 위해 데이터를 온-프레미스 데이터 원본(예: SQL Server)에서 Azure 데이터 저장소(예: Azure blob, Azure 테이블 또는 Azure SQL 데이터베이스)로 복사합니다.
	- 추가 처리를 위해 Azure의 중앙 위치에 데이터를 수집합니다.
	- 온-프레미스 또는 Azure가 아닌 클라우드 플랫폼의 데이터를 Azure로 마이그레이션합니다.
	- 데이터를 Azure에 보관하거나 백업하여 비용 효율적이고 계층화된 저장소를 만듭니다.
- **Azure에서 송신**. 이 시나리오에서는 다음 하위 시나리오를 위해 데이터를Azure(예: Azure blob, Azure 테이블 또는 Azure SQL 데이터베이스)에서 온-프레미스 데이터 마트 및 데이터 웨어하우스(예: SQL Server)로 복사합니다.
	- 클라우드 데이터 웨어하우스 지원 부족으로 인해 데이터를 온-프레미스로 전송합니다.
	- 데이터를 온-프레미스로 전송하여 기존 온-프레미스 솔루션 또는 보고 인프라를 활용합니다.
	- 데이터를 온-프레미스에 보관 또는 백업하여 계층화된 저장소를 만듭니다.
- **Azure 간 복사**. 이 시나리오에서는 Azure 데이터 원본 전체에 분산된 데이터를 중앙의 Azure 데이터 저장소에 집계합니다. 예: Azure 테이블-Azure Blob, Azure Blob-Azure 테이블, Azure 테이블-Azure SQL, Azure Blob-Azure SQL

자세한 내용은 다음을 참조하세요.

- [Azure Data Factory 복사 작업 소개][copy-activity-video] 비디오를 참조하세요.
- 복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 방법을 보여 주는 자습서는 [Azure Data Factory 시작][adfgetstarted]을 참조하세요. 
- 복사 작업을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 주는 연습은 [파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources]을 참조하세요.


## 지원되는 원본 및 싱크
복사 작업은 다음과 같은 데이터 이동 시나리오를 지원합니다.

| *원본/싱크* | Azure Blob | Azure 테이블 | Azure SQL 데이터베이스 | Azure DocumentDB | Azure VM에서 SQL Server | 온-프레미스 SQL Server |
| ------------- | ---------- | ----------- | ------------------ | ---------------- | ------------------ | ------------------- |
| Azure Blob | X | X | X | X | X | X |
| Azure 테이블 | X | X | X | X | X | X |
| Azure SQL 데이터베이스 | X | X | X | X | X | X |
| Azure DocumentDB | X | X | X | | | |  
| 온-프레미스 SQL Server | X | X | X | | X | X |
| Azure VM에서 SQL Server | X | X | X | | X | X |
| 온-프레미스 파일 시스템 | X | X | X | | X | X |
| 온-프레미스 Oracle 데이터베이스 | X | X | X | | X | X |
| 온-프레미스 MySQL 데이터베이스| X | X | X | | X | X |
| 온-프레미스 DB2 데이터베이스 | X | X | X | | X | X |
| 온-프레미스 Teradata 데이터베이스 | X | X | X | | X | X |
| 온-프레미스 Sybase 데이터베이스 | X | X | X | | X | X |
| 온-프레미스 PostgreSQL 데이터베이스 | X | X | X | | X | X |


자세한 내용은 MSDN 라이브러리에서 [지원되는 원본 및 싱크](https://msdn.microsoft.com/library/dn894007.aspx) 항목을 참조하십시오.

### IaaS(Infrastructure-as-a-Service)의 SQL
IaaS의 SQL Server도 원본 및 싱크로 지원됩니다. IaaS의 SQL Server에 대한 연결된 서비스를 만들 때는 데이터 관리 게이트웨이가 필요합니다. SQL 서버와 게이트웨이의 리소스 경합으로 인한 성능 저하를 방지하기 위해 SQL Server를 호스트하는 것과 다른 가상 컴퓨터에 데이터 관리 게이트웨이를 설치하는 것이 좋습니다. 데이터 관리 게이트웨이에 대한 자세한 내용은 [파이프라인에서 온-프레미스 데이터에 액세스할 수 있도록 설정][use-onpremises-datasources]을 참조하세요.

1.	공용 DNS 이름 및 고정 공용 포트 : 개인 포트 매핑을 사용하는 VM
2.	표시된 SQL 끝점 없이 공용 DNS 이름을 사용하는 VM
3.	가상 네트워크
	<ol type='a'>
<li>목록 끝에 다음과 같은 토폴로지가 있는 Azure 클라우드 VPN </li>	
<li>Azure 가상 네트워크를 사용하는 온-프레미스에서 클라우드까지 사이트 간 VPN을 포함하는 VM</li>	
</ol>![복사 작업을 사용하는 데이터 팩터리][image-data-factory-copy-actvity]

## 복사 작업 - 구성 요소
복사 작업에는 다음과 같은 구성 요소가 포함됩니다.

- **입력 테이블**. 테이블은 스키마가 있는 데이터 집합이며 사각형입니다. 입력 테이블 구성 요소는 테이블 이름, 테이블 유형 및 데이터 원본을 참조하는 연결된 서비스를 포함하는 작업의 입력 데이터를 설명하며, 입력 데이터를 포함합니다.
- **출력 테이블**. 출력 테이블은 테이블 이름, 테이블 유형 및 데이터 원본을 참조하는 연결된 서비스를 포함하는 작업의 출력 데이터를 설명하며, 출력 데이터를 포함합니다.
- **변환 규칙**. 변환 규칙은 원본에서 입력 데이터를 추출하는 방법 및 출력 데이터를 싱크 등으로 로드하는 방법을 지정합니다.
 
복사 작업에는 하나의 **입력 테이블**과 하나의 **출력 테이블**이 있을 수 있습니다.

## <a name="CopyActivityJSONSchema"></a>복사 작업에 대한 JSON
파이프라인은 하나 이상의 작업으로 구성됩니다. 파이프라인의 작업은 **activities ** 섹션에 정의합니다. 파이프라인에 대한 JSON은 다음과 같습니다.
         
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

| 태그 | 설명 | 필수 |
|-----|-------------|----------|
|name|작업의 이름입니다.|Y|
|description|작업이 무엇에 사용되는지 설명하는 텍스트입니다.|Y|
|type|작업의 유형을 지정합니다. type은 **Copy**로 설정해야 합니다. |Y|
|inputs|작업에 사용되는 입력 테이블입니다. 복사 작업의 입력 테이블은 하나만 지정합니다. | Y
|outputs|작업에 사용되는 출력 테이블입니다. 복사 작업의 출력 테이블은 하나만 지정합니다. | Y
|transformation|변환의 속성은 유형에 따라 다릅니다. 복사 작업에서는 transformation 섹션 내에 source 및 sink 섹션을 지정해야 합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다.|Y
|policy| 작업의 런타임 동작에 영향을 주는 정책입니다. 지정하지 않으면 기본값이 사용됩니다. | N

JSON 속성/태그에 대한 자세한 내용은 [JSON 스크립트 참조][json-script-reference]를 참조하세요.

### 원본 및 싱크 유형
원본 및 싱크 유형 목록과 이러한 유형에서 지원되는 속성은 MSDN 라이브러리에서 [지원되는 원본 및 싱크][msdn-supported-sources-sinks] 항목을 참조하세요.

## 복사 작업 - 예제
이 예제에서는 입력 테이블과 출력 테이블을 정의하고 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 파이프라인 내의 복사 작업에서 이 테이블을 사용합니다.

**가정** 다음에 나오는 샘플 JSON 스크립트에서는 다음과 같은 Azure Data Factory 아티팩트가 참조됩니다.

* **ADF**라는 리소스 그룹
* **CopyFactory**라는 Azure Data Factory
* 온-프레미스 SQL Server 데이터베이스를 가리키는 **MyOnPremisesSQLDB**라는 연결된 서비스
* Azure Blob 저장소를 가리키는 **MyAzureStorage**라는 연결된 서비스

### 입력 테이블 JSON
다음 JSON 스크립트는 **MyOnPremisesSQLDB** 연결 서비스에 정의된 온-프레미스 SQL Server 데이터베이스에서 **MyTable** SQL 테이블을 참조하는 입력 테이블을 정의합니다. **name**은 Azure Data Factory 테이블의 이름이고 **tableName**은 SQL Server 데이터베이스에 있는 SQL 테이블의 이름입니다.

         
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

다음 샘플 Azure PowerShell 명령은 위 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryTable**을 사용하여 Azure Data Factory **CopyFactory**에 테이블(**MyOnPremTable**)을 만듭니다.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF –Name MyOnPremTable –DataFactoryName CopyFactory –File <Filepath>\MyOnPremTable.json.

데이터 팩터리 cmdlet에 대한 자세한 내용은 [Cmdlet 참조][cmdlet-reference]를 참조하세요.

### 출력 테이블 JSON
다음 JSON 스크립트는 Blob 폴더 **MySubFolder** 및 Blob 컨테이너 **MyContainer**의 Azure Blob **MyBlob**을 참조하는 **MyDemoBlob**이라는 출력 테이블을 정의합니다.
         
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

다음 샘플 Azure PowerShell 명령은 위 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryTable**을 사용하여 Azure Data Factory **CopyFactory**에 테이블(**MyDemoBlob**)을 만듭니다.
         
	New-AzureDataFactoryTable -ResourceGroupName ADF -DataFactoryName CopyFactory –File <Filepath>


### 파이프라인(복사 작업 포함) JSON
이 예제에서는 **CopyActivityPipeline** 파이프라인을 다음 속성으로 정의합니다.

- **type** 속성을 **CopyActivity**로 설정합니다.
- **MyOnPremTable**을 입력으로 지정합니다(**inputs** 태그).
- **MyAzureBlob**을 출력으로 지정합니다(**outputs** 태그).
- **Transformation** 섹션에는 **source** 및 **sink**라는 두 개의 하위 섹션이 있습니다. 원본의 유형을 **SqlSource**로 설정하고 싱크의 유형을 **BlobSink**로 설정합니다. **sqlReaderQuery**는 원본에 대해 수행할 변환(프로젝션)을 정의합니다. 모든 속성에 대한 자세한 내용은[ JSON 스크립트 참조][json-script-reference]를 참조하세요.

         
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


 다음 샘플 Azure PowerShell 명령은 위 스크립트가 포함된 JSON 파일을 사용하는 **New-AzureDataFactoryPipeline**을 사용하여 Azure Data Factory **CopyFactory**에 파이프라인(**CopyActivityPipeline**)을 만듭니다.
         
		New-AzureDataFactoryPipeline -ResourceGroupName ADF –DataFactoryName CopyFactory –File <Filepath>

> [AZURE.NOTE]복사 활동을 사용하는 추가 예제는 [Azure 데이터 팩터리에서 복사 활동 사용 예][copy-activity-examples]를 참조하세요.

## 보안
이 섹션에서는 복사 작업을 위해 데이터 저장소에 대한 보안 액세스를 설정하는 데 도움이 되는 전반적인 보안 지침 및 모범 사례를 제공합니다.

HTTPS 연결을 제공하는 데이터 저장소의 경우 복사 작업에 대해 HTTPS 연결을 선택하여 네트워크를 통한 보안 통신을 설정합니다. 예를 들어 **Azure 저장소**의 경우 연결 문자열에서 **DefaultEndpointsProtocol=https**를 사용합니다.

**Azure SQL 데이터베이스**의 경우 “메시지 가로채기" 공격을 방지하려면 암호화된 연결을 명시적으로 요청하고 서버 인증서를 신뢰하지 않아야 합니다. 이렇게 하려면 연결 문자열에서 **Encrypt=True** 및 **TrustServerCertificate=False**를 사용합니다. 자세한 내용은 [Azure SQL 데이터베이스 보안 지침 및 제한 사항](https://msdn.microsoft.com/library/azure/ff394108.aspx)을 참조하세요.

**SQL Server**와 같은 기존 데이터베이스는 특히 인스턴스가 Azure 가상 컴퓨터에 있는 경우 연결 문자열에서 **Encrypt=True** 및 **TrustServerCertificate=False**를 사용해 서명된 인증서를 구성하여 암호화된 연결 옵션을 사용하도록 설정합니다. 자세한 내용은 [데이터베이스 엔진에 암호화된 연결 사용](https://msdn.microsoft.com/library/ms191192(v=sql.110)) 및 [연결 문자열 구문](https://msdn.microsoft.com/library/ms254500.aspx)을 참조하십시오.

## 고급 시나리오
- **구조 정의를 사용한 열 필터링**. 테이블 유형에 따라서는 기본 데이터 원본에 있는 열보다 적은 수의 열을 테이블 정의의 **Structure** 정의에 지정하여 원본에 있는 열의 하위 집합을 지정할 수 있습니다.
- **변환 규칙-열 매핑**. 열 매핑을 사용하여 원본 테이블의 열이 싱크 테이블의 열에 매핑되는 방법을 지정할 수 있습니다.
- **복사 작업에서 데이터 형식 처리**. 테이블 정의의 Structure 섹션에 지정된 데이터 형식이 적용/무시되는 경우를 설명합니다.
- **SQL 싱크에 대한 저장 프로시저 호출**. SQL Server 또는 Azure SQL 데이터베이스로 데이터를 복사할 때 사용자 지정 저장 프로시저를 구성하고 호출할 수 있습니다.

이러한 시나리오에 대한 자세한 내용은 [Azure 데이터 팩터리에서 복사 작업을 사용하는 고급 시나리오][copy-activity-advanced] 문서를 참조하세요.

## 연습
복사 작업을 사용하여 Azure Blob 저장소에서 Azure SQL 데이터베이스로 데이터를 복사하는 방법을 보여 주는 자습서는 [Azure 데이터 팩터리 시작][adfgetstarted]을 참조하세요.
 
복사 작업을 사용하여 온-프레미스 SQL Server 데이터베이스에서 Azure Blob 저장소로 데이터를 복사하는 방법을 보여 주는 연습은 [파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources]을 참조하세요.

## 참고 항목
- [복사 작업 - 예제][copy-activity-examples]
- [비디오: Azure 데이터 팩터리 복사 작업 소개][copy-activity-video]
- [MSDN 라이브러리의 복사 작업 항목][msdn-copy-activity]
- [Azure 데이터 팩터리에서 복사 작업을 사용하는 고급 시나리오][copy-activity-advanced]

[msdn-copy-activity]: https://msdn.microsoft.com/library/dn835035.aspx
[msdn-linkedservices]: https://msdn.microsoft.com/library/dn834986.aspx
[msdn-tables-topic]: https://msdn.microsoft.com/library/dn835002.aspx
[msdn-supported-sources-sinks]: https://msdn.microsoft.com/library/dn894007.aspx
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/
[table-valued-parameters]: http://msdn.microsoft.com/library/bb675163.aspx

[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[copy-activity-examples]: data-factory-copy-activity-examples.md

[copy-activity-advanced]: data-factory-copy-activity-advanced.md
[json-script-reference]: http://go.microsoft.com/fwlink/?LinkId=516971
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[image-data-factory-copy-actvity]: ./media/data-factory-copy-activity/VPNTopology.png
[image-data-factory-column-mapping-1]: ./media/data-factory-copy-activity/ColumnMappingSample1.png
[image-data-factory-column-mapping-2]: ./media/data-factory-copy-activity/ColumnMappingSample2.png
 

<!---HONumber=August15_HO6-->