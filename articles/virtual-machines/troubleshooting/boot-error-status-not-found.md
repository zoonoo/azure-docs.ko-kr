---
title: Windows 부팅 관리자 오류 문제 해결 - 0xC0000225 "상태를 찾을 수 없음"
description: Azure VM에서 오류 코드 0xC0000225가 발생하는 문제를 해결하는 단계입니다.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: c17899a4-b270-4725-9530-0dcd829b178c
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: v-miegge
ms.openlocfilehash: 3677d67f55cfccdc80245b2ec870ffa76b0a1940
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87088668"
---
# <a name="troubleshoot-windows-boot-manager-error----0xc0000225-status-not-found"></a>Windows 부팅 관리자 오류 문제 해결 - 0xC0000225 "상태를 찾을 수 없음"
 
이 문서에서는 Azure VM에서 오류 코드 0xC0000225가 발생하는 문제를 해결하는 단계를 제공합니다. 이 오류는 상태 또는 개체를 찾을 수 없음을 나타냅니다.

## <a name="symptoms"></a>증상

[부팅 진단](./boot-diagnostics.md)을 사용하여 VM의 스크린샷을 보면 스크린샷에 상태 코드 *0xc0000225*와 함께 Widows를 시작할 수 없음 오류가 표시되는 것을 확인할 수 있습니다.

이 오류 코드와 연결된 파일은 문제를 해결하기 위해 수행할 단계를 알려 줍니다. **파일:** 섹션의 텍스트를 찾아 적절한 작업 과정을 결정합니다.

### <a name="drivers-os-related-or-third-party"></a>드라이버, OS 관련 또는 기타

