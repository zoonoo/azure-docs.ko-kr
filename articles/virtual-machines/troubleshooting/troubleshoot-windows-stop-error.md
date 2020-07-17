---
title: Windows 중지 오류 - 메모리 없음 상태
description: 이 문서에서는 Windows가 시작 되지 않는 문제를 해결 하는 단계를 제공 하 고 "상태 메모리 없음" 상태를 표시 합니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: d29c7e49-4578-43c8-b829-831da4e48932
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 06/26/2020
ms.author: v-mibufo
ms.openlocfilehash: 67064cf694445acf8472b958660133c2f2d31db9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85660950"
---
# <a name="windows-stop-error---status-no-memory"></a>Windows 중지 오류 - 메모리 없음 상태

이 문서에서는 Windows가 시작 되지 않는 문제를 해결 하는 단계를 제공 하 고, 상태 또는 오류 #0xC0000017 코드를 표시 하는 단계를 제공 합니다 ("상태 메모리 없음"이 라고도 함)

## <a name="symptom"></a>증상

[부팅 진단을](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/boot-diagnostics) 사용 하 여 VM (가상 머신)의 스크린샷을 볼 때 스크린 샷에 오류 코드가 표시 됩니다 `0xC0000017` . 실행 중인 Windows 버전에 따라 **Windows 부팅 관리자나** **복구 화면**에이 코드가 표시 될 수 있습니다.

   **Windows 부팅 관리자**

   ![Windows 부팅 관리자에서 "Windows를 시작 하지 못했습니다. 최근 하드웨어 또는 소프트웨어 변경이 원인일 수 있습니다. 아래로 스크롤하면 상태 코드 "0xC0000017" 뿐만 아니라 "필요한 파일이 없거나 오류가 포함 되어 있어 응용 프로그램 또는 운영 체제를 로드할 수 없습니다." 라는 정보를 볼 수 있습니다.](./media/troubleshoot-windows-stop-error/1.png)

   **복구 화면**
 
   !["PC/장치를 복구 해야 함을 나타내는 복구 화면 Ramdisk 장치를 만드는 데 사용할 수 있는 메모리가 부족 합니다. " 또한 오류 코드 "0xC0000017"이 표시 됩니다.](./media/troubleshoot-windows-stop-error/2.png)

## <a name="cause"></a>원인

운영 체제의 디스크가 꽉 찼거나, 너무 단편화 되었거나, 운영 체제 (OS)가 메모리 나 페이지 파일에 액세스할 수 없거나, 둘 다에 액세스할 수 없는 경우

## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요:

1. 복구 VM 만들기 및 액세스
1. 디스크 공간 확보
1. BCD 저장소에서 잘못 된 메모리를 정리 합니다.
1. 페이지 파일을 기본 위치로 복원
1. 직렬 콘솔 및 메모리 덤프 수집 사용
1. VM 다시 빌드

> [!NOTE]
> 이 오류가 발생할 경우 게스트 OS가 작동하지 않습니다. 문제를 해결하기 위해 오프라인 모드에서 문제 해결을 진행하세요.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령의 1~3단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands)를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="for-generation-2-vms-assign-a-letter-to-the-extensible-firmware-interface-efi-partition"></a>2 세대 Vm의 경우 EFI (Extensible 펌웨어 인터페이스) 파티션에 문자를 할당 합니다.

2 세대 VM을 사용 하는 경우 연결 된 디스크의 EFI 파티션에 문자를 할당 하지 않을 수 있습니다. 이 문제 해결 가이드를 진행 하기 전에 다음 단계를 수행 하 여 파티션에 문자를 할당 해야 합니다.

1. Windows 검색에서 `diskmgmt` **디스크 관리 콘솔**을 입력 하 고 엽니다.
1. 복구 VM에 연결 된 손상 된 디스크를 확인 합니다. 일반적으로이 디스크는 콘솔의 마지막에 표시 되며 숫자 값이 가장 높습니다.
1. 이 디스크에는 **EFI 시스템 파티션을**보유 하는 파티션이 있습니다. 여기에는 문자 값 (예: 드라이브 *F:*)도 할당 되지 않습니다. 모든 파티션이 할당 된 경우 디스크의 공간을 확보 하기 위해 건너뛸 수 있습니다. 그렇지 않으면 계속 해 서이 디스크에 문자를 할당 합니다.

   ![디스크 관리 콘솔에는 연결 된 디스크 "디스크 2"가 포함 되 고 할당 되지 않은 파티션 (100, "EFI 시스템 파티션")도 포함 됩니다.](./media/troubleshoot-windows-stop-error/3.png)

