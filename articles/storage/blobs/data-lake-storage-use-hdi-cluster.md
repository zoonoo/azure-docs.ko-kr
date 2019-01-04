---
title: Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 미리 보기 사용
description: Azure Data Lake Storage Gen2 미리 보기의 데이터를 쿼리하고 분석 결과를 저장하는 방법을 알아봅니다.
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: b9f7a1144be21b425ff0bed9e2e6cb47315c13a2
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/06/2018
ms.locfileid: "52976586"
---
# <a name="use-azure-data-lake-storage-gen2-preview-with-azure-hdinsight-clusters"></a>Azure HDInsight 클러스터에 Azure Data Lake Storage Gen2 미리 보기 사용

HDInsight 클러스터의 데이터를 분석하기 위해 Azure Blob Storage, Azure Data Lake Storage Gen2 미리 보기가 활성화된 Azure Blob Storage 또는 Azure Data Lake Storage Gen1의 조합에 데이터를 저장할 수 있습니다. 모든 저장소 옵션을 사용하면 사용자 데이터 손실 없이 계산에 사용된 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

Hadoop은 기본 파일 시스템의 개념을 지원합니다. 기본 파일 시스템은 기본 체계와 권한을 의미합니다. 상대 경로를 확인하기 위해 사용할 수 있습니다. HDInsight 클러스터 만들기 프로세스 중에 Azure Storage에 Blob 컨테이너를 지정하거나 Data Lake Storage Gen2에서 기본 파일 시스템으로 제공하는 계층 구조 네임스페이스를 지정할 수 있습니다. 또는 HDInsight 3.5를 사용하는 경우 몇 가지 예외를 제외하고 컨테이너 또는 계층 구조 네임스페이스를 기본 파일 시스템으로 선택할 수 있습니다.

이 문서에서는 Data Lake Storage Gen2가 HDInsight 클러스터에서 작동하는 방식에 대해 알아봅니다. HDInsight 클러스터를 만드는 방법에 대한 자세한 내용은 [Hadoop, Spark, Kafka 등으로 Azure Data Lake Storage를 사용하여 HDInsight 클러스터 설정](data-lake-storage-quickstart-create-connect-hdi-cluster.md)을 참조하세요.

Azure Storage는 HDInsight와 매끄럽게 통합되는 강력한 범용 저장소 솔루션입니다. HDInsight는 Azure Data Lake Storage를 클러스터의 기본 파일 시스템으로 사용할 수 있습니다. HDFS(Hadoop Distributed File System) 인터페이스를 통해 HDInsight의 전체 구성 요소 집합이 Azure Data Lake Storage의 파일에서 직접 작동할 수 있습니다.

기본 파일 시스템은 비즈니스 데이터를 저장하는 데 사용하지 않는 것이 좋습니다. 저장소 비용을 줄이기 위해 사용한 후에는 매번 기본 파일 시스템을 삭제하는 것이 좋습니다. 기본 컨테이너에는 응용 프로그램 및 시스템 로그가 포함되어 있습니다. 컨테이너를 삭제하기 전에 이러한 로그를 검색해야 합니다.

여러 클러스터에 대해 하나의 파일 시스템을 공유하는 것은 지원되지 않습니다.

## <a name="hdinsight-storage-architecture"></a>HDInsight 저장소 아키텍처

다음 다이어그램은 Azure Storage 사용의 HDInsight 저장소 아키텍처의 추상 보기를 제공합니다.

![Hadoop 클러스터는 HDFS API를 사용하여 Blob Storage의 구조적 및 비구조적 데이터에 액세스하고 저장합니다.](./media/data-lake-storage-use-hdi-cluster/HDI.ABFS.Arch.png "HDInsight Storage 아키텍처")

HDInsight는 컴퓨터 노드에 로컬로 연결된 분산 파일 시스템에 대한 액세스를 제공합니다. 정규화된 URI를 사용하여 이 파일 시스템에 액세스할 수 있습니다. 예를 들면 다음과 같습니다.

    hdfs://<NAME_NODE_HOST>/<PATH>

