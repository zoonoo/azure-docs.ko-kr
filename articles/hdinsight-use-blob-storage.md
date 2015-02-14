<properties 
	pageTitle="Hadoop 호환 Blob 저장소에서 빅 데이터 쿼리 | Azure" 
	description="HDInsight에서는 Hadoop 호환 Blob 저장소를 HDFS용 빅 데이터 저장소로 사용합니다. Blob 저장소에서 쿼리하고 분석을 위해 결과를 저장하는 방법에 대해 알아봅니다." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# HDInsight에서 분석을 위해 Hadoop 호환 Blob 저장소에서 빅 데이터 쿼리

경제적인 Blob 저장소는 HDInsight와 매끄럽게 통합되는 강력한 범용 Hadoop 호환 Azure 저장소 솔루션입니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합을 Blob 저장소의 데이터에서 직접 작동할 수 있습니다. 이 자습서에서는 Blob 저장소의 컨테이너를 설정하고 데이터를 내부에서 처리하는 방법을 알아봅니다.

Blob 저장소에 데이터를 저장하면 계산에 사용된 HDInsight 클러스터를 사용자 데이터 손실 없이 안전하게 삭제할 수 있습니다. 

> [AZURE.NOTE]	 *asv://* 구문은 HDInsight 클러스터 버전 3.0은 물론 이후 버전에서도 지원되지 않습니다. 즉, 정확히 "asv://" 구문을 명시적으로 사용하며 HDInsight 클러스터 버전 3.0에 제출하는 작업은 실패하게 됩니다. 대신 *wasb://* 구문을 사용해야 합니다. 또한 asv:// 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 메타스토어로 만들어 HDInsight 클러스터 버전 3.0에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 wasb://를 사용하여 이러한 메타스토어를 다시 만들어야 합니다.

> HDInsight는 현재 블록 Blob만 지원합니다.

> <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> 등과 같은 대부분의 HDFS 명령은 여전히 예상대로 작동합니다. <b>fschk</b> 및 <b>dfsadmin</b> 등 기본 HDFS 구현(DFS라고 함)과 관련된 명령만 Azure Blob 저장소에서 다르게 동작합니다.

HDInsight 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 시작][hdinsight-get-started] 또는 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요.

## 이 문서의 내용

