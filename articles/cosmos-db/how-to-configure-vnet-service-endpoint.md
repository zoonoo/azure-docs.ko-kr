---
title: Azure Cosmos 계정에 대 한 가상 네트워크 기반 액세스 구성
description: 이 문서에서는 Azure Cosmos DB의 가상 네트워크 서비스 엔드포인트에 필요한 단계를 설명합니다.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mjbrown
ms.openlocfilehash: 442623880c1b95f3d7e038ae44832b74853d2c4a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80366239"
---
# <a name="configure-access-from-virtual-networks-vnet"></a>가상 네트워크(VNet)에서 액세스 구성

Azure 가상 네트워크의 특정 서브넷에서만 액세스할 수 있도록 Azure Cosmos DB 계정을 구성할 수 있습니다. 가상 네트워크의 서브넷에서 연결을 사용하여 Azure Cosmos DB 계정에 액세스하는 것을 제한하려면:

1. 서브넷 및 가상 네트워크 ID를 Azure Cosmos DB에 보내도록 서브넷을 설정합니다. 특정 서브넷에서 Azure Cosmos DB에 대한 서비스 엔드포인트를 사용하도록 설정하면 됩니다.

1. 서브넷을 계정에 액세스할 수 있는 원본으로 지정하는 규칙을 Azure Cosmos DB 계정에 추가합니다.