또한 HDInsight를 통해 Azure Data Lake Storage에 저장된 데이터에 액세스할 수 있습니다. 구문은 다음과 같습니다.

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<path>

다음은 HDInsight 클러스터와 Azure Storage 계정을 사용하는 경우 고려 사항입니다.

* **클러스터에 연결된 저장소 계정의 파일**에는 생성 중에 클러스터와 연결된 계정 이름과 키가 있습니다. 이 구성을 통해 파일 시스템에 있는 파일에 대한 모든 권한이 부여됩니다.

* **클러스터에 연결되지 않은 저장소 계정에 있는 공개 파일**은 파일 시스템의 파일에 읽기 전용 권한을 노출합니다.
  
  > [!NOTE]
  > 공용 파일 시스템을 사용하면 파일 시스템에서 사용할 수 있는 모든 파일 목록을 가져와서 메타데이터에 액세스할 수 있습니다. 공용 파일 시스템을 사용하면 정확한 URL을 아는 경우에만 파일에 액세스할 수 있습니다. 자세한 내용은 [컨테이너 및 Blob에 대한 액세스 제한](https://msdn.microsoft.com/library/windowsazure/dd179354.aspx)을 참조하세요. (컨테이너 및 Blob에 대한 규칙은 파일 및 파일 시스템에 대해 동일하게 작동합니다.)
 
* **클러스터에 연결되지 않은 저장소 계정의 개인 파일 시스템**에서는 WebHCat 작업을 제출할 때 저장소 계정을 정의하지 않는 경우 파일 시스템의 파일에 액세스할 수 없습니다. 이렇게 제한하는 이유는 이 문서의 뒷부분에 설명되어 있습니다.

만들기 프로세스에서 정의된 저장소 계정과 해당 키는 클러스터 노드의 *%HADOOP_HOME%/conf/core-site.xml*에 저장됩니다. HDInsight의 기본 동작은 *core-site.xml* 파일에 정의된 저장소 계정을 사용하는 것입니다. [Ambari](../../hdinsight/hdinsight-hadoop-manage-ambari.md)를 사용하여 이 설정을 수정할 수 있습니다.

Hive, MapReduce, Hadoop 스트리밍 및 Pig를 비롯한 여러 WebHCat 작업은 저장소 계정 및 메타데이터 설명을 포함할 수 있습니다. (이 방식은 저장소 계정이 있는 Pig에는 적합하지만 메타데이터에는 적합하지 않습니다.) 자세한 내용은 [대체 Storage 계정 및 Metastore와 HDInsight 클러스터 사용](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)을 참조하세요.

## <a id="benefits"></a>Azure Storage의 이점

계산 클러스터 및 저장소 리소스의 공동 배치에 따른 암시적 성능 비용은 계산 클러스터를 Azure 지역 내의 저장소 계정 리소스 근처에 만드는 방식으로 완화됩니다. 여기서 고속 네트워크 환경은 계산 노드가 Azure Storage 내에 있는 데이터에 효율적으로 액세스하도록 합니다.

HDFS 대신 Azure Storage에 데이터를 저장할 경우 몇 가지 이점이 있습니다:

* **데이터 다시 사용 및 공유:** HDFS의 데이터는 컴퓨팅 클러스터 내에 있습니다. 계산 클러스터에 액세스할 수 있는 응용 프로그램만 HDFS API를 통해 데이터를 사용할 수 있습니다. Azure Storage의 데이터는 HDFS API 또는 [Blob Storage REST API][blob-storage-restAPI]를 통해 액세스할 수 있습니다. 따라서 데이터의 생성과 소비에 더 많은 응용 프로그램(기타 HDInsight 클러스터 포함)과 도구를 사용할 수 있습니다.

* **데이터 보관:** Azure 스토리지에 데이터를 저장하면 사용자 데이터를 손실하지 않고 계산에 사용되는 HDInsight 클러스터를 안전하게 삭제할 수 있습니다.

* **데이터 스토리지 비용:** 컴퓨팅 클러스터의 비용이 Azure 스토리지의 비용보다 높으므로 데이터를 네이티브 HDFS에 오랫동안 저장하는 경우 Azure 스토리지에 저장하는 것보다 비용이 많이 듭니다. 또한 계산 클러스터를 생성할 때마다 데이터를 다시 로드할 필요가 없기 때문에 데이터 로드 비용도 절약됩니다.

* **탄력적인 규모 확장:** HDFS는 확장된 파일 시스템을 제공하지만, 크기 조정은 클러스터에 대해 만드는 노드의 수에 따라 결정됩니다. 규모를 변경하는 것이 Azure Storage에서 자동으로 수행되는 탄력적인 확장 기능에 의존하는 것보다 더 복잡한 프로세스가 될 수 있습니다.

* **지역 복제:** Azure 스토리지 데이터는 지역에서 복제할 수 있습니다. 이 기능을 사용하면 지리적 복구와 데이터 중복 기능이 제공되지만, 지역에서 복제된 위치로의 장애 조치(Failover)를 지원하면 성능에 심각한 영향을 미치게 되며 추가 비용을 발생시킬 수 있습니다. 따라서 지역에서 복제 기능은 신중하게 선택해야 하며, 추가 비용을 감수할 만큼 가치 있는 데이터에만 사용하는 것이 좋습니다.

* **데이터 수명 주기 관리:** 모든 파일 시스템의 모든 데이터는 자체 수명 주기를 거칩니다. 처음에는 데이터가 매우 가치 있고 자주 액세스되었다가 덜 중요해지고 덜 자주 액세스되며, 최종적으로는 보관이나 삭제가 필요해집니다. Azure Storage에는 수명 주기 단계에 적합하게 데이터를 계층화하는 데이터 계층화 및 수명 주기 관리 정책이 제공됩니다.

특정 MapReduce 작업과 패키지는 Azure Storage에 전혀 저장하고 싶지 않을 만한 중간 결과를 생성할 수 있습니다. 그런 경우에는 로컬 HDFS에 데이터를 저장하도록 선택할 수 있습니다. 실제로 HDInsight는 Hive 작업 및 기타 프로세스에서 생성되는 이러한 중간 결과 중 일부에 네이티브 HDFS 구현(DFS라고도 함)을 사용합니다.

> [!NOTE]
> 대부분의 HDFS 명령(예: `ls`, `copyFromLocal` 및 `mkdir`)은 여전히 예상대로 작동합니다. `fschk` 및 `dfsadmin`과 같은 DFS에 해당하는 명령만 Azure Storage에서 다르게 동작합니다.

## <a name="create-a-data-lake-storage-file-system"></a>Data Lake Storage 파일 시스템 만들기

파일 시스템을 사용하려면 먼저 [Azure Storage 계정][azure-storage-create]을 만듭니다. 이 프로세스의 일부로 저장소 계정이 만들어지는 Azure 지역을 지정합니다. 클러스터와 저장소 계정은 동일한 지역에서 호스트되어야 합니다. Hive Metastore SQL Server 데이터베이스 및 Oozie Metastore SQL Server 데이터베이스도 동일한 지역에 위치해야 합니다.

어디에 있든지 간에 만든 각 Blob은 스토리지 계정의 파일 시스템에 속합니다.

기본 Data Lake Storage Gen2 파일 시스템은 작업 기록 및 로그와 같은 클러스터 특정 정보를 저장합니다. 기본 Data Lake Storage Gen2 파일 시스템을 여러 HDInsight 클러스터와 공유하지 마세요. 이 경우 작업 기록이 손상될 수 있습니다. 각 클러스터에 다른 파일 시스템을 사용하고 기본 저장소 계정 대신 모든 관련 클러스터 배포에 지정된 연결 저장소 계정에서 공유 데이터를 배치하는 것이 좋습니다. 연결된 저장소 계정에 대한 자세한 내용은 [HDInsight 클러스터 만들기][hdinsight-creation]를 참조하세요. 그러나 원래 HDInsight 클러스터를 삭제한 후에 기본 저장소 파일 시스템을 다시 사용할 수 있습니다. HBase 클러스터의 경우 HBase 클러스터에서 사용되는 기본 Blob 컨테이너를 사용하여 새 HBase 클러스터를 만듦으로써 HBase 테이블 스키마 및 데이터를 보존할 수 있습니다.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="use-the-azure-portal"></a>Azure Portal 사용

포털에서 HDInsight 클러스터를 만드는 경우 저장소 계정 세부 정보를 제공할 수 있는 옵션(아래 스크린샷 참조)이 있습니다. 추가 저장소 계정을 클러스터와 연결할지 여부를 지정할 수도 있으며, 연결하려는 경우, 추가 저장소에 사용할 수 있는 저장소 옵션 중 하나를 선택합니다.

![HDInsight Hadoop 만들기 데이터 원본](./media/data-lake-storage-use-hdi-cluster/create-storage-account.png)

> [!WARNING]
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.

### <a name="use-azure-powershell"></a>Azure PowerShell 사용

[Azure PowerShell을 설치하고 구성][powershell-install]한 경우 Azure PowerShell 프롬프트에서 다음 코드를 사용하여 저장소 계정 및 컨테이너를 만들 수 있습니다.

[!INCLUDE [upgrade-powershell](../../../includes/hdinsight-use-latest-powershell.md)]

    $SubscriptionID = "<Your Azure Subscription ID>"
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "WEST US 2"

    $StorageAccountName = "<New Azure Storage Account Name>"
    $containerName = "<New Azure Blob Container Name>"

    Connect-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionId $SubscriptionID

    # Create resource group
    New-AzureRmResourceGroup -name $ResourceGroupName -Location $Location

    # Create default storage account
    New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName `
      -Name StorageAccountName `
      -Location $Location `
      -SkuName Standard_LRS `
      -Kind StorageV2 
      -HierarchialNamespace $True

    # Create default blob containers
    $storageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -StorageAccountName $StorageAccountName)[0].Value
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer -Name $containerName -Context $destContext

