---
title: Azure Stack Edge Pro GPU 장치에서 가상 컴퓨터 콘솔에 연결
description: Azure Stack Edge Pro GPU 장치에서 실행 되는 VM에서 가상 컴퓨터 콘솔에 연결 하는 방법을 설명 합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/22/2021
ms.author: alkohli
ms.openlocfilehash: 68cf157a512e9b1f6caee4734869c5bb4b836e2f
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962693"
---
# <a name="connect-to-a-virtual-machine-console-on-an-azure-stack-edge-pro-gpu-device"></a>Azure Stack Edge Pro GPU 장치에서 가상 컴퓨터 콘솔에 연결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Edge Pro GPU 솔루션 Azure Stack 가상 컴퓨터를 통해 비 컨테이너 화 된 작업을 실행 합니다. 이 문서에서는 장치에 배포 된 가상 컴퓨터의 콘솔에 연결 하는 방법을 설명 합니다. 

가상 컴퓨터 콘솔을 사용 하면 일반적으로 사용 가능한 원격 데스크톱 도구를 사용 하 여 키보드, 마우스 및 화면 기능을 통해 Vm에 액세스할 수 있습니다. 콘솔에 액세스 하 고 장치에 가상 컴퓨터를 배포할 때 발생 하는 문제를 해결할 수 있습니다. VM이 프로 비전에 실패 한 경우에도 가상 컴퓨터 콘솔에 연결할 수 있습니다.


## <a name="workflow"></a>워크플로

개략적인 워크플로는 다음 단계를 수행 합니다.

1. 장치에서 PowerShell 인터페이스에 연결 합니다.
1. VM에 대 한 콘솔 액세스를 사용 하도록 설정 합니다.
1. RDP (원격 데스크톱 프로토콜)를 사용 하 여 VM에 연결 합니다.
1. VM에 대 한 콘솔 액세스를 취소 합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작 하기 전에 다음 필수 구성 요소를 완료 했는지 확인 합니다.

#### <a name="for-your-device"></a>장치

활성화 된 Azure Stack Edge Pro GPU 장치에 액세스할 수 있어야 합니다. 장치에 하나 이상의 Vm이 배포 되어 있어야 합니다. Azure PowerShell, 템플릿을 통해 또는 Azure Portal를 통해 Vm을 배포할 수 있습니다.

#### <a name="for-client-accessing-the-device"></a>장치에 액세스 하는 클라이언트

다음과 같은 클라이언트 시스템에 액세스할 수 있는지 확인 합니다.

- 장치의 PowerShell 인터페이스에 액세스할 수 있습니다. 클라이언트에서 지원 되는 [운영 체제](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)를 실행 하 고 있습니다.
- 클라이언트에서 PowerShell 7.0 이상을 실행 하 고 있습니다. 이 버전의 PowerShell은 Windows, Mac 및 Linux 클라이언트에 대해 작동 합니다. [PowerShell 7.0을 설치](/powershell/scripting/whats-new/what-s-new-in-powershell-70?view=powershell-7.1&preserve-view=true)하는 지침을 참조 하세요.
- 에는 원격 데스크톱 기능이 있습니다. Windows, macOS 또는 Linux를 사용 하는지에 따라 이러한 [원격 데스크톱 클라이언트](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients)중 하나를 설치 해야 합니다. 이 문서에서는 [Windows 원격 데스크톱](/windows-server/remote/remote-desktop-services/clients/windowsdesktop#install-the-client) 및 [freerdp](https://www.freerdp.com/)와 관련 된 지침을 제공 합니다. <!--Which version of FreeRDP to use?-->


## <a name="connect-to-vm-console"></a>VM 콘솔에 연결

장치에서 가상 컴퓨터 콘솔에 연결 하려면 다음 단계를 수행 합니다.

### <a name="connect-to-the-powershell-interface-on-your-device"></a>장치에서 PowerShell 인터페이스에 연결

첫 번째 단계는 장치의 [PowerShell 인터페이스에 연결](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) 하는 것입니다. 

### <a name="enable-console-access-to-the-vm"></a>VM에 대 한 콘솔 액세스 사용

1.  PowerShell 인터페이스에서 다음 명령을 실행 하 여 VM 콘솔에 대 한 액세스를 사용 하도록 설정 합니다.

    ```powershell
    Grant-HcsVMConnectAccess -ResourceGroupName <VM resource group> -VirtualMachineName <VM name>
    ```
2. 샘플 출력에서 가상 컴퓨터 ID를 적어 둡니다. 이후 단계에 필요 합니다.

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

이제 원격 데스크톱 클라이언트를 사용 하 여 가상 컴퓨터 콘솔에 연결할 수 있습니다.

#### <a name="use-windows-remote-desktop"></a>Windows 원격 데스크톱 사용

1. 새 텍스트 파일을 만들고 다음 텍스트를 입력 합니다.

    ```
    pcb:s:<VM ID from PowerShell>;EnhancedMode=0
    full address:s:<IP address of the device>   
    server port:i:2179
    username:s:EdgeARMUser
    negotiate security layer:i:0
    ```
1. 클라이언트 시스템에 **.rdp* 파일로 파일을 저장 합니다. 이 프로필을 사용 하 여 VM에 연결 합니다.
1. 프로필을 두 번 클릭 하 여 VM에 연결 합니다. 다음 자격 증명을 제공 합니다.

    - **사용자 이름**: EdgeARMUser로 로그인 합니다.
    - **암호**: 장치에 대 한 로컬 Azure Resource Manager 암호를 제공 합니다. 암호를 잊은 경우 [Azure Portal를 통해 Azure Resource Manager 암호를 다시 설정](azure-stack-edge-gpu-set-azure-resource-manager-password.md#reset-password-via-the-azure-portal)합니다. 

#### <a name="use-freerdp"></a>FreeRDP 사용

Linux 클라이언트에서 FreeRDP를 사용 하는 경우 다음 명령을 실행 합니다. 

```powershell
./wfreerdp /u:EdgeARMUser /vmconnect:<VM ID from PowerShell> /v:<IP address of the device>
```

## <a name="revoke-vm-console-access"></a>VM 콘솔 액세스 해지

VM 콘솔에 대 한 액세스를 취소 하려면 장치의 PowerShell 인터페이스로 돌아갑니다. 다음 명령을 실행합니다.

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
> VM 콘솔을 사용 하 여 작업을 완료 한 후에는 액세스를 취소 하거나 PowerShell 창을 닫아 세션을 종료 하는 것이 좋습니다. 

## <a name="next-steps"></a>다음 단계

- Azure Portal에서 [Azure Stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md) 문제를 해결 합니다.