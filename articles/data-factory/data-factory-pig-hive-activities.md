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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Azure 데이터 팩터리에서 Pig 및 Hive 사용
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서는 Azure 데이터 팩터리 파이프라인에서 Pig/Hive 변환으로 HDInsight 활동을 사용 하 여 설명 합니다. 참조 [데이터 공장에서 MapReduce 프로그램 호출][data-factory-map-reduce] MapReduce를 실행 하는 방법에 대 한 세부 정보에 대 한 Azure 데이터 팩터리 파이프라인에서 클러스터에 HDInsight에서 프로그램입니다.

## 연습: 하이브를 사용 하 여 Azure 데이터 팩터리
이 연습에서는 데이터 팩터리 파이프라인에서 Hive 변환으로 HDInsight 작업을 사용 하기 위한 단계별 지침을 제공 합니다.

### 필수 구성 요소
1. 자습서를 완료 [Azure 데이터 팩터리 시작][adfgetstarted] 문서입니다.
2. 업로드 **emp.txt** 으로 위의 자습서에서 만든 파일 **hiveinput\emp.txt** adftutorial 컨테이너의 blob 저장소에 있습니다.  **hiveinput** 폴더에 자동으로 만들어집니다는 **adftutorial** 이 구문 사용 하 여 emp.txt 파일을 업로드할 때 컨테이너입니다.

	> [AZURE.NOTE]Emp.txt 파일이이 연습에 대 한 한 더미 파일입니다. 실제 입력된 데이터에서 제공 된 **hivesampletable** HDInsight 클러스터에 이미 있는 합니다. 파이프라인은 emp.txt 파일을 전혀 사용 하지 않습니다.
	
2. 만들기 **hivequery.hql** 라는 하위 폴더에서 파일 **하이브** 아래 **C:\ADFGetStarted** 다음 내용을 사용 합니다.
    		
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

	> [AZURE.NOTE]사용 하는 **Tez** HQL 파일에서 Hive 쿼리 실행, 추가 하는 엔진 "* * hive.execution.engine=tez**; 설정" 파일의 위쪽에 있습니다.
		
3.  업로드는 **hivequery.hql** 에 **adftutorial** blob 저장소에서 컨테이너


### 연습

#### 입력 테이블 만들기
1. 에 **데이터 팩터리** 에 대 한 블레이드는 **ADFTutorialDataFactory**, 클릭 **작성자 및 배포** 데이터 팩터리 편집기를 시작 하려면.
	
	![데이터 팩터리 블레이드][data-factory-blade]

2. 에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 데이터 집합**, 클릭 하 고 **Azure Blob 저장소** 명령 모음에서.
3. 오른쪽 창에서 JSON 스크립트를 다음 JSON 스크립트 바꿉니다.    
    		
		{
    		"name": "HiveInputBlobTable",
    		"properties":
    		{
        		"location": 
        		{
            		"type": "AzureBlobLocation",
            		"folderPath": "adftutorial/hiveinput",
            		"linkedServiceName": "StorageLinkedService"
        		},
        		"availability": 
        		{
            		"frequency": "Day",
            		"interval": 1,
            		"waitonexternal": {}
        		}
    		}
		}

 
	**다음 참고:**
	
	- 위치 **유형** 로 설정 된 **AzureBlobLocation**.
	- **linkedServiceName** 로 설정 된 **StorageLinkedService** Azure 저장소 계정 정의입니다.
	- **folderPath** 입력된 데이터에 대 한 blob container\folder를 지정 합니다. 
	- **빈도 = 일** 및 **간격 = 1** 조각이 매일 사용할 수 있는 것을 의미
	- **waitOnExternal** 은이 데이터 다른 파이프라인에 의해 생성 되지 않을, 대신 생성 되는 외부 데이터 팩터리를 의미 합니다. 
	

	참조 [데이터 팩터리 개발자 참조][developer-reference] JSON 속성에 대 한 설명입니다.

2. 클릭 하 여 **배포** 테이블을 배포 하려면 명령 모음에 있습니다.
  
#### 출력 테이블 만들기
        
1. 에 **데이터 팩터리 편집기**, 를 클릭 하 여 **새 데이터 집합**, 클릭 하 고 **Azure Blob 저장소** 명령 모음에서.
2. 오른쪽 창에서 JSON 스크립트를 다음 JSON 스크립트 바꿉니다.

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

2. 클릭 하 여 **배포** 테이블을 배포 하려면 명령 모음에 있습니다.


