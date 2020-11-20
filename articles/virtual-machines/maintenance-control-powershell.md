---
title: PowerShell을 사용 하 여 Azure 가상 머신에 대 한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용 하 여 Azure Vm에 유지 관리를 적용 하는 시기를 제어 하는 방법을 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/19/2020
ms.author: cynthn
ms.openlocfilehash: f33cb7d4d005f15d0a5fcc70d56ebd4698f86694
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94988224"
---
# <a name="control-updates-with-maintenance-control-and-azure-powershell"></a>유지 관리 제어 및 Azure PowerShell를 사용 하 여 업데이트 제어

유지 관리 제어를 통해 격리 된 Vm 및 Azure 전용 호스트에 업데이트를 적용 하는 시기를 결정할 수 있습니다. 이 항목에서는 유지 관리 제어를 위한 Azure PowerShell 옵션을 설명 합니다. 유지 관리 제어, 제한 사항 및 기타 관리 옵션을 사용 하는 이점에 대 한 자세한 내용은 [유지 관리 제어를 사용 하 여 플랫폼 업데이트 관리](maintenance-control.md)를 참조 하세요.
 
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

신뢰할 수 없는 *리포지토리에서* 설치할지를 확인 하는 메시지가 표시 될 수도 있습니다. `Y` **모두 예를** 입력 하거나 선택 하 여 모듈을 설치 합니다.


## <a name="create-a-maintenance-configuration"></a>유지 관리 구성을 만듭니다.

구성에 대 한 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *myMaintenanceRG* 이라는 리소스 그룹을 *에서는 eastus* 에 만듭니다. 사용 하려는 리소스 그룹이 이미 있는 경우이 부분을 건너뛰고 나머지 예에서는 리소스 그룹 이름을 자신의 이름으로 바꿀 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[AzMaintenanceConfiguration](/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 를 사용 하 여 유지 관리 구성을 만듭니다. 이 예제에서는 호스트로 범위가 지정 된 *Myconfig* 라는 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

를 사용 하면 `-MaintenanceScope host` 호스트에 대 한 업데이트를 제어 하는 데 유지 관리 구성이 사용 됩니다.

이름이 같은 구성을 만들려고 하지만 다른 위치에 있는 경우 오류가 발생 합니다. 구성 이름은 리소스 그룹에서 고유 해야 합니다.

[AzMaintenanceConfiguration](/powershell/module/az.maintenance/get-azmaintenanceconfiguration)를 사용 하 여 사용 가능한 유지 관리 구성에 대해 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

### <a name="create-a-maintenance-configuration-with-scheduled-window"></a>예약 된 기간을 사용 하 여 유지 관리 구성 만들기

Azure가 리소스에 대 한 업데이트를 적용 하는 경우 예약 된 기간을 선언할 수도 있습니다. 이 예제에서는 매월 네 번째 월요일에 예약 된 기간이 5 시간인 myConfig 라는 유지 관리 구성을 만듭니다. 예약 된 기간을 만든 후에는 더 이상 수동으로 업데이트를 적용할 필요가 없습니다.

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup $RGName `
   -Name $MaintenanceConfig `
   -MaintenanceScope Host `
   -Location $location `
   -StartDateTime "2020-10-01 00:00" `
   -TimeZone "Pacific Standard Time" `
   -Duration "05:00" `
   -RecurEvery "Month Fourth Monday"
```
> [!IMPORTANT]
> 유지 관리 **기간은** *2 시간* 이상 이어야 합니다. 유지 관리 **되풀이** 는 35 일에 한 번 이상 발생 하도록 설정 되어야 합니다.

유지 관리 **되풀이** 는 매일, 매주 또는 매월로 표시 될 수 있습니다. 예는 다음과 같습니다.
 - 매일-"recurEvery: Day" **또는** "RecurEvery: 3days" 
 - 주별-"recurEvery: 3Weeks" **또는** "RecurEvery: Week 토요일, 일요일" 
 - 매월-"recurEvery: Month day23, day24" **or** "RecurEvery: Month Last 일요일이" **또는** "recurEvery: month 4"  
      

## <a name="assign-the-configuration"></a>구성 할당

[AzConfigurationAssignment](/powershell/module/az.maintenance/new-azconfigurationassignment) 를 사용 하 여 격리 된 VM 또는 Azure 전용 호스트에 구성을 할당 합니다.

### <a name="isolated-vm"></a>격리 된 VM

구성의 ID를 사용 하 여 VM에 구성을 적용 합니다. 에 `-ResourceType VirtualMachines` vm의 이름과 `-ResourceName` 에 대 한 vm의 리소스 그룹을 지정 하 고 제공 `-ResourceGroupName` 합니다. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 구성을 적용 하려면 `-ResourceType hosts` `-ResourceParentName` 호스트 그룹의 이름 및를 포함 하는도 포함 해야 `-ResourceParentType hostGroups` 합니다. 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>보류 중인 업데이트 확인

[AzMaintenanceUpdate](/powershell/module/az.maintenance/get-azmaintenanceupdate) 를 사용 하 여 보류 중인 업데이트가 있는지 확인 합니다. `-subscription`사용자가 로그인 하는 것과 다른 VM의 Azure 구독을 지정 하려면를 사용 합니다.

표시할 업데이트가 없는 경우이 명령은 아무것도 반환 하지 않습니다. 그렇지 않으면 PSApplyUpdate 개체를 반환 합니다.

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>격리 된 VM

격리 된 VM에 대 한 보류 중인 업데이트를 확인 합니다. 이 예제에서 출력은 가독성을 위해 테이블 형식으로 지정 됩니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트의 보류 중인 업데이트를 확인 합니다. 이 예제에서 출력은 가독성을 위해 테이블 형식으로 지정 됩니다. 리소스의 값을 고유한 값으로 바꿉니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>업데이트 적용

[AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 를 사용 하 여 보류 중인 업데이트를 적용 합니다.

### <a name="isolated-vm"></a>격리 된 VM

격리 된 VM에 업데이트를 적용 하는 요청을 만듭니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

성공 하면이 명령은 개체를 반환 `PSApplyUpdate` 합니다. 명령에서 Name 특성을 사용 하 여 `Get-AzApplyUpdate` 업데이트 상태를 확인할 수 있습니다. [업데이트 상태 확인](#check-update-status)을 참조 하세요.

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용 합니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>업데이트 상태 확인
[AzApplyUpdate](/powershell/module/az.maintenance/get-azapplyupdate) 를 사용 하 여 업데이트 상태를 확인 합니다. 아래에 표시 된 명령은를 사용 하 여 매개 변수의 최신 업데이트 상태를 보여 줍니다 `default` `-ApplyUpdateName` . 업데이트 이름 ( [AzApplyUpdate](/powershell/module/az.maintenance/new-azapplyupdate) 명령에 의해 반환 됨)을 대체 하 여 특정 업데이트의 상태를 가져올 수 있습니다.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime은 자동 유지 관리 기간을 사용 하지 않는 경우 사용자 또는 플랫폼에 의해 시작 된 업데이트가 완료 된 시간입니다. 유지 관리 제어를 통해 업데이트를 적용 한 적이 없는 경우 기본값을 표시 합니다.

### <a name="isolated-vm"></a>격리 된 VM

특정 가상 컴퓨터에 대 한 업데이트를 확인 합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대 한 업데이트를 확인 합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>유지 관리 구성 제거

[AzMaintenanceConfiguration](/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) 를 사용 하 여 유지 관리 구성을 삭제 합니다.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>다음 단계
자세히 알아보려면 [유지 관리 및 업데이트](maintenance-and-updates.md)를 참조 하세요.
