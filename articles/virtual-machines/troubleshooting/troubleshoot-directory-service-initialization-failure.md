---
title: Windows 중지 오류 문제 해결 – 디렉터리 서비스 초기화 오류
description: Azure의 Active Directory 도메인 컨트롤러 VM(가상 머신)이 다시 시작해야 한다는 루프에 걸려 멈춰 있는 문제를 해결합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 3396f8fe-7573-4a15-a95d-a1e104c6b76d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/05/2020
ms.author: v-miegge
ms.openlocfilehash: 118c81dd52951729bfbbb97a510e693861666ee6
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2020
ms.locfileid: "83663941"
---
# <a name="troubleshoot-windows-stop-error--directory-service-initialization-failure"></a>Windows 중지 오류 문제 해결 – 디렉터리 서비스 초기화 오류

이 문서에서는 Azure의 Active Directory 도메인 컨트롤러 VM(가상 머신)이 루프에 걸려 멈춰 있으며 다시 시작해야 한다고 표시되는 문제를 해결하는 방법을 설명합니다.

## <a name="symptom"></a>증상

[부트 진단](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics)을 사용하여 VM의 스크린샷을 보면 스크린샷에 오류로 인해 VM을 다시 시작해야 한다는 메시지와 Windows Server 2008 R2의 경우 중지 코드 **0xC00002E1**이, Windows Server 2012 이상의 경우 **0xC00002E2**가 표시되어 있습니다.

![Windows Server 2012 시작 화면에 다음과 같은 메시지가 표시됩니다. “PC에 문제가 발생하여 다시 시작해야 합니다. 일부 오류 정보를 수집하고 있습니다. 그런 다음 자동으로 다시 시작합니다.”](./media/troubleshoot-directory-service-initialization-failure/1.png)

## <a name="cause"></a>원인

오류 코드 **0xC00002E2**는 **STATUS_DS_INIT_FAILURE**를 나타내고, 오류 코드 **0xC00002E1**은 **STATUS_DS_CANT_START**를 나타냅니다. 두 오류 모두 디렉터리 서비스에 문제가 있는 경우 발생합니다.

OS가 부팅되면 사용자 로그인을 인증하는 로컬 보안 인증 서버(**LSASS.exe**)에 의해 자동으로 강제로 다시 시작됩니다. VM의 운영 체제가 로컬 Active Directory 데이터베이스에 대한 읽기/쓰기 권한이 없는 도메인 컨트롤러인 경우에는 인증이 수행되지 않습니다. **AD(Active Directory)** 에 대한 액세스 권한이 없으므로 LSASS.exe가 인증할 수 없으며 강제로 OS를 다시 시작하게 됩니다.

이 오류는 다음과 같은 경우에 발생할 수 있습니다.

- 로컬 AD 데이터베이스(**NTDS.DIT**)를 포함하는 디스크에 대한 액세스 권한이 없습니다.
- 로컬 AD 데이터베이스(NTDS.DIT)를 포함하는 디스크에 사용 가능한 공간이 부족합니다.
- 로컬 AD 데이터베이스(NTDS.DIT) 파일이 없습니다.
- VM에 여러 디스크가 있고 SAN(스토리지 영역 네트워크) 정책이 잘못 구성되었습니다. SAN 정책이 **ONLINEALL**로 설정되어 있지 않으며 비OS 디스크가 디스크 관리자에서 오프라인 모드로 연결되어 있습니다.
- 로컬 AD 데이터베이스(NTDS.DIT) 파일이 손상되었습니다.

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM을 만들고 액세스합니다.
1. 디스크의 사용 가능한 공간을 확보합니다.
1. AD 데이터베이스를 포함하는 드라이브가 연결되어 있는지 확인합니다.
1. 디렉터리 서비스 복원 모드를 사용하도록 설정합니다.
1. **권장**: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.
1. SAN 정책을 다시 구성합니다.

> [!NOTE]
> 이 오류가 발생할 경우 게스트 OS가 작동하지 않습니다. 문제를 해결하기 위해 오프라인 모드에서 문제 해결을 진행하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="free-up-space-on-disk"></a>디스크에서 공간 확보

이제 디스크가 복구 VM에 연결되어 있으니 Active Directory 내부 데이터베이스를 포함하는 디스크에 작업을 올바르게 수행하기에 충분한 공간이 있는지 확인합니다.

