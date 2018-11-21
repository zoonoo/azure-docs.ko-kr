---
title: Azure Cosmos 계정에 대한 가상 네트워크 및 서브넷 기반 액세스를 구성하는 방법
description: 이 문서에서는 Azure Cosmos DB 가상 네트워크 서비스 엔드포인트에 필요한 단계를 설명합니다.
author: kanshiG
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: a7c2d1e41fa4ac26854e2e6ab57184cd6ed0bd0c
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/14/2018
ms.locfileid: "51633685"
---
# <a name="how-to-access-azure-cosmos-db-resources-from-virtual-networks"></a>가상 네트워크에서 Azure Cosmos DB 리소스에 액세스하는 방법

Azure Virtual Network의 특정 서브넷에서만 액세스할 수 있도록 Azure CosmosDB 계정을 구성할 수 있습니다. VNET(가상 네트워크)의 서브넷에서 연결을 사용하여 Azure Cosmos 계정에 액세스하는 것을 제한하려면 두 단계가 필요합니다.
 
1. 서브넷 및 VNET ID를 Azure Cosmos DB에 보내도록 서브넷을 설정합니다. 특정 서브넷에서 Azure Cosmos DB에 대한 서비스 엔드포인트를 사용하도록 설정하면 됩니다.

1. 서브넷을 계정에 액세스할 수 있는 소스로 지정하는 규칙을 Azure Cosmos 계정에 추가합니다.

