<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Azure 데이터 팩터리에서 MapReduce 프로그램 호출" description="Azure 데이터 팩터리에서 Azure HDInsight 클러스터에서 MapReduce 프로그램을 실행 하 여 데이터를 처리 하는 방법에 알아봅니다." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# 데이터 팩터리에서 MapReduce 프로그램 호출
이 문서에서는 **HDInsight 작업**과 **MapReduce 변환**을 사용하여 Azure 데이터 팩터리 파이프라인에서 **MapReduce** 프로그램을 호출하는 방법을 설명합니다. 

## 소개 
Azure 데이터 팩터리의 파이프라인은 연결된 저장소 서비스의 데이터를 연결된 계산 서비스를 사용하여 처리합니다. 파이프라인에는 일련의 작업이 포함되며 각 작업에서는 특정 처리 작업을 수행합니다. 

- **복사 작업**은 원본 저장소의 데이터를 대상 저장소에 복사합니다. 복사 작업에 대한 자세한 내용은 [데이터 팩터리를 사용하여 데이터 복사][data-factory-copy-activity]를 참조하세요. 
- **HDInsight 작업**은 HDInsight 클러스터에 대해 Hive/Pig 스크립트 또는 MapReduce 프로그램을 실행하여 데이터를 처리합니다. HDInsight 작업은 세 가지 변환 **Hive**, **Pig** 및 **MapReduce**를 지원합니다. HDInsight 작업은 하나 이상의 입력을 사용하고 하나 이상의 출력을 생성할 수 있습니다.
 
HDInsight 작업의 Pig/Hive 변환을 사용하여 Azure 데이터 팩터리 파이프라인에서 HDInsight 클러스터에 대해 Pig/Hive 스크립트를 실행하는 방법에 대한 자세한 내용은 [데이터 팩터리에서 Pig 및 Hive 사용][data-factory-pig-hive-activities]을 참조하세요. 이 문서에서는 HDInsight 작업의 MapReduce 변환을 사용하는 방법을 설명합니다.

## 파이프라인 JSON
파이프라인에 대한 JSON 파일에서 다음을 수행합니다.
 
1. **activity**의 **type**을 **HDInsightActivity**로 설정합니다.
2. **transformation**의 **type**을 **MapReduce**로 설정합니다.
3. **className** 속성에 대한 클래스의 이름을 지정합니다.
4. **jarFilePath** 속성의 JAR 파일 경로(파일 이름 포함)를 지정합니다.
5. **jarLinkedService** 속성의 JAR 파일이 포함된 Azure Blob 저장소를 참조하는 연결된 서비스를 지정합니다.   
6. **arguments** 섹션에 MapReduce 프로그램의 모든 인수를 지정합니다. 

MapReduce 변환을 사용하여 HDInsight 클러스터에 대해 모든 MapReduce jar 파일을 실행할 수 있습니다. 다음 파이프라인의 샘플 JSON 정의에서 HDInsight 작업은 Mahout JAR 파일을 실행하도록 구성되어 있습니다.   
 

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

## 샘플
HDInsight 작업과 MapReduce 변환을 사용하는 샘플은 [GitHub의 데이터 팩터리 샘플][data-factory-samples](영문)에서 다운로드할 수 있습니다.  

## 참고 항목

문서 | 설명
------ | ---------------
[Azure 데이터 팩터리 소개][data-factory-introduction] | 이 문서에서는 Azure 데이터 팩터리 서비스, 개념, Azure 데이터 팩터리 서비스가 제공하는 가치 및 지원하는 시나리오에 대해 소개합니다.
[Azure 데이터 팩터리 시작][adf-getstarted] | 이 문서에서는 Azure Blob에서 Azure SQL 데이터베이스로 데이터를 복사하는 샘플 Azure 데이터 팩터리를 만드는 방법을 보여 주는 종단 간 자습서를 제공합니다.
[파이프라인에서 온-프레미스 데이터를 사용할 수 있도록 설정][use-onpremises-datasources](영문) | 이 문서의 연습에서는 온-프레미스 SQL Server 데이터베이스에서 Azure Blob으로 데이터를 복사하는 방법을 보여 줍니다.
[자습서: 데이터 팩터리를 사용하여 로그 파일 이동 및 처리][adf-tutorial](영문) | 이 문서에서는 Azure 데이터 팩터리를 사용하는 실제 시나리오를 구현하여 로그 파일의 데이터에서 통찰력을 얻는 방법을 보여 주는 종단 간 연습을 제공합니다.
[데이터 팩터리에서 사용자 지정 작업 사용][use-custom-activities](영문) | 이 문서에서는 사용자 지정 작업을 만들어 파이프라인에서 사용하는 방법에 대한 단계별 지침이 포함된 연습을 제공합니다. 
[데이터 팩터리 문제 해결][troubleshoot](영문) | 이 문서에서는 Azure 데이터 팩터리 문제를 해결하는 방법에 대해 설명합니다.
[Azure 데이터 팩터리 개발자 참조][developer-reference](영문) | 개발자 참조에는 cmdlet, JSON 스크립트, 함수 등에 대한 포괄적인 참조 콘텐츠가 포함되어 있습니다. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
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

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
