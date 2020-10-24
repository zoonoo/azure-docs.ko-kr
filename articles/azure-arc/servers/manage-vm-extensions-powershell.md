---
title: Azure PowerShell를 사용 하 여 VM 확장 사용
description: 이 문서에서는 Azure PowerShell를 사용 하 여 하이브리드 클라우드 환경에서 실행 되는 Azure Arc 사용 서버에 가상 머신 확장을 배포 하는 방법을 설명 합니다.
ms.date: 10/23/2020
ms.topic: conceptual
ms.openlocfilehash: d2408f75c7b6d81ba297de6dcdb85a712cd8908f
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/24/2020
ms.locfileid: "92495450"
---
# <a name="enable-azure-vm-extensions-using-azure-powershell"></a>Azure PowerShell를 사용 하 여 Azure VM 확장 사용

이 문서에서는 Azure PowerShell를 사용 하 여 Azure Arc 사용 서버에서 지 원하는 Azure VM 확장을 Linux 또는 Windows 하이브리드 컴퓨터에 배포 하 고 제거 하는 방법을 보여 줍니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure PowerShell이 설치된 컴퓨터 자세한 내용은 [Azure PowerShell 설치 및 구성](/powershell/azure/)을 참조하세요.

Azure PowerShell를 사용 하 여 Arc 사용 서버에서 관리 하는 하이브리드 서버에서 VM 확장을 관리 하려면 먼저 모듈을 설치 해야 `Az.ConnectedMachine` 합니다. Arc 사용 서버에서 다음 명령을 실행 합니다.

`Install-Module -Name Az.ConnectedMachine`.

설치가 완료 되 면 다음 메시지가 반환 됩니다.

`The installed extension `Az. ConnectedMachine` is experimental and not covered by customer support. Please use with discretion.`

## <a name="enable-extension"></a>확장 사용

Arc 사용 서버에서 VM 확장을 사용 하도록 설정 하려면, [New-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/new-azconnectedmachineextension) ,,, `-Name` `-ResourceGroupName` `-MachineName` `-Location` `-Publisher` ,- `ExtensionType` 및 `-Settings` 매개 변수와 함께 AzConnectedMachineExtension를 사용 합니다.

다음 예제에서는 Arc 사용 Linux 서버에서 Log Analytics VM 확장을 사용 하도록 설정 합니다.

```powershell
PS C:\> $Setting = @{ "workspaceId" = "workspaceId" }
PS C:\> $protectedSetting = @{ "workspaceKey" = "workspaceKey" }
PS C:\> New-AzConnectedMachineExtension -Name OMSLinuxAgent -ResourceGroupName "myResourceGroup" -MachineName "myMachine" -Location "eastus" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -TypeHandlerVersion "1.10" -Settings $Setting -ProtectedSetting $protectedSetting -ExtensionType OmsAgentforLinux"
```

다음 예에서는 Arc 사용 서버에서 사용자 지정 스크립트 확장을 사용 하도록 설정 합니다.

```powershell
PS C:\> $Setting = @{ "commandToExecute" = "powershell.exe -c Get-Process" }
PS C:\> New-AzConnectedMachineExtension -Name custom -ResourceGroupName myResourceGroup -MachineName myMachineName -Location eastus -Publisher "Microsoft.Compute" -TypeHandlerVersion 1.10 -Settings $Setting -ExtensionType CustomScriptExtension
```

## <a name="list-extensions-installed"></a>설치 된 목록 확장

Arc 사용 서버에서 VM 확장 목록을 가져오려면 및 매개 변수와 함께 [AzConnectedMachineExtension](/powershell/module/az.connectedmachine/get-azconnectedmachineextension) 를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예제:

```powershell
Get-AzConnectedMachineExtension -ResourceGroupName myResourceGroup -MachineName myMachineName

Name    Location  PropertiesType        ProvisioningState
----    --------  --------------        -----------------
custom  westus2   CustomScriptExtension Succeeded
```

## <a name="remove-an-installed-extension"></a>설치 된 확장 제거

Arc 사용 서버에서 설치 된 VM 확장을 제거 하려면 [Remove-AzConnectedMachineExtension](/powershell/module/az.connectedmachine/remove-azconnectedmachineextension) `-Name` , 및 매개 변수와 함께 AzConnectedMachineExtension를 사용 `-MachineName` `-ResourceGroupName` 합니다.

예를 들어 Linux 용 Log Analytics VM 확장을 제거 하려면 다음 명령을 실행 합니다.

```powershell
Remove-AzConnectedMachineExtension -MachineName myMachineName -ResourceGroupName myResourceGroup -Name OmsAgentforLinux
```

## <a name="next-steps"></a>다음 단계

- [Azure CLI](manage-vm-extensions-cli.md), [Azure Portal](manage-vm-extensions-portal.md)또는 [AZURE RESOURCE MANAGER 템플릿을](manage-vm-extensions-template.md)사용 하 여 VM 확장을 배포, 관리 및 제거할 수 있습니다.

- 문제 해결 정보는 [VM 확장 문제 해결 가이드](troubleshoot-vm-extensions.md)에서 찾을 수 있습니다.