> [!NOTE]
> Azure Cosmos 계정에 대한 서비스 엔드포인트가 서브넷에서 사용하도록 설정되면 Azure Cosmos DB에 도달하는 트래픽의 원본이 공용 IP에서 VNET 및 서브넷으로 전환됩니다. 트래픽 전환은 이 서브넷에서 액세스하는 모든 Azure Cosmos 계정에 적용됩니다. Azure Cosmos 계정에 이 서브넷을 허용하는 IP 기반 방화벽이 있는 경우 서브넷을 사용하는 서비스의 요청이 더 이상 IP 방화벽 규칙과 일치하지 않아 거부됩니다. 자세히 알아보려면 이 문서의 [IP 방화벽 규칙에서 VNET 액세스 제어 목록으로 마이그레이션](#migrate-from-firewall-to-vnet) 섹션에 설명된 단계를 참조하세요. 

다음 섹션에서는 Azure Cosmos DB 계정에 대한 VNET 서비스 엔드포인트를 구성하는 방법을 설명합니다.

## <a id="configure-using-portal"></a>Azure Portal을 사용하여 서비스 엔드포인트 구성

### <a name="configure-service-endpoint-for-an-existing-azure-virtual-network-and-subnet"></a>Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos 계정을 찾습니다.

1. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택하고 **선택된 네트워크**에서 액세스 허용을 선택합니다.

1. 기존 가상 네트워크의 서브넷에 대한 액세스를 허용하려면 **가상 네트워크**에서 **기존 Azure Virtual Network 추가**를 선택합니다.

1. Azure Virtual Network를 추가할 **구독**을 선택합니다. Azure Cosmos 계정에 대한 액세스를 제공하려는 Azure **Virtual Network** 및 **서브넷**을 차례로 선택합니다. 다음으로 **사용**을 선택하여 선택한 네트워크가 “Microsoft.AzureCosmosDB”에 대한 서비스 엔드포인트를 사용하도록 설정합니다. 완료되면 **추가**를 선택합니다. 

   ![Virtual Network 및 서브넷 선택](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet.png)


1. Azure Cosmos 계정이 가상 네트워크에서 액세스할 수 있도록 활성화된 후에는 선택한 이 서브넷의 트래픽만 허용합니다. 추가한 가상 네트워크 및 서브넷은 다음 스크린샷에 표시된 것처럼 표시됩니다.

   ![성공적으로 구성된 가상 네트워크 및 서브넷](./media/how-to-configure-vnet-service-endpoint/vnet-and-subnet-configured-successfully.png)

> [!NOTE]
> 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하려면 다음 구독 권한이 필요합니다.
  * VNET을 사용한 구독: 네트워크 기여자
  * Azure Cosmos 계정을 사용한 구독: DocumentDB 계정 기여자

### <a name="configure-service-endpoint-for-a-new-azure-virtual-network-and-subnet"></a>새로운 Azure Virtual Network 및 서브넷에 대한 서비스 엔드포인트 구성

1. **모든 리소스** 블레이드에서 보호할 Azure Cosmos DB 계정을 찾습니다.  

2. 가상 네트워크 서비스 엔드포인트를 사용하도록 설정하기 전에 나중에 사용할 수 있도록 Azure Cosmos DB 계정과 연결된 IP 방화벽 정보를 복사합니다. 서비스 엔드포인트를 구성한 후 IP 방화벽을 다시 설정할 수 있습니다.  

3. 설정 메뉴에서 **방화벽 및 Azure Virtual Network**를 선택하고 **선택된 네트워크**에서 액세스 허용을 선택합니다.  

4. 새로운 Azure Virtual Network에 대한 액세스를 허용하려면 가상 네트워크에서 **새로운 가상 네트워크 추가**를 선택합니다.  

5. 새 가상 네트워크를 만드는 데 필요한 세부 정보를 제공한 다음, 만들기를 선택합니다. “Microsoft.AzureCosmosDB”가 활성화된 서비스 엔드포인트와 함께 서브넷이 생성됩니다.

   ![새 가상 네트워크에 대한 가상 네트워크 및 서브넷을 선택합니다.](./media/how-to-configure-vnet-service-endpoint/choose-subnet-and-vnet-new-vnet.png)

Azure Search와 같은 다른 Azure 서비스에서 Azure Cosmos 계정을 사용하거나 Stream Analytics 또는 Power BI에서 액세스하는 경우 Azure Services에 액세스 허용을 선택하여 액세스를 허용합니다.

포털에서 Azure Cosmos DB 메트릭에 대한 액세스 권한이 있는지 확인하려면 **Azure Portal에 액세스 허용** 옵션을 사용하도록 설정해야 합니다. 이러한 옵션에 대한 자세한 정보는 [IP 방화벽](how-to-configure-firewall.md) 구성 문서의 Azure Portal에서 요청 및 Azure PaaS 서비스에서 요청 섹션을 참조하세요. 액세스를 선택한 후 **저장**을 선택하여 설정을 저장합니다.

## <a id="remove-vnet-or-subnet"></a>가상 네트워크 또는 서브넷 제거 

1. **모든 리소스** 블레이드에서 서비스 엔드포인트를 할당한 Azure Cosmos DB 계정을 찾습니다.  

2. 설정 메뉴에서 **방화벽 및 가상 네트워크**를 선택합니다.  

3. 가상 네트워크 또는 서브넷 규칙을 제거하려면 가상 네트워크 또는 서브넷 옆에 있는 “...”를 선택하고 **제거**를 선택합니다.

   ![가상 네트워크 제거](./media/how-to-configure-vnet-service-endpoint/remove-a-vnet.png)

4.  **저장**을 클릭하여 변경 내용을 적용합니다.

## <a id="configure-using-powershell"></a>Azure PowerShell을 사용하여 서비스 엔드포인트 구성 

Azure PowerShell을 사용하여 Azure Cosmos DB 계정에 서비스 엔드포인트를 구성하려면 다음 단계를 사용합니다.  

1. 최신 [Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)을 설치하고 [로그인](https://docs.microsoft.com/powershell/azure/authenticate-azureps)합니다.  

1. 가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.  

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname  `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. 가상 네트워크 및 서브넷에서 서비스 엔드포인트가 사용자 계정에 대해 사용하도록 설정되었는지 확인하여 Azure Cosmos 계정에서 ACL의 활성화를 준비합니다.

   ```powershell
   $vnProp = Get-AzureRmVirtualNetwork `
     -Name $vnName  -ResourceGroupName $rgName
   ```

1. 다음 cmdlet을 실행하여 Azure Cosmos DB 계정의 속성을 가져옵니다.  

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Azure Cosmos DB account name>"

   $cosmosDBConfiguration = Get-AzureRmResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 나중에 사용할 수 있도록 변수를 초기화합니다. 기존 계정 정의에서 모든 변수를 설정하고, 여러 위치가 있는 경우 배열의 일환으로 추가해야 합니다. 이 단계에서는 또한 “accountVNETFilterEnabled” 변수를 “True”로 설정하여 가상 네트워크 서비스 엔드포인트를 구성합니다. 이 값은 나중에 “isVirtualNetworkFilterEnabled” 매개 변수에 할당됩니다. 

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
                 "failoverPriority"=0}, 
               @{"locationName"="<Read location>"; 
                  "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName

   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy

   $accountVNETFilterEnabled = $True
   $subnetID = $vnProp.Id+"/subnets/" + $sname  
   $virtualNetworkRules = @(@{"id"=$subnetID})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. 다음 cmdlet을 실행하여 새 구성으로 Azure Cosmos DB 계정 속성을 업데이트합니다. 

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName -Properties $CosmosDBProperties
   ```

1. 다음 명령을 실행하여 이전 단계에서 구성한 가상 네트워크 서비스 엔드포인트로 Azure Cosmos DB 계정이 업데이트되었는지 확인합니다.

   ```powershell
   $UpdatedcosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName

   $UpdatedcosmosDBConfiguration.Properties
   ```

## <a id="configure-using-cli"></a>Azure CLI를 사용하여 서비스 엔드포인트 구성 

1. 가상 네트워크의 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

1. 서브넷 ACL을 사용하여 기존 Azure Cosmos 계정 업데이트

   ```azurecli-interactive

   name="<Azure Cosmos account name>"
   resourceGroupName="<Resource group name>"

   az cosmosdb update \
      --name $name \
    --resource-group $resourceGroupName \
    --enable-virtual-network true \
    --virtual-network-rules "/subscriptions/testsub/resourceGroups/testRG/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/frontend"
   ```

1. 서브넷 ACL을 사용하여 새로운 Azure Cosmos 계정 만들기

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

## <a id="migrate-from-firewall-to-vnet"></a>IP 방화벽 규칙에서 VNET ACL로 마이그레이션 

다음 단계는 서브넷을 허용하는 기존 IP 방화벽 규칙을 사용하는 Azure Cosmos 계정 및 IP 방화벽 규칙 대신 VNET 및 서브넷 기반 ACL을 사용하려는 경우에만 필요합니다.

Azure Cosmos 계정에 대한 서비스 엔드포인트가 서브넷에 대해 사용하도록 설정되면 공용 IP 대신 VNET 및 서브넷 정보를 포함하는 원본을 사용하여 요청이 전송됩니다. 따라서 그러한 요청은 IP 필터와 일치하지 않습니다. 이 원본 스위치는 서비스 엔드포인트가 사용하도록 설정된 서브넷에서 액세스하는 모든 Azure Cosmos 계정에서 발생합니다. 가동 중지 시간을 방지하려면 다음 단계를 사용합니다.

1. 다음 cmdlet을 실행하여 Azure Cosmos 계정의 속성을 가져옵니다.

   ```powershell
   $apiVersion = "2015-04-08"
   $acctName = "<Cosmos account name>"

   $cosmosDBConfiguration = Get-AzureRmResource `
     -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
     -ApiVersion $apiVersion `
     -ResourceGroupName $rgName `
     -Name $acctName
   ```

1. 나중에 사용할 수 있도록 변수를 초기화합니다. 기존 계정 정의에서 모든 변수를 설정합니다. `ignoreMissingVNetServiceEndpoint` 플래그를 사용하여 서브넷에서 액세스하는 모든 Azure Cosmos 계정에 VNET ACL을 추가합니다.  

   여러 위치가 있는 경우 배열의 일환으로 추가해야 합니다. 이 단계에서는 또한 “accountVNETFilterEnabled” 변수를 “True”로 설정하여 가상 네트워크 서비스 엔드포인트를 구성합니다. 이 값은 나중에 “isVirtualNetworkFilterEnabled” 매개 변수에 할당됩니다.

   ```powershell
   $locations = @(@{})

   <# If you have read regions in addition to a write region, use the following code to set the $locations variable instead.

   $locations = @(@{"locationName"="<Write location>"; 
              "failoverPriority"=0}, 
            @{"locationName"="<Read location>"; 
               "failoverPriority"=1}) #>

   $consistencyPolicy = @{}
   $cosmosDBProperties = @{}

   $locations[0]['failoverPriority'] = $cosmosDBConfiguration.Properties.failoverPolicies.failoverPriority
   $locations[0]['locationName'] = $cosmosDBConfiguration.Properties.failoverPolicies.locationName
   $consistencyPolicy = $cosmosDBConfiguration.Properties.consistencyPolicy
   $accountVNETFilterEnabled = $True
   $subnetID = “Subnet ARM URL” e.g "/subscriptions/f7ddba26-ab7b-4a36-a2fa-7d01778da30b/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/subnet1"

   $virtualNetworkRules = @(@{"id"=$subnetID, "ignoreMissingVNetServiceEndpoint"="True"})
   $databaseAccountOfferType = $cosmosDBConfiguration.Properties.databaseAccountOfferType
   ```

1. 다음 cmdlet을 실행하여 새 구성으로 Azure Cosmos 계정 속성을 업데이트합니다.

   ```powershell
   $cosmosDBProperties['databaseAccountOfferType'] = $databaseAccountOfferType
   $cosmosDBProperties['locations'] = $locations
   $cosmosDBProperties['consistencyPolicy'] = $consistencyPolicy
   $cosmosDBProperties['virtualNetworkRules'] = $virtualNetworkRules
   $cosmosDBProperties['isVirtualNetworkFilterEnabled'] = $accountVNETFilterEnabled

   Set-AzureRmResource `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ApiVersion $apiVersion `
    -ResourceGroupName $rgName `
    -Name $acctName -Properties $CosmosDBProperties
   ```

1. 서브넷에서 액세스하는 모든 Azure Cosmos 계정에 대해 1-3단계를 반복합니다.

1.  15분 동안 기다린 다음, 서브넷을 업데이트하여 서비스 엔드포인트를 사용하도록 설정합니다.

1.  가상 네트워크의 기존 서브넷에 대한 서비스 엔드포인트를 사용하도록 설정합니다.

   ```powershell
   $rgname= "<Resource group name>"
   $vnName = "<virtual network name>"
   $sname = "<Subnet name>"
   $subnetPrefix = "<Subnet address range>"

   Get-AzureRmVirtualNetwork `
    -ResourceGroupName $rgname `
    -Name $vnName | Set-AzureRmVirtualNetworkSubnetConfig `
    -Name $sname `
    -AddressPrefix $subnetPrefix `
    -ServiceEndpoint "Microsoft.AzureCosmosDB" | Set-AzureRmVirtualNetwork
   ```

1. 서브넷에 대한 IP 방화벽 규칙을 제거합니다.

## <a name="next-steps"></a>다음 단계

* Azure Cosmos DB에 대한 방화벽을 구성하려면 [방화벽 지원](firewall-support.md) 문서를 참조하세요.

