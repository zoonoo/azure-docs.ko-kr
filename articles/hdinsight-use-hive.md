<properties 
	pageTitle="HDInsight에서 Hadoop Hive 사용 | Azure" 
	description="HDInsight에서 Hive를 사용하는 방법에 대해 알아봅니다. 로그 파일을 HDInsight 테이블에 대한 입력으로 사용하고 HiveQL을 사용해 데이터를 쿼리하고 기본 통계를 보고합니다." 
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/25/2014" 
	ms.author="jgao"/>

# HDInsight에서 Hadoop과 Hive 사용

[Apache Hive][apache-hive]에서는 SQL과 같은 스크립트 언어인 *HiveQL*을 통해 MapReduce 작업을 실행할 수 있습니다. Hive는 대량의 데이터 요약, 쿼리 및 분석을 수행할 수 있는 Hadoop용 데이터 웨어하우스 시스템입니다. 이 문서에서는 HiveQL을 사용하여 HDInsight 클러스터 프로비전의 일부분으로 제공되는 샘플 데이터 파일을 쿼리합니다.


**필수 조건:**

- **HDInsight 클러스터**를 미리 프로비전해야 합니다. Azure 포털에서 프로비전하는 방법을 자세히 알아보려면 [HDInsight 시작][hdinsight-get-started]을 참조하세요. HDInsight 클러스터를 만드는 기타 여러 방법에 대한 지침은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

- 워크스테이션에 **Azure PowerShell**을 미리 설치해 두어야 합니다. 설치 및 구성 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install-configure]을 참조하세요.

## 이 문서의 내용