1. 관리자 권한으로 관리자 권한 명령 프롬프트를 열고 `diskpart` 를 입력 하 여 **DISKPART** 도구를 시작 합니다.
1. 다음 명령을 입력합니다.

   ```
   list disk
   sel disk <NUMBER OF THE ATTACHED DISK>
   list partition
   sel partition <NUMBER OF THE SYSTEM (EFI) PARTION>
   assign
   ```
   
   - 명령에서을 `<NUMBER OF THE ATTACHED DISK>` 2 단계에서 확인 한 디스크 번호로 바꿉니다.
   - 명령에서를 `<NUMBER OF THE SYSTEM PARTION>` EFI 시스템 파티션의 파티션 번호로 바꿉니다. 이 파티션에는 아직 문자를 할당 하지 않았지만 형식 **시스템** 이어야 하 고 크기가 100mb 여야 합니다.

   > [!NOTE]
   > 디스크 관리 콘솔과 DISKPART 도구에 나열 된 파티션을 비교 하면 연결 된 디스크의 EFI 시스템 파티션에 해당 하는 파티션 번호를 확인 하는 데 도움이 될 수 있습니다.

1. 명령 프롬프트 창을 닫습니다.

### <a name="free-up-space-on-the-disk"></a>디스크 공간 확보

이제 손상 된 디스크가 복구 VM에 연결 되었으므로 해당 디스크의 OS에 제대로 작동 하는 데 충분 한 공간이 있는지 확인 해야 합니다. 

