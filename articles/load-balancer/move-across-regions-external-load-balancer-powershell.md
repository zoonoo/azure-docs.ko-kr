---
title: Azure PowerShell을 사용하여 Azure 외부 로드 밸러커를 다른 Azure 영역으로 이동
description: Azure 리소스 관리자 템플릿을 사용하여 Azure PowerShell을 사용하여 Azure 외부 로드 밸러커를 한 Azure 리전에서 다른 Azure 지역으로 이동합니다.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: a24eb4608e7630d5b613751fa2120361eccd7672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644820"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 외부 로드 밸러커를 다른 지역으로 이동

기존 외부 로드 밸러버를 한 리전에서 다른 리전으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성을 가진 외부 로드 밸런서를 만들 수 있습니다. 재해 복구 계획의 일환으로 외부 부하 분산기를 다른 지역으로 이동할 수도 있습니다.

Azure 외부 로드 밸로드는 한 리전에서 다른 리전으로 이동할 수 없습니다. 그러나 Azure 리소스 관리자 템플릿을 사용하여 외부 로드 밸런서의 기존 구성 및 공용 IP를 내보낼 수 있습니다.  그런 다음 로드 밸런서 및 공용 IP를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음 템플릿을 새 지역에 배포하여 다른 리전에서 리소스를 배치할 수 있습니다.  리소스 관리자 및 템플릿에 대한 자세한 내용은 [리소스 그룹 에서 템플릿으로 내보내기를 참조하세요.](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)


## <a name="prerequisites"></a>사전 요구 사항

- Azure 외부 로드 밸러버가 이동하려는 Azure 지역에 있는지 확인합니다.

- Azure 외부 로드 밸로드는 리전 간에 이동할 수 없습니다.  새 로드 밸러블러를 대상 지역의 리소스에 연결해야 합니다.

- 외부 로드 밸런서 구성을 내보내고 템플릿을 배포하여 다른 지역에서 외부 로드 밸런서를 만들려면 네트워크 기여자 역할 이상이 필요합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 로드 밸런서, 네트워크 보안 그룹, 공용 IP 및 가상 네트워크가 포함되나 이에 국한되지 않습니다.