> [!NOTE]
> 컨테이너를 만드는 것은 Data Lake Storage Gen2에서 파일 시스템을 만드는 것과 같습니다.

### <a name="use-azure-cli"></a>Azure CLI 사용

[!INCLUDE [use-latest-version](../../../includes/hdinsight-use-latest-cli.md)]

[Azure CLI를 설치 및 구성](../../cli-install-nodejs.md)한 경우, 다음 명령을 사용하여 저장소 계정 및 컨테이너를 사용할 수 있습니다.

```bash
az storage account create \
    --name <STORAGE_ACCOUNT_NAME> \
    --resource-group <RESOURCE_GROUP_NAME> \
    --location westus2 \
    --sku Standard_LRS \
    --kind StorageV2 \
    --Enable-hierarchical-namespace true
```

> [!NOTE]
> Data Lake Storage Gen2의 공개 미리 보기 기간에는 `--sku Standard_LRS`만 지원됩니다.

저장소 계정에 생성된 지리적 지역을 지정하라는 메시지가 표시됩니다. HDInsight 클러스터를 만들려는 동일한 지역에 저장소 계정을 만듭니다.

저장소 계정을 만든 후 다음 명령을 사용하여 저장소 계정 키를 검색합니다.

    azure storage account keys list <STORAGE_ACCOUNT_NAME>

