---
title: Azure Vm에 대 한 자동 VM 게스트 패치
description: Azure에서 가상 컴퓨터를 자동으로 패치 하는 방법에 대해 알아봅니다.
author: mayanknayar
ms.service: virtual-machines
ms.subservice: automatic-guest-patching
ms.collection: windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 02/17/2021
ms.author: manayar
ms.openlocfilehash: 276762bc2b8624f687cbb77e1af771478791a57b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/20/2021
ms.locfileid: "101679800"
---
# <a name="preview-automatic-vm-guest-patching-for-azure-vms"></a>미리 보기: Azure Vm에 대 한 자동 VM 게스트 패치

Azure Vm에 대해 자동 VM 게스트 패치를 사용 하도록 설정 하면 안전 하 고 자동으로 가상 머신을 패치 하 여 보안을 준수 하면서 업데이트를 쉽게 관리할 수 있습니다.

자동 VM 게스트 패치는 다음과 같은 특징이 있습니다.
- *중요* 또는 *보안* 으로 분류 된 패치는 자동으로 다운로드 되어 VM에 적용 됩니다.
- 패치는 VM의 표준 시간대에서 사용량이 적은 시간에 적용 됩니다.
- 패치 오케스트레이션은 Azure에 의해 관리 되며, 패치는 [가용성 우선 원칙](#availability-first-patching)에 따라 적용 됩니다.
- 플랫폼 상태 신호를 통해 결정 되는 가상 머신 상태는 패치 실패를 감지 하기 위해 모니터링 됩니다.
- 모든 VM 크기에 사용할 수 있습니다.

> [!IMPORTANT]
> 자동 VM 게스트 패치는 현재 공개 미리 보기로 제공 됩니다. 아래에 설명 된 공개 미리 보기 기능을 사용 하려면 옵트인 프로시저가 필요 합니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="how-does-automatic-vm-guest-patching-work"></a>자동 VM 게스트 패치는 어떻게 작동 하나요?

VM에서 자동 VM 게스트 패치를 사용 하는 경우 사용 가능한 *중요* 및 *보안* 패치가 vm에 자동으로 다운로드 되 고 적용 됩니다. 이 프로세스는 새 패치가 출시 될 때마다 매월 자동으로 시작 됩니다. 패치 평가 및 설치는 자동으로 수행 되며, 필요에 따라이 프로세스에는 VM을 다시 부팅 하는 작업이 포함 됩니다.

VM은 해당 VM에 대 한 적용 가능한 패치를 결정 하기 위해 며칠 마다 정기적으로 평가 되 고 30 일 기간 내에 여러 번 평가 됩니다. Vm에 대 한 사용량이 적은 시간에 VM의 모든 날에 패치를 설치할 수 있습니다. 이러한 자동 평가를 통해 누락 된 패치가 가능한 가장 빠른 기회에서 검색 됩니다.

패치는 아래에 설명 된 가용성 우선 오케스트레이션 이후 월별 패치 릴리스의 30 일 이내에 설치 됩니다. 패치는 vm의 표준 시간대에 따라 VM에 대 한 사용률이 낮은 시간에만 설치 됩니다. 패치를 자동으로 설치 하는 데 사용량이 많은 시간 동안 VM이 실행 중 이어야 합니다. 정기적으로 평가 하는 동안 VM의 전원이 꺼진 경우 vm이 자동으로 평가 되 고 해당 하는 패치는 VM의 전원이 켜져 있을 때 다음 정기 평가 (일반적으로 며칠 이내) 동안 자동으로 설치 됩니다.

*중요* 또는 *보안* 으로 분류 되지 않은 정의 업데이트 및 기타 패치는 자동 VM 게스트 패치를 통해 설치 되지 않습니다. 다른 패치 분류를 사용 하 여 패치를 설치 하거나 사용자 지정 유지 관리 기간 내에 패치 설치를 예약 하려면 [업데이트 관리](./windows/tutorial-config-management.md#manage-windows-updates)를 사용할 수 있습니다.

### <a name="availability-first-patching"></a>가용성 우선 패치

자동 VM 게스트 패치가 활성화 된 모든 Vm에 대해 Azure에서 패치 설치 프로세스를 전역적으로 오케스트레이션 합니다. 이 오케스트레이션은 Azure에서 제공 하는 다양 한 가용성 수준에서 가용성 우선 원칙을 따릅니다.

업데이트를 수행 하는 가상 머신 그룹의 경우 Azure platform은 업데이트를 오케스트레이션 합니다.

**지역에서:**
- 월간 업데이트는 글로벌 배포 실패를 방지 하기 위해 전 세계적으로 Azure 전체에 걸쳐 오케스트레이션 됩니다.
- 한 단계에는 하나 이상의 지역이 포함 될 수 있으며, 업데이트는 단계에서 적합 한 Vm이 성공적으로 업데이트 된 경우에만 다음 단계로 이동 합니다.
- 지리적으로 쌍을 이루는 지역은 동시에 업데이트 되지 않으며 동일한 지역에 있을 수 없습니다.
- 업데이트 성공은 VM의 상태 사후 업데이트를 추적 하 여 측정 합니다. Vm 상태는 VM에 대 한 플랫폼 상태 표시기를 통해 추적 됩니다.

**지역 내에서:**
- 다른 가용성 영역의 Vm은 동시에 업데이트 되지 않습니다.
- 가용성 집합의 일부가 아닌 Vm은 구독의 모든 Vm에 대 한 동시 업데이트를 방지 하기 위해 최상의 노력을 기준으로 일괄 처리 됩니다.

**가용성 집합 내에서:**
- 공통 가용성 집합의 모든 Vm은 동시에 업데이트 되지 않습니다.
-   일반 가용성 집합의 Vm은 업데이트 도메인 경계 내에서 업데이트 되며 여러 업데이트 도메인의 Vm은 동시에 업데이트 되지 않습니다.

특정 VM은 월별 패치 주기 사이에 다른 일괄 처리에서 선택할 수 있으므로 지정 된 VM에 대 한 패치 설치 날짜는 월별로 다를 수 있습니다.

### <a name="which-patches-are-installed"></a>설치 된 패치는 무엇입니까?
설치 된 패치는 VM의 롤아웃 단계에 따라 달라 집니다. 매월 개별 VM에 대해 평가 된 모든 보안 및 중요 패치가 해당 VM에 대해 설치 되는 새 글로벌 롤아웃이 시작 됩니다. 롤아웃은 모든 Azure 지역에서 일괄 처리 됩니다 (위의 가용성 우선 패치 섹션에서 설명).

설치할 정확한 패치 집합은 OS 유형 및 평가 타이밍을 포함 하 여 VM 구성에 따라 달라 집니다. 서로 다른 시간에 patch 오케스트레이션이 다른 지역에 도달할 때 사용할 수 있는 패치가 더 많이 있는 경우 다른 지역에 있는 두 개의 동일한 Vm에서 다른 패치를 설치할 수 있습니다. 마찬가지로 동일한 지역 내에 있지만 다른 시간 (가용성 영역 또는 가용성 집합 일괄 처리로 인해)으로 평가 되는 동일한 지역 내에 있는 Vm은 서로 다른 패치를 얻을 수 있습니다.

자동 VM 게스트 패치는 패치 원본을 구성 하지 않으므로 공용 리포지토리와 개인 리포지토리와 같은 다른 패치 원본으로 구성 된 두 개의 유사한 Vm은 설치 된 정확한 패치 집합의 차이를 확인할 수도 있습니다.

고정 된 흐름에서 패치를 해제 하는 OS 유형의 경우 OS에 대 한 공용 리포지토리로 구성 된 Vm은 한 달에 다양 한 출시 단계에서 동일한 패치 집합을 받게 될 것입니다. 예를 들어, 공용 Windows 업데이트 리포지토리로 구성 된 Windows Vm입니다.

매월 새 롤아웃이 트리거되면 vm이 사용량이 적은 시간에 전원이 켜진 경우 매월 하나 이상의 패치 출시를 수신 합니다. 이렇게 하면 VM이 월 기준으로 사용 가능한 최신 보안 및 중요 패치를 사용 하 여 패치 됩니다. 설치 된 패치 집합의 일관성을 보장 하기 위해 Vm을 구성 하 여 사용자 고유의 개인 리포지토리에서 패치를 평가 하 고 다운로드할 수 있습니다.

## <a name="supported-os-images"></a>지원되는 OS 이미지
특정 OS 플랫폼 이미지에서 만든 Vm만 현재 미리 보기에서 지원 됩니다. 사용자 지정 이미지는 현재 미리 보기에서 지원 되지 않습니다.

현재 지원 되는 플랫폼 Sku는 다음과 같습니다 .이는 주기적으로 추가 됩니다.

| 게시자               | OS 제품      |  SKU               |
|-------------------------|---------------|--------------------|
| Canonical  | UbuntuServer | 18.04-LTS |
| Redhat  | RHEL | 7.x |
| MicrosoftWindowsServer  | WindowsServer | 2012-R2-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter    |
| MicrosoftWindowsServer  | WindowsServer | 2016-Datacenter-Server-Core |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter |
| MicrosoftWindowsServer  | WindowsServer | 2019-Datacenter-Core |

## <a name="patch-orchestration-modes"></a>패치 오케스트레이션 모드
Azure의 Vm은 이제 다음과 같은 패치 오케스트레이션 모드를 지원 합니다.

**자동 Byplatform:**
- 이 모드는 Linux 및 Windows Vm 모두에서 지원 됩니다.
- 이 모드에서는 가상 머신에 대해 자동 VM 게스트 패치를 사용 하도록 설정 하 고 후속 패치 설치는 Azure에 의해 오케스트레이션 됩니다.
- 이 모드는 가용성 우선 패치를 적용 하는 데 필요 합니다.
- 이 모드는 위의 지원 되는 OS 플랫폼 이미지를 사용 하 여 만든 Vm에 대해서만 지원 됩니다.
- Windows Vm의 경우이 모드를 설정 하면 중복을 방지 하기 위해 Windows 가상 머신에서 기본 자동 업데이트도 사용 하지 않도록 설정 됩니다.
- Linux Vm에서이 모드를 사용 하려면 VM 템플릿에서 속성을 설정 `osProfile.linuxConfiguration.patchSettings.patchMode=AutomaticByPlatform` 합니다.
- Windows Vm에서이 모드를 사용 하려면 속성을 설정 하 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` 고 VM 템플릿에서 속성을 설정 합니다  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform` .

**자동 Byos:**
- 이 모드는 Windows Vm에 대해서만 지원 됩니다.
- 이 모드는 Windows 가상 머신에서 자동 업데이트를 사용 하도록 설정 하며, 패치는 자동 업데이트를 통해 VM에 설치 됩니다.
- 이 모드는 가용성 우선 패치를 지원 하지 않습니다.
- Windows VM에 대해 다른 패치 모드가 지정 되지 않은 경우이 모드는 기본적으로 설정 됩니다.
- Windows Vm에서이 모드를 사용 하려면 속성을 설정 하 `osProfile.windowsConfiguration.enableAutomaticUpdates=true` 고 VM 템플릿에서 속성을 설정 합니다  `osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByOS` .

**수동:**
- 이 모드는 Windows Vm에 대해서만 지원 됩니다.
- 이 모드는 Windows 가상 머신에서 자동 업데이트를 사용 하지 않도록 설정 합니다.
- 이 모드는 가용성 우선 패치를 지원 하지 않습니다.
- 사용자 지정 패치 솔루션을 사용 하는 경우이 모드를 설정 해야 합니다.
- Windows Vm에서이 모드를 사용 하려면 속성을 설정 하 `osProfile.windowsConfiguration.enableAutomaticUpdates=false` 고 VM 템플릿에서 속성을 설정 합니다  `osProfile.windowsConfiguration.patchSettings.patchMode=Manual` .

**ImageDefault:**
- 이 모드는 Linux Vm에 대해서만 지원 됩니다.
- 이 모드는 가용성 우선 패치를 지원 하지 않습니다.
- 이 모드는 VM을 만드는 데 사용 되는 이미지의 기본 패치 구성을 적용 합니다.
- Linux VM에 대해 다른 패치 모드가 지정 되지 않은 경우이 모드는 기본적으로 설정 됩니다.
- Linux Vm에서이 모드를 사용 하려면 VM 템플릿에서 속성을 설정 `osProfile.linuxConfiguration.patchSettings.patchMode=ImageDefault` 합니다.

> [!NOTE]
>Windows Vm의 경우에는 `osProfile.windowsConfiguration.enableAutomaticUpdates` 현재 VM을 처음 만들 때만 속성을 설정할 수 있습니다. 수동 모드에서 자동 모드로 또는 수동 모드에서 수동 모드로 전환 하는 것은 현재 지원 되지 않습니다. 자동 Byos 모드에서 AutomaticByPlatfom 모드로 전환 하는 것이 지원 됩니다.

## <a name="requirements-for-enabling-automatic-vm-guest-patching"></a>자동 VM 게스트 패치를 사용 하기 위한 요구 사항

- 가상 컴퓨터에는 [Windows](./extensions/agent-windows.md) 또는 [LINUX](./extensions/agent-linux.md) 용 Azure VM 에이전트가 설치 되어 있어야 합니다.
- Linux Vm의 경우 Azure Linux 에이전트는 2.2.53.1 버전 이상 이어야 합니다. 현재 버전이 필요한 버전 보다 낮은 경우 [Linux 에이전트를 업데이트](./extensions/update-linux-agent.md) 합니다.
- Windows Vm의 경우 가상 머신에서 Windows 업데이트 서비스가 실행 중 이어야 합니다.
- 가상 컴퓨터에서 구성 된 업데이트 끝점에 액세스할 수 있어야 합니다. 가상 머신이 Linux 용 개인 리포지토리를 사용 하도록 구성 되어 있거나 Windows Vm에 대해 WSUS (Windows Server Update Services)를 사용 하도록 구성 된 경우 관련 업데이트 끝점에 액세스할 수 있어야 합니다.
- Compute API 버전 2020-12-01 이상을 사용 합니다. 계산 API 버전 2020-06-01은 기능이 제한 된 Windows Vm에 사용할 수 있습니다.

미리 보기 기능을 사용 하도록 설정 하려면 다음 섹션에 설명 된 대로 **InGuestAutoPatchVMPreview** 및 **InGuestPatchVMPreview** 기능에 대해 일회성 옵트인이 필요 합니다.

### <a name="rest-api"></a>REST API
다음 예제에서는 구독에 대해 미리 보기를 사용 하도록 설정 하는 방법을 설명 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview/register?api-version=2015-12-01`
```
```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview/register?api-version=2015-12-01`
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestAutoPatchVMPreview?api-version=2015-12-01`
```
```
GET on `/subscriptions/{subscriptionId}/providers/Microsoft.Features/providers/Microsoft.Compute/features/InGuestPatchVMPreview?api-version=2015-12-01`
```
구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```
POST on `/subscriptions/{subscriptionId}/providers/Microsoft.Compute/register?api-version=2020-06-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) cmdlet을 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Register-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName InGuestAutoPatchVMPreview -ProviderNamespace Microsoft.Compute
Get-AzProviderFeature -FeatureName InGuestPatchVMPreview -ProviderNamespace Microsoft.Compute
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

### <a name="azure-cli-20"></a>Azure CLI 2.0
[Az feature register](/cli/azure/feature#az-feature-register) 를 사용 하 여 구독에 대 한 미리 보기를 사용 하도록 설정 합니다.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature register --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

기능 등록에는 최대 15 분이 걸릴 수 있습니다. 등록 상태를 확인하는 방법은 다음과 같습니다.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name InGuestAutoPatchVMPreview `
az feature show --namespace Microsoft.Compute --name InGuestPatchVMPreview
```

구독에 대 한 기능을 등록 한 후에는 계산 리소스 공급자에 변경 내용을 전파 하 여 옵트인 프로세스를 완료 합니다.

```azurecli-interactive
az provider register --namespace Microsoft.Compute
```

## <a name="enable-automatic-vm-guest-patching"></a>자동 VM 게스트 패치 사용
Windows VM에서 자동 VM 게스트 패치를 사용 하도록 설정 하려면 VM 템플릿 정의에서 *osProfile* 속성을 *true* 로 설정 해야 합니다. VM을 만들 때만이 속성을 설정할 수 있습니다. Linux Vm에는이 추가 속성을 적용할 수 없습니다.

### <a name="rest-api-for-linux-vms"></a>Linux Vm에 대 한 REST API
다음 예제에서는 자동 VM 게스트 패치를 사용 하도록 설정 하는 방법을 설명 합니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-12-01`
```

```json
{
  "properties": {
    "osProfile": {
      "linuxConfiguration": {
        "provisionVMAgent": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="rest-api-for-windows-vms"></a>Windows Vm에 대 한 REST API
다음 예제에서는 자동 VM 게스트 패치를 사용 하도록 설정 하는 방법을 설명 합니다.

```
PUT on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine?api-version=2020-06-01`
```

```json
{
  "properties": {
    "osProfile": {
      "windowsConfiguration": {
        "provisionVMAgent": true,
        "enableAutomaticUpdates": true,
        "patchSettings": {
          "patchMode": "AutomaticByPlatform"
        }
      }
    }
  }
}
```

### <a name="azure-powershell-for-windows-vms"></a>Windows VM용 Azure PowerShell 샘플
VM을 만들거나 업데이트할 때 [AzVMOperatingSystem](/powershell/module/az.compute/set-azvmoperatingsystem) cmdlet을 사용 하 여 자동 vm 게스트 패치를 사용 하도록 설정 합니다.

```azurepowershell-interactive
Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate -PatchMode "AutomaticByPlatform"
```

### <a name="azure-cli-for-windows-vms"></a>Windows Vm에 대 한 Azure CLI
새 VM을 만들 때 [az vm create](/cli/azure/vm#az-vm-create) 를 사용 하 여 자동 vm 게스트 패치를 사용 하도록 설정 합니다. 다음 예제에서는 *Myvm* 이라는 리소스 그룹에서 *MYVM* 이라는 vm에 대 한 자동 VM 게스트 패치를 구성 합니다.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image Win2019Datacenter --enable-agent --enable-auto-update --patch-mode AutomaticByPlatform
```

기존 VM을 수정 하려면 [az VM update](/cli/azure/vm#az-vm-update) 를 사용 합니다.

```azurecli-interactive
az vm update --resource-group myResourceGroup --name myVM --set osProfile.windowsConfiguration.enableAutomaticUpdates=true osProfile.windowsConfiguration.patchSettings.patchMode=AutomaticByPlatform
```

## <a name="enablement-and-assessment"></a>활성화 및 평가

> [!NOTE]
>Vm의 사용량이 적은 시간에 사용이 완료 되 면 VM에서 자동 VM 게스트 업데이트를 사용 하는 데 3 시간 이상 걸릴 수 있습니다. 평가 및 패치 설치는 사용량이 적은 시간에만 발생 하므로 패치를 적용 하기 위해 사용량이 적은 시간에도 VM이 실행 되 고 있어야 합니다.

VM에 대해 자동 VM 게스트 패치를 사용 하도록 설정 하는 경우 형식의 VM 확장은 `Microsoft.CPlat.Core.LinuxPatchExtension` LINUX vm에 설치 되 고 형식의 vm 확장은 `Microsoft.CPlat.Core.WindowsPatchExtension` Windows vm에 설치 됩니다. 이 확장은 자동 VM 게스트 패치 프로세스의 일부로 Azure 플랫폼에서 관리 되므로 수동으로 설치 하거나 업데이트할 필요가 없습니다.

Vm의 사용량이 적은 시간에 사용이 완료 되 면 VM에서 자동 VM 게스트 업데이트를 사용 하는 데 3 시간 이상 걸릴 수 있습니다. 확장은 VM의 사용량이 적은 시간에도 설치 되 고 업데이트 됩니다. 사용이 중지 될 때까지 VM의 사용량이 적은 시간을 종료 하는 경우 사용 가능한 다음 시간 외 시간에 사용 프로세스가 다시 시작 됩니다.

자동 업데이트는 대부분의 시나리오에서 사용 하지 않도록 설정 되며, 패치 설치는 앞으로 확장을 통해 수행 됩니다. 다음 조건이 적용 됩니다.
- Windows VM이 자동 Windows 업데이트 자동으로 자동으로 설정 되어 있는 경우에는 확장을 설치할 때 VM에 대해 자동 Windows 업데이트 꺼집니다.
- Ubuntu Vm의 경우 자동 VM 게스트 패치가 사용 하도록 설정 되 면 기본 자동 업데이트가 자동으로 사용 하지 않도록 설정 됩니다.
- RHEL의 경우 자동 업데이트를 수동으로 사용 하지 않도록 설정 해야 합니다 (미리 보기 제한). 다음 코드를 실행합니다.

```
systemctl stop packagekit
```

```
systemctl mask packagekit
```

자동 VM 게스트 패치가 완료 되었으며 패치 확장이 VM에 설치 되어 있는지 확인 하려면 VM의 인스턴스 보기를 검토 하면 됩니다. 사용 프로세스가 완료 되 면 확장이 설치 되 고 VM에 대 한 평가 결과가에서 사용 가능 하 게 됩니다 `patchStatus` . VM의 인스턴스 보기는 아래에 설명 된 대로 여러 가지 방법으로 액세스할 수 있습니다.

### <a name="rest-api"></a>REST API

```
GET on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/instanceView?api-version=2020-12-01`
```
### <a name="azure-powershell"></a>Azure PowerShell
매개 변수와 함께 [new-azvm](/powershell/module/az.compute/get-azvm) cmdlet을 사용 `-Status` 하 여 VM의 인스턴스 보기에 액세스 합니다.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" -Status
```

PowerShell은 현재 patch 확장에 대 한 정보만 제공 합니다. 에 대 한 정보 `patchStatus` 는 PowerShell을 통해 곧 제공 될 예정입니다.

### <a name="azure-cli"></a>Azure CLI
[Az vm get instance-view](/cli/azure/vm#az-vm-get-instance-view) 를 사용 하 여 vm의 인스턴스 보기에 액세스할 수 있습니다.

```azurecli-interactive
az vm get-instance-view --resource-group myResourceGroup --name myVM
```

### <a name="understanding-the-patch-status-for-your-vm"></a>VM에 대 한 패치 상태 이해

`patchStatus`인스턴스 보기 응답의 섹션에서는 VM에 대 한 최신 평가 및 마지막 패치 설치에 대 한 세부 정보를 제공 합니다.

VM에 대 한 평가 결과는 섹션에서 검토할 수 있습니다 `availablePatchSummary` . 자동 VM 게스트 패치를 사용 하는 VM에 대 한 평가는 주기적으로 수행 됩니다. 및 결과에서 평가 후 사용 가능한 패치의 수입니다 `criticalAndSecurityPatchCount` `otherPatchCount` . 자동 VM 게스트 패치는 *중요* 및 *보안* 패치 분류에서 평가 된 모든 패치를 설치 합니다. 다른 평가 된 패치는 모두 건너뜁니다.

VM에 대 한 패치 설치 결과는 섹션에서 검토할 수 있습니다 `lastPatchInstallationSummary` . 이 섹션에서는 설치, 보류, 실패 또는 건너뛴 패치 수를 비롯 하 여 VM에서 마지막으로 패치를 설치 하는 방법에 대해 자세히 설명 합니다. 패치는 VM의 사용량이 적은 시간 유지 관리 기간 동안에만 설치 됩니다. 보류 중 및 실패 한 패치는 다음 사용량이 적은 시간 유지 관리 기간 동안 자동으로 다시 시도 됩니다.

## <a name="on-demand-patch-assessment"></a>주문형 패치 평가
VM에 대해 자동 VM 게스트 패치를 이미 사용 하도록 설정한 경우 vm의 사용량이 적은 시간에 VM에 대 한 주기적인 패치 평가가 수행 됩니다. 이 프로세스는 자동으로 진행 되며이 문서의 앞부분에서 설명한 대로 VM의 인스턴스 보기를 통해 최신 평가 결과를 검토할 수 있습니다. 언제 든 지 VM에 대 한 주문형 패치 평가를 트리거할 수도 있습니다. 패치 평가를 완료 하는 데 몇 분 정도 걸릴 수 있으며 VM의 인스턴스 보기에서 최신 평가의 상태가 업데이트 됩니다.

미리 보기 기능을 사용 하도록 설정 하려면 구독 당 기능 **InGuestPatchVMPreview** 에 대 한 일회성 옵트인이 필요 합니다. 이 기능 미리 보기는 **InGuestAutoPatchVMPreview** 에 대해 이전에 수행 된 자동 VM 게스트 패치 기능 등록과 다릅니다. 추가 기능 미리 보기를 사용 하도록 설정 하는 것은 별도의 추가 요구 사항입니다. 주문형 패치 평가를 위한 기능 미리 보기는 이전에 자동 VM 게스트 패치 적용에 대해 설명한 [미리 보기 사용 프로세스](automatic-vm-guest-patching.md#requirements-for-enabling-automatic-vm-guest-patching) 에 따라 사용 하도록 설정할 수 있습니다.

> [!NOTE]
>주문형 패치 평가는 자동으로 패치 설치를 트리거하지 않습니다. 자동 VM 게스트 패치를 사용 하도록 설정한 경우이 문서의 앞부분에서 설명한 가용성 우선 패치 프로세스에 따라 vm의 사용량이 적은 시간에 VM에 대 한 평가 및 적용 가능한 패치가 설치 됩니다.

### <a name="rest-api"></a>REST API
```
POST on `/subscriptions/subscription_id/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVirtualMachine/assessPatches?api-version=2020-12-01`
```

### <a name="azure-powershell"></a>Azure PowerShell
[AzVmPatchAssessment](/powershell/module/az.compute/invoke-azvmpatchassessment) cmdlet을 사용 하 여 가상 컴퓨터에 대 한 사용 가능한 패치를 평가 합니다.

```azurepowershell-interactive
Invoke-AzVmPatchAssessment -ResourceGroupName "myResourceGroup" -VMName "myVM"
```

### <a name="azure-cli"></a>Azure CLI
[Az vm 평가-패치](/cli/azure/vm#az-vm-assess-patches) 를 사용 하 여 가상 머신에 사용 가능한 패치를 평가 합니다.

```azurecli-interactive
az vm assess-patches --resource-group myResourceGroup --name myVM
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [Windows 가상 머신을 만들고 관리 하는 방법에 대 한 자세한 정보](./windows/tutorial-manage-vm.md)
