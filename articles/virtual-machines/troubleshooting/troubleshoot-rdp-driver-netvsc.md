---
title: Windows 10 또는 Azure의 Windows Server 2016 VM에 원격으로 연결할 때 netvsc.sys 문제 해결 | Microsoft Docs
description: Windows 10 또는 Azure의 Windows Server 2016 VM에 연결할 때 netvsc.sys 관련 RDP 문제를 해결하는 방법을 알아봅니다.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: v-jesits
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/19/2018
ms.author: genli
ms.openlocfilehash: e6685a5e77d92bb9e05ab9578e48c99e80a64b74
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362257"
---
# <a name="cannot-connect-remotely-to-a-windows-10-or-windows-server-2016-vm-in-azure-because-of-netvscsys"></a>netvsc.sys로 인해 Windows 10 또는 Azure의 Windows Server 2016 VM에 원격으로 연결할 수 없습니다.

이 문서에서는 Hyper-V Server 2016 호스트에서 Windows 10 또는 Windows Server 2016 Datacenter VM(가상 머신)에 연결할 때 네트워크가 연결되지 않는 문제를 해결하는 방법을 설명합니다.

## <a name="symptoms"></a>증상

원격 데스크톱 프로토콜 (RDP)를 사용 하 여 Azure Windows 10 또는 Windows Server 2016 VM에 연결할 수 없습니다. [부트 진단](boot-diagnostics.md) 화면에는 NIC(네트워크 인터페이스 카드)를 위에 빨간색 십자가 표시됩니다. 이것은 운영 체제를 완전히 로드한 후에 VM이 연결되지 않았음을 의미합니다.

일반적으로 이 문제는 Windows [빌드 14393](https://support.microsoft.com/help/4093120/) 및 [빌드 15063](https://support.microsoft.com/help/4015583/)에서 나타납니다. 해당 빌드 이상의 운영 체제 버전을 사용 중인 경우, 이 문서의 내용이 해당 시나리오에 적용되지 않습니다. 시스템 버전을 확인하려면 [직렬 액세스 콘솔 기능](serial-console-windows.md)에서 CMD 세션을 열고 **Ver**을 실행합니다.

## <a name="cause"></a>원인

이 문제는 설치된 netvsc.sys 시스템 파일의 버전이 **10.0.14393.594** 또는 **10.0.15063.0**일 때 발생할 수 있습니다. 이러한 버전의 netvsc.sys는 시스템이 Azure 플랫폼과 상호 작용하지 못하게 할 수 있습니다.


## <a name="solution"></a>해결 방법

다음 단계를 따르기 전에 영향을 받는 VM의 [시스템 디스크 스냅샷을 백업으로 만듭니다](../windows/snapshot-copy-managed-disk.md). 이 문제를 해결하려면 직렬 콘솔을 사용하거나 VM의 시스템 디스크를 복구 VM에 연결하여 [오프라인으로 VM을 복구](#repair-the-vm-offline)합니다.


### <a name="use-the-serial-console"></a>직렬 콘솔 사용

[직렬 콘솔에 연결한 다음, PowerShell 인스턴스를 열고](serial-console-windows.md) 다음 단계를 따릅니다.

> [!NOTE]
> VM에서 직렬 콘솔을 사용하지 않도록 설정한 경우 [오프라인으로 VM 복구](#repair-the-vm-offline) 섹션으로 이동합니다.

1. PowerShell 인스턴스에서 다음 명령을 실행하여 파일(**c:\windows\system32\drivers\netvsc.sys**) 버전을 가져옵니다.

   ```
   (get-childitem "$env:systemroot\system32\drivers\netvsc.sys").VersionInfo.FileVersion
   ```

2. 동일한 지역에서 작동하는 VM에 연결된 새 데이터 디스크 또는 기존 데이터 디스크에 적절한 업데이트를 다운로드합니다.

   - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) 이상의 업데이트
   - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) 이상의 업데이트

3. 작동하는 VM에서 유틸리티 디스크를 분리하고 손상된 VM에 연결합니다.

4. 다음 명령을 실행하여 VM에 해당 업데이트를 설치합니다.

   ```
   dism /ONLINE /add-package /packagepath:<Utility Disk Letter>:\<KB .msu or .cab>
   ```

5. VM을 다시 시작합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).

2. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다.

3. 디스크 관리 콘솔에서 디스크의 플래그가 **온라인**으로 지정되었는지 확인합니다. 연결된 시스템 디스크에 할당된 드라이브 문자를 적어 둡니다.

4. 변경할 때 롤백해야 하는 경우 **\Windows\System32\config** 폴더의 복사본을 만듭니다.

5. 복구 VM에서 레지스트리 편집기(regedit.exe)를 시작합니다.

6. **HKEY_LOCAL_MACHINE** 키를 선택한 다음, 메뉴에서 **파일** > **하이브 로드**를 차례로 선택합니다.

7. **\Windows\System32\config** 폴더에서 SYSTEM 파일을 찾습니다.

8. **열기**를 선택하고, 이름으로 **BROKENSYSTEM**을 입력하고, **HKEY_LOCAL_MACHINE** 키를 확장한 후 이름이 **BROKENSYSTEM**인 추가 키를 찾습니다.

9. 다음 위치로 이동합니다.

   ```
   HKLM\BROKENSYSTEM\ControlSet001\Control\Class\{4d36e972-e325-11ce-bfc1-08002be10318}
   ```

10. 각 하위 키(예: 0000)에서 **Microsoft HYPER-V Network Adapter**로 표시되는 **DriverDesc** 값을 검토합니다.

11. 하위 키에서 VM 네트워크 어댑터의 드라이버 버전인 **DriverVersion** 값을 확인합니다.

12. 적절한 업데이트를 다운로드합니다.

    - **10.0.14393.594**: [KB4073562](https://support.microsoft.com/help/4073562) 이상의 업데이트
    - **10.0.15063.0**: [KB4016240](https://support.microsoft.com/help/4016240) 이상의 업데이트

13. 시스템 디스크를 업데이트를 다운로드할 수 있는 복구 VM에 데이터 디스크로 연결합니다.

14. 다음 명령을 실행하여 VM에 해당 업데이트를 설치합니다.

    ```
    dism /image:<OS Disk letter>:\ /add-package /packagepath:c:\temp\<KB .msu or .cab>
    ```

15. 다음 명령을 실행하여 하이브를 분리합니다.

    ```
    reg unload HKLM\BROKENSYSTEM
    ```

16. [시스템 디스크를 분리하고 VM을 다시 만듭니다](../windows/troubleshoot-recovery-disks-portal.md).

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

추가 도움이 필요한 경우 [Azure 지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.
