---
title: DHCP가 해제되어 Azure Virtual Machines에 원격으로 연결할 수 없음 | Microsoft Docs
description: Microsoft Azure에서 DHCP 클라이언트 서비스가 해제되어 발생하는 RDP 문제 해결 방법 알아보기 | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: cshepard
editor: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/13/2018
ms.author: genli
ms.openlocfilehash: daddb859c6bfc6309ef833c6c6c3ea43c70f1889
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60362291"
---
#  <a name="cannot-rdp-to-azure-virtual-machines-because-the-dhcp-client-service-is-disabled"></a>DHCP 클라이언트 서비스가 해제되어 Azure Virtual Machines에 RDP로 연결할 수 없음

이 문서에서는 VM에서 DHCP 클라이언트 서비스가 해제된 후 Azure Windows VM(Virtual Machines)에 원격 데스크톱으로 연결할 수 없는 문제에 대해 설명합니다.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="symptoms"></a>증상
VM에서 DHCP 클라이언트 서비스가 해제되어 Azure의 VM에 RDP로 연결할 수 없습니다. Azure Portal의 [부팅 진단](../troubleshooting/boot-diagnostics.md)에서 스크린샷을 확인하면 VM이 정상적으로 부팅되고 로그인 화면에서 자격 증명을 기다리는 것으로 표시됩니다. 이벤트 뷰어를 사용하여 이벤트 로그를 VM에서 원격으로 봅니다. DHCP 클라이언트 서비스가 시작되지 않거나 시작에 실패하는 것이 보입니다. 다음은 샘플 로그입니다.

**로그 이름**: 시스템 </br>
**원본**: 서비스 제어 관리자 </br>
**날짜**: 2015/12/16 AM 11:19:36 </br>
**이벤트 ID**: 7022 </br>
**작업 범주**: 없음 </br>
**수준**: 오류 </br>
**키워드**: 클래식</br>
**사용자**: N/A </br>
**컴퓨터**: myvm.cosotos.com</br>
**설명**: DHCP 클라이언트 서비스가 시작 시 멈춥니다.</br>

Resource Manager VM의 경우 직렬 액세스 콘솔 기능을 사용하여 다음 명령으로 이벤트 로그 7022를 쿼리할 수 있습니다.

    wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more

클래식 VM의 경우 오프라인 모드에서 작업하여 로그를 수동으로 수집해야 합니다.

## <a name="cause"></a>원인

VM에서 DHCP 클라이언트 서비스가 실행되고 있지 않습니다.

> [!NOTE]
> 이 문서는 DHCP 클라이언트 서비스에만 적용되고 DHCP 서버에는 적용되지 않습니다.

## <a name="solution"></a>해결 방법

다음 단계를 수행하기 전에 영향을 받는 VM의 OS 디스크 스냅샷을 백업으로 만듭니다. 자세한 내용은 [디스크 스냅샷](../windows/snapshot-copy-managed-disk.md)을 참조하세요.

이 문제를 해결하려면 직렬 컨트롤을 사용하여 DHCP를 사용하도록 설정하거나 VM에 대한 [네트워크 인터페이스를 다시 설정](reset-network-interface.md)하세요.

### <a name="use-serial-control"></a>직렬 콘솔 사용

1. [직렬 콘솔에 연결하고 CMD 인스턴스를 엽니다](serial-console-windows.md#use-cmd-or-powershell-in-serial-console).
)을 참조하세요. VM에서 직렬 콘솔을 사용할 수 없는 경우 [네트워크 인터페이스 다시 설정](reset-network-interface.md)을 참조하세요.
2. 네트워크 인터페이스에서 DHCP를 사용할 수 없는지 확인합니다.

        sc query DHCP
3. DHCP가 중지되면 서비스를 시작해 봅니다.

        sc start DHCP

4. 서비스를 다시 쿼리하여 서비스가 성공적으로 시작되었는지 확인합니다.

        sc query DHCP

    VM에 연결해 보고 문제가 해결되었는지 확인합니다.
