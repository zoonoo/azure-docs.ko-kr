---
title: Azure PowerShell를 사용 하 여 Azure 내부 Load Balancer을 다른 Azure 지역으로 이동
description: Azure PowerShell를 사용 하 여 azure 지역 간에 Azure 내부 Load Balancer를 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: load-balancer
ms.topic: how-to
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: 73a9356de555e33996b92f05c3bbbabb651f1c9f
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694799"
---
# <a name="move-azure-internal-load-balancer-to-another-region-using-powershell"></a>PowerShell을 사용 하 여 Azure 내부 Load Balancer을 다른 지역으로 이동

기존 내부 부하 분산 장치를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트에 대해 동일한 구성을 사용 하 여 내부 부하 분산 장치를 만들 수 있습니다. 재해 복구 계획의 일부로 내부 부하 분산 장치를 다른 지역으로 이동할 수도 있습니다.

Azure 내부 부하 분산 장치는 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 내부 부하 분산 장치의 기존 구성과 가상 네트워크를 내보낼 수 있습니다.  그런 다음 부하 분산 장치 및 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조하세요.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 내부 부하 분산 장치가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 내부 부하 분산 장치는 지역 간에 이동할 수 없습니다.  새 부하 분산 장치를 대상 지역의 리소스에 연결 해야 합니다.

