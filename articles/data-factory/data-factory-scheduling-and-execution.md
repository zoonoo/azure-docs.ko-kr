<properties 
	pageTitle="데이터 팩터리에서 예약 및 실행" 
	description="Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 알아봅니다." 
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
	ms.date="04/11/2016" 
	ms.author="spelluru"/>

# 데이터 팩터리에서 예약 및 실행
  
이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. 이 문서는 [파이프라인 만들기](data-factory-create-pipelines.md) 및 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 작성하며 사용자가 작업, 파이프라인, 연결된 서비스 및 데이터 집합과 같은 데이터 팩터리 응용 프로그램 모델의 기본 개념을 이해하고 있다고 가정합니다.

## 활동 예약

작업 JSON의 **scheduler** 섹션에서 작업에 대한 되풀이 일정을 지정할 수 있습니다. 예를 들어 1시간마다 작업을 다음과 같이 예약할 수 있습니다.

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![스케줄러 예제](./media/data-factory-scheduling-and-execution/scheduler-example.png)

위에 표시된 것과 같이 작업에 대한 일정을 지정하면 연속 창의 시리즈가 만들어집니다. 연속 창은 여러 창이 고정 크기로, 겹치지 않으며 연속적인 시간 간격으로 나타나는 것입니다. 작업에 대한 이러한 논리 연속 창은 **작업 창**이라고 합니다.
 
현재 실행 중인 작업 창의 경우 작업 창과 관련된 시간 간격은 작업 JSON에서 [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) 및 [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) 시스템 변수를 통해 액세스할 수 있습니다. 이러한 변수는 입력에서 데이터 선택, 출력 데이터 집합에서 시계열 데이터 표현 등 작업 JSON 및 작업에 연결된 스크립트에서 다양한 용도로 사용할 수 있습니다.

**스케줄러** 속성은 데이터 집합의 **가용성** 속성과 같은 하위 속성을 지원합니다. 특정 시간 오프셋에 예약, 작업 창에 대한 간격 시작 또는 끝에 처리에 맞게 모드 설정 등 스케줄러에서 제공되는 다양한 속성에 대한 자세한 내용은 [데이터 집합 가용성](data-factory-create-datasets.md#Availability) 문서를 참조하세요.

이번에는 활동에 대한 스케줄러 속성 지정이 선택 사항입니다. 이 속성을 지정할 경우 출력 데이터 집합 정의에 지정하는 빈도와 일치해야 합니다. 이번에는 출력 데이터 집합이 내용을 결정하므로 활동이 출력을 생성하지 않는 경우 출력 데이터 집합을 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 집합 만들기를 건너뛸 수 있습니다.

## 시계열 데이터 집합 및 데이터 조각

시계열 데이터는 일반적으로 시간 간격에 따라 생성되는 연속된 측정값으로 이루어진 연속적인 데이터 요소 시퀀스입니다. 시계열 데이터의 일반적인 예로는 센서 데이터, 응용 프로그램 원격 분석 데이터 등이 있습니다.

Azure Data Factory에서는 작업 실행을 통한 배치 방식으로 시계열 데이터를 처리할 수 있습니다. 일반적으로 입력 데이터가 도착하면 출력 데이터를 생성해야 하는 되풀이되는 상황이 있습니다. 이러한 상황은 데이터 집합에서 **availability** 섹션을 다음과 같이 지정하여 모델링합니다.

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

각 데이터 단위는 데이터 **조각**이라는 작업 실행으로 사용되고 생성됩니다. 아래 다이어그램에서는 availability가 매시간 빈도로 설정된 입력 시계열 데이터 집합과 출력 시계열 데이터 집합이 각각 있는 작업의 예를 보여줍니다.

![가용성 스케줄러](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

입력 및 출력 데이터 집합에 대한 매시간 데이터 조각은 위의 다이어그램에 표시됩니다. 다이어그램은 처리할 준비가 된 입력 조각 3개와 오전 10-11시에 진행 중인 작업 실행으로 오전 10-11시 출력 조각이 생성되는 것을 보여 줍니다.

현재 생성되는 조각에 연결된 시간 간격은 데이터 집합 JSON에서 [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 및 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 변수로 액세스할 수 있습니다.

현재 데이터 팩터리에서는 작업에 지정된 일정이 출력 데이터 집합의 가용성에 지정된 일정과 정확히 일치해야 합니다. 즉, WindowStart, WindowEnd와 SliceStart, SliceEnd가 항상 같은 기간과 단일 출력 조각으로 매핑됩니다.

availability 섹션에서 사용할 수 있는 다른 속성에 대한 자세한 내용은 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요.

## 샘플 – 데이터를 Azure SQL에서 Azure Blob로 이동하는 복사 작업

[파이프라인 만들기](data-factory-create-pipelines.md) 문서에 나와 있는 복사 작업에 대한 샘플을 다시 확인하여 코드를 합치고 실행하여 매시간 Azure SQL 테이블에서 Azure blob로 데이터를 복사해 보겠습니다.

**입력: Azure SQL 데이터 집합**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


**availability** 섹션에서 **frequency**는 **Hour**로, **interval**은 **1**로 설정합니다.

**출력: Azure Blob 데이터 집합**
	
	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
	            },
	            "partitionedBy": [
	                {
	                    "name": "Year",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "yyyy"
	                    }
	                },
	                {
	                    "name": "Month",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
	                    }
	                }
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}


