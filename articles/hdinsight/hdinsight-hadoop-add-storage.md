---
title: HDInsight에 Azure 저장소 계정 추가
description: 기존 HDInsight 클러스터에 Azure 저장소 계정을 추가하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206710"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>HDInsight에 추가 스토리지 계정 추가

스크립트 작업을 사용하여 HDInsight에 Azure 저장소 *계정을* 추가하는 방법을 알아봅니다. 이 문서의 단계는 기존 HDInsight 클러스터에 저장소 *계정을* 추가합니다. 이 문서는 기본 클러스터 저장소 계정이 아닌 저장소 *계정에* 적용되며 [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) 및 Azure Data Lake Storage [Gen2와](hdinsight-hadoop-use-data-lake-storage-gen2.md)같은 추가 저장소에는 적용되지 않습니다.

> [!IMPORTANT]  
> 이 문서의 정보는 저장소 계정을 만든 후 클러스터에 추가 저장소 계정을 추가하는 것입니다. 클러스터를 만드는 동안 스토리지 계정을 추가하는 방법에 대한 자세한 내용은 [Apache Hadoop, Apache Spark, Apache Kafka 등으로 HDInsight에서 클러스터 설정](hdinsight-hadoop-provision-linux-clusters.md)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

* HDInsight의 하두프 클러스터. [리눅스에서 HDInsight로 시작하기를](./hadoop/apache-hadoop-linux-tutorial-get-started.md)참조하십시오.
* 저장소 계정 이름 및 키입니다. [저장소 계정 액세스 키 관리를](../storage/common/storage-account-keys-manage.md)참조하십시오.
* PowerShell을 사용하는 경우 AZ 모듈이 필요합니다.  [Azure PowerShell 의 개요를](https://docs.microsoft.com/powershell/azure/overview)참조하십시오.

## <a name="how-it-works"></a>작동 방법

처리 중에 스크립트는 다음 작업을 수행합니다.

* 클러스터의 core-site.xml 구성에 스토리지 계정이 이미 있는 경우 스크립트가 종료되고 더 이상의 추가 동작이 수행되지 않습니다.

* 스토리지 계정이 있고 키를 사용하여 액세스할 수 있는지 확인합니다.

* 클러스터 자격 증명을 사용하여 키를 암호화합니다.

* core-site.xml 파일에 스토리지 계정을 추가합니다.

* [Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html) 및 [Apache Hadoop HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) 서비스를 중지하고 다시 시작합니다. 이러한 서비스를 중지하고 시작하면 서비스에서 새 스토리지 계정을 사용할 수 있습니다.

> [!WARNING]  
> HDInsight 클러스터와 다른 위치에서는 스토리지 계정을 사용할 수 없습니다.

## <a name="add-storage-account"></a>스토리지 계정 추가

[스크립트 작업을](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) 사용하여 다음 사항을 고려하여 변경 내용을 적용합니다.

|속성 | 값 |
|---|---|
|Bash 스크립트 URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|노드 유형|Head|
|매개 변수|`ACCOUNTNAME``ACCOUNTKEY` `-p` 사항)|

* `ACCOUNTNAME`은 HDInsight 클러스터에 추가할 저장소 계정의 이름입니다.
* `ACCOUNTKEY`은 에 대한 `ACCOUNTNAME`액세스 키입니다.
* `-p`는 선택 사항입니다. 지정된 경우 키는 암호화되지 않으며 core-site.xml 파일에 일반 텍스트로 저장됩니다.

## <a name="verification"></a>확인

Azure 포털에서 HDInsight 클러스터를 볼 때 __속성__ 아래의 __저장소 계정__ 항목을 선택하면 이 스크립트 작업을 통해 추가된 저장소 계정이 표시되지 않습니다. Azure PowerShell 및 Azure CLI는 추가 저장소 계정도 표시되지 않습니다. 스크립트가 클러스터의 `core-site.xml` 구성만 수정하기 때문에 저장소 정보가 표시되지 않습니다. 이 정보는 Azure 관리 API를 사용하여 클러스터 정보를 검색할 때 사용되지 않습니다.

