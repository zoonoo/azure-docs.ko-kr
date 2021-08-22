---
title: Azure HDInsight 만들기 - Azure Data Lake Storage Gen2 - Azure CLI
description: Azure CLI를 사용하여 Azure HDInsight 클러스터에서 Azure Data Lake Storage Gen2를 사용하는 방법을 알아봅니다.
author: guyhay
ms.author: guyhay
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020, devx-track-azurecli
ms.date: 09/17/2020
ms.openlocfilehash: 4adc7fffb04ac917c680993c43ef334a5df80582
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112280480"
---
# <a name="create-a-cluster-with-data-lake-storage-gen2-using-azure-cli"></a>Azure CLI를 사용하여 Data Lake Storage Gen2 클러스터 생성

스토리지에 Data Lake Storage Gen2를 사용하는 HDInsight 클러스터를 만들려면 다음 단계를 수행합니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure Data Lake Storage Gen2에 대해 잘 모르겠으면 [개요 섹션](hdinsight-hadoop-use-data-lake-storage-gen2.md)을 확인하세요. 
- 아직 Azure 계정이 없으면 계속하기 전에 [평가판 계정](https://azure.microsoft.com/free/)에 등록해야 합니다.
- CLI 스크립트 예제는 다음의 세 가지 옵션 중 하나로 실행할 수 있습니다.
    - Azure Portal에서 [Azure Cloud Shell](../cloud-shell/overview.md)을 사용합니다(다음 섹션 참조).
    - 각 코드 블록의 오른쪽 위에 있는 "사용해 보세요." 단추를 통해 포함된 Azure Cloud Shell을 사용합니다.
    - 로컬 CLI 콘솔을 사용하려는 경우 [Azure CLI의 최신 버전을 설치](/cli/azure/install-azure-cli)합니다(2.0.13 이상). 사용자 할당 관리 ID를 배포하려는 Azure 구독과 연결된 계정으로 `az login`을 사용하여 Azure에 로그인합니다. 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

[!INCLUDE [delete-cluster-warning](includes/hdinsight-delete-cluster-warning.md)]

[샘플 템플릿 파일을 다운로드하고](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/hdinsight-adls-gen2-template.json) [샘플 매개 변수 파일을 다운로드](https://github.com/Azure-Samples/hdinsight-data-lake-storage-gen2-templates/blob/master/parameters.json)할 수 있습니다. 아래 템플릿 및 Azure CLI 코드 조각을 사용하기 전에 다음 자리 표시자를 올바른 값으로 바꿉니다.

| 자리 표시자 | Description |
|---|---|
| `<SUBSCRIPTION_ID>` | Azure 구독의 ID |
| `<RESOURCEGROUPNAME>` | 새 클러스터 및 스토리지 계정을 만들 리소스 그룹입니다. |
| `<MANAGEDIDENTITYNAME>` | Azure Data Lake Storage Gen2를 사용하여 스토리지 계정에 대한 권한을 부여 받을 관리 ID의 이름입니다. |
| `<STORAGEACCOUNTNAME>` | 생성될 Azure Data Lake Storage Gen2가 있는 새 스토리지 계정입니다. |
| `<FILESYSTEMNAME>`  | 스토리지 계정에서 이 클러스터가 사용해야 하는 파일 시스템의 이름입니다. |
| `<CLUSTERNAME>` | HDInsight 클러스터의 이름입니다. |
| `<PASSWORD>` | SSH 및 Ambari 대시보드를 사용하여 클러스터에 로그인하기 위해 선택한 암호입니다. |

아래 코드 조각은 다음과 같은 초기 단계를 수행합니다.

1. Azure 계정에 로그인합니다.
1. 만들기 작업을 수행할 활성 구독을 설정합니다.
1. 새 배포 작업에 대한 새 리소스 그룹을 만듭니다.
1. 사용자 할당 관리 ID를 만듭니다.
1. Data Lake Storage Gen2용 기능을 사용할 수 있도록 Azure CLI에 확장을 추가합니다.
1. `--hierarchical-namespace true` 플래그를 사용하여 Data Lake Storage Gen2를 사용하여 새 스토리지 계정을 만듭니다.

```azurecli
az login
az account set --subscription <SUBSCRIPTION_ID>

# Create resource group
az group create --name <RESOURCEGROUPNAME> --location eastus

# Create managed identity
az identity create -g <RESOURCEGROUPNAME> -n <MANAGEDIDENTITYNAME>

az extension add --name storage-preview

az storage account create --name <STORAGEACCOUNTNAME> \
    --resource-group <RESOURCEGROUPNAME> \
    --location eastus --sku Standard_LRS \
    --kind StorageV2 --hierarchical-namespace true
```

다음으로, Portal에 로그인합니다. 스토리지 계정의 **Storage Blob 데이터 기여자** 역할에 새로운 사용자 할당 관리 ID를 추가합니다. 이 단계는 [Azure Portal 사용](hdinsight-hadoop-use-data-lake-storage-gen2.md) 아래의 3단계에 설명되어 있습니다.

 > [!IMPORTANT]
 > 스토리지 계정에 **Storage Blob 데이터 기여자** 역할 권한이 있는 사용자 할당 ID가 있는지 확인합니다. 그렇지 않으면 클러스터를 만들지 못합니다.

```azurecli
az deployment group create --name HDInsightADLSGen2Deployment \
    --resource-group <RESOURCEGROUPNAME> \
    --template-file hdinsight-adls-gen2-template.json \
    --parameters parameters.json
```

## <a name="clean-up-resources"></a>리소스 정리

이 문서를 완료한 후에 클러스터를 삭제할 수 있습니다. HDInsight를 사용하면 데이터가 Azure Storage에 저장되기 때문에 클러스터를 사용하지 않을 때 안전하게 삭제할 수 있습니다. HDInsight 클러스터를 사용하지 않는 기간에도 요금이 청구됩니다. 클러스터에 대한 요금이 스토리지에 대한 요금보다 몇 배 더 많기 때문에, 클러스터를 사용하지 않을 때는 삭제하는 것이 경제적인 면에서 더 합리적입니다.

리소스를 제거하려면 다음 명령의 전체 또는 일부를 입력합니다.

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

HDInsight 클러스터를 성공적으로 만들었습니다. 이제 클러스터를 사용하는 방법을 알아봅니다.

### <a name="apache-spark-clusters"></a>Apache Spark 클러스터

* [스크립트 동작을 사용하여 Linux 기반 HDInsight 클러스터 사용자 지정](hdinsight-hadoop-customize-cluster-linux.md)
* [Scala를 사용하여 독립 실행형 애플리케이션 만들기](spark/apache-spark-create-standalone-application.md)
* [Apache Livy를 사용하여 Apache Spark 클러스터에서 원격으로 작업 실행](spark/apache-spark-livy-rest-interface.md)
* [BI와 Apache Spark: BI 도구와 함께 HDInsight의 Spark를 사용하여 대화형 데이터 분석 수행](spark/apache-spark-use-bi-tools.md)
* [Machine Learning과 Apache Spark: HDInsight의 Spark를 사용하여 식품 검사 결과 예측](spark/apache-spark-machine-learning-mllib-ipython.md)

### <a name="apache-hadoop-clusters"></a>Apache Hadoop 클러스터

* [HDInsight에서 Apache Hive 사용](hadoop/hdinsight-use-hive.md)
* [HDInsight와 함께 MapReduce 사용](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase 클러스터

* [HDInsight에서 Apache HBase 사용 시작](hbase/apache-hbase-tutorial-get-started-linux.md)
* [HDInsight에서 Apache HBase용 Java 애플리케이션 개발](hbase/apache-hbase-build-java-maven-linux.md)
