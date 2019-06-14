---
title: Azure VM에서 원격 데스크톱 서비스가 시작되지 않음 | Microsoft Docs
description: 가상 머신에 연결할 때 원격 데스크톱 서비스 관련 문제를 해결하는 방법 알아보기 | Microsoft Docs
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
ms.date: 10/23/2018
ms.author: genli
ms.openlocfilehash: 5458a02c09a3600875c7300b27c5a87a735b2f1b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "60318903"
---
# <a name="remote-desktop-services-isnt-starting-on-an-azure-vm"></a>Azure VM에서 원격 데스크톱 서비스가 시작되지 않음

이 문서에서는 Azure VM(가상 머신)에 연결할 때 및 원격 데스크톱 서비스 또는 TermService가 시작되지 않거나 시작에 실패하는 경우 문제를 해결하는 방법에 대해 설명합니다.

> [!NOTE]  
> Azure에는 리소스를 만들고 사용하기 위한 [Azure Resource Manager 및 클래식](../../azure-resource-manager/resource-manager-deployment-model.md)이라는 두 가지 배포 모델이 있다는 것을 이해해야 합니다. 이 문서에서는 Resource Manager 배포 모델 사용에 대해 설명합니다. 이 모델은 클래식 배포 모델 대신 새 배포에 사용하는 것이 좋습니다.

## <a name="symptoms"></a>증상

VM에 연결하려고 시도할 때 다음과 같은 상황이 발생합니다.

- 이 VM 스크린샷은 운영 체제가 완전히 로드되어 자격 증명을 기다리고 있다는 것을 보여줍니다.

    ![VM 상태의 스크린샷](./media/troubleshoot-remote-desktop-services-issues/login-page.png)

- 이벤트 뷰어를 사용하여 이벤트 로그를 VM에서 원격으로 봅니다. 원격 데스크톱 서비스, TermService가 시작되지 않거나 시작에 실패한 것으로 표시됩니다. 다음은 샘플 로그입니다.

    **로그 이름**:      시스템 </br>
    **원본**:        서비스 제어 관리자 </br>
    **날짜**:          2017/12/16 오전 11:19:36</br>
    **이벤트 ID**:      7022</br>
    **작업 범주**: 없음</br>
    **수준**:         오류</br>
    **키워드**:      클래식</br>
    **사용자**:          N/A</br>
    **컴퓨터**: vm.contoso.com</br>
    **설명**: 원격 데스크톱 서비스가 시작 시 멈춤. 

    또한 다음 쿼리를 실행하면 이러한 오류에 대해 살펴보는 데 직렬 액세스 콘솔 기능을 사용할 수 있습니다. 

        wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Service Control Manager'] and EventID=7022 and TimeCreated[timediff(@SystemTime) <= 86400000]]]" | more 

## <a name="cause"></a>원인
 
이 문제는 원격 데스크톱 서비스가 VM에서 실행되지 않기 때문에 발생합니다. 원인은 다음 시나리오에 따라 달라질 수 있습니다. 

- TermService 서비스가 **사용 안 함**으로 설정됩니다. 
- TermService 서비스 충돌 하거나 응답 하지 않습니다. 
- 잘못 된 구성에는 TermService이 때문에 시작 되지 않음.

## <a name="solution"></a>해결 방법

