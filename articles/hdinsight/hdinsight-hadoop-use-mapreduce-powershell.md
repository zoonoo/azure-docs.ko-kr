<properties
   pageTitle="Hadoop과 MapReduce 및 PowerShell 사용 | Microsoft Azure"
   description="PowerShell을 사용하여 HDInsight에서 Hadoop으로 MapReduce 작업을 원격으로 실행하는 방법에 대해 알아봅니다."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#PowerShell을 사용하여 HDInsight에서 Hadoop과 Hive 쿼리 실행

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

이 문서에서는 Azure PowerShell을 사용하여 HDInsight 클러스터의 Hadoop에서 MapReduce 작업을 실행하는 예제를 제공합니다.

##<a id="prereq"></a>필수 조건

이 문서의 단계를 완료하려면 다음이 필요합니다.

- **Azure HDInsight(HDInsight의 Hadoop) 클러스터(Windows 기반 또는 Linux 기반)**

- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 구성](../powershell-install-configure.md)을 참조하세요.

##<a id="powershell"></a>Azure PowerShell을 사용하여 MapReduce 작업 실행

Azure PowerShell은 HDInsight에서 MapReduce 작업을 원격으로 실행할 수 있는 *cmdlet*을 제공합니다. 내부적으로는 HDInsight 클러스터에서 실행되는 [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)(이전의 Templeton)에 대한 REST 호출을 사용하여 이 작업을 수행합니다.

다음 cmdlet은 원격 HDInsight 클러스터에서 MapReduce 작업을 실행할 때 사용됩니다.

* **Add-AzureAccount**: Azure 구독으로 Azure PowerShell을 인증합니다.

* **New-AzureHDInsightMapReduceJobDefinition**:지정한 MapReduce 정보를 사용하여 새 *job definition*을 만듭니다.

* **Start-AzureHDInsightJob**: HDInsight로 작업 정의를 보내고, 작업을 시작하고, 작업 상태를 확인하는 데 사용할 수 있는 *job* 개체를 반환합니다.

* **Wait-AzureHDInsightJob**: 작업 개체를 사용하여 작업 상태를 확인합니다. 작업이 완료되거나 대기 시간이 초과될 때까지 기다립니다.

* **Get-AzureHDInsightJobOutput**: 작업 출력을 검색하는 데 사용됩니다.

다음 단계는 HDInsight 클러스터에서 작업을 실행하기 위해 이러한 cmdlet을 사용하는 방법에 대해 설명합니다.

