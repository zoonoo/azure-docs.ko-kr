---
title: Azure PowerShell을 사용하여 Azure 가상 네트워크를 다른 Azure 지역으로 이동
description: Resource Manager 템플릿 및 Azure PowerShell을 사용하여 Azure 지역 간에 Azure 가상 네트워크를 이동합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ba112304839fe75e3bc55bc3b554b6ad8f3792c5
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110672499"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure 가상 네트워크를 다른 지역으로 이동

기존 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동하는 다양한 시나리오가 있습니다. 예를 들어, 기존 가상 네트워크와 동일한 테스트용 구성 및 가용성을 사용해서 가상 네트워크를 만들 수 있습니다. 또는 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동할 수 있습니다.

Azure Resource Manager 템플릿을 사용하여 가상 네트워크를 다른 지역으로 이동할 수 있습니다. 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치하도록 매개 변수를 수정한 다음, 새 지역에 템플릿을 배포하여 이 작업을 수행합니다. Resource Manager 템플릿에 대한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 가상 네트워크가 이동을 시작하려는 Azure 지역에 있는지 확인합니다.

- 가상 네트워크를 내보내고 템플릿을 배포하여 다른 지역에 가상 네트워크를 만들려면 네트워크 기여자 역할 이상이 필요합니다.

- 가상 네트워크 피어링은 다시 생성되지 않으며 템플릿에 여전히 있는 경우 실패합니다. 템플릿을 내보내기 전에 가상 네트워크 피어를 모두 제거해야 합니다. 그런 다음, 가상 네트워크 이동 후 다시 설정할 수 있습니다.
    
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 배치에는 부하 분산 장치, NSG(네트워크 보안 그룹) 및 공용 IP를 포함하되 이에 국한되지 않습니다.

- Azure 구독에서 대상 지역에 가상 네트워크를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 가상 네트워크 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-for-the-move"></a>이동 준비
이 섹션에서는 Resource Manager 템플릿을 사용하여 가상 네트워크의 이동 준비를 수행합니다. 그런 다음, Azure PowerShell 명령을 사용하여 가상 네트워크를 대상 지역으로 이동합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용하여 가상 네트워크를 내보내고 대상 가상 네트워크를 배포하려면 다음을 수행합니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인한 다음 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 대상 지역으로 이동하려는 가상 네트워크의 리소스 ID를 가져온 다음 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)를 사용하여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) 명령을 실행하는 디렉터리의 .json 파일로 원본 가상 네트워크를 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. 다운로드한 파일은 리소스를 내보낸 리소스 그룹과 이름이 같습니다. 명령을 사용하여 내보낸 *\<resource-group-name> json* 파일을 찾아 편집기에서 엽니다.
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 가상 네트워크 이름의 매개 변수를 편집하려면 원본 가상 네트워크 이름의 **defaultValue** 속성을 대상 가상 네트워크의 이름으로 변경합니다. 이름을 큰따옴표로 묶어야 합니다.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 가상 네트워크가 이동될 대상 지역을 편집하려면 리소스에서 **location** 속성을 변경합니다.

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
  
1. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. (선택 사항) 또한 요구 사항에 따라 *\<resource-group-name>.json* 파일의 다른 매개 변수를 변경할 수 있습니다.

    * **주소 공간**: 파일을 저장하기 전에 **resources** > **addressSpace** 섹션을 수정하고 **addressPrefixes** 속성을 변경하여 가상 네트워크의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷**: 파일의 **subnets** 섹션을 변경하여 서브넷 이름 및 서브넷 주소 공간을 변경하거나 추가할 수 있습니다. **이름** 속성을 변경하여 서브넷의 이름을 변경할 수 있습니다. 또한 **addressPrefix** 속성을 변경하여 서브넷 주소 공간을 변경할 수 있습니다.

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

        주소 접두사를 변경하려면 이전 섹션의 코드와 다음 코드의 **형식** 섹션에서 파일을 편집합니다. 다음 코드의 **addressPrefix** 속성을 이전 섹션의 코드에 있는 **addressPrefix** 속성과 일치하도록 변경합니다.

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

1. *\<resource-group-name>.json* 파일을 저장합니다.

1. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 대상 VNET을 배포할 대상 지역에 리소스 그룹 만들기:
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 *\<resource-group-name>.json* 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. 대상 지역에 리소스가 만들어졌는지 확인하려면 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) 및 [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)를 사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>가상 네트워크 또는 리소스 그룹 삭제 

가상 네트워크를 배포한 후 대상 지역에서 가상 네트워크를 시작하거나 무시하려면 대상 지역에서 만든 리소스 그룹을 삭제합니다. 이동한 가상 네트워크는 삭제됩니다. 

리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 이동을 완료하려면 다음 중 하나를 수행합니다.

* [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용하여 리소스 그룹 삭제:

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* [AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)를 사용하여 원본 가상 네트워크 삭제:  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PowerShell을 사용하여 가상 네트워크를 한 지역에서 다른 지역으로 이동한 다음, 불필요한 원본 리소스를 정리했습니다. Azure에서 지역 간 리소스 이동 및 재해 복구에 대한 자세한 내용은 다음을 참조하세요.

- [리소스를 새 리소스 그룹 또는 구독으로 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure 가상 머신 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)
