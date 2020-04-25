---
title: Azure PowerShell를 사용 하 여 Azure 공용 IP를 다른 Azure 지역으로 이동
description: Azure PowerShell를 사용 하 여 azure 지역 간에 Azure 공용 IP를 이동 하려면 Azure Resource Manager 템플릿을 사용 합니다.
author: asudbring
ms.service: virtual-network
ms.subservice: ip-services
ms.topic: article
ms.date: 08/29/2019
ms.author: allensu
ms.openlocfilehash: 76924705ff801ce3be6a5c76f7ae276bdbf93def
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/24/2020
ms.locfileid: "82147872"
---
# <a name="move-azure-public-ip-to-another-region-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure 공용 IP를 다른 지역으로 이동

기존 Azure 공용 Ip를 한 지역에서 다른 지역으로 이동 하려는 다양 한 시나리오가 있습니다. 예를 들어 테스트를 위해 동일한 구성 및 sku를 사용 하 여 공용 IP를 만들 수 있습니다. 재해 복구 계획의 일부로 공용 IP를 다른 지역으로 이동할 수도 있습니다.

Azure 공용 Ip는 지역에만 해당 되며 한 지역에서 다른 지역으로 이동할 수 없습니다. 그러나 Azure Resource Manager 템플릿을 사용 하 여 공용 IP의 기존 구성을 내보낼 수 있습니다.  그런 다음 공용 IP를 템플릿으로 내보내고 대상 지역과 일치 하도록 매개 변수를 수정한 다음 새 지역에 템플릿을 배포 하 여 다른 지역의 리소스를 준비할 수 있습니다.  리소스 관리자 및 템플릿에 대 한 자세한 내용은 [템플릿으로 리소스 그룹 내보내기](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-powershell#export-resource-groups-to-templates) 를 참조 하세요.


## <a name="prerequisites"></a>전제 조건

- Azure 공용 IP가 이동 하려는 Azure 지역에 있는지 확인 합니다.

- Azure 공용 Ip는 지역 간에 이동할 수 없습니다.  새 공용 ip를 대상 지역의 리소스에 연결 해야 합니다.

- 공용 IP 구성을 내보내고 템플릿을 배포 하 여 다른 지역에 공용 IP를 만들려면 네트워크 참가자 역할이 이상 필요 합니다.
   
- 현재 사용하고 있는 모든 리소스와 원본 네트워킹 레이아웃을 식별합니다. 이 레이아웃에는 부하 분산 장치, NSGs (네트워크 보안 그룹) 및 가상 네트워크를 포함 하지만이에 국한 되지 않습니다.

- Azure 구독을 사용 하 여 사용 되는 대상 지역에서 공용 Ip를 만들 수 있는지 확인 합니다. 필요한 할당량을 사용하려면 지원 팀에 문의하세요.

- 구독에이 프로세스에 대 한 공용 Ip 추가를 지원할 수 있는 충분 한 리소스가 있는지 확인 합니다.  [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)을 참조하세요.


## <a name="prepare-and-move"></a>준비 및 이동
다음 단계에서는 리소스 관리자 템플릿을 사용 하 여 구성 이동에 대 한 공용 IP를 준비 하 고 Azure PowerShell를 사용 하 여 공용 IP 구성을 대상 지역으로 이동 하는 방법을 보여 줍니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="export-the-template-and-deploy-from-a-script"></a>템플릿을 내보내고 스크립트에서 배포

1. [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-2.5.0) 명령을 사용 하 여 Azure 구독에 로그인 하 고 화면의 지시를 따릅니다.
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```

2. 대상 지역으로 이동 하려는 공용 IP의 리소스 ID를 가져와 [AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/get-azpublicipaddress?view=azps-2.6.0)를 사용 하 여 변수에 넣습니다.

    ```azurepowershell-interactive
    $sourcePubIPID = (Get-AzPublicIPaddress -Name <source-public-ip-name> -ResourceGroupName <source-resource-group-name>).Id

    ```
3. 원본 가상 네트워크를 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup?view=azps-2.6.0)명령을 실행 하는 디렉터리에 대 한 json 파일로 내보냅니다.
   
   ```azurepowershell-interactive
   Export-AzResourceGroup -ResourceGroupName <source-resource-group-name> -Resource $sourceVNETID -IncludeParameterDefaultValue
   ```

4. 다운로드 한 파일은 리소스를 내보낸 리소스 그룹의 이름으로 지정 됩니다.  **리소스 그룹-이름>. json 명령에서 내보낸 파일을 찾아 원하는 편집기에서 엽니다. \<**
   
   ```azurepowershell
   notepad <source-resource-group-name>.json
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

    * **Sku** **- \<리소스 그룹 이름>. json** 파일에서 **Sku** > **이름** 속성을 변경 하 여 구성의 공용 IP sku를 standard에서 basic 또는 basic으로 변경할 수 있습니다.

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

    * **공용 IP 할당 방법** 및 **유휴 시간 제한** - **PublicIPAllocationMethod** 속성을 **동적** 에서 **정적** 또는 **정적** 에서 **동적**으로 변경 하 여 템플릿에서 이러한 두 옵션을 모두 변경할 수 있습니다. **IdleTimeoutInMinutes** 속성을 원하는 양만큼 변경 하 여 유휴 시간 제한을 변경할 수 있습니다.  기본값은 **4**입니다.

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


9. ** \<리소스 그룹 이름> json** 파일을 저장 합니다.

10. [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup?view=azps-2.6.0)를 사용 하 여 배포할 대상 공용 IP에 대 한 대상 지역에 리소스 그룹을 만듭니다.
    
    ```azurepowershell-interactive
    New-AzResourceGroup -Name <target-resource-group-name> -location <target-region>
    ```
11. [AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment?view=azps-2.6.0)를 사용 하 여 이전 단계에서 만든 리소스 그룹에 편집 ** \<된 리소스 그룹 이름> json** 파일을 배포 합니다.

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
## <a name="discard"></a>취소 

배포 후 대상에서 공용 ip를 시작 하거나 삭제 하려는 경우 대상에서 만든 리소스 그룹을 삭제 하면 이동 된 공용 IP가 삭제 됩니다.  리소스 그룹을 제거 하려면 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0)를 사용 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <target-resource-group-name>

```

## <a name="clean-up"></a>정리

변경 내용을 커밋하고 가상 네트워크의 이동을 완료 하려면 원본 가상 네트워크 또는 리소스 그룹을 삭제 하 고 [AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup?view=azps-2.6.0) 또는 [AzPublicIPAddress](https://docs.microsoft.com/powershell/module/az.network/remove-azpublicipaddress?view=azps-2.6.0)을 사용 합니다.

```azurepowershell-interactive

Remove-AzResourceGroup -Name <source-resource-group-name>

```

``` azurepowershell-interactive

Remove-AzPublicIpAddress -Name <source-publicip-name> -ResourceGroupName <resource-group-name>

```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 Azure 공용 IP를 한 지역에서 다른 지역으로 이동 하 고 원본 리소스를 정리 했습니다.  Azure에서 지역 및 재해 복구 간에 리소스를 이동 하는 방법에 대 한 자세한 내용은 다음을 참조 하세요.


- [새 리소스 그룹 또는 구독으로 리소스 이동](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)
- [다른 지역으로 Azure VM 이동](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-tutorial-migrate)
