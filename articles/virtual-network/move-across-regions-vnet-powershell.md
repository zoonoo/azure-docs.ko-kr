---
title: Azure PowerShell를 사용 하 여 Azure 가상 네트워크를 다른 Azure 지역으로 이동
description: 리소스 관리자 템플릿 및 Azure PowerShell를 사용 하 여 azure 가상 네트워크를 한 Azure 지역에서 다른 지역으로 이동 합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: e13164c3ec6049a8ae3954528a02d20e313dd883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84711462"
---
# <a name="move-an-azure-virtual-network-to-another-region-by-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 가상 네트워크를 다른 지역으로 이동

기존 Azure 가상 네트워크를 한 지역에서 다른 지역으로 이동 하는 다양 한 시나리오가 있습니다. 예를 들어 기존 가상 네트워크로 테스트 및 가용성에 대해 동일한 구성으로 가상 네트워크를 만들 수 있습니다. 또는 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동 하는 것이 좋습니다.

Azure Resource Manager 템플릿을 사용 하 여 가상 네트워크를 다른 지역으로 이동 하는 것을 완료할 수 있습니다. 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여이 작업을 수행 합니다. 리소스 관리자 템플릿에 대 한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates)를 참조 하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 가상 네트워크가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- 가상 네트워크를 내보내고 템플릿을 배포 하 여 다른 지역에서 가상 네트워크를 만들려면 네트워크 참가자 역할이 이상 있어야 합니다.

- 가상 네트워크 피어 링은 다시 생성 되지 않으며 템플릿에 여전히 있는 경우 실패 합니다. 템플릿을 내보내기 전에 가상 네트워크 피어를 모두 제거 해야 합니다. 그런 다음 가상 네트워크 이동 후 다시 설정할 수 있습니다.
    
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, NSGs (네트워크 보안 그룹) 및 공용 Ip가 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 대상 지역에서 가상 네트워크를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용 하도록 설정 하려면 지원 담당자에 게 문의 하세요.

- 구독에이 프로세스에 대 한 가상 네트워크 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다. 자세한 내용은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조 하세요.


## <a name="prepare-for-the-move"></a>이동 준비
이 섹션에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 가상 네트워크를 준비 합니다. 그런 다음 Azure PowerShell 명령을 사용 하 여 가상 네트워크를 대상 지역으로 이동 합니다.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

PowerShell을 사용 하 여 가상 네트워크를 내보내고 대상 가상 네트워크를 배포 하려면 다음을 수행 합니다.

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

1. 대상 지역으로 이동 하려는 가상 네트워크의 리소스 ID를 가져온 다음 [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 하 여 변수에 저장 합니다.

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id
    ```

1. 원본 가상 네트워크를 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리의 json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

1. 다운로드 한 파일은 리소스를 내보낸 리소스 그룹과 이름이 같습니다. 명령을 사용 하 여 내보낸 * \<resource-group-name> json* 파일을 찾아 편집기에서 엽니다.
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

1. 가상 네트워크 이름의 매개 변수를 편집 하려면 원본 가상 네트워크 이름의 **defaultValue** 속성을 대상 가상 네트워크의 이름으로 변경 합니다. 이름을 따옴표로 묶어야 합니다.
    
    ```json
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentmyResourceGroupVNET.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "virtualNetworks_myVNET1_name": {
        "defaultValue": "<target-virtual-network-name>",
        "type": "String"
        }
    ```

1. 가상 네트워크를 이동할 대상 지역을 편집 하려면 리소스에서 **위치** 속성을 변경 합니다.

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
  
1. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    ```

1. 필드 또한 요구 사항에 따라 * \<resource-group-name> json* 파일의 다른 매개 변수를 변경할 수도 있습니다.

    * **주소 공간**: 파일을 저장 하기 전에 **resources**  >  **addressSpace** 섹션을 수정 하 고 **addressPrefixes** 속성을 변경 하 여 가상 네트워크의 주소 공간을 변경할 수 있습니다.

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

    * **서브넷**: 파일의 **서브넷** 섹션을 변경 하 여 서브넷 이름 및 서브넷 주소 공간을 변경 하거나 추가할 수 있습니다. **이름** 속성을 변경 하 여 서브넷의 이름을 변경할 수 있습니다. **AddressPrefix** 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 있습니다.

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

        주소 접두사를 변경 하려면 이전 섹션의 코드와 다음 코드의 **형식** 섹션에서 파일을 편집 합니다. 이전 섹션에 있는 코드의 **addressPrefix** 속성과 일치 하도록 다음 코드의 **addressPrefix** 속성을 변경 합니다.

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

1. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 배포할 대상 가상 네트워크의 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
1. [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용 하 여 이전 단계에서 만든 리소스 그룹에 편집 된 * \<resource-group-name> json* 파일을 배포 합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    ```

1. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>
    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>
    ```

## <a name="delete-the-virtual-network-or-resource-group"></a>가상 네트워크 또는 리소스 그룹을 삭제 합니다. 

가상 네트워크를 배포한 후 대상 지역에서 가상 네트워크를 시작 하거나 삭제 하려면 대상 지역에서 만든 리소스 그룹을 삭제 하 고 이동한 가상 네트워크는 삭제 됩니다. 

리소스 그룹을 제거하려면 [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>
```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크 이동을 완료 하려면 다음 중 하나를 수행 합니다.

* [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)를 사용 하 여 리소스 그룹을 삭제 합니다.

    ```azurepowershell-interactive

    Remove-AzResourceGroup -Name <source-resource-group-name>
    ```

* [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)를 사용 하 여 원본 가상 네트워크를 삭제 합니다.  
    ``` azurepowershell-interactive

    Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>
    ```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PowerShell을 사용 하 여 가상 네트워크를 한 지역에서 다른 지역으로 이동한 다음 불필요 한 소스 리소스를 정리 했습니다. Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.

- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure 가상 머신 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