> [!NOTE]
> Azure Cosmos DB 계정에 대한 서비스 엔드포인트를 서브넷에서 사용하도록 설정되면 Azure Cosmos DB에 도달하는 트래픽의 원본이 공용 IP에서 가상 네트워크 및 서브넷으로 전환됩니다. 트래픽 전환은 이 서브넷에서 액세스하는 모든 Azure Cosmos DB 계정에 적용됩니다. Azure Cosmos DB 계정에 이 서브넷을 허용하는 IP 기반 방화벽이 있는 경우 서비스 지원 서브넷의 요청이 더 이상 IP 방화벽 규칙과 일치하지 않으므로 거부됩니다.
>
> 자세히 알아보려면 이 문서의 [IP 방화벽 규칙에서 가상 네트워크 액세스 제어 목록으로 마이그레이션](#migrate-from-firewall-to-vnet) 섹션에 설명된 단계를 참조하세요.

다음 섹션에서는 Azure Cosmos DB 계정에 대한 가상 네트워크 서비스 엔드포인트를 구성하는 방법을 설명합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="configure-a-service-endpoint-by-using-the-azure-portal"></a><a id="configure-using-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트 구성

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.

1. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택하고 **선택된 네트워크**에서 액세스를 허용하도록 선택합니다.

1. 기존 가상 네트워크의 서브넷에 대 한 액세스 권한을 부여 하려면 **가상**네트워크에서 **기존 Azure virtual network 추가**를 선택 합니다.

1. Azure 가상 네트워크를 추가할 **구독**을 선택합니다. Azure Cosmos DB 계정에 대한 액세스를 제공하려는 Azure **가상 네트워크** 및 **서브넷**을 선택합니다. 다음으로, **사용**을 선택하여 선택한 네트워크가 "Microsoft.AzureCosmosDB"에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 완료 되 면 **추가**를 선택 합니다.

   ![Virtual Network 및 서브넷 선택](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)

1. Azure Cosmos DB 계정을 가상 네트워크에서 액세스할 수 있도록 활성화한 후에 선택한 이 서브넷의 트래픽만 허용합니다. 추가한 가상 네트워크 및 서브넷은 다음 스크린샷에 표시된 것처럼 표시됩니다.

   ![성공적으로 구성된 가상 네트워크 및 서브넷](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하려면 다음 구독 사용 권한이 필요합니다.
>   * 가상 네트워크를 포함한 구독: 네트워크 기여자
>   * Azure Cosmos DB 계정을 포함한 구독: DocumentDB 계정 기여자
>   * 가상 네트워크와 Azure Cosmos DB 계정이 서로 다른 구독에 있는 경우 가상 네트워크를 포함 하는 구독에도 리소스 공급자가 `Microsoft.DocumentDB` 등록 되어 있는지 확인 합니다. 리소스 공급자를 등록 하려면 [Azure 리소스 공급자 및 형식](../azure-resource-manager/management/resource-providers-and-types.md) 문서를 참조 하세요.

리소스 공급자에 구독을 등록 하기 위한 지침은 다음과 같습니다.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>새로운 Azure 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

1. 설정 메뉴에서 **방화벽 및 Azure 가상 네트워크**를 선택하고, **선택된 네트워크**에서 액세스를 허용하도록 선택합니다.  

1. 새로운 Azure 가상 네트워크에 대한 액세스를 허용하려면 **가상 네트워크**에서 **새로운 가상 네트워크 추가**를 선택합니다.  

1. 새 가상 네트워크를 만드는 데 필요한 세부 정보를 제공한 다음, **만들기**를 선택합니다. “Microsoft.AzureCosmosDB”가 활성화된 서비스 엔드포인트와 함께 서브넷이 생성됩니다.

   ![새 가상 네트워크에 대한 가상 네트워크 및 서브넷을 선택합니다.](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Azure Cosmos DB 계정이 Azure Cognitive Search 같은 다른 Azure 서비스에서 사용 되거나 Stream analytics 또는 Power BI에서 액세스 되는 경우 **글로벌 Azure 데이터 센터 내에서 연결 허용**을 선택 하 여 액세스를 허용 합니다.

포털에서 Azure Cosmos DB 메트릭에 대한 액세스 권한이 있는지 확인하려면 **Azure Portal에서 액세스 허용** 옵션을 사용하도록 설정해야 합니다. 이러한 옵션에 대한 자세한 내용은 [IP 방화벽 구성](how-to-configure-firewall.md) 문서를 참조하세요. 액세스를 사용하도록 설정한 후에 **저장**을 선택하여 설정을 저장합니다.

## <a name="remove-a-virtual-network-or-subnet"></a><a id="remove-vnet-or-subnet"></a>가상 네트워크 또는 서브넷 제거

1. **모든 리소스** 블레이드에서 서비스 엔드포인트를 할당한 Azure Cosmos DB 계정을 찾습니다.  

1. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택합니다.  

1. 가상 네트워크 또는 서브넷 규칙을 제거하려면 가상 네트워크 또는 서브넷 옆에 있는 **...** 를 선택하고, **제거**를 선택합니다.

   ![가상 네트워크 제거](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

1. **저장**을 선택하여 변경 내용을 적용합니다.

## <a name="configure-a-service-endpoint-by-using-azure-powershell"></a><a id="configure-using-powershell"></a>Azure PowerShell을 사용하여 서비스 엔드포인트 구성

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용하는 경우 매개 변수에서 추가해야 하는 것뿐만 아니라 IP 필터 및 가상 네트워크 ACL의 전체 목록을 지정해야 합니다.

Azure PowerShell을 사용하여 Azure Cosmos DB 계정에 서비스 엔드포인트를 구성하려면 다음 단계를 사용합니다.  

1. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 설치 및 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  

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

이 샘플은 Azure Cosmos 계정을 서비스 끝점에 대 한 서브넷이 아직 구성 되지 않은 기존 새 가상 네트워크에 연결 하는 방법을 보여 주기 위해 작성 되었습니다. 매개 변수를 `--ignore-missing-vnet-service-endpoint` 사용 하 여이 작업을 수행 합니다. 이렇게 하면 가상 네트워크의 서브넷에 대 한 구성이 완료 되기 전에 Cosmos 계정에 대 한 구성이 오류 없이 완료 될 수 있습니다. 서브넷 구성이 완료되면 구성된 서브넷을 통해 Cosmos 계정에 액세스할 수 있게 됩니다.

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

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](firewall-support.md) 문서를 참조하세요.
