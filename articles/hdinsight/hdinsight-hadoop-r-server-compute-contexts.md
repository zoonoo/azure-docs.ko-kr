---
title: "HDInsight에서 R Server의 계산 컨텍스트 옵션 | Microsoft 문서"
description: "HDInsight에서 R Server 사용자에게 제공되는 다양한 계산 컨텍스트 옵션에 대해 알아봅니다."
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 841e70fa3a80bbeb7e2281246bac2f99c0de899f
ms.openlocfilehash: 169743012b1f50d67d5eafdb279e706719752eb8


---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>HDInsight에서 R 서버의 계산 컨텍스트 옵션
Azure HDInsight에서 Microsoft R Server는 R 기반 분석을 위한 최신 기능을 제공합니다. [Azure Blob](../storage/storage-introduction.md "Azure Blob 저장소") 저장소 계정, Data Lake 저장소 또는 로컬 Linux 파일 시스템에 있는 컨테이너의 HDFS에 저장된 데이터를 사용합니다. R Server가 오픈 소스 R을 기반으로 하기 때문에 빌드한 R 기반 응용 프로그램은 8000개 이상의 오픈 소스 R 패키지를 활용할 수 있습니다. R 서버와 함께 포함된 Microsoft의 빅 데이터 분석 패키지인 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR")에서 루틴을 활용할 수도 있습니다.  

클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 위치를 제공합니다. 에지 노드를 사용하는 경우 에지 노드 서버의 코어에서 ScaleR의 병렬화된 분산 함수를 실행하는 옵션이 제공됩니다. 또한 ScaleR의 Hadoop Map Reduce 또는 Spark 계산 컨텍스트를 사용하여 클러스터의 노드에서 함수를 실행하는 옵션도 제공됩니다.

## <a name="compute-contexts-for-an-edge-node"></a>에지 노드에 대한 계산 컨텍스트
일반적으로 에지 노드의 R 서버에서 실행되는 R 스크립트는 해당 노드의 R 인터프리터 내에서 실행됩니다. 예외는 ScaleR 함수를 호출하는 단계입니다. ScaleR 호출은 ScaleR 계산 컨텍스트를 설정하는 방법에 따라 결정된 계산 환경에서 실행됩니다.  에지 노드에서 R 스크립트를 실행하는 경우 계산 컨텍스트에 사용 가능한 값은 로컬 순차('local'), 로컬 병렬('localpar'), Map Reduce 및 Spark입니다.

'local' 및 'localpar' 옵션은 rxExec 호출 실행 방법에 따라서만 달라집니다. 이러한 두 옵션은 ScaleR numCoresToUse 옵션(예: rxOptions(numCoresToUse=6))을 사용하여 다르게 지정하지 않는 한, 사용 가능한 모든 코어에서 병렬 방식으로 다른 rx-function 호출을 실행합니다. 다음은 다양한 계산 컨텍스트 옵션을 요약해서 설명한 것입니다.

| 계산 컨텍스트  | 설정 방법                      | 실행 컨텍스트                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| 로컬 순차 | rxSetComputeContext('local')    | 직렬로 실행되는 rxExec 호출을 제외하고, 에지 노드 서버의 코어 간 병렬화된 실행 |
| 로컬 병렬   | rxSetComputeContext('localpar') | 에지 노드 서버의 코어 간 병렬화된 실행 |
| Spark            | RxSpark()                       | HDI 클러스터의 노드에서 Spark를 통해 분산된 실행 병렬 처리 |
| Map Reduce       | RxHadoopMR()                    | HDI 클러스터의 노드에서 Map Reduce를 통해 분산된 실행 병렬 처리 |

성능을 위해 실행을 병렬 처리하려면 세 가지 옵션이 있습니다. 선택한 옵션은 분석 작업의 특성, 데이터의 크기 및 위치에 따라 달라집니다.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>계산 컨텍스트를 결정하기 위한 지침
현재 사용할 계산 컨텍스트를 알려주는 수식은 없습니다. 그러나 올바로 선택할 수 있도록, 또는 적어도 벤치마크를 실행하기 전에 선택할 범위를 좁히는 데 도움이 되는 몇 가지 가이드 원칙이 있습니다. 기본 원칙은 다음과 같습니다.

1. 로컬 Linux 파일 시스템이 HDFS보다 빠릅니다.
2. 데이터가 로컬에 있고 XDF 형식인 경우 반복된 분석이 더 빠릅니다.
3. 텍스트 데이터 원본에서 적은 양의 데이터를 스트리밍하는 것이 좋습니다. 데이터의 양이 클 경우 분석하기 전에 XDF로 변환합니다.
4. 분석을 위해 에지 노드에 데이터를 복사하거나 스트리밍하는 경우 발생하는 오버헤드는 매우 많은 양의 데이터가 감당할 수 없게 됩니다.
5. Hadoop의 분석의 경우 Spark가 Map Reduce보다 빠릅니다.

이러한 원칙을 감안할 때 계산 컨텍스트 선택에 대한 몇 가지 일반적인 규칙은 다음과 같습니다.

### <a name="local"></a>Local
* 분석할 데이터의 양이 작고 분석을 반복하지 않아도 되는 경우 분석 루틴에 직접 스트림하고 'local' 또는 'localpar'를 사용합니다.
* 분석할 데이터의 양이 작거나 중간 크기이고 분석을 반복해야 하는 경우 로컬 파일 시스템에 복사하고 XDF로 가져와서 'local' 또는 'localpar'를 통해 분석합니다.

### <a name="hadoop-spark"></a>Hadoop Spark
* 분석할 데이터 양이 많은 경우 RxHiveData 또는 RxParquetData를 사용하여 Spark DataFrame으로 가져오거나 저장소 문제가 아닌 한 HDFS의 XDF로 가져와서 'Spark'를 통해 분석합니다.

### <a name="hadoop-map-reduce"></a>Hadoop Map Reduce
* 일반적으로 느려질 수 있기 때문에 Spark 계산 컨텍스트를 사용하여 대처할 수 없는 문제가 발생하는 경우에만 사용합니다.  

## <a name="inline-help-on-rxsetcomputecontext"></a>rxSetComputeContext의 인라인 도움말
자세한 내용 및 ScaleR 계산 컨텍스트의 예제는 다음과 같이 rxSetComputeContext 메서드에 대한 R의 인라인 도움말을 참조하세요.

    > ?rxSetComputeContext

[R Server MSDN](https://msdn.microsoft.com/library/mt674634.aspx "MSDN의 R Server") 라이브러리에서 사용할 수 있는 "[ScaleR 분산 컴퓨팅 가이드](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)"(영문)를 참조할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 R 서버가 포함된 새 HDInsight 클러스터를 만드는 방법을 알아보았습니다. SSH 세션에서 R 콘솔을 사용하는 기본 사항을 알아보았습니다. 이제 HDInsight에서 R Server로 작업하는 다른 방법을 검색하려면 다음 문서를 참고할 수 있습니다.

* [Hadoop용 R 서버 개요](hdinsight-hadoop-r-server-overview.md)
* [Hadoop용 R Server 시작](hdinsight-hadoop-r-server-get-started.md)
* [HDInsight에 RStudio Server 추가(클러스터를 만드는 중에 추가되지 않은 경우)](hdinsight-hadoop-r-server-install-r-studio.md)
* [HDInsight에서 R Server의 Azure Storage 옵션](hdinsight-hadoop-r-server-storage.md)




<!--HONumber=Nov16_HO4-->


