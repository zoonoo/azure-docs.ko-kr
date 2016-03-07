<properties 
	pageTitle="Azure Data Factory의 Azure 데이터 레이크 분석에서 U-SQL 스크립트 실행" 
	description="Azure 데이터 레이크 분석 계산 서비스에서 U-SQL 스크립트를 실행하여 데이터를 처리하는 방법에 대해 알아봅니다." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Azure Data Factory의 Azure 데이터 레이크 분석에서 U-SQL 스크립트 실행 
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서는 **Azure 데이터 레이크 분석** 계산 연결된 서비스에서 **U-SQL** 스크립트를 실행하는 **데이터 레이크 분석 U-SQL 작업**에 대해 설명합니다.

> [AZURE.NOTE] 
데이터 레이크 분석 U-SQL 작업이 포함된 파이프라인을 만들기 전에 Azure 데이터 레이크 분석 계정을 만들어야 합니다. Azure 데이터 레이크 분석에 대해 알아보려면 [Azure 데이터 레이크 분석 시작](../data-lake-analytics/data-lake-analytics-get-started-portal.md)을 참조하세요.
>  
> Data Factory, 연결된 서비스, 데이터 집합 및 파이프라인 만들기를 위한 자세한 단계는 [첫 파이프라인 빌드하기 자습서](data-factory-build-your-first-pipeline.md)를 검토하세요. Data Factory 편집기, Visual Studio 또는 Azure PowerShell에서 JSON 조각을 사용하여 Data Factory 엔터티를 만듭니다.

## Azure 데이터 레이크 분석 연결된 서비스
파이프라인에서 데이터 레이크 분석 U-SQL 작업을 사용하기 전에 **Azure 데이터 레이크 분석** 연결된 서비스를 만들어 Azure 데이터 레이크 분석 계산 서비스를 Azure Data Factory에 연결합니다.

다음 예제에서는 Azure 데이터 레이크 분석 연결된 서비스에 JSON 정의를 제공합니다.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


다음 표에는 JSON 정의에서 사용하는 JSON 속성이 나와 있습니다.

속성 | 설명 | 필수
-------- | ----------- | --------
형식 | type 속성은 **AzureDataLakeAnalytics**로 설정해야 합니다. | 예
accountName | Azure 데이터 레이크 분석 계정 이름입니다. | 예
dataLakeAnalyticsUri | Azure 데이터 레이크 분석 URI입니다. | 아니요 
권한 부여 | Data Factory 편집기에서 **권한 부여** 단추를 클릭하고 OAuth 로그인을 완료하면 인증 코드가 자동으로 검색됩니다. | 예 
subscriptionId | Azure 구독 ID | 아니요(지정하지 않으면 Data Factory의 구독이 사용됨). 
resourceGroupName | Azure 리소스 그룹 이름 | 아니요(지정하지 않으면 Data Factory의 리소스 그룹이 사용됨).
sessionId | OAuth 권한 부여 세션의 세션 ID입니다. 각 세션 ID는 고유하고 한 번만 사용될 수 있으며 Data Factory 편집기에서 자동으로 생성됩니다. | 예

**권한 부여** 단추를 사용하여 생성된 권한 부여 코드는 잠시 후 만료됩니다. 다양한 유형의 사용자 계정에 대한 만료 시간은 다음 표를 참조하세요. 인증 **토큰이 만료**되는 경우 다음과 같은 오류 메시지가 표시될 수 있습니다. "자격 증명 작업 오류: invalid\_grant - AADSTS70002: 자격 증명의 유효성 검사 오류 AADSTS70008: 제공된 액세스 권한 부여가 만료되었거나 해지됩니다. 추적 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 상관관계 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 타임스탬프: 2015-12-15 21:09:31Z

 
| 사용자 유형 | 다음 시간 후에 만료 |
| :-------- | :----------- | 
| Azure Active Directory에서 관리되지 않는 사용자(@hotmail.com, @live.com 등) | 12시간 |
| AAD(Azure Active Directory)에서 관리되는 사용자 | | 마지막 조각 실행 후 14일 동안 OAuth 기반 연결된 서비스 실행을 기반으로 하는 조각이 없는 경우 마지막 조각 실행일로부터 14일 후 <p>OAuth 기반 연결된 서비스를 기반으로 하는 조각이 14일마다 한 번 이상 실행된 경우 90일</p> |

