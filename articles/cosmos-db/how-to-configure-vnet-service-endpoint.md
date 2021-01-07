---
title: Azure Cosmos 계정에 대 한 가상 네트워크 기반 액세스 구성
description: 이 문서에서는 Azure Cosmos DB의 가상 네트워크 서비스 엔드포인트에 필요한 단계를 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/13/2020
ms.author: mjbrown
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d63d21f4c49e3c7aef035208477ac9fc79f2e51
ms.sourcegitcommit: 295db318df10f20ae4aa71b5b03f7fb6cba15fc3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2020
ms.locfileid: "94637186"
---
# <a name="configure-access-to-azure-cosmos-db-from-virtual-networks-vnet"></a>VNet (가상 네트워크)에서 Azure Cosmos DB에 대 한 액세스 구성
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

가상 네트워크(VNet)의 특정 서브넷에서만 액세스할 수 있도록 Azure Cosmos 계정을 구성할 수 있습니다. 가상 네트워크 내의 서브넷에 있는 Azure Cosmos DB에 액세스하도록 [서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 설정하면 해당 서브넷의 트래픽이 서브넷 및 Virtual Network의 ID를 통해 Azure Cosmos DB로 보내집니다. Azure Cosmos DB 서비스 엔드포인트를 사용하도록 설정하고 나면, Azure Cosmos 계정에 추가하여 서브넷에 대한 액세스를 제한할 수 있습니다.

기본적으로 요청에 유효한 권한 부여 토큰이 포함되어 있으면 어떠한 원본에서든 Azure Cosmos 계정에 액세스할 수 있습니다. VNet 내에서 하나 이상의 서브넷을 추가하면 해당 서브넷에서 시작된 요청만 유효한 응답을 받습니다. 다른 원본에서 시작된 요청은 403(금지됨) 응답을 받게 됩니다. 

Azure 가상 네트워크의 특정 서브넷에서만 액세스할 수 있도록 Azure Cosmos DB 계정을 구성할 수 있습니다. 가상 네트워크의 서브넷에서 연결을 사용하여 Azure Cosmos DB 계정에 액세스하는 것을 제한하려면:

1. 서브넷 및 가상 네트워크 ID를 Azure Cosmos DB에 보내도록 서브넷을 설정합니다. 특정 서브넷에서 Azure Cosmos DB에 대한 서비스 엔드포인트를 사용하도록 설정하면 됩니다.

1. 서브넷을 계정에 액세스할 수 있는 원본으로 지정하는 규칙을 Azure Cosmos DB 계정에 추가합니다.

> [!NOTE]
> Azure Cosmos DB 계정에 대한 서비스 엔드포인트를 서브넷에서 사용하도록 설정되면 Azure Cosmos DB에 도달하는 트래픽의 원본이 공용 IP에서 가상 네트워크 및 서브넷으로 전환됩니다. 트래픽 전환은 이 서브넷에서 액세스하는 모든 Azure Cosmos DB 계정에 적용됩니다. Azure Cosmos DB 계정에 이 서브넷을 허용하는 IP 기반 방화벽이 있는 경우 서비스 지원 서브넷의 요청이 더 이상 IP 방화벽 규칙과 일치하지 않으므로 거부됩니다.
>
> 자세히 알아보려면 이 문서의 [IP 방화벽 규칙에서 가상 네트워크 액세스 제어 목록으로 마이그레이션](#migrate-from-firewall-to-vnet) 섹션에 설명된 단계를 참조하세요.

다음 섹션에서는 Azure Cosmos DB 계정에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법을 설명합니다.

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트 구성

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.

1. 설정 메뉴에서 **방화벽 및 가상 네트워크** 를 선택하고 **선택된 네트워크** 에서 액세스를 허용하도록 선택합니다.

1. 기존 가상 네트워크의 서브넷에 대 한 액세스 권한을 부여 하려면 **가상** 네트워크에서 **기존 Azure virtual network 추가** 를 선택 합니다.

1. Azure 가상 네트워크를 추가할 **구독** 을 선택합니다. Azure Cosmos DB 계정에 대한 액세스를 제공하려는 Azure **가상 네트워크** 및 **서브넷** 을 선택합니다. 다음으로, **사용** 을 선택하여 선택한 네트워크가 "Microsoft.AzureCosmosDB"에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 완료 되 면 **추가** 를 선택 합니다.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png" alt-text="Virtual Network 및 서브넷 선택":::

1. Azure Cosmos DB 계정을 가상 네트워크에서 액세스할 수 있도록 활성화한 후에 선택한 이 서브넷의 트래픽만 허용합니다. 추가한 가상 네트워크 및 서브넷은 다음 스크린샷에 표시된 것처럼 표시됩니다.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png" alt-text="성공적으로 구성된 가상 네트워크 및 서브넷":::

> [!NOTE]
> 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하려면 다음 구독 사용 권한이 필요합니다.
>   * 가상 네트워크를 포함한 구독: 네트워크 기여자
>   * Azure Cosmos DB 계정을 포함한 구독: DocumentDB 계정 기여자
>   * 가상 네트워크와 Azure Cosmos DB 계정이 서로 다른 구독에 있는 경우 가상 네트워크를 포함 하는 구독에도 리소스 공급자가 등록 되어 있는지 확인 `Microsoft.DocumentDB` 합니다. 리소스 공급자를 등록 하려면 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 문서를 참조 하세요.

리소스 공급자에 구독을 등록 하기 위한 지침은 다음과 같습니다.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>새로운 Azure 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

1. 설정 메뉴에서 **방화벽 및 Azure 가상 네트워크** 를 선택하고, **선택된 네트워크** 에서 액세스를 허용하도록 선택합니다.  

1. 새로운 Azure 가상 네트워크에 대한 액세스를 허용하려면 **가상 네트워크** 에서 **새로운 가상 네트워크 추가** 를 선택합니다.  

1. 새 가상 네트워크를 만드는 데 필요한 세부 정보를 제공한 다음, **만들기** 를 선택합니다. “Microsoft.AzureCosmosDB”가 활성화된 서비스 엔드포인트와 함께 서브넷이 생성됩니다.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png" alt-text="새 가상 네트워크에 대한 가상 네트워크 및 서브넷을 선택합니다.":::

Azure Cosmos DB 계정이 Azure Cognitive Search 같은 다른 Azure 서비스에서 사용 되거나 Stream analytics 또는 Power BI에서 액세스 되는 경우 **글로벌 Azure 데이터 센터 내에서 연결 허용** 을 선택 하 여 액세스를 허용 합니다.

포털에서 Azure Cosmos DB 메트릭에 대한 액세스 권한이 있는지 확인하려면 **Azure Portal에서 액세스 허용** 옵션을 사용하도록 설정해야 합니다. 이러한 옵션에 대한 자세한 내용은 [IP 방화벽 구성](how-to-configure-firewall.md) 문서를 참조하세요. 액세스를 사용하도록 설정한 후에 **저장** 을 선택하여 설정을 저장합니다.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>가상 네트워크 또는 서브넷 제거

1. **모든 리소스** 블레이드에서 서비스 엔드포인트를 할당한 Azure Cosmos DB 계정을 찾습니다.  

1. 설정 메뉴에서 **방화벽 및 가상 네트워크** 를 선택합니다.  

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 가상 네트워크 또는 서브넷 옆에 있는 **...** 를 선택하고, **제거** 를 선택합니다.

   :::image type="content" source="./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png" alt-text="가상 네트워크 제거":::

1. **저장** 을 선택하여 변경 내용을 적용합니다.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Azure PowerShell을 사용하여 서비스 엔드포인트 구성

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell을 사용하여 Azure Cosmos DB 계정에 서비스 엔드포인트를 구성하려면 다음 단계를 사용합니다.  

1. [Azure PowerShell](/powershell/azure/install-Az-ps) 설치 및 [로그인](/powershell/azure/authenticate-azureps)  

1. 가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.  

   ```powershell
   $resourceGroupName = "<Resource group name>"
   $vnetName = "<Virtual network name>"
   $subnetName = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"
   $serviceEndpoint = "Microsoft.AzureCosmosDB"

   Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName | Set-AzVirtualNetworkSubnetConfig `
      -Name $subnetName `
      -AddressPrefix $subnetPrefix `
      -ServiceEndpoint $serviceEndpoint | Set-AzVirtualNetwork
   ```

   > [!NOTE]
   > PowerShell 또는 Azure CLI를 사용하는 경우 매개 변수에서 추가해야 하는 것뿐만 아니라 IP 필터 및 가상 네트워크 ACL의 전체 목록을 지정해야 합니다.

1. 가상 네트워크 정보를 가져옵니다.

   ```powershell
   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Cosmos DB Virtual Network 규칙 준비

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 새 Virtual Network 끝점 구성을 사용 하 여 Azure Cosmos DB 계정 속성을 업데이트 합니다. 

   ```powershell
   $accountName = "<Cosmos DB account name>"

   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 다음 명령을 실행하여 이전 단계에서 구성한 가상 네트워크 서비스 엔드포인트로 Azure Cosmos DB 계정이 업데이트되었는지 확인합니다.

   ```powershell
   $account = Get-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName

   $account.IsVirtualNetworkFilterEnabled
   $account.VirtualNetworkRules
   ```

## <a name="configure-a-service-endpoint-by-using-the-azure-cli"></a><a id="configure-using-cli"></a>Azure CLI를 사용하여 서비스 엔드포인트 구성

Azure Cosmos 계정은 나중에 생성 되거나 업데이트 되는 경우 서비스 끝점에 대해 서브넷이 이미 구성 되어 있는 경우 구성할 수 있습니다. 서브넷이 아직 구성 되지 않은 Cosmos 계정에서 서비스 끝점을 사용 하도록 설정 하 고 나중에 서브넷을 구성할 때 작동을 시작할 수도 있습니다. 이러한 유연성을 통해 Cosmos 계정 및 가상 네트워크 리소스 모두에 액세스할 수 없는 관리자가 구성을 서로 독립적으로 만들 수 있습니다.

### <a name="create-a-new-cosmos-account-and-connect-it-to-a-back-end-subnet-for-a-new-virtual-network"></a>새 Cosmos 계정을 만들고 새 가상 네트워크에 대 한 백 엔드 서브넷에 연결 합니다.

이 예제에서는 가상 네트워크 및 서브넷이 생성 될 때 둘 다에 대해 서비스 끝점을 사용 하도록 설정 된 상태로 만들어집니다.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet with service endpoints enabled for Cosmos DB
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values and service endpoints
az cosmosdb create \
   -n $accountName \
   -g $resourceGroupName \
   --enable-virtual-network true \
   --virtual-network-rules $svcEndpoint
```

### <a name="connect-and-configure-a-cosmos-account-to-a-back-end-subnet-independently"></a>Cosmos 계정을 백 엔드 서브넷에 독립적으로 연결 및 구성

이 샘플은 Azure Cosmos 계정을 서비스 끝점에 대 한 서브넷이 아직 구성 되지 않은 기존 새 가상 네트워크에 연결 하는 방법을 보여 주기 위해 작성 되었습니다. 매개 변수를 사용 하 여이 작업을 수행 `--ignore-missing-vnet-service-endpoint` 합니다. 이렇게 하면 가상 네트워크의 서브넷에 대 한 구성이 완료 되기 전에 Cosmos 계정에 대 한 구성이 오류 없이 완료 될 수 있습니다. 서브넷 구성이 완료되면 구성된 서브넷을 통해 Cosmos 계정에 액세스할 수 있게 됩니다.

```azurecli-interactive
# Create an Azure Cosmos Account with a service endpoint connected to a backend subnet
# that is not yet enabled for service endpoints.

# Resource group and Cosmos account variables
resourceGroupName='MyResourceGroup'
location='West US 2'
accountName='mycosmosaccount'

# Variables for a new Virtual Network with two subnets
vnetName='myVnet'
frontEnd='FrontEnd'
backEnd='BackEnd'

# Create a resource group
az group create -n $resourceGroupName -l $location

# Create a virtual network with a front-end subnet
az network vnet create \
   -n $vnetName \
   -g $resourceGroupName \
   --address-prefix 10.0.0.0/16 \
   --subnet-name $frontEnd \
   --subnet-prefix 10.0.1.0/24

# Create a back-end subnet but without configuring service endpoints (--service-endpoints Microsoft.AzureCosmosDB)
az network vnet subnet create \
   -n $backEnd \
   -g $resourceGroupName \
   --address-prefix 10.0.2.0/24 \
   --vnet-name $vnetName

svcEndpoint=$(az network vnet subnet show -g $resourceGroupName -n $backEnd --vnet-name $vnetName --query 'id' -o tsv)

# Create a Cosmos DB account with default values
az cosmosdb create -n $accountName -g $resourceGroupName

# Add the virtual network rule but ignore the missing service endpoint on the subnet
az cosmosdb network-rule add \
   -n $accountName \
   -g $resourceGroupName \
   --virtual-network $vnetName \
   --subnet svcEndpoint \
   --ignore-missing-vnet-service-endpoint true

read -p'Press any key to now configure the subnet for service endpoints'

az network vnet subnet update \
   -n $backEnd \
   -g $resourceGroupName \
   --vnet-name $vnetName \
   --service-endpoints Microsoft.AzureCosmosDB
```

## <a name="port-range-when-using-direct-mode"></a>직접 모드를 사용 하는 경우의 포트 범위

직접 모드 연결을 통해 Azure Cosmos 계정을 사용 하 여 서비스 끝점을 사용 하는 경우 TCP 포트 범위 (1만 ~ 2만)가 열려 있는지 확인 해야 합니다.

## <a name="migrating-from-an-ip-firewall-rule-to-a-virtual-network-acl"></a><a id="migrate-from-firewall-to-vnet"></a>IP 방화벽 규칙에서 가상 네트워크 ACL로 마이그레이션

IP 방화벽 규칙을 사용 하 여 가상 네트워크 서비스 끝점을 사용 하는 Azure Cosmos DB 계정을 마이그레이션하려면 다음 단계를 사용 합니다.

서브넷의 서비스 끝점에 대해 Azure Cosmos DB 계정을 구성한 후에는 해당 서브넷의 요청이 원본 공용 IP 주소 대신 가상 네트워크 및 서브넷 원본 정보를 사용 하 여 Azure Cosmos DB으로 전송 됩니다. 이러한 요청은 Azure Cosmos DB 계정에 구성 된 IP 필터와 더 이상 일치 하지 않습니다. 따라서 가동 중지 시간을 방지 하기 위해 다음 단계가 필요 합니다.

계속 하기 전에 "가상 네트워크의 기존 서브넷에 대 한 서비스 끝점 사용"의 위에 나와 있는 단계를 사용 하 여 가상 네트워크 및 서브넷에서 Azure Cosmos DB 서비스 끝점을 사용 하도록 설정 합니다.

1. 가상 네트워크 및 서브넷 정보 가져오기:

   ```powershell
   $resourceGroupName = "myResourceGroup"
   $accountName = "mycosmosaccount"
   $vnetName = "myVnet"
   $subnetName = "mySubnet"

   $vnet = Get-AzVirtualNetwork `
      -ResourceGroupName $resourceGroupName `
      -Name $vnetName

   $subnetId = $vnet.Id + "/subnets/" + $subnetName
   ```

1. Azure Cosmos DB 계정에 대 한 새 Virtual Network 규칙 개체를 준비 합니다.

   ```powershell
   $vnetRule = New-AzCosmosDBVirtualNetworkRule `
      -Id $subnetId
   ```

1. 서브넷에서 서비스 끝점에 액세스할 수 있도록 Azure Cosmos DB 계정을 업데이트 합니다.

   ```powershell
   Update-AzCosmosDBAccount `
      -ResourceGroupName $resourceGroupName `
      -Name $accountName `
      -EnableVirtualNetwork $true `
      -VirtualNetworkRuleObject @($vnetRule)
   ```

1. 서브넷에서 액세스 하는 모든 Azure Cosmos DB 계정에 대해 이전 단계를 반복 합니다.

1. Azure Cosmos DB 계정의 방화벽 규칙에서 서브넷에 대 한 IP 방화벽 규칙을 제거 합니다.

## <a name="frequently-asked-questions"></a>질문과 대답

가상 네트워크의 액세스 구성에 대해 몇 가지 자주 묻는 질문은 다음과 같습니다.

### <a name="are-notebooks-and-mongocassandra-shell-currently-compatible-with-virtual-network-enabled-accounts"></a>노트북 및 Mongo/Cassandra Shell이 현재 Virtual Network 사용 계정과 호환 되나요?

현재 Cosmos DB 데이터 탐색기 및 [Jupyter 노트북 서비스](./cosmosdb-jupyter-notebooks.md)의 [Mongo shell](https://devblogs.microsoft.com/cosmosdb/preview-native-mongo-shell/) 및 [Cassandra shell](https://devblogs.microsoft.com/cosmosdb/announcing-native-cassandra-shell-preview/) 통합은 VNET 액세스에서 지원 되지 않습니다. 이 기능은 현재 개발 중입니다.

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>가상 네트워크 서비스 엔드포인트 및 IP 액세스 제어 정책 모두 Azure Cosmos 계정에서 지정할 수 있나요? 

Azure Cosmos 계정에 가상 네트워크 서비스 끝점 및 IP 액세스 제어 정책 (방화벽이 라고도 함)을 모두 사용 하도록 설정할 수 있습니다. 이러한 두 기능은 상호 보완적이며 전체적으로 Azure Cosmos 계정의 격리 및 보안을 보장합니다. IP 방화벽을 사용하면 고정 IP가 사용자 계정에 액세스할 수 있습니다. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>가상 네트워크 내의 서브넷에 대한 액세스를 제한하려면 어떻게 해야 하나요? 

서브넷의 Azure Cosmos 계정에 대한 액세스를 제한하려면 두 단계가 필요합니다. 먼저 서브넷의 트래픽이 해당 서브넷과 가상 네트워크 ID를 Azure Cosmos DB로 전송하도록 허용합니다. 서브넷에서 Azure Cosmos DB에 대한 서비스 엔드포인트를 사용하도록 설정하면 됩니다. 다음은 이 서브넷을 계정에 액세스할 수 있는 소스로 지정하는 규칙을 Azure Cosmos 계정에 추가하는 것입니다.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>가상 네트워크 ACL 및 IP 방화벽은 요청 또는 연결을 거부하나요? 

IP 방화벽 또는 가상 네트워크 액세스 규칙이 추가되면 허용된 원본의 요청만 유효한 응답을 가져옵니다. 다른 요청은 거부되며 403(금지됨)이 수신됩니다. Azure Cosmos 계정의 방화벽을 연결 수준 방화벽과 구별하는 것이 중요합니다. 원본은 여전히 서비스에 연결할 수 있으며 연결 자체는 거부되지 않습니다.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>서비스 엔드포인트를 서브넷의 Azure Cosmos DB에 대해 사용하도록 설정했을 때 내 요청이 차단되기 시작했습니다. 어떻게 된 건가요?

Azure Cosmos DB에 대한 서비스 엔드포인트가 서브넷에서 사용하도록 설정되면 계정에 도달하는 트래픽의 원본이 공용 IP에서 가상 네트워크 및 서브넷으로 전환됩니다. Azure Cosmos 계정에 IP 기반 방화벽이 있는 경우에만 서브넷을 사용하는 서비스의 요청이 더 이상 IP 방화벽 규칙과 일치하지 않아 거부됩니다. IP 기반 방화벽에서 가상 네트워크 기반 액세스 제어로 원활하게 마이그레이션하는 단계로 이동하세요.

### <a name="are-additional-azure-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>VNET 서비스 끝점을 사용 하는 Azure Cosmos 계정에 추가 Azure RBAC 권한이 필요 한가요?

Azure Cosmos 계정에 VNet 서비스 엔드포인트를 추가한 후 계정 설정을 변경하려면 Azure Cosmos 계정에 구성된 모든 VNET의 `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` 작업에 액세스해야 합니다. 속성을 평가하기 전에 권한 부여 프로세스에서 리소스(예: 데이터베이스 및 가상 네트워크 리소스)에 대한 액세스의 유효성을 검사하기 때문에 이 권한이 필요합니다.
 
사용자가 Azure CLI를 사용하여 VNET ACL을 지정하지 않더라도 권한 부여에서는 VNet 리소스 작업 권한의 유효성을 검사합니다. 현재 Azure Cosmos 계정의 컨트롤 플레인은 Azure Cosmos 계정의 전체 상태를 설정하도록 지원합니다. 컨트롤 플레인 호출에 대한 매개 변수 중 하나는 `virtualNetworkRules`입니다. 이 매개 변수를 지정하지 않으면 Azure CLI는 get database를 호출하여 `virtualNetworkRules`를 검색하고 업데이트 호출에서 이 값을 사용합니다.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>피어링된 가상 네트워크도 Azure Cosmos 계정에 액세스할 수 있나요? 
Azure Cosmos 계정에 추가된 가상 네트워크 및 해당 서브넷만 액세스할 수 있습니다. 피어링된 가상 네트워크 내 서브넷이 계정에 추가될 때까지는 피어링된 해당 VNet이 계정에 액세스할 수 없습니다.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>단일 Cosmos 계정에 액세스할 수 있는 최대 서브넷 수는? 
현재 Azure Cosmos 계정에 허용되는 최대 서브넷 수는 256개입니다.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>VPN 및 Express Route의 액세스를 사용하도록 설정할 수 있나요? 
온-프레미스에서 ExpressRoute를 통해 Azure Cosmos 계정에 액세스하려면 Microsoft 피어링을 사용하도록 설정해야 합니다. IP 방화벽 또는 가상 네트워크 액세스 규칙을 입력한 후에는 Azure Cosmos 계정 IP 방화벽에서 Microsoft 피어링에 사용되는 공용 IP 주소를 추가하여 Azure Cosmos 계정에 대한 온-프레미스 서비스 액세스를 허용할 수 있습니다. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>NSG(네트워크 보안 그룹) 규칙을 업데이트해야 하나요? 
NSG 규칙은 가상 네트워크를 사용하여 서브넷 간의 연결을 제한하는 데 사용됩니다. 서브넷에 Azure Cosmos DB에 대한 서비스 엔드포인트를 추가한 경우 Azure Cosmos 계정에 대한 NSG에서 아웃바운드 연결을 열 필요가 없습니다. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>모든 VNet에서 서비스 엔드포인트를 사용할 수 있나요?
아니요, Azure Resource Manager 가상 네트워크만 서비스 엔드포인트를 사용하도록 설정할 수 있습니다. 클래식 가상 네트워크는 서비스 엔드포인트를 지원하지 않습니다.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Azure Cosmos DB에 대해 서비스 엔드포인트 액세스가 가능하면 "공용 Azure 데이터 센터 내에서 연결을 허용"해도 되나요?  
이는 Azure Data Factory, Azure Cognitive Search 또는 주어진 Azure 지역에 배포되는 다른 서비스와 같이 다른 Azure 자사 서비스에서 Azure Cosmos DB 계정에 액세스하려는 경우에만 필요합니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](how-to-configure-firewall.md) 문서를 참조하세요.
