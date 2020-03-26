---
title: '자습서: Azure CLI를 사용하여 HDInsight에서 Apache Kafka REST 프록시 사용 클러스터 만들기'
description: Azure HDInsight에서 Kafka REST 프록시를 사용하여 Apache Kafka 작업을 수행하는 방법을 알아봅니다.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: hrasheed
ms.service: hdinsight
ms.topic: tutorial
ms.date: 02/27/2020
ms.openlocfilehash: 6ba5e433839d1f27c9522749fd7a8831c7243aae
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "78201710"
---
# <a name="tutorial-create-an-apache-kafka-rest-proxy-enabled-cluster-in-hdinsight-using-azure-cli"></a>자습서: Azure CLI를 사용하여 HDInsight에서 Apache Kafka REST 프록시 사용 클러스터 만들기

이 자습서에서는 Azure CLI(명령줄 인터페이스)를 사용하여 Azure HDInsight에서 Apache Kafka [REST 프록시 사용](./rest-proxy.md) 클러스터를 만드는 방법에 대해 알아봅니다. Azure HDInsight는 엔터프라이즈를 위한 관리형의 전체 스펙트럼 오픈 소스 분석 서비스입니다. Apache Kafka는 오픈 소스 분산형 스트리밍 플랫폼입니다. 게시-구독 메시지 큐와 유사한 기능을 제공하므로 메시지 브로커로 자주 사용됩니다. Kafka REST 프록시를 사용하면 HTTP를 통해 [REST API](https://docs.microsoft.com/rest/api/hdinsight-kafka-rest-proxy/)에서 Kafka 클러스터와 상호 작용할 수 있습니다. Azure CLI는 Azure 리소스를 관리하기 위한 Microsoft의 플랫폼 간 명령줄 환경입니다.

Apache Kafka API는 동일한 가상 네트워크 내에서만 리소스에서 액세스할 수 있습니다. SSH를 사용하여 클러스터에 직접 액세스할 수 있습니다. 다른 서비스, 네트워크 또는 가상 머신을 Apache Kafka에 연결하려면 먼저 가상 네트워크를 만든 다음, 네트워크 내에 리소스를 만듭니다. 자세한 내용은 [가상 네트워크를 사용하여 Apache Kafka에 연결](./apache-kafka-connect-vpn-gateway.md)을 참조하세요.

이 자습서에서는 다음에 대해 알아봅니다.

> [!div class="checklist"]
> * Kafka REST 프록시에 대한 필수 구성 요소
> * Azure CLI를 사용하여 Apache Kafka 클러스터 만들기

Azure 구독이 아직 없는 경우 시작하기 전에 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure AD에 등록된 애플리케이션. Kafka REST 프록시와 상호 작용하도록 작성하는 클라이언트 애플리케이션은 이 애플리케이션의 ID 및 비밀을 사용하여 Azure에 인증합니다. 자세한 내용은 [Microsoft ID 플랫폼을 사용하여 애플리케이션 등록](../../active-directory/develop/quickstart-register-app.md)을 참조하세요.

* 멤버로 등록된 애플리케이션이 있는 Azure AD 보안 그룹입니다. 이 보안 그룹은 REST 프록시와 상호 작용할 수 있는 애플리케이션을 제어하는 데 사용됩니다. Azure AD 그룹을 만드는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

* Azure CLI. 2\.0.79 버전 이상이 있는지 확인합니다. [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli)를 참조하세요.

## <a name="create-an-apache-kafka-cluster"></a>Apache Kafka 클러스터 만들기

1. Azure 구독에 로그인합니다.

    ```azurecli
    az login
    
    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

1. 환경 변수를 설정합니다. 이 자습서에서 변수를 사용하는 것은 Bash를 기반으로 합니다. 다른 환경에서는 약간의 변형이 필요합니다.

    |변수 | Description |
    |---|---|
    |resourceGroupName|RESOURCEGROUPNAME을 새 리소스 그룹의 이름으로 바꿉니다.|
    |위치|LOCATION을 클러스터가 만들어질 지역으로 바꿉니다. 올바른 위치 목록을 보려면 `az account list-locations` 명령을 사용합니다.|
    |clusterName|CLUSTERNAME을 전역적으로 고유한 새 클러스터 이름으로 바꿉니다.|
    |storageAccount|STORAGEACCOUNTNAME을 새 스토리지 계정의 이름으로 바꿉니다.|
    |httpPassword|PASSWORD를 클러스터 로그인(**admin**)의 암호로 바꿉니다.|
    |sshPassword|PASSWORD를 보안 셸 사용자 이름(**sshuser**)의 암호로 바꿉니다.|
    |securityGroupName|SECURITYGROUPNAME을 Kafka REST 프록시의 클라이언트 AAD 보안 그룹 이름으로 바꿉니다. 이 변수는 `az-hdinsight-create`의 `--kafka-client-group-name` 매개 변수로 전달됩니다.|
    |securityGroupID|SECURITYGROUPID를 Kafka REST 프록시의 클라이언트 AAD 보안 그룹 ID로 바꿉니다. 이 변수는 `az-hdinsight-create`의 `--kafka-client-group-id` 매개 변수로 전달됩니다.|
    |storageContainer|클러스터에서 사용할 스토리지 컨테이너이며, 이 자습서에서는 있는 그대로 둡니다. 이 변수는 클러스터의 이름으로 설정됩니다.|
    |workernodeCount|클러스터의 작업자 노드 수이며, 이 자습서에서는 있는 그대로 둡니다. 고가용성을 보장하기 위해 Kafka에는 3개 이상의 작업자 노드가 필요합니다.|
    |clusterType|HDInsight 클러스터 유형이며, 이 자습서에서는 있는 그대로 둡니다.|
    |clusterVersion|HDInsight 클러스터 버전이며, 이 자습서에서는 있는 그대로 둡니다. Kafka REST 프록시에는 클러스터 버전 4.0 이상이 필요합니다.|
    |componentVersion|Kafka 버전이며, 이 자습서에서는 있는 그대로 둡니다. Kafka REST 프록시에는 구성 요소 버전 2.1 이상이 필요합니다.|

    변수를 원하는 값으로 업데이트합니다. 그런 다음, CLI 명령을 입력하여 환경 변수를 설정합니다.

    ```azurecli
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export storageAccount=STORAGEACCOUNTNAME
    export httpPassword='PASSWORD'
    export sshPassword='PASSWORD'
    export securityGroupName=SECURITYGROUPNAME
    export securityGroupID=SECURITYGROUPID

    export storageContainer=$(echo $clusterName | tr "[:upper:]" "[:lower:]")
    export workernodeCount=3
    export clusterType=kafka
    export clusterVersion=4.0
    export componentVersion=kafka=2.1
    ```

1. 아래 명령을 입력하여 [리소스 그룹을 만듭니다](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create).

    ```azurecli
     az group create \
        --location $location \
        --name $resourceGroupName
    ```

1. 아래 명령을 입력하여 [Azure Storage 계정을 만듭니다](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create).

    ```azurecli
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $storageAccount \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

1. 아래 명령을 입력하여 Azure Storage 계정에서 [기본 키를 추출](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list)하고 변수에 저장합니다.

    ```azurecli
    export storageAccountKey=$(az storage account keys list \
        --account-name $storageAccount \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

1. 아래 명령을 입력하여 [Azure Storage 컨테이너를 만듭니다](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create).

    ```azurecli
    az storage container create \
        --name $storageContainer \
        --account-key $storageAccountKey \
        --account-name $storageAccount
    ```

1. [HDInsight 클러스터를 만듭니다](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create). 명령을 입력하기 전에 다음 매개 변수를 확인하세요.

    1. Kafka 클러스터에 필요한 매개 변수는 다음과 같습니다.

        |매개 변수 | Description|
        |---|---|
        |--type|값은 **Kafka**여야 합니다.|
        |--workernode-data-disks-per-node|작업자 노드당 사용할 데이터 디스크의 수입니다. HDInsight Kafka는 데이터 디스크에서만 지원됩니다. 이 자습서에서는 **2** 값을 사용합니다.|

    1. Kafka REST 프록시에 필요한 매개 변수는 다음과 같습니다.

        |매개 변수 | Description|
        |---|---|
        |--kafka-management-node-size|노드의 크기입니다. 이 자습서에서는 **Standard_D4_v2** 값을 사용합니다.|
        |--kafka-client-group-id|Kafka REST 프록시의 클라이언트 AAD 보안 그룹 ID입니다. 값은 **$securityGroupID** 변수에서 전달됩니다.|
        |--kafka-client-group-name|Kafka REST 프록시의 클라이언트 AAD 보안 그룹 이름입니다. 값은 **$securityGroupName** 변수에서 전달됩니다.|
        |--version|HDInsight 클러스터 버전은 4.0 이상이어야 합니다. 값은 **$clusterVersion** 변수에서 전달됩니다.|
        |--component-version|Kafka 버전은 2.1 이상이어야 합니다. 값은 **$componentVersion** 변수에서 전달됩니다.|
    
        REST 프록시를 사용하지 않고 클러스터를 만들려면 `az hdinsight create` 명령에서 `--kafka-management-node-size`, `--kafka-client-group-id` 및 `--kafka-client-group-name`을 제거합니다.

    1. 기존의 가상 네트워크가 있는 경우 `--vnet-name` 및 `--subnet` 매개 변수와 해당 값을 추가합니다.

    다음 명령을 입력하여 클러스터를 만듭니다.

    ```azurecli
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpPassword \
        --http-user admin \
        --location $location \
        --ssh-password $sshPassword \
        --ssh-user sshuser \
        --storage-account $storageAccount \
        --storage-account-key $storageAccountKey \
        --storage-container $storageContainer \
        --version $clusterVersion \
        --workernode-count $workernodeCount \
        --workernode-data-disks-per-node 2 \
        --kafka-management-node-size "Standard_D4_v2" \
        --kafka-client-group-id $securityGroupID \
        --kafka-client-group-name "$securityGroupName"
    ```

    클러스터 만들기 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 일반적으로 약 15분이 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

리소스를 제거하려면 다음 명령의 전체 또는 일부를 입력합니다.

```azurecli
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $storageAccount  \
    --name $storageContainer

# Remove storage account
az storage account delete \
    --name $storageAccount  \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 Azure HDInsight에서 Apache Kafka REST 프록시 사용 클러스터가 성공적으로 만들어졌으므로 Python 코드를 사용하여 REST 프록시와 상호 작용할 수 있습니다.

> [!div class="nextstepaction"]
> [샘플 애플리케이션 만들기](./rest-proxy.md#client-application-sample)