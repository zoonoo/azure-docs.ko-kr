<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="HDInsight에서 Hadoop Hive 사용 | Azure" metaKeywords="" description="HDInsight에서 Hive를 사용하는 방법에 대해 알아봅니다. 로그 파일을 HDInsight 테이블에 대한 입력으로 사용하고 HiveQL을 사용해 데이터를 쿼리하고 기본 통계를 보고합니다." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight에서 Hadoop Hive 사용" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# HDInsight에서 Hadoop과 Hive 사용

[Apache Hive][Apache Hive]에서는 SQL 스타일 스크립팅 언어인 *HiveQL*을 통해 MapReduce 작업을 실행할 수 있습니다. Hive는 대량의 데이터 요약, 쿼리 및 분석을 수행할 수 있는 Hadoop용 데이터 웨어하우스 시스템입니다. 이 문서에서는 HiveQL을 사용하여 HDInsight 클러스터 프로비전의 일부분으로 제공되는 샘플 데이터 파일을 쿼리합니다.

**필수 조건:**

-   **HDInsight 클러스터**를 미리 프로비전해야 합니다. Azure 포털에서 프로비전하는 방법을 자세히 알아보려면 [HDInsight 시작][HDInsight 시작]을 참조하세요. HDInsight 클러스터를 만드는 기타 여러 방법에 대한 지침은 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

-   워크스테이션에 **Azure PowerShell**을 미리 설치해두어야 합니다. 설치 및 구성 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell 설치 및 구성]을 참조하세요.

**예상 완료 시간:** 30분

## 이 문서에서는 다음을 수행합니다.

-   [Hive 사용 사례][Hive 사용 사례]
-   [Hive 테이블용 데이터 업로드][Hive 테이블용 데이터 업로드]
-   [PowerShell을 사용하여 Hive 쿼리 실행][PowerShell을 사용하여 Hive 쿼리 실행]
-   [성능 개선을 위해 Tez 사용][성능 개선을 위해 Tez 사용]
-   [다음 단계][다음 단계]

## <span id="usage"></span></a>Hive 사용 사례

![HDI.HIVE.Architecture][HDI.HIVE.Architecture]

Hive에서는 대체로 비구조적인 데이터 집합에서 일종의 구조를 제공하며(Hadoop의 특징) HiveQL을 사용하여 해당 데이터를 쿼리할 수 있도록 합니다. 또한 Java 기반 MapReduce 프레임워크를 통한 추상화 계층을 제공하므로 사용자가 Java 또는 MapReduce에 대해 몰라도 데이터를 쿼리할 수 있습니다. HiveQL은 SQL 스타일의 간단한 래퍼를 제공하므로 HiveQL로 작성한 쿼리를 HDInsight에서 MapReduce로 컴파일한 다음 클러스터에서 실행할 수 있습니다. Hive의 기타 이점은 다음과 같습니다.

-   MapReduce 프레임워크에 익숙한 프로그래머는 Hive를 사용해 사용자 지정 매퍼와 리듀서를 연결하여, HiveQL 언어의 기본 제공 기능에서 지원하지 않을 수 있는 좀 더 정교한 분석을 수행할 수 있습니다.
-   Hive는 변경할 수 없는 대량의 데이터(예: 웹 로그)를 일괄 처리하는 데 가장 적합합니다. 그러나 매우 빠른 응답 시간이 필요한 트랜잭션 응용 프로그램(예: 데이터베이스 관리 시스템)에는 적합하지 않습니다.
-   Hive는 규모 확장성(Hadoop 클러스터에 더 많은 컴퓨터를 동적으로 추가할 수 있음), 연결 확장성(MapReduce 프레임워크 내에서 및 기타 프로그래밍 인터페이스와) 및 내결함성에 맞게 최적화되었습니다. 대기 시간은 주요 설계 고려 사항이 아닙니다.

## <span id="uploaddata"></span></a>Hive 테이블용 데이터 업로드

HDInsight는 Azure Blob 저장소 컨테이너를 Hadoop 클러스터의 기본 파일 시스템으로 사용합니다. 클러스터 프로비전의 일부분으로 일부 샘플 데이터 파일이 Blob 저장소에 추가됩니다. 클러스터에서 Hive 쿼리를 실행하는 데 이러한 샘플 데이터 파일을 사용할 수 있습니다. 원하는 경우 클러스터와 연결된 Blob 저장소 계정에 고유한 데이터 파일을 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]를 참조하세요. HDInsight에서 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][HDInsight에서 Azure Blob 저장소 사용]을 참조하세요.

