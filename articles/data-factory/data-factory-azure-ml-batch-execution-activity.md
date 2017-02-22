---
title: "기계 학습 작업 사용 | Microsoft Docs"
description: "Azure Data Factory 및 Azure 기계 학습을 사용하여 예측 파이프라인을 만드는 방법을 설명합니다."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 4fad8445-4e96-4ce0-aa23-9b88e5ec1965
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: cbd5ca0444a1d0f9ad67864ae9507d5659191a05
ms.openlocfilehash: aea3600cafeb297822280d7dc7ec9d13cce76ca1


---
# <a name="create-predictive-pipelines-using-azure-machine-learning-activities"></a>Azure 기계 학습 작업을 사용하여 예측 파이프라인 만들기

> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md) 
> * [Pig](data-factory-pig-activity.md)
> * [MapReduce](data-factory-map-reduce.md)
> * [Hadoop 스트리밍](data-factory-hadoop-streaming-activity.md)
> * [기계 학습](data-factory-azure-ml-batch-execution-activity.md)
> * [저장 프로시저](data-factory-stored-proc-activity.md)
> * [데이터 레이크 분석 U-SQL](data-factory-usql-activity.md)
> * [.NET 사용자 지정](data-factory-use-custom-activities.md)
>

## <a name="introduction"></a>소개

