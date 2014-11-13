<properties urlDisplayName="Run Hadoop Samples in HDInsight" pageTitle="HDInsight에서 Hadoop 샘플 실행 | Azure" metaKeywords="hdinsight, hdinsight sample,  hadoop, mapreduce" description="제공된 샘플을 사용하여 Azure HDInsight 서비스 사용을 시작합니다. 또한 데이터 클러스터에 대해 MapReduce 프로그램을 실행하는 PowerShell 스크립트를 사용합니다." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight에서 Hadoop 샘플 실행" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# HDInsight에서 Hadoop 샘플 실행

Azure HDInsight를 사용하여 Hadoop 클러스터에서 MapReduce 작업을 실행하는 데 도움이 되는 일련의 샘플이 제공됩니다. 이 샘플은 직접 만든 각 HDInsight의 관리되는 클러스터에 대해 사용할 수 있습니다. 이러한 샘플을 실행하면 Azure PowerShell HDInsight cmdlet을 사용하여 Hadoop 클러스터에 대해 작업을 실행하는 방법을 알 수 있게 됩니다.

HDInsight용 Microsoft .NET API를 사용하여 응용 프로그램에서 MapReduce 프로그램을 프로그래밍 방식으로 실행할 수도 있습니다. 작업 제출을 위해 HDInsight API를 사용하는 방법에 대한 자세한 내용은 [Hadoop 작업을 프로그래밍 방식으로 제출][Hadoop 작업을 프로그래밍 방식으로 제출]을 참조하세요.

Hadoop 관련 기술(예: Java 기반 MapReduce 프로그래밍 및 스트리밍)에 대한 다양한 추가 설명서 및 PowerShell 스크립팅에 사용되는 cmdlet에 대한 설명서를 웹에서 찾을 수 있습니다. 이 리소스에 대한 자세한 내용은 [Azure HDInsight 소개][Azure HDInsight 소개] 항목의 마지막 섹션인 **HDInsight용 리소스**를 참조하세요.

**이 샘플에 대하여**

이 샘플은 Hadoop 작업을 배포하는 방법에 대해 빨리 이해할 수 있게 돕고 개념 및 서비스에서 사용되는 스크립팅 절차를 사용하여 작업할 수 있는 확장형 테스트 베드를 제공합니다. 다양한 크기의 데이터 집합 만들기 및 가져오기, 순차적으로 작업 실행 및 구성, 작업 결과 검사와 같은 일반적인 작업의 예제를 제공합니다. 사용되는 데이터 집합의 크기가 다양할 수 있어서, 다양한 크기의 데이터 집합이 작업 성능에 미치는 영향을 관찰할 수 있습니다.

**필수 조건**:

-   Azure 계정이 있어야 합니다. 계정 등록 옵션은 [Azure 평가판 사용][Azure 평가판 사용] 페이지를 참조하세요.

-   HDInsight 클러스터를 미리 프로비전해야 합니다. 이 클러스터를 만드는 여러 방법에 대한 자세한 내용은 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

-   Azure PowerShell을 설치하고 계정과 함께 사용하도록 구성해야 합니다. 설치 및 구성 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.

## 샘플

HDInsight에는 다음 샘플이 함께 제공됩니다.

-   [**파이 추정 샘플**][**파이 추정 샘플**] 이 자습서에서는 HDInsight에서 통계(준난수 몬테카를로) 방법을 사용하는 MapReduce 프로그램을 실행하여 파이 값을 추정하는 방법을 보여 줍니다.
-   [**WordCount 샘플**][**WordCount 샘플**] 이 자습서에서는 HDInsight 클러스터를 사용하여 텍스트 파일에 나오는 단어 수를 계산하는 MapReduce 프로그램을 실행하는 방법을 보여 줍니다.
-   [**10-GB GraySort 샘플**][**10-GB GraySort 샘플**] 이 자습서에서는 HDInsight를 사용하여 10GB 파일에 대해 일반적인 용도의 GraySort를 실행하는 방법을 보여 줍니다. 실행할 작업은 데이터를 생성하는 Teragen, 데이터를 정렬하는 Terasort, 데이터가 제대로 정렬되었는지 확인하는 Teravalidate 등 세 가지가 있습니다.
-   [**C# 스트리밍 샘플**][**C# 스트리밍 샘플**] 이 자습서에서는 C#을 사용하여 Hadoop 스트리밍 인터페이스를 사용하는 MapReduce 프로그램을 작성하는 방법을 보여 줍니다.

## 샘플을 실행하는 방법

Azure PowerShell을 사용하여 샘플을 실행할 수 있습니다. 실행 방법에 대한 지침은 샘플별로 위의 링크로 연결된 페이지에서 제공됩니다.

## 다음 단계

이 문서 및 각 샘플의 문서에서 HDInsight 클러스터에 포함된 샘플을 Azure PowerShell을 사용하여 실행하는 방법을 알아보았습니다. HDInsight에서 Pig, Hive 및 MapReduce를 사용하는 방법에 대한 자습서는 다음 항목을 참조하세요.

-   [Azure HDInsigth Service 시작][Azure HDInsigth Service 시작]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [프로그래밍 방식으로 Hadoop 작업 제출][Hadoop 작업을 프로그래밍 방식으로 제출]
-   [Azure HDInsight SDK 문서][Azure HDInsight SDK 문서]
-   [HDInsight 디버그: 오류 메시지][HDInsight 디버그: 오류 메시지]

  [Hadoop 작업을 프로그래밍 방식으로 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Azure HDInsight 소개]: ../hdinsight-introduction/
  [Azure 평가판 사용]: http://azure.microsoft.com/ko-kr/pricing/free-trial/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [**파이 추정 샘플**]: ../hdinsight-sample-pi-estimator/
  [**WordCount 샘플**]: ../hdinsight-sample-wordcount/
  [**10-GB GraySort 샘플**]: ../hdinsight-sample-10gb-graysort/
  [**C# 스트리밍 샘플**]: ../hdinsight-sample-csharp-streaming/
  [Azure HDInsigth Service 시작]: ../hdinsight-get-started/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [Azure HDInsight SDK 문서]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
  [HDInsight 디버그: 오류 메시지]: ../hdinsight-debug-jobs/