1. 편집기를 사용하여 다음 코드를 **mapreducejob.ps1**로 저장합니다. **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Define the MapReduce job
		#NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
		# -JarFile = the JAR containing the MapReduce application
		# -ClassName = the class of the application
		# -Arguments = The input file, and the output directory
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
		                          -ClassName "wordcount" `
		                          -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

		#Submit the job to the cluster
		Write-Host "Start the MapReduce job..." -ForegroundColor Green
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition

		#Wait for the job to complete
		Write-Host "Wait for the job to complete..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600

		# Print the output
		Write-Host "Display the standard output..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardOutput

2. 새 **Azure PowerShell** 명령 프롬프트를 엽니다. **mapreducejob.ps1** 파일의 디렉터리 위치를 변경한 다음 명령을 사용하여 스크립트를 실행합니다.

		.\mapreducejob.ps1

3. 작업이 완료되면 다음과 유사한 출력이 나타납니다.

		Cluster         : CLUSTERNAME
		ExitCode        : 0
		Name            : wordcount
		PercentComplete : map 100% reduce 100%
		Query           :
		State           : Completed
		StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
		SubmissionTime  : 12/5/2014 8:34:09 PM
		JobId           : job_1415949758166_0071

	이 출력은 작업이 성공적으로 완료되었음을 나타냅니다.

	> [AZURE.NOTE]**ExitCode**가 0이 아닌 값이면 [문제 해결](#troubleshooting)을 참조하세요.

##<a id="results"></a>작업 출력 보기

MapReduce 작업은 작업 인수로 지정한 ****wasb:///example/data/WordCountOutput** 경로의 Azure Blob 저장소에 작업 결과를 저장합니다. Azure Blob 저장소는 Azure PowerShell을 통해 액세스할 수 있지만 저장소 계정 이름, 키 및 파일에 직접 액세스하기 위해 HDInsight 클러스터에서 사용되는 컨테이너를 알고 있어야 합니다.

다행스럽게도 다음 Azure PowerShell cmdlet을 사용하여 이 정보를 얻을 수 있습니다.

* **Get-AzureHDInsightCluster**: HDInsight 클러스터와 관련된 모든 저장소 계정을 포함하여 HDInsight 클러스터에 대한 정보를 반환합니다. 클러스터와 관련된 기본 저장소 계정은 항상 하나입니다.
* **New-AzureStorageContext**: **Get-AzureHDInsightCluster**를 사용하여 찾은 저장소 계정 이름 및 키를 지정하면 저장소 계정에 액세스하는데 사용할 수 있는 컨텍스트 개체를 반환합니다.
* **Get-AzureStorageBlob**: 컨텍스트 개체 및 컨테이너 이름을 지정하면 컨테이너 내의 Blob 목록을 반환합니다.
* **Get-AzureStorageBlobContent**: 컨텍스트 개체, 파일 경로 및 이름, 컨테이너 이름(**Get-AzureHDinsightCluster**에서 반환된 값)을 지정하면 Azure Blob 저장소에서 파일을 다운로드합니다.

다음 예에서는 저장소 정보를 검색한 다음 ****wasb:///example/data/WordCountOutput**에서 출력을 다운로드합니다. **CLUSTERNAME**을 HDInsight 클러스터의 이름으로 바꿉니다.

		#Login to your Azure subscription
		# Is there an active Azure subscription?
		$sub = Get-AzureSubscription -ErrorAction SilentlyContinue
		if(-not($sub))
		{
		    Add-AzureAccount
		}

		#Specify the cluster name
		$clusterName = "CLUSTERNAME"

		#Retrieve the cluster information
		$clusterInfo = Get-AzureHDInsightCluster -ClusterName $clusterName

		#Get the storage account information
		$storageAccountName = $clusterInfo.DefaultStorageAccount.StorageAccountName
		$storageAccountKey = $clusterInfo.DefaultStorageAccount.StorageAccountKey
		$storageContainer = $clusterInfo.DefaultStorageAccount.StorageContainerName

		#Create the context object
		$context = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		#Download the files from wasb:///example/data/WordCountOutput
		#Use the -blob switch to filter only blobs contained in example/data/WordCountOutput
		Get-AzureStorageBlob -Container $storageContainer -Blob example/data/WordCountOutput/* -Context $context | Get-AzureStorageBlobContent -Context $context

> [AZURE.NOTE]이 예제에서는 다운로드한 파일을 스크립트를 실행한 디렉터리의 **example/data/WordCountOutput** 폴더에 저장합니다.

MapReduce 작업의 출력은 *part-r-#####* 이름으로 파일에 저장됩니다. 작업에서 생성한 단어 및 단어 개수를 보려면 **example/data/WordCountOutput/part-r-00000** 파일을 텍스트 편집기에서 엽니다.

> [AZURE.NOTE]MapReduce 작업의 출력 파일은 변경할 수 없습니다. 따라서 이 샘플을 다시 실행하는 경우 출력 파일의 이름을 변경해야 합니다.

##<a id="troubleshooting"></a>문제 해결

작업이 완료될 때 정보가 반환되지 않은 경우, 처리하는 동안 오류가 발생했을 수 있습니다. 이 작업에 대한 오류 정보를 보려면 **mapreducejob.ps1** 파일의 끝에 다음 명령을 추가하고 파일을 저장한 다음 다시 실행합니다.

	# Print the output of the WordCount job.
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

이 명령은 작업을 실행할 때 서버의 STDERR에 기록된 정보를 반환하며 이 정보는 작업이 실패한 이유를 확인하는 데 도움이 될 수 있습니다.

##<a id="summary"></a>요약

여기에서 볼 수 있듯이 Azure PowerShell은 HDInsight 클러스터에서 MapReduce 작업 상태를 모니터링하고, 출력을 검색하는 쉬운 방법을 제공합니다.

##<a id="nextsteps"></a>다음 단계

HDInsight의 MapReduce 작업에 대한 일반적인 정보:

* [HDInsight Hadoop에서 MapReduce 사용](hdinsight-use-mapreduce.md)

HDInsight에서 Hadoop으로 작업하는 다른 방법에 관한 정보:

* [HDInsight에서 Hadoop과 Hive 사용](hdinsight-use-hive.md)

* [HDInsight에서 Hadoop과 Pig 사용](hdinsight-use-pig.md)

<!---HONumber=Oct15_HO3-->