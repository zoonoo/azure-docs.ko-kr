<properties linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Blob Storage with  Hadoop in HDInsight" pageTitle="Use Blob storage with Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with Hadoop in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# HDInsight에서 Hadoop과 Azure Blob 저장소 사용

Azure HDInsight는 데이터 저장에 HDFS(Hadoop Distributed Files System)와 Azure Blob 저장소를 모두 지원합니다. Blob 저장소는 강력한 범용 Azure 저장소 솔루션입니다. 모든 기능을 갖춘 HDFS 인터페이스를 제공하는 Blob 저장소에서는 Hadoop 에코시스템의 모든 구성 요소가 데이터에 직접 작동하도록 설정하는(기본값) 능률적인 환경을 이용할 수 있습니다. Blob 저장소는 단순한 저비용 솔루션이 아닙니다. Blob 저장소에 데이터를 저장하면 계산에 사용된 HDInsight 클러스터를 사용자 데이터 손실 없이 안전하게 삭제할 수 있습니다.

> [WACOM.NOTE] *asv://* 구문은 HDInsight 클러스터 버전 3.0은 물론 이후 버전에서도 지원되지 않습니다. 즉, 정확히 "asv://" 구문을 명시적으로 사용하며 HDInsight 클러스터 버전 3.0에 제출하는 작업은 실패하게 됩니다. 대신 *wasb://* 구문을 사용해야 합니다. 또한 asv:// 구문을 사용하는 리소스에 대한 명시적 참조를 포함하는 기존의 메타스토어로 만들어 HDInsight 클러스터 버전 3.0에 제출하는 작업도 실패하게 됩니다. 리소스 주소를 지정하려면 wasb://를 사용하여 이러한 메타스토어를 다시 만들어야 합니다.

> [WACOM.NOTE] HDInsight는 현재 블록 Blob만 지원합니다.

> [WACOM.NOTE]
> **ls**, **copyFromLocal**, **mkdir** 등과 같은 대부분의 HDFS 명령은 여전히 예상대로 작동합니다. **fschk**와 **dfsadmin** 등 기본 HDFS 구현(DFS라고 함)과 관련된 명령만 Azure Blob 저장소에서 다르게 동작합니다.

HDInsight 클러스터 프로비전에 대한 자세한 내용은 [HDInsight 시작][HDInsight 시작] 또는 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

## 이 문서에서는 다음을 수행합니다.

-   [HDInsight 저장소 아키텍처][HDInsight 저장소 아키텍처]
-   [Azure Blob 저장소의 이점][Azure Blob 저장소의 이점]
-   [Blob 저장소용 컨테이너 준비][Blob 저장소용 컨테이너 준비]
-   [Blob 저장소에서 파일 주소 지정][Blob 저장소에서 파일 주소 지정]
-   [PowerShell을 사용하여 Blob에 액세스][PowerShell을 사용하여 Blob에 액세스]
-   [다음 단계][다음 단계]

## <span id="architecture"></span></a>HDInsight 저장소 아키텍처

다음 다이어그램은 HDInsight 저장소 아키텍처의 추상 보기를 제공합니다.

![HDI.ASVArch][HDI.ASVArch]

HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 정규화된 URI를 사용하여 이 파일 시스템에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

    hdfs://<namenodehost>/<path>

또한 HDInsight는 Blob 저장소에 저장된 데이터에 대한 액세스 기능을 제공합니다. Blob 저장소에 액세스하기 위한 구문은 다음과 같습니다.

    wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>

Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미하며, 상대 경로를 확인하는 데 사용할 수도 있습니다. HDInsight 프로비전 프로세스 중에 Azure 저장소 계정 및 이 계정에서 오는 특정 Blob 저장소 컨테이너가 기본 파일 시스템으로 지정됩니다.

프로비전 프로세스 중에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 프로비전][HDInsight 클러스터 프로비전]을 참조하세요.

-   **클러스터에 연결된 저장소 계정의 컨테이너:** 계정 이름과 키는 *core-site.xml*에 저장되어 있으므로 사용자는 이러한 컨테이너의 Blob에 대한 모든 권한을 보유합니다.
-   **클러스터에 연결되지 않은 저장소 계정의 공용 컨테이너 또는 공용 Blob:** 사용자는 컨테이너의 Blob에 대한 읽기 전용 권한을 보유합니다.

    > [WACOM.NOTE]
    >  \> 공용 컨테이너를 사용하면 해당 컨테이너에서 사용할 수 있는 모든 Blob 목록 및 컨테이너 메타데이터를 가져올 수 있습니다. 공용 Blob을 사용하면 정확한 URL을 아는 경우에만 Blob에 액세스할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 액세스 제한][컨테이너 및 Blob에 대한 액세스 제한]을 참조하세요.