### HDInsight 클러스터에 대한 연결된 서비스 만들기
Azure 데이터 팩터리 서비스는 주문형 클러스터 만들기를 지원하며 이 클러스터를 사용하여 입력을 처리하고 출력 데이터를 생성합니다. 또한 고유한 클러스터를 사용하여 같은 작업을 할 수도 있습니다. 주문형 HDInsight 클러스터를 사용하면 각 조각에 대해 클러스터가 생성됩니다. 반면 고유한 HDInsight 클러스터를 사용하는 경우에는 클러스터에서 조각을 즉시 처리할 수 있습니다. 따라서 주문형 클러스터를 사용하는 경우 출력 데이터가 고유한 클러스터를 사용할 때처럼 빠르게 표시되지 않을 수 있습니다. 샘플을 보여 주기 위해 주문형 클러스터를 사용해 보겠습니다.

#### 주문형 HDInsight 클러스터를 사용하려면
1. 클릭 하 여 **새 계산** 를선택하고 명령 모음에서 **주문형 HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterSize** 속성을 HDInsight 클러스터의 크기를 지정 합니다.
	2. 에 대 한는 **jobsContainer** 속성, 클러스터 로그를 저장할 기본 컨테이너의 이름을 지정 합니다. 이 자습서에서는 지정 **adfjobscontainer**.
	3. 에 대 한는 **timeToLive** 속성을 얼마나 오래 클러스터 유휴 상태일 수 있는 삭제 하기 전에 지정 합니다. 
	4. 에 대 한는 **버전** 속성을 사용 하려는 HDInsight 버전을 지정 합니다. 이 속성을 제외 하는 경우에 최신 버전이 사용 됩니다.  
	5. 에 대 한는 **linkedServiceName**, 지정 **StorageLinkedService** 가져오기에서 만들었을 자습서를 시작 합니다. 

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

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.
   
   
#### 고유한 HDInsight 클러스터를 사용하려면 

1. 클릭 하 여 **새 계산** 를선택하고 명령 모음에서 **HDInsight 클러스터** 메뉴에서.
2. JSON 스크립트에서 다음을 수행 합니다. 
	1. 에 대 한는 **clusterUri** 속성을 프로그램 HDInsight에 대 한 URL을 입력 합니다. 예: https://<clustername>.azurehdinsight.net/     
	2. 에 대 한는 **UserName** 속성을 HDInsight 클러스터에 대 한 액세스 권한이 있는 사용자 이름을 입력 합니다.
	3. 에 대 한는 **암호** 속성을 사용자에 대 한 암호를 입력 합니다. 
	4. 에 대 한는 **LinkedServiceName** 속성, 입력 **StorageLinkedService**. 시작된 자습서 다운로드에에서 만들 때 연결 된 서비스입니다. 

2. 클릭 하 여 **배포** 연결 된 서비스를 배포 하려면 명령 모음에 있습니다.

### 파이프라인 만들기 및 예약
   