컨테이너를 만들려면 다음 명령을 사용합니다.

    azure storage container create <CONTAINER_NAME> --account-name <STORAGE_ACCOUNT_NAME> --account-key <STORAGE_ACCOUNT_KEY>

> [!NOTE]
> 컨테이너를 만드는 것은 Data Lake Storage Gen2에서 파일 시스템을 만드는 것과 같습니다.

## <a name="address-files-in-azure-storage"></a>Azure Storage에서 파일 주소 지정

HDInsight에서 Azure Storage의 파일에 액세스하기 위한 URI 구성표는 다음과 같습니다.

    abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>

URI 체계는 암호화되지 않은 액세스(*abfs:* 접두사가 있음)와 SSL로 암호화된 액세스(*abfs*가 있음)를 제공합니다. Azure의 동일한 지역에 있는 데이터에 액세스하는 경우에도 가능하면 *abfss*를 사용하는 것이 좋습니다.

* &lt;FILE_SYSTEM_NAME&gt;은 Data Lake Storage Gen2 파일 시스템의 경로를 식별합니다.
* &lt;ACCOUNT_NAME&gt;은 Azure Storage 계정 이름을 식별합니다. FQDN(정규화된 도메인 이름)이 필요합니다.

    &lt;FILE_SYSTEM_NAME&gt; 및 &lt;ACCOUNT_NAME&gt;에 대한 값이 둘 다 지정되지 않으면 기본 파일 시스템이 사용됩니다. 기본 파일 시스템의 파일에 대해서는 상대 경로나 절대 경로를 사용할 수 있습니다. 예를 들어, HDInsight 클러스터와 함께 제공되는 *hadoop-mapreduce-examples.jar* 파일을 가리킬 때 다음 경로 중 하나를 사용할 수 있습니다.
    
        abfs://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
        abfs:///example/jars/hadoop-mapreduce-examples.jar
        /example/jars/hadoop-mapreduce-examples.jar