**availability** 섹션에서 **frequency**는 **Hour**로, **interval**은 **1**로 설정합니다.



**작업: 복사 작업**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",	
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


위의 샘플은 availability 섹션이 매시간 빈도로 설정된 작업 일정과 데이터 집합을 보여줍니다. 이 샘플은 **WindowStart** 및 **WindowEnd** 변수를 활용하여 지정된 작업 실행을 위해 관련 데이터를 선택하고 적절한 동적 **folderPath** 매개 변수와 함께 이를 blob로 전송하여 매시간 폴더가 포함되도록 방법을 보여줍니다.

오전 8 – 11시 사이에 3개 조각이 실행되면 샘플 Azure 테이블 및 Blob처럼 표시됩니다.

Azure SQL에서는 데이터가 다음과 같다고 가정합니다.

![샘플 입력](./media/data-factory-scheduling-and-execution/sample-input-data.png)

위의 파이프라인을 배포하면 Azure blob는 다음과 같이 채워집니다.

1.	데이터가 포함된 mypath/2015/1/1/8/Data.<Guid>.txt 파일 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**참고:** <Guid>은(는) 실제 guid로 대체됩니다. 파일 이름 예: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	데이터가 포함된 mypath/2015/1/1/9/Data.<Guid>.txt 파일:

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	데이터가 포함된 mypath/2015/1/1/10/Data.<Guid>.txt 파일


## 데이터 조각, 파이프라인 및 조각 동시 실행에 대한 활성 기간

[파이프라인 만들기](data-factory-create-pipelines.md) 문서에서는 파이프라인의 **start** 및 **end** 속성으로 지정한 파이프라인의 활성 기간에 대한 개념을 소개했습니다.
 
과거 파이프라인 활성 기간에 대한 시작 날짜를 설정할 수 있으며 데이터 팩터리가 과거 모든 데이터 조각을 자동으로 계산(뒷면 채우기)하며 처리를 시작합니다.

뒷면 채우기된 데이터 조각으로 병렬 실행을 구성할 수 있습니다. [파이프라인 만들기](data-factory-create-pipelines.md) 문서에 나와 있는 것처럼 작업 JSON의 **정책** 섹션에서 **동시성** 속성을 설정하여 이 작업을 수행할 수 있습니다.

## 실패한 데이터 조각 및 자동 데이터 종속성 추적 다시 실행

