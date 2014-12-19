<properties title="Use Pig and Hive with Azure Data Factory" pageTitle="Azure 데이터 팩터리에서 Pig 및 Hive 사용" description="Learn how to process data by running Pig and Hive scripts on an Azure HDInsight cluster from an Azure data factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Azure 데이터 팩터리에서 Pig 및 Hive 사용
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는  특정 처리 작업을 수행합니다. 예를 들어 복사 작업은 원본 저장소에서 대상 저장소로 데이터를 복사하고 Hive/Pig 변환이 포함된 HDInsight 작업은 Azure HDInsight 클러스터를 사용하여 Hive/Pig 스크립트로 데이터를 처리합니다. HDInsight 작업은 하나 이상의 입력을 사용하고 하나 이상의 출력을 생성할 수 있습니다. 

## 이 문서에서는 다음을 수행합니다.

섹션 | 설명
------- | -----------
[Pig JSON 예제](#PigJSON) | 이 섹션에서는 Pig 변환을 사용하는 HDInsight 작업을 정의하는 JSON 스키마를 제공합니다. 
[Hive JSON 예제](#HiveJSON) | 이 섹션에서는 Hive 변환을 사용하는 HDInsight 작업을 정의하는 JSON 스키마를 제공합니다. 
[Azure Blob 저장소에 저장된 Pig 및 Hive 스크립트 사용](#ScriptInBlob) | Pig/Hive 변환을 사용하는 HDInsight 작업에서 Azure Blob 저장소에 저장된 Pig/Hive 스크립트를 참조하는 방법을 설명합니다.
[매개 변수가 지정된 Pig 및 Hive 쿼리](#ParameterizeQueries) | Pig 및 Hive 스크립트에서 사용되는 매개 변수의 값을 JSON에서 **extendedProperties** 속성을 사용하여 지정하는 방법에 대해 설명합니다.
[연습: Azure 데이터 팩터리에서 Hive 사용](#Waltkthrough) | Hive를 사용하여 데이터를 처리하는 파이프라인을 만드는 단계별 지침을 제공합니다.  



파이프라인 JSON에서 Pig 또는 Hive 작업을 정의할 때는 **type** 속성을 **HDInsightActivity**로 설정해야 합니다.

## <a name="PigJSON"></a> Pig JSON 예제

    {
		"name": "Pig Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Pig",
			"script": "pig script",
			"extendedProperties":
			{	
				"param1": "param1Value"
 			}
		}
	}

**다음 사항에 유의하세요.**
	
- 작업 **type**을 **HDInsightActivity**로 설정합니다.
- **linkedServiceName**을 **MyHDInsightLinkedService**로 설정합니다. 
- transformation**의 **type**을 ****Pig**로 설정합니다.
- Pig 스크립트를 **script** 속성에 인라인으로 지정할 수도 있고 스크립트 파일을 Azure Blob 저장소에 저장하고 이 문서 뒷부분에서 설명하는 **scriptPath** 속성을 사용하여 파일을 참조할 수도 있습니다. 
- Pig 스크립트에 대한 매개 변수는 **extendedProperties**를 사용하여 지정합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 


## <a name="HiveJSON"></a> ## Hive JSON 예제


    {
		"name": "Hive Activity",
		"description": "description", 
		"type": "HDInsightActivity",
		"inputs":  [ { "name": "InputSqlDA"  } ],
		"outputs":  [ { "name": "OutputBlobDA" } ],
		"linkedServiceName": "MyHDInsightLinkedService",
		"transformation":
		{
			"type": "Hive",
			"script": "Hive script",
			"extendedProperties":
			{	
				"param1": "param1Value"
            }
		}
	}

**다음 사항에 유의하세요.**
	
- 작업 **type**을 **HDInsightActivity**로 설정합니다.
- **linkedServiceName**을 **MyHDInsightLinkedService**로 설정합니다. 
- transformation**의 **type**을 ****Hive**로 설정합니다.
- Hive 스크립트를 **script** 속성에 인라인으로 지정할 수도 있고 스크립트 파일을 Azure Blob 저장소에 저장하고 이 문서 뒷부분에서 설명하는 **scriptPath** 속성을 사용하여 파일을 참조할 수도 있습니다. 
- Hive 스크립트에 대한 매개 변수는 **extendedProperties**를 사용하여 지정합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 

> [WACOM.NOTE] Cmdlet, JSON 스키마 및 스키마의 속성에 대한 자세한 내용은 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908)(영문)를 참조하세요. 


## <a name="ScriptInBlob"></a>Azure Blob 저장소에 저장된 Pig 및 Hive 스크립트 사용
HDInsight 클러스터와 연결된 Azure Blob 저장소에 Pig/Hive 스크립트를 저장하고 JSON에 다음 속성을 사용하여 Pig/Hive 작업에서 이 스크립트를 참조할 수 있습니다. 

* **scriptPath** - Pig 또는 Hive 스크립트 파일의 경로
* **scriptLinkedService** - 스크립트 파일을 포함하는 Azure 저장소 계정

샘플 파이프라인에 대한 다음 JSON 예제에서는 **StorageLinkedService**가 나타내는 Azure Blob 저장소의 **adfwalkthrough** 컨테이너에 있는 **scripts** 폴더에 저장된 **transformdata.hql** 파일을 참조하는 Hive 작업을 사용합니다.

    {
    	"name": "AnalyzeMarketingCampaignPipeline",
    	"properties":
    	{
	        "description" : " Enriched Gamer Fact Data and push to SQL Azure",
    	    "activities":
    	    [
    	        {
					"name": "JoinData",
					"description": "Join Regional Campaign data with Enriched Gamer Fact Data",
					"type": "HDInsightActivity",
					"inputs": [ {"name": "EnrichedGameEventsTable"}, 
                            {"name": "RefMarketingCampaignTable"} ],
					"outputs": [ {"name": "MarketingCampaignEffectivenessBlobTable"} ],
					"linkedServiceName": "MyHDInsightLinkedService",
					"transformation":
					{
    					"type": "Hive",
    					"scriptpath": "adfwalkthrough\\scripts\\transformdata.hql",    		
						"scriptLinkedService": "StorageLinkedService", 
						"extendedProperties":
						{
						}		
					},
					"policy":
					{
						"concurrency": 1,
						"executionPriorityOrder": "NewestFirst",
						"retry": 1,
						"timeout": "01:00:00"
					}
            	}
        	]
      	}
	}

  

> [WACOM.NOTE] Cmdlet, JSON 스키마 및 스키마의 속성에 대한 자세한 내용은 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908)(영문)를 참조하세요.

## <a name="ParameterizeQueries"></a>매개 변수가 지정된 Pig 및 Hive 쿼리
데이터 팩터리 Pig 및 Hive 작업을 사용하면 Pig 및 Hive 스크립트에 사용되는 매개 변수의 값을 **extendedProperties**를 사용하여 지정할 수 있습니다. extendedProperties 섹션은 매개 변수 이름과 매개 변수 값으로 구성됩니다.

Hive 스크립트의 매개 변수를 **extendedProperties**를 사용하여 지정하는 다음 예제를 참조하세요. 매개 변수가 있는 Hive  스크립트를 사용하려면 다음을 수행합니다.

1.	**extendedProperties**에 매개 변수를 정의합니다.
2.	인라인 Hive 스크립트 또는 Blob 저장소에 저장된 Hive 스크립트에서 **${hiveconf:parameterName}**을 사용하여 매개 변수를 참조합니다.

   
    		
    	{
			"name": "ParameterizedHivePipeline",
			"properties": 
			{
	    		"description" : "Example - Parameterized Hive Pipeline",
		   	 "activities": 
				[
					{
						"name": "ProcessLog",
					  	"type": "HDInsightActivity",
					  	"inputs": [{"Name": "DA_Input"}],
						"outputs": [{"Name": "DA_Output1"}, {"Name": "DA_Output2"}],
				  		"linkedServiceName": "MyHDInsightLinkedService",
				  		"transformation":
				  		{
							"type": "Hive", 
							"extendedProperties":
							{
								"Param1": "$$Text.Format('{0:yyyy-MM-dd}', SliceStart)",
								"Param2": "value"
						  	},
    						"script": "ADD FILE ${hiveconf:Param1}://${hiveconf:Param2}/MyFile.DLL;"
    					}
					}
			   	]
			}
		}


-  

## <a name="Walkthrough"></a>연습: Azure 데이터 팩터리에서 Hive 사용
### 필수 구성 요소
1. [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 완료합니다.
2. 위 자습서에서 만든 **emp.txt** 파일을 Blob 스토리지의 adftutorial 컨테이너에 **hiveinput\emp.txt**로 업로드합니다. 이 구문을 사용하여 emp.txt 파일을 업로드하면 **hiveinput** 폴더가 **adftutorial** 컨테이너에 자동으로 생성됩니다. 
2. **C:\ADFGetStarted** 아래의 **Hive**라는 하위 폴더에 **hivequery.hql** 파일을 만들고 다음 내용을 입력합니다.
    		
    	DROP TABLE IF EXISTS adftutorialhivetable; 
		CREATE EXTERNAL TABLE  adftutorialhivetable
		(                                  
 			country         string,                                   
 			state           string,   
 			sessioncount int                                 
		) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:RESULTOUTPUT}/${hiveconf:Year}/${hiveconf:Month}/${hiveconf:Day}'; 

		INSERT OVERWRITE TABLE adftutorialhivetable 
		SELECT  country, state, count(*) 
		FROM hivesampletable 
		group by country, state;
		
3.  **hivequery.hql**을 Blob 저장소의 **adftutorial** 컨테이너로 업로드합니다.


### 연습

#### 입력 테이블 만들기
1. 다음과 같은 내용의 JSON 파일 **HiveInputBlobTable.json**을 **C:\ADFGetStarted\Hive** 폴더에 만듭니다.
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**다음 사항에 유의하세요.**
	
	- location **type**은 **AzureBlobLocation**으로 설정합니다.
	- **linkedServiceName**을 Azure 저장소 계정을 정의하는 **MyBlobStore**로 설정합니다.
	- **folderPath**는 입력 데이터의 Blob 컨테이너\폴더를 지정합니다. 
	- **frequency=Day**와 **interval=1**을 지정하면 조각이 매일 제공됩니다.
	- **waitOnExternal**을 지정하면 이 데이터가 다른 파이프라인에서 생성되지 않고 대신 데이터 팩터리 외부에서 생성됩니다. 
	

	JSON 속성에 대한 설명은 [데이터 팩터리 개발자 참조][developer-reference](영문)를 참조하세요.  

2. **Azure PowerShell**을 시작하고 필요한 경우 **AzureResourceManager** 모드로 전환합니다.
    		
    	Switch-AzureMode AzureResourceManager

5. 폴더 **C:\ADFGetStarted\Hive**로 전환합니다.
6. 다음 명령을 실행하여 **ADFTutorialDataFactory**에 입력 테이블을 만듭니다.

		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveInputBlobTable.json

	데이터 팩터리 cmdlet에 대한 자세한 개요는 [데이터 팩터리 Cmdlet 참조][cmdlet-reference](영문)를 참조하세요. 
#### 출력 테이블 만들기
        
1. 다음과 같은 내용으로 **HiveOutputBlobTable.json**이라는 JSON 파일을 만들어 **C:\ADFGetStarted\Hive** 폴더에 저장합니다.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 다음 명령을 실행하여 **ADFTutorialDataFactory**에 출력 파일을 만듭니다.
 
		New-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HiveOutputBlobTable.json

### HDInsight 클러스터에 대한 연결된 서비스 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다. 

#### 주문형 HDInsight 클러스터를 사용하려면
1. 다음과 같은 내용으로 JSON 파일 **HDInsightOnDemandCluster.json**을 만들어 **C:\ADFGetStarted\Hive** 폴더에 저장합니다.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": "4",
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Hive** 폴더로 전환합니다.
4. 다음 명령을 실행하여 주문형 HDInsight 클러스터에 대한 연결된 서비스를 만듭니다.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
3. 테이블 및 연결된 서비스가 **Azure 미리 보기 포털**의 **Data Factory** 블레이드에 표시됩니다.    
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 다음과 같은 내용으로 JSON 파일 **MyHDInsightCluster.json**을 만들어 **C:\ADFGetStarted\Hive** 폴더에 저장합니다. JSON 파일을 저장하기 전에 clustername, username 및 password를 적절한 값으로 바꿉니다.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. **Azure PowerShell**을 시작하고 다음 명령을 실행하여 **AzureResourceManager** 모드로 전환합니다. Azure 데이터 팩터리 cmdlet은 **AzureResourceManager** 모드에서 사용할 수 있습니다.

         switch-azuremode AzureResourceManager
		

3. **C:\ADFGetstarted\Hive** 폴더로 전환합니다.
4. 다음 명령을 실행하여 고유한 HDInsight 클러스터에 대한 연결된 서비스를 만듭니다.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json

### 파이프라인 만들기 및 예약
   
1. 다음과 같은 내용으로 JSON 파일 **ADFTutorialHivePipeline.json**을 만들어 **C:\ADFGetStarted\Hive** 폴더에 저장합니다. 고유한 클러스터를 만들고 단계에 따라 **MyHDInsightCluster** 연결된 서비스를 만들려면 다음 JSON에서 **HDInsightOnDemandCluster**를 **MyHDInsightCluster**로 바꿉니다. 


    	{
    		"name": "ADFTutorialHivePipeline",
    		"properties":
    		{
        		"description" : "It runs a HiveQL query and stores the result set in a blob",
        		"activities":
        		[
            		{
						"name": "RunHiveQuery",
						"description": "Runs a hive query",
						"type": "HDInsightActivity",
						"inputs": [{"name": "HiveInputBlobTable"}],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandCluster",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@spestore.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\\hivequery.hql",
						    "scriptLinkedService": "MyBlobStore"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		]
      		}
		}

2. 다음 명령을 실행하여 파이프라인을 만듭니다.
    	
		New-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\ADFTutorialHivePipeline.json
    	
3. 파이프라인을 예약합니다.
    	
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-27 -EndDateTime 2014-09-30 -Name ADFTutorialHivePipeline 

	> [WACOM.NOTE] **StartDateTime** 값을 현재 날짜 3일 전 날짜로, **EndDateTime** 값을 현재 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 UTC 시간이며 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)이어야 합니다. 예를 들면 다음과 같습니다. 2014-10-14T16:32:41Z. 
	> **EndDateTime**을 지정하지 않는 경우 "**StartDateTime + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **9/9/9999**를 **EndDateTime**으로 지정합니다.
  	
	출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다. 

4. [데이터 팩터리 시작][adfgetstarted] 문서의 [데이터 집합 및 파이프라인 모니터링][adfgetstartedmonitoring] 섹션을 참조하세요.   

## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 소개][data-factory-introduction] | 이 문서에서는 Azure 데이터 팩터리 서비스의 개념, 제공하는 가치 및 지원하는 시나리오에 대해 소개합니다.
[Azure 데이터 팩터리 시작][adf-getstarted] | 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 sample Azure 데이터 팩터리를 만드는 방법을 보여 주는 완전한 자습서를 제공합니다.
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources] | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 완전한 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities] | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot] | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다.  
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발\자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 


[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[개발자 참조]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure 포털]: http://portal.azure.com
