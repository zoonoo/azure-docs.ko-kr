---
title: "'그룹 정책 로컬 사용자 & 그룹' 정책을 적용 하는 동안 가상 시스템이 응답 하지 않습니다."
description: 이 문서에서는 Azure 가상 시스템(VM)에서 부팅하는 동안 로드 화면이 정책을 적용하는 데 문제가 있는 문제를 해결하는 단계를 제공합니다.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: ff113268-f5bf-4e6a-986e-63b9b0ceff20
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 04/02/2020
ms.author: v-mibufo
ms.openlocfilehash: 085880122e9a80e976cfe59686748b58aeba1922
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80620858"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>'그룹 정책 로컬 사용자 & 그룹' 정책을 적용 하는 동안 가상 시스템이 응답 하지 않습니다.

이 문서에서는 Azure 가상 시스템(VM)에서 부팅 하는 동안 정책을 적용 하 고 로드 화면이 붙어 있는 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용하여 VM의 스크린샷을 볼 때 *화면이 그룹 정책 로컬 사용자 및 그룹 정책 적용이라는*메시지와 함께 로드가 막혀 있음을 알 수 있습니다.

![대체 텍스트: 그룹 정책 로컬 사용자 및 그룹 정책 로드 적용(Windows Server 2012)을 표시하는 화면입니다.](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![대체 텍스트: 그룹 정책 로컬 사용자 및 그룹 정책 로드 적용(Windows Server 2012 R2)을 표시하는 화면입니다.](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>원인

이 동결의 증상은 Windows 프로필 서비스 동적 링크 라이브러리 *(profsvc.dll)의*코드 결함으로 인해 발생합니다.

> [!NOTE]
> 이 결함은 Windows 서버 2012 및 Windows 서버 2012 R2에서만 적용됩니다.

### <a name="the-policy-in-question"></a>문제의 정책

프로세스를 완료하지 않는 적용되는 정책은 다음과 같은 것입니다.

* *컴퓨터 구성\정책\관리 템플릿\시스템/사용자 프로필\시스템 다시 시작 시 지정된 일 수보다 오래된 사용자 프로필 삭제*

이 정책은 다음 여섯 가지 조건이 true인 경우에만 중단됩니다.

* *시스템 다시 시작 정책에서 지정된 일 수보다 오래된 사용자 프로필 삭제가* 활성화됩니다.
* 정리가 필요한 연령 요건을 충족하는 프로필이 있습니다.
* 프로필에 대한 삭제 알림을 위해 등록된 구성요소가 있습니다.
* 구성 요소는 시작, 중지 또는 서비스에 대한 쿼리 정보와 같은 Windows의 SCM(서비스 제어 관리자) 구성 요소에서 데이터를 수집해야 하는 직접 또는 간접 호출(직접 또는 간접)을 합니다.
* *자동으로*시작하도록 구성된 서비스가 있습니다.
* 이 서비스는 로컬 시스템과 같은 기본 제공 계정을 사용하는 대신 도메인 계정의 컨텍스트에서 실행되도록 설정됩니다.

### <a name="the-code-defect"></a>코드 결함

코드 결함은 SCM(서비스 제어 관리자)과 서로 잠금을 동시에 적용하려는 프로필 서비스 때문입니다. 여러 서비스가 동시에 동일한 데이터를 변경하지 못하도록 잠금이 존재하여 손상이 발생할 수 있습니다. 일반적으로 여러 잠금 요청으로 인해 문제가 발생하지 않습니다. 그러나 부팅 하는 동안 발생 하기 때문에 두 서비스는 서로 대기 붙어 있기 때문에 프로세스를 완료할 수 없습니다.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS 버그 5880648 - "다시 시작할 때 사용자 프로필 삭제" 정책이 있는 서비스 제어 관리자 교착 상태

두 가지 작업이 겹쳐서 다음과 같은 작업을 수행합니다.

* 작업 1은 프로파일 잠금을 획득하지만 아직 SCM 잠금을 획득하지 못했습니다.

  **및**

* 작업 2는 SCM 잠금을 획득했지만 아직 프로필 잠금을 획득하지 못했습니다.

이 교착 상태가 발생하면 두 번째 필수 잠금을 획득하려는 시도가 작업을 중단합니다.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>작업 1 - 이전 프로필 삭제 **알림(프로필 잠금이**있음, **SCM 잠금이**필요)

1. 첫째, 이전 프로파일을 삭제하도록 설정된 정책은 내부 프로파일 서비스 잠금을 획득합니다.

   * 이 잠금은 *삭제 작업이* 진행되는 동안 두 스레드가 프로파일과 상호 작용하는 것을 방지합니다.

2. 이 정책은 삭제할 수 있을 만큼 오래된 프로필을 찾습니다.
3. 프로필 삭제의 일부로 프로필 삭제 알림에 등록한 구성 요소는 **서비스를 시작하려고**시도합니다.
4. 서비스를 시작하기 전에 SCM(서비스 제어 관리자)은 **작업 2의**스레드가 보유한 **내부 SCM 잠금을** 획득해야 합니다.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>작업 2 - 사용자 특정 데이터에 대한 프로필 로드/생성(SCM **잠금,** **프로필 잠금**필요)

1. 부팅 시 SCM은 해당 그룹이 제공하는 모든 *자동 시작* 서비스와 해당 서비스가 종속된 모든 서비스를 주문해야 합니다.

2. **SCM은** 서비스를 주문할 때 서비스를 시작, 중지 또는 구성하는 액세스를 제어하는 데 사용되는 내부 SCM 잠금을 획득합니다.

3. 서비스가 순서대로 완료되면 SCM은 각 서비스를 순환하고 시작합니다.

4. 서비스가 도메인 계정의 컨텍스트에서 실행되는 경우 사용자 특정 데이터를 저장할 수 있도록 도메인 계정에 대해 프로필을 로드하거나 만들어야 합니다.

5. 이 요청은 **프로필 서비스**로 전송됩니다.

6. 프로필 서비스는 **작업 1에서**획득한 **내부 잠금에** 액세스해야 합니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM 만들기 및 액세스
2. 직렬 콘솔 및 메모리 덤프 컬렉션 사용
3. VM 재생성
4. 메모리 덤프 파일 수집

   > [!NOTE]
   > 이 부팅 오류가 발생하면 게스트 OS가 작동하지 않습니다. 이 문제를 해결 하려면 오프라인 모드에서 문제 해결 됩니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 복구 VM을 준비합니다.
2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="enable-serial-console-and-memory-dump-collection"></a>직렬 콘솔 및 메모리 덤프 컬렉션 사용

메모리 덤프 컬렉션 및 직렬 콘솔을 사용하려면 아래 스크립트를 실행하십시오.

1. 높은 명령 프롬프트 세션을 엽니다(관리자로 실행).
2. 다음 명령을 실행합니다.

   * 직렬 콘솔 사용:

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS 디스크의 사용 가능한 공간이 VM의 RAM(메모리 크기)만큼인지 확인합니다.

   * OS 디스크에 공간이 충분하지 않으면 메모리 덤프 파일이 생성될 위치를 변경하고 여유 공간이 충분한 VM에 연결된 데이터 디스크를 참조해야 합니다. 위치를 변경하려면 아래 `%SystemRoot%` 명령의 데이터 디스크의 드라이브 문자(예: "F:")로 바꿉니다.

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS 덤프를 사용하도록 설정하는 권장 구성

**로드 깨진 OS 디스크:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**제어Set001에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**제어Set002에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>VM 재생성

[VM 복구 명령의 5단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 VM을 다시 어셈블합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결하려면 먼저 충돌에 대한 메모리 덤프 파일을 수집하고 메모리 덤프 파일과 지원을 문의해야 합니다. 덤프 파일을 수집하려면 다음 단계를 따르십시오.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>OS 디스크를 새 복구 VM에 연결

1. [VM 복구 명령의 1-3단계를](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 사용하여 새 복구 VM을 준비합니다.

2. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾아 지원 티켓을 제출합니다.

1. 복구 VM에서 연결된 OS 디스크의 창 폴더로 이동합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 F인 경우 F:\Windows로 이동해야 합니다.

2. memory.dmp 파일을 찾은 다음 메모리 덤프 [파일로 지원 티켓을 제출합니다.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)

3. memory.dmp 파일을 찾는 데 문제가 있는 경우 [대신 직렬 콘솔에서 마스크할 수 없는 인터럽트(NMI) 호출을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 사용할 수 있습니다. 가이드를 따라 NMI 호출을 사용하여 [커널을 생성하거나 크래시 덤프](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) 파일을 완료할 수 있습니다.
