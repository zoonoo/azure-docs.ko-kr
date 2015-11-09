<properties
	pageTitle="PowerShell을 사용하여 HDInsight의 Hadoop 클러스터 관리 | Microsoft Azure"
	description="Azure PowerShell을 사용하여 HDInsight에서 Hadoop 클러스터에 대해 관리 작업을 수행하는 방법에 대해 알아봅니다."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/23/2015"
	ms.author="jgao"/>

# Azure PowerShell을 사용하여 HDInsight의 Hadoop 클러스터 관리

[AZURE.INCLUDE [선택기](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell은 Azure에서 작업의 배포와 관리를 제어 및 자동화하기 위해 사용할 수 있는 강력한 스크립팅 환경입니다. 이 문서에서는 Windows PowerShell을 통해 로컬 Azure PowerShell 콘솔을 사용하여 Azure HDInsight의 Hadoop 클러스터를 관리하는 방법을 알아보겠습니다. HDInsight PowerShell cmdlet의 목록은 [HDInsight cmdlet 참조][hdinsight-powershell-reference]를 참조하세요.



**필수 구성 요소**

이 문서를 시작하기 전에 다음이 있어야 합니다.

- **Azure 구독**. [Azure 무료 평가판](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)을 참조하세요.
- **Azure PowerShell이 포함된 워크스테이션**. [Azure PowerShell 설치 및 사용](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/)을 참조하세요.

	> [AZURE.NOTE]이 문서에서 제공된 PowerShell 스크립트는 Azure 리소스 관리자 모드를 사용합니다. 예제를 작동하려면 Microsoft 웹 플랫폼 설치 관리자를 사용하여 최신 Azure PowerShell을 다운로드하십시오.

##HDInsight 클러스터 프로비전

HDInsight 클러스터는 Azure 리소스 그룹 및 Azure 저장소 계정의 Blob 컨테이너에 필요합니다.

- Azure 리소스 그룹은 Azure 리소스에 대한 논리적 컨테이너입니다. Azure 리소스 그룹 및 HDInsight 클러스터는 동일한 위치에 있을 필요가 없습니다. 자세한 내용은 [Azure 리소스 관리자에서 Azure PowerShell 사용](powershell-azure-resource-manager.md)을 참조하세요.
- HDInsight는 기본 파일 시스템으로 Azure 저장소 계정의 Blob 컨테이너를 사용합니다. HDInsight 클러스터를 만들려면 먼저 Azure 저장소 계정 및 저장소 컨테이너가 필요합니다. 기본 저장소 계정 및 HDInsight 클러스터는 동일한 위치에 있어야 합니다.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**Azure 리소스 그룹을 만들려면**

2. Azure 계정에 연결하고 구독을 선택합니다.(여러 구독이 있는 경우)

		Add-AzureRmAccount
		Get-AzureRmSubscription
		Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

3. 새 리소스 그룹 만들기:

	새 AzureRmResourceGroup-이름 <New Azure Resource Group Name> -위치 <Azure Data Center> # 예를 들어 "미국 서부"


**Azure 저장소 계정을 만들려면**

	New-AzureRmStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

저장소 계정 유형의 전체 목록은[https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx)을 참조하세요.

[AZURE.INCLUDE [데이터 센터 목록](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Azure 미리 보기 포털을 사용하여 Azure 저장소 계정을 만드는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](storage-create-storage-account.md)를 참조하세요.

저장소 계정이 이미 있지만 계정 이름과 계정 키를 모르는 경우 다음 명령을 사용하여 정보를 검색할 수 있습니다.

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

미리 보기 포털을 사용하여 정보를 얻는 방법에 대한 자세한 내용은 [저장소 계정 만들기, 관리 또는 삭제](storage-create-storage-account.md)의 "저장소 액세스 키 보기, 복사 및 다시 생성" 섹션을 참조하세요.

**Azure 저장소 컨테이너를 만들려면**

Azure PowerShell은 HDInsight 프로비전 중 Blob 컨테이너를 만들 수 없습니다. 다음 스크립트를 사용하여 컨테이너를 만들 수 있습니다.

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureRmStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureRmStorageContainer -Name $containerName -Context $destContext

**클러스터를 프로비전하려면**

저장소 계정 및 Blob 컨테이너가 준비되면 클러스터를 만들 준비가 되었습니다.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##클러스터 세부 정보 나열
현재 구독의 클러스터를 모두 나열하려면 다음 명령을 사용합니다.

	Get-AzureRmHDInsightCluster

현재 구독의 특정 클러스터 세부 정보를 표시하려면 다음 명령을 사용합니다.

	Get-AzureRmHDInsightCluster -ClusterName <ClusterName>

##클러스터 삭제
클러스터를 삭제하려면 다음 명령을 사용합니다.

	Remove-AzureRmHDInsightCluster -ClusterName <ClusterName>



##HTTP 서비스 액세스 권한 부여/해지

HDInsight 클러스터에는 다음과 같은 HTTP 웹 서비스가 있습니다(이러한 모든 서비스에 RESTful 끝점이 있음).

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


이러한 서비스에는 기본적으로 액세스 권한이 부여됩니다. 액세스 권한을 해지/부여할 수 있습니다. 샘플은 다음과 같습니다.

	Revoke-AzureHDInsightHttpServicesAccess -ClusterName <Cluster Name>

>[AZURE.NOTE]액세스 권한을 부여/해지하여 클러스터 사용자 이름 및 암호를 다시 설정합니다.

이 작업은 미리 보기 포털을 통해서도 수행할 수 있습니다. [Azure 미리 보기 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]를 참조하세요.

##클러스터 크기 조정
클러스터 크기 조정 기능을 사용하여 클러스터를 다시 생성하지 않고 Azure HDInsight에서 실행되는 클러스터에서 사용되는 작업자 노드 수를 변경합니다.

>[AZURE.NOTE]HDInsight 버전 3.1.3 이상을 사용하는 클러스터만 지원됩니다. 클러스터 버전을 알 수 없는 경우 속성 페이지를 확인할 수 있습니다. [클러스터 포털 인터페이스에 대해 알아보기](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface)를 참조하세요.

HDInsight에서 지원되는 클러스터의 각 형식에 대한 데이터 노드 수를 변경하는 영향은 다음과 같습니다.

- Hadoop은

	모든 보류 중인 또는 실행 중인 작업에 영향을 주지 않고 실행되는 Hadoop 클러스터의 작업자 노드 수를 원활하게 늘릴 수 있습니다. 작업이 진행 중인 동안에 새 작업을 제출할 수 있습니다. 크기 조정 작업의 오류는 정상적으로 처리되므로 클러스터는 항상 기능 상태로 남아 있습니다.

	데이터 노드 수를 줄여 Hadoop 클러스터를 축소하면 클러스터의 서비스 중 일부가 다시 시작됩니다. 그러면 실행 중인 작업과 보류 중인 작업이 크기 조정 작업을 완료하지 못하고 실패합니다. 그러나 작업이 완료되면 작업을 다시 제출할 수 있습니다.

- HBase

	HBase 클러스터가 실행 중인 동안 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 지역 서버는 크기 조정 작업을 완료하는 몇 분 안에 자동으로 균형을 맞춥니다. 그러나 클러스터의 헤드 노드에 로그인한 다음 명령 프롬프트 창에서 다음 명령을 실행하여 자동으로 지역 서버의 균형을 맞출 수도 있습니다.

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	HBase shell 사용에 대한 자세한 내용은 다음을 참조하세요.
- Storm

	실행 중인 동안 Storm 클러스터에 데이터 노드를 원활하게 추가하거나 제거할 수 있습니다. 하지만 크기 조정 작업이 성공적으로 완료되면 다시 토폴로지 균형을 조정해야 합니다.

	다음 두 가지 방법으로 사용하여 균형을 조정할 수 있습니다.

	* Storm 웹 UI
	* 명령줄 인터페이스(CLI) 도구

	자세한 내용은 [Apache Storm 설명서](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html)(영문)을 참조하세요.

	Storm 웹 UI는 HDInsight 클러스터에서 제공됩니다.

	![HDinsight Storm 규모 균형 재조정](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	다음은 CLI 명령을 사용하여 Storm 토폴로지 균형을 다시 조정하는 방법의 예입니다.

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Azure PowerShell을 사용하여 Hadoop 클러스터 크기를 변경하려면 클라이언트 컴퓨터에서 다음 명령을 실행합니다.

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>



##MapReduce 작업 제출
HDInsight 클러스터 배포는 몇 가지 MapReduce 샘플과 함께 제공됩니다. 샘플 중 하나는 소스 파일에서 단어의 빈도수를 계산하는 것입니다.

**MapReduce 작업을 제출하려면**

다음 Azure PowerShell 스크립트는 단어 수 샘플 작업을 제출합니다.

	$clusterName = "<HDInsightClusterName>"

	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
								-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
								-ClassName "wordcount" `
								-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
	
	# Submit the job and wait for job completion
	$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
	$wordCountJob = Start-AzureRmHDInsightJob `
						-ResourceGroupName $resourceGroupName `
						-ClusterName $clusterName `
						-HttpCredential $cred `
						-JobDefinition $wordCountJobDefinition 
	
	Wait-AzureRmHDInsightJob `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-HttpCredential $cred `
		-JobId $wordCountJob.JobId 

	# Get the job output
	$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
	$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$defaultStorageContainer = $cluster.DefaultStorageContainer
	
	Get-AzureRmHDInsightJobOutput `
		-ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-HttpCredential $cred `
		-DefaultStorageAccountName $defaultStorageAccount `
		-DefaultStorageAccountKey $defaultStorageAccountKey `
		-DefaultContainer $defaultStorageContainer  `
		-JobId $wordCountJob.JobId `
		-DisplayOutputType StandardError
		
**wasb** 접두사에 대한 자세한 내용은 [HDInsight에 대해 Azure Blob 저장소 사용][hdinsight-storage]을 참조하세요.

**MapReduce 작업 출력을 다운로드하려면**

다음 Azure PowerShell 스크립트는 마지막 절차의 MapReduce 작업 출력을 검색합니다.

	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

MapReduce 작업 개발 및 실행에 대한 자세한 내용은 [HDInsight와 함께 MapReduce 사용][hdinsight-use-mapreduce]을 참조하세요.






































##Hive 작업 제출
HDInsight 클러스터 배포는 *hivesampletable*이라는 샘플 Hive 테이블과 함께 제공됩니다. HiveQL **SHOW TABLES** 명령을 사용하여 클러스터의 Hive 테이블을 나열할 수 있습니다.

**Hive 작업을 제출하려면**

다음 스크립트는 Hive 테이블을 나열하는 Hive 작업을 제출합니다.

	$clusterName = "<HDInsightClusterName>"

	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

Hive 작업은 먼저 클러스터에 생성된 Hive 테이블을 표시한 후 hivesampletable 테이블에서 반환된 데이터를 표시합니다.

Hive 사용에 대한 자세한 내용은 [HDInsight와 함께 Hive 사용][hdinsight-use-hive]을 참조하세요.


##Azure Blob 저장소에 데이터 업로드
[HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.

##Azure Blob 저장소에서 작업 출력을 다운로드합니다.
이 문서의 [MapReduce 작업 제출](#mapreduce) 섹션을 참조하세요.

## 참고 항목
* [HDInsight Cmdlet 참조 설명서][hdinsight-powershell-reference]
* [Azure 미리 보기 포털을 사용하여 HDInsight 관리][hdinsight-admin-portal]
* [명령줄 인터페이스를 사용하여 HDInsight 관리][hdinsight-admin-cli]
* [HDInsight 클러스터 프로비전][hdinsight-provision]
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [프로그래밍 방식으로 Hadoop 작업 제출][hdinsight-submit-jobs]
* [Azure HDInsight 시작][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=Nov15_HO1-->