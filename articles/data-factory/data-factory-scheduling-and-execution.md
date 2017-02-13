---
title: "Data Factory에서 예약 및 실행 | Microsoft Docs"
description: "Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: febc8fef864f88fa07accf91efc9b87727a48b32
ms.openlocfilehash: 8b1029075178fbc591645a5fd6a112ad0a7f8b86


---
# <a name="data-factory-scheduling-and-execution"></a>Data Factory 예약 및 실행
이 문서에서는 Azure Data Factory 응용 프로그램 모델의 예약 및 실행에 대한 내용을 설명합니다. 

## <a name="prerequisites"></a>필수 조건
이 문서는 사용자가 작업, 파이프라인, 연결된 서비스 및 데이터 집합과 같은 Data Factory 응용 프로그램 모델 개념을 이해하고 있다고 가정합니다. Azure Data Factory의 기본 개념은 다음 문서를 참조하세요.

* [데이터 팩터리 소개](data-factory-introduction.md)
* [파이프라인](data-factory-create-pipelines.md)
* [데이터 집합](data-factory-create-datasets.md) 

## <a name="schedule-an-activity"></a>작업 예약
작업 JSON의 scheduler 섹션에서 작업에 대한 되풀이 일정을 지정할 수 있습니다. 예를 들어 1시간마다 작업을 다음과 같이 예약할 수 있습니다.

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},  
```

![스케줄러 예제](./media/data-factory-scheduling-and-execution/scheduler-example.png)

다이어그램에 표시된 것과 같이 작업에 대한 일정을 지정하면 연속 창의 시리즈가 만들어집니다. 연속 창은 여러 창이 고정 크기로, 겹치지 않으며 연속적인 시간 간격으로 나타나는 것입니다. 작업에 대한 이러한 논리 연속 창은 *작업 창*이라고 합니다.

현재 실행 중인 작업 창의 작업 JSON에서 [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) 및 [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) 시스템 변수를 통해 작업 창과 관련된 시간 간격에 액세스할 수 있습니다. 이러한 변수를 작업 JSON에서 다양한 용도로 사용할 수 있습니다. 예를 들어 입력에서 데이터 선택, 출력 데이터 집합에서 시계열 데이터를 표현하는 데 사용할 수 있습니다.

**scheduler** 속성은 데이터 집합의 **availability** 속성과 같은 하위 속성을 지원합니다. 세부 정보는 [데이터 집합 가용성](data-factory-create-datasets.md#Availability) 을 참조하세요. 예: 특정 시간 오프셋에 예약, 작업 창에 대한 간격 시작 또는 끝에 처리에 맞게 모드 설정 등.

작업에 대한 **scheduler** 속성은 지정할 수 있지만 **선택 사항**입니다. 속성을 지정할 경우 출력 데이터 집합 정의에 지정하는 빈도와 일치해야 합니다. 현재 출력 데이터 집합이 일정을 결정하므로 작업이 출력을 생성하지 않는 경우 출력 데이터 집합을 만들어야 합니다. 활동이 입력을 가져오지 않으면 입력 데이터 집합 만들기를 건너뛸 수 있습니다.

## <a name="time-series-datasets-and-data-slices"></a>시계열 데이터 집합 및 데이터 조각
시계열 데이터는 일반적으로 시간 간격에 따라 생성되는 연속된 측정값으로 이루어진 연속적인 데이터 요소 시퀀스입니다. 시계열 데이터의 일반적인 예로는 센서 데이터 및 응용 프로그램 원격 분석 데이터가 있습니다.

Data Factory에서는 작업 실행을 통한 배치 방식으로 시계열 데이터를 처리할 수 있습니다. 일반적으로 입력 데이터가 도착하면 출력 데이터를 생성해야 하는 되풀이되는 상황이 있습니다. 이러한 상황은 데이터 집합에서 **availability** 를 다음과 같이 지정하여 모델링합니다.

```json
"availability": {
  "frequency": "Hour",
  "interval": 1
},
```

각 데이터 단위는 데이터 조각이라는 작업 실행으로 사용되고 생성됩니다. 다음 다이어그램에는 입력 데이터 집합과 출력 데이터 집합이 하나씩 포함된 활동의 예가 나와 있습니다. 이러한 데이터 집합의 **가용성** 집합 빈도는 매시간으로 설정됩니다.

![가용성 스케줄러](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

위의 다이어그램은 입력 및 출력 데이터 집합에 대한 매시간 데이터 조각을 보여 줍니다. 다이어그램은 처리할 준비가 된 입력 조각 3개를 보여 줍니다. 오전 10-11시 작업이 진행 중이며 오전 10-11시 출력 조각이 생성되고 있습니다.

[SliceStart](data-factory-functions-variables.md#data-factory-system-variables) 및 [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) 변수를 통해 데이터 집합 JSON에서 생성되고 있는 현재 조각에 연결된 시간 간격에 액세스할 수 있습니다.

현재 Data Factory에서는 작업에 지정된 일정이 출력 데이터 집합의 **가용성** 에 지정된 일정과 정확히 일치해야 합니다. 따라서 **WindowStart**, **WindowEnd**, **SliceStart** 및 **SliceEnd**가 항상 같은 기간과 단일 출력 조각으로 매핑됩니다.

availability 섹션에서 사용할 수 있는 다른 속성에 대한 자세한 내용은 [데이터 집합 만들기](data-factory-create-datasets.md)를 참조하세요.

## <a name="move-data-from-sql-database-to-blob-storage"></a>SQL Database에서 Blob 저장소로 데이터 이동
매시간 Azure SQL Database 테이블에서 Azure Blob 저장소로 데이터를 복사하는 파이프라인을 만들어 코드를 합치고 실행해 보겠습니다.

**입력: Azure SQL Database 데이터 집합**

```json
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
```

availability 섹션에서 **frequency**는 **Hour**로, **interval**은 **1**로 설정됩니다.

**출력: Azure Blob 저장소 데이터 집합**

```json
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
```

availability 섹션에서 **frequency**는 **Hour**로, **interval**은 **1**로 설정됩니다.

**작업: 복사 작업**

```json
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
```

이 샘플에서는 availability 섹션이 매시간 빈도로 설정된 작업 일정과 데이터 집합을 보여 주는데, **WindowStart** 및 **WindowEnd**를 사용하여 작업 실행에 관련된 데이터를 선택하고 적절한 **folderPath**의 Blob로 이 데이터를 복사하는 방식을 보여 줍니다. **folderPath** 는 매시간 별도의 폴더를 포함하도록 매개 변수화됩니다.

오전 8-11시 사이에 3개 조각이 실행되면 Azure SQL Database의 데이터는 다음과 같습니다.

![샘플 입력](./media/data-factory-scheduling-and-execution/sample-input-data.png)

파이프라인이 배포된 후 Azure Blob은 다음과 같이 채워집니다.

* 다음 데이터가 포함된 mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt 파일:
    ```  
    10002345,334,2,2015-01-01 08:24:00.3130000
    10002345,347,15,2015-01-01 08:24:00.6570000
    10991568,2,7,2015-01-01 08:56:34.5300000
    ```
  
  > [!NOTE]
  > &lt;Guid&gt;는 실제 guid로 대체됩니다. 파일 이름 예: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
  > 
  > 
* 다음 데이터가 포함된 mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt 파일:

    ```json  
    10002345,334,1,2015-01-01 09:13:00.3900000
    24379245,569,23,2015-01-01 09:25:00.3130000
    16777799,21,115,2015-01-01 09:47:34.3130000
    ```
* 데이터가 없는 mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt 파일

## <a name="active-period-for-pipeline"></a>파이프라인에 대한 활성 기간
[파이프라인 만들기](data-factory-create-pipelines.md)에서는 파이프라인의 **start** 및 **end** 속성을 설정하여 지정한 파이프라인의 활성 기간에 대한 개념을 소개했습니다.

과거에 파이프라인 활성 기간에 대한 시작 날짜를 설정할 수 있습니다. Data Factory에서 과거의 모든 데이터 조각을 자동으로 계산(뒷면 채우기)하고 처리를 시작합니다.

## <a name="parallel-processing-of-data-slices"></a>데이터 조각의 병렬 처리
작업 JSON의 정책 섹션에서 **동시성** 속성을 설정하여 뒷면 채우기된 데이터 조각이 병렬로 실행되도록 구성할 수 있습니다. 이 속성에 대한 자세한 내용은 [파이프라인 만들기](data-factory-create-pipelines.md)를 참조하세요.

## <a name="rerun-a-failed-data-slice"></a>실패한 데이터 조각 다시 실행
시각적으로 풍부한 방식으로 조각의 실행을 모니터링할 수 있습니다. 자세한 내용은 [Azure 포털 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 또는 [앱 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

두 활동을 보여주는 다음 예제를 살펴보세요. Activity1은 출력으로 조각이 포함된 시계열 데이터 집합을 생성하며 Activity2는 이것을 다시 입력으로 사용하여 최종 출력 시계열 데이터 집합을 생성합니다.

![실패한 조각](./media/data-factory-scheduling-and-execution/failed-slice.png)

다이어그램은 최근 3개 조각 중 Dataset2에 대한 오전 9-10시 조각을 생성하는 데 오류가 있음을 보여줍니다. Data Factory는 시계열 데이터 집합에 대한 종속성을 자동으로 추적합니다. 따라서 오전 9-10시 다운스트림 조각에 대한 작업 실행은 시작하지 않습니다.

Data Factory 모니터링 및 관리 도구를 사용하면 실패한 조각에 대한 진단 로그를 자세히 보고 문제에 대한 근본 원인을 쉽게 파악하여 해결할 수 있습니다. 문제를 해결했으면 작업 실행을 쉽게 시작하여 실패한 조각을 생성할 수 있습니다. 데이터 조각의 상태 전환을 다시 실행하고 이해하는 방법에 대한 자세한 내용은 [Azure 포털 블레이드를 사용하여 파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 또는 [앱 모니터링 및 관리](data-factory-monitor-manage-app.md)를 참조하세요.

**Dataset2**에 대한 오전 9-10시 조각을 다시 실행한 후 Data Factory는 최종 데이터 집합에서 오전 9-10시 종속 조각에 대한 실행을 시작합니다.

![실패한 조각 다시 실행](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="run-activities-in-a-sequence"></a>시퀀스에서 활동 실행
한 활동의 출력 데이터 집합을 다른 활동의 입력 데이터 집합으로 설정하여 두 활동을 연결하면 해당 활동을 차례로 실행할 수 있습니다. 활동은 동일한 파이프라인 또는 다른 파이프라인에 있을 수 있습니다. 두 번째 활동은 첫 번째 활동이 완료된 경우에만 실행됩니다.

예를 들어 다음과 같은 경우를 고려해 보겠습니다.

1. 파이프라인 P1에는 외부 입력 데이터 집합 D1이 필요하고 출력 데이터 집합 D2를 생성하는 작업 A1이 있습니다.
2. 파이프라인 P2에는 데이터 집합 D2의 입력이 필요하며 출력 데이터 집합 D3을 생성하는 활동 A2가 있습니다.

이 시나리오에서는 활동 A1과 A2가 서로 다른 파이프라인에 있습니다. 활동 A1은 외부 데이터를 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 활동 A2는 D2에서 예약된 조각을 사용할 수 있고 예약된 가용성 빈도에 도달할 때 실행됩니다. 데이터 집합 D2의 조각 중 하나에 오류가 있으면 해당 조각을 사용할 수 있을 때까지 A2가 실행되지 않습니다.

다이어그램 뷰는 다음 다이어그램과 같습니다.

![두 개의 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

앞에서 설명한 것처럼, 두 활동이 동일한 파이프라인에 있을 수 있습니다. 동일한 파이프라인에서 두 활동을 사용하는 다이어그램 뷰는 다음 다이어그램과 같습니다.

![동일한 파이프라인에서 활동 연결](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### <a name="copy-sequentially"></a>순차적으로 복사
순차/순서가 지정된 방식으로 하나씩 여러 복사 작업을 실행하는 것이 가능합니다. 예를 들어 파이프라인에 두 개의 복사 활동, 즉 다음과 같은 입력 데이터 및 출력 데이터 집합을 포함하는 CopyActivity1과 CopyActivity2가 있을 수 있습니다.   

CopyActivity1

입력: Dataset1. 출력: Dataset2.

CopyActivity2

입력: Dataset2.  출력: Dataset3.

CopyActivity2는 CopyActivity1을 성공적으로 실행하고 Dataset2를 사용할 수 있는 경우에만 실행됩니다.

샘플 파이프라인 JSON은 다음과 같습니다.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

예제에서는 첫 번째 복사 활동의 출력 데이터 집합(Dataset2)이 두 번째 활동의 입력으로 지정되어 있습니다. 따라서 첫 번째 활동의 출력 데이터 집합이 준비되어야 두 번째 활동이 실행됩니다.  

예제에서 CopyActivity2에 다른 입력(예: Dataset3)을 지정할 수 있지만 CopyActivity2에 대한 입력으로 Dataset2를 지정해야 CopyActivity1을 끝낼 때까지 작업이 실행되지 않습니다. 예:

CopyActivity1

입력: Dataset1. 출력: Dataset2.

CopyActivity2

입력: Dataset3, Dataset2. 출력: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

예제에서는 두 번째 복사 활동에 대해 입력 데이터 집합 두 개가 지정되어 있습니다. 여러 입력을 지정하는 경우 첫 번째 입력 데이터 집합만 데이터를 복사하는 데 사용되고 다른 데이터 집합은 종속성으로 사용됩니다. CopyActivity2는 다음 조건을 충족한 후에만 시작합니다.

* CopyActivity1이 성공적으로 완료되고 Dataset2가 사용 가능합니다. 이 데이터 집합은 Dataset4에 데이터를 복사할 때 사용되지 않습니다. CopyActivity2에 대한 일정 종속성으로만 작동합니다.   
* Dataset3을 사용할 수 있습니다. 이 데이터 집합은 대상에 복사되는 데이터를 나타냅니다.  

## <a name="model-datasets-with-different-frequencies"></a>다양한 빈도로 데이터 집합 모델링
샘플에서 입력 및 출력 데이터 집합과 작업 일정 창에 대한 빈도는 동일합니다. 일부 시나리오에서는 하나 이상의 입력 빈도와 다른 빈도로 출력을 생성하는 기능이 필요합니다. Data Factory가 이러한 시나리오의 모델링을 지원합니다.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>샘플 1: 매시간 제공되는 입력 데이터에 대해 일별 출력 보고서 생성
Azure Blob 저장소에서 매시간 사용 가능한 센서로부터 입력 측정값 데이터가 있는 시나리오를 살펴보겠습니다. [Data Factory hive 작업](data-factory-hive-activity.md)을 사용하여 평균, 최대값, 최소값 등 특정일의 통계가 포함된 일별 집계 보고서를 생성하려고 합니다.

다음은 Data Factory로 이 시나리오를 모델링하는 방법입니다.

**입력 데이터 집합**

매시간 입력 파일이 지정된 날에 대한 폴더에서 삭제됩니다. 입력에 대한 Availability가 **Hour** 로 설정됩니다(frequency: Hour, interval: 1).

```json
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
```
**출력 데이터 집합**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 생성됩니다. 출력의 Availability는 **Day** 로 설정됩니다(frequency: Day 및 interval: 1).

```json
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
```

**작업: 파이프라인에서 hive 작업**

hive 스크립트는 적절한 *DateTime* 정보를 매개 변수로 받아 **WindowStart** 변수를 다음 코드 조각에 표시된 대로 사용합니다. hive 스크립트는 이 변수를 사용하여 그 날의 정확한 폴더에서 데이터를 로드하고 집계를 실행하여 출력을 생성합니다.

```json
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
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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
```

다음 다이어그램은 데이터 종속성 관점에서 시나리오를 보여 줍니다.

![데이터 종속성](./media/data-factory-scheduling-and-execution/data-dependency.png)

매일 출력 조각은 입력 데이터 집합에서 24시간 조각에 따라 달라집니다. Data Factory는 동일한 기간에 속하는 입력 데이터 조각을 생성할 출력 조각으로 파악하여 이러한 종속성을 자동으로 계산합니다. 24개의 입력 조각 모두를 사용할 수 없는 경우 Data Factory는 입력 조각이 준비될 때까지 기다렸다가 일별 작업 실행을 시작합니다.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>샘플 2: 식 및 데이터 Data Factory 함수로 종속성 지정
다른 시나리오를 살펴보겠습니다. 두 개의 입력 데이터 집합을 처리하는 hive 작업이 있다고 가정합니다. 그 중 하나는 매일 새 데이터가 제공되지만 다른 하나는 매주 새 데이터를 가져옵니다. 두 입력에 조인을 수행하여 매일 출력을 생성하고 싶습니다.

Data Factory가 출력 데이터 조각의 기간에 맞추어 처리할 적절한 입력 조각을 자동으로 확인하는 단순한 접근법은 작동하지 않습니다.

모든 작업 실행에 대해 지정해야 하며 Data Factory에서 매주 입력 데이터 집합에 대해 마지막 주의 데이터 조각을 사용해야 합니다. 다음 코드 조각에 나와 있는 것처럼 Azure Data Factory 함수를 사용하여 이 동작을 구현할 수 있습니다.

**입력1: Azure Blob**

첫 번째 입력은 매일 업데이트된 Azure Blob입니다.

```json
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
```

**입력2: Azure Blob**

입력2는 매주 업데이트된 Azure Blob입니다.

```json
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
```

**출력: Azure Blob**

하나의 출력 파일이 그 날에 대한 폴더에서 매일 생성됩니다. 출력의 Availability는 **day** 로 설정됩니다(frequency: Day, interval: 1).

```json
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
```

**작업: 파이프라인에서 hive 작업**

hive 작업에서는 2개의 입력을 받아 매일 출력 조각을 생성합니다. 다음과 같이 매주 입력에 대한 이전 주의 입력 조각에 따라 매일 출력 조각을 지정할 수 있습니다.

```json
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
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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
```

## <a name="data-factory-functions-and-system-variables"></a>데이터 팩터리 함수 및 시스템 변수
Data Factory에서 지원하는 함수 및 시스템 변수 목록은 [Data Factory 함수 및 시스템 변수](data-factory-functions-variables.md) 를 참조하세요.

## <a name="data-dependency-deep-dive"></a>데이터 종속성 자세히 알아보기
작업 실행으로 데이터 집합 조각을 생성하기 위해 Data Factory는 다음 *종속성 모델* 을 사용하여 작업에 사용되는 데이터 집합과 작업으로 생성되는 데이터 집합 간의 관계를 결정합니다.

출력 데이터 집합 조각을 생성하는 데 필요한 입력 데이터 집합의 시간 범위를 *종속성 기간*이라고 합니다.

작업 실행 시 입력 데이터 집합에 있는 데이터 조각이 종속성 기간 내에 사용 가능한 경우에만 데이터 집합 조각이 생성됩니다. 즉, 종속성 기간을 구성하는 모든 입력 조각은 출력 데이터 집합 조각을 생성하는 작업 실행에 대해 **Ready** 상태에 있어야 합니다.

데이터 집합 조각 [**start**, **end**]을 생성하려면 함수에서 데이터 집합 조각을 종속성 기간에 매핑해야 합니다. 이 함수는 기본적으로 데이터 집합 조각의 시작 및 끝을 종속성 기간의 시작 및 끝으로 변환하는 수식입니다. 공식적으로 표현하자면 다음과 같습니다.

```
DatasetSlice = [start, end]
DependecyPeriod = [f(start, end), g(start, end)]
```

여기서 **F** 및 **g**는 각 작업 입력에 대한 종속성 기간의 시작과 끝을 계산하는 매핑 함수입니다.

샘플에서 본 것처럼 종속성 기간은 생성된 데이터 조각의 기간과 같습니다. 이 경우 Data Factory는 종속성 기간에 속하는 입력 조각을 자동으로 계산합니다.  

예를 들어 출력이 매일 생성되고 입력 데이터가 매시간 제공되는 집계 샘플에서 데이터 조각 기간은 24시간입니다. Data Factory는 이 기간에 대한 관련 입력 조각을 매시간 찾고 입력 조각에 따라 출력 조각을 만듭니다.

또한 한 개의 입력이 매주이고 출력 조각이 매일 생성되는 샘플에 표시된 것처럼 종속성 기간에 대해 고유한 매핑을 제공할 수도 있습니다.

## <a name="data-dependency-and-validation"></a>데이터 종속성 및 유효성 검사
데이터 집합에는 조각 실행으로 생성된 데이터를 사용하기 전에 유효성 검사하는 방법을 지정하는 유효성 검사 정책이 정의되어 있을 수 있습니다. 세부 정보는 [데이터 집합 만들기](data-factory-create-datasets.md) 를 참조하세요.

이러한 경우 조각의 실행이 완료된 후 출력 조각 상태는 **Validation**의 하위 상태를 포함한 **Waiting**으로 변경됩니다. 조각의 유효성이 검사되었으면 조각 상태는 **Ready**로 변경됩니다.

데이터 조각이 생성되었으나 유효성 검사를 통과하지 못하면 이 조각에 따라 다운스트림 조각에 대한 작업 실행은 처리되지 않습니다.

[파이프라인 모니터링 및 관리](data-factory-monitor-manage-pipelines.md) 에서는 Data Factory에 있는 데이터 조각의 다양한 상태에 대해 다룹니다.

## <a name="external-data"></a>외부 데이터
데이터 집합을 외부(다음 JSON 코드 조각에 표시)로 표시할 수 있으며 이는 Data Factory에서 생성되지 않았음을 의미합니다. 이러한 경우 데이터 집합 정책은 데이터 집합에 대한 유효성 검사 및 재시도 정책을 설명하는 추가 매개 변수 집합을 포함할 수 있습니다. 모든 속성에 대한 설명은 [파이프라인 만들기](data-factory-create-pipelines.md) 를 참조하세요.

Data Factory에서 생성하는 데이터 집합과 마찬가지로 외부 데이터에 대한 데이터 조각도 종속 조각을 처리하기 전에 준비되어야 합니다.

```json
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
```
## <a name="onetime-pipeline"></a>일회성 파이프라인
파이프라인 정의 내에 지정한 시작 및 종료 시간 내에서 정기적(예: 매시간 또는 매일)으로 실행되도록 파이프라인을 만들고 일정을 예약할 수 있습니다. 자세한 내용은 [활동 예약](#scheduling-and-execution) 을 참고하세요. 한 번만 실행되는 파이프라인을 만들 수도 있습니다. 이렇게 하려면 다음 JSON 샘플에서 보여 주듯이 파이프라인 정의에서 **pipelineMode** 속성을 **onetime**으로 설정합니다. 이 속성의 기본값은 **scheduled**입니다.

```json
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
```

다음 사항에 유의하세요.

* 파이프라인의 **start** 및 **end** 시간은 지정되지 않습니다.
* Data Factory에서 값이 사용되지 않더라도 입력 및 출력 데이터 집합의 **availability**(**frequency** 및 **interval**)가 지정됩니다.  
* 다이어그램 뷰는 일회성 파이프라인을 표시하지 않습니다. 이 동작은 의도된 것입니다.
* 일회성 파이프라인은 업데이트할 수 없습니다. 일회성 파이프라인을 복제하고, 이름을 변경하고, 속성을 업데이트하고, 배포하여 또 다른 파이프라인을 만들 수 있습니다.




<!--HONumber=Nov16_HO3-->