- 내부 부하 분산 장치 구성을 내보내고 템플릿을 배포 하 여 다른 지역에 내부 부하 분산 장치를 만들려면 네트워크 참가자 역할이 이상 필요 합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, 네트워크 보안 그룹, 가상 머신 및 가상 네트워크만 포함 되지만이에 국한 되지는 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에 내부 부하 분산 장치를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 부하 분산 장치 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건을](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 참조 하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 내부 부하 분산 장치를 준비 하 고 Azure PowerShell를 사용 하 여 내부 부하 분산 장치 구성을 대상 지역으로 이동 하는 방법을 보여 줍니다.  이 프로세스의 일부로 내부 부하 분산 장치의 가상 네트워크 구성이 포함 되어야 하 고 내부 부하 분산 장치를 이동 하기 전에 먼저 수행 되어야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-template-and-deploy-from-azure-powershell"></a>가상 네트워크 템플릿 내보내기 및 Azure PowerShell에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2.  대상 지역으로 이동 하려는 가상 네트워크의 리소스 ID를 가져와 [AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0) 명령을 실행하는 디렉터리의 .json 파일로 원본 가상 네트워크를 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 가상 네트워크 이름의 매개 변수를 편집 하려면 원본 가상 네트워크 이름의 속성 **defaultValue** 를 대상 가상 네트워크의 이름으로 변경 하 고 이름이 따옴표 안에 있는지 확인 합니다.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

6.  VNET이 이동 될 대상 지역을 편집 하려면 리소스에서 **위치** 속성을 변경 합니다.

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
  
7. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  을 선택 하는 경우에는 json 파일의 다른 매개 변수도 변경할 수 있으며 요구 사항에 따라 선택적으로 선택 해야 **\<resource-group-name> 합니다** .

    * **주소 공간** - **리소스**  >  **addressSpace** 섹션을 수정 하 고 **\<resource-group-name> .** i n i 파일에서 **addressPrefixes** 속성을 변경 하 여 저장 하기 전에 VNET의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷** - **\<resource-group-name> json** 파일의 **서브넷** 섹션을 수정 하 여 서브넷 이름 및 서브넷 주소 공간을 변경 하거나에 추가할 수 있습니다. **이름** 속성을 변경 하 여 서브넷의 이름을 변경할 수 있습니다. **\<resource-group-name> Json** 파일에서 **addressPrefix** 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 있습니다.

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

         **\<resource-group-name> Json** 파일에서 주소 접두사를 변경 하려면 위에 나열 된 섹션 및 아래에 나열 된 **형식** 섹션에서 두 위치를 편집 해야 합니다.  **AddressPrefix** 속성을 위와 일치 하도록 변경 합니다.

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

9.  **\<resource-group-name>.json** 파일을 저장합니다.

10. [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 를 사용 하 여 배포할 대상 VNET의 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 **\<resource-group-name>.json** 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```
12. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```
### <a name="export-the-internal-load-balancer-template-and-deploy-from-azure-powershell"></a>내부 부하 분산 장치 템플릿을 내보내고 Azure PowerShell에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동 하려는 내부 부하 분산 장치의 리소스 ID를 가져와 [AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceIntLBID = (Get-AzLoadBalancer -Name <source-internal-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 내부 부하 분산 장치 구성을 [AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceIntLBID -IncludeParameterDefaultValue
   ```
4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 내부 부하 분산 장치 이름의 매개 변수를 편집 하려면 원본 내부 부하 분산 장치 이름의 속성 **defaultValue** 를 대상 내부 부하 분산 장치의 이름으로 변경 하 고 이름이 따옴표 안에 있는지 확인 합니다.

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
 
6. 위에서 이동한 대상 가상 네트워크의 값을 편집 하려면 먼저 리소스 ID를 가져온 다음이를 복사 하 여 **\<resource-group-name> json** 파일에 붙여넣어야 합니다.  ID를 가져오려면 [AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)을 사용 합니다.
   
   ```azurepowershell-interactive
    $targetVNETID = (Get-AzVirtualNetwork -Name <target-vnet-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    변수를 입력 하 고 enter 키를 눌러 리소스 ID를 표시 합니다.  ID 경로를 강조 표시 하 고 클립보드에 복사 합니다.

    ```powershell
    PS C:\> $targetVNETID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupVNET-Move/providers/Microsoft.Network/virtualNetworks/myVNET2-Move
    ```

7.  **\<resource-group-name> Json** 파일에서 대상 가상 네트워크 ID에 대 한 두 번째 매개 변수에서 **DefaultValue** 대신 변수의 **리소스 ID** 를 붙여넣고 경로를 따옴표로 묶어야 합니다.
   
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

8. 내부 부하 분산 장치 구성을 이동할 대상 지역을 편집 하려면 다음을 수행 **\<resource-group-name> 합니다. json** 파일의 **리소스** 에서 **위치** 속성을 변경 합니다.

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

11. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 또한 선택하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.
    
    * **Sku** - **sku**  >  **\<resource-group-name> json** 파일의 sku **이름** 속성을 변경 하 여 구성의 내부 부하 분산 장치에 대 한 sku를 표준에서 기본으로 또는 기본에서 표준으로 변경할 수 있습니다.

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
      기본 및 표준 sku 부하 분산 장치 간의 차이점에 대 한 자세한 내용은 [Azure 표준 Load Balancer 개요](./load-balancer-overview.md) 를 참조 하세요.

    * **부하 분산 규칙** - **\<resource-group-name> json** 파일의 **loadBalancingRules** 섹션에 항목을 추가 하거나 제거 하 여 구성에서 부하 분산 규칙을 추가 하거나 제거할 수 있습니다.

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
       부하 분산 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](./load-balancer-overview.md) 을 참조 하세요.

    * **프로브** - **\<resource-group-name> json** 파일의 **프로브** 섹션에 항목을 추가 하거나 제거 하 여 구성의 부하 분산 장치에 대 한 프로브를 추가 하거나 제거할 수 있습니다.

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
       Azure Load Balancer 상태 프로브에 대 한 자세한 내용은 [상태 프로브 Load Balancer](./load-balancer-custom-probe-overview.md) 를 참조 하세요.

    * **인바운드 nat 규칙** - **\<resource-group-name> json** 파일의 **loadbalancer.inboundnatrules** 섹션에 항목을 추가 하거나 제거 하 여 부하 분산 장치에 대 한 인바운드 nat 규칙을 추가 하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료 하려면 **\<resource-group-name> json** 파일의 끝에 **유형** 속성으로 규칙을 설치 하거나 제거 해야 합니다.

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
        인바운드 NAT 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](./load-balancer-overview.md) 을 참조 하세요.
    
13. **\<resource-group-name>.json** 파일을 저장합니다.
    
10. [AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 배포할 대상 내부 부하 분산 장치의 대상 지역에 또는 리소스 그룹을 만듭니다. 위의 기존 리소스 그룹을이 프로세스의 일부로 재사용할 수도 있습니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 **\<resource-group-name>.json** 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzLoadBalancer](/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>취소 

배포 후에는 대상에서 가상 네트워크 및 부하 분산 장치를 시작 하거나 삭제 하려는 경우 대상에 생성 된 리소스 그룹을 삭제 하 고 이동한 가상 네트워크 및 부하 분산 장치를 삭제 합니다.  리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료 하려면 [AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 또는 [AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0) 및 [AzLoadBalancer](/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) 를 사용 하 여 nsg 또는 리소스 그룹을 삭제 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzVirtualNetwork -Name <virtual-network-name> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 한 지역에서 다른 지역으로 Azure 내부 부하 분산 장치를 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)