1. 드라이브를 마우스 오른쪽 단추로 클릭하고 **속성**을 선택하여 디스크가 가득 차 있는지 확인합니다.
1. 디스크의 사용 가능한 공간이 300Mb 미만인 경우 [PowerShell을 사용하여 최대 1Tb로 확장합니다](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk).
1. 디스크의 사용 중인 공간이 1Tb에 도달한 경우 디스크 정리를 수행합니다.

   1. PowerShell을 사용하여 손상된 VM에서 [데이터 디스크를 분리](https://docs.microsoft.com/azure/virtual-machines/windows/detach-disk#detach-a-data-disk-using-powershell)합니다.
   1. 손상된 VM에서 분리한 후에는 [데이터 디스크를 작동하는 VM에 연결](https://docs.microsoft.com/azure/virtual-machines/windows/attach-disk-ps#attach-an-existing-data-disk-to-a-vm)합니다.
   1. [디스크 정리 도구](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup)를 사용하여 추가 공간을 확보합니다.

1. **선택 사항** - 더 많은 공간이 필요한 경우 CMD 인스턴스를 열고 `defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g` 명령을 입력하여 드라이브 조각 모음을 수행합니다.

  * 명령에서 `<LETTER ASSIGNED TO THE OS DISK>`를 OS 디스크의 문자로 바꿉니다. 예를 들어 디스크 문자가 `F:`이면 명령은 `defrag F: /u /x /g`가 됩니다.

  * 조각화 수준에 따라 조각 모음에 몇 시간까지 걸릴 수도 있습니다.

디스크에 충분한 공간이 있는 경우 다음 작업으로 넘어갑니다.

### <a name="check-that-the-drive-containing-the-active-directory-database-is-attached"></a>Active Directory 데이터베이스를 포함하는 드라이브가 연결되어 있는지 확인

1. 관리자 권한 CMD 인스턴스를 열고 다음 명령을 실행합니다.

   1. 레지스트리 파일을 로드합니다.

      `REG LOAD HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM`

      `f:`라는 지정은 디스크가 드라이브 `F:`라고 가정합니다. OS 디스크를 포함하는 드라이브에 해당하는 드라이브 문자를 사용하세요.

   1. **NTDS.DIT**의 드라이브 문자와 폴더를 확인합니다.

      ```
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Working Directory"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "DSA Database file"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database backup path"
      REG QUERY "HKLM\BROKENSYSTEM\ControlSet001\Services\NTDS\parameters" /v "Database log files path"
      ```

   1. 레지스트리 파일을 언로드합니다.

      `REG UNLOAD HKLM\BROKENSYSTEM`

1. Azure Portal을 사용하여, NTDS.DIT가 설정된 드라이브가 VM에 추가되었는지 확인합니다.
1. 게스트 OS의 디스크 관리 콘솔을 사용하여, NTDS.DIT를 포함하는 디스크가 온라인 상태인지 확인합니다.
   1. 디스크 관리 도구는 **관리 도구 > 컴퓨터 관리 > 스토리지**에서 찾을 수 있습니다. 또는 CMD 인스턴스에서 `diskmgmt.msc` 명령을 사용하여 액세스할 수 있습니다.
1. 디스크가 VM에 연결되어 있지 않으면 데이터 디스크를 다시 연결하여 문제를 해결합니다.

   디스크가 정상적으로 연결되어 있는 경우 다음 작업으로 넘어갑니다.

### <a name="enable-directory-services-restore-mode"></a>디렉터리 서비스 복원 모드 사용

**DSRM(디렉터리 서비스 복원 모드)** 모드에서 부팅되도록 VM을 설정하여 부팅 중에 NTDS.DIT 파일의 존재 여부 검사를 우회합니다.

1. 계속하기 전에 디스크를 복구 VM에 연결하는 이전 작업을 완료했고 NTDS.DIT 파일이 어느 디스크에 있는지 확인했어야 합니다.
1. 관리자 권한 CMD 인스턴스를 사용하여 해당 저장소에서 부팅 파티션 정보를 나열하여 활성 파티션에서 식별자를 찾습니다.

   `bcdedit /store <Drive Letter>:\boot\bcd /enum`

   `< Drive Letter >`를 이전 단계에서 확인한 문자로 바꿉니다.

   ![관리자 권한 CMD 인스턴스에 ‘bcdedit /store <Drive Letter>:\boot\bcd /enum’ 명령을 입력하여 Windows 부팅 관리자와 식별자가 표시된 스크린샷.](./media/troubleshoot-directory-service-initialization-failure/2.png)

1. 부팅 파티션에서 `safeboot DsRepair` 플래그를 사용하도록 설정합니다.

   `bcdedit /store <Drive Letter>:\boot\bcd /set {<Identifier>} safeboot dsrepair`

   `< Drive Letter >` 및 `< Identifier >`를 이전 단계에서 확인한 값으로 바꿉니다.

1. 부팅 옵션을 다시 쿼리하여 변경 내용이 올바르게 설정되었는지 확인합니다.

   ![관리자 권한 CMD 인스턴스에서 safeboot DsRepair 플래그를 사용하도록 설정한 후의 스크린샷.](./media/troubleshoot-directory-service-initialization-failure/3.png)

### <a name="recommended-before-you-rebuild-the-vm-enable-serial-console-and-memory-dump-collection"></a>권장: VM을 다시 빌드하기 전에 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정하기

메모리 덤프 수집과 직렬 콘솔을 사용하려면 관리자 권한 명령 프롬프트 세션(관리자 권한으로 실행)을 열고 다음 명령을 실행하여 다음 스크립트를 실행합니다.

1. 직렬 콘솔을 사용하도록 설정합니다.

  ```
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
  bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
  ```

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 크거나 같은지 확인합니다.

  1. OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크를 참조합니다.

     위치를 변경하려면 다음 명령에서 `%SystemRoot%`를 데이터 디스크의 드라이브 문자(예: `F:`)로 바꿉니다.

  #### <a name="the-following-configuration-is-suggested-to-enable-os-dump"></a>OS 덤프를 사용하도록 설정하려면 다음 구성이 권장됩니다.

  **손상된 OS 디스크를 로드합니다.**

  `REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM`

  **ControlSet001에서 사용하도록 설정합니다.**

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **ControlSet002에서 사용하도록 설정합니다.**

  ```
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
  REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f
  ```

  **손상된 OS 디스크를 언로드합니다.**

  `REG UNLOAD HKLM\BROKENSYSTEM`

### <a name="rebuild-the-vm"></a>VM 다시 빌드

1. [VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 조합합니다.

### <a name="reconfigure-the-storage-area-network-policy"></a>SAN(스토리지 영역 네트워크) 정책 다시 구성

1. DSRM 모드로 부팅한 경우 로그인 할 수 있는 유일한 사용자는 VM이 도메인 컨트롤러로 승격되었을 때 사용된 복구 관리자입니다. 다른 모든 사용자에게는 인증 오류가 표시됩니다.

   1. 사용할 수 있는 다른 DC가 없는 경우 `.\administrator` 또는 `machinename\administrator`와 DSRM 암호를 사용하여 로컬에 로그인해야 합니다.

1. 모든 디스크가 온라인 상태가 되도록 SAN 정책을 설정합니다.

   1. 관리자 권한 CMD 인스턴스를 열고 `DISKPART`를 입력합니다.
   1. 디스크 목록을 쿼리합니다.

      `DISKPART> list disk`

   1. 다음 명령을 입력하여 온라인 상태로 전환해야 하는 디스크를 선택하고 SAN 정책을 변경합니다.

      ```
      DISKPART> select disk 1
      Disk 1 is now the selected disk.

      DISKPART> attributes disk clear readonly
      Disk attributes cleared successfully.

      DISKPART> attributes disk
      Current Read-only State : No
      Read-only : No
      Boot Disk : No
      Pagefile Disk : No
      Hibernation File Disk : No
      Crashdump Disk : No
      Clustered Disk : No

      DISKPART> online disk
      DiskPart successfully onlined the selected disk.

      DISKPART> san
      SAN Policy : Online All
      ```

1. 문제가 해결되면 `DsRepair safeboot` 플래그가 제거되었는지 확인합니다.

   `bcdedit /deletevalue {default} safeboot dsrepair`

1. VM이 다시 시작됩니다.

   > [!NOTE]
   > VM이 온-프레미스에서 방금 마이그레이션되었으며 온-프레미스에서 Azure로 더 많은 도메인 컨트롤러를 마이그레이션하려는 경우 아래 문서의 단계를 수행하여 추후 마이그레이션에서 이 문제가 발생하지 않도록 해야 합니다.
   >
   > [Azure PowerShell을 사용하여 기존 온-프레미스 Hyper-V 도메인 컨트롤러를 Azure에 업로드하는 방법](https://support.microsoft.com/help/2904015)
