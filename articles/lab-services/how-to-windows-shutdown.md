---
title: Azure Lab Services에서 Windows 종료 동작을 제어 하기 위한 가이드 | Microsoft Docs
description: 유휴 Windows 가상 머신을 자동으로 종료 하 고 Windows 종료 명령을 제거 하는 단계입니다.
ms.topic: article
ms.date: 09/29/2020
ms.openlocfilehash: 248bbeabaf704ba636e2f82c7a93d0ee90a09f22
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647701"
---
# <a name="guide-to-controlling-windows-shutdown-behavior"></a>Windows 종료 동작 제어 가이드

Azure Lab Services는 Windows Vm (가상 머신)이 예기치 않게 실행 되지 않도록 몇 가지 비용 제어를 제공 합니다.
 - [일정 설정](./tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
 - [사용자에 대한 할당량 설정](./how-to-configure-student-usage.md#set-quotas-for-users)
 - [연결 해제 시 자동 종료 사용](./how-to-enable-shutdown-disconnect.md)

이러한 비용 제어를 사용 하는 경우에도 Windows VM이 예기치 않게 계속 실행 되는 경우가 있습니다. 그 결과, 학생의 할당량에서 공제 됩니다.

- **RDP 창이 열려 있습니다.**
  
    학생이 RDP를 사용 하 여 VM에 연결 하는 경우 실수로 RDP 창이 열린 상태로 있을 수 있습니다.  RDP 창이 열려 있는 동안 **에는 연결 끊기에 대 한 자동 종료** 설정이 rdp 세션의 연결이 끊긴 후에만 트리거되 므로 적용 되지 않습니다.

- **Windows shutdown 명령을 사용 하 여 VM을 해제 합니다.**
  
    학생은 Windows 종료 명령 또는 Windows에서 제공 되는 기타 종료 메커니즘을 사용 하 여 [Azure Lab Services ' 중지 ' 단추](./how-to-use-classroom-lab.md#start-or-stop-the-vm)를 사용 하는 대신 VM을 해제할 수 있습니다.  이 경우 Azure Lab Services 관점에서 VM은 계속 사용 되 고 있습니다.
    
이러한 상황을 방지 하기 위해이 가이드에서는 유휴 Windows VM을 자동으로 종료 하 고 **시작** 메뉴에서 windows 종료 명령을 제거 하는 단계를 제공 합니다.  

> [!NOTE]
> 또한 VM은 학생이 VM을 시작할 때 할당량에서 예기치 않게 공제 RDP를 사용 하 여 실제로 연결 하지 않을 수 있습니다.  이 가이드는 현재이 시나리오를 해결 *하지* 않습니다.  대신, 학생 들이 시작한 후 RDP를 사용 하 여 VM에 즉시 연결 하도록 미리 알림을 받아야 합니다. 또는 VM을 중지 해야 합니다.

## <a name="remove-windows-shutdown-command-from-start-menu"></a>시작 메뉴에서 Windows 종료 명령 제거

Windows **로컬 그룹 정책** 설정을 사용 하 여 **시작** 메뉴에서 종료 명령을 제거할 수도 있습니다.

종료 명령을 제거 하려면 템플릿 VM에 연결 하 고 아래 PowerShell 스크립트를 실행할 수 있습니다.

```powershell
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\Explorer" -Name "HidePowerOptions" -Value 1 -Force
```

또는 템플릿 VM을 사용 하 여 다음 수동 단계를 수행 하도록 선택할 수 있습니다.

1. Windows 키를 누르고 **gpedit.msc** 를 입력 한 다음 **그룹 정책 편집 (제어판)** 을 선택 합니다.

1. **컴퓨터 구성 > 관리 템플릿 > 시작 메뉴 및 작업 표시줄** 로 이동 합니다.  

    ![로컬 그룹 정책 편집기](./media/how-to-windows-shutdown/group-policy-shutdown.png)

1. 제거를 마우스 오른쪽 단추로 클릭 하 **고 종료, 다시 시작, 절전 모드 및 최대 절전 모드 명령에 대 한 액세스를 차단** 하 고 **편집** 을 클릭 합니다.

1. **사용** 설정을 선택 하 고 **확인** 을 클릭 합니다.
 
   ![종료 설정](./media/how-to-windows-shutdown/edit-shutdown.png)

1. Windows **시작** 메뉴에는 종료 명령이 더 이상 표시 되지 않습니다. **연결 끊기** 명령만 표시 됩니다.

    ![종료 명령](./media/how-to-windows-shutdown/start-menu.png)

## <a name="next-steps"></a>다음 단계
Windows 템플릿 VM을 준비 하는 방법에 대 한 문서를 참조 하세요. [Azure Lab Services에서 windows 템플릿 컴퓨터를 설정 하는 가이드](how-to-prepare-windows-template.md)