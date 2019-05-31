---
title: Azure Cosmos DB 계정에 대한 가상 네트워크 및 서브넷 기반 액세스 구성
description: 이 문서에서는 Azure Cosmos DB의 가상 네트워크 서비스 엔드포인트에 필요한 단계를 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 375e79d2fe70e0988d8c58997a746f77b21d7f50
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/27/2019
ms.locfileid: "66241995"
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

## <a id="configure-using-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트 구성

### <a name="configure-a-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.

1. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택하고 **선택된 네트워크**에서 액세스를 허용하도록 선택합니다.

1. 기존 가상 네트워크의 서브넷에 대한 액세스를 허용하려면 **가상 네트워크**에서 **기존 Azure Virtual Network 추가**를 선택합니다.

1. Azure 가상 네트워크를 추가할 **구독**을 선택합니다. Azure Cosmos DB 계정에 대한 액세스를 제공하려는 Azure **가상 네트워크** 및 **서브넷**을 선택합니다. 다음으로, **사용**을 선택하여 선택한 네트워크가 "Microsoft.AzureCosmosDB"에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 완료되면 **추가**를 선택합니다. 

   ![Virtual Network 및 서브넷 선택](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Azure Cosmos DB 계정을 가상 네트워크에서 액세스할 수 있도록 활성화한 후에 선택한 이 서브넷의 트래픽만 허용합니다. 추가한 가상 네트워크 및 서브넷은 다음 스크린샷에 표시된 것처럼 표시됩니다.

   ![성공적으로 구성된 가상 네트워크 및 서브넷](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하려면 다음 구독 사용 권한이 필요합니다.
>   * 가상 네트워크를 사용한 구독: 네트워크 기여자
>   * Azure Cosmos DB 계정을 사용한 구독: DocumentDB 계정 기여자
>   * 가상 네트워크 및 Azure Cosmos DB 계정에 있는 경우 다른 구독 가지도록 가상 네트워크가 있는 구독도 `Microsoft.DocumentDB` 리소스 공급자를 등록 합니다. 리소스 공급자를 등록 하려면 참조 [Azure 리소스 공급자 및 형식](../azure-resource-manager/resource-manager-supported-services.md) 문서. 

다음은 리소스 공급자를 사용 하 여 구독을 등록 하기 위한 지침입니다.

### <a name="configure-a-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>새로운 Azure 가상 네트워크 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

1. 설정 메뉴에서 **방화벽 및 Azure 가상 네트워크**를 선택하고, **선택된 네트워크**에서 액세스를 허용하도록 선택합니다.  

1. 새로운 Azure 가상 네트워크에 대한 액세스를 허용하려면 **가상 네트워크**에서 **새로운 가상 네트워크 추가**를 선택합니다.  

1. 새 가상 네트워크를 만드는 데 필요한 세부 정보를 제공한 다음, **만들기**를 선택합니다. “Microsoft.AzureCosmosDB”가 활성화된 서비스 엔드포인트와 함께 서브넷이 생성됩니다.

   ![새 가상 네트워크에 대한 가상 네트워크 및 서브넷을 선택합니다.](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Azure Cosmos DB 계정이 Azure Search와 같은 다른 Azure 서비스에서 사용되거나 Stream 분석 또는 Power BI에서 액세스되는 경우 **공용 Azure 데이터 센터 내에서 연결 허용**을 선택하여 액세스를 허용합니다.

포털에서 Azure Cosmos DB 메트릭에 대한 액세스 권한이 있는지 확인하려면 **Azure Portal에서 액세스 허용** 옵션을 사용하도록 설정해야 합니다. 이러한 옵션에 대한 자세한 내용은 [IP 방화벽 구성](how-to-configure-firewall.md) 문서를 참조하세요. 액세스를 사용하도록 설정한 후에 **저장**을 선택하여 설정을 저장합니다.

## <a id="remove-vnet-or-subnet"></a>가상 네트워크 또는 서브넷 제거 

1. **모든 리소스** 블레이드에서 서비스 엔드포인트를 할당한 Azure Cosmos DB 계정을 찾습니다.  

2. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택합니다.  

3. 가상 네트워크 또는 서브넷 규칙을 제거하려면 가상 네트워크 또는 서브넷 옆에 있는 **...** 를 선택하고, **제거**를 선택합니다.

   ![가상 네트워크 제거](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  **저장**을 선택하여 변경 내용을 적용합니다.

## <a id="configure-using-powershell"></a>Azure PowerShell을 사용하여 서비스 엔드포인트 구성

> [!NOTE]
> PowerShell 또는 Azure CLI를 사용하는 경우 매개 변수에서 추가해야 하는 것뿐만 아니라 IP 필터 및 가상 네트워크 ACL의 전체 목록을 지정해야 합니다.

Azure PowerShell을 사용하여 Azure Cosmos DB 계정에 서비스 엔드포인트를 구성하려면 다음 단계를 사용합니다.  

1. [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) 설치 및 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)  

1. 가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.  

   ```powershell
   $rgname = "<Resource group name>"
   $vnName = "<Virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
   ```

1. 가상 네트워크 정보를 가져옵니다.

   ```powershell
   $vnProp = Get-AzVirtualNetwork `
     -Name $vnName `
     -ResourceGroupName $rgName
   ```

1. 다음 cmdlet을 실행하여 Azure Cosmos DB 계정의 속성을 가져옵니다.  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 나중에 사용할 수 있도록 변수를 초기화합니다. 기존 계정 정의에서 모든 변수를 설정합니다.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $virtualNetworkRules = @(@{
      id = "$($vnProp.Id)/subnets/$sname";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 다음 cmdlet을 실행하여 새 구성으로 Azure Cosmos DB 계정 속성을 업데이트합니다. 

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName `
     -Properties $CosmosDBProperties
   ```

1. 다음 명령을 실행하여 이전 단계에서 구성한 가상 네트워크 서비스 엔드포인트로 Azure Cosmos DB 계정이 업데이트되었는지 확인합니다.

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Azure CLI를 사용하여 서비스 엔드포인트 구성 

1. 가상 네트워크의 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

1. 서브넷 ACL(액세스 제어 목록)로 기존 Azure Cosmos DB 계정을 업데이트합니다.

   ```azurecli-interactive

   name="<Azure Cosmos DB account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
    --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. 서브넷 ACL을 사용하여 새로운 Azure Cosmos DB 계정을 만듭니다.

   ```azurecli-interactive
   az cosmosdb create \
    --name $name \
    --kind GlobalDocumentDB \
    --resource-group $resourceGroupName \
    --max-interval 10 \
    --max-staleness-prefix 200 \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/default"
   ```

## <a id="migrate-from-firewall-to-vnet"></a>IP 방화벽 규칙에서 가상 네트워크 ACL로 마이그레이션 

IP 방화벽 규칙 대신 가상 네트워크 및 서브넷 기반 ACL을 사용하려는 경우 서브넷을 허용하는 기존 IP 방화벽 규칙을 사용하는 Azure Cosmos DB 계정에만 다음 단계를 사용합니다.

Azure Cosmos DB 계정에 대한 서비스 엔드포인트가 서브넷에 대해 설정되면 공용 IP 대신 가상 네트워크 및 서브넷 정보를 포함하는 원본을 사용하여 요청이 전송됩니다. 이러한 요청은 IP 필터와 일치하지 않습니다. 이 원본 전환은 서비스 엔드포인트를 사용하는 서브넷에서 액세스하는 모든 Azure Cosmos DB 계정에서 발생합니다. 가동 중지 시간을 방지하려면 다음 단계를 사용합니다.

1. 다음 cmdlet을 실행하여 Azure Cosmos DB 계정의 속성을 가져옵니다.

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 나중에 사용할 수 있도록 변수를 초기화합니다. 기존 계정 정의에서 모든 변수를 설정합니다. `ignoreMissingVNetServiceEndpoint` 플래그를 사용하여 서브넷에서 액세스하는 모든 Azure Cosmos DB 계정에 가상 네트워크 ACL을 추가합니다.

   ```powershell
   $locations = @()

   foreach ($readLocation in $cosmosDBConfiguration.Properties.readLocations) {
      $locations += , @{
         locationName     = $readLocation.locationName;
         failoverPriority = $readLocation.failoverPriority;
      }
   }

   $subnetID = "Subnet ARM URL" e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{
      id = $subnetID;
      ignoreMissingVNetServiceEndpoint = "True";
   })

   if ($cosmosDBConfiguration.Properties.isVirtualNetworkFilterEnabled) {
      $virtualNetworkRules = $cosmosDBConfiguration.Properties.virtualNetworkRules + $virtualNetworkRules
   }
   ```

1. 다음 cmdlet을 실행하여 새 구성으로 Azure Cosmos DB 계정 속성을 업데이트합니다.

   ```powershell
   $cosmosDBProperties = @{
      databaseAccountOfferType      = $cosmosDBConfiguration.Properties.databaseAccountOfferType;
      consistencyPolicy             = $cosmosDBConfiguration.Properties.consistencyPolicy;
      ipRangeFilter                 = $cosmosDBConfiguration.Properties.ipRangeFilter;
      locations                     = $locations;
      virtualNetworkRules           = $virtualNetworkRules;
      isVirtualNetworkFilterEnabled = $True;
   }

   Set-AzResource `
      -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
      -ApiVersion $apiVersion `
      -ResourceGroupName $rgName `
      -Name $acctName `
      -Properties $CosmosDBProperties
   ```

1. 서브넷에서 액세스하는 모든 Azure Cosmos DB 계정에 대해 1-3단계를 반복합니다.

1.  15분 동안 기다린 다음, 서브넷을 업데이트하여 서비스 엔드포인트를 사용하도록 설정합니다.

1.  가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

    ```powershell
    $rgname= "<Resource group name>"
    $vnName = "<virtual network name>"
    $sname = "<Subnet name>"
    $subnetPrefix = "<Subnet address range>"

    Get-AzVirtualNetwork `
       -ResourceGroupName $rgname `
       -Name $vnName | Set-AzVirtualNetworkSubnetConfig `
       -Name $sname `
       -AddressPrefix $subnetPrefix `
       -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzVirtualNetwork
    ```

1. 서브넷에 대한 IP 방화벽 규칙을 제거합니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](firewall-support.md) 문서를 참조하세요.
