<properties 
	pageTitle="Azure 데이터 팩터리에서 MapReduce 프로그램 호출" 
	description="Azure 데이터 팩터리에서 Azure HDInsight 클러스터에서 MapReduce 프로그램을 실행 하 여 데이터를 처리 하는 방법에 알아봅니다." 
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

# 데이터 팩터리에서 MapReduce 프로그램 호출
이 문서에서는 호출 하는 방법을 설명는 **MapReduce** 프로그램을 사용 하 여는 Azure 데이터 팩터리 파이프라인에서는 **HDInsight 활동** 와 **MapReduce 변환**.

## 소개 
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 이 문서에서는 HDInsight 작업의 MapReduce 변환을 사용하는 방법을 설명합니다.
 
참조 [사용 하 여 Pig 및 Hive 데이터 팩터리로][data-factory-pig-hive-activities] Pig/Hive를 실행 하는 방법에 대 한 세부 정보에 대 한 스크립트에는 HDInsight 클러스터는 Azure 데이터 팩터리 파이프라인에서 HDInsight 활동의 Pig/Hive 변환을 사용 하 여 합니다.

## MapReduce 변환을 사용 하 여 HDInsight 활동에 대 한 JSON 

HDInsight 활동에 대 한 JSON 정의:
 
1. 설정의 **유형** 의 **활동** 를 **HDInsightActivity**.
2. 설정의 **유형** 의 **변환** 를 **MapReduce**.
3. 에 대 한 클래스의 이름을 지정 **className** 속성입니다.
4. 에 대 한 파일 이름을 포함 하 여 JAR 파일의 경로를 지정 **jarFilePath** 속성입니다.
5. 지정 하기 위한 JAR 파일을 포함 하는 Azure Blob 저장소를 참조 하는 연결 된 서비스 **jarLinkedService** 속성입니다.   
6. MapReduce 프로그램에 대 한 모든 인수를 지정 하는 **인수** 섹션. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

MapReduce 변환을 사용하여 HDInsight 클러스터에 대해 모든 MapReduce jar 파일을 실행할 수 있습니다. 다음 파이프라인의 샘플 JSON 정의에서 HDInsight 작업은 Mahout JAR 파일을 실행하도록 구성되어 있습니다.

## 샘플
HDInsight 활동에서 MapReduce 변환으로 사용 하는 것에 대 한 예제를 다운로드할 수 있습니다: [github 데이터 팩터리 샘플][data-factory-samples].

## 참고 항목

문서 | 설명
------ | ---------------
[이동 하 고 데이터 팩터리를 사용 하 여 로그 파일을 처리 하는 자습서:][adf-tutorial] | 이 문서를 거의 실시간을 구현 하는 방법을 보여주는 프로그램-종단간 연습을 제공 Azure 데이터 팩터리를 사용 하 여 통찰력으로 로그 파일의에서 데이터를 변환 하는 업무 시나리오입니다. 이 자습서를 사용 하 여 모두 Pig 및 Hive 변환 데이터를 처리 합니다. 
[Azure 데이터 팩터리 개발자 참조][developer-reference] | 개발자 참조에는 cmdlet, JSON 스크립트, 함수에 대 한 포괄적인 참조 콘텐츠 중... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->