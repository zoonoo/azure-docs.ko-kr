<properties 
	pageTitle="Pig 작업" 
	description="Azure Data Factory에서 Pig 작업을 사용하여 주문형/사용자 고유의 HDInsight 클러스터에서 Pig 스크립트를 실행하는 방법을 알아봅니다." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Pig 작업

데이터 공장 [파이프라인](data-factory-create-pipelines.md)에서 HDInsight Pig 활동은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) 또는 [주문형](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux 기반 HDInsight 클러스터의 Pig 쿼리를 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 작업의 일반적인 개요를 설명하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서를 기반으로 작성되었습니다.

## 구문

	{
		"name": "HiveActivitySamplePipeline",
	  	"properties": {
	    "activities": [
	    	{
	        	"name": "Pig Activity",
	        	"description": "description",
	        	"type": "HDInsightPig",
	        	"inputs": [
	          		{
	            		"name": "input tables"
	          		}
	        	],
	        	"outputs": [
	          		{
	            		"name": "output tables"
	          		}
        		],
	        	"linkedServiceName": "MyHDInsightLinkedService",
	        	"typeProperties": {
	          		"script": "Pig script",
	          		"scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
	          		"defines": {
	            		"param1": "param1Value"
	          		}
	        	},
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
	      	}
	    ]
	  }
	}

## 구문 세부 정보

속성 | 설명 | 필수
-------- | ----------- | --------
name | 작업의 이름 | 예
description | 작업이 무엇에 사용되는지 설명하는 텍스트입니다. | 아니요
type | HDinsightPig | 예
inputs | Hive 작업에서 사용하는 입력 | 아니요
outputs | Pig 작업에서 생성하는 출력 | 예
linkedServiceName | 데이터 팩토리에서 연결된 서비스로 등록된 HDInsight 클러스터에 대한 참조 | 예
script | Pig 스크립트 인라인 지정 | 아니요
script path | Pig 스크립트를 Azure blob 저장소에 저장하고 파일에 대한 경로를 제공합니다. 'script' 또는 'scriptPath' 속성을 사용합니다. 둘 모두를 사용할 수는 없습니다. | 아니요
defines | Pig 스크립트 내에서 참조하기 위해 매개 변수를 키/값 쌍으로 지정 | 아니요

## 예

회사에서 출시한 게임을 사용자가 플레이한 시간을 파악하려는 게임 로그 분석의 예를 살펴보겠습니다.
 
아래은 쉼표(,)로 구분되고 ProfileID, SessionStart, Duration, SrcIPAddress 및 GameType 필드를 포함하는 샘플 게임 로그입니다.

	1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
	1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
	1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
	1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
	.....

이 데이터를 처리하는 **Pig 스크립트**는 다음과 같습니다.

	PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
	
	GroupProfile = Group PigSampleIn all;
	
	PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
	
	Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

데이터 팩터리 파이프라인에서 이 Pig 스크립트를 실행하려면 다음을 수행해야 합니다

1. 연결된 서비스를 만들어 [자체적인 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-linked-service)를 등록하거나 [주문형 HDInsight 컴퓨팅 클러스터](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 구성합니다. 이 연결된 서비스를 "HDInsightLinkedService"라고 하겠습니다.
2.	데이터를 호스팅하는 Azure Blob 저장소로의 연결을 구성하기 위해 [연결된 서비스](data-factory-azure-storage-connector.md)를 만듭니다. 이 연결된 서비스를 "StorageLinkedService"라고 하겠습니다.
3.	입력 및 출력 데이터를 가르키는 [데이터 집합](data-factory-create-datasets.md)을 만듭니다. 입력 데이터 집합을 "PigSampleIn"이라고 하고 출력 데이터 집합을 "PigSampleOut"이라고 하겠습니다.
4.	위의 2단계에서 구성된 Azure Blob 저장소에 Pig 쿼리를 파일로 복사합니다. 데이터를 호스팅하는 연결된 서비스가 이 쿼리 파일을 호스트하는 연결된 서비스와 다른 경우 서비스에 연결된 별도 Azure 저장소를 만들고 작업 구성에서 이를 참조합니다. **scriptPath**을 사용하여 Pig 스크립트 파일에 대한 경로를 지정하고 **scriptLinkedService**를 사용하여 스크립트 파일을 포함하는 Azure 저장소를 지정합니다.
	
	> [AZURE.NOTE]**스크립트** 속성을 사용하여 활동 정의에서 Pig 스크립트를 인라인으로 입력할 수도 있지만 JSON 문서 내에서 스크립트의 모든 특수 문자가 이스케이프되어야 하고 디버깅 문제를 발생킬 수 있기 때문에 권장되지 않습니다. 모법 사례는 4단계를 수행하는 것입니다.
5. HDInsightPig 작업으로 아래 파이프라인을 만들어 데이터를 처리합니다.

		{
		  "name": "PigActivitySamplePipeline",
		  "properties": {
		    "activities": [
		      {
		        "name": "PigActivitySample",
		        "type": "HDInsightPig",
		        "inputs": [
		          {
		            "name": "PigSampleIn"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "PigSampleOut"
		          }
		        ],
		        "linkedServiceName": "HDInsightLinkedService",
		        "typeproperties": {
		          "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
		          "scriptLinkedService": "StorageLinkedService"
		        },
       			"scheduler": {
          			"frequency": "Day",
          			"interval": 1
        		}
		      }
		    ]
		  }
		} 
6. 파이프라인을 배포합니다. 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md) 문서를 참조하세요. 
7. 데이터 팩터리 모니터링 및 관리 보기를 사용하여 파이프라인을 모니터링합니다. 자세한 내용은 [데이터 팩터리 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서를 참조하세요.

## 정의 요소를 사용하여 Pig 스크립트의 매개 변수 지정

게임 로그가 Azure Blob 저장소에 매일 수집되고 날짜 및 시간으로 분할된 폴더에 저장되는 경우의 예를 들어 보겠습니다. Pig 스크립트에 매개 변수를 사용하여 런타임 동안 입력 폴더 위치를 동적으로 전달하며 날짜 및 시간으로 분할된 출력을 생성하려고 합니다.
 
매개 변수가 있는 Pig 스크립트를 사용하려면 다음을 수행합니다.

- **defines**에서 매개 변수를 정의합니다.

		{
			"name": "PigActivitySamplePipeline",
		  	"properties": {
		    "activities": [
		    	{
		        	"name": "PigActivitySample",
		        	"type": "HDInsightPig",
		        	"inputs": [
		          		{
		            		"name": "PigSampleIn"
		          		}
		        	],
		        	"outputs": [
		          		{
		            		"name": "PigSampleOut"
		          		}
		        	],
		        	"linkedServiceName": "HDInsightLinkedService",
		        	"typeproperties": {
		          		"scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
		          		"scriptLinkedService": "StorageLinkedService",
		          		"defines": {
		            		"Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
				            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
		          		}
		        	},
       				"scheduler": {
          				"frequency": "Day",
	          			"interval": 1
    	    		}
		      	}
		    ]
		  }
		}  
	  
- Pig 스크립트에서 다음 예와 같이 '**$parameterName**'을 사용하여 매개 변수를 참조합니다.

		PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);	
		GroupProfile = Group PigSampleIn all;		
		PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);		
		Store PigSampleOut into '$Output' USING PigStorage (','); 

<!---HONumber=Nov15_HO3-->