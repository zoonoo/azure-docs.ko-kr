<properties 
	pageTitle="Azure 데이터 팩터리에서 Pig 및 Hive 사용" 
	description="Azure HDInsight 클러스터에서 Azure 데이터 팩터리의 Pig 및 Hive 스크립트를 실행하여 데이터를 처리하는 방법에 대해 알아봅니다." 
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
	ms.date="06/19/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리에서 Pig 및 Hive 사용
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서에서는 Azure 데이터 팩터리 파이프라인에서 Pig/Hive 변환과 함께 HDInsight 작업을 사용하는 방법을 설명합니다. Azure 데이터 팩터리 파이프라인에서 HDInsight 클러스터에 대해 MapReduce 프로그램을 실행하는 방법에 대한 자세한 내용은 [데이터 팩터리에서 MapReduce 프로그램 호출][data-factory-map-reduce]을 참조하십시오.

## 연습: Azure 데이터 팩터리에서 Hive 사용
이 연습에서는 데이터 팩터리 파이프라인에서 Hive 변환과 함께 HDInsight 작업을 사용하기 위한 단계별 지침을 제공합니다.

### 필수 구성 요소
1. [Azure 데이터 팩터리 시작][adfgetstarted] 문서의 자습서를 완료합니다.
2. **C:\ADFGetStarted** 아래의 **Hive**라는 하위 폴더에 **hivequery.hql** 파일을 다음과 같은 내용으로 만듭니다.
    		
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

	> [AZURE.NOTE]**Tez** 엔진을 사용하여 HQL 파일의 Hive 쿼리를 실행하려면 파일의 맨 위에 "**set hive.execution.engine=tez**;"를 추가합니다.
		
3.  **hivequery.hql**을 Blob 저장소의 **adftutorial** 컨테이너에 업로드합니다.


### 연습

#### 출력 테이블 만들기
        
1. **데이터 팩터리 편집기**에서 **새 데이터 집합**을 클릭하고 명령 모음에서 **Azure Blob 저장소**를 클릭합니다.
2. 오른쪽 창의 JSON 스크립트를 다음 JSON 스크립트로 바꿉니다.

		{
    		"name": "HiveOutputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
	    			"folderPath": "adftutorial/hiveoutput/",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1
        		}
    		}
		}

2. 명령 모음에서 **배포**를 클릭하여 테이블을 배포합니다.


### HDInsight 클러스터에 대한 연결된 서비스 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터가 자동으로 작성되며 Azure 데이터 팩터리 서비스에서 데이터를 처리하도록 관리됩니다. 주문형 HDInsight 연결된 서비스는 [HDInishgt 주문형 연결된 서비스](https://msdn.microsoft.com/library/dn893526.aspx)를 참조하세요. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다. 주문 시 HDInsight 클러스터가 만들어지는 데는 15+분이 걸리며 HDInsight 클러스터가 시작되어 작업을 실행하는 시간에만 청구됩니다.

#### 주문형 HDInsight 클러스터를 사용하려면
1. 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **주문형 HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterSize** 속성에 대해 HDInsight 클러스터의 크기를 지정합니다.
	2. **jobsContainer** 속성에 대해 클러스터 로그를 저장할 기본 컨테이너의 이름을 지정합니다. 이 자습서에서는 **adfjobscontainer**를 지정합니다.
	3. **timeToLive** 속성에 대해 클러스터가 삭제되기 전에 유휴 상태로 유지될 수 있는 기간을 지정합니다. 
	4. **version** 속성에 대해 사용할 HDInsight 버전을 지정합니다. 이 속성을 제외하면 최신 버전이 사용됩니다.  
	5. **linkedServiceName**에 대해 시작 자습서에서 만든 **StorageLinkedService**를 지정합니다. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.
   
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 명령 모음에서 **새 계산**을 클릭하고 메뉴에서 **HDInsight 클러스터**를 선택합니다.
2. JSON 스크립트에서 다음을 수행합니다. 
	1. **clusterUri** 속성에 대해 HDInsight의 URL을 입력합니다. 예를 들어 https://<clustername>.azurehdinsight.net/을 입력합니다.     
	2. **UserName** 속성에 대해 HDInsight 클러스터에 액세스할 수 있는 사용자 이름을 입력합니다.
	3. **password** 속성에 대해 사용자 암호를 입력합니다. 
	4. **LinkedServiceName** 속성에 대해 **StorageLinkedService**를 입력합니다. 시작 자습서에서 만든 연결된 서비스입니다.. 

2. 명령 모음에서 **배포**를 클릭하여 연결된 서비스를 배포합니다.

### 파이프라인 만들기 및 예약
   
