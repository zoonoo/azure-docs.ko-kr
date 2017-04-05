---
title: "Windows VM에서 암호 또는 원격 데스크톱 구성 다시 설정 | Microsoft Docs"
description: "Azure 포털 또는 Azure PowerShell을 사용하여 Windows VM에서 계정 암호 또는 원격 데스크톱 서비스를 다시 설정하는 방법을 알아봅니다."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 45c69812-d3e4-48de-a98d-39a0f5675777
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 2673c95d2e312d427454585d46ac790cb126fea6
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-reset-the-remote-desktop-service-or-its-login-password-in-a-windows-vm"></a>Windows VM에서 원격 데스크톱 서비스 또는 해당 로그인 암호를 다시 설정하는 방법
Windows 가상 컴퓨터에 연결할 수 없는 경우 로컬 관리자 암호를 다시 설정할 수도 있고 원격 데스크톱 서비스 구성을 다시 설정할 수도 있습니다. 암호를 다시 설정하려면 Azure 포털이나 Azure PowerShell의 VM 액세스 확장을 사용할 수 있습니다. PowerShell을 사용하는 경우 [최신 PowerShell 모듈을 설치 및 구성](/powershell/azureps-cmdlets-docs)하고 Azure 구독에 로그인해야 합니다. [클래식 배포 모델을 사용하여 만든 VM에 대해 이러한 단계를 수행](windows/classic/reset-rdp.md)할 수도 있습니다.

## <a name="ways-to-reset-configuration-or-credentials"></a>구성 또는 자격 증명을 다시 설정하는 방법
필요에 따라 몇 가지 방법으로 원격 데스크톱 서비스 및 자격 증명을 재설정할 수 있습니다.

- [Azure 포털을 사용하여 재설정](#azure-portal)
- [Azure PowerShell을 사용하여 재설정](#vmaccess-extension-and-powershell)

## <a name="azure-portal"></a>Azure 포털
포털 메뉴를 확장하려면 왼쪽 위 구석에 있는 세 개의 막대를 클릭한 다음 **가상 컴퓨터**를 클릭합니다.

![Azure VM 찾아보기](./media/virtual-machines-windows-reset-rdp/Portal-Select-VM.png)

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**

Windows 가상 컴퓨터를 선택한 다음 **지원 + 문제 해결** > **암호 재설정**을 클릭합니다. 암호 다시 설정 블레이드가 표시됩니다.

![암호 다시 설정 페이지](./media/virtual-machines-windows-reset-rdp/Portal-RM-PW-Reset-Windows.png)

사용자 이름 및 새 암호를 입력한 후 **업데이트**를 클릭합니다. VM에 연결을 다시 시도하세요.

> [!NOTE] 
> - 암호를 변경하고 포털에서 작업이 완료되면 VM에서 변경 내용이 적용되는 데 3-5분이 소요될 수 있습니다. 그러나 변경 내용이 적용되지 않으면 VM을 다시 시작해 보세요.
> - VMAccess 확장은 기본 제공 로컬 관리자 계정에 대해서만 작동하며 다른 로컬 ID나 도메인 ID에 대해서는 아무 작업도 수행하지 않습니다.
> - 대상 컴퓨터가 도메인 컨트롤러인 경우 이 확장은 도메인 관리자 계정을 다시 설정하거나 이름을 바꿉니다.


### <a name="reset-the-remote-desktop-service-configuration"></a>**원격 데스크톱 서비스 구성을 다시 설정**

Windows 가상 컴퓨터를 선택한 다음 **지원 + 문제 해결** > **암호 재설정**을 클릭합니다. 다음과 같이 암호 다시 설정 블레이드가 표시됩니다.

![RDP 구성 다시 설정](./media/virtual-machines-windows-reset-rdp/Portal-RM-RDP-Reset.png)

드롭다운 메뉴에서 **구성만 재설정**을 선택하고 **업데이트**를 클릭합니다. VM에 연결을 다시 시도하세요.


## <a name="vmaccess-extension-and-powershell"></a>VMAccess 확장 및 PowerShell
[최신 PowerShell 모듈을 설치 및 구성](/powershell/azureps-cmdlets-docs)하고 `Login-AzureRmAccount` cmdlet을 사용하여 Azure 구독에 로그인해야 합니다.

### <a name="reset-the-local-administrator-account-password"></a>**로컬 관리자 계정 암호를 다시 설정**
[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell cmdlet을 사용하여 관리자 암호 또는 사용자 이름을 다시 설정합니다. 계정 자격 증명을 다음과 같이 만듭니다.

```powershell
$cred=Get-Credential
```

> [!NOTE] 
> VM에서 현재 로컬 관리자 계정이 아닌 다른 이름을 입력한 경우 VMAccess 확장은 로컬 관리자 계정의 이름을 바꾸고, 해당 계정에 지정된 암호를 할당하며, 원격 데스크톱 로그오프 이벤트를 실행합니다. VM에서 로컬 관리자 계정이 사용되지 않도록 설정된 경우 VMAccess 확장은 이를 사용하도록 설정합니다.

다음 예제에서는 리소스 그룹 `myResourceGroup`의 VM `myVM`을 지정된 자격 증명으로 업데이트합니다.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

### <a name="reset-the-remote-desktop-service-configuration"></a>**원격 데스크톱 서비스 구성을 다시 설정**
[Set-AzureRmVMAccessExtension](https://msdn.microsoft.com/library/mt619447.aspx) PowerShell cmdlet을 사용하여 VM에 대한 원격 액세스를 다시 설정합니다. 다음 예제에서는 리소스 그룹 `myVMAccess`의 VM `myVM`에서 `myResourceGroup` 진단 확장을 다시 설정합니다.

```powershell
Set-AzureRmVMAccessExtension -ResourceGroupName "myResoureGroup" -VMName "myVM" `
    -Name "myVMAccess" -Location WestUS -typeHandlerVersion "2.0 -ForceRerun
```

> [!TIP]
> 언제든지 VM은 단일 VM 액세스 에이전트를 하나만 사용할 수 있습니다. VM 액세스 에이전트 속성을 성공적으로 설정하려면 `-ForceRerun` 옵션을 사용하면 됩니다. `-ForceRerun`을 사용하는 경우 VM 액세스 에이전트에 이전 명령에서 사용한 것과 동일한 이름을 사용해야 합니다.

가상 컴퓨터에 원격으로 연결할 수 없으면 시도에 더 많은 단계를 [Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)을 참조하세요.


## <a name="next-steps"></a>다음 단계
Azure VM 액세스 확장이 응답하지 않고 암호를 다시 설정할 수 없는 경우 [오프라인으로 로컬 Windows 암호를 다시 설정](virtual-machines-windows-reset-local-password-without-agent.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)할 수 있습니다. 이 방법은 좀 더 고급 프로세스로, 문제가 있는 VM의 가상 하드 디스크를 다른 VM에 연결해야 합니다. 먼저 이 문서에 설명된 단계를 수행하고, 오프라인 암호 다시 설정 방법은 최후의 수단으로만 시도합니다.

[Azure VM 확장 및 기능](virtual-machines-windows-extensions-features.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[RDP 또는 SSH를 사용하여 Azure 가상 컴퓨터에 연결](http://msdn.microsoft.com/library/azure/dn535788.aspx)

[Windows 기반 Azure 가상 컴퓨터에 대한 원격 데스크톱 연결 문제 해결](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)


