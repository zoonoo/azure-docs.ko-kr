---
title: Azure PowerShell를 사용 하 여 Azure 외부 Load Balancer를 다른 Azure 지역으로 이동
description: Azure PowerShell를 사용 하 여 azure 지역 간에 Azure 외부 Load Balancer를 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: load-balancer
ms.topic: article
ms.date: 09/17/2019
ms.author: allensu
ms.openlocfilehash: d8dfbf3f86a2233571a99c4ad832ef7bd3c3ed48
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077582"
---
# <a name="move-azure-external-load-balancer-to-another-region-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 외부 Load Balancer를 다른 지역으로 이동

기존 외부 부하 분산 장치를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트에 대해 동일한 구성으로 외부 부하 분산 장치를 만들 수 있습니다. 재해 복구 계획의 일부로 외부 부하 분산 장치를 다른 지역으로 이동할 수도 있습니다.

Azure 외부 부하 분산 장치는 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 외부 부하 분산 장치의 기존 구성 및 공용 IP를 내보낼 수 있습니다.  그런 다음 부하 분산 장치 및 공용 IP를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다.  리소스 관리자 및 템플릿에 대 한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) 를 참조 하세요.


## <a name="prerequisites"></a>필수 구성 요소

- Azure 외부 부하 분산 장치가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 외부 부하 분산 장치는 지역 간에 이동할 수 없습니다.  새 부하 분산 장치를 대상 지역의 리소스에 연결 해야 합니다.

- 외부 부하 분산 장치 구성을 내보내고 템플릿을 배포 하 여 다른 지역에 외부 부하 분산 장치를 만들려면 네트워크 참가자 역할이 이상 필요 합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, 네트워크 보안 그룹, 공용 Ip, 가상 네트워크 등이 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에 외부 부하 분산 장치를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 부하 분산 장치 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 외부 부하 분산 장치를 준비 하 고 Azure PowerShell를 사용 하 여 외부 부하 분산 장치 구성을 대상 지역으로 이동 하는 방법을 보여 줍니다.  이 프로세스의 일부로 외부 부하 분산 장치의 공용 IP 구성이 포함 되어야 하며, 외부 부하 분산 장치를 이동 하기 전에 먼저 작업을 수행 해야 합니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-public-ip-template-and-deploy-from-azure-powershell"></a>공용 IP 템플릿을 내보내고 Azure PowerShell에서 배포

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
2. 대상 지역으로 이동 하려는 공용 IP의 리소스 ID를 가져와 [AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 공용 IP를 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리로 json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드 한 파일은 리소스를 내보낸 리소스 그룹의 이름으로 지정 됩니다.  **리소스 그룹-이름 >. json 명령에서 내보낸 파일을 찾아 원하는 편집기에서 엽니다. \<**
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 공용 IP 이름의 매개 변수를 편집 하려면 원본 공용 ip 이름의 속성 **defaultValue** 를 대상 공용 ip의 이름으로 변경 하 고 이름이 따옴표 안에 있는지 확인 합니다.
    
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

6. 공용 IP가 이동 될 대상 영역을 편집 하려면 리소스에서 **위치** 속성을 변경 합니다.

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
  
7. 지역 위치 코드를 가져오려면 다음 명령을 실행 하 여 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 또한 선택 하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택 사항입니다.

    * **Sku** - >  **리소스 그룹 이름 >. json 파일에서 Sku 이름 속성을 변경 하 여 구성의 공용 IP sku를 standard에서 basic 또는 basic으로 \<** 변경할 수 있습니다.

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

         기본 및 표준 sku 공용 ip 간의 차이점에 대 한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)를 참조 하세요.

    * **공용 IP 할당 방법** 및 **유휴 시간 제한** - **PublicIPAllocationMethod** 속성을 **동적** 에서 **정적** 으로 또는 **정적** 에서 동적으로 변경 하 여 템플릿에서 이러한 두 옵션을 모두 변경할 수 있습니다. **IdleTimeoutInMinutes** 속성을 원하는 양만큼 변경 하 여 유휴 시간 제한을 변경할 수 있습니다.  기본값은 **4**입니다.

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

        할당 방법 및 유휴 시간 제한 값에 대 한 자세한 내용은 [공용 IP 주소 만들기, 변경 또는 삭제](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)를 참조 하세요.


9. **\<리소스 그룹 이름 > json** 파일을 저장 합니다.

10. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 배포할 대상 공용 IP에 대 한 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용 하 여 이전 단계에서 만든 리소스 그룹에 편집  **\<된 리소스 그룹 이름 > json** 파일을 배포 합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzPublicIPAddress -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

