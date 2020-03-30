---
title: Azure PowerShell을 사용하여 Azure 내부 로드 밸러커를 다른 Azure 영역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure PowerShell을 사용하여 Azure 내부 로드 밸러터를 한 Azure 리전에서 다른 Azure 지역으로 이동
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: f8e431124155fe23853fe61e985fe4db522c3f77
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644276"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>PowerShell을 사용하여 Azure 내부 로드 밸러블러를 다른 지역으로 이동

기존 내부 로드 밸러버를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 가진 내부 부하 분산기를 만들 수 있습니다. 재해 복구 계획의 일부로 내부 부하 분산기를 다른 지역으로 이동할 수도 있습니다.

Azure 내부 로드 밸로드는 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 내부 부하 분산 검사기의 기존 구성 및 가상 네트워크를 내보낼 수 있습니다.  그런 다음 로드 밸러블러 및 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 영역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다.  리소스 관리자 및 템플릿에 대한 자세한 내용은 [리소스 그룹 에서 템플릿으로 내보내기를 참조하세요.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>사전 요구 사항

- Azure 내부 로드 밸러버가 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 내부 로드 밸로드는 리전 간에 이동할 수 없습니다.  새 로드 밸러블러를 대상 지역의 리소스에 연결해야 합니다.

- 내부 로드 밸런서 구성을 내보내고 템플릿을 배포하여 다른 지역에서 내부 로드 밸런서를 만들려면 네트워크 기여자 역할 이상이 필요합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸러버, 네트워크 보안 그룹, 가상 시스템 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 사용되는 대상 지역에서 내부 로드 밸로드워서를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 로드 밸로드밸로드 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) 참조


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용하여 이동을 위해 내부 로드 밸런서를 준비하고 Azure PowerShell을 사용하여 내부 로드 밸런서 구성을 대상 영역으로 이동하는 방법을 보여 주습니다.  이 프로세스의 일부로 내부 로드 밸러블러의 가상 네트워크 구성을 포함해야 하며 먼저 내부 로드 밸러블러를 이동하기 전에 수행해야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>Azure PowerShell에서 가상 네트워크 템플릿 을 내보내고 배포

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  대상 지역으로 이동하려는 가상 네트워크의 리소스 ID를 가져와 [Get-AzVirtualNetwork를](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)사용하여 변수에 배치합니다.

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 소스 가상 네트워크를 .json 파일로 [내보내서 내보내기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행하는 디렉터리로:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일은 리소스를 내보낸 리소스 그룹의 이름을 따서 명명됩니다.  ** \<리소스 그룹 이름>.json이라는** 명령에서 내보낸 파일을 찾아 선택한 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 가상 네트워크 이름의 매개 변수를 편집하려면 소스 가상 네트워크 이름의 속성 **defaultValue를** 대상 가상 네트워크의 이름으로 변경하고 이름이 따옴표에 있는지 확인합니다.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  VNET이 이동할 대상 영역을 편집하려면 리소스 에서 **위치** 속성을 변경합니다.

    ```json
    "resources": [
                {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region>",
                    "properties": {
                        "provisioningState": "Succeeded",
                        "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                        "addressSpace": {
                            "addressPrefixes": [
                                "10.0.0.0/16"
                            ]
                        },

    ```
  
7. 지역 위치 코드를 얻으려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation을](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  원하는 경우 ** \<리소스 그룹 이름>.json** 파일에서 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

    * **주소 공간** - **리소스** > **addressSpace** 섹션을 수정하고 ** \<리소스 그룹 이름>.json** 파일에서 **addressPrefixes** 속성을 변경하여 저장하기 전에 VNET의 주소 공간을 변경할 수 있습니다.

        ```json
                "resources": [
                    {
                    "type": "Microsoft.Network/virtualNetworks",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('virtualNetworks_myVNET1_name')]",
                    "location": "<target-region",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "resourceGuid": "6e2652be-35ac-4e68-8c70-621b9ec87dcb",
                    "addressSpace": {
                        "addressPrefixes": [
                        "10.0.0.0/16"
                        ]
                    },

        ```

    * **서브넷** - ** \<리소스 그룹 이름>.json** 파일의 **서브넷** 섹션을 수정하여 서브넷 이름과 서브넷 주소 공간을 변경하거나 추가할 수 있습니다. 서브넷의 이름은 **name** 속성을 변경하여 변경할 수 있습니다. 서브넷 주소 공간은 ** \<리소스 그룹 이름>.json** 파일에서 **addressPrefix** 속성을 변경하여 변경할 수 있습니다.

        ```json
                "subnets": [
                    {
                    "name": "subnet-1",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.0.0/24",
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    },
                    {
                    "name": "GatewaySubnet",
                    "etag": "W/\"d9f6e6d6-2c15-4f7c-b01f-bed40f748dea\"",
                    "properties": {
                    "provisioningState": "Succeeded",
                    "addressPrefix": "10.0.1.0/29",
                    "serviceEndpoints": [],
                    "delegations": [],
                    "privateEndpointNetworkPolicies": "Enabled",
                    "privateLinkServiceNetworkPolicies": "Enabled"
                    }
                    }

                ]
        ```

         리소스 그룹 이름>.json 파일에서 주소 접두사를 변경하려면 위에 나열된 섹션과 아래에 나열된 **형식** 섹션의 두 위치에서 편집해야 합니다. ** \<**  위의 것과 일치하도록 **addressPrefix** 속성을 변경합니다.

        ```json
         "type": "Microsoft.Network/virtualNetworks/subnets",
           "apiVersion": "2019-06-01",
           "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/GatewaySubnet')]",
              "dependsOn": [
                 "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                   ],
              "properties": {
                 "provisioningState": "Succeeded",
                 "addressPrefix": "10.0.1.0/29",
                 "serviceEndpoints": [],
                 "delegations": [],
                 "privateEndpointNetworkPolicies": "Enabled",
                 "privateLinkServiceNetworkPolicies": "Enabled"
                  }
                 },
                  {
                  "type": "Microsoft.Network/virtualNetworks/subnets",
                  "apiVersion": "2019-06-01",
                  "name": "[concat(parameters('virtualNetworks_myVNET1_name'), '/subnet-1')]",
                     "dependsOn": [
                        "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworks_myVNET1_name'))]"
                          ],
                     "properties": {
                        "provisioningState": "Succeeded",
                        "addressPrefix": "10.0.0.0/24",
                        "delegations": [],
                        "privateEndpointNetworkPolicies": "Enabled",
                        "privateLinkServiceNetworkPolicies": "Enabled"
                         }
                  }
         ]
        ```

9.  리소스 그룹 이름>.json 파일을 저장합니다. ** \<**

10. [New-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 사용하여 배포할 대상 VNET에 대한 리소스 그룹을 대상 지역에 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. 편집된 ** \<리소스 그룹 이름>.json** 파일을 [New-AzResourceGroupDeploy를](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)사용하여 이전 단계에서 만든 리소스 그룹에 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. 대상 지역에서 생성된 리소스를 확인하려면 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [Get-AzVirtualNetwork를](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>내부 로드 밸러블러 템플릿 내보내기 및 Azure PowerShell에서 배포

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 영역으로 이동하려는 내부 로드 밸러블러의 리소스 ID를 가져와 [Get-AzLoadBalancer를](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)사용하여 변수에 배치합니다.

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 소스 내부 로드 밸러블러 구성을 .json 파일로 내보내 [서 명령을](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)실행 하는 디렉터리
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. 다운로드한 파일은 리소스를 내보낸 리소스 그룹의 이름을 따서 명명됩니다.  ** \<리소스 그룹 이름>.json이라는** 명령에서 내보낸 파일을 찾아 선택한 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 내부 로드 밸러블러 이름의 매개 변수를 편집하려면 소스 내부 로드 밸러터 이름의 속성 **defaultValue를** 대상 내부 부하 밸로터의 이름으로 변경하여 이름이 따옴표에 있는지 확인합니다.

    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```
 
6. 위에 이동된 대상 가상 네트워크의 값을 편집하려면 먼저 리소스 ID를 얻은 다음 리소스 ** \<그룹 이름>.json** 파일에 복사하여 붙여야 합니다.  ID를 얻으려면 [Get-AzVirtualNetwork를](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)사용합니다.
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    변수를 입력하고 enter를 적중하여 리소스 ID를 표시합니다.  ID 경로를 강조 표시하고 클립보드에 복사합니다.

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  리소스 **그룹 이름>.json 파일에서 대상 가상 네트워크 ID에 대한 두 번째 매개 변수대신 변수에서 리소스 ID를 붙여 넣습니다. \<** **Resource ID** **defaultValue**
   
    ```json
         "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
         "contentVersion": "1.0.0.0",
         "parameters": {
            "loadBalancers_myLoadBalancer_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
             },
            "virtualNetworks_myVNET2_externalid": {
             "defaultValue": "<target-vnet-resource-ID>",
             "type": "String"
             }
    ```

8. 내부 로드 밸러블러 구성이 이동될 대상 영역을 편집하려면 ** \<리소스 그룹 이름>.json** 파일의 **리소스** 아래의 **위치** 속성을 변경합니다.

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-internal-lb-region>",
                "sku": {
                    "name": "Standard",
                    "tier": "Regional"
                },
    ```

11. 지역 위치 코드를 얻으려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation을](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 원하는 경우 템플릿의 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.
    
    * **Sku** - 리소스 그룹 이름 ** \<>.json** 파일에서 sku**이름** 속성을 변경하여 구성에서 기본 또는 기본으로 **구성의 sku를** > 변경할 수 있습니다.

        ```json
        "resources": [
        {
            "type": "Microsoft.Network/loadBalancers",
            "apiVersion": "2019-06-01",
            "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
            "location": "<target-internal-lb-region>",
            "sku": {
                "name": "Standard",
                "tier": "Regional"
            },
        ```
      기본 및 표준 sku 로드 밸로드 밸로드밸로드 의 차이점에 대한 자세한 내용은 [Azure 표준 로드 밸로드 밸로드서 개요를](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 참조하십시오.

    * **로드 균형 조정 규칙** - ** \<리소스 그룹 이름>.json** 파일의 **loadBalancingRules** 섹션에 항목을 추가하거나 제거하여 구성에서 로드 분산 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "loadBalancingRules": [
                    {
                        "name": "myInboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false,
                            "loadDistribution": "Default",
                            "disableOutboundSnat": true,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolInbound')]"
                            },
                            "probe": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/probes/myHTTPProbe')]"
                            }
                        }
                    }
                ]
        ```
       로드 분산 규칙에 대한 자세한 내용은 [Azure 로드 밸런서란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)

    * **프로브** - ** \<리소스 그룹 이름>.json** 파일의 프로브 섹션에 항목을 추가하거나 제거하여 구성에서 로드 밸러버에 대한 **프로브를** 추가하거나 제거할 수 있습니다.

        ```json
        "probes": [
                    {
                        "name": "myHTTPProbe",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "protocol": "Http",
                            "port": 80,
                            "requestPath": "/",
                            "intervalInSeconds": 15,
                            "numberOfProbes": 2
                        }
                    }
                ],
        ```
       Azure 로드 밸러서 상태 프로브에 대한 자세한 내용은 [로드 밸론저 상태 프로브를 참조하십시오.](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)

    * **인바운드 NAT 규칙** - ** \<리소스 그룹 이름>.json** 파일의 **인바운드NatRules** 섹션에 항목을 추가하거나 제거하여 로드 밸러터에 대한 인바운드 NAT 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "inboundNatRules": [
                    {
                        "name": "myInboundNATRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "frontendIPConfiguration": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                            },
                            "frontendPort": 4422,
                            "backendPort": 3389,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 4,
                            "protocol": "Tcp",
                            "enableTcpReset": false
                        }
                    }
                ]
        ```
        인바운드 NAT 규칙의 추가 또는 제거를 완료하려면 ** \<리소스 그룹 이름>.json** 파일 의 끝에 **형식** 속성으로 표시하거나 제거해야 합니다.

        ```json
        {
            "type": "Microsoft.Network/loadBalancers/inboundNatRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('loadBalancers_myLoadBalancer_name'), '/myInboundNATRule')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "frontendIPConfiguration": {
                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPinbound')]"
                },
                "frontendPort": 4422,
                "backendPort": 3389,
                "enableFloatingIP": false,
                "idleTimeoutInMinutes": 4,
                "protocol": "Tcp",
                "enableTcpReset": false
            }
        }
        ```
        인바운드 NAT 규칙에 대한 자세한 내용은 [Azure 로드 밸러저란 무엇입니까?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
    
13. 리소스 그룹 이름>.json 파일을 저장합니다. ** \<**
    
10. [New-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)사용하여 배포할 대상 내부 부하 분산검사기의 대상 영역에 리소스 그룹을 만들거나 리소스 그룹을 만듭니다. 위의 기존 리소스 그룹을 이 프로세스의 일부로 다시 사용할 수도 있습니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 편집된 ** \<리소스 그룹 이름>.json** 파일을 [New-AzResourceGroupDeploy를](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)사용하여 이전 단계에서 만든 리소스 그룹에 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 대상 리전에서 생성된 리소스를 확인하려면 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [Get-AzLoadBalancer를](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>취소 

배포 후 대상에서 가상 네트워크 및 로드 밸러터를 다시 시작하거나 폐기하려면 대상에서 생성된 리소스 그룹을 삭제하고 이동한 가상 네트워크 및 로드 밸러커가 삭제됩니다.  리소스 그룹을 제거하려면 [제거-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료하려면 소스 NSG 또는 리소스 그룹을 삭제하고 [제거-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 또는 [제거-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) 및 [제거-AzLoadBalancer를](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 내부 로드 밸런서를 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
