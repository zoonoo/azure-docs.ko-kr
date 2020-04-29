---
title: "' 그룹 정책 로컬 사용자 & 그룹 ' 정책을 적용 하는 동안 가상 컴퓨터가 응답 하지 않습니다."
description: 이 문서에서는 Azure VM (가상 머신)에서 부팅 하는 동안 로드 화면에서 정책 적용을 중지 하는 문제를 해결 하는 단계를 제공 합니다.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "80620858"
---
# <a name="virtual-machine-is-unresponsive-while-applying-group-policy-local-users--groups-policy"></a>' 그룹 정책 로컬 사용자 & 그룹 ' 정책을 적용 하는 동안 가상 컴퓨터가 응답 하지 않습니다.

이 문서에서는 Azure VM (가상 머신)에서 부팅 하는 동안 로드 화면에서 정책을 적용 하는 데 걸린 문제를 해결 하는 단계를 제공 합니다.

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM의 스크린샷을 볼 때 *로컬 사용자 및 그룹 정책 적용 그룹 정책*메시지와 함께 화면이 로드 되지 않는 것을 볼 수 있습니다.

![Alt 텍스트: 로컬 사용자 및 그룹 정책 로드 그룹 정책 적용을 보여 주는 화면 (Windows Server 2012)입니다.](media/troubleshoot-vm-unresponsive-group-policy-local-users/1.png)

Windows Server 2012

![Alt 텍스트: 로컬 사용자 및 그룹 정책 로드를 적용 하는 화면 그룹 정책 (Windows Server 2012 R2)](media/troubleshoot-vm-unresponsive-group-policy-local-users/2.png)

Windows Server 2012 R2

## <a name="cause"></a>원인

이 고정의 증상은 Windows 프로필 서비스 동적 연결 라이브러리 (*profsvc*)의 코드 오류로 인해 발생 합니다.

> [!NOTE]
> 이 오류는 Windows Server 2012 및 Windows Server 2012 r 2에만 적용 됩니다.

### <a name="the-policy-in-question"></a>문제의 정책

적용 되는 정책은 프로세스가 완료 되지 않습니다.

* *시스템 다시 시작 시 지정 된 기간 (일) 보다 오래 된 사용자 프로필을 삭제 하는 컴퓨터 Configuration\Policies\Administrative 템플릿 \ 시스템/사용자 프로 파일링*

다음 6 가지 조건이 true 인 경우에만이 정책이 중단 됩니다.

* *시스템 다시 시작 정책에서 지정 된 기간 (일) 보다 오래 된 사용자 프로필 삭제가* 사용 됩니다.
* 정리가 필요한 기간 요구 사항을 충족 하는 프로필이 있습니다.
* 프로필에 대 한 삭제 알림을 등록 한 구성 요소가 있습니다.
* 구성 요소는 서비스에 대 한 시작, 중지 또는 쿼리 정보와 같은 Windows의 SCM (서비스 제어 관리자) 구성 요소에서 데이터를 가져와야 하는 (직접 또는 간접) 호출을 수행 합니다.
* 서비스가 *자동*으로 시작 되도록 구성 되어 있습니다.
* 이 서비스는 로컬 시스템 등의 기본 제공 계정을 사용 하는 것과는 반대로 도메인 계정의 컨텍스트에서 실행 되도록 설정 됩니다.

### <a name="the-code-defect"></a>코드 오류

코드 오류는 SCM (서비스 제어 관리자) 및 프로필 서비스가 서로 잠금을 동시에 적용 하려고 시도 하기 때문에 발생 합니다. 잠금은 여러 서비스가 동시에 같은 데이터를 변경 하는 것을 방지 하기 위해 존재 하므로 손상이 발생 합니다. 일반적으로 여러 잠금 요청으로 인해 문제가 발생 하지 않습니다. 그러나이는 부팅 하는 동안 발생 하므로 서로 대기 하는 동안에는 두 서비스 모두 프로세스를 완료할 수 없습니다.

### <a name="os-bug-5880648---service-control-manager-deadlocks-with-the-delete-user-profiles-on-restart-policy"></a>OS 버그 5880648-"다시 시작 시 사용자 프로필 삭제" 정책으로 서비스 제어 관리자가 교착 상태 발생

다음과 같이 겹치는 두 가지 작업이 있습니다.

* 작업 1은 프로필 잠금을 획득 하지만 SCM 잠금을 아직 획득 하지 않았습니다.

  **하거나**

* 작업 2에서 SCM 잠금을 획득 하지만 프로필 잠금을 아직 획득 하지 않았습니다.

이 교착 상태가 발생 하면 두 번째 필수 잠금을 획득 하려고 하면 작업이 중단 됩니다.

### <a name="action-1---old-profile-deletion-notification-has-profile-lock-needs-scm-lock"></a>작업 1-이전 프로필 삭제 알림 ( **프로필 잠금**, **SCM 잠금**필요)

1. 먼저 이전 프로필 삭제로 설정 된 정책에서 내부 프로필 서비스 잠금을 획득 합니다.

   * 이 잠금은 *삭제 작업이* 진행 되는 동안 두 스레드가 프로필과 상호 작용 하는 것을 방지 하기 위한 것입니다.