* [HDInsight 저장소 아키텍처](#architecture)
* [Azure Blob 저장소의 이점](#benefits)
* [Blob 저장소용 컨테이너 준비](#preparingblobstorage)
* [Blob 저장소에서 파일 주소 지정](#addressing)
* [PowerShell을 사용하여 Blob에 액세스](#powershell)
* [다음 단계](#nextsteps)

## <a id="architecture"></a>HDInsight 저장소 아키텍처
다음 다이어그램은 HDInsight 저장소 아키텍처의 추상 보기를 제공합니다.

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 정규화된 URI를 사용하여 이 파일 시스템에 액세스할 수 있습니다. 예를 들면 다음과 같습니다. 

	hdfs://<namenodehost>/<path>

또한 HDInsight는 Blob 저장소에 저장된 데이터에 대한 액세스 기능을 제공합니다. Blob 저장소에 액세스하기 위한 구문은 다음과 같습니다.

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미하며, 상대 경로를 확인하는 데 사용할 수도 있습니다. HDInsight 프로비전 프로세스 중에 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 기본 파일 시스템으로 지정됩니다.

프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][hdinsight-provision]을 참조하세요. 

- **클러스터에 연결된 저장소 계정의  컨테이너:** 계정 이름과 키는 *core-site.xml*에 저장되어 있으므로 사용자는 이러한 컨테이너의 Blob에 대한 모든 권한을 보유합니다.
- **클러스터에 연결되지 않은 저장소 계정의 공용 컨테이너 또는 공용 Blob:** 컨테이너의 Blob에 대한 읽기 전용 권한을 가집니다.

	> [AZURE.NOTE]
        > 공용 컨테이너를 사용하면 해당 컨테이너에서 사용할 수 있는 모든 Blob 목록 및 컨테이너 메타데이터를 가져올 수 있습니다. 공용 Blob을 사용하면  정확한 URL을 아는 경우에만 Blob에 액세스할 수 있습니다. 자세한 내용은 다음을 참조하세요. <a href="http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179354.aspx">컨테이너 및 Blob에 대한 액세스 제한</a>.

- **클러스터에 연결되지 않은 저장소 계정의 개인 컨테이너:** WebHCat 작업을 제출할 때 저장소 계정을 정의하지 않는 경우 컨테이너의 Blob에 액세스할 수 없습니다. 이 문서 뒷부분에서 자세히 설명합니다.


프로비전 프로세스에 정의된 저장소 계정과 해당 키는 %HADOOP_HOME%/conf/core-site.xml에 저장됩니다.  HDInsight는 기본적으로 core-site.xml 파일에 정의된 저장소 계정을 사용합니다. 클러스터 헤드 노드(마스터)는 언제든지 재이미징(re-imaging)되거나 마이그레이션될 수 있으며 해당 파일의 변경 내용은 손실되므로 core-site.xml 파일은 편집하지 않는 것이 좋습니다.

Hive, MapReduce, Hadoop 스트리밍 및 Pig를 비롯한 여러 WebHCat 작업은 저장소 계정 및 메타데이터 설명을 포함할 수 있습니다(현재 메타데이터가 아닌 저장소 계정이 있는 Pig에 작동). 이 문서의 [PowerShell을 사용하여 Blob에 액세스](#powershell) 섹션에 이 기능의 샘플이 있습니다. 자세한 내용은 [대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)을 참조하세요.

Blob 저장소 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 "/" 문자를 사용할 수 있습니다. 예를 들어 Blob의 키 이름을 *input/log1.txt*로 정할 수 있습니다. 실제로 *input* 디렉터리는 없지만 키 이름에 "/" 문자가 있으므로 파일 경로처럼 보입니다.










## <a id="benefits"></a>Azure Blob 저장소의 이점
계산 및 저장소 공동 배치에 따른 암시적 성능 비용은 계산 클러스터를 Azure 데이터 센터 내에서 저장소 계정 리소스 근처에 프로비전하는 방식으로 완화됩니다. 고속 네트워크 환경에서는 컴퓨터 노드가 Blob 저장소 내 데이터에 매우 효율적으로 액세스할 수 있습니다.

HDFS 대신 Blob 저장소에 데이터를 저장할 경우 몇 가지 이점이 있습니다.

* **데이터 다시 사용 및 공유:** HDFS의 데이터는 계산 클러스터 내에 있습니다. 계산 클러스터에 액세스할 수 있는 응용 프로그램만 HDFS API를 사용하여 데이터를 사용할 수 있습니다. Blob 저장소에 있는 데이터는 HDFS API나 [Blob 저장소 REST API][blob-storage-restAPI]를 통해 액세스할 수 있습니다. 따라서, 대규모 응용 프로그램 집합(기타 HDInsight 클러스터 포함) 및 도구를 사용하여 데이터를 생산하고 소비할 수 있습니다.
* **데이터 보관:** Blob 저장소에 데이터를 저장하면 계산에 사용된 HDInsight 클러스터를 사용자 데이터 손실 없이 안전하게 삭제할 수 있습니다. 
* **데이터 저장소 비용:** DFS에 데이터를 장기적으로 저장하면 Blob 저장소에 데이터를 저장하는 것보다 더 많은 비용이 듭니다. 계산 클러스터 비용이 Blob 저장소 비용보다 높기 때문입니다. 또한, 계산 클러스터를 생성할 때마다 데이터를 다시 로드할 필요가 없기 때문에 데이터 로드 비용도 절감됩니다.
* **탄력적인 규모 확장:** HDFS는 규모 확장 파일 시스템을 제공하지만, 그 크기는 클러스터에 대해 프로비전하는 노드 수로 결정됩니다. 크기를 변경하는 것은 자동으로 이루어지는 Blob 저장소의 탄력적인 규모 확장 기능을 이용하는 것보다 더 복잡한 프로세스가 될 수 있습니다.
* **지역에서 복제:** Blob 저장소 컨테이너는 Azure 포털을 통해 지역에서 복제할 수 있습니다. 이 경우 지리적 복구와 데이터 중복 기능이 제공되지만, 지역에서 복제된 위치에 대한 장애 조치(Failover)는 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 따라서 지역에서 복제 기능은 추가 비용을 감수할 만큼 가치 있는 데이터에 한해서만 현명하게 사용하는 것이 좋습니다.

특정 MapReduce 작업과 패키지는 Blob 저장소 컨테이너에 전혀 저장하고 싶지 않을 만한 중간 결과를 생성할 수 있습니다. 그런 경우에는 여전히 로컬 HDFS에 데이터를 저장하도록 선택할 수 있습니다. 실제로 HDInsight는 Hive 작업 및 기타 프로세스에서 생성되는 이러한 중간 결과 중 일부에 DFS를 사용합니다. 





## <a id="preparingblobstorage"></a>Blob 저장소용 컨테이너 준비
Blob을 사용하려면 먼저 [Azure 저장소 계정][azure-storage-create]을 만들어야 합니다. 이 작업의 일부로, 이 계정을 사용하여 만드는 개체를 저장할 Azure 데이터 센터를 지정합니다. 클러스터와 저장소 계정 모두 동일한 데이터 센터에 호스트해야 합니다(Hive 메타스토어 SQL 데이터베이스와 Oozie 메타스토어 SQL 데이터베이스 모두 동일한 데이터 센터에 두어야 합니다). 어디에 있든, 만들어진 각 Blob은 저장소 계정의 일부 컨테이너에 속합니다. 이 컨테이너는 HDInsight 외부에 생성된 기존 Blob 저장소 컨테이너일 수도 있고 HDInsight 클러스터용으로 생성된 컨테이너일 수도 있습니다. 



### 관리 포털을 사용하여 HDInsight용 Blob 컨테이너 만들기

Azure 관리 포털에서 HDInsight 클러스터를 프로비전할 경우  *quick create* 및 *custom create* 등 두 가지 옵션이 있습니다. 빠른 생성 옵션을 사용하려면 Azure 저장소 계정을 미리 만들어두어야 합니다.  자세한 내용은 [저장소 계정을 만드는 방법][azure-storage-create]을 참조하세요. 

빠른 생성 옵션을 사용하는 경우 기존 저장소 계정을 선택할 수 있습니다. 프로비전 프로세스에서 HDInsight 클러스터 이름과 동일한 이름의 새 컨테이너가 생성됩니다. 같은 이름의 컨테이너가 이미 있으면 <clusterName>-<x>가 사용됩니다. 예를 들면 myHDIcluster-1과 같습니다. 이 컨테이너는 기본 파일 시스템으로 사용됩니다.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
사용자 지정 만들기를 사용할 경우 기본 저장소 계정에 대해 다음 옵션 중 하나를 사용할 수 있습니다.

- 기존 저장소 사용
- 새 저장소 만들기
- 다른 구독의 저장소 사용

자체 Blob 컨테이너를 만들거나 기존 컨테이너를 사용할 수 있는 옵션도 제공됩니다.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Azure PowerShell을 사용하여 컨테이너 만들기
[Azure PowerShell][powershell-install]을 사용하여 Blob 컨테이너를 만들 수 있습니다. 다음은 샘플 PowerShell 스크립트입니다.

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>Blob 저장소에서 파일 주소 지정

Blob 저장소의 파일에 액세스하기 위한 URI 체계는 다음과 같습니다. 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] 저장소 에뮬레이터(HDInsight 에뮬레이터에서 실행됨)에서 파일의 주소를 지정하기 위한 구문은 <i>wasb://&lt;ContainerName&gt;@storageemulator</i>입니다.



URI 체계는 *wasb:* 접두사가 있는 암호화되지 않은 액세스와 *wasbs*가 있는 SSL로 암호화된 액세스를 제공합니다. 동일한 Azure 데이터 센터에 있는 데이터에 액세스하는 경우에도 가능하면 *wasbs*를 사용하는 것이 좋습니다.
	
&lt;BlobStorageContainerName&gt;은 Blob 저장소 컨테이너의 이름을 식별합니다.
&lt;StorageAccountName&gt;은 Azure 저장소 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.
	
&lt;BlobStorageContainerName&gt;과 &lt;StorageAccountName&gt;이 둘 다 지정되지 않은 경우 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용할 수 있습니다. 예를 들어, HDInsight 클러스터와 함께 제공되는 hadoop-mapreduce-examples.jar 파일을 가리킬 때 다음 중 하나를 사용할 수 있습니다.

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] HDInsight 클러스터 버전 1.6 및 2.1에서 파일 이름은 <i>hadoop-examples.jar</i> 입니다.


&lt;path&gt;는 파일 또는 디렉터리 HDFS 경로 이름입니다. Blob 저장소 컨테이너는 단지 키-값 저장소이므로 실제 계층적 파일 시스템이 없습니다. Blob 키 내부의 "/"는 디렉터리 구분 기호로 해석됩니다. 예를 들어 *hadoop-mapreduce-examples.jar*의 Blob 이름은 다음과 같습니다.

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>Azure PowerShell을 사용하여 Blob에 액세스

워크스테이션에서 Azure PowerShell을 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][powershell-install]을 참조하세요. Azure PowerShell 콘솔 창 또는 PowerShell_ISE를 사용하여 PowerShell cmdlet을 실행할 수 있습니다. 

다음 명령을 사용하여 Blob 관련 cmdlet을 나열합니다.

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**파일 업로드를 위한 PowerShell 샘플**

[HDInsight에 데이터 업로드][hdinsight-upload-data]를 참조하세요.

**파일 다운로드를 위한 PowerShell 샘플**

다음 스크립트를 실행하면 블록 Blob이 현재 폴더로 다운로드됩니다. 스크립트를 실행하기 전에 디렉터리를 쓰기 권한이 있는 폴더로 변경하세요. 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**파일 삭제를 위한 PowerShell 샘플**

다음 스크립트는 파일 삭제 방법을 보여 줍니다.
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**폴더의 파일을 나열하기 위한 PowerShell 샘플**

다음 스크립트는 "폴더" 내의 파일을 나열하는 방법을 보여 줍니다. 다음 샘플은 Invoke-Hive cmdlet을 사용하여 폴더를 나열하는 dfs ls 명령 실행 방법을 보여 줍니다.

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**정의되지 않은 저장소 계정 액세스를 위한 PowerShell 샘플**
	
이 샘플은 프로비전 프로세스 중에 정의되지 않은 저장소 계정에서 폴더를 나열하는 방법을 보여 줍니다.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>다음 단계

이 문서에서는 HDInsight와 함께 Blob 저장소를 사용하는 방법에 대해 알아보았고 Blob 저장소가 HDInsight의 기본 구성 요소라는 점을 배웠습니다. 이제 Azure Blob 저장소를 통해 장기적이고 확장 가능한 보관 데이터 취득 솔루션을 구축할 수 있으며, 저장된 데이터 내부의 정보를 활용하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.

* [Azure HDInsight 시작][hdinsight-get-started]
* [HDInsight에 데이터 업로드][hdinsight-upload-data]
* [HDInsight에서 Hive 사용][hdinsight-use-hive]
* [HDInsight에서 Pig 사용][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  
<!--HONumber=42-->