이 문제를 해결하려면 직렬 콘솔을 사용합니다. 또는 VM의 OS 디스크를 복구 VM에 연결하여 [오프라인으로 VM을 복구](#repair-the-vm-offline)합니다.

### <a name="use-serial-console"></a>직렬 콘솔 사용

1. **지원 및 문제 해결** > **직렬 콘솔**을 선택하여 [직렬 콘솔](serial-console-windows.md)에 액세스합니다. VM에서 기능을 사용하도록 설정하면 VM을 성공적으로 연결할 수 있습니다.

2. CMD 인스턴스에 대한 새 채널을 만듭니다. **CMD**를 입력하여 채널을 시작하고 채널 이름을 가져옵니다.

3. CMD 인스턴스를 실행하는 채널로 전환합니다. 이 경우 채널 1이어야 합니다.

   ```
   ch -si 1
   ```

4. **Enter** 키를 다시 선택하고 VM에 대한 유효한 사용자 이름 및 암호(로컬 또는 도메인 ID)를 입력합니다.

5. TermService 서비스의 상태를 쿼리합니다.

   ```
   sc query TermService
   ```

6. 서비스 상태가 **중지됨**으로 표시되면 서비스를 시작해 봅니다.

    ```
    sc start TermService
     ``` 

7. 서비스를 다시 쿼리하여 서비스가 성공적으로 시작되는지 확인합니다.

   ```
   sc query TermService
   ```
8. 서비스가 시작에 실패하는 경우 수신한 오류에 따라 솔루션을 수행합니다.

    |  오류 |  제안 |
    |---|---|
    |5- ACCESS DENIED |[액세스 거부 오류로 인해 TermService 서비스가 중지됨](#termservice-service-is-stopped-because-of-an-access-denied-problem)을 참조하세요. |
    |1053 - ERROR_SERVICE_REQUEST_TIMEOUT  |[TermService 서비스 사용 안 함](#termservice-service-is-disabled)을 참조하세요.  |  
    |1058 - ERROR_SERVICE_DISABLED  |[TermService 서비스가 충돌 또는 중단](#termservice-service-crashes-or-hangs)을 참조하세요.  |
    |1059 - ERROR_CIRCULAR_DEPENDENCY |[지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.|
    |1067 - ERROR_PROCESS_ABORTED  |[TermService 서비스가 충돌 또는 중단](#termservice-service-crashes-or-hangs)을 참조하세요.  |
    |1068 - ERROR_SERVICE_DEPENDENCY_FAIL|[지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.|
    |1069 - ERROR_SERVICE_LOGON_FAILED  |[TermService 서비스가 로그온 실패로 인해 실패](#termservice-service-fails-because-of-logon-failure)를 참조하세요. |
    |1070 - ERROR_SERVICE_START_HANG   | [TermService 서비스가 충돌 또는 중단](#termservice-service-crashes-or-hangs)을 참조하세요. |
    |1077 - ERROR_SERVICE_NEVER_STARTED   | [TermService 서비스 사용 안 함](#termservice-service-is-disabled)을 참조하세요.  |
    |1079 - ERROR_DIFERENCE_SERVICE_ACCOUNT   |[지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요. |
    |1753   |[지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 신속하게 해결하세요.   |
    
#### <a name="termservice-service-is-stopped-because-of-an-access-denied-problem"></a>액세스 거부 문제로 인해 TermService 서비스가 중지됨

1. [직렬 콘솔](serial-console-windows.md)에 연결하고 PowerShell 인스턴스를 엽니다.
2. 다음 스크립트를 실행하여 프로세스 모니터 도구를 다운로드합니다.

   ```
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

4. **액세스 거부됨**이 표시되는 서비스를 시작하여 문제를 재현합니다. 

   ```
   sc start TermService 
   ```

   실패하면 프로세스 모니터 추적을 종료합니다.

   ```   
   procmon /Terminate 
   ```

5. \ **c:\temp\ProcMonTrace.PML** 파일을 수집합니다.

    1. [VM에 데이터 디스크를 연결합니다](../windows/attach-managed-disk-portal.md
).
    2. 새 드라이브에 파일을 복사할 수 있는 직렬 콘솔을 사용합니다. 예: `copy C:\temp\ProcMonTrace.PML F:\`. 이 명령에서 F는 연결된 데이터 디스크의 드라이브 문자입니다.
    3. 데이터 드라이브를 분리하고 프로세스 모니터 ubstakke가 설치되어 있는 작동 중인 VM에서 연결합니다.

6. 작동 중인 VM에서 프로세스 모니터를 사용하여 **ProcMonTrace.PML**을 엽니다. 그런 다음, 다음 스크린샷에 나온 것처럼  **결과가 액세스 거부됨**으로 필터링합니다.

    ![프로세스 모니터의 결과로 필터링](./media/troubleshoot-remote-desktop-services-issues/process-monitor-access-denined.png)

 
6. 출력에 있는 레지스트리 키, 폴더 또는 파일을 수정합니다. 일반적으로 이 문제는 서비스에 사용된 로그인 계정에 이러한 개체에 액세스할 수 있는 ACL 권한이 없기 때문에 발생합니다. 로그인 계정에 대한 올바른 ACL 권한을 알아보려면 정상 VM에서 확인할 수 있습니다. 

#### <a name="termservice-service-is-disabled"></a>TermService 서비스 사용 안 함

1. 기본 시작 값으로 서비스를 복원합니다.

   ```
   sc config TermService start= demand 
   ```

2. 서비스를 시작합니다.

   ```
   sc start TermService
   ```

3. 다시 해당 상태를 쿼리하여 서비스가 실행되고 있는지 확인합니다.

   ```
   sc query TermService 
   ```

4. 원격 데스크톱을 사용하여 VM에 연결을 시도합니다.

#### <a name="termservice-service-fails-because-of-logon-failure"></a>TermService 서비스가 로그온 실패로 인해 실패

1. 이 문제는 이 서비스의 시작 계정이 변경된 경우 발생합니다. 이를 다시 기본값으로 변경합니다. 

        sc config TermService obj= 'NT Authority\NetworkService'
2. 서비스를 시작합니다.

        sc start TermService
3. 원격 데스크톱을 사용하여 VM에 연결을 시도합니다.

#### <a name="termservice-service-crashes-or-hangs"></a>TermService 서비스가 충돌 또는 중단
1. 서비스 상태가 **시작하는 중** 또는 **중지하는 중**인 경우 서비스 중지를 시도합니다. 

        sc stop TermService
2. 자체 ‘svchost’ 컨테이너에서 서비스를 격리합니다.

        sc config TermService type= own
3. 서비스를 시작합니다.

        sc start TermService
4. 서비스를 계속 시작할 수 없는 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)합니다.

### <a name="repair-the-vm-offline"></a>오프라인으로 VM 복구

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>복구 VM에 OS 디스크 연결

1. [복구 VM에 OS 디스크를 연결합니다](../windows/troubleshoot-recovery-disks-portal.md).
2. 복구 VM에 대한 원격 데스크톱 연결을 시작합니다. 디스크 관리 콘솔에서 연결된 디스크의 플래그가 **온라인**으로 지정되었는지 확인합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 적어 둡니다.
3. 관리자 권한 명령 프롬프트 인스턴스를 엽니다(**관리자 권한으로 실행**). 그런 다음, 다음 스크립트를 실행합니다. 연결된 OS 디스크에 할당된 드라이브 문자를 **F**라고 가정합니다. 사용자 VM의 적절한 값으로 바꿉니다. 

   ```
   reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv
        
   REM Set default values back on the broken service 
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService“ /f
   reg add "HKLM\BROKENSYSTEM\ControlSet001\services\TermService" /v type /t REG_DWORD /d 16 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v start /t REG_DWORD /d 3 /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v ObjectName /t REG_SZ /d "NT Authority\NetworkService" /f
   reg add "HKLM\BROKENSYSTEM\ControlSet002\services\TermService" /v type /t REG_DWORD /d 16 /f
   ```

4. [OS 디스크를 분리하고 VM을 다시 만듭니다](../windows/troubleshoot-recovery-disks-portal.md). 그런 다음, 문제가 해결되었는지 확인합니다.

## <a name="need-help-contact-support"></a>도움 필요 시 지원 문의

추가 도움이 필요한 경우 [지원에 문의](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)하여 문제를 해결하세요.