시각적으로 풍부한 방식으로 조각의 실행을 모니터링할 수 있습니다. 자세한 내용은 [Azure 포털 블레이드](data-factory-monitor-manage-pipelines.md)**를 사용하여 파이프라인 모니터링 및 관리** (또는) [앱 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

두 활동을 보여주는 다음 예제를 살펴보세요. Activity1은 출력으로 조각이 포함된 시계열 데이터 집합을 생성하며 Activity2는 이것을 다시 입력으로 사용하여 최종 출력 시계열 데이터 집합을 생성합니다.

![실패한 조각](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

위의 다이어그램은 최근 3개 조각 중 **Dataset2**에 대한 오전 9-10시 조각을 생성하는 데 오류가 있음을 보여줍니다. 데이터 팩터리가 시계열 데이터 집합에 대한 종속성을 자동으로 추적하고 그 결과, 오전 9-10시 다운스트림 조각에 대한 작업 실행 시작을 보류합니다.


데이터 팩터리 모니터링 및 관리 도구를 사용하면 실패한 조각에 대한 진단 로그를 자세히 보고 문제에 대한 근본 원인을 쉽게 파악하여 해결할 수 있습니다. 문제를 해결했으면 작업 실행을 쉽게 다시 시작하여 실패한 조각을 생성할 수 있습니다. 다시 실행을 시작하고, 데이터 조각의 상태 전환을 이해하는 방법에 대한 자세한 내용은 [Azure 포털 블레이드](data-factory-monitor-manage-pipelines.md)**를 사용하여 파이프라인 모니터링 및 관리** (또는) [앱 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

다시 실행을 시작하고 dataset2에 대한 오전 9-10시 조각이 준비되었으면 아래 다이어그램처럼 데이터 팩터리가 최종 데이터 집합에서 오전 9-10시에 종속된 조각에 대한 실행을 시작합니다.

![실패한 조각 다시 실행](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

복잡한 활동 및 데이터 집합 체인에 대한 종속성 지정 및 종속성 추적에 대해 자세히 알아보려면 아래 섹션을 참조하세요.

## 활동 연결
한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 지정하여 두 활동을 연결할 수 있습니다. 활동은 동일한 파이프라인 또는 다른 파이프라인에 있을 수 있습니다. 두 번째 활동은 첫 번째 활동이 완료된 경우에만 실행됩니다.

예를 들어 다음과 같은 경우를 고려해 보겠습니다.
 
1.	파이프라인 P1에는 외부 입력 데이터 집합 D1이 필요하고 **출력** 데이터 집합 **D2**를 생성하는 활동 A1이 있습니다.
2.	파이프라인 P2에는 데이터 집합 **D2**의 **입력**이 필요하고 출력 데이터 집합 D3을 생성하는 활동 A2가 있습니다.
 
이 시나리오에서 활동 A1은 외부 데이터를 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 활동 A2는 D2에서 예약된 조각을 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 데이터 집합 A2의 조각 중 하나에 오류가 있으면 해당 조각을 사용할 수 있을 때까지 A2가 실행되지 않습니다.

다이어그램 뷰는 아래와 같습니다.

![두 개의 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

동일한 파이프라인에서 두 활동을 사용하는 다이어그램 뷰는 아래와 같습니다.

![동일한 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### 순서가 지정된 복사
순차/순서가 지정된 방식으로 하나씩 여러 복사 작업을 실행하는 것이 가능합니다. 파이프라인에 두 번의 복사 작업, 즉 다음과 같은 입력 데이터 출력 데이터 집합으로 된 CopyActivity1과 CopyActivity2가 있다고 가정합니다.

CopyActivity1: 입력: Dataset1 출력: Dataset2

CopyActivity2: 입력: Dataset2 출력: Dataset4

CopyActivity2는 CopyActivity1을 성공적으로 실행하고 Dataset2를 사용할 수 있는 경우에만 실행됩니다.

위의 예제에서 CopyActivity2에 다른 입력(예: Dataset3)을 지정할 수 있지만 CopyActivity2에 대한 입력으로 Dataset2를 지정해야 CopyActivity1을 완료할 때까지 작업이 실행되지 않습니다. 예:

CopyActivity1: 입력: Dataset1 출력: Dataset2

CopyActivity2: 입력: Dataset3, Dataset2 출력: Dataset4

여러 입력을 지정하는 경우 첫 번째 입력 데이터 집합만 데이터를 복사하는 데 사용되고 다른 데이터 집합은 종속성으로 사용됩니다. CopyActivity2는 다음 조건을 충족할 때만 실행을 시작합니다.

- CopyActivity2가 성공적으로 완료되고 Dataset2가 사용 가능합니다. 이 데이터 집합은 Dataset4에 데이터를 복사할 때 사용되지 않으며 CopyActivity2에 대한 일정 종속성으로만 작동합니다.   
- Dataset3을 사용할 수 있습니다. 이 데이터 집합은 대상에 복사되는 데이터를 나타냅니다.  



## 다양한 빈도로 데이터 집합 모델링

위에 표시된 샘플에서 입력 및 출력 데이터 집합과 작업 일정 창에 대한 빈도는 동일합니다. 일부 시나리오에서는 하나 이상의 입력 빈도와 다른 빈도로 출력을 생성하는 기능이 필요합니다. 데이터 팩터리가 이러한 시나리오의 모델링을 지원합니다.

### 샘플 1: 매시간 제공되는 입력 데이터에 대해 일별 출력 보고서 생성

Azure Blob에서 매시간 사용 가능한 센서로부터 측정값 데이터가 입력되고 데이터 팩터리 [Hive 작업](data-factory-hive-activity.md)으로 그 날에 대한 평균, 최대, 최소 등과 같은 통계를 포함하는 집계 보고서를 일별로 생성하려는 시나리오를 고려해 보세요.

다음은 데이터 팩터리로 모델링하는 방법입니다.

**입력 Azure blob 데이터 집합:**

매시간 입력 파일이 지정된 날에 대한 폴더에서 삭제됩니다. 입력에 대한 Availability가 Hourly로 설정됩니다(frequency: Hour, interval: 1).

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**출력 Azure blob 데이터 집합**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 삭제됩니다. 출력의 Availability는 Daily로 설정됩니다(frequency: Day 및 interval: 1).


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**작업: 파이프라인에서 Hive 작업**

hive 스크립트는 적절한 datetime 정보를 매개 변수로 받아 **WindowStart** 변수를 아래와 같이 활용합니다. hive 스크립트는 이 변수를 사용하여 그 날의 해당 폴더에서 데이터를 로드하고 집계를 실행하여 출력을 생성합니다.

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

다음은 데이터 종속성 관점에서 어떻게 표시되는지를 보여줍니다.

![데이터 종속성](./media/data-factory-scheduling-and-execution/data-dependency.png)

매일 출력 조각은 입력 데이터 집합에서 24시간 조각에 따라 달라집니다. 데이터 팩터리는 동일한 기간에 속하는 입력 데이터 조각을 생성할 출력 조각으로 파악하여 이러한 종속성을 자동으로 계산합니다. 24개의 입력 조각 모두를 사용할 수 없는 경우(예: 해당 조각을 생성하는 작업 업스트림에서 처리가 발생) 데이터 팩터리는 입력 조각이 준비될 때까지 기다렸다가 일별 작업 실행을 시작합니다.


### 샘플 2: 식 및 데이터 팩터리 함수로 종속성 지정

다른 시나리오를 살펴보겠습니다. 두 개의 입력 데이터 집합을 처리하는 Hive 작업이 있다고 가정합니다. 그 중 하나는 매일 새 데이터가 제공되지만 다른 하나는 매주 새 데이터를 가져옵니다. 두 입력에 조인을 수행하여 매일 출력을 생성하고 싶습니다.
 
데이터 팩터리가 출력 데이터 조각의 기간에 맞게 입력 데이터 조각을 포함시켜 처리할 적절한 입력 조각을 자동으로 확인하는 방법이 지금까지 단순한 접근법이었지만 이것은 더 이상 작동하지 않습니다.

데이터 팩터리에서 매주 입력 데이터 집합에 대해 마지막 주의 데이터 조각을 사용하도록 모든 작업 실행에 대해 지정할 방법이 필요합니다. 아래와 같이 Azure Data Factory 함수를 이용하여 이를 수행할 수 있습니다.

**입력1: Azure Blob**

첫 번째 입력은 **매일** 업데이트된 Azure blob입니다.
	
	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**입력2: Azure Blob**

Input2는 **매주** 업데이트된 Azure blob입니다.

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**출력: Azure Blob**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 삭제됩니다. 출력의 Availability는 Daily로 설정됩니다(frequency: Day, interval: 1).
	
	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**작업: 파이프라인에서 Hive 작업**

hive 작업에서는 2개의 입력을 받아 매일 출력 조각을 생성합니다. 다음과 같이 매주 입력에 대한 마지막 주의 입력 조각에 따라 매일 출력 조각을 지정할 수 있습니다.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   } 
	     ]
	   }
	}


## 데이터 팩터리 함수 및 시스템 변수   

Azure Data Factory에서 지원하는 함수 및 시스템 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md) 문서를 참조하세요.

## 데이터 종속성 자세히 알아보기

작업 실행으로 데이터 집합 조각을 생성하기 위해 데이터 팩터리는 다음 **종속성 모델**을 사용하여 작업 시 사용하는 데이터 집합과 작업으로 생성되는 데이터 집합 간의 관계를 결정합니다.

출력 데이터 집합 조각을 생성하는 데 필요한 입력 데이터 집합의 시간 범위를 **종속성 기간**이라고 합니다.

작업 실행 시 입력 데이터 집합에 있는 데이터 조각이 종속성 기간 내에 사용 가능한 경우에만 데이터 집합 조각이 생성됩니다. 즉, 종속성 기간을 구성하는 모든 입력 조각은 작업 실행 시 생성되는 출력 데이터 집합 조각에 대해 **Ready** 상태에 있어야 합니다.

데이터 집합 조각 [start, end]을 생성하려면 함수에서 데이터 집합 조각을 종속성 기간에 매핑해야 합니다. 이 함수는 기본적으로 데이터 집합 조각의 시작 및 끝을 종속성 기간의 시작 및 끝으로 변환하는 수식입니다. 공식적으로 표현하자면,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

여기서 f 및 g는 각 작업 입력에 대한 종속성 기간의 시작과 끝을 계산하는 매핑 함수입니다.

위의 샘플에서 본 것처럼 대부분의 경우 종속성 기간은 생성할 데이터 조각의 기간과 같습니다. 이 경우 데이터 팩터리는 종속성 기간에 속하는 입력 조각을 자동으로 계산합니다.

예: 출력이 매일 생성되고 입력 데이터가 매시간 제공되는 위의 집계 샘플에서 데이터 조각 기간은 24시간입니다. 데이터 팩터리는 이 기간에 대한 관련 입력 조각을 매시간 찾고 입력 조각에 따라 출력 조각을 만듭니다.

또한 한 개의 입력이 매주이고 출력 조각이 매일 생성되는 위의 샘플에 표시된 것처럼 종속성 기간에 대해 고유한 매핑을 제공할 수도 있습니다.
   
## 데이터 종속성 및 유효성 검사

데이터 집합에는 조각 실행으로 생성된 데이터를 사용하기 전에 유효성 검사하는 방법을 지정하는 유효성 검사 정책이 선택적으로 정의되어 있을 수 있습니다. 세부 정보는 [데이터 집합 만들기](data-factory-create-datasets.md) 문서를 참조하세요.

이러한 경우 조각의 실행이 완료되면 출력 조각 상태가 **Validation**의 하위 상태가 있는 **Waiting**으로 변경됩니다. 조각의 유효성이 검사되었으면 조각 상태는 **Ready**로 변경됩니다.
   
데이터 조각이 생성되었으나 유효성 검사를 통과하지 못하면 유효성 검사에 실패한 조각에 따라 작업 실행한 다운스트림 조각은 처리되지 않습니다.

데이터 팩터리에 있는 데이터 조각의 다양한 상태는 [파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 문서에서 설명합니다.

## 외부 데이터

데이터 집합을 외부(아래 JSON에 표시)로 표시할 수 있으며 이는 Azure Data Factory에서 생성되지 않았음을 의미합니다. 이러한 경우 데이터 집합 정책은 데이터 집합에 대한 유효성 검사 및 재시도 정책을 설명하는 추가 매개 변수 집합을 포함할 수 있습니다. 모든 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요.

데이터 팩터리에서 생성하는 데이터 집합과 마찬가지로 외부 데이터에 대한 데이터 조각도 종속 조각을 처리하기 전에 준비되어야 합니다.

	{
		"name": "AzureSqlInput",
		"properties": 
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties": 
			{
				"tableName": "MyTable"	
			},
			"availability": 
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy": 
			{
				"externalData": 
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		} 
	} 


## 일회성 파이프라인
파이프라인 정의 내에 지정한 시작 및 종료 시간 내에서 정기적(매시간, 매일 등)으로 실행되도록 파이프라인을 만들고 일정을 예약할 수 있습니다. 자세한 내용은 [활동 예약](#scheduling-and-execution)을 참고하세요. 한 번만 실행되는 파이프라인을 만들 수도 있습니다. 이렇게 하려면, 파이프라인 정의 내에서 **pipelineMode** 속성을 **onetime**으로 설정합니다(아래 JSON 샘플 참조). 이 속성의 기본값은 **scheduled**입니다.

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

다음 사항에 유의하세요.
 
- 파이프라인에 대한 **시작** 및 **종료** 시간을 지정할 필요가 없습니다. 
- 데이터 팩터리에서 이 값이 사용되지 않더라도 이 때는 입력 및 출력 데이터 집합의 가용성(빈도 및 간격)을 지정해야 합니다.  
- 다이어그램 뷰는 일회성 파이프라인을 표시하지 않습니다. 의도적인 작동입니다. 
- 일회성 파이프라인은 업데이트할 수 없습니다. 일회성 파이프라인을 복제하고, 이름을 변경하고, 속성을 업데이트하고, 배포하여 또 다른 파이프라인을 만들 수 있습니다. 

  




  









 
 












      

  

<!---HONumber=AcomDC_0525_2016-->