이 오류를 방지/해결하려면 **토큰이 만료**되면 **권한 부여** 단추를 사용하여 다시 인증하고 연결된 서비스를 다시 배포해야 합니다. 다음 섹션의 코드를 사용하여 프로그래밍 방식으로 **sessionId** 및 **권한 부여** 속성의 값을 생성할 수도 있습니다.

  
### 프로그래밍 방식으로 sessionId와 권한 부여 값을 생성하려면 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

코드에 사용되는 데이터 팩터리 클래스에 대한 세부 정보는 [AzureDataLakeStoreLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) 및 [AuthorizationSessionGetResponse 클래스](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx) 항목을 참조하세요. WindowsFormsWebAuthenticationDialog 클래스의 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll에 대한 참조를 추가해야 합니다.
 
 
## 데이터 레이크 분석 U-SQL 작업 

다음 JSON 조각은 데이터 레이크 분석 U-SQL 작업이 포함된 파이프라인을 정의합니다. 작업 정의에 앞에서 만든 Azure 데이터 레이크 분석 연결된 서비스에 대한 참조가 있습니다.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


다음 표에는 이 작업과 관련된 속성 이름과 설명이 나와 있습니다.

속성 | 설명 | 필수
:-------- | :----------- | :--------
type | type 속성은 **DataLakeAnalyticsU-SQL**로 설정되어야 합니다. | 예
scriptPath | U-SQL 스크립트가 포함된 폴더 경로입니다. | 아니요(스크립트를 사용하는 경우)
scriptLinkedService | 스크립트가 포함된 저장소를 Data Factory에 연결하는 연결된 서비스입니다. | 아니요(스크립트를 사용하는 경우)
script | ScriptPath 및 scriptLinkedService를 지정하는 대신 인라인 스크립트를 지정합니다. 예: "script" : "CREATE DATABASE test" | 아니요(scriptPath 및 scriptLinkedService를 사용하는 경우)
degreeOfParallelism | 작업을 실행하는 데 동시에 사용되는 최대 노드 수입니다. | 아니요
우선 순위 | 대기열에 있는 모든 작업 중에서 먼저 실행해야 하는 작업을 결정합니다. 번호가 낮을수록 우선 순위가 높습니다. | 아니요 
매개 변수 | U-SQL 스크립트의 매개 변수 | 아니요 

스크립트 정의에 대해서는 [SearchLogProcessing.txt 스크립트 정의](#script-definition)를 참조하세요.

### 샘플 입력 및 출력 데이터 집합

#### 입력 데이터 집합
이 예제에서 입력 데이터는 Azure 데이터 레이크 저장소(datalake/input 폴더의 SearchLog.tsv 파일)에 있습니다.

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### 출력 데이터 집합
이 예제에서 U-SQL 스크립트에서 생성한 출력 데이터는 Azure 데이터 레이크 저장소(datalake/output 폴더)에 저장됩니다.

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Azure 데이터 레이크 저장소 연결된 서비스 샘플
다음은 위의 입/출력 데이터 집합에서 사용되는 Azure 데이터 레이크 저장소 연결된 서비스 샘플의 정의입니다.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

위의 Azure 데이터 레이크 저장소 연결된 서비스 및 데이터 집합 JSON 조각의 JSON 속성에 대한 설명을 보려면 [Azure 데이터 레이크 저장소에서 데이터 이동](data-factory-azure-datalake-connector.md)을 참조하세요.

### 스크립트 정의

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

위 U-SQL 스크립트의 **@in** 및 **@out** 매개 변수 값은 'parameters' 섹션을 사용하여 ADF를 통해 동적으로 전달됩니다. 파이프라인 정의 윗부분의 ‘parameters' 섹션을 참조하세요.

Azure 데이터 레이크 분석 서비스에서 실행되는 작업에 대한 파이프라인 정의뿐 아니라 다른 속성 viz. degreeOfParallelism, 우선 순위 등을 지정할 수 있습니다.

<!---HONumber=AcomDC_0224_2016-->