-   **클러스터에 연결되지 않은 저장소 계정의 개인 컨테이너:** WebHCat 작업을 제출할 때 저장소 계정을 정의하지 않으면 컨테이너의 Blob에 액세스할 수 없습니다. 이 내용은 문서 뒷부분에 설명되어 있습니다.

프로비전 프로세스에 정의된 저장소 계정과 해당 키는 %HADOOP\_HOME%/conf/core-site.xml에 저장됩니다. HDInsight는 기본적으로 core-site.xml 파일에 정의된 저장소 계정을 사용합니다. 클러스터 헤드 노드(마스터)는 언제든지 재이미징(re-imaging)되거나 마이그레이션될 수 있으며 해당 파일의 변경 내용은 손실되므로 core-site.xml 파일은 편집하지 않는 것이 좋습니다.

Hive, MapReduce, Hadoop 스트리밍 및 Pig를 비롯한 여러 WebHCat 작업은 저장소 계정 및 메타데이터 설명을 포함할 수 있습니다(현재 메타데이터가 아닌 저장소 계정이 있는 Pig에 작동). 이 문서의 [PowerShell을 사용하여 Blob 액세스][PowerShell을 사용하여 Blob에 액세스] 섹션에는 이 기능의 샘플이 포함되어 있습니다. 자세한 내용은 [대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용][대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용]을 참조하세요.

Blob 저장소 컨테이너는 키/값 쌍으로 데이터를 저장하며, 디렉터리 계층 구조는 없습니다. 그러나 파일이 디렉터리 구조 내에 저장된 것처럼 보이도록 키 이름에 "/" 문자를 사용할 수 있습니다. 예를 들어 Blob의 키 이름을 *input/log1.txt*로 지정할 수 있습니다. 실제로 *input* 디렉터리는 없지만 키 이름에 "/" 문자가 있으므로 파일 경로처럼 보입니다.

## <span id="benefits"></span></a>Azure Blob 저장소의 이점

계산 및 저장소 공동 배치에 따른 암시적 성능 비용은 계산 클러스터를 Azure 데이터 센터 내에서 저장소 계정 리소스 근처에 프로비전하는 방식으로 완화됩니다. 고속 네트워크 환경에서는 컴퓨터 노드가 Blob 저장소 내 데이터에 매우 효율적으로 액세스할 수 있습니다.

HDFS 대신 Blob 저장소에 데이터를 저장할 경우 몇 가지 이점이 있습니다.

-   **데이터 다시 사용 및 공유:** HDFS의 데이터는 계산 클러스터 내에 있습니다. 계산 클러스터에 액세스할 수 있는 응용 프로그램만 HDFS API를 통해 데이터를 사용할 수 있습니다. Blob 저장소의 데이터는 HDFS API 또는 [Blob 저장소 REST API][Blob 저장소 REST API]를 통해 액세스할 수 있습니다. 따라서 데이터의 생성과 소비에 더 많은 응용 프로그램(기타 HDInsight 클러스터 포함)과 도구를 사용할 수 있습니다.
-   **데이터 보관:** Blob 저장소에 데이터를 저장하면 계산에 사용된 HDInsight 클러스터를 사용자 데이터 손실 없이 안전하게 삭제할 수 있습니다.
-   **데이터 저장소 비용:** 데이터를 장기간 저장하는 경우 DFS에 저장하는 것이 Blob 저장소에 저장하는 것보다 비용이 많이 드는데, 이는 계산 클러스터의 비용이 Blob 저장소 컨테이너의 비용보다 비싸기 때문입니다. 또한 계산 클러스터를 생성할 때마다 데이터를 다시 로드할 필요가 없기 때문에 데이터 로드 비용도 절약됩니다.
-   **탄력적인 확장:** HDFS는 확장된 파일 시스템을 제공하지만, 확장은 클러스터에 대해 프로비전하는 노드의 수에 의해 결정됩니다. 규모를 변경하는 것이 자동으로 수행되는 Blob 저장소의 탄력적인 확장 기능에 의존하는 것보다 더 복잡한 프로세스가 될 수 있습니다.
-   **지역에서 복제:** Azure 포털을 통해 Blob 저장소 컨테이너를 지역에서 복제할 수 있습니다. 이 경우 지리적 복구와 데이터 중복 기능이 제공되지만, 지역에서 복제된 위치에 대한 장애 조치(Failover)는 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 따라서 지역에서 복제 기능은 추가 비용을 감수할 만큼 가치 있는 데이터에 한해서만 현명하게 사용하는 것이 좋습니다.

