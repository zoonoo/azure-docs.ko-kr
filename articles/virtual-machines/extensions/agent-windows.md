---
title: Azure Virtual Machines 에이전트 개요
description: Azure Virtual Machines 에이전트 개요
services: virtual-machines-windows
author: mimckitt
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 07/20/2019
ms.author: mimckitt
ms.openlocfilehash: 1ef2c9ef4e2a2296ceb214c89bb6e3fb98dcb26f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974909"
---
# <a name="azure-virtual-machine-agent-overview"></a>Azure Virtual Machines 에이전트 개요
Microsoft Azure VM 에이전트(가상 머신 에이전트)는 Azure 패브릭 컨트롤러와 VM(가상 머신)의 상호 작용을 관리하는 안전하고 간단한 프로세스입니다. VM 에이전트는 Azure 가상 머신 확장을 설정하고 실행하는 데 기본적인 역할을 수행합니다. VM 확장을 사용하면 소프트웨어 설치 및 구성과 같은 VM의 배포 후 구성을 설정할 수 있습니다. 또한 VM 확장을 사용하면 VM의 관리 암호를 다시 설정하는 등의 복구 기능도 사용할 수 있습니다. Azure VM 에이전트가 없으면 VM 확장을 실행할 수 없습니다.

이 문서에서는 Azure 가상 머신 에이전트의 설치 및 검색에 대해 자세히 설명 합니다.

## <a name="install-the-vm-agent"></a>VM 에이전트 설치

### <a name="azure-marketplace-image"></a>Azure Marketplace 이미지

Azure VM 에이전트는 Azure Marketplace 이미지에서 배포된 모든 Windows VM에 기본적으로 설치됩니다. 포털, PowerShell, 명령줄 인터페이스 또는 Azure Resource Manager 템플릿에서 Azure Marketplace 이미지를 배포하면 Azure VM 에이전트도 설치됩니다.

Windows 게스트 에이전트 패키지는 두 부분으로 나뉘어 있습니다.

- PA(프로비전 에이전트)
- WinGA(Windows 게스트 에이전트)

VM을 부팅하려면 VM에 PA가 설치되어 있어야 하지만 WinGA는 설치할 필요가 없습니다. VM 배포 시 WinGA를 설치하지 않도록 선택할 수 있습니다. 다음 예제에서는 Azure Resource Manager 템플릿으로 *provisionVmAgent* 옵션을 선택하는 방법을 보여 줍니다.

```json
"resources": [{
"name": "[parameters('virtualMachineName')]",
"type": "Microsoft.Compute/virtualMachines",
"apiVersion": "2016-04-30-preview",
"location": "[parameters('location')]",
"dependsOn": ["[concat('Microsoft.Network/networkInterfaces/', parameters('networkInterfaceName'))]"],
"properties": {
    "osProfile": {
    "computerName": "[parameters('virtualMachineName')]",
    "adminUsername": "[parameters('adminUsername')]",
    "adminPassword": "[parameters('adminPassword')]",
    "windowsConfiguration": {
        "provisionVmAgent": "false"
}
```

에이전트가 설치되어 있지 않으면 Azure Backup 또는 Azure Security와 같은 일부 Azure 서비스를 사용할 수 없습니다. 이러한 서비스를 사용하려면 확장을 설치해야 합니다. WinGA 없이 VM을 배포한 경우 나중에 최신 버전의 에이전트를 설치할 수 있습니다.