* [Hive 사용 사례](#usage)
* [Hive 테이블용 데이터 업로드](#uploaddata)
* [PowerShell을 사용하여 Hive 쿼리 실행](#runhivequeries)
* [Visual Studio용 HDInsight 도구를 사용하여 Hive 쿼리 실행](#runhivefromvs)
* [성능 개선을 위해 Tez 사용](#usetez)
* [다음 단계](#nextsteps)

## <a id="usage"></a>Hive 사용 사례

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive 프로젝트는 대체로 구조화되지 않은 데이터를 기반으로 구성되며, 사용자는 이 프로젝트를 통해 해당 데이터를 쿼리할 수 있습니다. 또한 Java 기반 MapReduce 프레임워크를 통한 추상화 계층을 제공하므로 사용자가 Java 또는 MapReduce에 대해 몰라도 데이터를 쿼리할 수 있습니다. HiveQL(Hive 쿼리 언어)을 사용하여 T-SQL과 같은 문이 포함된 쿼리를 작성할 수 있습니다. HiveQL 쿼리는 HDInsight를 통해 MapReduce로 컴파일되고 클러스터에서 실행됩니다. Hive의 기타 이점은 다음과 같습니다.

- MapReduce 프레임워크에 익숙한 프로그래머는 Hive를 사용해 사용자 지정 매퍼와 리듀서를 연결하여, HiveQL 언어의 기본 제공 기능에서 지원하지 않을 수 있는 좀 더 정교한 분석을 수행할 수 있습니다.
- Hive는 변경할 수 없는 대량의 데이터(예: 웹 로그)를 일괄 처리하는 데 가장 적합합니다. 그러나 매우 빠른 응답 시간이 필요한 트랜잭션 응용 프로그램(예: 데이터베이스 관리 시스템)에는 적합하지 않습니다.
- Hive는 규모 확장성(Hadoop 클러스터에 더 많은 컴퓨터를 동적으로 추가할 수 있음), 연결 확장성(MapReduce 프레임워크 내에서 및 기타 프로그래밍 인터페이스와) 및 내결함성에 맞게 최적화되었습니다. 대기 시간은 주요 설계 고려 사항이 아닙니다.

## <a id="uploaddata"></a>Hive 테이블용 데이터 업로드

HDInsight는 Azure Blob 저장소 컨테이너를 Hadoop 클러스터의 기본 파일 시스템으로 사용합니다. 클러스터 프로비전의 일부분으로 일부 샘플 데이터 파일이 Blob 저장소에 추가됩니다. 이 문서에서는 HDInsight 클러스터와 함께 배포되며 Blob 저장소 컨테이너의 **/example/data/sample.log**에 저장되는 *log4j* 샘플 파일을 사용합니다. 파일 내부의 각 로그는 유형과 심각도를 표시하는 `[LOG LEVEL]` 필드가 포함된 필드의 줄로 구성되어 있습니다. 예를 들면 다음과 같습니다.

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

위 예제에서 로그 수준은 ERROR입니다.

> [AZURE.NOTE] [Apache Log4j][apache-log4j] 로깅 유틸리티를 사용하여 log4j 파일을 직접 생성한 다음 Blob 컨테이너에 업로드할 수도 있습니다. 해당 지침은 [HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요. HDInsight에서 Azure Blob 저장소를 사용하는 방법에 대한 자세한 내용은 [HDInsight에서 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

HDInsight에서는 **wasb** 접두사를 사용하여 Blob 저장소에 저장된 파일에 액세스할 수 있습니다. 예를 들어 sample.log 파일에 액세스하려는 경우 다음 구문을 사용합니다.

	wasb:///example/data/sample.log

WASB가 HDInsight의 기본 저장소이므로 **/example/data/sample.log**를 사용하여 파일에 액세스할 수도 있습니다.

> [AZURE.NOTE] 위의 구문 **wasb:///**는 HDInsight 클러스터의 기본 저장소 컨테이너에 저장된 파일에 액세스하는 데 사용됩니다. 클러스터를 프로비전할 때 추가 저장소 계정을 지정한 경우 이러한 계정에 저장된 파일에 액세스하려면 컨테이너 이름과 저장소 계정 주소를 지정하여 데이터에 액세스하면 됩니다. 예를 들면 **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**와 같습니다.

## <a id="runhivequeries"></a> PowerShell을 사용하여 Hive 쿼리 실행

Hive 쿼리는 **Start-AzureHDInsightJob** cmdlet 또는 **Invoke-Hive** cmdlet을 사용하여 PowerShell에서 실행할 수 있습니다.

* **Start-AzureHDInsightJob**은 HDInsight 클러스터에서 Hive, Pig 및 MapReduce 작업을 시작하는 데 사용되는 일반 작업 실행입니다. **Start-AzureHDInsightJob**은 비동기 작업이며 작업이 완료되기 전에 반환됩니다. 작업에 대한 정보가 반환되고 **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob**, **Get-AzureHDInsightJobOutput** 등의 cmdlet에서 이 정보를 사용할 수 있습니다.  **STDOUT** 또는 **STDERR**에 작업을 통해 기록된 정보를 검색하려면 **Get-AzureHDInsightJobOutput**을 사용해야 합니다.

* **Invoke-Hive**는 Hive 쿼리를 실행하고, 쿼리가 완료될 때까지 기다리고 나서, 단일 작업으로 쿼리 출력을 검색합니다.

1. Azure PowerShell 콘솔 창을 엽니다. 지침은 [Azure PowerShell 설치 및 구성][powershell-install-configure]에서 찾아볼 수 있습니다.
2. 다음 명령을 실행하여 Azure 구독에 연결합니다.

		Add-AzureAccount

	Azure 계정 자격 증명을 입력하라는 메시지가 표시됩니다.

2. 다음 스크립트에서 변수를 설정하고 실행합니다.

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. 다음 스크립트를 실행하여 샘플 데이터로 **log4jLogs**라는 새 테이블을 만듭니다.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	HiveQL 문은 다음 작업을 수행합니다.

	* **DROP TABLE** - 테이블과 데이터 파일을 삭제합니다(테이블이 이미 있는 경우).
	* **CREATE EXTERNAL TABLE** - Hive에서 새 'external' 테이블을 만듭니다. 외부 테이블은 Hive에 테이블 정의만 저장하고, 데이터는 원래 위치에 남아 있습니다.
	* **ROW FORMAT** - 데이터의 형식 지정 방식을 Hive에 알립니다. 이 경우, 각 로그의 필드는 공백으로 구분됩니다.
	* **STORED AS TEXTFILE LOCATION** - 데이터가 저장된 위치와 텍스트로 저장되었다는 것을 Hive에 알립니다.
	* **SELECT** - **t4** 열에 **[ERROR]** 값이 포함된 모든 행의 수를 선택합니다. 이 값이 포함된 3개 행이 있으므로 **3** 값이 반환되어야 합니다.

	> [AZURE.NOTE] 자동화된 데이터 업로드 프로세스와 같은 외부 원본이나 또 다른 MapReduce 작업을 통해 기본 데이터를 업데이트해야 하지만 Hive 쿼리에서 항상 최신 데이터를 사용하려고 할 경우 외부 테이블을 사용해야 합니다.
	>
	> 외부 테이블을 삭제하면 데이터**뿐만 아니라** 테이블 정의도 삭제됩니다.


4. 다음 스크립트를 실행하여 이전 쿼리에서 Hive 작업 정의를 만듭니다.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	-File 스위치를 사용하여 HDFS에서 HiveQL 스크립트 파일을 지정할 수도 있습니다.

5. 다음 스크립트를 실행하여 Hive 작업을 제출합니다.

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. 다음 스크립트를 실행하여 Hive 작업이 완료되기를 기다립니다.

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. 다음 스크립트를 실행하여 표준 출력을 인쇄합니다.

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	결과는 다음과 같습니다.

		[ERROR] 3

	위의 결과는 *sample.log* 파일에 ERROR 로그 인스턴스가 3개 있음을 의미합니다.

4. **Invoke-Hive**를 사용하려면 먼저 클러스터를 사용하도록 설정해야 합니다.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. 다음 스크립트를 사용하여 **Invoke-Hive** cmdlet을 통해 **errorLogs**라는 새 'internal' 테이블을 만듭니다.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	이러한 문은 다음 작업을 수행합니다.

	* **CREATE TABLE IF NOT EXISTS** - 테이블을 만듭니다(아직 없는 경우). **EXTERNAL** 키워드가 사용되지 않으므로 Hive 데이터 웨어하우스에 저장되고 Hive에서 완전히 관리되는 'internal' 테이블입니다.
	* **STORED AS ORC** - 데이터를 ORC(Optimized Row Columnar) 형식으로 저장합니다. Hive 데이터를 저장하기 위한 고도로 최적화되고 효율적인 형식입니다.
	* **INSERT OVERWRITE ... SELECT** - **[ERROR]**가 포함된 **log4jLogs** 테이블에서 행을 선택하고 데이터를 **errorLogs** 테이블에 삽입합니다.

	> [AZURE.NOTE] **EXTERNAL** 테이블과 달리 내부 테이블을 삭제하면 기본 데이터도 삭제됩니다.

	출력은 다음과 같이 표시됩니다.

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] 더 긴 HiveQL 쿼리에는 PowerShell Here-Strings 또는 HiveQL 스크립트 파일을 사용할 수 있습니다. 다음 코드 조각은 *Invoke-Hive* cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 WASB에 업로드해야 합니다.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Here-Strings에 대한 자세한 내용은 [Windows PowerShell Here-Strings 사용][powershell-here-strings]을 참조하세요.

5. t4 열에 **[ERROR]**가 포함된 행만 **errorLogs** 테이블에 저장되었는지 확인하려면 다음 문을 사용하여 **errorLogs**의 모든 행을 반환합니다.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	데이터 중 t4 열에 모두 **[ERROR]**가 포함된 3개 행이 반환되어야 합니다.


> [AZURE.NOTE] 필요한 경우 추가 분석을 위해 쿼리 출력을 Microsoft Excel로 가져올 수도 있습니다. 해당 지침은 [파워 쿼리로 Hadoop에 Excel 연결][import-to-excel]을 참조하세요.

## <a id="runhivefromvs"></a>Visual Studio를 사용하여 Hive 쿼리 실행
Visual Studio용 HDInsight 도구는 Azure SDK for .NET 버전 2.5 이상과 함께 제공됩니다.  Visual Studio에서 이 도구를 사용하여 HDInsight 클러스터에 연결하고, Hive 테이블을 만들고, Hive 쿼리를 실행할 수 있습니다.  자세한 내용은 [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]을 참조하세요.



## <a id="usetez"></a>성능 개선을 위해 Tez 사용

[Apache Tez][apache-tez]는 Hive와 같이 데이터를 많이 사용하는 응용 프로그램을 큰 규모에서도 훨씬 더 효율적으로 실행할 수 있는 프레임워크입니다. HDInsight의 최신 릴리스에서는 이제 Hive를 Tez에서 실행할 수 있습니다.  현재는 이러한 실행 기능이 기본적으로 해제되어 있으며 사용하도록 설정해야 합니다.  이후 클러스터 버전에서는 이 기능이 기본적으로 설정될 예정입니다. Tez를 사용하려면 Hive 쿼리에 대해 다음 값을 설정해야 합니다.

		set hive.execution.engine=tez;

이 값을 쿼리 시작 부분에 배치하여 쿼리별로 제출할 수 있습니다.  클러스터를 만들 때 구성 값을 설정하여 클러스터에 대해 이 기능을 기본적으로 설정할 수도 있습니다.  자세한 내용은 다음에서 찾을 수 있습니다.  [HDInsight 클러스터 프로비전][hdinsight-provision].

[Tez의 Hive 디자인 문서][hive-on-tez-wiki]에는 선택 가능한 구현 및 튜닝 구성과 관련한 여러 세부 정보가 포함되어 있습니다.


## <a id="nextsteps"></a>다음 단계

Hive를 사용하면 SQL과 유사한 쿼리 언어로 손쉽게 데이터를 쿼리할 수 있으며, HDInsight에서 사용할 수 있는 기타 구성 요소는 데이터 이동과 변형 등의 보완 기능을 제공합니다. 자세한 내용은 다음 문서를 참조하세요.

* [Visual Studio용 HDInsight Hadoop 도구 사용 시작][1]
* [HDInsight에서 Oozie 사용][hdinsight-use-oozie]
* [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]
* [HDInsight와 함께 Pig 사용](hdinsight-use-pig.md)
* [HDInsight를 사용하여 비행 지연 데이터 분석][hdinsight-analyze-flight-data]
* [Azure HDInsight SDK 설명서][hdinsight-sdk-documentation]
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [Azure HDInsight 시작](hdinsight-get-started.md)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/ko-kr/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png
<!--HONumber=42-->
