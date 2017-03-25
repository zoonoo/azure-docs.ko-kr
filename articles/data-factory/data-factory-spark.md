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
ms.sourcegitcommit: bb1ca3189e6c39b46eaa5151bf0c74dbf4a35228
ms.openlocfilehash: 8be5e1525a7c481de5cb02edd26da305af2d4798
ms.lasthandoff: 03/18/2017


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

## <a name="hdinsight-linked-service"></a>HDInsight 연결된 서비스
Data Factory 파이프라인에서 Spark 작업을 사용하기 전에 HDInsight 사용자 고유의 연결된 서비스를 만듭니다. 다음 JSON 코드 조각은 사용자 고유의 Azure HDInsight Spark 클러스터를 가리키는 HDInsight 연결된 서비스의 정의를 보여 줍니다.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> 현재, Spark 작업은 기본 저장소로 Data Lake Store 또는 온-프레미스 HDInsight 연결된 서비스를 사용하여 Spark 클러스터를 지원하지 않습니다. 

HDInsight 연결된 서비스 및 기타 연결된 서비스 계산에 대한 자세한 내용은 [Data Factory 연결된 서비스 계산](data-factory-compute-linked-services.md) 문서를 참조하세요. 

## <a name="spark-activity-json"></a>Spark 작업 JSON
Spark작업의 샘플 JSON 정의는 다음과 같습니다.    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
다음 표에서는 JSON 정의에 사용하는 JSON 속성을 설명합니다. 

| 속성 | 설명 | 필수 |
| -------- | ----------- | -------- |
| name | 파이프라인에서 작업에 대한 이름입니다. | 예 |
| 설명 | 작업이 어떤 일을 수행하는지 설명하는 텍스트입니다. | 아니요 |
| type | 이 속성은 HDInsightSpark로 설정해야 합니다. | 예 |
| linkedServiceName | Spark 프로그램이 실행되는 HDInsight 연결된 서비스에 대한 참조입니다. | 예 |
| rootPath | Spark 파일이 포함된 Azure Blob 컨테이너 및 폴더입니다. 파일 이름은 대/소문자를 구분합니다. | 예 |
| entryFilePath | Spark 코드/패키지의 루트 폴더에 대한 상대 경로 | 예 |
| className | 응용 프로그램의 Java/Spark main 클래스 | 아니요 | 
| arguments | Spark 프로그램에 대한 명령줄 인수 목록입니다. | 아니요 | 
| proxyUser | Spark 프로그램 실행을 가장하는 사용자 계정 | 아니요 | 
| sparkConfig | Spark 구성 속성 | 아니요 | 
| getDebugInfo | sparkJobLinkedService에 지정되었거나 HDInsight 클러스터에 사용된 Azure Storage에 Spark 로그 파일을 언제 복사할지 지정합니다. 허용되는 값: None, Always 또는 Failure. 기본값: None. | 아니요 | 
| sparkJobLinkedService | Spark 작업 파일, 종속성 및 로그를 보유하는 Azure Storage 연결된 서비스입니다.  이 속성에 대한 값을 지정하지 않으면 HDInsight 클러스터와 연결된 저장소가 사용됩니다. | 아니요 |

## <a name="folder-structure"></a>폴더 구조
Spark 작업은 Pig 및 Hive 작업에서 수행하는 것처럼 인라인 스크립트를 지원하지 않습니다. Spark 작업은 Pig/Hive 작업보다 확장성이 뛰어납니다. Spark 작업의 경우 jar 패키지(java CLASSPATH에 배치), python 파일(PYTHONPATH에 배치) 및 기타 파일과 같은 여러 종속성을 제공할 수 있습니다.

HDInsight 연결된 서비스에서 참조하는 Azure Blob Storage에 다음 폴더 구조를 만듭니다. 그런 다음 종속 파일을 **entryFilePath**로 표시되는 루트 폴더의 해당 하위 폴더에 업로드합니다. 예를 들어 python 파일을 루트 폴더의 pyFiles 하위 폴더에, jar 파일을 jars 하위 폴더에 업로드합니다. 런타임 시, Data Factory 서비스는 Azure Blob Storage에서 다음 폴더 구조를 필요로 합니다.     

| Path | 설명 | 필수 | 형식 |
| ---- | ----------- | -------- | ---- | 
| 등&amp;4;가지 유형의 클러스터가 제공됩니다.    | 저장소 연결된 서비스에서 Spark 작업의 루트 경로    | 예 | 폴더 |
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


