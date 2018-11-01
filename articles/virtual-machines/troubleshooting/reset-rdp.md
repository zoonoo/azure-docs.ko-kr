---
title: Windows VM에서 원격 데스크톱 서비스 또는 해당 관리자 암호 다시 설정| Microsoft Docs
description: Azure Portal 또는 Azure PowerShell을 사용하여 Windows VM에서 계정 암호 또는 원격 데스크톱 서비스를 다시 설정하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 6f825d3419a8aebeae4500a383740058e984a2f2
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50420832"
---
# <a name="reset-remote-desktop-services-or-its-administrator-password-in-a-windows-vm"></a>Windows VM에서 원격 데스크톱 서비스 또는 해당 관리자 암호 다시 설정
Windows VM(가상 머신)에 연결할 수 없는 경우 로컬 관리자 암호를 다시 설정하거나 원격 데스크톱 서비스 구성을 다시 설정할 수 있습니다(Windows 도메인 컨트롤러에서는 지원되지 않음). 암호를 다시 설정하려면 Azure Portal이나 Azure PowerShell의 VM 액세스 확장을 사용할 수 있습니다. VM에 로그인한 후 해당 로컬 관리자에 대한 암호를 다시 설정합니다.  
PowerShell을 사용하는 경우 [최신 PowerShell 모듈을 설치 및 구성](/powershell/azure/overview)하고 Azure 구독에 로그인해야 합니다. [클래식 배포 모델을 사용하여 만든 VM에 대해 이러한 단계를 수행](https://docs.microsoft.com/azure/virtual-machines/windows/classic/reset-rdp)할 수도 있습니다.

다음과 같은 방법으로 원격 데스크톱 서비스 및 자격 증명을 다시 설정할 수 있습니다.

- [Azure Portal을 사용하여 다시 설정](#reset-by-using-the-azure-portal)
- [VMAccess 확장 및 PowerShell을 사용하여 다시 설정](#reset-by-using-the-vmaccess-extension-and-powershell)

## <a name="reset-by-using-the-azure-portal"></a>Azure Portal을 사용하여 다시 설정
Azure Portal에 로그인하고 왼쪽 메뉴에서 **가상 머신**을 클릭합니다.

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**

Windows VM을 선택한 다음, **지원 + 문제 해결** 아래에서 **암호 다시 설정**을 선택합니다. **암호 다시 설정** 창이 표시됩니다.

**암호 다시 설정**을 선택하고 사용자 이름 및 암호를 입력한 다음, **업데이트**를 선택합니다. VM에 연결을 다시 시도하세요.

### <a name="reset-the-remote-desktop-services-configuration"></a>**원격 데스크톱 서비스 구성 다시 설정**

Windows VM을 선택한 다음, **지원 + 문제 해결** 아래에서 **암호 다시 설정**을 선택합니다. **암호 다시 설정** 창이 표시됩니다. 

**구성만 다시 설정**, **업데이트**를 차례로 선택합니다. VM에 연결을 다시 시도하세요.


## <a name="reset-by-using-the-vmaccess-extension-and-powershell"></a>VMAccess 확장 및 PowerShell을 사용하여 다시 설정
[최신 PowerShell 모듈을 설치 및 구성](/powershell/azure/overview)하고 [Connect-AzureRmAccount](https://docs.microsoft.com/powershell/module/azurerm.profile/connect-azurermaccount) cmdlet을 사용하여 Azure 구독에 로그인해야 합니다.

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**
[Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet을 사용하여 관리자 암호 또는 사용자 이름을 다시 설정합니다. 버전 1은 사용되지 않으므로 `typeHandlerVersion` 설정은 2.0 이상이어야 합니다. 

```powershell
$SubID = "<SUBSCRIPTION ID>" 
$RgName = "<RESOURCE GROUP NAME>" 
$VmName = "<VM NAME>" 
$Location = "<LOCATION>" 
 
Connect-AzureRmAccount 
Select-AzureRMSubscription -SubscriptionId $SubID 
Set-AzureRmVMAccessExtension -ResourceGroupName $RgName -Location $Location -VMName $VmName -Credential (get-credential) -typeHandlerVersion "2.0" -Name VMAccessAgent 
```

> [!NOTE] 
> VM에서 현재 로컬 관리자 계정이 아닌 다른 이름을 입력한 경우 VMAccess 확장은 해당 이름으로 로컬 관리자 계정을 추가하고 지정된 암호를 해당 계정에 할당합니다. VM에 로컬 관리자 계정이 있는 경우 VMAccess 확장은 암호를 다시 설정합니다. 계정이 비활성화된 경우 VMAccess 확장은 사용하도록 설정합니다.

### <a name="reset-the-remote-desktop-services-configuration"></a>**원격 데스크톱 서비스 구성 다시 설정**
[Set-AzureRmVMAccessExtension](/powershell/module/azurerm.compute/set-azurermvmaccessextension) PowerShell cmdlet을 사용하여 VM에 대한 원격 액세스를 다시 설정합니다. 다음 예제에서는 리소스 그룹 `myVMAccess`의 VM `myVM`에서 `myResourceGroup` 진단 확장을 다시 설정합니다.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0" -ForceRerun
```

> [!TIP]
> 언제든지 VM은 단일 VM 액세스 에이전트를 하나만 사용할 수 있습니다. VM 액세스 에이전트 속성을 설정하려면 `-ForceRerun` 옵션을 사용합니다. `-ForceRerun`을 사용하는 경우 이전 명령에서 사용한 동일한 이름을 VM 액세스 에이전트에 사용해야 합니다.

여전히 가상 머신에 원격으로 연결할 수 없으면 [Windows 기반 Azure 가상 머신에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요. Windows 도메인 컨트롤러에 대한 연결이 끊어지면 도메인 컨트롤러 백업에서 복원해야 합니다.

## <a name="next-steps"></a>다음 단계
Azure VM 액세스 확장이 응답하지 않고 암호를 다시 설정할 수 없는 경우 [오프라인으로 로컬 Windows 암호를 다시 설정](reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수 있습니다. 이는 좀 더 고급스러운 방법이며, 문제가 있는 VM의 가상 하드 디스크를 다른 VM에 연결해야 합니다. 먼저 이 문서에 설명된 단계를 수행하고, 이 단계로 해결되지 않으면 오프라인 암호 다시 설정 방법을 시도합니다.

[Azure VM 확장 및 기능 알아보기](../extensions/features-windows.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP 또는 SSH를 사용하여 Azure 가상 머신에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