### <a name="azure-machine-learning"></a>Azure 기계 학습
[Azure 기계 학습](https://azure.microsoft.com/documentation/services/machine-learning/) 을 사용하여 예측 분석 솔루션을 빌드, 테스트 및 배포할 수 있습니다. 대략적인 관점에서 이 작업은 다음 세 단계로 수행됩니다.

1. **학습 실험 만들기**. Azure ML Studio를 사용하여 이 단계를 수행합니다. ML Studio는 학습 데이터를 사용하여 예측 분석 모델을 학습하고 테스트하는 데 사용하는 시각적 공동 개발 환경입니다.
2. **예측 실험으로 변환**. 기존 데이터로 모델을 학습시키고 새 데이터의 점수를 매기는 데 사용할 준비가 되면, 점수 매기기를 위해 실험을 준비하고 간소화합니다.
3. **웹 서비스로 배포**. 점수 매기기 실험을 Azure 웹 서비스로 게시할 수 있습니다. 이 웹 서비스 끝점을 통해 데이터를 모델로 전송하고 모델로부터 결과 예측을 받을 수 있습니다.  

### <a name="azure-data-factory"></a>Azure 데이터 팩터리
Data Factory는 데이터의 **이동**과 **변환**을 조율하고 자동화하는 클라우드 기반의 데이터 통합 서비스입니다. 다양한 데이터 저장소에서 데이터를 수집하고 변환/처리하며 데이터 저장소에 결과 데이터를 게시할 수 있는 Data Factory 서비스를 사용하여 데이터 통합 솔루션을 만들 수 있습니다.

Data Factory 서비스를 통해 데이터를 이동하고 변환하는 파이프라인을 실행한 다음 데이터 파이프라인을 지정된 일정(매시간, 매일, 매주 등)으로 만들 수 있습니다. 또한 데이터 파이프라인 간의 종속성과 계보를 표시하는 다양한 시각화를 제공하며 문제를 쉽고 정확하게 파악하고 모니터링 경고를 설정하는 통합된 단일 보기에서 모든 데이터 파이프라인을 모니터링합니다.

[Azure Data Factory 소개](data-factory-introduction.md) 및 [첫 번째 파이프라인 빌드](data-factory-build-your-first-pipeline.md) 문서를 참조하여 Azure Data Factory 서비스를 빠르게 시작합니다.

### <a name="data-factory-and-machine-learning-together"></a>Data Factory 및 Machine Learning
Azure Data Factory를 사용하면 예측 분석을 위해 게시된 [Azure Machine Learning][azure-machine-learning] 웹 서비스를 사용하는 파이프라인을 쉽게 만들 수 있습니다. Azure 데이터 팩터리 파이프라인에서 **배치 실행 작업** 을 사용하여 Azure ML 웹 서비스를 호출하고 배치에 있는 데이터에 대한 예측을 할 수 있습니다. 자세한 내용은 [배치 실행 작업을 사용하여 Azure ML 웹 서비스 호출](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity) 섹션을 참조하세요.

시간이 지남에 따라 Azure ML 점수 매기기 실험의 예측 모델은 새 입력 데이터 집합을 사용하여 다시 학습되어야 합니다. 다음 단계를 수행하여 데이터 팩터리 파이프라인에서 Azure ML 모델을 다시 학습할 수 있습니다.

1. 웹 서비스로 학습 실험(예측 실험 아님)을 게시합니다. 이전 시나리오에서 웹 서비스로 예측 실험을 노출했으므로 Azure ML Studio에서 이 단계를 수행합니다.
2. Azure ML 배치 실행 작업을 사용하여 학습 실험을 위한 웹 서비스를 호출합니다. 기본적으로, Azure ML 배치 실행 작업을 사용하여 학습 웹 서비스와 점수 매기기 웹 서비스를 모두 호출할 수 있습니다.

재학습으로 완료한 후에는 **Azure ML 업데이트 리소스 작업**을 사용하여 새로 학습한 모델로 점수 매기기 웹 서비스(웹 서비스로 노출된 예측 실험)를 업데이트합니다. 자세한 내용은 [업데이트 리소스 작업을 사용하여 모델 업데이트](#updating-models-using-update-resource-activity)를 참조하세요.

## <a name="invoking-a-web-service-using-batch-execution-activity"></a>배치 실행 작업을 사용하여 웹 서비스 호출
Azure 데이터 팩터리를 사용하여 데이터 이동 및 처리를 오케스트레이션한 다음 Azure 기계 학습을 사용하는 배치 실행을 수행할 수 있습니다. 최상위 단계는 다음과 같습니다.

1. Azure 기계 학습 연결된 서비스를 만듭니다. 다음이 필요합니다.

   1. **요청 URI** . 웹 서비스 페이지에서 **배치 실행** 링크를 클릭하여 요청 URI를 찾을 수 있습니다.
   2. **API 키** . 게시한 웹 서비스를 클릭하여 API 키를 찾을 수 있습니다.
   3. **AzureMLBatchExecution** 작업을 사용합니다.

      ![기계 학습 대시보드](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

      ![배치 URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)

### <a name="scenario-experiments-using-web-service-inputsoutputs-that-refer-to-data-in-azure-blob-storage"></a>시나리오: Azure Blob 저장소의 데이터를 참조하는 웹 서비스 입력/출력을 사용하여 실험
이 시나리오에서는 Azure 기계 학습 웹 서비스는 Azure Blob 저장소의 파일에서 데이터를 사용하여 예측을 만들고 Blob 저장소에 예측 결과를 저장합니다. 다음 JSON은 AzureMLBatchExecution 작업이 포함된 Data Factory 파이프라인을 정의합니다. 작업에는 입력으로 **DecisionTreeInputBlob** 데이터 집합, 출력으로 **DecisionTreeResultBlob** 데이터 집합이 있습니다. **DecisionTreeInputBlob**은 **webServiceInput** JSON 속성을 사용하여 웹 서비스에 입력으로 전달됩니다. **DecisionTreeResultBlob**은 **webServiceOutputs** JSON 속성을 사용하여 웹 서비스에 출력으로 전달됩니다.  

> [!IMPORTANT]
> 웹 서비스에서 다중 입력을 받을 경우 **webServiceInput**를 사용하는 대신에 **webServiceInputs** 속성을 사용합니다. webServiceInputs 속성을 사용하는 예제는 [웹 서비스에는 다중 입력이 필요합니다](#web-service-requires-multiple-inputs) 섹션을 참조합니다.
>
> **webServiceInput**/**webServiceInputs** 및 **webServiceOutputs** 속성(**typeProperties** 내)에서 참조하는 데이터 집합은 **inputs** 및 **outputs** 작업에 포함되어야 합니다.
>
> Azure ML 실험에서 웹 서비스 입력 및 출력 포트와 전역 매개 변수에는 사용자 지정할 수 있는 기본 이름("input1", "input2")이 붙어있습니다. WebServiceInputs, webServiceOutputs 및 globalParameters 설정에 대해 사용하는 이름은 실험에서의 이름과 정확히 일치해야 합니다. 예상된 매핑을 확인하기 위해 일괄 처리 실행 도움말 페이지에서 Azure ML 끝점에 대한 샘플 요청 페이로드를 볼 수 있습니다.
>
>

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchExecution",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "DecisionTreeInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "DecisionTreeResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "typeProperties":
        {
            "webServiceInput": "DecisionTreeInputBlob",
            "webServiceOutputs": {
                "output1": "DecisionTreeResultBlob"
            }                
        },
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```
> [!NOTE]
> AzureMLBatchExecution 작업의 입력 및 출력만 웹 서비스에 매개 변수로 전달될 수 있습니다. 예를 들어 위의 JSON 조각에서 DecisionTreeInputBlob은 webServiceInput 매개 변수를 통해 웹 서비스에 입력으로 전달되는 AzureMLBatchExecution 작업에 대한 입력입니다.   
>
>

### <a name="example"></a>예
이 예제에서는 Azure 저장소를 사용하여 입력 및 출력 데이터를 저장합니다.

이 예제를 진행하기 전에 [Data Factory를 사용하여 첫 번째 파이프라인 빌드][adf-build-1st-pipeline] 자습서를 살펴보는 것이 좋습니다. 이 예제에서는 Data Factory Editor를 사용하여 Data Factory 아티팩트(연결된 서비스, 데이터 집합, 파이프라인)를 만듭니다.   

1. **Azure Storage**에 대한 **연결된 서비스**를 만듭니다. 입력 및 출력 파일이 서로 다른 저장소 계정에 있는 경우 연결된 서비스가 두 개 필요합니다. 다음은 JSON 예제입니다.

    ```JSON
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
        }
      }
    }
    ```
2. **입력** Azure Data Factory **데이터 집합**을 만듭니다. 다른 Data Factory 데이터 집합과 달리 이러한 데이터 집합은 **folderPath** 및 **fileName** 값을 둘 다 포함해야 합니다. 분할을 사용하여 각 배치 실행(각 데이터 조각)이 고유한 입력 및 출력 파일을 처리하거나 생성하도록 할 수 있습니다. 입력을 CSV 파일 형식으로 변환하여 각 조각의 저장소 계정에 배치하는 업스트림 작업을 포함해야 할 수 있습니다. 이 경우 다음 예제에 표시된 **external** 및 **externalData** 설정을 포함하지 않으며, DecisionTreeInputBlob은 다른 작업의 출력 데이터 집합이 됩니다.

    ```JSON
    {
      "name": "DecisionTreeInputBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/input",
          "fileName": "in.csv",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }
    ```

    입력 csv 파일에는 열 머리글 행이 있어야 합니다. **복사 작업**을 사용하여 csv를 만들고 Blob 저장소로 이동하는 경우 싱크 속성 **blobWriterAddHeader**를 **true**로 설정해야 합니다. 예:

    ```JSON
    sink:
    {
        "type": "BlobSink",     
        "blobWriterAddHeader": true
    }
    ```

    csv 파일에 머리글 행이 없는 경우 다음과 같은 오류가 표시될 수 있습니다. **Error in Activity: Error reading string. Unexpected token: StartObject. Path '', line 1, position 1**.
3. **출력** Azure Data Factory **데이터 집합**을 만듭니다. 이 예제에서는 분할을 사용하여 각 조각 실행의 고유한 출력 경로를 만듭니다. 분할하지 않으면 작업에서 파일을 덮어씁니다.

    ```JSON
    {
      "name": "DecisionTreeResultBlob",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "azuremltesting/scored/{folderpart}/",
          "fileName": "{filepart}result.csv",
          "partitionedBy": [
            {
              "name": "folderpart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "yyyyMMdd"
              }
            },
            {
              "name": "filepart",
              "value": {
                "type": "DateTime",
                "date": "SliceStart",
                "format": "HHmmss"
              }
            }
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ","
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 15
        }
      }
    }
    ```
4. **AzureMLLinkedService** 형식의 **연결된 서비스**를 만들고 API 키 및 모델 배치 실행 URL을 제공합니다.

    ```JSON
    {
      "name": "MyAzureMLLinkedService",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
          "mlEndpoint": "https://[batch execution endpoint]/jobs",
          "apiKey": "[apikey]"
        }
      }
    }
    ```
5. 끝으로, **AzureMLBatchExecution** 작업이 포함된 파이프라인을 작성합니다. 런타임에 파이프라인은 다음 단계를 수행합니다.

   1. 입력 데이터 집합에서 입력 파일의 위치를 가져옵니다.
   2. Azure Machine Learning 배치 실행 API 호출
   3. 배치 실행 출력을 출력 데이터 집합에 지정된 Blob에 복사합니다.

      > [!NOTE]
      > AzureMLBatchExecution 작업에는&0;개 이상의 입력 및&1;개 이상의 출력이 있을 수 있습니다.
      >
      >

    ```JSON
    {
        "name": "PredictivePipeline",
        "properties": {
            "description": "use AzureML model",
            "activities": [
            {
                "name": "MLActivity",
                "type": "AzureMLBatchExecution",
                "description": "prediction analysis on batch input",
                "inputs": [
                {
                    "name": "DecisionTreeInputBlob"
                }
                ],
                "outputs": [
                {
                    "name": "DecisionTreeResultBlob"
                }
                ],
                "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob"
                    }                
                },
                "policy": {
                    "concurrency": 3,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            }
            ],
            "start": "2016-02-13T00:00:00Z",
            "end": "2016-02-14T00:00:00Z"
        }
    }
    ```

      **start** 및 **end** 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항입니다. **end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09**를 지정합니다. JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx) 를 참조하세요.

      > [!NOTE]
      > AzureMLBatchExecution 작업에 대한 입력 지정은 선택 사항입니다.
      >
      >

### <a name="scenario-experiments-using-readerwriter-modules-to-refer-to-data-in-various-storages"></a>시나리오: 다양한 저장소의 데이터를 참조하는 판독기/기록기 모듈을 사용하여 실험
Azure ML 실험을 만들 때 다른 일반적인 시나리오는 판독기 및 기록기기 모듈을 사용하는 것입니다. 판독기 모듈은 실험으로 데이터를 로드할 때 사용되고 기록기 모듈은 실험에서 데이터를 저장할 때 사용됩니다. 판독기 및 기록기 모듈에 대한 자세한 내용은 MSDN 라이브러리의 [판독기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [기록기](https://msdn.microsoft.com/library/azure/dn905984.aspx) 항목을 참조하세요.     

판독기 및 작성기 모듈을 사용하는 경우 해당 판독기/기록기 모듈의 각 속성에 대해 웹 서비스 매개 변수를 사용하는 것이 좋습니다. 이러한 웹 매개 변수를 통해 런타임 중 값을 구성할 수 있습니다. 예를 들어 Azure SQL 데이터베이스: XXX.database.windows.net을 사용하는 판독기 모듈을 사용하여 실험을 만들 수 있습니다. 웹 서비스를 배포한 후 웹 서비스의 소비자가 YYY.database.windows.net이라는 다른 Azure SQL Server를 지정할 수 있도록 하려고 합니다. 웹 서비스 매개 변수를 사용하여 이 값을 구성할 수 있습니다.

> [!NOTE]
> 웹 서비스 입력 및 출력은 웹 서비스 매개 변수와 다릅니다. 첫 번째 시나리오에서 Azure ML 웹 서비스에 대해 입력 및 출력을 지정할 수 있는 방법을 살펴보았습니다. 이 시나리오에서는 판독기/기록기 모듈의 속성에 해당하는 웹 서비스에 대한 매개 변수를 전달합니다.
>
>

웹 서비스 매개 변수를 사용하는 시나리오를 살펴보겠습니다. Azure Machine Learning에서 지원하는 데이터 원본(예: Azure SQL Database) 중 하나에서 데이터를 읽는 판독기 모듈을 사용하는 Azure Machine Learning 웹 서비스를 배포했습니다. 배치 실행이 수행된 후 기록기 모듈(Azure SQL 데이터베이스)을 사용하여 결과가 기록됩니다.  웹 서비스 입력 및 출력이 실험에서 정의되지 않습니다. 이 경우 판독기 및 기록기 모듈에 대한 관련 웹 서비스 매개 변수를 구성하는 것이 좋습니다. 이 구성을 통해 AzureMLBatchExecution 작업을 사용하는 경우 판독기/기록기 모듈을 구성할 수 있습니다. 다음과 같이 작업 JSON의 **globalParameters** 섹션에서 웹 서비스 매개 변수를 지정합니다.

```JSON
"typeProperties": {
    "globalParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```

다음 예제와 같이 웹 서비스 매개 변수 값을 전달하는 데 [데이터 팩터리 함수](data-factory-functions-variables.md) 를 사용할 수도 있습니다.

```JSON
"typeProperties": {
    "globalParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> 웹 서비스 매개 변수는 대/소문자를 구분하므로 작업 JSON에서 지정한 이름이 웹 서비스에 의해 노출된 이름과 일치해야 합니다.
>
>

### <a name="using-a-reader-module-to-read-data-from-multiple-files-in-azure-blob"></a>Azure Blob에서 여러 파일의 데이터를 읽는 판독기 모듈 사용
Pig, Hive와 같은 작업이 있는 빅 데이터 파이프라인은 확장명 없이 하나 이상의 출력 파일을 만들 수 있습니다. 예를 들어 외부 Hive 테이블을 지정하는 경우 외부 Hive 테이블에 대한 데이터는 다음 이름 000000_0으로 Azure Blob 저장소에 저장될 수 있습니다. 실험에서 판독기 모듈을 사용하여 여러 파일을 읽고 예측에 사용할 수 있습니다.

Azure 기계 학습 실험에서 판독기 모듈을 사용하는 경우 입력으로 Azure Blob를 지정할 수 있습니다. Azure Blob 저장소에 있는 파일은 HDInsight에서 실행되는 Pig 및 Hive 스크립트에서 생성되는 출력 파일(예: 000000_0)일 수 있습니다. 판독기 모듈을 통해 **컨테이너, 디렉터리/blob에 대한 경로**를 구성하여 파일(확장명 없음)을 읽을 수 있습니다. **컨테이너에 대한 경로**는 컨테이너를 가리키고 **디렉터리/blob**은 다음 이미지에 표시된 파일이 들어 있는 폴더를 가리킵니다. **컨테이너/폴더에 있는 모든 파일을 지정하는(즉, data/aggregateddata/year=2014/month-6/\*)** 별표, 즉 \*)는 실험의 일부로 읽습니다.

![Azure Blob 속성](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### <a name="example"></a>예
#### <a name="pipeline-with-azuremlbatchexecution-activity-with-web-service-parameters"></a>AzureMLBatchExecution 작업 및 웹 서비스 매개 변수가 포함된 파이프라인

```JSON
{
  "name": "MLWithSqlReaderSqlWriter",
  "properties": {
    "description": "Azure ML model with sql azure reader/writer",
    "activities": [
      {
        "name": "MLSqlReaderSqlWriterActivity",
        "type": "AzureMLBatchExecution",
        "description": "test",
        "inputs": [
          {
            "name": "MLSqlInput"
          }
        ],
        "outputs": [
          {
            "name": "MLSqlOutput"
          }
        ],
        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
        "typeProperties":
        {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
                "output1": "MLSqlOutput"
            }
              "globalParameters": {
                "Database server name": "<myserver>.database.windows.net",
                "Database name": "<database>",
                "Server user account name": "<user name>",
                "Server user account password": "<password>"
              }              
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        },
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

위 JSON 예제에서

* 배포된 Azure 기계 학습 웹 서비스는 판독기 및 기록기 모듈을 사용하여 Azure SQL 데이터베이스에서/로 데이터를 읽고/쓸 수 있습니다. 이 웹 서비스는 네 개의 매개 변수, 즉 데이터베이스 서버 이름, 데이터베이스 이름, 서버 사용자 계정 이름 및 서버 사용자 계정 암호를 공개합니다.  
* **start** 및 **end** 날짜/시간은 둘 다 [ISO 형식](http://en.wikipedia.org/wiki/ISO_8601)(영문)이어야 합니다. 예: 2014-10-14T16:32:41Z. **end** 시간은 선택 사항입니다. **end** 속성 값을 지정하지 않는 경우 "**start + 48시간**"으로 계산됩니다. 파이프라인을 무기한 실행하려면 **종료** 속성 값으로 **9999-09-09**를 지정합니다. JSON 속성에 대한 자세한 내용은 [JSON 스크립트 참조](https://msdn.microsoft.com/library/dn835050.aspx) 를 참조하세요.

### <a name="other-scenarios"></a>기타 시나리오
#### <a name="web-service-requires-multiple-inputs"></a>웹 서비스에는 다중 입력이 필요합니다
웹 서비스에서 다중 입력을 받을 경우 **webServiceInput**를 사용하는 대신에 **webServiceInputs** 속성을 사용합니다. **webServiceInputs**에서 참조하는 데이터 집합은 또한 **입력** 작업에 포함되어야 합니다.

Azure ML 실험에서 웹 서비스 입력 및 출력 포트와 전역 매개 변수에는 사용자 지정할 수 있는 기본 이름("input1", "input2")이 붙어있습니다. WebServiceInputs, webServiceOutputs 및 globalParameters 설정에 대해 사용하는 이름은 실험에서의 이름과 정확히 일치해야 합니다. 예상된 매핑을 확인하기 위해 일괄 처리 실행 도움말 페이지에서 Azure ML 끝점에 대한 샘플 요청 페이로드를 볼 수 있습니다.

```JSON
{
    "name": "PredictivePipeline",
    "properties": {
        "description": "use AzureML model",
        "activities": [{
            "name": "MLActivity",
            "type": "AzureMLBatchExecution",
            "description": "prediction analysis on batch input",
            "inputs": [{
                "name": "inputDataset1"
            }, {
                "name": "inputDataset2"
            }],
            "outputs": [{
                "name": "outputDataset"
            }],
            "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties": {
                "webServiceInputs": {
                    "input1": "inputDataset1",
                    "input2": "inputDataset2"
                },
                "webServiceOutputs": {
                    "output1": "outputDataset"
                }
            },
            "policy": {
                "concurrency": 3,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "02:00:00"
            }
        }],
        "start": "2016-02-13T00:00:00Z",
        "end": "2016-02-14T00:00:00Z"
    }
}
```

#### <a name="web-service-does-not-require-an-input"></a>웹 서비스에는 입력이 필요하지 않습니다.
Azure ML 배치 실행 웹 서비스는 입력이 필요하지 않는 모든 워크플로(예: R 또는 Python 스크립트)를 실행하는 데 사용할 수 있습니다. 또는 어떠한 GlobalParameters도 노출하지 않는 판독기 모듈로 실험을 구성할 수 있습니다. 이 경우 AzureMLBatchExecution 작업은 다음과 같이 구성합니다.

```JSON
{
    "name": "scoring service",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "myBlob"
        }
    ],
    "typeProperties": {
        "webServiceOutputs": {
            "output1": "myBlob"
        }              
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-does-not-require-an-inputoutput"></a>웹 서비스에는 입력/출력이 필요하지 않습니다.
Azure ML 배치 실행 웹 서비스에는 웹 서비스 출력이 구성되어 있지 않을 수 있습니다. 이 예제에서는 웹 서비스 입력 또는 출력이 없으며 GlobalParameters도 구성되어 있지 않습니다. 작업 자체에 여전히 출력이 구성되어 있지만 webServiceOutput으로 제공된 것이 아닙니다.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "outputs": [
        {
            "name": "placeholderOutputDataset"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

#### <a name="web-service-uses-readers-and-writers-and-the-activity-runs-only-when-other-activities-have-succeeded"></a>웹 서비스는 판독기 및 기록기를 사용하며 작업은 다른 작업이 성공한 경우에만 실행됩니다.
Azure ML 웹 서비스의 판독기 및 기록기 모듈은 GlobalParameters를 포함 또는 포함하지 않고 실행하도록 구성될 수 있습니다. 하지만 일부 업스트림 처리가 완료될 때만 서비스를 호출하도록 데이터 집합 종속성을 사용하는 파이프라인에 서비스 호출을 포함하려 할 수 있습니다. 또한 이 방법을 사용하여 배치 실행이 완료된 후 다른 작업을 트리거할 수도 있습니다. 이 경우 웹 서비스 입력 또는 출력으로 이름을 지정하지 않고 입력 및 출력 작업을 사용하여 종속성을 표현할 수 있습니다.

```JSON
{
    "name": "retraining",
    "type": "AzureMLBatchExecution",
    "inputs": [
        {
            "name": "upstreamData1"
        },
        {
            "name": "upstreamData2"
        }
    ],
    "outputs": [
        {
            "name": "downstreamData"
        }
    ],
    "typeProperties": {
     },
    "linkedServiceName": "mlEndpoint",
    "policy": {
        "concurrency": 1,
        "executionPriorityOrder": "NewestFirst",
        "retry": 1,
        "timeout": "02:00:00"
    }
},
```

**내용** 은 다음과 같습니다.

* 실험 끝점에서 webServiceInput을 사용하면 Blob 데이터 집합으로 표시되고 작업 입력 및 webServiceInput 속성에 포함됩니다. 그렇지 않은 경우 webServiceInput 속성은 생략됩니다.
* 실험 끝점에서 webServiceOutput을 사용하면 Blob 데이터 집합으로 표시되고 작업 출력 및 webServiceOutputs 속성에 포함됩니다. 작업 출력 및 webServiceOutputs는 실험에서 각 출력의 이름으로 매핑됩니다. 그렇지 않은 경우 webServiceOutputs 속성은 생략됩니다.
* 실험 끝점에서 globalParameter를 노출하는 경우 키, 값 쌍으로 작업 globalParameters 속성에 지정됩니다. 그렇지 않은 경우 globalParameters 속성은 생략됩니다. 키는 대/소문자를 구분합니다. [Azure Data Factory 함수](data-factory-functions-variables.md) 를 사용할 수 있습니다.
* 작업 typeProperties 속성에 참조되지 않고도 추가 데이터 집합이 작업 입력 및 출력 속성에 포함될 수 있습니다. 이러한 데이터 집합은 조각 종속성을 사용한 실행에 적용되지만 그렇지 않은 경우 AzureMLBatchExecution 작업에서 무시됩니다.


## <a name="updating-models-using-update-resource-activity"></a>업데이트 리소스 작업을 사용하여 모델 업데이트
시간이 지남에 따라 Azure ML 점수 매기기 실험의 예측 모델은 새 입력 데이터 집합을 사용하여 다시 학습되어야 합니다. 재학습으로 완료한 후에는 재학습한 ML 모델로 점수 매기기 웹 서비스를 업데이트하려고 합니다. 웹 서비스를 통해 Azure ML 모델을 재학습하고 업데이트하는 일반적인 단계는 다음과 같습니다.

1. [Azure 기계 학습 스튜디오](https://studio.azureml.net)에서 실험을 만듭니다.
2. 모델에 만족하면 Azure ML Studio를 사용하여 **학습 실험** 및 점수 매기기/**예측 실험** 모두에 대해 웹 서비스를 게시합니다.

다음 표에서는 이 예제에 사용된 웹 서비스에 대해 설명합니다.  자세한 내용은 [프로그래밍 방식으로 기계 학습 모델 다시 학습](../machine-learning/machine-learning-retrain-models-programmatically.md) 을 참조하세요.

- **학습 웹 서비스** - 학습 데이터를 수신하고 학습된 모델을 생성합니다. 재학습의 출력은 Azure Blob 저장소에서 .ilearner 파일입니다. 웹 서비스로 학습 실험을 게시할 때 사용자에 대한 **기본 끝점** 이 자동으로 만들어집니다. 더 많은 끝점을 만들 수 있지만 예제에서는 기본 끝점만 사용합니다.
- **점수 매기기 웹 서비스** - 레이블이 지정되지 않은 데이터 예제를 수신하고 예측을 합니다. 예측의 출력은 실험의 구성에 따라 .csv 파일 또는 Azure SQL 데이터베이스의 행과 같은 다양한 형태를 포함할 수 있습니다. 웹 서비스로 예측 실험을 게시할 때 사용자에 대한 기본 끝점이 자동으로 만들어집니다. 

다음 그림에서는 Azure ML에서 학습 및 점수 매기기 끝점 간의 관계를 보여줍니다.

![웹 서비스](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)

**Azure ML Batch 실행 작업**을 사용하여 **학습 웹 서비스**를 호출할 수 있습니다. 학습 웹 서비스를 호출하는 것은 점수 매기기 데이터에 대해 Azure ML 웹 서비스(점수 매기기 웹 서비스)를 호출하는 것과 동일합니다. 이전 섹션에서는 Azure Data Factory 파이프라인에서 Azure ML 웹 서비스를 호출하는 방법에 대해 자세히 설명합니다. 

**scoring web service** 을 사용하여 두 번째 **Azure ML 업데이트 리소스 작업** 을 사용하여 새로 학습된 모델로 웹 서비스를 업데이트합니다. 다음 예제에서는 연결된 서비스 정의를 제공합니다. 

### <a name="scoring-web-service-is-a-classic-web-service"></a>웹 서비스 점수 매기기는 클래식 웹 서비스입니다.
웹 서비스 점수 매기기가 **클래식 웹 서비스**인 경우 [Azure Portal](https://manage.windowsazure.com)을 사용하여 두 번째 **기본이 아닌 업데이트 가능한 끝점**을 만들어야 합니다. 이에 대한 단계는 [끝점 만들기](../machine-learning/machine-learning-create-endpoint.md) 문서를 참조하세요. 기본이 아닌 업데이트 가능한 끝점을 만든 후 다음을 수행합니다.

* **배치 실행**을 클릭하여 **mlEndpoint** JSON 속성에 대한 URI 값을 가져옵니다.
* **업데이트 리소스** 링크를 클릭하여 **updateResourceEndpoint** JSON 속성에 대한 URI 값을 가져옵니다. API 키는 끝점 페이지 자체의 오른쪽 하단에 있습니다.

![업데이트 가능한 끝점](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

다음 예제에서는 AzureML 연결된 서비스에 샘플 JSON 정의를 제공합니다. 연결된 서비스는 인증을 위해 apiKey를 사용합니다.  

```json
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
            "apiKey": "endpoint2Key",
            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
        }
    }
}
```

### <a name="scoring-web-service-is-a-new-type-of-web-service-azure-resource-manager"></a>웹 서비스 점수 매기기는 새로운 유형의 웹 서비스(Azure Resource Manager)입니다.
웹 서비스가 Azure Resource Manager 끝점을 노출하는 새로운 유형의 웹 서비스인 경우 두 번째 **기본이 아닌** 끝점을 추가할 필요가 없습니다. 연결된 서비스의 **updateResourceEndpoint**는 다음 형식을 갖습니다. 

```
https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resource-group-name}/providers/Microsoft.MachineLearning/webServices/{web-service-name}?api-version=2016-05-01-preview. 
```

[Azure Machine Learning 웹 서비스 포털](https://services.azureml.net/)에서 웹 서비스를 쿼리할 때 URL에 대한 자리 표시자 값을 가져올 수 있습니다. 새로운 유형의 업데이트 리소스 끝점에는 AAD(Azure Active Directory) 토큰이 필요합니다. AzureML 연결된 서비스에서 **servicePrincipalId** 및 **servicePrincipalKey**를 지정합니다. [서비스 보안 주체 만들기 및 Azure 리소스를 관리하기 위한 권한 할당 방법](../azure-resource-manager/resource-group-create-service-principal-portal.md)을 참조하세요. 샘플 AzureML 연결된 서비스 정의는 다음과 같습니다. 

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "description": "The linked service for AML web service.",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/0000000000000000000000000000000000000/services/0000000000000000000000000000000000000/jobs?api-version=2.0",
            "apiKey": "xxxxxxxxxxxx",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "000000000-0000-0000-0000-0000000000000",
            "servicePrincipalKey": "xxxxx",
            "tenant": "mycompany.com"
        }
    }
}
```

다음 시나리오는 보다 자세한 내용을 제공합니다. Azure Data Factory 파이프라인에서 Azure ML 모델을 재학습 및 업데이트하는 예제가 포함되어 있습니다.

### <a name="scenario-retraining-and-updating-an-azure-ml-model"></a>시나리오: Azure ML 모델 재학습 및 업데이트
이 섹션에서는 **Azure ML 배치 실행 작업** 을 사용하여 모델을 재학습하는 샘플 파이프라인을 제공합니다. 파이프라인은 또한 **Azure ML 업데이트 리소스 작업** 을 사용하여 점수 매기기 웹 서비스에서 모델을 업데이트합니다. 섹션에서는 또한 모든 연결된 서비스, 데이터 집합 및 파이프라인에 대한 JSON 코드 조각 예제도 제공합니다.

샘플 파이프라인의 다이어그램 보기는 다음과 같습니다. 보다시피 Azure ML 배치 실행 작업은 학습 입력을 사용하여 학습 출력(iLearner 파일)을 생성합니다. Azure ML 업데이트 리소스 작업이 이 학습 출력을 사용하여 점수 매기기 웹 서비스 끝점에서 모델을 업데이트합니다. 업데이트 리소스 작업은 출력을 생성하지 않습니다. placeholderBlob는 Azure 데이터 팩터리 서비스가 파이프라인을 실행하기 위해 필요로 하는 더미 출력 데이터 집합입니다.

![파이프라인 다이어그램](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

#### <a name="azure-blob-storage-linked-service"></a>Azure Blob 저장소 연결된 서비스:
Azure 저장소는 다음 데이터를 보관합니다.

* 학습 데이터. Azure ML 학습 웹 서비스에 대한 입력 데이터입니다.  
* iLearner 파일. Azure ML 학습 웹 서비스에서의 출력입니다. 이 파일은 업데이트 리소스 작업에 대한 입력이기도 합니다.  

연결된 서비스의 샘플 JSON 정의는 다음과 같습니다.

```JSON
{
    "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
        }
    }
}
```

#### <a name="training-input-dataset"></a>학습 입력 데이터 집합:
다음 데이터 집합은 Azure ML 학습 웹 서비스에 대한 입력 학습 데이터를 나타냅니다. Azure ML 배치 실행 작업은 이 데이터 집합을 입력으로 사용합니다.

```JSON
{
    "name": "trainingData",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "labeledexamples",
            "fileName": "labeledexamples.arff",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "policy": {          
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

#### <a name="training-output-dataset"></a>학습 출력 데이터 집합:
다음 데이터 집합은 Azure ML 학습 웹 서비스에서 출력 iLearner 파일을 나타냅니다. Azure ML 배치 실행 작업은 이 데이터 집합을 생성합니다. 이 데이터 집합은 Azure ML 업데이트 리소스 작업에 대한 입력이기도 합니다.

```JSON
{
    "name": "trainedModelBlob",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "trainingoutput",
            "fileName": "model.ilearner",
            "format": {
                "type": "TextFormat"
            }
        },
        "availability": {
            "frequency": "Week",
            "interval": 1
        }
    }
}
```

#### <a name="linked-service-for-azure-ml-training-endpoint"></a>Azure ML 학습 끝점에 대한 연결된 서비스
다음 JSON 코드 조각은 학습 웹 서비스의 기본 끝점을 가리키는 Azure 기계 학습 연결된 서비스를 정의합니다.

```JSON
{    
    "name": "trainingEndpoint",
      "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
              "apiKey": "myKey"
        }
      }
}
```

**Azure ML Studio**에서 다음을 수행하여 **mlEndpoint** 및 **apiKey**에 대한 값을 가져옵니다.

1. 왼쪽 메뉴에서 **웹 서비스** 를 클릭합니다.
2. 웹 서비스 목록에서 **학습 웹 서비스** 를 클릭합니다.
3. **API 키** 텍스트 상자 옆의 복사를 클릭합니다. 클립보드의 키를 Data Factory JSON 편집기에 붙여넣습니다.
4. **Azure ML studio**에서 **배치 실행** 링크를 클릭합니다.
5. **요청** 섹션에서 **요청 URI**를 복사하여 Data Factory JSON 편집기에 붙여넣습니다.   

#### <a name="linked-service-for-azure-ml-updatable-scoring-endpoint"></a>Azure ML 업데이트 가능한 점수 매기기 끝점에 대한 연결된 서비스:
다음 JSON 코드 조각은 점수 매기기 웹 서비스의 기본이 아닌 업데이트 가능한 끝점을 가리키는 Azure 기계 학습 연결된 서비스를 정의합니다.  

```JSON
{
    "name": "updatableScoringEndpoint2",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/00000000eb0abe4d6bbb1d7886062747d7/services/00000000026734a5889e02fbb1f65cefd/jobs?api-version=2.0",
            "apiKey": "sooooooooooh3WvG1hBfKS2BNNcfwSO7hhY6dY98noLfOdqQydYDIXyf2KoIaN3JpALu/AKtflHWMOCuicm/Q==",
            "updateResourceEndpoint": "https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/Default-MachineLearning-SouthCentralUS/providers/Microsoft.MachineLearning/webServices/myWebService?api-version=2016-05-01-preview",
            "servicePrincipalId": "fe200044-c008-4008-a005-94000000731",
            "servicePrincipalKey": "zWa0000000000Tp6FjtZOspK/WMA2tQ08c8U+gZRBlw=",
            "tenant": "mycompany.com"
        }
    }
}
```

#### <a name="placeholder-output-dataset"></a>자리 표시자 출력 데이터 집합:
Azure ML 업데이트 리소스 작업은 어떠한 출력도 생성하지 않습니다. 그러나 Azure Data Factory에서 파이프라인의 일정을 구동하려면 출력 데이터 집합이 필요합니다. 따라서 이 예에서는 더미/자리 표시자 데이터 집합을 사용합니다.  

```JSON
{
    "name": "placeholderBlob",
    "properties": {
        "availability": {
            "frequency": "Week",
            "interval": 1
        },
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "any",
            "format": {
                "type": "TextFormat"
            }
        }
    }
}
```

#### <a name="pipeline"></a>파이프라인
파이프라인에는 **AzureMLBatchExecution** 및 **AzureMLUpdateResource**라는 두 활동이 있습니다. Azure ML 배치 실행 작업은 학습 데이터를 입력으로 사용하여 .iLearner 파일을 출력으로 생성합니다. 이 작업은 입력 교육 데이터와 함께 학습 웹 서비스(웹 서비스로 노출된 학습 실험)를 호출하고 웹 서비스로부터 ilearner 파일을 수신합니다. placeholderBlob는 Azure 데이터 팩터리 서비스가 파이프라인을 실행하기 위해 필요로 하는 더미 출력 데이터 집합입니다.

![파이프라인 다이어그램](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "Training Exp for ADF ML [trained model]",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "outputs": [
                    {
                        "name": "placeholderBlob"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00Z",
           "end": "2016-02-14T00:00:00Z"
    }
}
```

### <a name="reader-and-writer-modules"></a>판독기 및 작성기 모듈
웹 서비스 매개 변수를 사용하는 일반적인 시나리오는 Azure SQL 판독기 및 기록기 사용입니다. 판독기 모듈은 Azure Machine Learning Studio 외부 데이터 관리 서비스에서 실험으로 데이터를 로드하는 데 사용됩니다. 작성기 모듈은 사용자 실험에서 Azure Machine Learning Studio 외부 데이터 관리 서비스로 데이터를 저장합니다.  

Azure Blob/Azure SQL 판독기/기록기에 대한 자세한 내용은 MSDN 라이브러리의 [판독기](https://msdn.microsoft.com/library/azure/dn905997.aspx) 및 [기록기](https://msdn.microsoft.com/library/azure/dn905984.aspx) 항목을 참조하세요. 이전 섹션의 예제에서는 Azure Blob 판독기 및 Azure Blob 기록기를 사용했습니다. 이 섹션에서는 Azure SQL 판독기 및 Azure SQL 기록기를 사용하는 방법을 설명합니다.

## <a name="frequently-asked-questions"></a>질문과 대답
**Q:** 빅 데이터 파이프라인에서 생성된 여러 파일이 있습니다. 모든 파일에서 작동하도록 AzureMLBatchExecution 작업을 사용할 수 있습니까?

**A:** 예. 자세한 내용은 **Azure Blob에서 여러 파일의 데이터를 읽는 판독기 모듈 사용** 섹션을 참조하세요.

## <a name="azure-ml-batch-scoring-activity"></a>Azure ML 일괄 처리 점수 매기기 작업
**AzureMLBatchScoring** 작업을 사용하여 Azure Machine Learning과 통합하는 경우 최신 **AzureMLBatchExecution** 작업을 사용하는 것이 좋습니다.

AzureMLBatchExecution 작업은2015년 8월 Azure SDK 및 Azure PowerShell 릴리스에서 도입되었습니다.

AzureMLBatchScoring 작업을 사용하여 계속하려면 이 섹션을 계속 읽어보세요.  

### <a name="azure-ml-batch-scoring-activity-using-azure-storage-for-inputoutput"></a>입/출력에 대해 Azure 저장소를 사용하는 Azure ML 일괄 처리 점수 매기기 작업

```JSON
{
  "name": "PredictivePipeline",
  "properties": {
    "description": "use AzureML model",
    "activities": [
      {
        "name": "MLActivity",
        "type": "AzureMLBatchScoring",
        "description": "prediction analysis on batch input",
        "inputs": [
          {
            "name": "ScoringInputBlob"
          }
        ],
        "outputs": [
          {
            "name": "ScoringResultBlob"
          }
        ],
        "linkedServiceName": "MyAzureMLLinkedService",
        "policy": {
          "concurrency": 3,
          "executionPriorityOrder": "NewestFirst",
          "retry": 1,
          "timeout": "02:00:00"
        }
      }
    ],
    "start": "2016-02-13T00:00:00Z",
    "end": "2016-02-14T00:00:00Z"
  }
}
```

### <a name="web-service-parameters"></a>웹 서비스 매개 변수
웹 서비스 매개 변수에 대한 값을 지정하려면 다음 예제와 같이 파이프라인 JSON의 **AzureMLBatchScoringActivty** 섹션에 **typeProperties** 섹션을 추가합니다.

```JSON
"typeProperties": {
    "webServiceParameters": {
        "Param 1": "Value 1",
        "Param 2": "Value 2"
    }
}
```
다음 예제와 같이 웹 서비스 매개 변수 값을 전달하는 데 [데이터 팩터리 함수](data-factory-functions-variables.md) 를 사용할 수도 있습니다.

```JSON
"typeProperties": {
    "webServiceParameters": {
       "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    }
}
```

> [!NOTE]
> 웹 서비스 매개 변수는 대/소문자를 구분하므로 작업 JSON에서 지정한 이름이 웹 서비스에 의해 노출된 이름과 일치해야 합니다.
>
>

## <a name="see-also"></a>참고 항목
* [Azure 블로그 게시물: Azure 데이터 팩터리 및 Azure 기계 학습 시작하기](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)

[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/



<!--HONumber=Jan17_HO4-->