특정 MapReduce 작업과 패키지는 Blob 저장소 컨테이너에 전혀 저장하고 싶지 않을 만한 중간 결과를 생성할 수 있습니다. 그런 경우에는 여전히 로컬 HDFS에 데이터를 저장하도록 선택할 수 있습니다. 실제로 HDInsight는 Hive 작업 및 기타 프로세스에서 생성되는 이러한 중간 결과 중 일부에 DFS를 사용합니다.

## <span id="preparingblobstorage"></span></a>Blob 저장소용 컨테이너 준비

Blob을 사용하려면 먼저 [Azure 저장소 계정][Azure 저장소 계정]을 만들어야 합니다. 이 작업의 일부로, 이 계정을 사용하여 만드는 개체를 저장할 Azure 데이터 센터를 지정합니다. 클러스터와 저장소 계정 모두 동일한 데이터 센터에 호스트해야 합니다(Hive 메타스토어 SQL 데이터베이스와 Oozie 메타스토어 SQL 데이터베이스 모두 동일한 데이터 센터에 두어야 합니다). 어디에 있든, 만들어진 각 Blob은 저장소 계정의 일부 컨테이너에 속합니다. 이 컨테이너는 HDInsight 외부에 생성된 기존 Blob 저장소 컨테이너일 수도 있고 HDInsight 클러스터용으로 생성된 컨테이너일 수도 있습니다.

### 관리 포털을 사용하여 HDInsight용 Blob 컨테이너 만들기

Azure 관리 포털에서 HDInsight 클러스터를 프로비전할 경우 *빠른 생성*과 *사용자 지정 만들기*의 두 가지 옵션이 있습니다. 빠른 생성 옵션을 사용하려면 Azure 저장소 계정을 미리 만들어두어야 합니다. 자세한 내용은 [저장소 계정을 만드는 방법][Azure 저장소 계정]을 참조하세요.

빠른 생성 옵션을 사용하는 경우 기존 저장소 계정을 선택할 수 있습니다. 프로비전 프로세스에서 HDInsight 클러스터 이름과 동일한 이름의 새 컨테이너가 생성됩니다. 같은 이름의 컨테이너가 이미 있으면 <clustername>-<x>이 사용됩니다. 예를 들어 myHDIcluster-1과 같이 사용할 수 있습니다. 이 컨테이너는 기본 파일 시스템으로 사용됩니다.

![HDI.QuickCreate][HDI.QuickCreate]

사용자 지정 만들기를 사용할 경우 기본 저장소 계정에 대해 다음 옵션 중 하나를 사용할 수 있습니다.

-   기존 저장소 사용
-   새 저장소 만들기
-   다른 구독의 저장소 사용

자체 Blob 컨테이너를 만들거나 기존 컨테이너를 사용할 수 있는 옵션도 제공됩니다.

![HDI.CustomCreateStorageAccount][HDI.CustomCreateStorageAccount]

### Azure PowerShell을 사용하여 컨테이너 만들기

[Azure PowerShell][Azure PowerShell]을 사용하여 Blob 컨테이너를 만들 수 있습니다. 다음은 샘플 PowerShell 스크립트입니다.

    $subscriptionName = "<SubscriptionName>"    # Azure subscription name
    $storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
    $containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

    # Connect to your Azure account and selec the current subscription
    Add-AzureAccount # The connection will expire in a few hours.
    Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions

    # Create a storage context object
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Create a Blob storage container
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <span id="addressing"></span></a>Blob 저장소에서 파일 주소 지정

Blob 저장소의 파일에 액세스하기 위한 URI 체계는 다음과 같습니다.

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] 저장소 에뮬레이터(HDInsight 에뮬레이터에서 실행됨)에서 파일의 주소를 지정하기 위한 구문은 *wasb:[//\<ContainerName\>@storageemulator][//\<ContainerName\>@storageemulator]*입니다.

URI 체계는 *wasb:* 접두사를 사용한 암호화되지 않은 액세스와 *wasbs*를 사용한 SSL 암호화된 액세스를 둘 다 제공합니다. 동일한 Azure 데이터 센터에 있는 데이터에 액세스하는 경우에도 가능하면 *wasbs*를 사용하는 것이 좋습니다.

\<BlobStorageContainerName\>은 Blob 저장소 컨테이너의 이름을 식별합니다.
\<StorageAccountName\>은 Azure 저장소 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.

\<BlobStorageContainerName\>과 \<StorageAccountName\>이 둘 다 지정되지 않은 경우 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용할 수 있습니다. 예를 들어, HDInsight 클러스터와 함께 제공되는 hadoop-mapreduce-examples.jar 파일을 가리킬 때 다음 중 하나를 사용할 수 있습니다.

    wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] HDInsight 클러스터 버전 1.6과 2.1에서는 파일 이름이 *hadoop-examples.jar*입니다.

