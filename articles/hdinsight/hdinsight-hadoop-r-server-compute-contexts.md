<properties
   pageTitle="HDI Premium의 R 서버에 대한 계산 컨텍스트 옵션 | Azure"
   description="HDInsight Premium에서 R 서버 사용자가 사용할 수 있는 다양한 계산 컨텍스트 옵션을 알아봅니다."
   services="HDInsight"
   documentationCenter=""
   authors="jeffstokes72"
   manager="paulettem"
   editor="cgronlun"
/>

<tags
   ms.service="HDInsight"
   ms.devlang="R"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-services"
   ms.date="03/28/2016"
   ms.author="jeffstok"
/>

# HDInsight Premium의 R 서버에 대한 계산 컨텍스트 옵션

HDI Spark의 R 서버는 [Azure Blob](../storage/storage-introduction.md "Azure Blob 저장소") 저장소 계정 또는 로컬 Linux 파일 시스템의 컨테이너에 있는 HDFS에 저장된 데이터를 사용하는 최신 R 기반 분석 기능을 제공합니다. R 서버는 오픈 소스 R을 기반으로 하기 때문에 빌드한 R 기반 응용 프로그램에서 8000 이상의 오픈 소스 R 패키지와 R 서버에 포함된 Microsoft의 빅 데이터 분석 패키지인 [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "Revolution Analytics ScaleR")의 루틴을 활용할 수 있습니다. Premium 클러스터의 에지 노드는 클러스터에 연결하고 R 스크립트를 실행하는 데 편리한 연결 영역을 제공합니다. 에지 노드를 사용하는 경우 ScaleR의 Hadoop Map Reduce 또는 Spark 계산 컨텍스트를 통해 에지 노드 서버의 코어 또는 클러스터 노드에서 ScaleR의 병렬화된 분산 함수를 실행할 수 있습니다.

## 가장자리 노드에 대한 계산 컨텍스트

일반적으로 가장자리 노드의 R 서버에서 실행되는 R 스크립트는 ScaleR 함수를 호출하는 단계를 제외하고 해당 노드의 R 인터프리터 내에서 실행됩니다. ScaleR 호출은 ScaleR 계산 컨텍스트를 설정하는 방법에 따라 결정된 계산 환경에서 실행됩니다. 가장자리 노드에서 R 스크립트를 실행할 경우 계산 컨텍스트의 가능한 값은 로컬 순차('local'), 로컬 병렬('localpar'), Map Reduce 및 Spark입니다.

| 계산 컨텍스트 | 설정 방법 | 실행 컨텍스트 |
|------------------|---------------------------------|---------------------------------------------------------------------------------------|
| 로컬 순차 | rxSetComputeContext('local') | 가장자리 노드 서버에서 순차(비병렬) 실행 |
| 로컬 병렬 | rxSetComputeContext('localpar') | 가장자리 노드 서버의 코어에서 병렬 처리 |
| Spark | RxSpark() | HDI 클러스터의 노드에서 Spark를 통해 분산된 실행 병렬 처리 |
| Map Reduce | RxHadoopMR() | HDI 클러스터의 노드에서 Map Reduce를 통해 분산된 실행 병렬 처리 |


성능을 목적으로 하는 병렬 처리 실행을 수행하고 싶은 경우 세 가지 옵션이 있습니다. 선택은 분석 작업의 특성, 크기 및 데이터의 위치에 따라 달라집니다.

## 계산 컨텍스트 결정

현재는 어떤 계산 컨텍스트를 사용해야 할지 알려주는 수식이 없지만 올바른 선택을 하도록 지원하거나 최적의 선택이 요구되는 경우 벤치마크를 실행하기 전에 적어도 오차를 좁히도록 도와주는 몇 가지 기본 원칙이 있습니다. 기본 원칙은 다음과 같습니다.

1.	로컬 Linux 파일 시스템이 HDFS보다 빠릅니다.
2.	데이터가 로컬에 있고 XDF 형식인 경우 반복된 분석이 더 빠릅니다. 
3.	작은 데이터의 경우 텍스트 데이터 원본의 스트림이 분석하기 전에 XDF로 변환합니다. 
4.	매우 큰 데이터의 경우 분석을 위해 가장자리 노드에 데이터 복사/스트리밍의 오버헤드는 감당해야 합니다. 
5.	데이터가 매우 커지고 분산된 메모리에 더 이상 맞지 않게 될 때까지는 Spark가 Map Reduce보다 Hadoop 분석에서 더 빠릅니다.

이러한 원칙을 감안할 때 계산 컨텍스트 선택에 대한 몇 가지 일반적인 규칙은 다음과 같습니다.

### 로컬 병렬

- 분석할 데이터가 작고 분석을 반복하지 않아도 되는 경우 분석 루틴에 직접 스트림하고 'localpar'을 사용합니다. 
- 분석할 데이터가 작고 분석을 반복해야 하거나, 보통일 경우 로컬 파일 시스템에 복사하고 XDF로 가져와서 'localpar'을 통해 분석합니다. 

### Hadoop Spark

- 분석할 데이터가 클 경우 저장소 문제가 발생하지 않는 한 HDFS의 XDF로 가져와서 'Spark'를 통해 분석합니다. 

### Hadoop Map Reduce

- 분석할 데이터가 매우 크고 Spark 성능이 저하되기 시작할 경우 'Map Reduce'를 통해 분석을 시도합니다.

## rxSetComputeContext의 인라인 도움말

자세한 내용 및 ScaleR 계산 컨텍스트의 예제는 다음과 같이 rxSetComputeContext 메서드에 대한 R의 인라인 도움말을 참조하세요.

    > ?rxSetComputeContext 

또는 [R 서버 MSDN](https://msdn.microsoft.com/library/mt674634.aspx "MSDN에서 R 서버") 라이브러리에서 사용할 수 있는 "ScaleR 분산 컴퓨팅 가이드"를 참조하세요.


## 다음 단계

이제 R 서버를 포함하는 새 HDInsight 클러스터를 만드는 방법 및 SSH 세션에서 R 콘솔 사용 기본 사항을 이해했으므로 다음을 사용하여 HDInsight에서 R 서버로 작업하는 다른 방법을 확인할 수 있습니다.

- [Hadoop에서 R 서버 개요](hdinsight-hadoop-r-server-overview.md)
- [Hadoop에서 R 서버 시작](hdinsight-hadoop-r-server-get-started.md)
- [HDInsight Premium에 RStudio 서버 추가](hdinsight-hadoop-r-server-install-r-studio.md)
- [HDInsight Premium의 R 서버에 대한 Azure 저장소 옵션](hdinsight-hadoop-r-server-storage.md)

<!---HONumber=AcomDC_0330_2016-->