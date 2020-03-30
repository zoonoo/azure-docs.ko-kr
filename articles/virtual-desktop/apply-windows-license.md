---
title: 세션 호스트 가상 컴퓨터에 Windows 라이선스 적용 - Azure
description: Windows 가상 데스크톱 VM에 대한 Windows 라이선스를 적용하는 방법을 설명합니다.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254236"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>세션 호스트 가상 컴퓨터에 Windows 라이선스 적용

Windows 가상 데스크톱 워크로드를 실행할 수 있도록 적절한 라이선스를 받은 고객은 세션 호스트 가상 컴퓨터에 Windows 라이선스를 적용하고 다른 라이선스에 대한 비용을 지불하지 않고 실행할 수 있습니다. 자세한 내용은 [Windows 가상 데스크톱 가격 책정을](https://azure.microsoft.com/pricing/details/virtual-desktop/)참조하십시오.

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Windows 가상 데스크톱 라이선스를 사용하는 방법
Windows 가상 데스크톱 라이선스를 사용하면 호스트 풀에서 세션 호스트로 등록되어 사용자 연결을 받는 모든 Windows 또는 Windows Server 가상 컴퓨터에 라이선스를 적용할 수 있습니다. 이 라이센스는 파일 공유 서버, 도메인 컨트롤러 등으로 실행되는 가상 시스템에는 적용되지 않습니다.

Windows 가상 데스크톱 라이선스를 사용하는 몇 가지 방법이 있습니다.
- [Azure Marketplace 오퍼링을](./create-host-pools-azure-marketplace.md)사용하여 호스트 풀및 해당 세션 호스트 가상 컴퓨터를 만들 수 있습니다. 이러한 방식으로 만든 가상 머신에는 라이센스가 자동으로 적용됩니다.
- [GitHub Azure 리소스 관리자 템플릿을](./create-host-pools-arm-template.md)사용하여 호스트 풀 및 해당 세션 호스트 가상 컴퓨터를 만들 수 있습니다. 이러한 방식으로 만든 가상 머신에는 라이센스가 자동으로 적용됩니다.
- 기존 세션 호스트 가상 시스템에 라이선스를 적용할 수 있습니다. 이렇게 하려면 먼저 [PowerShell을 사용하여 호스트 풀 만들기의](./create-host-pools-powershell.md) 지침을 따라 호스트 풀 및 관련 VM을 만든 다음 이 문서로 돌아가 라이선스를 적용하는 방법을 알아봅니다.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>세션 호스트 VM에 Windows 라이선스 적용
먼저 [최신 Azure PowerShell을 설치 및 구성](/powershell/azure/overview)했는지 확인합니다. 다음 PowerShell cmdlet을 실행하여 Windows 라이선스를 적용합니다.

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>세션 호스트 VM이 라이선스 혜택을 활용하고 있는지 확인
VM을 배포한 후 이 cmdlet ot를 실행하여 라이센스 유형을 확인합니다.
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

적용된 Windows 라이선스가 있는 세션 호스트 VM은 다음과 같은 것을 보여줍니다.

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

적용 된 Windows 라이센스없이 VM은 다음과 같은 것을 보여줍니다 :

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

다음 cmdlet을 실행하여 Azure 구독에 Windows 라이선스가 적용된 모든 세션 호스트 VM 목록을 볼 수 있습니다.

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