추가 저장소를 확인하려면 아래 와 같은 방법 중 하나를 사용하십시오.

### <a name="powershell"></a>PowerShell

스크립트는 지정된 클러스터와 연결된 저장소 계정 이름을 반환합니다. 실제 `CLUSTERNAME` 클러스터 이름으로 바꾼 다음 스크립트를 실행합니다.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net`의 `CLUSTERNAME` 이름으로 이동합니다.

1. **HDFS** > **구성** > **고급** > **사용자 정의 코어 사이트로**이동합니다.

1. 로 시작하는 키를 `fs.azure.account.key`관찰합니다. 계정 이름은 이 샘플 이미지에 보이는 키의 일부입니다.

   ![아파치 암바리를 통한 검증](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>스토리지 계정 제거

1. 웹 브라우저에서 클러스터 `https://CLUSTERNAME.azurehdinsight.net`의 `CLUSTERNAME` 이름으로 이동합니다.

1. **HDFS** > **구성** > **고급** > **사용자 정의 코어 사이트로**이동합니다.

1. 다음 키를 제거합니다.
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

이러한 키를 제거하고 구성을 저장한 후 Oozie, 원사, MapReduce2, HDFS 및 하이브를 하나씩 다시 시작해야 합니다.

## <a name="known-issues"></a>알려진 문제

### <a name="storage-firewall"></a>스토리지 방화벽

**선택한 네트워크의**방화벽 및 가상 **네트워크** 제한으로 저장소 계정을 보호하도록 선택한 경우 HDInsight가 저장소 계정에 액세스할 수 있도록 신뢰할 수 있는 Microsoft 서비스 **허용 예외를** 사용하도록 설정해야 합니다.

### <a name="unable-to-access-storage-after-changing-key"></a>키를 변경한 후 스토리지에 액세스할 수 없음

스토리지 계정의 키를 변경하면 HDInsight에서 더 이상 스토리지 계정에 액세스할 수 없습니다. HDInsight에서는 클러스터에 대한 core-site.xml에서 키의 캐시된 복사본을 사용합니다. 이 캐시된 복사본은 새 키와 일치하도록 업데이트해야 합니다.

스크립트 동작을 다시 실행하면 스크립트에서 스토리지 계정에 대한 항목이 이미 있는지 확인하기 때문에 키를 업데이트하지 __않습니다__. 항목이 이미 있는 경우 변경하지 않습니다.

이 문제를 해결하려면 다음을 수행하십시오.  
1. 저장소 계정을 제거합니다.
1. 저장소 계정을 추가합니다.

> [!IMPORTANT]  
> 클러스터에 연결된 기본 저장소 계정에 대한 저장소 키를 회전하는 것은 지원되지 않습니다.

### <a name="poor-performance"></a>성능 저하

스토리지 계정이 HDInsight 클러스터와 다른 하위 지역에 있는 경우 성능이 저하될 수 있습니다. 다른 하위 지역의 데이터에 액세스하면 하위 지역 Azure 데이터 센터 외부와 공용 인터넷을 통해 네트워크 트래픽이 전송되어 대기 시간이 발생할 수 있습니다.

### <a name="additional-charges"></a>추가 요금

스토리지 계정이 HDInsight 클러스터와 다른 하위 지역에 있는 경우 Azure 청구에서 추가 송신 요금이 발생할 수 있습니다. 데이터가 지역 데이터 센터를 떠날 때 송신 요금이 부과됩니다. 트래픽이 다른 하위 지역의 또 다른 Azure 데이터 센터로 전송되는 경우에도 이 요금이 적용됩니다.

## <a name="next-steps"></a>다음 단계

기존 HDInsight 클러스터에 저장소 계정을 추가하는 방법을 배웠습니다. 스크립트 동작에 대한 자세한 내용은 [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)를 참조하세요.
