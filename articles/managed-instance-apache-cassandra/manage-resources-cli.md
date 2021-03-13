---
title: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 리소스 관리
description: Azure CLI를 사용 하 여 Apache Cassandra에 대 한 Azure Managed Instance의 관리를 자동화 하는 일반적인 명령에 대해 알아봅니다.
author: TheovanKraay
ms.service: managed-instance-apache-cassandra
ms.topic: how-to
ms.date: 03/02/2021
ms.author: thvankra
ms.openlocfilehash: 68b1ca625b5c8bd7ec195b89de63485c542e6691
ms.sourcegitcommit: df1930c9fa3d8f6592f812c42ec611043e817b3b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/13/2021
ms.locfileid: "103419075"
---
# <a name="manage-azure-managed-instance-for-apache-cassandra-resources-using-azure-cli-preview"></a>Azure CLI를 사용 하 여 Apache Cassandra 리소스에 대 한 Azure Managed Instance 관리 (미리 보기)

이 문서에서는 Azure CLI를 사용 하 여 Apache Cassandra 클러스터에 대 한 Azure Managed Instance의 관리를 자동화 하는 일반적인 명령을 설명 합니다.

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

> [!IMPORTANT]
> 이 문서를 진행하려면 Azure CLI 버전 2.12.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.
>
> Apache Cassandra 리소스에 대 한 Azure Managed Instance 관리는 Azure Resource Manager 리소스 Uri와 함께 작동 하는 방법을 위반 하므로 이름을 바꿀 수 없습니다.

## <a name="azure-managed-instance-for-apache-cassandra-clusters"></a>Apache Cassandra 클러스터에 대 한 Azure Managed Instance

다음 섹션에서는 다음을 포함 하 여 Apache Cassandra 클러스터에 대 한 Azure Managed Instance를 관리 하는 방법을 보여 줍니다.

* [관리 되는 인스턴스 클러스터 만들기](#create-cluster)
* [관리 되는 인스턴스 클러스터 삭제](#delete-cluster)
* [클러스터 세부 정보 가져오기](#get-cluster-details)
* [클러스터 노드 상태 가져오기](#get-cluster-status)
* [리소스 그룹별 클러스터 나열](#list-clusters-resource-group)
* [구독 ID 별 클러스터 나열](#list-clusters-subscription)

### <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>관리형 인스턴스 클러스터 만들기

Apache Cassandra 클러스터에 대 한 Azure Managed Instance를 만듭니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
location='West US'
delegatedManagementSubnetId='/subscriptions/<subscription id>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/management'
initialCassandraAdminPassword='myPassword'

# You can override the cluster name if the original name is not legal for an Azure resource.
# overrideClusterName='ClusterNameIllegalForAzureResource'
# the default Cassandra version is v3.11

az managed-cassandra cluster create \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName \
    --location $location \
    --delegated-management-subnet-id $delegatedManagementSubnetId \
    --initial-cassandra-admin-password $initialCassandraAdminPassword \
```

### <a name="delete-a-managed-instance-cluster"></a><a id="delete-cluster"></a>관리 되는 인스턴스 클러스터 삭제

클러스터를 삭제 합니다.

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster delete \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-details"></a><a id="get-cluster-details"></a>클러스터 세부 정보 가져오기

클러스터 세부 정보 가져오기:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra cluster show \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="get-the-cluster-node-status"></a><a id="get-cluster-status"></a>클러스터 노드 상태 가져오기

클러스터 세부 정보 가져오기:

```azurecli-interactive
clusterName='cassandra-hybrid-cluster'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster node-status \
    --cluster-name $clusterName \
    --resource-group $resourceGroupName
```

### <a name="list-the-clusters-by-resource-group"></a><a id="list-clusters-resource-group"></a>리소스 그룹별 클러스터 나열

리소스 그룹별 클러스터 나열:

```azurecli-interactive
subscriptionId='MySubscriptionId'
resourceGroupName='MyResourceGroup'

az managed-cassandra cluster list\
    --resource-group $resourceGroupName
```

### <a name="list-clusters-by-subscription-id"></a><a id="list-clusters-subscription"></a>구독 ID 별 클러스터 나열

구독 ID로 클러스터 나열:

```azurecli-interactive
# set your subscription id
az account set -s <subscription id>

az managed-cassandra cluster list
```

## <a name="the-managed-instance-datacenters"></a><a id="managed-instance-datacenter"></a>관리 되는 인스턴스 데이터 센터

다음 섹션에서는 다음을 포함 하 여 Apache Cassandra 데이터 센터에 대 한 Azure Managed Instance를 관리 하는 방법을 보여 줍니다.

* [데이터 센터 만들기](#create-datacenter)
* [데이터 센터 삭제](#delete-datacenter)
* [데이터 센터 정보 가져오기](#get-datacenter-details)
* [데이터 센터 업데이트 또는 크기 조정](#update-datacenter)
* [클러스터의 데이터 센터 가져오기](#get-datacenters-cluster)

### <a name="create-a-datacenter"></a><a id="create-datacenter"></a>데이터 센터 만들기

데이터 센터 만들기:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus2'
delegatedSubnetId='/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter create \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --data-center-location $dataCenterLocation \
    --delegated-subnet-id $delegatedSubnetId \
    --node-count 3 
```

### <a name="delete-a-datacenter"></a><a id="delete-datacenter"></a>데이터 센터 삭제

데이터 센터 삭제:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter delete \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="get-datacenter-details"></a><a id="get-datacenter-details"></a>데이터 센터 정보 가져오기

데이터 센터 정보 가져오기:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'

az managed-cassandra datacenter show \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName 
```

### <a name="update-or-scale-a-datacenter"></a><a id="update-datacenter"></a>데이터 센터 업데이트 또는 크기 조정

데이터 센터 업데이트 또는 크기 조정 (nodeCount 값 크기 조정):

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'
dataCenterName='dc1'
dataCenterLocation='eastus'
delegatedSubnetId= '/subscriptions/<Subscription_ID>/resourceGroups/customer-vnet-rg/providers/Microsoft.Network/virtualNetworks/customer-vnet/subnets/dc1-subnet'

az managed-cassandra datacenter update \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName \
    --data-center-name $dataCenterName \
    --node-count 13 
```

### <a name="get-the-datacenters-in-a-cluster"></a><a id="get-datacenters-cluster"></a>클러스터의 데이터 센터 가져오기

클러스터의 데이터 센터 가져오기:

```azurecli-interactive
resourceGroupName='MyResourceGroup'
clusterName='cassandra-hybrid-cluster'

az managed-cassandra datacenter list \
    --resource-group $resourceGroupName \
    --cluster-name $clusterName
```

## <a name="next-steps"></a>다음 단계

* [Azure Portal에서 관리형 인스턴스 만들기](create-cluster-portal.md)
* [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)