이 문서에서는 HDInsight 클러스터와 함께 배포되며 Blob 저장소 컨테이너의 *\\example\\data\\sample.log*에 저장되는 *log4j* 샘플 파일을 사용합니다. [Apache Log4j][Apache Log4j] 로깅 유틸리티를 사용하여 log4j 파일을 직접 생성한 다음 Blob 컨테이너에 업로드할 수도 있습니다. 파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]` 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들면 다음과 같습니다.

    2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937 

위 예제에서 로그 수준은 ERROR입니다.

응용 프로그램 내에서 다음 구문을 사용하여 파일에 액세스할 수 있습니다.

    wasb://<containerName>@<AzureStorageName>.blob.core.windows.net

예를 들어 sample.log 파일에 액세스하려는 경우 다음 구문을 사용합니다.

    wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log

*mycontainer*를 Azure Blob 컨테이너 이름으로, *mystorage*를 Azure 저장소 계정 이름으로 바꿉니다.

파일은 기본 파일 시스템에 저장되므로 다음을 사용해 파일에 액세스할 수도 있습니다.

    wasb:///example/data/sample.log
    /example/data/sample.log

## <span id="runhivequeries"></span></a> PowerShell을 사용하여 Hive 쿼리 실행

이전 섹션에서 Hive 쿼리를 실행하는 데 사용할 *sample.log* 파일을 식별했습니다. 이 섹션에서는 HiveQL을 실행하여 Hive 테이블을 만들고, Hive 테이블에 샘플 데이터를 로드하고, 데이터를 쿼리하여 파일에 있는 오류 로그의 개수를 알아보겠습니다.

이 문서에서는 Azure PowerShell cmdlet을 사용하여 Hive 쿼리를 실행하는 방법에 대한 지침을 제공합니다. 이 섹션을 진행하기 전에 이 항목의 시작 부분에 있는 **필수 조건** 섹션의 설명에 따라 먼저 로컬 환경을 설정하고 Azure에 대한 연결을 구성해야 합니다.

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

4.  다음 스크립트를 실행하여 HiveQL 쿼리를 정의합니다. *내부* 또는 *외부* Hive 테이블을 만들 수 있습니다.

    -   내부 테이블을 만드는 경우 사용하는 샘플 데이터는 기존 위치에서 \\hive\\warehouse\\\<*테이블 이름\>* 폴더로 이동됩니다. 따라서 내부 테이블을 삭제하면 연결된 데이터도 삭제됩니다. 내부 테이블을 사용하는 경우 스크립트를 다시 실행하려면 *sample.log* 파일을 Blob 저장소에 다시 업로드해야 합니다.
    -   외부 테이블을 사용하는 경우에는 데이터가 원래 위치에서 이동되지 않습니다. 데이터 파일이 다른 컨테이너 또는 저장소 계정에 있는 상황에서도 외부 테이블을 사용할 수 있습니다.

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

    DROP TABLE 명령은 테이블이 이미 있는 경우 테이블과 데이터 파일을 삭제합니다. LOAD DATA HiveQL 명령은 \\example\\data to the \\hive\\warehouse\\\<*테이블 이름\>* 폴더에서 데이터 파일을 제거합니다.

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

        # Print the standard error and the standard output of the Hive job.
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

    ![HDI.HIVE.PowerShell][HDI.HIVE.PowerShell]

    결과는 다음과 같습니다.

        [ERROR] 3

    위의 결과는 *sample.log* 파일에 ERROR 로그 인스턴스가 3개 있음을 의미합니다.

필요한 경우 추가 분석을 위해 쿼리 출력을 Microsoft Excel로 가져올 수도 있습니다. 해당 지침은 [파워 쿼리로 Hadoop에 Excel 연결][파워 쿼리로 Hadoop에 Excel 연결]을 참조하세요.

**Invoke-Hive를 사용하여 Hive 쿼리를 제출하려면**

1.  Azure PowerShell 콘솔 창을 엽니다.
2.  다음 명령을 실행하여 Azure 구독에 연결합니다.

        Add-AzureAccount

    Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

3.  다음 스크립트에서 변수를 설정하고 실행합니다.

        # Provide Azure subscription name, Azure Storage account, and container.
        $subscriptionName = "<SubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"
        $containerName = "<AzureStorageContainerName>"

        # Provide HDInsight cluster name Where you want to run the Hive job
        $clusterName = "<HDInsightClusterName>"

4.  HDInsight 클러스터에 연결합니다.

        # Connect to the cluster
        Use-AzureHDInsightCluster $clusterName

5.  다음 스크립트를 실행하여 HiveQL 쿼리를 호출합니다.

        # Run the query to create an internal Hive table, load sample data
        $response = Invoke-Hive -Query @"
        >> DROP TABLE log4jLogs;
        >> CREATE TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW   
            FORMAT DELIMITED FIELDS TERMINATED BY ' ';
        >> LOAD DATA INPATH 'wasb://$containerName@$storageAccountName.blob.core.windows.net/example/data/
            sample.log' OVERWRITE INTO TABLE log4jLogs;
        >> SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4; 
        >> "@

6.  콘솔에 출력을 인쇄합니다.

        # print the output on the console
        Write-Host $response

    출력은 다음과 같이 표시됩니다.

    ![PowerShell Invoke-Hive output][PowerShell Invoke-Hive output]

    더 긴 HiveQL 쿼리에는 PowerShell Here-Strings 또는 HiveQL 스크립트 파일을 사용할 수 있습니다. 다음 코드 조각은 *Invoke-Hive* cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 WASB에 업로드해야 합니다.

        Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"

    Here-Strings에 대한 자세한 내용은 [Windows PowerShell Here-Strings 사용][Windows PowerShell Here-Strings 사용]을 참조하세요.

필요한 경우 추가 분석을 위해 쿼리 출력을 Microsoft Excel로 가져올 수도 있습니다. 해당 지침은 [파워 쿼리로 Hadoop에 Excel 연결][파워 쿼리로 Hadoop에 Excel 연결]을 참조하세요.

## <span id="usetez"></span></a>성능 개선을 위해 Tez 사용

[Apache Tez][Apache Tez]는 Hive와 같이 데이터를 많이 사용하는 응용 프로그램을 큰 규모에서도 훨씬 더 효율적으로 실행할 수 있는 프레임워크입니다. HDInsight의 최신 릴리스에서는 이제 Hive를 Tez에서 실행할 수 있습니다. 현재는 이러한 실행 기능이 기본적으로 해제되어 있으며 사용하도록 설정해야 합니다. 이후 클러스터 버전에서는 이 기능이 기본적으로 설정될 예정입니다. Tez를 사용하려면 Hive 쿼리에 대해 다음 값을 설정해야 합니다.

        set hive.execution.engine=tez;
        

이 값을 쿼리 시작 부분에 배치하여 쿼리별로 제출할 수 있습니다. 클러스터를 만들 때 구성 값을 설정하여 클러스터에 대해 이 기능을 기본적으로 설정할 수도 있습니다. 자세한 내용은 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]에서 확인할 수 있습니다.

[Tez의 Hive 디자인 문서][Tez의 Hive 디자인 문서]에는 선택 가능한 구현 및 튜닝 구성과 관련한 여러 세부 정보가 포함되어 있습니다.

## <span id="nextsteps"></span></a>다음 단계

Hive를 사용하면 SQL과 유사한 쿼리 언어로 손쉽게 데이터를 쿼리할 수 있으며, HDInsight에서 사용할 수 있는 기타 구성 요소는 데이터 이동과 변형 등의 보완 기능을 제공합니다. 자세한 내용은 다음 문서를 참조하세요.

-   [HDInsight에서 Oozie 사용][HDInsight에서 Oozie 사용]
-   [프로그래밍 방식으로 Hadoop 작업 제출][프로그래밍 방식으로 Hadoop 작업 제출]
-   [HDInsight와 함께 Pig 사용][HDInsight와 함께 Pig 사용]
-   [HDInsight를 사용하여 비행 지연 데이터 분석][HDInsight를 사용하여 비행 지연 데이터 분석]
-   [Azure HDInsight SDK 문서][Azure HDInsight SDK 문서]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]
-   [Azure HDInsight 시작][HDInsight 시작]

  [Apache Hive]: http://hive.apache.org/
  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [Azure PowerShell 설치 및 구성]: ../install-configure-powershell/
  [Hive 사용 사례]: #usage
  [Hive 테이블용 데이터 업로드]: #uploaddata
  [PowerShell을 사용하여 Hive 쿼리 실행]: #runhivequeries
  [성능 개선을 위해 Tez 사용]: #usetez
  [다음 단계]: #nextsteps
  [HDI.HIVE.Architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [HDInsight에서 Azure Blob 저장소 사용]: ../hdinsight-use-blob-storage
  [Apache Log4j]: http://en.wikipedia.org/wiki/Log4j
  [HDI.HIVE.PowerShell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
  [파워 쿼리로 Hadoop에 Excel 연결]: http://azure.microsoft.com/ko-kr/documentation/articles/hdinsight-connect-excel-power-query/
  [PowerShell Invoke-Hive output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
  [Windows PowerShell Here-Strings 사용]: http://technet.microsoft.com/ko-kr/library/ee692792.aspx
  [Apache Tez]: http://tez.apache.org
  [Tez의 Hive 디자인 문서]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [HDInsight에서 Oozie 사용]: ../hdinsight-use-oozie/
  [프로그래밍 방식으로 Hadoop 작업 제출]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [HDInsight와 함께 Pig 사용]: ../hdinsight-use-pig/
  [HDInsight를 사용하여 비행 지연 데이터 분석]: ../hdinsight-analyze-flight-delay-data/
  [Azure HDInsight SDK 문서]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx
