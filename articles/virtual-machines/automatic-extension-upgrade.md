---
title: Azure의 Vm 및 확장 집합에 대 한 자동 확장 업그레이드
description: Azure의 가상 머신 및 가상 머신 확장 집합에 대해 자동 확장 업그레이드를 사용 하도록 설정 하는 방법을 알아봅니다.
author: mayanknayar
ms.service: virtual-machines
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/12/2020
ms.author: manayar
ms.openlocfilehash: acc014785105d14c3109cfa420f0e9402ca3f534
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417557"
---
# <a name="preview-automatic-extension-upgrade-for-vms-and-scale-sets-in-azure"></a>미리 보기: Azure에서 Vm 및 확장 집합에 대 한 자동 확장 업그레이드

자동 확장 업그레이드는 Azure Vm 및 Azure Virtual Machine Scale Sets 미리 보기에서 사용할 수 있습니다. VM 또는 확장 집합에 대해 자동 확장 업그레이드를 사용 하도록 설정 하면 확장 게시자가 해당 확장에 대 한 새 버전을 릴리스할 때마다 확장이 자동으로 업그레이드 됩니다.

 자동 확장 업그레이드는 다음과 같은 기능을 제공 합니다.
- Azure Vm 및 Azure Virtual Machine Scale Sets에 대해 지원 됩니다. Service Fabric Virtual Machine Scale Sets 현재 지원 되지 않습니다.
- 업그레이드는 가용성 우선 배포 모델에 적용 됩니다 (아래에서 자세히 설명).
- Virtual Machine Scale Sets에 적용 되는 경우 Virtual Machine Scale Sets 가상 컴퓨터의 20%가 한 번의 일괄 처리로 업그레이드 됩니다 (일괄 처리당 최소 하나의 가상 컴퓨터에 적용 됨).
- 모든 VM 크기와 Windows 및 Linux 확장 모두에 대해 작동 합니다.
- 언제 든 지 자동 업그레이드를 옵트아웃 (opt out) 할 수 있습니다.
- 자동 확장 업그레이드는 모든 크기의 Virtual Machine Scale Sets에서 사용 하도록 설정할 수 있습니다.
- 지원 되는 각 확장은 개별적으로 등록 되며 자동으로 업그레이드할 확장을 선택할 수 있습니다.
- 모든 공용 클라우드 지역에서 지원 됩니다.


> [!IMPORTANT]
> 자동 확장 업그레이드는 현재 공개 미리 보기로 제공 됩니다. 아래에 설명 된 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저가 필요 합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.


## <a name="how-does-automatic-extension-upgrade-work"></a>자동 확장 업그레이드는 어떻게 작동 하나요?
확장 업그레이드 프로세스는 VM의 기존 확장 버전을 확장 게시자가 게시 한 새 확장 버전으로 바꿔 작동 합니다. 새 확장을 설치한 후 VM의 상태를 모니터링 합니다. 업그레이드 완료 후 5 분 이내에 VM의 상태가 정상 상태가 아니면 새 확장 버전이 이전 버전으로 롤백됩니다.

실패 한 확장 업데이트는 자동으로 다시 시도 됩니다. 사용자 개입 없이 며칠 마다 자동으로 재시도를 시도 합니다.


## <a name="upgrade-process-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 업그레이드 프로세스
1. 업그레이드 프로세스를 시작 하기 전에 오 케 스트레이 터는 전체 확장 집합에 있는 Vm의 20% 이상이 비정상 (어떤 이유로 든) 할 수 있도록 합니다.

2. 업그레이드 오 케 스트레이 터는 업그레이드에 사용 되는 VM 인스턴스의 일괄 처리를 식별 합니다 .이 일괄 처리는 하나의 가상 컴퓨터에 대 한 최소 일괄 처리 크기에 따라 총 VM 수의 최대 20%를 포함 합니다.

3. 응용 프로그램 상태 프로브 또는 응용 프로그램 상태 확장이 구성 된 확장 집합의 경우 업그레이드는 다음 일괄 처리를 업그레이드 하기 위해 이동 하기 전에 VM이 정상 상태가 될 때까지 최대 5 분 (또는 정의 된 상태 프로브 구성)을 대기 합니다. 업그레이드 후 VM이 상태를 복구 하지 않으면 기본적으로 VM에 대 한 이전 확장 버전이 다시 설치 됩니다.

4. 업그레이드 orchestrator는 업그레이드 후 비정상 상태가 되는 Vm의 백분율도 추적 합니다. 업그레이드 프로세스 중에 업그레이드된 인스턴스의 20% 이상이 비정상 상태가 되면 업그레이드가 중지됩니다.

확장 집합의 모든 인스턴스가 업그레이드될 때까지 위의 프로세스가 계속됩니다.

확장 집합 업그레이드 orchestrator는 모든 일괄 처리를 업그레이드 하기 전에 전체 확장 집합 상태를 확인 합니다. 일괄 처리를 업그레이드 하는 동안 확장 집합 가상 컴퓨터의 상태에 영향을 줄 수 있는 다른 동시 계획 또는 계획 되지 않은 유지 관리 작업이 있을 수 있습니다. 이러한 경우 확장 집합 인스턴스의 20% 이상이 비정상 상태가 되 면 확장 집합 업그레이드는 현재 일괄 처리의 끝에서 중지 됩니다.


### <a name="availability-first-updates"></a>가용성 우선 업데이트
Platform 오케스트레이션 업데이트에 대 한 가용성 우선 모델은 Azure의 가용성 구성이 여러 가용성 수준에서 적용 되도록 합니다.

업데이트를 수행 하는 가상 머신 그룹의 경우 Azure platform은 업데이트를 오케스트레이션 합니다.

**지역에서:**
- Azure 전체 배포 오류를 방지 하기 위해 업데이트는 전체 Azure에서 단계적으로 이동 됩니다.
- ' T r u e '는 하나 이상의 영역을 구성할 수 있으며, 한 단계의 적격 Vm이 성공적으로 업데이트 되는 경우에만 업데이트가 여러 단계로 이동 합니다.
- 지리적으로 쌍을 이루는 지역은 동시에 업데이트 되지 않으며 동일한 지역에 있을 수 없습니다.
- 업데이트 성공은 VM 사후 업데이트의 상태를 추적 하 여 측정 합니다. Vm 상태는 VM에 대 한 플랫폼 상태 표시기를 통해 추적 됩니다. Virtual Machine Scale Sets 경우에는 확장 집합에 적용 된 경우 응용 프로그램 상태 프로브 또는 응용 프로그램 상태 확장을 통해 VM 상태가 추적 됩니다.

**지역 내에서:**
- 다른 가용성 영역의 Vm은 동시에 업데이트 되지 않습니다.
- 가용성 집합의 일부가 아닌 단일 Vm은 구독의 모든 Vm에 대 한 동시 업데이트를 방지 하기 위해 최상의 노력을 기준으로 일괄 처리 됩니다.  

**' Set ' 내에서:**
- 공통 가용성 집합 또는 확장 집합의 모든 Vm은 동시에 업데이트 되지 않습니다.  
- 일반 가용성 집합의 Vm은 업데이트 도메인 경계 내에서 업데이트 되며 여러 업데이트 도메인의 Vm은 동시에 업데이트 되지 않습니다.  
- 공통 가상 머신 확장 집합의 Vm은 일괄 처리로 그룹화 되 고 업데이트 도메인 경계 내에서 업데이트 됩니다.


## <a name="supported-extensions"></a>지원 되는 확장
자동 확장 업그레이드의 미리 보기에서는 다음 확장을 지원 합니다. 이러한 확장은 주기적으로 추가 됩니다.
- Dependency Agent – [Windows](./extensions/agent-dependency-windows.md) 및 [Linux](./extensions/agent-dependency-linux.md)
- [응용 프로그램 상태 확장](../virtual-machine-scale-sets/virtual-machine-scale-sets-health-extension.md) – Windows 및 Linux


## <a name="enabling-preview-access"></a>미리 보기 액세스 사용
미리 보기 기능을 사용 하도록 설정 하려면 아래에서 설명 하는 것 처럼 자동 구독 당 기능 자동 **Extensionupgradepreview** 에 대 한 일회성 옵트인이 필요 합니다.

### <a name="rest-api"></a>REST API
다음 예제에서는 구독에 대해 미리 보기를 사용 하도록 설정 하는 방법을 설명 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview/register?api-version=2015-12-01`
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/AutomaticExtensionUpgradePreview?api-version=2015-12-01`
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AutomaticExtensionUpgradePreview -ProviderNamespace Microsoft.Compute
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli"></a>Azure CLI
[Az feature register](/cli/azure/feature#az-feature-register) 를 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name AutomaticExtensionUpgradePreview
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```


## <a name="enabling-automatic-extension-upgrade"></a>자동 확장 업그레이드 사용
확장에 대해 자동 확장 업그레이드를 사용 하도록 설정 하려면 *Enableautomatic upgrade* 속성을 *true* 로 설정 하 고 모든 확장 정의에 개별적으로 추가 해야 합니다.


### <a name="rest-api-for-virtual-machines"></a>Virtual Machines에 대 한 REST API
Azure VM에서 확장 (이 예제에서는 Dependency Agent 확장)에 대해 자동 확장 업그레이드를 사용 하도록 설정 하려면 다음을 사용 합니다.

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachines/<vmName>/extensions/<extensionName>?api-version=2019-12-01`
```

```json
{    
    "name": "extensionName",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "location": "<location>",
    "properties": {
        "autoUpgradeMinorVersion": true,
        "enableAutomaticUpgrade": true, 
        "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
        "type": "DependencyAgentWindows",
        "typeHandlerVersion": "9.5"
        }
}
```

### <a name="rest-api-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 REST API
확장 집합 모델에 확장을 추가 하려면 다음을 사용 합니다.

```
PUT on `/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupName>/providers/Microsoft.Compute/virtualMachineScaleSets/<vmssName>?api-version=2019-12-01`
```

```json
{
   "location": "<location>",
   "properties": {
        "virtualMachineProfile": {
            "extensionProfile": {
                "extensions": [
                {
                "name": "<extensionName>",
                  "properties": {
                        "autoUpgradeMinorVersion": true,
                        "enableAutomaticUpgrade": true,
                    "publisher": "Microsoft.Azure.Monitoring.DependencyAgent",
                    "type": "DependencyAgentWindows",
                    "typeHandlerVersion": "9.5"
                    }
                }
                ]
            }
        }
    }
}
```

### <a name="azure-powershell-for-virtual-machines"></a>Virtual Machines에 대 한 Azure PowerShell
[AzVMExtension](/powershell/module/az.compute/set-azvmextension) cmdlet을 사용 합니다.

```azurepowershell-interactive
Set-AzVMExtension -ExtensionName "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -ExtensionType "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -Location WestUS `
    -EnableAutomaticUpgrade $true
```


### <a name="azure-powershell-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 Azure PowerShell
[AzVmssExtension](/powershell/module/az.compute/add-azvmssextension) cmdlet을 사용 하 여 확장 집합 모델에 확장을 추가 합니다.

```azurepowershell-interactive
Add-AzVmssExtension -VirtualMachineScaleSet $vmss
    -Name "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Publisher "Microsoft.Azure.Monitoring.DependencyAgent" `
    -Type "DependencyAgentWindows" `
    -TypeHandlerVersion 9.5 `
    -EnableAutomaticUpgrade $true
```

확장을 추가한 후 [AzVmss](/powershell/module/az.compute/update-azvmss) 를 사용 하 여 확장 집합을 업데이트 합니다.


### <a name="azure-cli-for-virtual-machines"></a>Virtual Machines용 Azure CLI
[Az vm extension set](/cli/azure/vm/extension#az_vm_extension_set) cmdlet을 사용 합니다.

```azurecli-interactive
az vm extension set \
    --resource-group myResourceGroup \
    --vm-name myVM \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

### <a name="azure-cli-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets에 대 한 Azure CLI
[Az vmss extension set](/cli/azure/vmss/extension#az_vmss_extension_set) cmdlet을 사용 하 여 확장 집합 모델에 확장을 추가 합니다.

```azurecli-interactive
az vmss extension set \
    --resource-group myResourceGroup \
    --vmss-name myVMSS \
    --name DependencyAgentLinux \
    --publisher Microsoft.Azure.Monitoring.DependencyAgent \
    --version 9.5 \
    --enable-auto-upgrade true
```

## <a name="extension-upgrades-with-multiple-extensions"></a>확장을 여러 개 포함 하는 확장 업그레이드

VM 또는 가상 머신 확장 집합에 자동 확장 업그레이드를 사용 하지 않는 다른 확장 외에도 자동 확장 업그레이드를 사용 하는 여러 확장이 있을 수 있습니다.  

가상 컴퓨터에 대해 여러 확장 업그레이드를 사용할 수 있는 경우 업그레이드를 함께 일괄 처리할 수 있습니다. 그러나 각 확장 업그레이드는 가상 컴퓨터에 개별적으로 적용 됩니다. 한 확장의 실패가 업그레이드 될 수 있는 다른 확장에 영향을 주지 않습니다. 예를 들어 두 개의 확장이 업그레이드를 위해 예약 되 고 첫 번째 확장 업그레이드가 실패 한 경우 두 번째 확장은 계속 업그레이드 됩니다.

자동 확장 업그레이드는 VM 또는 가상 머신 확장 집합에 [확장 시퀀싱](../virtual-machine-scale-sets/virtual-machine-scale-sets-extension-sequencing.md)으로 구성 된 확장이 여러 개 있는 경우에도 적용할 수 있습니다. 확장 시퀀싱은 VM의 첫 번째 배포에 적용 되며 확장에 대 한 모든 후속 확장 업그레이드는 독립적으로 적용 됩니다.


## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [응용 프로그램 상태 확장에 대 한 자세한 정보](./windows/automatic-vm-guest-patching.md)
