---
title: PowerShell을 사용하는 Azure 가상 시스템에 대한 유지 관리 제어
description: 유지 관리 제어 및 PowerShell을 사용하여 Azure VM에 유지 관리가 적용되는 시기를 제어하는 방법에 대해 알아봅니다.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060129"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>미리 보기: 유지 관리 제어 및 Azure PowerShell을 통해 업데이트를 제어합니다.

유지 관리 제어를 사용하여 재부팅이 필요하지 않은 플랫폼 업데이트를 관리합니다. Azure는 안정성, 성능, 보안을 개선하거나 새로운 기능을 시작하기 위해 인프라를 자주 업데이트합니다. 대부분의 업데이트는 사용자에게 투명합니다. 게임, 미디어 스트리밍 및 금융 거래와 같은 일부 민감한 워크로드는 유지 관리를 위해 VM 고정 또는 연결을 끊는 몇 초라도 견딜 수 없습니다. 유지 보수 제어를 통해 플랫폼 업데이트를 기다렸다가 35일 롤링 기간 내에 적용할 수 있습니다. 

유지 관리 제어를 사용하면 격리된 VM에 업데이트를 적용할 시기를 결정할 수 있습니다.

유지 보수 제어를 통해 다음을 수행할 수 있습니다.
- 하나의 업데이트 패키지로 일괄 업데이트합니다.
- 업데이트를 적용하려면 최대 35일까지 기다립니다. 
- Azure 함수를 사용하여 유지 관리 기간의 플랫폼 업데이트를 자동화합니다.
- 유지 관리 구성은 구독 및 리소스 그룹에서 작동합니다. 

> [!IMPORTANT]
> 유지 관리 제어는 현재 공개 미리 보기상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 

## <a name="limitations"></a>제한 사항

- VM은 전용 [호스트에](./linux/dedicated-hosts.md)있거나 [격리된 VM 크기를](./linux/isolation.md)사용하여 만들어야 합니다.
- 35일이 지나면 업데이트가 자동으로 적용됩니다.
- 사용자는 **리소스 기고자** 액세스 권한이 있어야 합니다.


## <a name="enable-the-powershell-module"></a>PowerShell 모듈 사용

최신 `PowerShellGet` 상태인지 확인합니다.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

Az.Maintenance PowerShell cmdlet은 미리 보기에 있으므로 클라우드 셸또는 로컬 PowerShell 설치에 매개 변수가 `AllowPrerelease` 있는 모듈을 설치해야 합니다.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

로컬로 설치하는 경우 관리자로 PowerShell 프롬프트를 열었는지 확인합니다.

*신뢰할 수 없는 리포지토리에서*설치하려는지 확인하라는 메시지가 표시될 수도 있습니다. 모듈을 설치하려면 `Y` **모두에 예(예)를** 입력하거나 선택합니다.



## <a name="create-a-maintenance-configuration"></a>유지 관리 구성 만들기

구성에 대한 컨테이너로 리소스 그룹을 만듭니다. 이 예제에서는 *myMaintenanceRG라는* 리소스 그룹이 *eastus에서*만들어집니다. 사용하려는 리소스 그룹이 이미 있는 경우 이 부분을 건너뛰고 나머지 예제에서 리소스 그룹 이름을 소유한 것으로 바꿀 수 있습니다.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

[New-Az 유지 보수 구성을](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) 사용하여 유지 관리 구성을 만듭니다. 이 예제는 호스트에 대한 범위로 지정된 *myConfig라는* 유지 관리 구성을 만듭니다. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

호스트에 `-MaintenanceScope host` 대한 업데이트를 제어하는 데 유지 관리 구성이 사용됩니다.

이름이 같지만 다른 위치에 있는 구성을 만들려고 하면 오류가 발생합니다. 구성 이름은 구독에 고유해야 합니다.

[Get-Az 유지 관리 구성을](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration)사용하여 사용 가능한 유지 관리 구성을 쿼리할 수 있습니다.

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>구성 할당

[New-AzConfigurationAssignment를](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) 사용하여 격리된 VM 또는 Azure 전용 호스트에 구성을 할당합니다.

### <a name="isolated-vm"></a>격리된 VM

구성의 ID를 사용하여 VM에 구성을 적용합니다. 에 `-ResourceType VirtualMachines` 대한 VM 및 에 `-ResourceName`대한 VM의 리소스 그룹의 `-ResourceGroupName`이름을 지정하고 제공합니다. 

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

전용 호스트에 구성을 적용하려면 호스트 그룹의 `-ResourceType hosts` `-ResourceParentName` 이름과 을 포함해야 `-ResourceParentType hostGroups`합니다. 


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

[Get-Az유지관리 업데이트를](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) 사용하여 보류 중인 업데이트가 있는지 확인합니다. VM의 Azure 구독이 로그인한 구독과 다른 경우 VM의 Azure 구독을 지정하는 데 사용합니다. `-subscription`

표시할 업데이트가 없는 경우 이 명령은 아무 것도 반환하지 않습니다. 그렇지 않으면 PSApplyUpdate 개체를 반환합니다.

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

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서는 출력이 가독성을 위해 테이블로 서식이 지정됩니다.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 보류 중인 업데이트를 확인합니다. 이 예제에서는 출력이 가독성을 위해 테이블로 서식이 지정됩니다. 리소스의 값을 사용자 고유의 값으로 바꿉니다.

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

[New-AzApplyUpdate를](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) 사용하여 보류 중인 업데이트를 적용합니다.

### <a name="isolated-vm"></a>격리된 VM

격리된 VM에 업데이트를 적용하도록 요청을 만듭니다.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

성공하면 이 명령은 개체를 반환합니다. `PSApplyUpdate` 명령의 Name 특성을 `Get-AzApplyUpdate` 사용하여 업데이트 상태를 확인할 수 있습니다. [업데이트 상태 확인을](#check-update-status)참조하십시오.

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 업데이트를 적용합니다.

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
[Get-AzApplyUpdate를](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) 사용하여 업데이트 상태를 확인합니다. 아래 표시된 명령은 매개 변수를 사용하여 `default` 최신 업데이트의 상태를 표시합니다. `-ApplyUpdateName` 업데이트 의 이름을 대체할 수 있습니다 [(New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) 명령에 의해 반환) 특정 업데이트의 상태를 얻을 수 있습니다.

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
LastUpdateTime은 자체 유지 관리 창이 사용되지 않은 경우 귀하 또는 플랫폼에서 시작된 업데이트가 완료된 시간입니다. 유지 관리 제어를 통해 적용된 업데이트가 없는 경우 기본값이 표시됩니다.

### <a name="isolated-vm"></a>격리된 VM

특정 가상 시스템에 대한 업데이트를 확인합니다.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>전용 호스트

전용 호스트에 대한 업데이트를 확인합니다.

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

[제거-Az유지 관리 구성을](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) 사용하여 유지 관리 구성을 삭제합니다.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>다음 단계
자세한 내용은 [유지 관리 및 업데이트를](maintenance-and-updates.md)참조하십시오.
