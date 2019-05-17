---
title: Azure CLI-Azure HDInsight를 사용 하 여 Apache Hadoop 클러스터 만들기
description: 플랫폼 간 Azure CLI를 사용 하 여 HDInsight 클러스터를 만드는 방법에 알아봅니다.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: hrasheed
ms.openlocfilehash: 0a278cd98b0dd6c6d8f0fe9bfee81e5bafd4f543
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597688"
---
# <a name="create-hdinsight-clusters-using-the-azure-cli"></a>Azure CLI를 사용하여 HDInsight 클러스터 만들기

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure CLI를 사용 하 여 HDInsight 3.6 클러스터를 만드는이 문서의 연습에서 설명 합니다.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Azure CLI. Azure CLI를 설치 하지 않은 경우 [Azure CLI 설치](https://docs.microsoft.com/cli/azure/install-azure-cli) 단계에 대 한 합니다.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-cluster"></a>클러스터 만들기

1. Azure 구독에 로그인 합니다. Azure Cloud Shell을 사용 하도록 선택 하기만 하면 계획 이라면 **사용해** 코드 블록의 오른쪽 위 모퉁이에서. 그렇지 않은 경우 아래 명령을 입력 합니다.

    ```azurecli-interactive
    az login

    # If you have multiple subscriptions, set the one to use
    # az account set --subscription "SUBSCRIPTIONID"
    ```

2. 환경 변수를 설정 합니다. 이 문서에서 변수 사용은 Bash를 기반으로 합니다. 다른 환경에 대 한 약간의 변형이 필요 합니다. 참조 [az-hdinsight 만들기](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 클러스터 생성에 대 한 가능한 매개 변수의 전체 목록은 합니다.

    |매개 변수 | 설명 |
    |---|---|
    |`--size`| 클러스터의 작업자 노드 수입니다. 이 문서에서는 변수 `clusterSizeInNodes` 에 전달 된 값으로 `--size`입니다. |
    |`--version`| HDInsight 클러스터 버전입니다. 이 문서에서는 변수 `clusterVersion` 에 전달 된 값으로 `--version`입니다. 참고 항목: [지원 되는 HDInsight 버전](./hdinsight-component-versioning.md#supported-hdinsight-versions)합니다.|
    |`--type`| HDInsight 클러스터를 같은 유형의: interactivehive, hadoop, hbase, kafka, storm, spark, rserver, mlservices 합니다.  이 문서에서는 변수 `clusterType` 에 전달 된 값으로 `--type`입니다. 참고 항목: [클러스터 유형 및 구성](./hdinsight-hadoop-provision-linux-clusters.md#cluster-types)합니다.|
    |`--component-version`|공백으로 구분 된 버전에서 다양 한 Hadoop 구성 요소, 버전 ' 구성 요소 버전 =' 형식입니다. 이 문서에서는 변수 `componentVersion` 에 전달 된 값으로 `--component-version`입니다. 참고 항목: [Hadoop 구성 요소](./hdinsight-component-versioning.md#apache-hadoop-components-available-with-different-hdinsight-versions)합니다.|

    바꿉니다 `RESOURCEGROUPNAME`, `LOCATION`를 `CLUSTERNAME`를 `STORAGEACCOUNTNAME`, 및 `PASSWORD` 원하는 값으로. 필요에 따라 다른 변수 값을 변경 합니다. 다음 CLI 명령을 입력 합니다.

    ```azurecli-interactive
    export resourceGroupName=RESOURCEGROUPNAME
    export location=LOCATION
    export clusterName=CLUSTERNAME
    export AZURE_STORAGE_ACCOUNT=STORAGEACCOUNTNAME
    export httpCredential='PASSWORD'
    export sshCredentials='PASSWORD'
    
    export AZURE_STORAGE_CONTAINER=$clusterName
    export clusterSizeInNodes=1
    export clusterVersion=3.6
    export clusterType=hadoop
    export componentVersion=Hadoop=2.7
    ```

3. [리소스 그룹을 만들려면](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) 아래 명령을 입력 하 여:

    ```azurecli-interactive
    az group create \
        --location $location \
        --name $resourceGroupName
    ```

    유효한 위치 목록을 사용 합니다 `az account list-locations` 명령을 실행 하 고 다음 위치 중 하나를 사용 하 여는 `name` 값입니다.

4. [Azure storage 계정 만들기](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) 아래 명령을 입력 하 여:

    ```azurecli-interactive
    # Note: kind BlobStorage is not available as the default storage account.
    az storage account create \
        --name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --https-only true \
        --kind StorageV2 \
        --location $location \
        --sku Standard_LRS
    ```

5. [Azure 저장소 계정에서 기본 키를 추출](https://docs.microsoft.com/cli/azure/storage/account/keys?view=azure-cli-latest#az-storage-account-keys-list) 아래 명령을 입력 하 여 변수에 저장 합니다.

    ```azurecli-interactive
    export AZURE_STORAGE_KEY=$(az storage account keys list \
        --account-name $AZURE_STORAGE_ACCOUNT \
        --resource-group $resourceGroupName \
        --query [0].value -o tsv)
    ```

6. [Azure storage 컨테이너를 만드는](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) 아래 명령을 입력 하 여:

    ```azurecli-interactive
    az storage container create \
        --name $AZURE_STORAGE_CONTAINER \
        --account-key $AZURE_STORAGE_KEY \
        --account-name $AZURE_STORAGE_ACCOUNT
    ```

7. [HDInsight 클러스터를 만들](https://docs.microsoft.com/cli/azure/hdinsight?view=azure-cli-latest#az-hdinsight-create) 다음 명령을 입력 하 여:

    ```azurecli-interactive
    az hdinsight create \
        --name $clusterName \
        --resource-group $resourceGroupName \
        --type $clusterType \
        --component-version $componentVersion \
        --http-password $httpCredential \
        --http-user admin \
        --location $location \
        --size $clusterSizeInNodes \
        --ssh-password $sshCredentials \
        --ssh-user sshuser \
        --storage-account $AZURE_STORAGE_ACCOUNT \
        --storage-account-key $AZURE_STORAGE_KEY \
        --storage-default-container $AZURE_STORAGE_CONTAINER \
        --version $clusterVersion
    ```

    > [!IMPORTANT]  
    > HDInsight 클러스터는 워크로드 또는 클러스터에 대한 튜닝 기술에 해당하는 다양한 형식을 제공합니다. 하나의 클러스터에서 Storm 및 HBase 등의 여러 유형을 결합하는 클러스터를 만들기 위해 지원되는 메서드가 없습니다.

    클러스터 생성 프로세스를 완료하는 데 몇 분 정도 걸릴 수 있습니다. 일반적으로 약 15분이 걸립니다.

## <a name="clean-up-resources"></a>리소스 정리

이 문서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 저장소에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

전체 또는 일부 리소스를 제거 하려면 다음 명령을 입력 합니다.

```azurecli-interactive
# Remove cluster
az hdinsight delete \
    --name $clusterName \
    --resource-group $resourceGroupName

# Remove storage container
az storage container delete \
    --account-name $AZURE_STORAGE_ACCOUNT \
    --name $AZURE_STORAGE_CONTAINER

# Remove storage account
az storage account delete \
    --name $AZURE_STORAGE_ACCOUNT \
    --resource-group $resourceGroupName

# Remove resource group
az group delete \
    --name $resourceGroupName
```

## <a name="troubleshoot"></a>문제 해결

HDInsight 클러스터를 만드는 동안 문제가 발생할 경우 [액세스 제어 요구 사항](./hdinsight-hadoop-customize-cluster-linux.md#access-control)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure CLI를 사용하여 HDInsight 클러스터를 정상적으로 만들었으므로 다음을 사용하여 클러스터 작업을 수행하는 방법을 알아봅니다.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight에서 Apache Pig 사용](hadoop/hdinsight-use-pig.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm 클러스터

* [HDInsight에서 Apache Storm용 Java 토폴로지 개발](storm/apache-storm-develop-java-topology.md)
* [HDInsight의 Apache Storm에서 Python 구성 요소 사용](storm/apache-storm-develop-python-topology.md)
* [HDInsight에서 Apache Storm을 사용하는 토폴로지 배포 및 모니터링](storm/apache-storm-deploy-monitor-topology-linux.md)
