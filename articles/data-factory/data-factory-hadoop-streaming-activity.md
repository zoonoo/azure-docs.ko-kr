<properties 
	pageTitle="Hadoop 스트리밍 작업" 
	description="Azure Data Factory에서 Hadoop 스트리밍 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행하는 방법에 대해 알아봅니다." 
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
	ms.date="10/25/2015" 
	ms.author="spelluru"/>

# Hadoop 스트리밍 작업
HDInsightStreamingActivity 작업을 사용하여 Azure Data Factory 파이프라인에서 Hadoop 스트리밍 작업을 호출할 수 있습니다. 다음 JSON 조각은 파이프라인 JSON 파일에서 HDInsightStreamingActivity를 사용하기 위한 구문을 보여 줍니다.

Data Factory [파이프라인](data-factory-create-pipelines.md)에서 HDInsight 스트리밍 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) HDInsight 클러스터에서 Hadoop 스트리밍 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 작업의 일반적인 개요를 설명하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 기반으로 작성되었습니다.

## 예

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "getDebugInfo": "Failure",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
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

다음 사항에 유의하세요.

1. **linkedServiceName**을 스트리밍 mapreduce 작업을 실행할 수 있는 HDInsight 클러스터를 가리키는 연결된 서비스의 이름으로 설정합니다.
2. activity의 type을 **HDInsightStreaming**으로 설정합니다.
3. **mapper** 속성에는 매퍼 실행 파일의 이름을 지정합니다. 위의 예제에서는 cat.exe가 매퍼 실행 파일입니다.
4. **reducer** 속성에는 리듀서 실행 파일의 이름을 지정합니다. 위의 예제에서는 wc.exe가 리듀서 실행 파일입니다.
5. **input** 속성에는 매퍼의 입력 파일(위치 포함)을 지정합니다. "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt" 예에서 adfsampleadf는 Blob 컨테이너, example/data/Gutenberg는 폴더, davinci.txt는 Blob입니다.
6. **output** 속성에는 리듀서의 출력 파일(위치 포함)을 지정합니다. Hadoop 스트리밍 작업의 출력이 이 속성에 지정된 위치에 기록됩니다.
7. **filePaths** 섹션에서 매퍼 및 리듀서 실행 파일의 경로를 지정합니다. "adfsample/example/apps/wc.exe" 예에서 adfsample은 Blob 컨테이너, example/apps는 폴더, wc.exe는 실행 파일입니다.
8. **fileLinkedService** 속성에는 filePaths 섹션에 지정된 파일이 포함된 Azure 저장소를 나타내는 Azure 저장소 연결된 서비스를 지정합니다.
9. **arguments** 속성에는 스트리밍 작업의 인수를 지정합니다.
10. **getDebugInfo** 속성은 선택적 요소입니다. Failure로 설정되면 실패한 경우에만 로그가 다운로드됩니다. All로 설정되면 실행 상태에 관계 없이 로그가 항상 다운로드됩니다. 

	

<!---HONumber=Nov15_HO1-->