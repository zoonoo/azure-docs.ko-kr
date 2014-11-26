<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Use Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao" />

# HDInsight에서 Hadoop과 Hive 사용

[Apache Hive][Apache Hive]는 대규모 데이터의 요약, 쿼리 및 분석에 적용할 수 있는 SQL 같은 스크립팅 언어인 *HiveQL*을 통해 MapReduce 작업을 실행하는 수단을 제공합니다. 이 문서에서는 HiveQL을 사용하여 Apache log4j 로그 파일의 데이터를 쿼리하고 기본 통계를 보고합니다.

**필수 조건:**

-   **HDInsight 클러스터**를 미리 프로비전해야 합니다. Azure 포털에서 프로비전하는 방법을 자세히 알아보려면 [HDInsight 시작][HDInsight 시작]을 참조하세요. HDInsight 클러스터를 만드는 기타 여러 방법에 대한 지침은 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

-   워크스테이션에 **Azure PowerShell**을 미리 설치해두어야 합니다. 설치 및 구성 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.

**예상 완료 시간:** 30분

## 이 문서에서는 다음을 수행합니다.

-   [Hive 사용 사례][Hive 사용 사례]
-   [Azure Blob 저장소에 데이터 파일 업로드][Azure Blob 저장소에 데이터 파일 업로드]
-   [PowerShell을 사용하여 Hive 쿼리 실행][PowerShell을 사용하여 Hive 쿼리 실행]
-   [다음 단계][다음 단계]

## <span id="usage"></span></a>Hive 사용 사례

대기 시간이 짧은 쿼리가 가능한 더 작은 데이터 집합을 관리할 때에는 데이터베이스가 적절합니다. 그러나 테라바이트 규모의 데이터가 포함된 빅데이터 집합의 경우에는 종종 기존의 SQL 데이터베이스가 이상적인 솔루션이 아닙니다. 데이터베이스 로드가 증가하고 성능이 저하되면 데이터베이스 관리자들은 습관적으로 이러한 더 큰 데이터 집합을 다루기 위해 규모를 확장하고 더 큰 하드웨어를 구매합니다.

Hive는 대규모 데이터 집합을 쿼리할 때 사용자가 규모를 확장할 수 있도록 하여 빅데이터와 관련된 문제를 해결합니다. Hive는 MapReduce를 사용해 여러 노드에 걸쳐 병렬로 데이터를 쿼리하여, 로드가 증가하면 데이터베이스를 여러 호스트에 분산합니다.

Hive와 HiveQL은 또한 데이터 쿼리 시 Java로 MapReduce 작업을 작성하는 것에 대한 대안을 제공합니다. SQL과 유사한 간단한 래퍼가 제공되는데, 사용자는 HiveQL로 쿼리를 작성한 다음 HDInsight를 이용해 MapReduce로 컴파일하고 클러스터에서 실행할 수 있습니다.

MapReduce 프레임워크에 익숙한 프로그래머는 Hive를 사용해 사용자 지정 매퍼와 리듀서를 연결하여, HiveQL 언어의 기본 제공 기능에서 지원하지 않을 수 있는 좀 더 정교한 분석을 수행할 수 있습니다.

Hive는 변경할 수 없는 대량의 데이터(예: 웹 로그)를 일괄 처리하는 데 가장 적합합니다. 그러나 매우 빠른 응답 시간이 필요한 트랜잭션 응용 프로그램(예: 데이터베이스 관리 시스템)에는 적합하지 않습니다. Hive는 규모 확장성(Hadoop 클러스터에 더 많은 컴퓨터를 동적으로 추가할 수 있음), 연결 확장성(MapReduce 프레임워크 내에서 및 기타 프로그래밍 인터페이스와) 및 내결함성에 맞게 최적화되었습니다. 대기 시간은 주요 설계 고려 사항이 아닙니다.