### <a name="export-the-external-load-balancer-template-and-deploy-from-azure-powershell"></a>외부 부하 분산 장치 템플릿을 내보내고 Azure PowerShell에서 배포

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동 하려는 외부 부하 분산 장치의 리소스 ID를 가져와 [AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceExtLBID = (Get-AzLoadBalancer -Name <source-external-lb-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 외부 부하 분산 장치 구성을 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리로 json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceExtLBID -IncludeParameterDefaultValue
   ```
4. 다운로드 한 파일은 리소스를 내보낸 리소스 그룹의 이름으로 지정 됩니다.  **리소스 그룹-이름 >. json 명령에서 내보낸 파일을 찾아 원하는 편집기에서 엽니다. \<**
   
   ```azurepowershell
   notepad.exe <source-resource-group-name>.json
   ```

5. 외부 부하 분산 장치 이름의 매개 변수를 편집 하려면 원본 외부 부하 분산 장치 이름의 속성 **defaultValue** 를 대상 외부 부하 분산 장치의 이름으로 변경 하 고 이름이 따옴표 안에 있는지 확인 합니다.

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

6.  위에서 이동한 대상 공용 IP의 값을 편집 하려면 먼저 리소스 ID를 가져온 다음  **\<리소스 그룹 이름 > json** 파일에 복사 하 여 붙여넣어야 합니다.  ID를 가져오려면 [AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)을 사용 합니다.

    ```azurepowershell-interactive
    $targetPubIPID = (Get-AzPublicIPaddress -Name <target-public-ip-name> -ResourceGroupName <target-resource-group-name>).Id
    ```
    변수를 입력 하 고 enter 키를 눌러 리소스 ID를 표시 합니다.  ID 경로를 강조 표시 하 고 클립보드에 복사 합니다.

    ```powershell
    PS C:\> $targetPubIPID
    /subscriptions/7668d659-17fc-4ffd-85ba-9de61fe977e8/resourceGroups/myResourceGroupLB-Move/providers/Microsoft.Network/publicIPAddresses/myPubIP-in-move
    ```

7.  리소스-그룹 **이름 > json 파일에서 공용 IP 외부 ID에 대 한 두 번째 매개 변수에 defaultValue 대신 리소스 ID를 붙여 넣어 경로를 따옴표로 묶어야 \<** 합니다.

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

8.  부하 분산 장치에 대 한 아웃 바운드 NAT 및 아웃 바운드 규칙을 구성한 경우이 파일에 아웃 바운드 공용 IP의 외부 ID에 대 한 세 번째 항목이 표시 됩니다.  **대상 영역** 에서 위의 단계를 반복 하 여 아웃 바운드 공용 iP에 대 한 ID를 확인 하 고 해당 항목을  **\<리소스 그룹 이름 > json** 파일에 붙여넣습니다.

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

10. 외부 부하 분산 장치 구성을 이동할 대상 지역을 편집 하려면  **\<리소스 그룹 이름 >.** i n t 파일의 **리소스** 에서 **위치** 속성을 변경 합니다.

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

11. 지역 위치 코드를 가져오려면 다음 명령을 실행 하 여 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
12. 또한 선택 하는 경우 템플릿의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택 사항입니다.
    
    * **Sku** - >  **리소스 그룹 이름 >의 sku 이름 속성을 변경 하 여 구성의 외부 부하 분산 장치에 대 한 sku를 standard에서 basic 또는 basic에서 standard로 변경할 수 있습니다. \<** 파일:

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
      기본 및 표준 sku 부하 분산 장치 간의 차이점에 대 한 자세한 내용은 [Azure 표준 Load Balancer 개요](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview) 를 참조 하세요.

    * **부하 분산 규칙** -loadBalancingRules 파일  **\<>** 의 섹션에 항목을 추가 하거나 제거 하 여 구성에서 부하 분산 규칙을 추가 하거나 제거할 수 있습니다.

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
       부하 분산 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 을 참조 하세요.

    * **프로브** **- \<리소스 그룹 이름 >. json** 파일의 **프로브** 섹션에 항목을 추가 하거나 제거 하 여 구성의 부하 분산 장치에 대 한 프로브를 추가 하거나 제거할 수 있습니다.

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
       Azure Load Balancer 상태 프로브에 대 한 자세한 내용은 [상태 프로브 Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview) 를 참조 하세요.

    * **인바운드 nat 규칙** -loadbalancer.inboundnatrules 파일  **\<>** 의 섹션에 항목을 추가 하거나 제거 하 여 부하 분산 장치에 대 한 인바운드 nat 규칙을 추가 하거나 제거할 수 있습니다.

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
        인바운드 NAT 규칙의 추가 또는 제거를 완료 하려면 규칙은  **\<리소스 그룹 이름 >. json** 파일의 끝에 **유형** 속성으로 표시 되거나 제거 되어야 합니다.

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
        인바운드 NAT 규칙에 대 한 자세한 내용은 [Azure Load Balancer?](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) 을 참조 하세요.

    * **아웃 바운드 규칙** -outboundRules 파일  **\<>** 의 속성을 편집 하 여 구성에서 아웃 바운드 규칙을 추가 하거나 제거할 수 있습니다.

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

         아웃 바운드 규칙에 대 한 자세한 내용은 [Load Balancer 아웃 바운드 규칙](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview) 을 참조 하세요.

13. **\<리소스 그룹 이름 > json** 파일을 저장 합니다.
    
10. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 배포할 대상 외부 부하 분산 장치에 대 한 대상 지역에 또는 리소스 그룹을 만듭니다. 위의 기존 리소스 그룹을이 프로세스의 일부로 재사용할 수도 있습니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용 하 여 이전 단계에서 만든 리소스 그룹에 편집  **\<된 리소스 그룹 이름 > json** 파일을 배포 합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/get-azloadbalancer?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzLoadBalancer -Name <target-publicip-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>삭제 

배포 후에는 대상에서 공용 IP 및 부하 분산 장치를 시작 하거나 삭제 하려는 경우 대상에 생성 된 리소스 그룹을 삭제 하 고 이동 된 공용 IP 및 부하 분산 장치를 삭제 합니다.  리소스 그룹을 제거 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)를 사용 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 또는 [AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0) 및 [AzLoadBalancer](https://docs.microsoft.com/powershell/module/az.network/remove-azloadbalancer?view=azps-2.6.0) 를 사용 하 여 nsg 또는 리소스 그룹을 삭제 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <resource-group-name>

```

``` azurepowershell-interactive

Remove-AzLoadBalancer -name <load-balancer> -ResourceGroupName <resource-group-name>

Remove-AzPublicIpAddress -Name <public-ip> -ResourceGroupName <resource-group-name>


```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