1. 클릭 하 여 **새 파이프라인** 명령 모음에서. 명령이 표시 되지 않으면 클릭 **... (줄임표)** 이 확인 합니다. 
2. 오른쪽 창에서 JSON을 다음 JSON 스크립트 바꿉니다. 만드는 단계를 실행 및 사용자 고유의 클러스터를 사용 하려는 경우는 **HDInsightLinkedService** 서비스에 연결 된 대체 **HDInsightOnDemandLinkedService** 와 **HDInsightLinkedService** 다음 JSON에 있습니다. 


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
						"linkedServiceName": "HDInsightLinkedService",
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

	> [AZURE.NOTE]대체 **시작 날짜 시간** 현재 날짜 전에 3 일을 사용 하 여 값 및 **EndDateTime** 은 현재 날짜를 사용 하 여 값입니다. 시작 날짜 시간 및 EndDateTime 모두에 있어야 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601). 예: 2014-10-14T16:32:41Z 합니다. 출력 테이블이 매일 생성되도록 예약되었으므로 3개의 조각이 생성됩니다.
	
	> [AZURE.NOTE]대체 **저장소 계정의** 저장소 계정의 이름으로 JSON에서.
	
	참조 [JSON 스크립팅 참조](http://go.microsoft.com/fwlink/?LinkId=516971) JSON 속성에 대 한 세부 정보에 대 한 합니다.
2. 클릭 하 여 **배포** 파이프라인을 배포 하려면 명령 모음에 있습니다.
4. 참조 [모니터링 데이터 집합 및 파이프라인][adfgetstartedmonitoring] 의 섹션 [데이터 팩터리 시작][adfgetstarted] 문서입니다. 

	> [AZURE.NOTE]에 **작업 실행 세부 정보** 출력 테이블의 한 조각에 대 한 블레이드 (출력 테이블을 선택 선택-> 조각에는 포털에서 활동 실행 선택->), HDInsight 클러스터에 의해 생성 된 로그에 대 한 링크가 표시 됩니다. 자체는 포털에서이 검토 하거나 컴퓨터에 다운로드 수 있습니다.
  

## Pig JSON 예제
JSON 파이프라인에서 Pig 또는 Hive 활동을 정의 하는 경우는 **유형** 속성 설정 해야 합니다: **HDInsightActivity**.

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

**다음 참고:**
	
- 활동 **유형** 로 설정 된 **HDInsightActivity**.
- **linkedServiceName** 로 설정 된 **MyHDInsightLinkedService**. 연결 된 HDInsight 서비스를 만드는 방법에 대 한 자세한 내용은 아래에서 연결 된 HDInsight 서비스 섹션을 참조 하십시오.
-  **유형** 의 **변환** 로 설정 된 **Pig**.
- 에 대 한 Pig 스크립트 인라인을 지정할 수는 **스크립트** 속성 또는 저장소 스크립트 파일에는 Azure blob 저장소 및 파일 사용을 참조 하십시오 **scriptPath** 속성을이 문서의 뒷부분에 대해서 설명 합니다. 
- 사용 하 여 Pig 스크립트에 대 한 매개 변수를 지정 된 **extendedProperties**. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 


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

**다음 참고:**
	
- 활동 **유형** 로 설정 된 **HDInsightActivity**.
- **linkedServiceName** 로 설정 된 **MyHDInsightLinkedService**. 
-  **유형** 의 **변환** 로 설정 된 **하이브**.
- 에 대 한 Hive 스크립트 인라인을 지정할 수는 **스크립트** 속성 또는 저장소 스크립트 파일에는 Azure blob 저장소 및 파일 사용을 참조 하십시오 **scriptPath** 속성을이 문서의 뒷부분에 대해서 설명 합니다. 
- 사용 하 여 Hive 스크립트에 대 한 매개 변수를 지정 된 **extendedProperties**. 자세한 내용은 이 문서의 뒷부분에 나와 있습니다. 

> [AZURE.NOTE]참조 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908) cmdlet, JSON 스키마 및 속성 스키마에 대 한 자세한 내용은 합니다.


## 활동 HDInsight에서에서 Pig 및 Hive 스크립트를 사용 하 여
HDInsight 클러스터와 연결된 Azure Blob 저장소에 Pig/Hive 스크립트를 저장하고 JSON에 다음 속성을 사용하여 Pig/Hive 작업에서 이 스크립트를 참조할 수 있습니다.

* **scriptPath** – Pig 또는 Hive 스크립트 파일에 대 한 경로
* **scriptLinkedService** – 스크립트 파일을 포함 하는 Azure 저장소 계정

샘플 파이프라인에 대 한 다음 JSON 예제를 참조 하는 Hive 작업을 사용 하 여 **transformdata.hql** 파일에 저장 된 **스크립트** 폴더에는 **adfwalkthrough** 나타내는 Azure blob 저장소의 컨테이너는 **StorageLinkedService**.

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


> [AZURE.NOTE]사용 하는 **Tez** 엔진 하이브 쿼리 실행, 실행을 "* * hive.execution.engine=tez**; 설정" 하이브 쿼리를 실행 하기 전에 합니다.
> 
> 참조 [개발자 참조](http://go.microsoft.com/fwlink/?LinkId=516908) cmdlet, JSON 스키마 및 속성 스키마에 대 한 자세한 내용은 합니다.

## 매개 변수가 지정된 Pig 및 Hive 쿼리
데이터 팩터리 Pig 및 Hive 활동을 사용에 대 한 매개 변수를 사용 하 여 Pig 및 Hive 스크립트에서 사용 되는 값을 지정 하면 **extendedProperties**. extendedProperties 섹션은 매개 변수 이름과 매개 변수 값으로 구성됩니다.

다음 예제에서는 사용 하 여 Hive 스크립트에 대 한 매개 변수를 지정 하는 것에 대 한 참조 **extendedProperties**. 매개 변수가 있는 Hive 스크립트를 사용하려면 다음을 수행합니다.

1.	매개 변수를 정의 **extendedProperties**.
2.	인라인 Hive 스크립트 (또는) 블로그 저장소에 저장 된 Hive 스크립트 파일을 사용 하 여 매개 변수를 참조 **${hiveconf:parameterName}**.

   
    		
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
[이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리 하는 자습서:][adf-tutorial] | 이 문서를 거의 실시간을 구현 하는 방법을 보여주는 프로그램-종단간 연습을 제공 Azure 데이터 팩터리를 사용 하 여 통찰력으로 로그 파일의에서 데이터를 변환 하는 업무 시나리오입니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수에 대 한 포괄적인 참조 콘텐츠 중... 

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

<!---HONumber=GIT-SubDir--> 