---
title: Azure 랩 서비스에서 Windows 종료 동작 제어 가이드 | 마이크로 소프트 문서
description: 유휴 Windows 가상 컴퓨터를 자동으로 종료하고 Windows 종료 명령을 제거하는 단계입니다.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.topic: article
ms.date: 3/30/2020
ms.author: spelluru
ms.openlocfilehash: 39ff4f42457451dfa4aae90b281d6b163c56b4cd
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80522239"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows 종료 동작 제어 가이드

Azure Lab Services는 Windows 가상 컴퓨터(VM)가 예기치 않게 실행되지 않도록 여러 비용 제어를 제공합니다.
 - [일정 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [사용자에 대한 할당량 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [연결 해제 시 자동 종료 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

이러한 비용 제어를 통해도 Windows VM이 예기치 않게 계속 실행될 수 있는 상황이 있습니다. 그 결과, 학생의 할당량에서 공제됩니다.

- **RDP 창이 열려 있는 상태로 남아 있습니다.**
  
    학생이 RDP를 사용하여 VM에 연결하면 실수로 RDP 창을 열어 둘 수 있습니다.  RDP 창이 열려 있는 한, RDP 세션의 연결이 끊어진 후에만 트리거되므로 **차단 해제 설정에 대한 자동 종료는** 적용되지 않습니다.

- **Windows 종료 명령은 VM을 해제하는 데 사용됩니다.**
  
    학생은 Windows 종료 명령 또는 Windows 내에서 제공되는 다른 종료 메커니즘을 사용하여 [Azure Lab Services의 중지 단추를](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)사용하는 대신 VM을 끌 수 있습니다.  이 경우 Azure Lab 서비스의 관점에서 VM이 계속 사용되고 있습니다.
    
이러한 상황이 발생하지 않도록 하려면 이 가이드에서는 유휴 Windows VM을 자동으로 종료하고 **시작** 메뉴에서 Windows 종료 명령을 제거하는 단계를 제공합니다.  

> [!NOTE]
> 또한 학생이 VM을 시작할 때 VM이 할당량에서 예기치 않게 차감될 수 있지만 RDP를 사용하여 실제로 연결되지는 않습니다.  이 가이드는 현재 이 시나리오를 다루지 *않습니다.*  대신, 학생들은 RDP를 시작한 후 RDP를 사용하여 VM에 즉시 연결하도록 상기시켜야 합니다. 또는 VM을 중지해야 합니다.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>유휴 VM을 위한 자동 RDP 분리 및 종료

Windows는 RDP 세션이 유휴 상태가 되면 RDP 세션을 자동으로 연결해제하는 시간 제한을 설정하는 데 사용할 수 있는 **로컬 그룹 정책** 설정을 제공합니다.  마우스\키보드 입력이 *없을* 때 세션이 유휴 상태인 것으로 확인됩니다.  마우스\키보드 입력을 포함하지 않는 장기 실행 활동으로 인해 VM이 유휴 상태가 됩니다.  여기에는 긴 쿼리 실행, 비디오 스트리밍, 컴파일 등이 포함됩니다.  수업의 필요에 따라 이러한 유형의 활동을 처리할 수 있을 만큼 충분히 길도록 유휴 시간 제한을 설정할 수 있습니다.  예를 들어 필요한 경우 유휴 시간 제한을 1시간 이상으로 설정할 수 있습니다.

[**다음은 연결 해제**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) 설정시 자동 종료 설정과 함께 **유휴 세션 제한을** 구성할 때의 학생 의 환경입니다.
 1. 학생은 RDP를 사용하여 Windows VM에 연결합니다.
 2. 학생이 RDP 창을 열어 두고 VM이 지정한 **유휴 세션** 제한(예: 5분)에 대해 유휴 상태인 경우 학생은 다음 대화 상자를 볼 수 있습니다.

    ![유휴 시간 제한 만료된 대화 상자](../media/how-to-windows-shutdown/idle-time-expired.png)

1. 학생이 **확인을** *클릭하지* 않으면 RDP 세션이 2분 후에 자동으로 연결이 끊어집니다.
2. RDP 세션이 끊어지면 **연결 해제 설정에** 대한 자동 종료에 대한 지정된 시간 프레임에 도달하면 Azure Lab Services에서 VM이 자동으로 종료됩니다.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>템플릿 VM에서 RDP 유휴 세션 시간 제한 설정

RDP 세션 유휴 시간 제한을 설정하려면 템플릿 VM에 연결하고 아래의 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 90

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
또는 템플릿 VM을 사용하여 다음 수동 단계를 따르도록 선택할 수 있습니다.

1. Windows 키를 누릅니다, **입력 gpedit,** 다음 **선택 편집 그룹 정책 (제어판)**.

1. 원격 **데스크톱 세션 > 호스트가 세션 시간 제한을 > Windows 구성 요소 > > 컴퓨터 구성 > 관리 템플릿으로**이동합니다.  

    ![로컬 그룹 정책 편집기](../media/how-to-windows-shutdown/group-policy-idle.png)
   
1. **활성 이지만 유휴 원격 데스크톱 서비스 세션에 대 한 시간 제한 설정을**마우스 오른쪽 단추로 클릭 하 고 **편집을**클릭 합니다.

1. 다음 설정을 입력한 다음 **확인을**클릭합니다.
   1. **사용**을 선택합니다.
   1. **옵션에서** **유휴 세션 제한을**지정합니다.

    ![유휴 세션 제한](../media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 마지막으로 이 동작을 **연결 해제 설정의 자동 종료와** 결합하려면 방법 [문서의](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)단계를 따라야 합니다.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>시작 메뉴에서 Windows 종료 명령 제거

Windows **로컬 그룹 정책** 설정을 사용하면 **시작** 메뉴에서 종료 명령을 제거할 수도 있습니다.

종료 명령을 제거하려면 템플릿 VM에 연결하고 아래 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

또는 템플릿 VM을 사용하여 다음 수동 단계를 따르도록 선택할 수 있습니다.

1. Windows 키를 누릅니다, **입력 gpedit,** 다음 **선택 편집 그룹 정책 (제어판)**.

1. 컴퓨터 **구성 > 시작 메뉴 및 작업 표시줄에 > 관리 템플릿으로 이동합니다.**  

    ![로컬 그룹 정책 편집기](../media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 마우스 오른쪽 단추로 **클릭하고 종료, 다시 시작, 절전 및 최대 절전 모드 명령에 대한 액세스를 차단하고** **편집을**클릭합니다.

1. 사용 **설정을** 선택한 다음 **확인을**클릭합니다.
 
   ![종료 설정](../media/how-to-windows-shutdown/edit-shutdown.png)

1. 종료 명령이 더 이상 Windows **시작** 메뉴 아래에 나타나지 않습니다. 연결 **끊기** 명령만 나타납니다.

    ![종료 명령](../media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>다음 단계
Windows 템플릿 VM을 준비하는 방법에 대한 도움말: [Azure Lab 서비스에서 Windows 템플릿 컴퓨터를 설정하는 방법 가이드](how-to-prepare-windows-template.md) 를 참조하세요.