2. 정책은 삭제할 수 있는 오래 된 프로필을 찾습니다.
3. 프로필 삭제의 일환으로 프로필 삭제 알림에 등록 된 구성 요소는 **서비스를 시작**하려고 시도 합니다.
4. 서비스를 시작 하기 전에 SCM (서비스 제어 관리자)은 **작업 2**의 스레드에서 보유 한 **내부 scm 잠금을** 획득 해야 합니다.

### <a name="action-2---profile-loadcreation-for-user-specific-data-has-scm-lock-needs-profile-lock"></a>작업 2-사용자 특정 데이터에 대 한 프로필 로드/만들기 ( **SCM 잠금**, **프로필 잠금**필요)

1. 부팅할 때 SCM은 해당 서비스에 종속 된 모든 서비스 뿐만 아니라 해당 그룹의 모든 *자동 시작* 서비스를 주문 해야 합니다.

2. Scm은 서비스를 주문할 때 서비스를 시작, 중지 또는 구성 하는 데 대 한 액세스를 제어 하는 데 사용 되는 **내부 scm 잠금을 획득** 합니다.

3. 서비스가 순서 대로 실행 되 면 SCM은 각 서비스를 반복 하 여 시작 합니다.

4. 서비스가 도메인 계정의 컨텍스트에서 실행 되는 경우 사용자 특정 데이터를 저장할 수 있도록 도메인 계정에 대해 프로필을 로드 하거나 만들어야 합니다.

5. 이 요청은 **프로필 서비스로**전송 됩니다.

6. 프로필 서비스는 **작업 1**에서 가져온 **내부 잠금** 에 대 한 액세스가 필요 합니다.

## <a name="solution"></a>솔루션

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM 만들기 및 액세스
2. 직렬 콘솔 및 메모리 덤프 수집 사용
3. VM 다시 빌드
4. 메모리 덤프 파일 수집

   > [!NOTE]
   > 이 부팅 오류가 발생할 경우 게스트 OS가 작동 하지 않습니다. 이 문제를 해결 하려면 오프 라인 모드에서 문제를 해결 합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [Vm 복구 명령의 1-3 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 를 사용 하 여 복구 vm을 준비 합니다.
2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

### <a name="enable-serial-console-and-memory-dump-collection"></a>직렬 콘솔 및 메모리 덤프 수집 사용

메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정 하려면 다음 스크립트를 실행 합니다.

1. 관리자 권한 명령 프롬프트 세션 (관리자 권한으로 실행)을 엽니다.
2. 다음 명령을 실행합니다.

   * 직렬 콘솔을 사용 하도록 설정 합니다.

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON`

     `bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200`

3. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기 (RAM)와 동일한 지 확인 합니다.

   * OS 디스크에 공간이 부족 한 경우 메모리 덤프 파일이 생성 될 위치를 변경 하 고 사용 가능한 공간이 충분 한 VM에 연결 된 데이터 디스크를 참조 합니다. 위치를 변경 하려면 아래 명령 `%SystemRoot%` 에서 데이터 디스크의 드라이브 문자 (예: "F:")로 대체 합니다.

#### <a name="suggested-configuration-to-enable-os-dump"></a>OS 덤프를 사용 하도록 설정 하기 위한 권장 구성

**손상 된 OS 디스크 로드:**

`REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

**ControlSet001에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

**ControlSet002에서 사용:**

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f`

`REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f`

### <a name="rebuild-the-vm"></a>VM 다시 빌드

Vm [복구 명령의 5 단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 를 사용 하 여 vm을 리 어셈블합니다.

### <a name="collect-the-memory-dump-file"></a>메모리 덤프 파일 수집

이 문제를 해결 하려면 먼저 크래시에 대 한 메모리 덤프 파일을 수집 하 고 메모리 덤프 파일을 사용 하 여 지원에 문의 해야 합니다. 덤프 파일을 수집 하려면 다음 단계를 수행 합니다.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>새 복구 VM에 OS 디스크를 연결 합니다.

1. [Vm 복구 명령의 1-3](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example) 단계를 사용 하 여 새 복구 vm을 준비 합니다.

2. 원격 데스크톱 연결를 사용 하 여 복구 VM에 연결 합니다.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>덤프 파일을 찾고 지원 티켓을 제출 합니다.

1. 복구 VM에서 연결 된 OS 디스크의 windows 폴더로 이동 합니다. 연결된 OS 디스크에 할당된 드라이브 문자가 F인 경우 F:\Windows로 이동해야 합니다.

2. Memory.dmp 파일을 찾은 다음 메모리 덤프 파일을 사용 하 여 [지원 티켓을 제출](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 합니다.

3. Memory.dmp 파일을 찾는 데 문제가 있는 경우 대신 [직렬 콘솔에서 비 마스크 인터럽트 (NMI) 호출](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/serial-console-windows#use-the-serial-console-for-nmi-calls) 을 사용 하는 것이 좋습니다. 이 가이드에 따라 NMI 호출을 사용 하 여 [커널 또는 전체 크래시 덤프](https://docs.microsoft.com/windows/client-management/generate-kernel-or-complete-crash-dump) 파일을 생성할 수 있습니다.
