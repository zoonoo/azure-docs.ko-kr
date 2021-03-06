---
title: 빠른 시작 - CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터 만들기
description: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터를 만들려면 이 빠른 시작을 사용하세요.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.openlocfilehash: 510fcf48091266af255c15aced80651619133aab
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101747847"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-using-azure-cli-preview"></a>빠른 시작: Azure CLI를 사용하여 Apache Cassandra용 Azure Managed Instance 클러스터 만들기(미리 보기)

Apache Cassandra용 Azure Managed Instance는 관리형 오픈 소스 Apache Cassandra 데이터 센터의 자동화된 배포 및 크기 조정 작업을 제공합니다. 이 서비스를 사용하면 하이브리드 시나리오를 가속화하고 지속적인 유지 관리를 줄일 수 있습니다.

> [!IMPORTANT]
> Apache Cassandra용 Azure Managed Instance는 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 빠른 시작에서는 Azure CLI 명령을 사용하여 Apache Cassandra용 Azure Managed Instance를 지원하는 클러스터를 만드는 방법을 설명합니다. 데이터 센터를 만들고 데이터 센터 내에서 노드를 스케일 업 또는 다운하는 방법도 보여 줍니다.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

* 이 문서를 진행하려면 Azure CLI 버전 2.12.1 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

* 자체 호스팅 또는 온-프레미스 환경에 연결된 [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). 온-프레미스 환경을 Azure에 연결하는 방법에 대한 자세한 내용은 [Azure에 온-프레미스 네트워크 연결](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/) 문서를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="create-a-managed-instance-cluster"></a><a id="create-cluster"></a>관리형 인스턴스 클러스터 만들기

1. [Azure 포털](https://portal.azure.com/)

1. Azure CLI에 구독 ID를 설정합니다.

   ```azurecli-interactive
   az account set -s <Subscription_ID>
   ```

1. 다음으로, 리소스 그룹에 전용 서브넷을 사용하는 Virtual Network를 만듭니다.

   ```azurecli-interactive
   az network vnet create -n <VNet_Name> -l eastus2 -g <Resource_Group_Name> --subnet-name <Subnet Name>
   ```

1. 관리형 인스턴스에 필요한 몇 가지 특수 권한을 Virtual Network 및 서브넷에 적용합니다. 이를 위해 기존 가상 네트워크의 리소스 ID를 가져와야 합니다. 다음 명령을 실행하고 `Resource ID` 매개 변수의 값을 복사합니다.

   ```azurecli-interactive
   # get the resource ID of the Virtual Network
   az network vnet show -n <VNet_name> -g <Resource_Group_Name> --query "id" --output tsv

1. Now apply the special permissions by using the `az role assignment create` command. Use the `Resource ID` parameter from the output of previous command to the `scope` parameter:

   ```azurecli-interactive
   az role assignment create --assignee e5007d2c-4b13-4a74-9b6a-605d99f03501 --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope <Resource ID>
   ```

   > [!NOTE]
   > 이전 명령에서 `assignee` 및 `role` 값은 각각 고정된 서비스 주체 및 역할 식별자입니다.

1. 다음으로, 새로 만든 Virtual Network에 클러스터를 만듭니다. 다음 명령을 실행하고, 이전 명령에서 검색된 `Resource ID` 값을 `delegatedManagementSubnetId` 변수의 값으로 사용해야 합니다.

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster_Name>'
   location='eastus2'
   delegatedManagementSubnetId='<Resource_ID>'
   initialCassandraAdminPassword='myPassword'
    
   az managed-cassandra cluster create \
      --cluster-name $clusterName \
      --resource-group $resourceGroupName \
      --location $location \
      --delegated-management-subnet-id $delegatedManagementSubnetId \
      --initial-cassandra-admin-password $initialCassandraAdminPassword \
      --debug
   ```

1. 마지막으로, 다음 3개 노드를 사용하여 클러스터의 데이터 센터를 만듭니다.

   ```azurecli-interactive
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId='<Resource_ID>'
    
   az managed-cassandra datacenter create \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --data-center-location $dataCenterLocation \
      --delegated-subnet-id $delegatedSubnetId \
      --node-count 3 
   ```

1. 데이터 센터가 생성된 후 데이터 센터의 노드를 스케일 업 또는 다운하려는 경우 다음 명령을 실행합니다. `node-count` 매개 변수 값을 원하는 값으로 변경합니다.

   ```azurecli-interactive
   resourceGroupName='<Resource_Group_Name>'
   clusterName='<Cluster Name>'
   dataCenterName='dc1'
   dataCenterLocation='eastus2'
   delegatedSubnetId= '<Resource_ID>'
    
   az managed-cassandra datacenter update \
      --resource-group $resourceGroupName \
      --cluster-name $clusterName \
      --data-center-name $dataCenterName \
      --node-count 9 
   ```

## <a name="connect-to-your-cluster"></a>클러스터에 연결

Apache Cassandra용 Azure Managed Instance는 공용 IP 주소를 사용하여 노드를 만들지 않습니다. 새로 만든 Cassandra 클러스터에 연결하려면 가상 네트워크 내에 다른 리소스를 만들어야 합니다. 이 리소스는 Apache의 오픈 소스 쿼리 도구인 [CQLSH](https://cassandra.apache.org/doc/latest/tools/cqlsh.html)가 설치된 가상 머신 또는 애플리케이션이 될 수 있습니다. [Resource Manager 템플릿](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)을 사용하여 Ubuntu 가상 머신을 배포할 수 있습니다. 배포 후 다음 명령에 표시된 것처럼 SSH를 사용하여 머신에 연결하고 CQLSH를 설치합니다.

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="clean-up-resources"></a>리소스 정리

더 이상 필요하지 않은 경우 `az group delete` 명령을 사용하여 리소스 그룹, 관리형 인스턴스 및 모든 관련 리소스를 제거할 수 있습니다.

```azurecli-interactive
az group delete --name <Resource_Group_Name>
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 CLI를 사용하여 Apache Cassandra용 Azure Managed Instance를 만드는 방법을 알아보았습니다. 이제 클러스터 사용을 시작할 수 있습니다.

> [!div class="nextstepaction"]
> [Azure Databricks를 사용하여 관리형 Apache Spark 클러스터 배포](deploy-cluster-databricks.md)