- Azure 구독을 통해 사용되는 대상 지역에서 외부 로드 밸로드워서를 만들 수 있는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 로드 밸로드밸로드 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits) 참조


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용하여 이동을 위해 외부 로드 밸런서를 준비하고 Azure PowerShell을 사용하여 외부 로드 밸런서 구성을 대상 영역으로 이동하는 방법을 보여 주습니다.  이 프로세스의 일부로 외부 로드 밸런서의 공용 IP 구성이 포함되어야 하며 외부 로드 밸런서를 이동하기 전에 먼저 수행해야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>공용 IP 템플릿 내보내기 및 Azure PowerShell에서 배포

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 대상 지역으로 이동하려는 공용 IP의 리소스 ID를 가져오고 [Get-AzPublicIPAddress를](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)사용하여 변수에 배치합니다.

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 소스 공용 IP를 .json 파일로 [내보내서 내보내기-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행하는 디렉터리로:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일은 리소스를 내보낸 리소스 그룹의 이름을 따서 명명됩니다.  ** \<리소스 그룹 이름>.json이라는** 명령에서 내보낸 파일을 찾아 선택한 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 공용 IP 이름의 매개 변수를 편집하려면 소스 공용 IP 이름의 속성 **defaultValue를** 대상 공용 IP의 이름으로 변경하고 이름이 따옴표에 있는지 확인합니다.
    
    ```json
        {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "publicIPAddresses_myVM1pubIP_name": {
        "defaultValue": "<target-publicip-name>",
        "type": "String"
        }
    }

    ```

6. 공용 IP가 이동할 대상 지역을 편집하려면 리소스 에서 **위치** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2019-06-01",
            "name": "[parameters('publicIPAddresses_myPubIP_name')]",
            "location": "<target-region>",
            "sku": {
                "name": "Basic",
                "tier": "Regional"
            },
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
               }
               }
             ]             
    ```
  
7. 지역 위치 코드를 얻으려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation을](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 원하는 경우 템플릿의 다른 매개 변수를 변경할 수도 있으며 요구 사항에 따라 선택 사항입니다.

    * **Sku** - 리소스 그룹 이름 ** \<>.json** 파일에서 **sku** > **이름** 속성을 변경하여 구성에서 공용 IP의 sku를 표준또는 기본 또는 표준으로 변경할 수 있습니다.

         ```json
            "resources": [
                   {
                    "type": "Microsoft.Network/publicIPAddresses",
                    "apiVersion": "2019-06-01",
                    "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                    "location": "<target-region>",
                    "sku": {
                        "name": "Basic",
                        "tier": "Regional"
                    },
         ```

         기본 ip와 표준 sku 공용 IP의 차이점에 대한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)참조하십시오.

    * **공용 IP 할당 방법** 및 **유휴 시간 초과** - **publicIPAllocationMethod** 속성을 **동적에서** **정적** 또는 **정적에서** **동적으로**변경하여 템플릿에서 이러한 옵션을 모두 변경할 수 있습니다. 유휴 시간 초과는 **idleTimeoutInMinutes** 속성을 원하는 양으로 변경하여 변경할 수 있습니다.  기본값은 **4:**

         ```json
         "resources": [
                {
                "type": "Microsoft.Network/publicIPAddresses",
                "apiVersion": "2019-06-01",
                "name": "[parameters('publicIPAddresses_myPubIP_name')]",
                "location": "<target-region>",
                  "sku": {
                  "name": "Basic",
                  "tier": "Regional"
                 },
                "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "7549a8f1-80c2-481a-a073-018f5b0b69be",
                "ipAddress": "52.177.6.204",
                "publicIPAddressVersion": "IPv4",
                "publicIPAllocationMethod": "Dynamic",
                "idleTimeoutInMinutes": 4,
                "ipTags": []
                   }
                }            
         ```

        할당 방법 및 유휴 시간 초과 값에 대한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제를](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)참조하십시오.


9. 리소스 그룹 이름>.json 파일을 저장합니다. ** \<**

10. [New-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)사용하여 배포할 대상 공용 IP에 대한 리소스 그룹을 대상 지역에 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. 편집된 ** \<리소스 그룹 이름>.json** 파일을 [New-AzResourceGroupDeploy를](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)사용하여 이전 단계에서 만든 리소스 그룹에 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 대상 리전에서 생성된 리소스를 확인하려면 [Get-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [Get-AzPublicIPAddress를](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>외부 로드 밸러버 템플릿 내보내기 및 Azure PowerShell에서 배포

1. [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지침을 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 영역으로 이동하려는 외부 로드 밸러블러의 리소스 ID를 가져와 [Get-AzLoadBalancer를](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)사용하여 변수에 배치합니다.

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 소스 외부 로드 밸러블러 구성을 .json 파일로 [내보내서 Export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행하는 디렉터리로:
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 다운로드한 파일은 리소스를 내보낸 리소스 그룹의 이름을 따서 명명됩니다.  ** \<리소스 그룹 이름>.json이라는** 명령에서 내보낸 파일을 찾아 선택한 편집기에서 엽니다.
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 외부 로드 밸러블러 이름의 매개 변수를 편집하려면 소스 외부 로드 밸러터 이름의 속성 **defaultValue를** 대상 외부 로드 밸로터의 이름으로 변경하여 이름이 따옴표에 있는지 확인합니다.

    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
        "loadBalancers_myLoadbalancer_ext_name": {
        "defaultValue": "<target-external-lb-name>",
        "type": "String"
            },
        "publicIPAddresses_myPubIP_in_externalid": {
        "defaultValue": "<target-publicIP-resource-ID>",
        "type": "String"
            },

    ```

6.  위에 이동된 대상 공용 IP의 값을 편집하려면 먼저 리소스 ID를 얻은 다음 리소스 ** \<그룹 이름>.json** 파일에 복사하여 붙여넣아야 합니다.  ID를 얻으려면 [Get-AzPublicIPAddress를](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)사용합니다.

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    변수를 입력하고 enter를 적중하여 리소스 ID를 표시합니다.  ID 경로를 강조 표시하고 클립보드에 복사합니다.

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  리소스 그룹 이름>.json 파일에서 public IP 외부 ID에 대한 두 번째 매개 변수대신 **변수에서** **리소스 ID를** 붙여 넣습니다. ** \<**

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
            "loadBalancers_myLoadbalancer_ext_name": {
            "defaultValue": "<target-external-lb-name>",
            "type": "String"
            },
            "publicIPAddresses_myPubIP_in_externalid": {
            "defaultValue": "<target-publicIP-resource-ID>",
            "type": "String"
            },

    ```

8.  로드 밸런서에 대한 아웃바운드 NAT 및 아웃바운드 규칙을 구성한 경우 아웃바운드 공용 IP에 대한 외부 ID에 대한 세 번째 항목이 이 파일에 표시됩니다.  **대상 영역에서** 위의 단계를 반복하여 아웃바운드 공용 iP에 대한 ID를 가져오고 ** \<리소스 그룹 이름>.json** 파일에 해당 항목을 붙여 넣습니다.

    ```json
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "parameters": {
                "loadBalancers_myLoadbalancer_ext_name": {
                "defaultValue": "<target-external-lb-name>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_in_externalid": {
                "defaultValue": "<target-publicIP-resource-ID>",
                "type": "String"
            },
                "publicIPAddresses_myPubIP_out_externalid": {
                "defaultValue": "<target-publicIP-outbound-resource-ID>",
                "type": "String"
            }
        },
    ```

10. 외부 로드 밸러블러 구성이 이동될 대상 영역을 편집하려면 ** \<리소스 그룹 이름>.json** 파일의 **리소스** 아래의 **위치** 속성을 변경합니다.

    ```json
        "resources": [
            {
                "type": "Microsoft.Network/loadBalancers",
                "apiVersion": "2019-06-01",
                "name": "[parameters('loadBalancers_myLoadBalancer_name')]",
                "location": "<target-external-lb-region>",
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
            "location": "<target-external-lb-region>",
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

    * **아웃바운드 규칙** - ** \<리소스 그룹 이름>.json** 파일에서 **outboundRules** 속성을 편집하여 구성에서 아웃바운드 규칙을 추가하거나 제거할 수 있습니다.

        ```json
        "outboundRules": [
                    {
                        "name": "myOutboundRule",
                        "etag": "W/\"39e5e9cd-2d6d-491f-83cf-b37a259d86b6\"",
                        "properties": {
                            "provisioningState": "Succeeded",
                            "allocatedOutboundPorts": 10000,
                            "protocol": "All",
                            "enableTcpReset": false,
                            "idleTimeoutInMinutes": 15,
                            "backendAddressPool": {
                                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/backendAddressPools/myBEPoolOutbound')]"
                            },
                            "frontendIPConfigurations": [
                                {
                                    "id": "[concat(resourceId('Microsoft.Network/loadBalancers', parameters('loadBalancers_myLoadBalancer_name')), '/frontendIPConfigurations/myfrontendIPoutbound')]"
                                }
                            ]
                        }
                    }
                ]
        ```

         아웃바운드 규칙에 대한 자세한 내용은 [로드 밸록터 아웃바운드 규칙을](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) 참조하십시오.

13. 리소스 그룹 이름>.json 파일을 저장합니다. ** \<**
    
10. [New-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)사용하여 배포할 대상 외부 로드 밸러버에 대한 리소스 그룹을 대상 영역에 만들거나 리소스 그룹을 만듭니다. 위의 기존 리소스 그룹을 이 프로세스의 일부로 다시 사용할 수도 있습니다.
    
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

배포 후 대상에서 공용 IP 및 로드 밸런서를 다시 시작하거나 폐기하려는 경우 대상에서 생성된 리소스 그룹을 삭제하고 이동한 공용 IP 및 로드 밸런서가 삭제됩니다.  리소스 그룹을 제거하려면 [제거-AzResourceGroup을](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료하려면 소스 NSG 또는 리소스 그룹을 삭제하고 [제거-AzResourceGroup 또는](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) [제거-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) 및 [제거-AzLoadBalancer를](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 리전에서 다른 리전으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 간 리소스 이동 및 재해 복구에 대해 자세히 알아보려면 다음을 참조하십시오.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