> [!NOTE]
> HDInsight 버전 2.1 및 1.6 클러스터에서는 파일 이름이*hadoop-examples.jar*입니다.

* &lt;PATH&gt;는 파일 또는 디렉터리 HDFS 경로 이름입니다.

> [!NOTE]
> HDInsight 외부에서 파일을 사용할 때 대부분의 유틸리티는 ABFS 형식을 인식하지 않으며 대신 `example/jars/hadoop-mapreduce-examples.jar`과 같은 기본 경로 형식을 예상합니다.
 
## <a name="use-additional-storage-accounts"></a>추가 저장소 계정 사용

HDInsight 클러스터를 만드는 동안 클러스터와 연결할 Azure Storage 계정을 지정합니다. 만들기 프로세스 중이나 클러스터를 만든 후에 이 저장소 계정 외에도 동일한 Azure 구독 또는 다른 Azure 구독에서 저장소 계정을 추가할 수 있습니다. 저장소 계정 추가에 대한 지침은 [HDInsight 클러스터 만들기](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)를 참조하세요.

> [!WARNING]
> HDInsight 클러스터와 다른 위치에서는 추가 저장소 계정을 사용할 수 없습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 HDInsight로 HDFS 호환 Azure Storage를 사용하는 방법을 알아보았습니다. 이 방식을 사용하면 장기적이고 확장성 있는 보관 데이터 취득 솔루션을 구축할 수 있으며, 저장된 구조적 및 비구조적 데이터 내부의 정보를 활용하는 데 HDInsight를 사용할 수 있습니다.

자세한 내용은 다음을 참조하세요.

* [Azure Data Lake Storage Gen2용 ABFS Hadoop 파일 시스템 드라이버](data-lake-storage-abfs-driver.md)
* [Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)
* [Azure Data Lake Storage Gen2를 사용하여 Hadoop, Spark, Kafka 등이 포함된 HDInsight 클러스터 설정](data-lake-storage-quickstart-create-connect-hdi-cluster.md)
* [distcp를 사용하여 Azure Data Lake Storage Gen2에 데이터 수집](data-lake-storage-use-distcp.md)

[powershell-install]: /powershell/azureps-cmdlets-docs
[hdinsight-creation]: ../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../common/storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/data-lake-storage-use-hdi-cluster/HDI.PowerShell.BlobCommands.png
