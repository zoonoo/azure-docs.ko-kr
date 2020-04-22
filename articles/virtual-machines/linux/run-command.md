---
title: Azure에서 Linux VM에서 셸 스크립트 실행
description: 이 항목에서는 명령 실행 기능을 사용하여 Azure Linux 가상 시스템 내에서 스크립트를 실행하는 방법에 대해 설명합니다.
services: automation
ms.service: virtual-machines
author: bobbytreed
ms.author: robreed
ms.date: 04/26/2019
ms.topic: article
manager: carmonm
ms.openlocfilehash: 80fc33a93d4d83dad1e687b176b39728fc7e8807
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758611"
---
# <a name="run-shell-scripts-in-your-linux-vm-by-using-run-command"></a>실행 명령을 사용하여 Linux VM에서 셸 스크립트 실행

실행 명령 기능은 가상 시스템(VM) 에이전트를 사용하여 Azure Linux VM 내에서 셸 스크립트를 실행합니다. 이러한 스크립트를 일반 컴퓨터 또는 응용 프로그램 관리에 사용할 수 있습니다. VM 액세스 및 네트워크 문제를 신속하게 진단 및 해결하고 VM을 양호한 상태로 되돌릴 수 있습니다.

## <a name="benefits"></a>이점

여러 가지 방법으로 가상 컴퓨터에 액세스할 수 있습니다. Run Command는 VM 에이전트를 사용하여 가상 컴퓨터에서 원격으로 스크립트를 실행할 수 있습니다. Linux VM에 Azure 포털, [REST API](/rest/api/compute/virtual%20machines%20run%20commands/runcommand)또는 [Azure CLI를](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 통해 실행 명령을 사용합니다.

이 기능은 가상 시스템 내에서 스크립트를 실행하려는 모든 시나리오에서 유용합니다. 부적절한 네트워크 또는 관리 사용자 구성으로 인해 RDP 또는 SSH 포트가 열려 있지 않은 가상 컴퓨터를 문제 해결하고 해결하는 유일한 방법 중 하나입니다.

## <a name="restrictions"></a>제한

Run Command를 사용할 때 다음 제한 사항이 적용됩니다.

* 출력은 마지막 4,096바이트로 제한됩니다.
* 스크립트를 실행하는 데 최소 시간은 약 20초입니다.
* 스크립트는 기본적으로 Linux에서 높은 사용자로 실행됩니다.
* 한 번에 하나의 스크립트를 실행할 수 있습니다.
* 정보를 요청하는 스크립트(대화형 모드)는 지원되지 않습니다.
* 실행 중인 스크립트는 취소할 수 없습니다.
* 스크립트를 실행할 수 있는 최대 시간은 90분입니다. 그런 다음 스크립트가 시간 시간 중지됩니다.
* 스크립트의 결과를 반환하려면 VM에서의 아웃바운드 연결이 필요합니다.

> [!NOTE]
> 제대로 작동하려면 실행 명령이 Azure 공용 IP 주소에 대한 연결(포트 443)이 필요합니다. 확장이 이러한 끝점에 액세스할 수 없는 경우 스크립트가 성공적으로 실행되지만 결과를 반환하지 않을 수 있습니다. 가상 컴퓨터에서 트래픽을 차단하는 경우 [서비스 태그를](../../virtual-network/security-overview.md#service-tags) 사용하여 `AzureCloud` 태그를 사용하여 Azure 공용 IP 주소로 의 트래픽을 허용할 수 있습니다.

## <a name="available-commands"></a>사용 가능한 명령

이 표에서는 Linux VM에 대해 사용할 수 있는 명령 목록을 보여줍니다. **RunShellScript** 명령을 사용하여 원하는 사용자 지정 스크립트를 실행할 수 있습니다. Azure CLI 또는 PowerShell을 사용하여 명령을 실행하는 경우 `--command-id` 또는 `-CommandId` 매개 변수에 대해 제공하는 값은 다음에 나열된 값 중 하나여야 합니다. 사용 가능한 명령이 아닌 값을 지정하면 다음과 같은 오류가 발생합니다.

```error
The entity was not found in this Azure location
```

|**이름**|**설명**|
|---|---|
|**RunShellScript**|Linux 셸 스크립트를 실행합니다.|
|**ifconfig**| 모든 네트워크 인터페이스의 구성을 가져옵니다.|

## <a name="azure-cli"></a>Azure CLI

다음 예제에서는 [az vm run-command](/cli/azure/vm/run-command?view=azure-cli-latest#az-vm-run-command-invoke) 명령을 사용하여 Azure Linux VM에서 셸 스크립트를 실행합니다.

```azurecli-interactive
az vm run-command invoke -g myResourceGroup -n myVm --command-id RunShellScript --scripts "sudo apt-get update && sudo apt-get install -y nginx"
```

> [!NOTE]
> 명령을 다른 사용자로 실행하려면 `sudo -u` 입력하여 사용자 계정을 지정합니다.

## <a name="azure-portal"></a>Azure portal

[Azure 포털의](https://portal.azure.com) VM으로 이동하여 **OPERATIONS**에서 **실행 명령을** 선택합니다. VM에서 실행할 사용 가능한 명령 목록이 표시됩니다.

![명령 목록](./media/run-command/run-command-list.png)

실행할 명령을 선택하세요. 일부 명령에는 선택적 또는 필수 입력 매개 변수가 있을 수 있습니다. 이러한 명령의 경우 입력 값을 제공하기 위해 매개 변수가 텍스트 필드로 표시됩니다. 각 명령에 대해 **View 스크립트를**확장하여 실행 중인 스크립트를 볼 수 있습니다. **RunShellScript는** 사용자 지정 스크립트를 제공할 수 있으므로 다른 명령과 다릅니다.

> [!NOTE]
> 기본 제공된 명령은 편집할 수 없습니다.

명령을 선택한 후 **실행을** 선택하여 스크립트를 실행합니다. 스크립트가 완료되면 출력 및 출력 창의 오류를 반환합니다. 다음 스크린샷은 **ifconfig** 명령을 실행하는 작업의 예제 출력을 보여줍니다.

![명령 실행 스크립트 출력](./media/run-command/run-command-script-output.png)

### <a name="powershell"></a>PowerShell

다음 예제에서는 [Invoke-AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand) cmdlet을 사용하여 Azure VM에서 PowerShell 스크립트를 실행합니다. cmdlet에서는 `-ScriptPath` 매개 변수에서 참조되는 스크립트가 cmdlet이 실행되는 위치의 로컬이어야 합니다.

```powershell-interactive
Invoke-AzVMRunCommand -ResourceGroupName '<myResourceGroup>' -Name '<myVMName>' -CommandId 'RunPowerShellScript' -ScriptPath '<pathToScript>' -Parameter @{"arg1" = "var1";"arg2" = "var2"}
```

## <a name="limiting-access-to-run-command"></a>명령 실행에 대한 액세스 제한

실행 명령을 나열하거나 명령의 세부 정보를 `Microsoft.Compute/locations/runCommands/read` 표시하려면 구독 수준에서 권한이 필요합니다. 기본 제공 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할 및 상위 수준에는 이 권한이 있습니다.

명령을 실행하려면 `Microsoft.Compute/virtualMachines/runCommand/action` 구독 수준에서 권한이 필요합니다. [가상 시스템 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) 역할과 상위 수준에는 이 권한이 있습니다.

[기본 제공 역할](../../role-based-access-control/built-in-roles.md) 중 하나를 사용하거나 [사용자 지정 역할을](../../role-based-access-control/custom-roles.md) 만들어 Run Command를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

VM에서 원격으로 스크립트 및 명령을 실행하는 다른 방법에 대해 알아보려면 [Linux VM에서 스크립트 실행](run-scripts-in-vm.md)을 참조하십시오.
