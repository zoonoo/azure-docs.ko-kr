<properties 
	pageTitle="Azure Data Factory에서 Spark 프로그램 호출" 
	description="MapReduce 작업을 사용하여 Azure Data Factory에서 Spark 프로그램을 호출하는 방법에 대해 알아봅니다." 
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
	ms.date="06/27/2016" 
	ms.author="spelluru"/>

# Data Factory에서 Spark 프로그램 호출
## 소개
Data Factory 파이프라인에서 MapReduce 작업을 사용하여 HDInsight Spark 클러스터에서 Spark 프로그램을 실행할 수 있습니다. 작업 사용에 대한 자세한 내용을 보려면 이 문서를 읽기 전에 [MapReduce 작업](data-factory-map-reduce.md) 문서를 참조하세요.

## GitHub의 Spark 샘플
[Spark - GitHub의 Data Factory 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark)에서는 MapReduce 작업을 사용하여 Spark 프로그램을 호출하는 방법을 보여 줍니다. Spark 프로그램은 단순히 Azure Blob 컨테이너에서 다른 컨테이너로 데이터를 복사합니다.

## 데이터 팩터리 엔터티
**Spark-ADF/src/ADFJsons** 폴더에는 Data Factory 엔터티(연결된 서비스, 데이터 집합, 파이프라인)에 대한 파일이 있습니다.

이 샘플에는 두 가지 **연결된 서비스**(Azure 저장소 및 Azure HDInsight)가 있습니다. **StorageLinkedService.json**에서 Azure 저장소 이름 및 키 값을 지정하고 **HDInsightLinkedService.json**에서 clusterUri, userName 및 password를 지정해야 합니다.

이 샘플에는 두 가지 **데이터 집합**(**input.json** 및 **output.json**)이 있습니다. 이러한 파일은 **Datasets** 폴더에 있습니다. 이러한 파일은 MapReduce 작업에 대한 입력 및 출력 데이터 집합을 나타냅니다.

**ADFJsons/Pipeline** 폴더의 샘플에는 하나의 **파이프라인**이 있습니다. 이 엔터티는 MapReduce 작업을 사용하여 Spark 프로그램을 호출하는 방법을 이해하기 위해 검토해야 하는 가장 중요한 엔터티입니다.

	{
	    "name": "SparkSubmit",
	    "properties": {
	        "description": "Submit a spark job",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "com.adf.spark.SparkJob",
	                    "jarFilePath": "libs/spark-adf-job-bin.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "--jarFile",
	                        "libs/sparkdemoapp_2.10-1.0.jar",
	                        "--jars",
	                        "/usr/hdp/current/hadoop-client/hadoop-azure-2.7.1.2.3.3.0-3039.jar,/usr/hdp/current/hadoop-client/lib/azure-storage-2.2.0.jar",
	                        "--mainClass",
	                        "com.adf.spark.demo.Demo",
	                        "--master",
	                        "yarn-cluster",
	                        "--driverMemory",
	                        "2g",
	                        "--driverExtraClasspath",
	                        "/usr/lib/hdinsight-logging/*",
	                        "--executorCores",
	                        "1",
	                        "--executorMemory",
	                        "4g",
	                        "--sparkHome",
	                        "/usr/hdp/current/spark-client",
	                        "--connectionString",
	                        "DefaultEndpointsProtocol=https;AccountName=<YOUR_ACCOUNT>;AccountKey=<YOUR_KEY>",
	                        "input=wasb://input@<YOUR_ACCOUNT>.blob.core.windows.net/data",
	                        "output=wasb://output@<YOUR_ACCOUNT>.blob.core.windows.net/results"
	                    ]
	                },
	                "inputs": [
	                    {
	                        "name": "input"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "output"
	                    }
	                ],
	                "policy": {
	                    "executionPriorityOrder": "OldestFirst",
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "Spark Launcher",
	                "description": "Submits a Spark Job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2015-11-16T00:00:01Z",
	        "end": "2015-11-16T23:59:00Z",
	        "isPaused": false,
	        "pipelineMode": "Scheduled"
	    }
	}

보는 것처럼 MapReduce 작업은 Azure 저장소의 **libs** 컨테이너에 있는 **spark-adf-job-bin.jar**을 호출하도록 구성됩니다(StorageLinkedService.json에서 지정) . 이 프로그램의 소스 코드는 Spark-ADF/src/main/java/com/adf/spark 폴더에 있으며 spark-submit을 호출하고 Spark 작업을 실행합니다.

HDInsight Spark 클러스터에서 실행되는 이 MapReduce 프로그램(spark-adf-job-bin.jar)은 Spark 프로그램 **sparkdemoapp_2.10-1.0.jar**을 호출하고 MapReduce 작업(위의 JSON에 표시)을 통해 받은 인수를 Spark 프로그램에 전달합니다. **sparkdemoapp_2.10-1.0.jar**에는 Azure blob 컨테이너 간에 데이터를 복사하는 Scala 소스 코드가 포함되어 있습니다. 이 데모 앱 jar를 Spark를 사용하여 실행하려는 작업이 포함된 다른 jar로 바꿀 수 있습니다.

다시 말해, **MapReduce 작업**은 Spark 프로그램 **sparkdemoapp_2.10-1.0.jar**을 호출하는 MapReduce 프로그램 **spark-adf-job-bin.jar**을 호출합니다. 사용자 고유의 Spark 프로그램을 실행하려면 sparkdemoapp_2.10-1.0.jar을 해당 프로그램으로 바꿉니다.

> [AZURE.NOTE] MapReduce 작업을 사용하여 Spark 프로그램을 호출하려면 사용자 고유의 HDInsight Spark 클러스터를 사용해야 합니다. 주문형 HDInsight 클러스터 사용은 지원되지 않습니다.


## 참고 항목
- [Hive 작업](data-factory-hive-activity.md)
- [Pig 작업](data-factory-pig-activity.md)
- [MapReduce 작업](data-factory-map-reduce.md)
- [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
- [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0629_2016-->