1. 명령 모음에서 **새 파이프라인**을 클릭합니다. 명령이 표시되지 않으면 **... (줄임표)**을 클릭하여 표시합니다. 
2. 오른쪽 창의 JSON을 다음 JSON 스크립트로 바꿉니다. 사용자 고유의 클러스터를 사용하려고 하며 **HDInsightLinkedService** 연결된 서비스를 만드는 단계를 따른 경우 다음 JSON에서 **HDInsightOnDemandLinkedService**를 **HDInsightLinkedService**로 바꿉니다. 


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
						"inputs": [],
						"outputs": [ {"name": "HiveOutputBlobTable"} ],
						"linkedServiceName": "HDInsightOnDemandLinkedService",
						"transformation":
						{
                    		"type": "Hive",
                    		"extendedProperties":
                    		{
                        		"RESULTOUTPUT": "wasb://adftutorial@<your storage account>.blob.core.windows.net/hiveoutput/",
		                        "Year":"$$Text.Format('{0:yyyy}',SliceStart)",
		                        "Month":"$$Text.Format('{0:%M}',SliceStart)",
		                        "Day":"$$Text.Format('{0:%d}',SliceStart)"
		                    },
		                    "scriptpath": "adftutorial\hivequery.hql",
						    "scriptLinkedService": "StorageLinkedService"
						},
						"policy":
						{
							"concurrency": 1,
							"executionPriorityOrder": "NewestFirst",
							"retry": 1,
							"timeout": "01:00:00"
						}
            		}
        		],
				"start": "2015-02-13T00:00:00Z",
        		"end": "2015-02-14T00:00:00Z",
        		"isPaused": false

      		}
		}

	> [AZURE.NOTE]**StartDateTime** 값을 현재 날짜 3일 전 날짜로, **EndDateTime** 값을 현재 날짜로 바꿉니다. StartDateTime 및 EndDateTime은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. 출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다.
	> 
	> JSON의 **your storage account**를 저장소 계정의 이름으로 바꿉니다.
	
	JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=516971)를 참조하세요.
2. 명령 모음에서 **배포**를 클릭하여 파이프라인을 배포합니다.
4. [데이터 팩터리 시작][adfgetstarted] 문서의 [데이터 집합 및 파이프라인 모니터링][adfgetstartedmonitoring] 섹션을 참조하세요. 

	> [AZURE.NOTE]출력 테이블의 조각에 대한 **작업 실행 세부 정보** 블레이드에(포털에서 출력 테이블 선택 -> 조각 선택 -> 작업 실행 선택) HDInsight 클러스터에 의해 생성된 로그의 링크가 표시됩니다. 포털 자체에서 검토하거나 컴퓨터로 다운로드할 수 있습니다.
  

## Pig JSON 예제
파이프라인 JSON에서 Pig 또는 Hive 작업을 정의할 때는 **type** 속성을 **HDInsightActivity**로 설정해야 합니다.

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

**다음 사항에 유의하십시오.**
	
- 작업 **type**을 **HDInsightActivity**로 설정합니다.
- **linkedServiceName**을 **MyHDInsightLinkedService**로 설정합니다. HDInsight 연결된 서비스를 만드는 방법에 대한 자세한 내용은 아래의 HDInsight 연결된 서비스 섹션을 참조하십시오.
- **transformation**의 **type**을 **Pig**로 설정합니다.
- Pig 스크립트를 **script** 속성에 인라인으로 지정할 수도 있고 스크립트 파일을 Azure Blob 저장소에 저장하고 이 문서 뒷부분에서 설명하는 **scriptPath** 속성을 사용하여 파일을 참조할 수도 있습니다. 
- Pig 스크립트에 대한 매개 변수는 **extendedProperties**를 사용하여 지정합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 


## Hive JSON 예제


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

**다음 사항에 유의하십시오.**
	
- 작업 **type**을 **HDInsightActivity**로 설정합니다.
- **linkedServiceName**을 **MyHDInsightLinkedService**로 설정합니다. 
- **transformation**의 **type**을 **Hive**로 설정합니다.
- Hive 스크립트를 **script** 속성에 인라인으로 지정할 수도 있고 스크립트 파일을 Azure Blob 저장소에 저장하고 이 문서 뒷부분에서 설명하는 **scriptPath** 속성을 사용하여 파일을 참조할 수도 있습니다. 
- Hive 스크립트에 대한 매개 변수는 **extendedProperties**를 사용하여 지정합니다. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 

> [AZURE.NOTE]cmdlet, JSON 스키마 및 스키마의 속성에 대한 자세한 내용은 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908)(영문)를 참조하세요.


## HDInsight 작업에 Pig 및 Hive 스크립트 사용
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
    					"scriptpath": "adfwalkthrough\scripts\transformdata.hql",    		
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


> [AZURE.NOTE]**Tez** 엔진을 사용하여 Hive 쿼리를 실행하려면 Hive 쿼리를 실행하기 전에 "**set hive.execution.engine=tez**;"를 실행합니다.
> 
> cmdlet, JSON 스키마 및 스키마의 속성에 대한 자세한 내용은 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908)(영문)를 참조하세요.

## 매개 변수가 지정된 Pig 및 Hive 쿼리
데이터 팩터리 Pig 및 Hive 작업에서는 **extendedProperties**를 사용하여 Pig 및 Hive 스크립트에 사용되는 매개 변수의 값을 지정할 수 있습니다. extendedProperties 섹션은 매개 변수 이름과 매개 변수 값으로 구성됩니다.

**extendedProperties**를 사용하여 Hive 스크립트의 매개 변수를 지정하는 방법은 다음 예제를 참조하세요. 매개 변수가 있는 Hive 스크립트를 사용하려면 다음을 수행합니다.

1.	**extendedProperties**에서 매개 변수를 정의합니다.
2.	인라인 Hive 스크립트 또는 Blob 저장소에 저장된 Hive 스크립트 파일에서 **${hiveconf:parameterName}**을 사용하여 매개 변수를 참조합니다.

   
    		
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


## 참고 항목

문서 | 설명
------ | ---------------
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial] | 이 문서에서는 Azure 데이터 팩터리를 사용하는 거의 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 완전한 연습을 제공합니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 

[data-factory-copy-activity]: ..//data-factory-copy-activity
[data-factory-map-reduce]: ..//data-factory-map-reduce

[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[data-factory-blade]: ./media/data-factory-pig-hive-activities/DataFactoryBlade.png


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 

<!---HONumber=62-->