파일이 있지만 드라이버(표시된 대로), OS 관련 또는 기타 내용을 나타내는 경우 [시스템 파일 복구](#repair-the-system-file) 섹션의 단계를 따르세요.
 
다음 이미지에서는 Windows 부팅 관리자는 "Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다." 또한 상태 "0xc0000225", **파일:** `\windows\System32\drivers\atapi.sys` 및 **정보:** "중요한 시스템 드라이버가 누락되거나 오류가 있기 때문에 운영 체제를 로드할 수 없습니다."를 표시합니다.

![Windows 부팅 관리자는 ‘Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다.’를 표시합니다. 또한 상태 “0xc0000225’, 파일 ‘\windows\System32\drivers\atapi.sys’를 표시하고 정보 섹션에 “중요한 시스템 드라이버가 누락되거나 오류가 있기 때문에 운영 체제를 로드할 수 없습니다.”를 표시합니다.](./media/troubleshoot-boot-error-status-not-found/1.png)

### <a name="no-file"></a>파일 없음

상태 코드와 함께 표시되지만 파일이 표시되지 않는 경우 [OSDEVICE 변수 추가](#add-the-osdevice-variable) 섹션의 단계를 따릅니다.

다음 이미지에서는 Windows 부팅 관리자는 "Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다." 또한 상태 "0xc0000225", **정보:** "필요한 장치에 액세스할 수 없기 때문에 부팅 선택 영역에서 오류가 발생했습니다."를 표시합니다.

![Windows 부팅 관리자는 ‘Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다.’를 표시합니다. 또한 상태 "0xc0000225"를 표시하고 정보 섹션에 "필요한 장치에 액세스할 수 없기 때문에 부팅 선택 영역에서 오류가 발생했습니다."를 표시합니다.](./media/troubleshoot-boot-error-status-not-found/2.png)

### <a name="registry-file"></a>레지스트리 파일

\windows\system32\config\system과 같은 레지스트리 파일을 참조하는 경우 [지원 티켓 만들기](#contact-support) 섹션의 단계를 따릅니다.
 
다음 이미지에서는 Windows 부팅 관리자는 "Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다." 또한 상태 "0xc0000225", 파일 `\windows\System32\config\system` 및 **정보:** "시스템 레지스트리 파일이 없거나 오류가 있으므로 운영 체제를 로드할 수 없습니다."를 표시합니다.

![Windows 부팅 관리자는 ‘Windows를 시작하지 못했습니다. 최근의 하드웨어 또는 소프트웨어 변경 때문일 수 있습니다.’를 표시합니다. 또한 상태 “0xc0000225’, 파일 ‘\windows\System32\config\system’을 표시하고 정보 섹션에 “시스템 레지스트리 파일이 없거나 오류가 있으므로 운영 체제를 로드할 수 없습니다.”를 표시합니다.](./media/troubleshoot-boot-error-status-not-found/3.png)

다음 이미지에서 복구 화면은 "PC/장치를 복구해야 합니다. 시스템 레지스트리 파일이 없거나 오류가 있으므로 운영 체제를 로드할 수 없습니다."를 표시합니다. 또한 오류 코드 "0xc0000225" 및 파일 `\windows\System32\config\system`을 표시합니다.

![복구 화면은 'PC/장치를 복구해야 합니다. 중요한 시스템 드라이버가 누락되거나 오류가 있기 때문에 운영 체제를 로드할 수 없습니다.'를 표시합니다. 또한 오류 코드 "0xc0000225" 및 파일'\windows\System32\config\system'을 표시합니다.](./media/troubleshoot-boot-error-status-not-found/4.png)

## <a name="causes"></a>원인

### <a name="missing-binary"></a>누락된 이진

시스템 **(.sys)** 파일에서 누락되었거나 손상된 이진이 발생할 수 있습니다.

### <a name="bcd-corruption-or-improper-vhd-migration"></a>BCD 손상 또는 잘못된 VHD 마이그레이션

이 경우 **BCD(부팅 구성 데이터)** 가 손상되었거나 **VHD(가상 하드 드라이브)** 가 온-프레미스에서 마이그레이션되었지만 제대로 준비되지 않은 것입니다. 따라서 **OSDEVICE** 변수가 누락되어 추가해야 합니다.

### <a name="registry-hive-corruption"></a>레지스트리 하이브 손상

레지스트리 하이브 손상은 다음과 같은 이유로 인해 발생할 수 있습니다.

- Hive에 오류가 발생했습니다.
- Hive가 탑재되었으나 비어 있습니다.
- Hive가 제대로 닫히지 않았습니다.
## <a name="solution"></a>해결 방법

### <a name="process-overview"></a>프로세스 개요

1. 복구 VM을 만들고 액세스합니다.
1. 다음과 같은 솔루션을 선택합니다.
   - [시스템 파일 복구](#repair-the-system-file)
   - [OSDevice 변수 추가](#add-the-osdevice-variable)
   - [지원 티켓 만들기](#contact-support)
1. 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.
1. VM을 다시 빌드합니다.

### <a name="create-and-access-a-repair-vm"></a>복구 VM 만들기 및 액세스

1. [VM 복구 명령](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md)의 1~3단계를 사용하여 복구 VM을 준비합니다.
1. 원격 데스크톱 연결을 사용하여 복구 VM에 연결합니다.

### <a name="select-a-solution"></a>솔루션 선택

1. 관리자 권한 명령 프롬프트를 엽니다.
1. 이전에 식별한 증상에 따라 해당 솔루션의 단계를 수행합니다. 다른 솔루션의 단계는 이슈에 적용되지 않기 때문에 건너뛰어도 됩니다.

- [시스템 파일 복구](#repair-the-system-file)
- [OSDevice 변수 추가](#add-the-osdevice-variable)
- [지원 티켓 만들기](#contact-support)

### <a name="repair-the-system-file"></a>시스템 파일 복구

1. 연결된 VHD를 사용하여 VM(가상 머신) 스크린샷에 표시된 이진 파일의 위치로 이동합니다.
1. 파일을 마우스 오른쪽 단추로 클릭하고 **속성**을 선택한 다음, **세부 정보** 탭을 선택하여 파일에 대한 정보를 확인합니다.
   1. 아래 이미지에 표시된 것처럼 파일의 버전을 확인합니다.

      ![파일 버전이 강조 표시된 'cng .sys' 파일의 속성 창](./media/troubleshoot-boot-error-status-not-found/5.png)

1. 파일 이름을 **< BINARY.SYS >.old**로 바꾸고 **< BINARY.SYS >** 를 파일의 이름으로 바꿉니다.

   위 단계의 이미지에서 **cng.sys** 파일의 이름은 **cng.sys.old**로 바꾸어야 합니다.

   > [!NOTE]
   > 파일의 이름을 바꾼 후에 "파일이 손상되어 읽을 수 없습니다."라는 메시지가 표시되면 이 솔루션이 작동하지 않는 것이므로 [지원 담당자에게 문의합니다](https://azure.microsoft.com/support/create-ticket/).

1. 손상된 파일의 이름을 변경했으므로 해당 파일을 내부 리포지토리에서 복원하여 수정합니다.
   1. **CMD** 세션을 시작합니다.
   1. **\windows\winsxs**로 이동합니다.

   1. 다음 명령을 사용하여 이 섹션의 시작 부분에 있는 이진을 검색합니다.

      `dir <BINARY WITH ".SYS" EXTENSION>  /s`

      이 명령은 머신에 있는 모든 버전의 파일을 나열하여 해당 구성 요소의 경로 기록을 제공합니다.
      
      예를 들어 **dir cng.sys**의 이름은 **dir cng.sys /s**로 바뀐 것입니다.

   1. 목록에서 최신 버전의 파일(또는 원하는 버전)을 선택하고 이전 경로 및 다음 명령을 사용하여 **windows\system32** 폴더에 파일을 복사합니다.

      `copy <drive>:\Windows\WinSxS\<DIRECTORY WHERE FILE IS>\<BINARY WITH ".SYS" EXTENSION> <DRIVE>:\Windows\System32\Drivers\`

      > [!NOTE]
      > 최신 이진이 작동하지 않는 경우 해당 버전 이전의 버전을 사용해 보거나, 패치 전 버전과 같은 안정적인 파일을 사용합니다.

      예를 들어, 찾고 있는 이진이 **cmimcext.sys**이고, 결함이 있는 드라이브가 드라이브 **F:** 이고, 최신 버전을 검색한 경우 다음 이미지가 표시됩니다. 여기서 `dir cmim* /s`의 명령 프롬프트에 표시되는 쿼리는 cmimcext.sys 파일의 최신 버전을 찾습니다.

      ![cmimcext.sys 파일의 최신 버전을 찾기 위한 "dir cmim */s" 명령 프롬프트의 쿼리](./media/troubleshoot-boot-error-status-not-found/6.png)

      위의 예제 이미지에서 쿼리는 **C:** 에 대해 수행되지만 드라이브 문자는 복구 VM에 데이터 디스크로서 연결된 OS 디스크인 결함 있는 **F:** 드라이브의 드라이브 문자여야 합니다.

      파일을 복사하는 결과 명령은 `copy F:\Windows\WinSxS\amd64_xxxxxx\cmimcext.sys F:\Windows\System32\Drivers`입니다.

이 작업이 완료되면 계속해서 [직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정](#enable-the-serial-console-and-memory-dump-collection)합니다.

### <a name="add-the-osdevice-variable"></a>OSDEVICE 변수 추가

현재 부팅 설정 정보를 수집하고 활성 파티션에서 해당 식별자를 확인합니다. 그런 다음 이 정보를 사용하여 사용 중인 VM 세대에 대한 지침에 따라 **OSDEVICE** 변수를 추가합니다.

이 정보 수집 시 **\boot\bcd** 파일이 없다는 오류가 발생하면 대신 [시스템 파일 복구](#repair-the-system-file)의 지침을 사용합니다. 

1. 1세대 VM의 경우 관리자 권한으로 명령 프롬프트를 열고 다음 명령을 입력합니다.

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /enum`

   이 이미지는 식별자 특성이 강조 표시된 1세대 VM의 Windows 부팅 로더를 표시합니다. 강조 표시된 식별자 특성은 고유한 영숫자 문자열을 보여 줍니다.

   ![식별자 특성이 강조 표시된 1세대 VM에 표시된 Windows 부팅 로더 강조 표시된 식별자 특성은 고유한 영숫자 문자열을 보여 줍니다.](./media/troubleshoot-boot-error-status-not-found/7.png)

   경로 값이 **\windows\system32\winload.exe**인 Windows 부팅 로더의 식별자를 확인합니다.

1. 2세대 VM의 경우 OS 디스크가 온라인 상태이고 해당 파티션 드라이브 문자가 할당되었는지 확인합니다. 이것이 확인되면 부팅 설정 정보를 수집합니다.
   1. **Windows 검색**에서 **디스크 관리**를 입력하고 디스크 관리 콘솔을 엽니다. 이 콘솔을 사용하여 복구 VM에 연결된 디스크 번호와 BCD 저장소를 포함하는 EFI(Extensible Firmware Interface) 파티션을 식별할 수 있습니다.

   다음 이미지에서 디스크 2는 복구 VM에 연결된 디스크 번호입니다. 이 이미지에는 디스크 2의 EFI 시스템 파티션(크기가 100MB이며 할당된 문자가 없음)도 표시됩니다.

   ![디스크 2는 복구 VM에 연결된 디스크 번호로 표시됩니다. 디스크 2의 EFI 시스템 파티션(크기가 100MB이며 할당된 문자가 없음)도 표시됩니다.](./media/troubleshoot-boot-error-status-not-found/8.png)

   1. 관리자 권한으로 명령 프롬프트를 열고 다음 명령을 입력합니다.
      1. 명령 `diskpart`를 사용하여 **DISKPART TOOL**을 엽니다.
      1. 모든 디스크를 나열하고 이전 단계에서 확인된 연결된 디스크를 선택합니다.
      
         ```
         list disk
         sel disk <DISK #>
         ```

         다음 이미지는 디스크를 나열하고 선택했을 때의 결과를 보여 줍니다. 디스크 0 (127 g b/온라인), 디스크 1 (32 g b/온라인) 및 디스크 2 (127 g b/온라인)가 표시 되 고 명령을 사용 하 여 디스크 2가 선택 됩니다 `sel disk 2` .

         ![디스크를 나열하고 선택했을 때의 결과 디스크 0(127GB | Online), 디스크 1(32GB | Online) 및 디스크 2(127GB | Online)가 나열되고 디스크 2가 선택되었다고 표시됩니다.](./media/troubleshoot-boot-error-status-not-found/9.png)

      1. 파티션을 나열하고 이전 단계에서 식별한 EFI 시스템 파티션을 선택합니다.
      
         ```
         list partition
         sel partition <PARTITION #>
         ```

         다음 이미지는 파티션을 나열하고 선택했을 때의 결과를 보여 줍니다. 파티션 1 (예약/16MB), 파티션 2 (시스템/m s) 및 파티션 3 (주/126 g b)이 나열 되 고,이 명령을 사용 하 여 파티션 2가 선택 됩니다 `sel part 2` .

         ![파티션을 나열하고 선택했을 때의 결과 파티션 1(Reserved | 16MB), 파티션 2(System | 100MB) 및 파티션 3(Primary | 126GB)이 나열되고 파티션 2가 선택되었다고 표시됩니다.](./media/troubleshoot-boot-error-status-not-found/10.png)

      1. `assign` 명령을 사용하여 EFI 파티션에 문자를 할당합니다.

         다음 이미지에서는 파일 탐색기에 `assign` 명령과 새 드라이브 **SYSTEM (F:)** 를 모두 볼 수 있습니다.

         ![파일 탐색기에 assign 명령과 새 드라이브 SYSTEM (F:)를 모두 볼 수 있습니다.](./media/troubleshoot-boot-error-status-not-found/11.png)

      1. 다음 명령을 사용하여 BCD 저장소 데이터를 나열합니다.
      
         `bcdedit /store <LETTER OF EFI SYSTEM PARTITION>:EFI\Microsoft\boot\bcd /enum`

         다음 이미지는 식별자 특성이 강조 표시된 2세대 VM의 Windows 부팅 로더를 표시합니다. 강조 표시된 식별자 특성의 값은 **{default}** 입니다.

         ![식별자 특성이 강조 표시된 2세대 VM에 표시된 Windows 부팅 로더 강조 표시된 식별자 특성은 해당 값으로 default를 표시합니다.](./media/troubleshoot-boot-error-status-not-found/12.png)

         경로 값이 **\windows\system32\winload.efi**인 Windows 부팅 로더의 식별자를 확인합니다.

1. 활성 파티션에 있는 OSDEVICE 변수가 누락되었는지 확인합니다.

   ![Windows 부팅 관리자 및 Windows 부팅 로더 특성은 명령 프롬프트에 나열되며 OS 디바이스 특성은 없습니다.](./media/troubleshoot-boot-error-status-not-found/13.png)
   
   이 이미지에서 Windows 부팅 관리자 및 Windows 부팅 로더 특성은 명령 프롬프트에 나열되지만 OSDEVICE 디바이스 특성은 없습니다.

1. 다음 정보에 따라 OSDEVICE 변수를 추가합니다.

   단일 파티션 OS 디스크의 경우 `BOOT`를 추가합니다.

   > [!NOTE]
   > 부팅 가능 폴더는 Windows 폴더 **\windows 폴더**와 동일한 파티션에 있게 됩니다.
   > - 1세대 VM에 대한 부팅 가능 폴더는 **(\boot\cc\c\d 폴더)** 입니다.
   > - 2세대 VM에 대한 부팅 가능 폴더 **EFI\Microsoft\boot\bcd**입니다.

   1세대 VM의 경우 다음 명령을 입력합니다.

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   2세대 VM의 경우 다음 명령을 입력합니다.

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {<IDENTIFIER>} OSDEVICE BOOT`

   다중 파티션 OS 디스크의 경우 `PARTITION=<LETTER OF WINDOWS FOLDER>:`를 추가합니다.

   > [!NOTE]
   > 부팅 가능 폴더는 Windows 폴더 **\windows 폴더**와 다른 파티션에 있을 수 있습니다.
   > - 1세대 VM에 대한 부팅 가능 폴더는 **(\boot\cc\c\d 폴더)** 입니다.
   > - 2세대 VM에 대한 부팅 가능 폴더 **EFI\Microsoft\boot\bcd**입니다.

   1세대 VM의 경우 다음 명령을 입력합니다.

   `bcdedit /store <LETTER OF BOOT FOLDER>:\boot\bcd /set {<IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

   2세대 VM의 경우 다음 명령을 입력합니다.

   `bcdedit /store <LETTER OF EFI FOLDER>:EFI\Microsoft\boot\bcd /set {< IDENTIFIER>} OSDEVICE partition=<LETTER OF WINDOWS FOLDER>:`

이 작업이 완료되면 계속해서 [직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정](#enable-the-serial-console-and-memory-dump-collection)합니다.

### <a name="contact-support"></a>지원에 문의

**레지스트리 파일** 오류에는 해결 방법이 있지만 추가 지원이 필요한 경우 [지원 티켓을 만들어야](https://azure.microsoft.com/support/create-ticket/) 합니다.

### <a name="enable-the-serial-console-and-memory-dump-collection"></a>직렬 콘솔과 메모리 덤프 수집 사용

**권장**: VM을 다시 빌드하기 전에 다음 스크립트를 실행하여 직렬 콘솔과 메모리 덤프 수집을 사용하도록 설정합니다.

1. 관리자 권한 명령 프롬프트 세션을 엽니다.
1. 다음 명령을 실행합니다.

   **직렬 콘솔을 사용하도록 설정합니다**.
   
   ```
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON 
   bcdedit /store <VOLUME LETTER WHERE THE BCD FOLDER IS>:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. OS 디스크의 사용 가능한 공간이 VM의 메모리 크기(RAM)보다 큰지 확인합니다.

   OS 디스크에 공간이 부족한 경우 메모리 덤프 파일이 생성될 위치를 변경하고 사용 가능한 공간이 충분한 VM에 연결된 데이터 디스크의 위치를 참조합니다. 이 위치를 변경하려면 다음 명령에서 **%SystemRoot%** 를 데이터 디스크의 드라이브 문자(예: **F:** )로 바꿉니다.

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

[VM 복구 명령의 5단계](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md#repair-process-example)를 사용하여 VM을 다시 빌드합니다.
