---
title: Azure Stack Edge Pro GPU 디바이스에서 가상 머신 콘솔 연결하기
description: Azure Stack Edge Pro GPU 디바이스에서 실행되는 VM에서 가상 머신 콘솔에 연결하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 06/04/2021
ms.author: alkohli
ms.openlocfilehash: 5bcd23c9a515155b9dc7cfdc04a3fafe122474ae
ms.sourcegitcommit: e39ad7e8db27c97c8fb0d6afa322d4d135fd2066
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111986357"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 디바이스에서 가상 머신 콘솔 연결하기

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Azure Stack Edge Pro GPU 솔루션은 가상 머신을 통해 비컨테이너식 워크로드를 실행합니다. 이 문서에서는 디바이스에 배포된 가상 머신의 콘솔에 연결하는 방법을 설명합니다. 

가상 머신 콘솔을 사용하면 일반적으로 사용 가능한 원격 데스크톱 도구를 사용하여 키보드, 마우스 및 화면 기능을 통해 VM에 액세스할 수 있습니다. 콘솔에 액세스하면 디바이스에 가상 머신을 배포할 때 발생하는 문제를 해결할 수 있습니다. VM이 프로비전에 실패한 경우에도 가상 머신 콘솔에 연결할 수 있습니다.


## <a name="workflow"></a>워크플로

상위 수준 워크플로의 단계는 다음과 같습니다.

1. 디바이스의 PowerShell 인터페이스에 연결합니다.
1. VM에 대한 콘솔 액세스를 사용하도록 설정합니다.
1. RDP(원격 데스크톱 프로토콜)을 사용하여 VM에 연결합니다.
1. VM에 대한 콘솔 액세스를 취소합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하기에 앞서 다음의 사전 요구 사항을 완료해야 합니다.

#### <a name="for-your-device"></a>디바이스

활성화된 Azure Stack Edge Pro GPU 디바이스에 액세스할 수 있어야 합니다. 디바이스에 하나 이상의 VM이 배포되어 있어야 합니다. Azure PowerShell, 템플릿 또는 Azure Portal를 통해 VM을 배포할 수 있습니다.

#### <a name="for-client-accessing-the-device"></a>디바이스에 액세스하는 클라이언트

액세스하는 클라이언트 시스템의 필수 조건은 다음과 같습니다.

- 디바이스의 PowerShell 인터페이스에 액세스할 수 있습니다. 클라이언트에서 [지원되는 운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행 중입니다.
- 클라이언트에서 PowerShell 7.0 이상을 실행하고 있습니다. 해당 버전의 PowerShell은 Windows, Mac 및 Linux 클라이언트에 대해 작동합니다. 지침을 참고하여 [PowerShell 7.0 설치](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)를 실행하십시오.
- 원격 데스크톱 기능을 탑재합니다. Windows, macOS, Linux 사용 여부에 따라 해당 [원격 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)를 설치해야 합니다. 이 문서에서는 [Windows 원격 데스크톱](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) 및 [FreeRDP](https://www.freerdp.com/)와 관련된 지침을 제공합니다. <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>VM 콘솔 연결

디바이스에서 가상 머신 콘솔에 연결하는 방법은 다음과 같습니다.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>디바이스의 PowerShell 인터페이스 연결

첫 번째 단계는 디바이스의 [PowerShell 인터페이스 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)입니다. 

### <a name="enable-console-access-to-the-vm"></a>VM에 대한 콘솔 액세스 사용 설정

1.  PowerShell 인터페이스에서 다음 명령을 실행하여 VM 콘솔에 대한 액세스를 사용하도록 설정합니다.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. 샘플 출력에서 가상 머신 ID를 적어 둡니다. 이후 단계에서 이 값이 필요합니다.

    ```powershell
    [10.100.10.10]: PS>Grant-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1
        
    VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
    VirtualMachineHostName : 3V78B03
    ResourceGroupName      : mywindowsvm1rg
    VirtualMachineName     : mywindowsvm1
    Id                     : 81462e0a-decb-4cd4-96e9-057094040063
    [10.100.10.10]: PS>
    ```

### <a name="connect-to-the-vm"></a>VM에 연결

이제 원격 데스크톱 클라이언트를 사용하여 가상 머신 콘솔에 연결할 수 있습니다.

#### <a name="use-windows-remote-desktop"></a>Windows 원격 데스크톱 사용

1. 새 텍스트 파일을 만들고 다음 텍스트를 입력합니다.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. 클라이언트 시스템에 * *.rdp* 로 파일을 저장합니다. 이 프로필을 사용하여 VM에 연결하게 됩니다.
1. 프로필을 두 번 클릭하여 VM에 연결합니다. 아래의 로그인 정보를 제공하십시오.

    - **사용자 이름**: EdgeARMUser로 로그인합니다.
    - **암호**: 디바이스의 로컬 Azure Resource Manager 암호를 제공합니다. 암호를 잊은 경우 [Azure Portal를 통해 Azure Resource Manager 암호를 다시 설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal)합니다. 

#### <a name="use-freerdp"></a>FreeRDP 사용

Linux 클라이언트에서 FreeRDP를 사용하는 경우 다음 명령을 실행합니다. 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>VM 콘솔 액세스 철회

VM 콘솔에 대한 액세스를 취소하려면 디바이스의 PowerShell 인터페이스로 돌아가야 합니다. 다음 명령 실행:

```
Revoke-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
```
출력의 예제는 다음과 같습니다.

```powershell
[10.100.10.10]: PS>Revoke-HcsVMConnectAccess -ResourceGroupName mywindowsvm1rg -VirtualMachineName mywindowsvm1

VirtualMachineId       : 81462e0a-decb-4cd4-96e9-057094040063
VirtualMachineHostName : 3V78B03
ResourceGroupName      : mywindowsvm1rg
VirtualMachineName     : mywindowsvm1
Id                     : 81462e0a-decb-4cd4-96e9-057094040063

[10.100.10.10]: PS>
```
> [!NOTE] 
> VM 콘솔을 사용하여 작업을 완료한 후에는 액세스를 취소하거나 PowerShell 창을 닫아 세션을 종료하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [VM 배포](azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning.md) 문제를 해결합니다.
<!--Make "VM guest logs" first link when article is available.-->