---
title: Azure Lab Services에서 Windows 종료 동작을 제어 하기 위한 가이드 | Microsoft Docs
description: 유휴 Windows 가상 머신을 자동으로 종료 하 고 Windows 종료 명령을 제거 하는 단계입니다.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: e17f6e79c3d18d82dd206954dcfb0e06b02b4d53
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85445171"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows 종료 동작 제어 가이드

Azure Lab Services는 Windows Vm (가상 머신)이 예기치 않게 실행 되지 않도록 몇 가지 비용 제어를 제공 합니다.
 - [일정 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
 - [사용자에 대한 할당량 설정](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users)
 - [연결 해제 시 자동 종료 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

이러한 비용 제어를 사용 하는 경우에도 Windows VM이 예기치 않게 계속 실행 되는 경우가 있습니다. 그 결과, 학생의 할당량에서 공제 됩니다.

- **RDP 창이 열려 있습니다.**
  
    학생이 RDP를 사용 하 여 VM에 연결 하는 경우 실수로 RDP 창이 열린 상태로 있을 수 있습니다.  RDP 창이 열려 있는 동안 **에는 연결 끊기에 대 한 자동 종료** 설정이 rdp 세션의 연결이 끊긴 후에만 트리거되 므로 적용 되지 않습니다.

- **Windows shutdown 명령을 사용 하 여 VM을 해제 합니다.**
  
    학생은 Windows 종료 명령 또는 Windows에서 제공 되는 기타 종료 메커니즘을 사용 하 여 [Azure Lab Services ' 중지 ' 단추](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-classroom-lab#start-or-stop-the-vm)를 사용 하는 대신 VM을 해제할 수 있습니다.  이 경우 Azure Lab Services 관점에서 VM은 계속 사용 되 고 있습니다.
    
이러한 상황을 방지 하기 위해이 가이드에서는 유휴 Windows VM을 자동으로 종료 하 고 **시작** 메뉴에서 windows 종료 명령을 제거 하는 단계를 제공 합니다.  

> [!NOTE]
> 또한 VM은 학생이 VM을 시작할 때 할당량에서 예기치 않게 공제 RDP를 사용 하 여 실제로 연결 하지 않을 수 있습니다.  이 가이드는 현재이 시나리오를 해결 *하지* 않습니다.  대신, 학생 들이 시작한 후 RDP를 사용 하 여 VM에 즉시 연결 하도록 미리 알림을 받아야 합니다. 또는 VM을 중지 해야 합니다.

## <a name="automatic-rdp-disconnect-and-shutdown-for-idle-vm"></a>유휴 VM에 대 한 자동 RDP 연결 끊기 및 종료

Windows에서는 유휴 상태가 될 때 RDP 세션의 연결을 자동으로 해제 하는 시간 제한을 설정 하는 데 사용할 수 있는 **로컬 그룹 정책** 설정을 제공 합니다.  Mouse\keyboard 입력이 *없는* 경우 세션이 유휴 상태로 확인 됩니다.  Mouse\keyboard 입력을 포함 하지 않는 장기 실행 작업을 수행 하면 VM이 유휴 상태가 됩니다.  여기에는 긴 쿼리, 스트리밍 비디오, 컴파일 등을 실행 하는 작업이 포함 됩니다.  클래스의 요구에 따라 유휴 시간 제한을 설정 하 여 이러한 유형의 활동을 처리 하는 데 충분 한지를 선택할 수 있습니다.  예를 들어 필요한 경우 유휴 시간 제한을 1 시간 이상으로 설정할 수 있습니다.

다음은 [**연결 끊기의 자동 종료**](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect) 설정과 함께 **유휴 세션 제한을** 구성할 때 사용 하는 학생의 경험입니다.
 1. 학생이 RDP를 사용 하 여 Windows VM에 연결 합니다.
 2. 학생이 RDP 창을 열고 지정 된 **유휴 세션 제한** (예: 5 분) 동안 유휴 상태 이면 학생에 게 다음과 같은 대화 상자가 표시 됩니다.

    ![유휴 시간 제한 만료 대화 상자](./media/how-to-windows-shutdown/idle-time-expired.png)

1. 학생이 **확인**을 클릭 *하지* 않으면 2 분 후에 RDP 세션이 자동으로 연결을 끊습니다.
2. RDP 세션의 연결을 끊은 후 **연결 끊기에 대 한 자동 종료** 설정에 지정 된 시간 프레임에 도달 하면 AZURE LAB SERVICES으로 VM이 자동으로 종료 됩니다.

### <a name="set-rdp-idle-session-time-limit-on-the-template-vm"></a>템플릿 VM에 대 한 RDP 유휴 세션 시간 제한 설정

RDP 세션 유휴 시간 제한을 설정 하기 위해 템플릿 VM에 연결 하 고 아래 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
# The MaxIdleTime is in milliseconds; by default, this script sets MaxIdleTime to 15 minutes.
$maxIdleTime = 15 * 60 * 1000

Set-ItemProperty -Path "HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" -Name "MaxIdleTime" -Value $maxIdleTime -Force
```
또는 템플릿 VM을 사용 하 여 다음 수동 단계를 수행 하도록 선택할 수 있습니다.

1. Windows 키를 누르고 **gpedit.msc**를 입력 한 다음 **그룹 정책 편집 (제어판)** 을 선택 합니다.

1. **컴퓨터 구성 > 관리 템플릿 > Windows 구성 요소 > 원격 데스크톱 서비스 > 원격 데스크톱 세션 호스트 세션 시간 제한**으로 이동 합니다.  

    ![로컬 그룹 정책 편집기](./media/how-to-windows-shutdown/group-policy-idle.png)
   
1. **활성 상태 이지만 유휴 상태 원격 데스크톱 서비스 세션에 대 한 시간 제한 설정**을 마우스 오른쪽 단추로 클릭 하 고 **편집**을 클릭 합니다.

1. 다음 설정을 입력 하 고 **확인**을 클릭 합니다.
   1. **사용**을 선택합니다.
   1. **옵션**에서 **유휴 세션 제한을**지정 합니다.

    ![유휴 세션 제한](./media/how-to-windows-shutdown/edit-idle-time-limit.png)

1. 마지막으로,이 동작을 **연결 끊기의 자동 종료** 설정과 결합 하려면 방법 문서: [연결 끊기 시 vm의 자동 종료 사용](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)에 대 한 단계를 수행 해야 합니다.

> [!WARNING]
> PowerShell을 사용 하 여 레지스트리 설정을 직접 또는 수동으로 수정 하는 방법으로 그룹 정책 편집기를 사용 하 여이 설정을 구성한 후에는 먼저 VM을 다시 시작 하 여 설정을 적용 해야 합니다.  또한 레지스트리를 사용 하 여 설정을 구성 하는 경우 레지스트리 설정에 대 한 변경 내용을 반영 하기 위해 그룹 정책 편집기가 항상 새로 고쳐지지는 않습니다. 그러나 레지스트리 설정이 예상한 대로 적용 되 고 지정 된 시간 동안 유휴 상태일 때 RDP 세션의 연결 끊김이 표시 됩니다.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>시작 메뉴에서 Windows 종료 명령 제거

Windows **로컬 그룹 정책** 설정을 사용 하 여 **시작** 메뉴에서 종료 명령을 제거할 수도 있습니다.

종료 명령을 제거 하려면 템플릿 VM에 연결 하 고 아래 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

또는 템플릿 VM을 사용 하 여 다음 수동 단계를 수행 하도록 선택할 수 있습니다.

1. Windows 키를 누르고 **gpedit.msc**를 입력 한 다음 **그룹 정책 편집 (제어판)** 을 선택 합니다.

1. **컴퓨터 구성 > 관리 템플릿 > 시작 메뉴 및 작업 표시줄**로 이동 합니다.  

    ![로컬 그룹 정책 편집기](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 제거를 마우스 오른쪽 단추로 클릭 하 **고 종료, 다시 시작, 절전 모드 및 최대 절전 모드 명령에 대 한 액세스를 차단**하 고 **편집**을 클릭 합니다.

1. **사용** 설정을 선택 하 고 **확인**을 클릭 합니다.
 
   ![종료 설정](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Windows **시작** 메뉴에는 종료 명령이 더 이상 표시 되지 않습니다. **연결 끊기** 명령만 표시 됩니다.

    ![종료 명령](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>다음 단계
Windows 템플릿 VM을 준비 하는 방법에 대 한 문서를 참조 하세요. [Azure Lab Services에서 windows 템플릿 컴퓨터를 설정 하는 가이드](how-to-prepare-windows-template.md)