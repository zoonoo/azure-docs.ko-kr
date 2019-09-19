---
title: Azure PowerShell를 사용 하 여 다른 Azure 지역으로 Azure Virtual Network 이동
description: Azure PowerShell를 사용 하 여 azure Virtual Network를 한 Azure 지역에서 다른 지역으로 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: virtual-network
ms.topic: article
ms.date: 08/26/2019
ms.author: allensu
ms.openlocfilehash: fe4c3fe131eb763ef8875cced91ab3ae22abca08
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/17/2019
ms.locfileid: "71077660"
---
# <a name="move-azure-virtual-network-to-another-region-using-azure-powershell"></a>Azure Powershell을 사용 하 여 Azure Virtual Network을 다른 지역으로 이동

기존 Azure Virtual Network (Vnet)를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 기존 가상 네트워크를 테스트 하 고 사용할 수 있도록 동일한 구성을 사용 하 여 가상 네트워크를 만들 수 있습니다. 재해 복구 계획의 일부로 프로덕션 가상 네트워크를 다른 지역으로 이동할 수도 있습니다.

Azure Resource Manager 템플릿을 사용 하 여 가상 네트워크를 다른 지역으로 이동 하는 것을 완료할 수 있습니다. 가상 네트워크를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여이 작업을 수행 합니다.  리소스 관리자 및 템플릿에 대 한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) 를 참조 하세요.


## <a name="prerequisites"></a>전제 조건

- Azure Virtual Network 이동 하려는 Azure 지역에 있는지 확인 합니다.

- 가상 네트워크를 내보내고 템플릿을 배포 하 여 다른 지역에서 가상 네트워크를 만들려면 네트워크 참가자 역할 이상이 필요 합니다.

- 가상 네트워크 피어 링은 다시 생성 되지 않으며 템플릿에 여전히 있는 경우 실패 합니다.  템플릿을 내보낸 다음 가상 네트워크를 이동한 후에 피어를 다시 설정 하기 전에 가상 네트워크 피어를 모두 제거 해야 합니다.
    
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, NSGs (네트워크 보안 그룹) 및 공용 Ip가 포함 되지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에서 가상 네트워크를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 가상 네트워크 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 이동에 대 한 가상 네트워크를 준비 하 고 Azure PowerShell 명령을 사용 하 여 가상 네트워크를 대상 지역으로 이동 하는 방법을 보여 줍니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-virtual-network-and-deploy-the-target-virtual-network-with-powershell"></a>PowerShell을 사용 하 여 가상 네트워크 내보내기 및 대상 가상 네트워크 배포

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동 하려는 가상 네트워크의 리소스 ID를 가져와 [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceVNETID = (Get-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 가상 네트워크를 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리에 대 한 json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드 한 파일은 리소스를 내보낸 리소스 그룹의 이름으로 지정 됩니다.  **리소스 그룹-이름 >. json 명령에서 내보낸 파일을 찾아 원하는 편집기에서 엽니다. \<**
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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
  
7. 지역 위치 코드를 가져오려면 다음 명령을 실행 하 여 Azure PowerShell cmdlet [AzLocation](https://docs.microsoft.com/powershell/module/az.resources/get-azlocation?view=azps-1.8.0) 을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8.  또한 선택 하는 경우  **\<리소스 그룹 이름 > json** 파일의 다른 매개 변수를 변경할 수 있으며 요구 사항에 따라 선택적입니다.

    * **주소 공간** - **리소스** > **addressSpace**  **섹션을수정하고의addressPrefixes속성을변경하여저장하기전에VNET의주소공간을변경할수있습니다.\< 리소스 그룹 이름 > json** 파일:

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

    * **서브넷** **- \<리소스 그룹 이름 >. json** 파일의 **서브넷** 섹션을 수정 하 여 서브넷 이름 및 서브넷 주소 공간을 변경 하거나 추가할 수 있습니다. **이름** 속성을 변경 하 여 서브넷의 이름을 변경할 수 있습니다. AddressPrefix 파일  **\<>** 에서 속성을 변경 하 여 서브넷 주소 공간을 변경할 수 있습니다.

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

         주소 접두사를 변경 하려면  **리소스그룹이름>.json파일에서위에나열된섹션과아래에나열된형식섹션에서두위치를편집해야\<** 합니다.  **AddressPrefix** 속성을 위와 일치 하도록 변경 합니다.

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

9.  **\<리소스 그룹 이름 > json** 파일을 저장 합니다.

10. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0) 를 사용 하 여 배포할 대상 VNET의 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용 하 여 이전 단계에서 만든 리소스 그룹에 편집  **\<된 리소스 그룹 이름 > json** 파일을 배포 합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 리소스가 대상 지역에 만들어졌는지 확인 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/get-azresourcegroup?view=azps-2.6.0) 및 [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork?view=azps-2.6.0)를 사용 합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzVirtualNetwork -Name <target-virtual-network-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>삭제 

배포 후 대상에서 가상 네트워크를 다시 시작 하거나 삭제 하려는 경우 대상에 생성 된 리소스 그룹을 삭제 하면 이동 된 가상 네트워크가 삭제 됩니다.  리소스 그룹을 제거 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)를 사용 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크의 이동을 완료 하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제 하 고 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 또는 [AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/remove-azvirtualnetwork?view=azps-2.6.0)을 사용 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzVirtualNetwork -Name <source-virtual-network-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure Virtual Network를 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
