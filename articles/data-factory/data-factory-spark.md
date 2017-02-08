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
ms.date: 02/06/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: b3895dbf56c58e8e10e6af84afc520e5aef6e01e
ms.openlocfilehash: c2eb07b83f3096f86772f9af63a48da79ce0fd2e


---
# <a name="invoke-spark-programs-from-data-factory"></a>Data Factory에서 Spark 프로그램 호출
## <a name="introduction"></a>소개
Data Factory 파이프라인에서 MapReduce 작업을 사용하여 HDInsight Spark 클러스터에서 Spark 프로그램을 실행할 수 있습니다. 활동 사용에 대한 자세한 내용을 보려면 이 문서를 확인하기 전에 [MapReduce 활동](data-factory-map-reduce.md) 문서를 참조하세요. 

## <a name="spark-sample-on-github"></a>GitHub의 Spark 샘플
[Spark - GitHub의 Data Factory 샘플](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) 에서는 MapReduce 작업을 사용하여 Spark 프로그램을 호출하는 방법을 보여 줍니다. Spark 프로그램은 단순히 Azure Blob 컨테이너에서 다른 컨테이너로 데이터를 복사합니다. 

## <a name="data-factory-entities"></a>데이터 팩터리 엔터티
**Spark-ADF/src/ADFJsons** 폴더에는 Data Factory 엔터티(연결된 서비스, 데이터 집합, 파이프라인)에 대한 파일이 있습니다.  

이 샘플에는 두 가지 **연결된 서비스** (Azure 저장소 및 Azure HDInsight)가 있습니다. **StorageLinkedService.json**에서 Azure Storage 이름 및 키 값을 지정하고 **HDInsightLinkedService.json**에서 clusterUri, userName 및 password를 지정합니다.

이 샘플에는 두 가지 **데이터 집합**(**input.json** 및 **output.json**)이 있습니다. 이러한 파일은 **Datasets** 폴더에 있습니다.  이러한 파일은 MapReduce 작업에 대한 입력 및 출력 데이터 집합을 나타냅니다.

샘플 파이프라인은 **ADFJsons/Pipeline** 폴더에 있습니다. 파이프라인을 검토하면 MapReduce 활동을 사용하여 Spark 프로그램을 호출하는 방법을 파악할 수 있습니다. 

MapReduce 활동은 StorageLinkedService.json에 지정된 Azure Storage의 **adflibs** 컨테이너에 있는 **com.adf.sparklauncher.jar**을 호출하도록 구성됩니다. 이 프로그램의 소스 코드는 Spark-ADF/src/main/java/com/adf/ 폴더에 있으며 spark-submit을 호출하고 Spark 작업을 실행합니다. 

> [!IMPORTANT]
> 샘플을 사용하기 전에 [README.md](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.md)를 확인하여 최신 정보와 추가 정보를 파악하세요. 
> 
> MapReduce 활동을 사용하여 Spark 프로그램을 호출하려면 이 방식에서 고유한 HDInsight Spark 클러스터를 사용해야 합니다. 주문형 HDInsight 클러스터 사용은 지원되지 않습니다.   
> 
> 

## <a name="see-also"></a>참고 항목
* [Hive 작업](data-factory-hive-activity.md)
* [Pig 작업](data-factory-pig-activity.md)
* [MapReduce 작업](data-factory-map-reduce.md)
* [Hadoop 스트리밍 작업](data-factory-hadoop-streaming-activity.md)
* [R 스크립트 호출](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)




<!--HONumber=Nov16_HO3-->


