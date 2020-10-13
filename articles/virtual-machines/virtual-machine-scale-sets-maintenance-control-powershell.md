---
title: PowerShell을 사용 하 여 Azure 가상 머신 확장 집합에서 OS 이미지 업그레이드에 대 한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용 하 여 Azure 가상 머신 확장 집합에 자동 OS 이미지 업그레이드가 롤아웃 되는 시기를 제어 하는 방법을 알아봅니다.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: d8acab17e9d8dfc078b46f6a279cc671a70b0a50
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974841"
---
# <a name="preview-maintenance-control-for-os-image-upgrades-on-azure-virtual-machine-scale-sets-using-powershell"></a>미리 보기: PowerShell을 사용 하 여 Azure 가상 머신 확장 집합에서 OS 이미지 업그레이드에 대 한 유지 관리 제어

유지 관리 제어를 통해 가상 머신 확장 집합에 자동 게스트 OS 이미지 업그레이드를 적용할 시기를 결정할 수 있습니다. 이 항목에서는 유지 관리 제어를 위한 Azure PowerShell 옵션을 설명 합니다. 유지 관리 제어를 사용 하는 방법에 대 한 자세한 내용은 [Azure 가상 머신 확장 집합에 대 한 유지 관리 제어](virtual-machine-scale-sets-maintenance-control.md)를 참조 하세요.

> [!IMPORTANT]
> Azure 가상 머신 확장 집합에서 OS 이미지 업그레이드에 대 한 유지 관리 제어는 현재 공개 미리 보기로 제공 됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="enable-the-powershell-module"></a>PowerShell 모듈 사용

최신 상태 인지 확인 `PowerShellGet` 합니다.    

```azurepowershell-interactive  
Install-Module -Name PowerShellGet -Repository PSGallery -Force 
``` 

`Az.Maintenance`PowerShell 모듈을 설치 합니다.     

```azurepowershell-interactive  
Install-Module -Name Az.Maintenance
``` 

로컬로 설치 하는 경우 관리자 권한으로 PowerShell 프롬프트를 열어야 합니다.

신뢰할 수 없는 *리포지토리에서*설치할지를 확인 하는 메시지가 표시 될 수도 있습니다. `Y` **모두 예를** 입력 하거나 선택 하 여 모듈을 설치 합니다.

## <a name="connect-to-an-azure-account"></a>Azure 계정에 연결

[AzAccount](/powershell/module/az.accounts/connect-azaccount) 및 [AzAccount](/powershell/module/az.accounts/set-azcontext)를 사용 하 여 원하는 Azure 계정에 연결 합니다.

```azurepowershell-interactive
Connect-AzAccount
Set-AzContext 00a000aa-0a00-0a0a-00aa-a00a000aaa00

$RGName="myMaintenanceRG"
$MaintenanceConfig="myMaintenanceConfig"
$location="eastus2"
$vmss="myMaintenanceVMSS"
```

## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

구성에 대 한 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *eastus2*에서 *myMaintenanceRG* 이라는 리소스 그룹을 만듭니다. 사용 하려는 리소스 그룹이 이미 있는 경우이 부분을 건너뛸 수 있습니다. 나머지 예제에서는 리소스 그룹 이름을 사용자 고유의 이름으로 바꿉니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location $location `
   -Name $RGName
```

[AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 를 사용 하 여 유지 관리 구성을 만듭니다. 이 예제에서는 OS 이미지로 범위가 지정 된 *Myconfig* 라는 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope OSImage `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Day"
```

> [!IMPORTANT]
> 유지 관리 **기간은** *5 시간* 이상 이어야 합니다. 유지 관리 **되풀이** 는 *Day*로 설정 되어야 합니다.

를 사용 하면 `-MaintenanceScope OSImage` 유지 관리 구성을 사용 하 여 게스트 OS에 대 한 업데이트를 제어할 수 있습니다.

이름이 같은 구성을 만들려고 하지만 다른 위치에 있는 경우 오류가 발생 합니다. 구성 이름은 리소스 그룹에서 고유 해야 합니다.

[AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)를 사용 하 여 사용 가능한 유지 관리 구성에 대해 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="associate-your-virtual-machine-scale-set-to-the-maintenance-configuration"></a>유지 관리 구성에 가상 머신 확장 집합 연결

가상 머신 확장 집합은 유지 관리 구성의 지역 및 구독에 관계 없이 유지 관리 구성에 연결할 수 있습니다. 유지 관리 구성에 옵트인 하면 확장 집합에 대 한 새 OS 이미지 업데이트가 다음에 사용 가능한 유지 관리 기간에 자동으로 예약 됩니다.

[AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 를 사용 하 여 가상 머신 확장 집합에 유지 관리 구성을 연결 합니다.

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName $RGName `
   -Location $location `
   -ResourceName $vmss `
   -ResourceType VirtualMachineScaleSets `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name`
   -MaintenanceConfigurationId $config.Id
``` 

## <a name="enable-automatic-os-upgrade"></a>자동 OS 업그레이드 사용

유지 관리 제어를 사용 하는 각 가상 머신 확장 집합에 대해 자동 OS 업그레이드를 사용 하도록 설정할 수 있습니다. 가상 머신 확장 집합에서 자동 OS 업그레이드를 사용 하도록 설정 하려면 [Azure 가상 머신 확장 집합 자동 os 이미지 업그레이드](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) 문서를 참조 하세요. 


## <a name="next-steps"></a>다음 단계

Azure에서 실행 되는 가상 머신에 대 한 유지 관리 및 업데이트에 대해 알아봅니다.

> [!div class="nextstepaction"]
> [유지 관리 및 업데이트](maintenance-and-updates.md)