5. 서비스가 시작되지 않으면 받은 오류 메시지에 따라 다음 중 적절한 솔루션을 사용합니다.

    | 오류  |  해결 방법 |
    |---|---|
    | 5- ACCESS DENIED  | [액세스 거부 오류로 인해 DHCP 클라이언트 서비스가 중지됨](#dhcp-client-service-is-stopped-because-of-an-access-denied-error)을 참조하세요.  |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT   | [DHCP 클라이언트 서비스가 충돌 또는 중지됨](#dhcp-client-service-crashes-or-hangs)을 참조하세요.  |
    | 1058 - ERROR_SERVICE_DISABLED  | [DHCP 클라이언트 서비스가 해제됨](#dhcp-client-service-is-disabled)을 참조하세요.  |
    | 1059 - ERROR_CIRCULAR_DEPENDENCY  |[지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.   |
    | 1067 - ERROR_PROCESS_ABORTED |[DHCP 클라이언트 서비스가 충돌 또는 중지됨](#dhcp-client-service-crashes-or-hangs)을 참조하세요.   |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL   | [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.  |
    |1069 - ERROR_SERVICE_LOGON_FAILED   |  [로그온 오류로 인해 DHCP 클라이언트 서비스가 실패함](#dhcp-client-service-fails-because-of-logon-failure)을 참조하세요. |
    | 1070 - ERROR_SERVICE_START_HANG  | [DHCP 클라이언트 서비스가 충돌 또는 중지됨](#dhcp-client-service-crashes-or-hangs)을 참조하세요.  |
    | 1077 - ERROR_SERVICE_NEVER_STARTED  | [DHCP 클라이언트 서비스가 해제됨](#dhcp-client-service-is-disabled)을 참조하세요.  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   | [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.  |
    |1053 | [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.  |


#### <a name="dhcp-client-service-is-stopped-because-of-an-access-denied-error"></a>액세스 거부 오류로 인해 DHCP 클라이언트 서비스가 중지됨

1. [직렬 콘솔](serial-console-windows.md)에 연결하고 PowerShell 인스턴스를 엽니다.
2. 다음 스크립트를 실행하여 프로세스 모니터 도구를 다운로드합니다.

   ```powershell
   remove-module psreadline
   $source = "https://download.sysinternals.com/files/ProcessMonitor.zip"
   $destination = "c:\temp\ProcessMonitor.zip"
   $wc = New-Object System.Net.WebClient
   $wc.DownloadFile($source,$destination)
   ```
3. 이제 **procmon** 추적을 시작합니다.

   ```
   procmon /Quiet /Minimized /BackingFile c:\temp\ProcMonTrace.PML
   ```
4. **액세스 거부됨** 메시지가 생성되는 서비스를 시작하여 문제를 재현합니다.

   ```
   sc start DHCP
   ```

   실패하면 프로세스 모니터 추적을 종료합니다.

   ```
   procmon /Terminate
   ```
5. **c:\temp\ProcMonTrace.PML** 파일을 수집합니다.

    1. [VM에 데이터 디스크를 연결합니다](../windows/attach-managed-disk-portal.md
).
    2. 새 드라이브에 파일을 복사할 수 있는 직렬 콘솔을 사용합니다. 예: `copy C:\temp\ProcMonTrace.PML F:\`. 이 명령에서 F는 연결된 데이터 디스크의 드라이브 문자입니다. 문자를 올바른 값으로 적절하게 바꿉니다.
    3. 데이터 드라이브를 분리하고 프로세스 모니터 ubstakke가 설치되어 있는 작동 중인 VM에 연결합니다.

6. 작동 중인 VM에서 프로세스 모니터를 사용하여 **ProcMonTrace.PML**을 엽니다. 그런 다음, 다음 스크린샷에 나온 것처럼  **결과가 액세스 거부됨**으로 필터링합니다.

    ![프로세스 모니터의 결과로 필터링](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

7. 출력에 있는 레지스트리 키, 폴더 또는 파일을 수정합니다. 일반적으로 이 문제는 서비스에 사용된 로그인 계정에 이러한 개체에 액세스할 수 있는 ACL 권한이 없기 때문에 발생합니다. 로그인 계정에 대한 올바른 ACL 권한은 정상 VM에서 확인할 수 있습니다.

#### <a name="dhcp-client-service-is-disabled"></a>DHCP 클라이언트 서비스가 해제됨

1. 기본 시작 값으로 서비스를 복원합니다.

   ```
   sc config DHCP start= auto
   ```

2. 서비스를 시작합니다.

   ```
   sc start DHCP
   ```

3. 서비스 상태를 다시 쿼리하여 서비스가 실행 중인지 확인합니다.

   ```
   sc query DHCP
   ```

4. 원격 데스크톱을 사용하여 VM에 연결을 시도합니다.

#### <a name="dhcp-client-service-fails-because-of-logon-failure"></a>로그온 오류로 인해 DHCP 클라이언트 서비스가 실패함

1. 이 문제는 이 서비스의 시작 계정이 변경된 경우에 발생하므로 계정을 기본 상태로 되돌려야 합니다.

        sc config DHCP obj= 'NT Authority\Localservice'
2. 서비스를 시작합니다.

        sc start DHCP
3. 원격 데스크톱을 사용하여 VM에 연결을 시도합니다.

#### <a name="dhcp-client-service-crashes-or-hangs"></a>DHCP 클라이언트 서비스가 충돌 또는 중지됨

1. 서비스 상태가 **시작하는 중** 또는 **중지하는 중**에서 변하지 않는 경우 서비스를 중지합니다.

        sc stop DHCP
2. 자체 ‘svchost’ 컨테이너에서 서비스를 격리합니다.

        sc config DHCP type= own
3. 서비스를 시작합니다.

        sc start DHCP
4. 그래도 서비스가 시작되지 않으면 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).
2. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다. 디스크 관리 콘솔에서 연결된 디스크의 플래그가 **온라인**으로 지정되었는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.
3.  관리자 권한 명령 프롬프트 인스턴스를 엽니다(**관리자 권한으로 실행**). 그런 다음, 다음 스크립트를 실행합니다. 이 스크립트는 연결된 OS 디스크에 할당된 드라이브 문자를 **F**라고 가정합니다. 문자를 해당 VM의 올바른 값으로 적절하게 바꿉니다.

    ```
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM

    REM Set default values back on the broken service
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet001\services\DHCP" /v type /t REG_DWORD /d 16 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v start /t REG_DWORD /d 2 /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v ObjectName /t REG_SZ /d "NT Authority\LocalService" /f
    reg add "HKLM\BROKENSYSTEM\ControlSet002\services\DHCP" /v type /t REG_DWORD /d 16 /f

    reg unload HKLM\BROKENSYSTEM
    ```

4. [OS 디스크를 분리하고 VM을 다시 만듭니다](../windows/troubleshoot-recovery-disks-portal.md). 그런 다음, 문제가 해결되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

여전히 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 해결하세요.