### <a name="manual-installation"></a>수동 설치
Windows VM 에이전트는 Windows 설치 관리자 패키지를 사용하여 수동으로 설치할 수 있습니다. Azure에 배포된 사용자 지정 VM 이미지를 만들 때 수동 설치가 필요할 수 있습니다. Windows VM 에이전트를 수동으로 설치하려면 [VM 에이전트 설치 관리자를 다운로드합니다](https://go.microsoft.com/fwlink/?LinkID=394789). VM 에이전트는 Windows Server 2008 (64 비트) 이상에서 지원 됩니다.

> [!NOTE]
> ProvisionVMAgent를 사용 하지 않고 이미지에서 배포 된 VM에 VMAgent를 수동으로 설치한 후 AllowExtensionOperations 옵션을 업데이트 하는 것이 중요 합니다.

```powershell
$vm.OSProfile.AllowExtensionOperations = $true
$vm | Update-AzVM
```

### <a name="prerequisites"></a>필수 구성 요소

- Windows VM 에이전트는 .NET Framework 4.0를 사용 하 여 Windows Server 2008 SP2 (64 비트) 이상을 실행 해야 합니다. [Azure의 가상 머신 에이전트에 대 한 최소 버전 지원](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)을 참조 하세요.

- VM에 IP 주소 168.63.129.16에 대 한 액세스 권한이 있는지 확인 합니다. 자세한 내용은 [IP 주소 168.63.129.16?](../../virtual-network/what-is-ip-address-168-63-129-16.md)을 참조 하세요.

- DHCP가 게스트 VM 내에서 사용 되도록 설정 되어 있는지 확인 합니다. IaaS VM 에이전트 및 확장이 작동 하려면 DHCP에서 호스트 또는 패브릭 주소를 가져오는 데 필요 합니다. 정적 개인 IP가 필요한 경우 Azure Portal 또는 PowerShell을 통해 구성 하 고 VM 내의 DHCP 옵션이 사용 하도록 설정 되어 있는지 확인 해야 합니다. PowerShell을 사용 하 여 고정 IP 주소를 설정 하는 방법에 [대해 자세히 알아보세요](../../virtual-network/virtual-networks-static-private-ip-arm-ps.md#change-the-allocation-method-for-a-private-ip-address-assigned-to-a-network-interface) .


## <a name="detect-the-vm-agent"></a>VM 에이전트 검색

### <a name="powershell"></a>PowerShell

Azure Resource Manager PowerShell 모듈을 사용하여 Azure VM에 대한 정보를 검색할 수 있습니다. Azure VM 에이전트의 프로비전 상태와 같이 VM에 대한 정보를 보려면 [Get-AzVM](/powershell/module/az.compute/get-azvm)을 사용합니다.

```powershell
Get-AzVM
```

다음 압축 예제 출력에서는 내부에 중첩 된 *ProvisionVMAgent* 속성을 보여 줍니다 `OSProfile` . 이 속성을 사용하여 VM 에이전트가 VM에 배포되었는지 여부를 확인할 수 있습니다.

```powershell
OSProfile                  :
  ComputerName             : myVM
  AdminUsername            : myUserName
  WindowsConfiguration     :
    ProvisionVMAgent       : True
    EnableAutomaticUpdates : True
```

다음 스크립트는 VM 이름의 간단한 목록과 VM 에이전트의 상태를 반환하는 데 사용할 수 있습니다.

```powershell
$vms = Get-AzVM

foreach ($vm in $vms) {
    $agent = $vm | Select -ExpandProperty OSProfile | Select -ExpandProperty Windowsconfiguration | Select ProvisionVMAgent
    Write-Host $vm.Name $agent.ProvisionVMAgent
}
```

### <a name="manual-detection"></a>수동 검색

Microsoft VM에 로그인하면 작업 관리자를 사용하여 실행 중인 프로세스를 검사할 수 있습니다. Azure VM 에이전트를 확인하려면 작업 관리자를 열고 *세부 정보* 탭을 클릭하고 **WindowsAzureGuestAgent.exe**라는 프로세스 이름을 찾습니다. 이 프로세스가 있으면 VM 에이전트가 설치되어 있는 것입니다.


## <a name="upgrade-the-vm-agent"></a>VM 에이전트 업그레이드
Windows 용 Azure VM 에이전트는 Azure Marketplace에서 배포 된 이미지에서 자동으로 업그레이드 됩니다. 새 VM이 Azure에 배포되면 VM 프로비전 시 최신 VM 에이전트가 제공됩니다. 에이전트를 수동으로 설치 하거나 사용자 지정 VM 이미지를 배포 하는 경우에는 이미지를 만들 때 새 VM 에이전트를 포함 하도록 수동으로 업데이트 해야 합니다.

## <a name="windows-guest-agent-automatic-logs-collection"></a>Windows 게스트 에이전트 자동 로그 수집
Windows 게스트 에이전트에는 일부 로그를 자동으로 수집 하는 기능이 있습니다. 이 기능은 CollectGuestLogs.exe 프로세스에 의해 컨트롤러입니다. PaaS Cloud Services 및 IaaS Virtual Machines 모두에 대해 존재 하며,이는 VM에서 일부 진단 로그를 자동으로 수집 하 & 오프 라인 분석에 사용할 수 있도록 하는 것입니다. 수집 된 로그는 이벤트 로그, OS 로그, Azure 로그 및 일부 레지스트리 키입니다. VM의 호스트로 전송 되는 ZIP 파일을 생성 합니다. 그런 다음이 ZIP 파일을 검토 하 여 엔지니어링 팀과 지원 전문가에 게 VM을 소유 하는 고객의 요청에 대 한 문제를 조사할 수 있습니다.

## <a name="guest-agent-and-osprofile-certificates"></a>게스트 에이전트 및 OSProfile 인증서
Azure VM 에이전트는 `OSProfile` vm 또는 가상 머신 확장 집합의에서 참조 되는 인증서를 설치 하는 일을 담당 합니다. 게스트 VM 내의 인증서 MMC 콘솔에서 이러한 인증서를 수동으로 제거 하는 경우 게스트 에이전트가 해당 인증서를 다시 추가 해야 합니다.
인증서를 영구적으로 제거 하려면에서 인증서를 제거한 `OSProfile` 후 게스트 운영 체제 내에서 제거 해야 합니다.

가상 컴퓨터의 경우 [AzVMSecret]() 를 사용 하 여에서 인증서를 제거 합니다 `OSProfile` .

가상 머신 확장 집합 인증서에 대 한 자세한 내용은 [Virtual Machine Scale Sets-어떻게 할까요? 사용 되지 않는 인증서 제거](../../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.md#how-do-i-remove-deprecated-certificates) 를 참조 하세요.


## <a name="next-steps"></a>다음 단계
VM 확장에 대한 자세한 내용은 [Azure 가상 머신 확장 및 기능 개요](overview.md)를 참조하세요.