---
title: Azure PowerShell을 사용하여 Azure NSG(네트워크 보안 그룹)를 다른 Azure 지역으로 이동
description: Azure PowerShell을 사용하여 Azure 네트워크 보안 그룹을 한 Azure 지역으로 이동하려면 Azure Resource Manager 템플릿을 사용합니다.
author: asudbring
ms.service: virtual-network
ms.topic: how-to
ms.date: 08/31/2019
ms.author: allensu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1330c567b88adaa99342c35cef9ed584724278e6
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110672584"
---
# <a name="move-azure-network-security-group-nsg-to-another-region-using-azure-powershell"></a>Azure PowerShell을 사용하여 Azure NSG(네트워크 보안 그룹)를 다른 지역으로 이동

기존 NSG를 한 지역에서 다른 지역으로 이동하려는 다양한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 보안 규칙을 사용하여 NSG를 만들 수 있습니다. 또한 재해 복구 계획의 일부로 NSG를 다른 지역으로 이동할 수도 있습니다.

Azure 보안 그룹은 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용하여 NSG의 기존 구성 및 보안 규칙을 내보낼 수 있습니다.  그런 다음, NSG를 템플릿으로 내보내고, 대상 지역과 일치하도록 매개 변수를 수정한 다음, 템플릿을 새 지역에 배포하여 리소스를 다른 지역에 스테이징할 수 있습니다.  Resource Manager 및 템플릿에 대한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](../azure-resource-manager/management/manage-resource-groups-powershell.md#export-resource-groups-to-templates)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

- 이동하려는 Azure 지역에 Azure 네트워크 보안 그룹이 있는지 확인합니다.

- Azure 네트워크 보안 그룹은 지역 간에 이동할 수 없습니다.  새 NSG를 대상 지역의 리소스에 연결해야 합니다.

- NSG 구성을 내보내고 템플릿을 배포하여 다른 지역에 NSG를 만들려면 네트워크 기여자 역할 이상이 필요합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃은 부하 분산 장치, 공용 IP 및 가상 네트워크를 포함하되 이에 국한되지 않습니다.

- Azure 구독에서 사용되는 대상 지역에 NSG를 만들 수 있도록 허용하는지 확인합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에 이 프로세스에 대한 NSG 추가를 지원할 수 있는 충분한 리소스가 있는지 확인합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 Resource Manager 템플릿을 사용하여 구성 및 보안 규칙 이동을 위해 네트워크 보안 그룹을 준비하고 Azure PowerShell을 사용하여 NSG 구성 및 보안 규칙을 대상 지역으로 이동하는 방법을 보여 줍니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>템플릿 내보내기 및 스크립트에서 배포

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동하려는 NSG의 리소스 ID를 가져와서 [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)을 사용하여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourceNSGID = (Get-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 NSG를 [Export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) 명령을 실행하는 디렉터리의 .json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceNSGID -IncludeParameterDefaultValue
   ```

4. 다운로드한 파일 이름으로 리소스를 내보낸 리소스 그룹의 이름이 사용됩니다.  명령에서 내보낸 **\<resource-group-name>.json** 파일을 찾은 후 원하는 편집기에서 엽니다.
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
   ```

5. NSG 이름의 매개 변수를 편집하려면 원본 NSG 이름의 **defaultValue** 속성을 대상 NSG의 이름으로 변경하고 이름이 따옴표 안에 있는지 확인합니다.
    
    ```json
            {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "networkSecurityGroups_myVM1_nsg_name": {
            "defaultValue": "<target-nsg-name>",
            "type": "String"
            }
        }

    ```


6. NSG 구성 및 보안 규칙이 이동될 대상 지역을 편집하려면 **리소스** 에서 **location** 속성을 변경합니다.

    ```json
            "resources": [
            {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2019-06-01",
            "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
            "location": "<target-region>",
            "properties": {
                "provisioningState": "Succeeded",
                "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78", 
             }
            }
    ```
  
7. 지역 위치 코드를 가져오려면 다음 명령을 실행하여 Azure PowerShell cmdlet [Get-AzLocation](/powershell/module/az.resources/get-azlocation)을 사용할 수 있습니다.

    ```azurepowershell-interactive

    Get-AzLocation | format-table
    
    ```
8. 또한 선택하는 경우 **\<resource-group-name>.json** 의 다른 매개 변수를 변경할 수 있고 요구 사항에 따라 선택적입니다.

    * **보안 규칙** - **\<resource-group-name>.json** 파일의 **securityRules** 섹션에 규칙을 추가하거나 제거하여 대상 NSG에 배포되는 규칙을 편집할 수 있습니다.

        ```json
           "resources": [
                  {
                  "type": "Microsoft.Network/networkSecurityGroups",
                  "apiVersion": "2019-06-01",
                  "name": "[parameters('networkSecurityGroups_myVM1_nsg_name')]",
                  "location": "TARGET REGION",
                  "properties": {
                       "provisioningState": "Succeeded",
                       "resourceGuid": "2c846acf-58c8-416d-be97-ccd00a4ccd78",
                  "securityRules": [
                    {
                        "name": "RDP",
                        "etag": "W/\"c630c458-6b52-4202-8fd7-172b7ab49cf5\"",
                        "properties": {
                             "provisioningState": "Succeeded",
                             "protocol": "TCP",
                             "sourcePortRange": "*",
                             "destinationPortRange": "3389",
                             "sourceAddressPrefix": "*",
                             "destinationAddressPrefix": "*",
                             "access": "Allow",
                             "priority": 300,
                             "direction": "Inbound",
                             "sourcePortRanges": [],
                             "destinationPortRanges": [],
                             "sourceAddressPrefixes": [],
                             "destinationAddressPrefixes": []
                            }
                        ]
            }  
            
        ```

        대상 NSG에서 규칙의 추가 또는 제거를 완료하려면 다음과 같은 형식으로 **\<resource-group-name>.json** 파일의 끝에 있는 사용자 지정 규칙 형식도 편집해야 합니다.

        ```json
           {
            "type": "Microsoft.Network/networkSecurityGroups/securityRules",
            "apiVersion": "2019-06-01",
            "name": "[concat(parameters('networkSecurityGroups_myVM1_nsg_name'), '/Port_80')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', parameters('networkSecurityGroups_myVM1_nsg_name'))]"
            ],
            "properties": {
                "provisioningState": "Succeeded",
                "protocol": "*",
                "sourcePortRange": "*",
                "destinationPortRange": "80",
                "sourceAddressPrefix": "*",
                "destinationAddressPrefix": "*",
                "access": "Allow",
                "priority": 310,
                "direction": "Inbound",
                "sourcePortRanges": [],
                "destinationPortRanges": [],
                "sourceAddressPrefixes": [],
                "destinationAddressPrefixes": []
            }
        ```

9. **\<resource-group-name>.json** 파일을 저장합니다.

10. [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)을 사용하여 배포할 대상 NSG에 대한 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
    
11. [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용하여 이전 단계에서 만든 리소스 그룹에 편집된 **\<resource-group-name>.json** 파일을 배포합니다.

    ```azurepowershell-interactive

    New-AzResourceGroupDeployment -ResourceGroupName <target-resource-group-name> -TemplateFile <source-resource-group-name>.json
    
    ```

12. 리소스가 대상 지역에 만들어졌는지 확인하려면 [Get-AzResourceGroup](/powershell/module/az.resources/get-azresourcegroup) 및 [Get-AzNetworkSecurityGroup](/powershell/module/az.network/get-aznetworksecuritygroup)을 사용합니다.
    
    ```azurepowershell-interactive

    Get-AzResourceGroup -Name <target-resource-group-name>

    ```

    ```azurepowershell-interactive

    Get-AzNetworkSecurityGroup -Name <target-nsg-name> -ResourceGroupName <target-resource-group-name>

    ```

## <a name="discard"></a>취소 

배포 후 대상에서 NSG를 시작하거나 버리려는 경우 대상에서 만든 리소스 그룹을 삭제하면 이동한 NSG가 삭제됩니다.  리소스 그룹을 제거하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)을 사용합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 NSG의 이동을 완료하려면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) 또는 [Remove-AzNetworkSecurityGroup](/powershell/module/az.network/remove-aznetworksecuritygroup)을 사용하여 원본 NSG 또는 리소스 그룹을 삭제합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzNetworkSecurityGroup -Name <source-nsg-name> -ResourceGroupName <source-resource-group-name>

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 네트워크 보안 그룹을 한 지역에서 다른 지역으로 이동하고 원본 리소스를 정리했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동하는 방법에 대한 자세한 내용은 다음을 참조하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [다른 지역으로 Azure VM 이동](../site-recovery/azure-to-azure-tutorial-migrate.md)