일반적으로 응용 프로그램은 오류, 예외 및 기타 코드 문제를 로그 파일에 저장합니다. 따라서 관리자는 로그 파일의 데이터를 사용하여 발생하는 문제를 검토하고 오류 또는 기타 문제(예: 성능)와 관련된 메트릭을 생성할 수 있습니다. 이러한 로그 파일은 대개 크기가 상당히 크며, 응용 프로그램에서 지능적으로 사용하기 위해 처리 및 마이닝을 거쳐야 하는 풍부한 데이터를 포함합니다.

따라서 로그 파일은 빅데이터의 전형적인 예입니다. HDInsight는 손쉬운 데이터 요약, 특별 쿼리, Hadoop 호환 파일 시스템(예: Azure Blob 저장소)에 저장된 빅데이터 집합의 분석 등을 지원하는 Hive 데이터 웨어하우스 시스템을 제공합니다.

## <span id="uploaddata"></span></a>Blob 저장소에 데이터 파일 업로드

HDInsight는 Azure Blob 저장소 컨테이너를 기본 파일 시스템으로 사용합니다. 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

이 문서에서는 HDInsight 클러스터로 배포한 log4j 샘플 파일(*\\example\\data\\sample.log*에 저장됨)을 사용합니다. 파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]` 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들면 다음과 같습니다.

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

파일에 액세스하려면 다음 구문을 사용합니다.

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

예를 들면 다음과 같습니다.

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer*를 컨테이너 이름으로, *mystorage*를 Blob 저장소 계정 이름으로 교체합니다.

파일은 기본 파일 시스템에 저장되므로 다음을 사용해 파일에 액세스할 수도 있습니다.

    wasb:///example/data/sample.log
    /example/data/sample.log

자신의 log4j 파일을 생성하려면 [Apache Log4j][Apache Log4j] 로깅 유틸리티를 사용하세요. Azure Blob 저장소에 데이터를 업로드하는 방법에 대한 자세한 내용은 [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]를 참조하세요.

## <span id="runhivequeries"></span></a> PowerShell을 사용하여 Hive 쿼리 실행

마지막 섹션에서는 sample.log라는 log4j 파일을 기본 파일 시스템 컨테이너에 업로드했습니다. 이 섹션에서는 HiveQL을 실행하여 Hive 테이블을 만들고, Hive 테이블에 데이터를 로드하고, 데이터를 쿼리하여 오류 로그의 개수를 알아보겠습니다.

이 문서에서는 Azure PowerShell cmdlet을 사용하여 Hive 쿼리를 실행하는 방법에 대한 지침을 제공합니다. 이 섹션을 수행하기 전에 이 항목의 시작 부분에 있는 **필수 조건** 섹션에서 설명한 것처럼, 먼저 로컬 환경을 설정하고 Azure에 대한 연결을 구성해야 합니다.

Hive 쿼리는 **Start-AzureHDInsightJob** cmdlet 또는 **Invoke-Hive** cmdlet을 사용하여 PowerShell에서 실행할 수 있습니다.

**Start-AzureHDInsightJob을 사용하여 Hive 쿼리를 실행하려면**

1.  Azure PowerShell 콘솔 창을 엽니다. 지침은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]에서 찾아볼 수 있습니다.
2.  다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트에서 변수를 설정하고 실행합니다.

        # Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  다음 스크립트를 실행하여 HiveQL 쿼리를 정의합니다.

        # HiveQL queries
        # Use the internal table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                       "CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ';" +
                       "LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/sample.log' OVERWRITE INTO TABLE log4jLogs;" +
                       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

        # Use the external table option. 
        $queryString = "DROP TABLE log4jLogs;" +
                        "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/';" +
                        "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

    LOAD DATA HiveQL 명령을 실행하면 데이터 파일이 \\hive\\warehouse\\ 폴더로 이동합니다. DROP TABLE 명령을 실행하면 테이블과 데이터 파일이 삭제됩니다. 내부 테이블 옵션을 사용하고 있으며 스크립트를 다시 실행하려는 경우 sample.log 파일을 다시 업로드해야 합니다. 데이터 파일을 유지하려면 스크립트에 표시된 대로 CREATE EXTERNAL TABLE 명령을 사용해야 합니다.

    데이터 파일이 다른 컨테이너 또는 저장소 계정에 있는 상황에서는 외부 테이블을 사용할 수도 있습니다.

    스크립트를 다시 실행하고자 하며 log4jlogs 테이블이 이미 있는 경우에는 먼저 DROP TABLE을 사용하세요.

5.  다음 스크립트를 실행하여 Hive 작업 정의를 만듭니다.

        # Create a Hive job definition 
        $hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString 

    -File 스위치를 사용하여 HDFS에서 HiveQL 스크립트 파일을 지정할 수도 있습니다.

6.  다음 스크립트를 실행하여 Hive 작업을 제출합니다.

        # Submit the job to the cluster 
        Select-AzureSubscription $subscriptionName
        $hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

7.  다음 스크립트를 실행하여 Hive 작업이 완료되기를 기다립니다.

        # Wait for the Hive job to complete
        Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

8.  다음 스크립트를 실행하여 표준 출력을 인쇄합니다.
9.  # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    결과는 다음과 같습니다.

        [ERROR] 3

**Invoke-Hive를 사용하여 Hive 쿼리를 제출하려면**

1.  Azure PowerShell 콘솔 창을 엽니다.
2.  다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  변수를 설정한 후 실행합니다.

        $clusterName = "<HDInsightClusterName>"

4.  다음 스크립트를 실행하여 HiveQL 쿼리를 호출합니다.

        Use-AzureHDInsightCluster $clusterName 
        $response = Invoke-Hive -Query @"
            SELECT * FROM hivesampletable
                WHERE devicemake LIKE "HTC%"
                LIMIT 10; 
        "@

        Write-Host $response

    출력은 다음과 같습니다.

    ![PowerShell Invoke-Hive output][PowerShell Invoke-Hive output]

    더 긴 HiveQL 쿼리에는 PowerShell Here-Strings 또는 HiveQL 스크립트 파일을 사용하는 것이 좋습니다. 다음 샘플은 Invoke-Hive cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 WASB에 업로드해야 합니다.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Here-Strings에 대한 자세한 내용은 [Windows PowerShell Here-Strings 사용][Windows PowerShell Here-Strings 사용]을 참조하세요.

## <span id="nextsteps"></span></a>다음 단계

Hive를 사용하면 SQL과 유사한 쿼리 언어로 손쉽게 데이터를 쿼리할 수 있으며, HDInsight에서 사용할 수 있는 기타 구성 요소는 데이터 이동과 변형 등의 보완 기능을 제공합니다. 자세한 내용은 다음 문서를 참조하세요.

-   [Azure HDInsight 시작][HDInsight 시작]
-   [HDInsight를 사용하여 비행 지연 데이터 분석][HDInsight를 사용하여 비행 지연 데이터 분석]
-   [HDInsight에서 Oozie 사용][HDInsight에서 Oozie 사용]
-   [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]
-   [HDInsight와 함께 Pig 사용][HDInsight와 함께 Pig 사용]
-   [Azure HDInsight SDK 문서][Azure HDInsight SDK 문서]

  [Apache Hive]: http://hive.apache.org/
  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [Hive 사용 사례]: #usage
  [Azure Blob 저장소에 데이터 파일 업로드]: #uploaddata
  [PowerShell을 사용하여 Hive 쿼리 실행]: #runhivequeries
  [다음 단계]: #nextsteps
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Windows PowerShell Here-Strings 사용]: http://technet.microsoft.com/ko-kr/library/ee692792.aspx
  [HDInsight를 사용하여 비행 지연 데이터 분석]: ../hdinsight-analyze-flight-delay-data/
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight와 함께 Pig 사용]: ../hdinsight-use-pig/
  [Azure HDInsight SDK 문서]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
