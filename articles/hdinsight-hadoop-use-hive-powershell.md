<properties
   pageTitle="HDInsight에서 Hadoop Hive 사용 | Azure"
   description="PowerShell 통해 HDInsight에서 Hadoop Hive를 사용합니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

#PowerShell을 사용하여 Hive 쿼리 실행

[AZURE.INCLUDE [hive-selector](../includes/hdinsight-selector-use-hive.md)]

이 문서는 PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 Hive 쿼리를 실행하는 예제를 제공합니다.

> [AZURE.NOTE] 이 문서에서는 예제에 사용된 HiveQL 문이 수행하는 작업에 대한 자세한 설명을 제공하지 않습니다. 이 예제에 사용된 HiveQL에 대한 자세한 내용은 <a href="../hdinsight-use-hive/" target="_blank">HDInsight에서 Hadoop과 Hive 사용</a>을 참조하세요.


##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

* Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 또는 Linux 기반)

* <a href="http://azure.microsoft.com/documentation/articles/install-configure-powershell/" target="_blank">Azure PowerShell</a>


##<a id="powershell"></a>PowerShell을 사용하여 Hive 쿼리 실행

Azure PowerShell은 HDInsight에서 Hive 쿼리를 원격으로 실행할 수 있는 *cmdlets*을 제공합니다. 내부적으로는 HDInsight 클러스터에서 실행되는 <a href="https://cwiki.apache.org/confluence/display/Hive/WebHCat" target="_blank">WebHCat</a>(이전의 Templeton)에 대한 REST 호출을 사용하여 이 작업을 수행합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 Hive 쿼리를 실행할 때 사용됩니다.

* **Add-AzureAccount** - Azure 구독으로 PowerShell을 인증합니다.

* **New-AzureHDInsightHiveJobDefinition** - 지정한 HiveQL 문을 사용하여 새 *job definition*을 만듭니다.

* **Start-AzureHDInsightJob** - HDInsight로 작업 정의를 보내고, 작업을 시작하고, 작업 상태를 확인하는 데 사용할 수 있는 *job* 개체를 반환합니다.

* **Wait-AzureHDInsightJob** - 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료될 때까지 기다리거나 대기 시간이 초과 될 때까지 기다립니다.

* **Get AzureHDInsightJobOutput** -작업의 출력을 검색하는데 사용됨

* **Invoke-Hive** - HiveQL 문 및 완료된 블록을 실행하는데 사용됨 그런 다음 결과를 반환합니다.

* **Use-AzureHDInsightCluster** - **Invoke-Hive** 명령에 사용하기 위해 현재 클러스터를 설정합니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다. 

1. 편집기를 사용하여 다음 코드를 **hivejob.ps1**으로 저장합니다. **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}
		
		#Specify the cluster name
		$clusterName = "CLUSTERNAME" 
		
		#HiveQL
		$queryString = "DROP TABLE log4jLogs;" +
				       "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
				       "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"
		
		#Create an HDInsight Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString
		
		#Submit the job to the cluster
		Write-Host "Start the Hive job..." -ForegroundColor Green
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition
		
		#Wait for the Hive job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600
		
		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput

2. 새 **Microsoft Azure PowerShell** 프롬프트를 엽니다. **hivejob.ps1** 파일의 디렉터리 위치를 변경한 다음을 사용하여 스크립트를 실행합니다.

		.\hivejob.ps1

7. 작업이 완료되면 다음과 유사한 정보가 반환됩니다.

		Display the standard output...
		[ERROR]	3

4. 앞서 설명한 것처럼 **Invoke-hive**는 쿼리를 실행하고 응답을 기다리는데 사용할 수 있습니다. 다음 명령을 사용하여 **CLUSTERNAME**을 클러스터의 이름으로 바꿉니다.

		Use-AzureHDInsightCluster CLUSTERNAME
		Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		SELECT * FROM errorLogs;
		"@

	The output will look like the following.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	incorrect	id	
		2012-02-03	18:55:54	SampleClass1	[ERROR]	incorrect	id	
		2012-02-03	19:25:27	SampleClass4	[ERROR]	incorrect	id

	> [AZURE.NOTE] 더 긴 HiveQL 쿼리에는 PowerShell Here-Strings 또는 HiveQL 스크립트 파일을 사용할 수 있습니다. 다음 코드 조각은 *Invoke-Hive* cmdlet을 사용하여 HiveQL 스크립트 파일을 실행하는 방법을 보여 줍니다. HiveQL 스크립트 파일은 WASB에 업로드해야 합니다.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Here-Strings에 대한 자세한 내용은 <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Windows PowerShell Here-Strings 사용</a>을 참조하세요.

##<a id="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않은 경우, 처리하는 동안 오류가 발생했을 수 있습니다. 이 작업에 대한 오류 정보를 보려면 **hivejob.ps1** 파일의 끝에 다음 내용을 추가하고 파일을 저장한 다음 다시 실행합니다.

	# Print the output of the Hive job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardError

이 작업을 실행하면 서버에서 STDERR로 기록되는 정보를 반환하며 이 정보는 작업이 실패하는 이유를 확인하는데 도움이 될 수 있습니다.

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 Hive 쿼리 실행 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

##<a id="nextsteps"></a>다음 단계

HDInsight Hive에 대한 일반적인 내용입니다.

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 내용입니다.

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

* [HDInsight에서 Hadoop과 MapReduce 사용](hdinsight-use-mapreduce.md)

<!--HONumber=47-->
