---
title: "Azure Data Factory에서 Spark 프로그램 호출"
description: "MapReduce 작업을 사용하여 Azure Data Factory에서 Spark 프로그램을 호출하는 방법에 대해 알아봅니다."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Data Factory에서 Spark 프로그램 호출
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Hive 작업](data-factory-hive-activity.md) 
> * [Pig 작업](data-factory-pig-activity.md)
> * [MapReduce 작업](data-factory-map-reduce.md)
> * [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
> * [Spark 작업](data-factory-spark.md)
> * [Machine Learning Batch 실행 작업](data-factory-azure-ml-batch-execution-activity.md)
> * [Machine Learning 업데이트 리소스 작업](data-factory-azure-ml-update-resource-activity.md)
> * [저장 프로시저 작업](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL 작업](data-factory-usql-activity.md)
> * [.NET 사용자 지정 작업](data-factory-use-custom-activities.md)

## <a name="introduction"></a>소개
Data Factory [파이프라인](data-factory-create-pipelines.md)에서 HDInsight Spark 작업은 [사용자 고유](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) HDInsight 클러스터에서 Spark 프로그램을 실행합니다. 이 문서는 데이터 변환 및 지원되는 변환 활동의 일반적인 개요를 표시하는 [데이터 변환 활동](data-factory-data-transformation-activities.md) 문서에서 작성합니다.

> [!IMPORTANT]
> Azure Data Factory를 처음 접하는 경우 이 문서를 읽기 전에 [첫 파이프라인 빌드하기](data-factory-build-your-first-pipeline.md) 자습서를 먼저 살펴보시기 바랍니다. 데이터 팩터리 서비스에 대한 개요는 [Azure Data Factory 소개](data-factory-introduction.md)를 참조하세요. 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Azure HDInsight의 Apache Spark 클러스터
먼저 [Azure HDInsight에서 Apache Spark 클러스터 만들기](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md) 자습서의 다음 지침에 따라 Azure HDInsight에서 Apache Spark 클러스터를 만듭니다. 

## <a name="create-data-factory"></a>데이터 팩터리 만들기 
Spark 작업으로 데이터 팩터리 파이프라인을 만드는 일반적인 단계는 다음과 같습니다.  

1. 데이터 팩터리를 만듭니다.
2. Azure HDInsight의 Apache Spark 클러스터를 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다.
3. 현재 생성 중인 출력이 없더라도 작업의 출력 데이터 집합을 지정해야 합니다. Azure Blob 데이터 집합을 만들려면 다음 단계를 수행합니다.
    1. Azure Storage 계정을 데이터 팩터리에 연결하는 연결된 서비스를 만듭니다.
    2. Azure Storage 연결된 서비스를 참조하는 데이터 집합을 만듭니다.  
3. Spark 작업을 사용하여 2단계에서 만든 Apache HDInsight 연결된 서비스를 참조하는 파이프라인을 만듭니다. 이 작업은 이전 단계에서 출력 데이터 집합으로 만든 데이터 집합을 통해 구성됩니다. 출력 데이터 집합은 일정(매시간, 매일 등)을 구동하는 기능입니다. 따라서 작업에서 실제로 출력을 생성하지 않더라도 출력 데이터 집합을 지정해야 합니다.

데이터 팩터리를 만드는 자세한 단계별 지침은 [첫 파이프라인 빌드하기](data-factory-build-your-first-pipeline.md) 자습서를 참조하세요. 이 자습서는 HDInsight Hadoop 클러스터에 Hive 작업을 사용하지만 그 단계는 Spark HDInsight 클러스터에 Spark 작업을 사용할 때와 비슷합니다.   

다음 섹션에서는 데이터 팩터리에 Apache Spark 클러스터 및 Spark 작업을 사용하는 데이터 팩터리 엔터티에 대한 정보를 제공합니다.   

## <a name="hdinsight-linked-service"></a>HDInsight 연결된 서비스
Data Factory 파이프라인에서 Spark 작업을 사용하기 전에 HDInsight 사용자 고유의 연결된 서비스를 만듭니다. 다음 JSON 코드 조각은 Azure HDInsight Spark 클러스터를 가리키는 HDInsight 연결된 서비스의 정의를 보여 줍니다.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 현재 Spark 작업은 기본 저장소로 Azure Data Lake Store 또는 주문형 HDInsight 연결된 서비스를 사용하는 Spark 클러스터를 지원하지 않습니다. 

HDInsight 연결된 서비스 및 기타 연결된 서비스 계산에 대한 자세한 내용은 [Data Factory 연결된 서비스 계산](data-factory-compute-linked-services.md) 문서를 참조하세요. 

## <a name="spark-activity-json"></a>Spark 작업 JSON
다음은 Spark 작업을 사용하는 파이프라인의 샘플 JSON 정의입니다.    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

다음 사항에 유의하세요. 
- type 속성은 HDInsightSpark로 설정됩니다. 
- rootPath는 adfspark\\pyFiles로 설정되며, 여기서 adfspark는 Azure Blob 컨테이너이고 pyFiles는 해당 컨테이너의 파일 폴더입니다. 이 예에서 Azure Blob Storage는 Spark 클러스터와 연결되어 있습니다. 파일을 다른 Azure Storage에 업로드할 수 있습니다. 이렇게 하는 경우 해당 저장소 계정을 데이터 팩터리에 연결하는 Azure Storage 연결된 서비스를 만들어야 합니다. 그런 다음 sparkJobLinkedService 속성의 값으로 연결된 서비스의 이름을 지정합니다. 이 속성과 Spark 작업에서 지원하는 기타 속성에 대한 자세한 내용은 [Spark 작업 속성](#spark-activity-properties)을 참조하세요.  
- entryFilePath는 python 파일인 test.py로 설정됩니다. 
- Spark 프로그램의 매개 변수 값은 arguments 속성을 사용하여 전달됩니다. 이 예에는 두 개의 인수 arg1 및 arg2가 있습니다. 
- getDebugInfo 속성은 Always로 설정되며 항상 로그 파일이 생성(성공 또는 실패)된다는 의미입니다. 
- sparkConfig 섹션에서는 하나의 python 환경 설정인 worker.memory를 지정합니다. 
- 출력 섹션에는 출력 데이터 집합이 하나 있습니다. Spark 프로그램이 출력을 생성하지 않더라도 출력 데이터 집합을 지정해야 합니다. 출력 데이터 집합은 파이프라인의 일정(매시간, 매일 등)을 구동합니다.     

typeProperties 섹션의 type 속성은 이 문서의 후반부에 나오는 [Spark 작업 속성](#spark-activity-properties) 섹션에 설명되어 있습니다. 

앞서 언급했듯이, 작업의 출력 데이터 집합은 파이프라인의의 일정(매시간, 매일 등)을 구동하므로 작업의 출력 데이터 집합을 지정해야 합니다. 이 예에서는 Azure Blob 데이터 집합이 사용됩니다. Azure Blob 데이터 집합을 만들려면 먼저 Azure Storage 연결된 서비스를 만들어야 합니다. 

다음은 Azure Storage 연결된 서비스 및 Azure Blob 데이터 집합의 샘플 정의입니다. 

**Azure Storage 연결된 서비스:**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Azure Blob 데이터 집합:** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

이 데이터 집합은 더미 데이터 집합입니다. 데이터 팩터리에서는 빈도 및 간격 설정을 사용하여 파이프라인의 시작 및 종료 시간 내에서 날마다 파이프라인을 실행합니다. 샘플 파이프라인 정의에서 시작 시간과 종료 시간이 하루 떨어져 있으므로 파이프라인이 한 번만 실행됩니다. 

## <a name="spark-activity-properties"></a>Spark 작업 속성

다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다. 

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| name | 파이프라인의 작업 이름입니다. | 예 |
| 설명 | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다. | 아니요 |
| type | 이 속성은 HDInsightSpark로 설정해야 합니다. | 예 |
| linkedServiceName | Spark 프로그램이 실행되는 HDInsight 연결된 서비스의 이름입니다. | 예 |
| rootPath | Spark 파일이 포함된 Azure Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. | 예 |
| entryFilePath | Spark 코드/패키지의 루트 폴더에 대한 상대 경로입니다. | 예 |
| className | 응용 프로그램의 Java/Spark main 클래스 | 아니요 | 
| arguments | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 아니요 | 
| proxyUser | Spark 프로그램 실행을 가장하는 사용자 계정 | 아니요 | 
| sparkConfig | Spark 구성 속성입니다. | 아니요 | 
| getDebugInfo | sparkJobLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 Spark 로그 파일을 언제 복사할지 지정합니다. 허용되는 값: None, Always 또는 Failure. 기본값: None. | 아니요 | 
| sparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Azure Storage 연결된 서비스입니다.  이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 저장소가 사용됩니다. | 아니요 |

## <a name="folder-structure"></a>폴더 구조
Spark 작업은 Pig 및 Hive 작업에서 수행하는 것처럼 인라인 스크립트를 지원하지 않습니다. Spark 작업은 Pig/Hive 작업보다 확장성이 뛰어납니다. Spark 작업의 경우 jar 패키지(java CLASSPATH에 배치), python 파일(PYTHONPATH에 배치) 및 기타 파일과 같은 여러 종속성을 제공할 수 있습니다.

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 다음 폴더 구조를 만듭니다. 그런 다음 종속 파일을 **entryFilePath**로 표시되는 루트 폴더의 해당 하위 폴더에 업로드합니다. 예를 들어 python 파일을 루트 폴더의 pyFiles 하위 폴더에, jar 파일을 jars 하위 폴더에 업로드합니다. 런타임 시, Data Factory 서비스는 Azure Blob Storage에서 다음 폴더 구조를 필요로 합니다.     

| Path | 설명 | 필수 | 형식 |
| ---- | ----------- | -------- | ---- | 
| 등 4가지 유형의 클러스터가 제공됩니다.    | 저장소 연결된 서비스에서 Spark 작업의 루트 경로    | 예 | 폴더 |
| &lt;사용자 정의 &gt; | Spark 작업의 엔트리 파일을 가리키는 경로 | 예 | 파일 | 
| ./jars | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 java classpath에 배치됨 | 아니요 | 폴더 |
| ./pyFiles | 이 폴더 아래 모든 파일이 업로드되고 클러스터의 PYTHONPATH에 배치됨 | 아니요 | 폴더 |
| ./files | 이 폴더 아래 모든 파일이 업로드되고 실행기 작업 디렉터리에 배치됨 | 아니요 | 폴더 |
| ./archives | 이 폴더 아래 모든 파일이 압축 해제됨 | 아니요 | 폴더 |
| ./logs | Spark 클러스터의 로그가 저장되는 폴더| 아니요 | 폴더 |

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 두 개의 Spark 작업 파일이 포함된 저장소에 대한 예는 다음과 같습니다. 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> 변환 작업으로 파이프라인을 만드는 전체 연습 과정은 [데이터를 변환하기 위해 파이프라인 생성](data-factory-build-your-first-pipeline-using-editor.md) 문서를 참조하세요. 



## <a name="see-also"></a>참고 항목
* [Hive 작업](data-factory-hive-activity.md)
* [Pig 작업](data-factory-pig-activity.md)
* [MapReduce 작업](data-factory-map-reduce.md)
* [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


