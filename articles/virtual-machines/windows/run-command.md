---
title: Azure의 Windows VM에서 PowerShell 스크립트 실행
description: 이 항목에서는 명령 실행을 사용하여 Azure Windows 가상 머신 내에서 PowerShell 스크립트를 실행하는 방법 설명
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 06/06/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 60cdcbeba0c4149d8f049bbab553bd658c965338
ms.sourcegitcommit: 2ad510772e28f5eddd15ba265746c368356244ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/28/2018
ms.locfileid: "43128598"
---
# <a name="run-powershell-scripts-in-your-windows-vm-with-run-command"></a>명령 실행을 사용하여 Windows VM에서 PowerShell 스크립트 실행

명령 실행은 VM 에이전트를 사용하여 Azure Windows VM 내에서 PowerShell 스크립트를 실행합니다. 이러한 스크립트는 일반 컴퓨터 또는 응용 프로그램 관리에 사용할 수 있으며, VM 액세스 및 네트워크 문제를 신속하게 진단 및 수정하고 VM을 정상 상태로 되돌리는 데 사용할 수 있습니다.

## <a name="benefits"></a>이점

가상 머신에 액세스하는 데 사용할 수 있는 여러 옵션이 있습니다. 명령 실행은 VM 에이전트를 사용하여 원격으로 가상 머신에서 스크립트를 실행할 수 있습니다. 명령 실행은 Azure Portal, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand), [Azure CLI](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 또는 [PowerShell](/powershell/module/azurerm.compute/invoke-azurermvmruncommand)을 통해 사용할 수 있습니다.

이 기능은 가상 머신 내에서 스크립트를 실행하려는 경우 모든 시나리오에서 유용하며, 부적절한 네트워크 또는 관리 사용자 구성으로 인해 RDP 또는 SSH를 개방하지 않은 가상 머신의 문제를 해결하고 수정할 수 있는 유일한 방법입니다.

## <a name="restrictions"></a>제한

다음 제한은 명령 실행을 사용할 때 적용됩니다.

* 출력은 마지막 4096바이트로 제한됨
* 스크립트를 실행하는 최소 시간은 약 20초
* 스크립트는 Windows에서 시스템으로 실행
* 한 번에 한 스크립트씩 실행 가능
* 실행 중인 스크립트는 취소할 수 없음
* 스크립트를 실행할 수 있는 최대 시간은 90분으로 이후는 시간 초과
* 스크립트의 결과를 반환하려면 VM에서의 아웃바운드 연결이 필요합니다.

## <a name="run-a-command"></a>명령 실행

[Azure](https://portal.azure.com)에서 VM으로 이동하여 **작업** 아래에서 **명령 실행**을 선택합니다. VM에서 실행에 사용할 수 있는 명령 목록이 표시됩니다.

![명령 실행 목록](./media/run-command/run-command-list.png)

실행할 명령을 선택하세요. 명령 중 일부에는 선택 또는 필수 입력 매개 변수가 있을 수 있습니다. 이러한 명령의 경우 매개 변수는 입력 값을 제공하기 위한 텍스트 필드로 표시됩니다. 각 명령의 경우 **스크립트 보기**를 확장하여 실행되는 스크립트를 볼 수 있습니다. **RunPowerShellScript**는 사용자 고유의 사용자 지정 스크립트를 제공할 수 있으므로 다른 명령과 다릅니다.

> [!NOTE]
> 기본 제공된 명령은 편집할 수 없습니다.

명령을 선택하면 **실행**을 클릭하여 스크립트를 실행합니다. 스크립트가 실행되고 완료되면 출력 창에 출력 및 모든 오류를 반환합니다. 다음 스크린샷은 **RDPSettings** 명령을 실행하는 작업의 예제 출력을 보여줍니다.

![명령 실행 스크립트 출력](./media/run-command/run-command-script-output.png)

## <a name="commands"></a>명령

이 표에서는 Windows VM에 대해 사용할 수 있는 명령 목록을 보여줍니다. **RunPowerShellScript** 명령은 원하는 모든 사용자 지정 스크립트를 실행하는 데 사용할 수 있습니다.

|**Name**|**설명**|
|---|---|
|**RunPowerShellScript**|PowerShell 스크립트 실행|
|**EnableRemotePS**|원격 PowerShell을 사용하도록 설정하려면 컴퓨터를 구성합니다.|
|**EnableAdminAccount**|로컬 관리자 계정이 비활성화됐는지 확인하여 그렇다면 활성화합니다.|
|**IPConfig**| TCP/IP에 바인딩된 각 어댑터에 대해 IP 주소, 서브넷 마스크 및 기본 게이트웨이에 대한 자세한 정보를 표시합니다.|
|**RDPSettings**|레지스트리 설정 및 도메인 정책 설정을 확인합니다. 컴퓨터가 도메인의 일부인 경우 또는 설정을 기본값으로 수정하는 경우 정책 작업을 제안합니다.|
|**ResetRDPCert**|RDP 수신기에 연결된 SSL 인증서를 제거하고 RDP 수신기 보안을 기본값으로 복원합니다. 인증서에 문제가 있는 경우 이 스크립트를 사용합니다.|
|**SetRDPPort**|원격 데스크톱 연결에 대한 기본 또는 사용자 지정 포트 번호를 설정합니다. 포트에 인바운드 액세스를 위한 방화벽 규칙을 사용하도록 설정합니다.|

## <a name="powershell"></a>PowerShell

다음은 Azure VM에서 PowerShell 스크립트를 실행하기 위해 [Invoke-AzureRmVMRunCommand](/powershell/module/azurerm.compute/invoke-azurermvmruncommand) cmdlet을 사용하는 예제입니다.

```azurepowershell-interactive
Invoke-AzureRmVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>명령 실행에 대한 액세스 제한

명령 실행을 나열하거나 명령의 세부 정보를 보여주는 것은 기본 제공 [판독기](../../role-based-access-control/built-in-roles.md#reader) 역할 이상이 가진 `Microsoft.Compute/locations/runCommands/read`의 사용 권한이 필요합니다.

명령 실행은 [기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할 이상이 가진 `Microsoft.Compute/virtualMachines/runCommand/action`의 권한이 필요합니다.

명령 실행을 사용하려면 [기본 제공](../../role-based-access-control/built-in-roles.md) 역할 중 하나를 사용하거나 [사용자 지정](../../role-based-access-control/custom-roles.md) 역할을 만들 수 있습니다.

## <a name="next-steps"></a>다음 단계

VM에서 원격으로 스크립트 및 명령을 실행하는 다른 방법을 알려면 [Windows VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조합니다.