\<path\>는 파일 또는 디렉터리 HDFS 경로 이름입니다. Blob 저장소 컨테이너는 단지 키-값 저장소이므로 실제 계층적 파일 시스템이 없습니다. Blob 키 내부의 "/"는 디렉터리 구분 기호로 해석됩니다. 예를 들어 *hadoop-mapreduce-examples.jar*의 Blob 이름은 다음과 같습니다.

    example/jars/hadoop-mapreduce-examples.jar

## <span id="powershell"></span></a>Azure PowerShell을 사용하여 Blob에 액세스

워크스테이션에서 Azure PowerShell을 설치하고 구성하는 방법에 대한 자세한 내용은 [Azure PowerShell 설치 및 구성][Azure PowerShell]을 참조하세요. Azure PowerShell 콘솔 창 또는 PowerShell\_ISE를 사용하여 PowerShell cmdlet을 실행할 수 있습니다.

다음 명령을 사용하여 Blob 관련 cmdlet을 나열합니다.

    Get-Command *blob*

![Blob.PowerShell.cmdlets][Blob.PowerShell.cmdlets]

**파일 업로드를 위한 PowerShell 샘플**

[HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]를 참조하세요.

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
 $storageAccountName = "<azurestorageaccountname>" \# The storage account used for the default file system specified at provision.
 $containerName = "<blobstoragecontainername>" \# The default file system container has the same name as the cluster.
 $blob = "example/data/sample.log" \# The name of the blob to be downloaded.

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
 $clusterName = "<hdinsightclustername>"

    $undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
    $undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

    $undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

    Use-AzureHDInsightCluster $clusterName

    $defines = @{}
    $defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

    Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

## <span id="nextsteps"></span></a>다음 단계

이 문서에서는 HDInsight와 함께 Blob 저장소를 사용하는 방법에 대해 알아보았고 Blob 저장소가 HDInsight의 기본 구성 요소라는 점을 배웠습니다. 이제 Azure Blob 저장소를 통해 장기적이고 확장 가능한 보관 데이터 취득 솔루션을 구축할 수 있으며, 저장된 데이터 내부의 정보를 활용하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음 문서를 참조하세요.

-   [Azure HDInsight 시작][HDInsight 시작]
-   [HDInsight에 데이터 업로드][HDInsight에 데이터 업로드]
-   [HDInsight에서 Hive 사용][HDInsight에서 Hive 사용]
-   [HDInsight에서 Pig 사용][HDInsight에서 Pig 사용]

  [HDInsight 시작]: ../hdinsight-get-started/
  [HDInsight 클러스터 프로비전]: ../hdinsight-provision-clusters/
  [HDInsight 저장소 아키텍처]: #architecture
  [Azure Blob 저장소의 이점]: #benefits
  [Blob 저장소용 컨테이너 준비]: #preparingblobstorage
  [Blob 저장소에서 파일 주소 지정]: #addressing
  [PowerShell을 사용하여 Blob에 액세스]: #powershell
  [다음 단계]: #nextsteps
  [HDI.ASVArch]: ./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight 저장소 아키텍처"
  [컨테이너 및 Blob에 대한 액세스 제한]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd179354.aspx
  [대체 저장소 계정 및 메타스토어와 HDInsight 클러스터 사용]: http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx
  [Blob 저장소 REST API]: http://msdn.microsoft.com/ko-kr/library/windowsazure/dd135733.aspx
  [Azure 저장소 계정]: ../storage-create-storage-account/
  [HDI.QuickCreate]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
  [HDI.CustomCreateStorageAccount]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
  [Azure PowerShell]: ../install-configure-powershell/
  [//\<ContainerName\>@storageemulator]: mailto://<ContainerName>@storageemulator
  [Blob.PowerShell.cmdlets]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
  [HDInsight에 데이터 업로드]: ../hdinsight-upload-data/
  [HDInsight에서 Hive 사용]: ../hdinsight-use-hive/
  [HDInsight에서 Pig 사용]: ../hdinsight-use-pig/