1. 연결 된 디스크의 드라이브를 마우스 오른쪽 단추로 클릭 하 고 **속성**을 선택 하 여 디스크가 꽉 찬 경우를 확인 합니다.
1. 디스크의 **사용 가능한 공간이 300 Mb 미만인**경우 [PowerShell을 사용 하 여 최대 1tb까지 확장](https://docs.microsoft.com/azure/virtual-machines/windows/expand-os-disk)합니다.
1. 디스크 크기가 **1tb**이면 디스크 정리를 수행 해야 합니다. [디스크 정리 도구](https://support.microsoft.com/help/4026616/windows-10-disk-cleanup) 를 사용 하 여 공간을 확보할 수 있습니다.
1. 관리자 권한 명령 프롬프트 (관리자 권한으로 실행) 인스턴스를 열고 드라이브에서 조각화를 취소 합니다.

   ``
   defrag <LETTER ASSIGNED TO THE OS DISK>: /u /x /g
   ``
   
   - 조각화 수준에 따라 조각화 취소에 시간이 걸릴 수 있습니다.
   - 명령에서를 `<LETTER ASSIGNED TO THE OS DISK>` OS 디스크의 문자 (예: *F:* 드라이브)로 바꿉니다.

### <a name="clean-out-bad-memory-from-the-boot-configuration-data-bcd-store"></a>부팅 구성 데이터 (BCD) 저장소에서 잘못 된 메모리를 정리 합니다.

1. 관리자 권한 명령 프롬프트를 엽니다 (관리자 권한으로 실행).
1. 다음 명령을 사용 하 여 부팅 구성 파일에서 잘못 된 메모리 플래그를 쿼리 합니다.

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /enum {badmemory}
   ``
   
   - 명령에서를 `<LETTER ASSIGNED TO THE OS DISK>` OS 디스크의 문자 (예: *F:* 드라이브)로 바꿉니다.

1. 쿼리에서 잘못 된 메모리 블록을 표시 하지 않는 경우 다음 태스크로 건너뜁니다. 그렇지 않으면 4 단계로 이동 합니다.
1. 잘못 된 메모리 블록이 식별 되 면 이러한 블록은 ramdisk 생성을 방지 하 고 다음 명령을 사용 하 여 삭제 해야 합니다.

   ``
   bcdedit /store <LETTER ASSIGNED TO THE OS DISK>:\boot\bcd /deletevalue {badmemory} badmemorylist
   ``
   
   - 명령에서를 `<LETTER ASSIGNED TO THE OS DISK>` OS 디스크의 문자 (예: *F:* 드라이브)로 바꿉니다.

### <a name="restore-the-page-file-to-its-default-location"></a>페이지 파일을 기본 위치로 복원

페이지 파일은 컴퓨터의 RAM (임의 액세스 메모리)에서 보유 하지 못하는 데이터를 오버플로/백업 형태로 저장 합니다. 파일은 기본 Azure 위치인 임시 드라이브가 아닌 VHD에서 호스팅될 수 있습니다. True 이면 파일에 액세스할 수 없으며 기본 위치로 복원 해야 합니다.

단계를 수행 하기 전에 정상 디스크에 **\windows\system32\config** 폴더의 복사본을 만들어야 합니다. 이 단계를 수행 하면에서 원치 않는 변경 내용을 취소할 수 있습니다. 중요 한 시스템 파일에 대 한 작업을 수행 하 고 있으므로이 예방 조치를 수행 하는 것이 좋습니다.

1. Windows 검색에서 **regedit** 를 입력 하 고 레지스트리 편집기 응용 프로그램을 엽니다.
1. 레지스트리 편집기에서 키 **HKEY_LOCAL_MACHINE** 를 강조 표시 하 고 메뉴에서 **파일 > 로드 하이브 ...** 를 선택 합니다.

   ![레지스트리 편집기의 Hive 로드 메뉴](./media/troubleshoot-windows-stop-error/4.png)

1. Hive 로드 대화 상자에서 **\windows\system32\config\SYSTEM** 를 선택 하 고 열기를 클릭 합니다.
   1. **BROKENSYSTEM**에 입력 해야 하는 이름을 입력 하 라는 메시지가 표시 됩니다. 이 이름은 문제를 해결 하는 동안 영향을 받는 하이브를 구분 하는 데 도움이 됩니다.
   1. **HKEY_LOCAL_MACHINE** 를 확장 하 여 추가한 새 BROKENSYSTEM 키를 확인 합니다.
1. 레지스트리 편집기를 사용 하 여 컴퓨터가 부팅 되는 ControlSet 확인 합니다.
   1. **HKEY_LOCAL_MACHINE >> BROKENSYSTEM >> Select**로 이동 합니다.
   1. 나열 된 키에서 Current의 데이터 값을 확인 합니다. 예를 들어이 값이 **1** 또는 **0x00000001 (1)** 이면 컨트롤 집합은 ControlSet001가 됩니다.
1. 페이지 파일 만들기가 구성 된 위치를 확인 합니다.
   1. HKEY_LOCAL_MACHINE \BROKENSYSTEM에서 4 단계에서 확인 한 ControlSet 번호와 일치 하는 디렉터리 (예: **ControlSet001**)를 확장 합니다.
   1. ** >> 세션 관리자 >> 메모리 관리** 로 이동 하 여 **existingpagefiles** 키의 위치를 확인 합니다.
   1. 이 키는 임시 드라이브의 기본 Azure 위치에 있어야 합니다. 데이터 디스크 드라이브 또는 OS 드라이브와 같은 다른 위치에 있는 VHD가 없는 경우에는 삭제 해야 합니다.
   1. 파일 탐색기에서 해당 위치로 이동한 다음 **pagefile.sys** 파일을 삭제 합니다.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

메모리 덤프 수집 및 직렬 콘솔을 사용 하도록 설정 하려면 다음 스크립트를 실행 합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
1. BCD 저장소 데이터를 나열 하 고 다음 단계에서 사용할 부팅 로더 식별자를 확인 합니다.

   1. 1 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.
   
      ``
      bcdedit /store <BOOT PARTITON>:\boot\bcd /enum
      ``
   
   - 명령에서를 `<BOOT PARTITON>` 부팅 폴더를 포함 하는 연결 된 디스크의 파티션 문자로 바꿉니다.

      ![Windows 부팅 로더 아래에 식별자 번호를 나열 하는 1 세대 VM에 BCD 저장소를 나열 하는 출력입니다.](./media/troubleshoot-windows-stop-error/5.png)

   1. 2 세대 VM의 경우 다음 명령을 입력 하 고 나열 된 식별자를 확인 합니다.
   
      ``
      bcdedit /store <LETTER OF THE EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum
      ``
   
   - 명령에서를 `<LETTER OF THE EFI SYSTEM PARTITION>` EFI 시스템 파티션의 문자로 바꿉니다.
   - **EFI 시스템 파티션**으로 레이블이 지정 된 적절 한 시스템 파티션을 식별 하는 디스크 관리 콘솔을 시작 하는 것이 유용할 수 있습니다.
   - 식별자는 고유한 GUID 이거나 기본 **bootmgr**일 수 있습니다.

1. 다음 명령을 실행 하 여 직렬 콘솔을 사용 하도록 설정 합니다.

   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
   
   - 명령에서를 `<VOLUME LETTER WHERE THE BCD FOLDER IS>` BCD 폴더의 문자로 바꿉니다.
   - 명령에서을 `<BOOT LOADER IDENTIFIER>` 이전 단계에서 찾은 식별자로 바꿉니다.

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 큰지 확인합니다.

   OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크의 위치를 참조합니다. 위치를 변경 하려면 다음 명령에서 **% SystemRoot%** 를 데이터 디스크의 드라이브 문자 (예: 드라이브 **F:**)로 바꿉니다.

   OS 덤프를 사용하도록 설정하기 위한 권장 구성:

   **손상된 OS 디스크에서 레지스트리 하이브를 로드합니다.**

   ```
   REG LOAD HKLM\BROKENSYSTEM <VOLUME LETTER OF BROKEN OS DISK>:\windows\system32\config\SYSTEM
   ```

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

   ```
   REG UNLOAD HKLM\BROKENSYSTEM
   ```
   
### <a name="rebuild-the-vm"></a>VM 다시 빌드

[VM 복구 명령의 5단계](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/repair-windows-vm-using-azure-virtual-machine-repair-commands#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
