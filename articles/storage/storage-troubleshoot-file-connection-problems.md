---
title: "Azure File Storage 문제 해결 | Microsoft Docs"
description: "Azure File Storage 문제 해결"
services: storage
documentationcenter: 
author: genlin
manager: felixwu
editor: na
tags: storage
ms.assetid: fbc5f600-131e-4b99-828a-42d0de85fff7
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: genli
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 212b4481affc345cff3e8abd2475c838926f5eda
ms.lasthandoff: 03/03/2017


---
# <a name="troubleshooting-azure-file-storage-problems"></a>Azure File Storage 문제 해결
이 문서에는 Windows 및 Linux 클라이언트에서 연결할 때 Microsoft Azure File Storage에 관련된 일반적인 문제가 나열됩니다. 또한 이러한 문제의 가능한 원인과 해결 방법을 제공합니다.

**일반적인 문제(Windows 및 Linux 클라이언트에서 발생)**

* [파일을 열 때 할당량 오류 발생](#quotaerror)
* [Windows 또는 Linux에서 Azure File Storage에 액세스할 때 성능이 저하됨](#slowboth)
* [Azure File Storage에서 읽기 및 쓰기 작업을 추적하는 방법](#traceop)

**Windows 클라이언트 문제**

* [Windows 8.1 또는 Windows Server 2012 R2에서 Azure File Storage에 액세스할 때 성능이 저하됨](#windowsslow)
* [Azure 파일 공유를 탑재할 때 오류 53 발생](#error53)
* [오류 87 Azure File Share를 마운트하려고 시도하는 중 매개 변수가 올바르지 않음](#error87)
* [Net use에 성공했지만 Windows Explorer에 탑재된 Azure 파일 공유가 표시되지 않음](#netuse)
* [내 저장소 계정에 "/"가 포함되고 net use 명령이 실패함](#slashfails)
* [내 응용 프로그램/서비스가 탑재된 Azure Files 드라이브에 액세스할 수 없음.](#accessfiledrive)
* [성능 최적화를 위한 추가 권장 사항](#additional)
* [파일을 Azure Files에 업로드/복사할 때 "암호화를 지원하지 않는 대상에 파일을 복사하는 중임" 오류 발생](#encryption)

**Linux 클라이언트 문제**

* [일시적인 IO 오류 - 기존 파일 공유에서 “호스트가 중단됨(오류 112)”오류 발생 또는 탑재 지점에서 list 명령을 실행할 때 셸이 중단됨](#errorhold)
* [Linux VM에 Azure Files를 탑재할 때 탑재 오류 115 발생](#error15)
* [Linux VM에 탑재된 Azure 파일 공유의 성능이 느려짐](#delayproblem)


**다른 응용 프로그램에서 액세스**

* [Webjob을 통해 응용 프로그램용 Azure 파일 공유를 참조할 수 있습니까?](#webjobs)

<a id="quotaerror"></a>

## <a name="quota-error-when-trying-to-open-a-file"></a>파일을 열 때 할당량 오류 발생
Windows에서 다음과 같은 오류 메시지가 수신됩니다.

`1816 ERROR_NOT_ENOUGH_QUOTA <--> 0xc0000044`
`STATUS_QUOTA_EXCEEDED`
`Not enough quota is available to process this command`
`Invalid handle value GetLastError: 53`

Linux에서는 다음과 같은 오류 메시지가 수신됩니다.

`<filename> [permission denied]`
`Disk quota exceeded`

### <a name="cause"></a>원인
파일에 허용되는 동시 열린 핸들의 상한에 도달했기 때문에 문제가 발생합니다.

### <a name="solution"></a>해결 방법
일부 핸들을 닫아 동시 열린 핸들 수를 줄이고 다시 시도하세요. 자세한 내용은 [Microsoft Azure Storage 성능 및 확장성 검사 목록](storage-performance-checklist.md)을 참조하세요.

<a id="slowboth"></a>

## <a name="slow-performance-when-accessing-file-storage-from-windows-or-linux"></a>Windows 또는 Linux에서 File Storage에 액세스할 때 성능이 저하됨
* 최소 I/O 크기에 대한 특정 요구 사항이 없을 경우 최적 성능을 위해 I/O 크기로 1MB를 사용하는 것이 좋습니다.
* 쓰기를 통해 확장 중인 파일의 최종 크기를 알고 파일에 아직 기록되지 않은 꼬리에&0;이 포함될 때 소프트웨어에 호환성 문제가 없다면 모든 쓰기를 확장 쓰기로 설정하는 대신 파일 크기를 미리 설정합니다.
* copy 메서드를 다음과 같이 올바르게 사용합니다.
      * 두 파일 공유 간의 전송에는 AZCopy를 사용합니다. 자세한 내용은 [AzCopy 명령줄 유틸리티로 데이터 전송](https://docs.microsoft.com/en-us/azure/storage/storage-use-azcopy#file-copy)을 참조하세요.
      * 온-프레미스 컴퓨터와 파일 공유 간에는 Robocopy를 사용합니다. 자세한 내용은 [빠른 복사를 위한 다중 스레드 robocopy](https://blogs.msdn.microsoft.com/granth/2009/12/07/multi-threaded-robocopy-for-faster-copies/)(영문)를 참조하세요.
<a id="windowsslow"></a>

## <a name="slow-performance-when-accessing-the-file-storage-from-windows-81-or-windows-server-2012-r2"></a>Windows 8.1 또는 Windows Server 2012 R2에서 File Storage에 액세스할 때 성능이 저하됨
Windows 8.1 또는 Windows Server 2012 R2를 실행 중인 클라이언트에서는 핫픽스 [KB3114025](https://support.microsoft.com/kb/3114025)가 설치되어 있는지 확인합니다. 이 핫픽스는 핸들 만들기 및 닫기 성능을 개선합니다.

다음 스크립트를 실행하여 핫픽스가 설치되었는지 확인할 수 있습니다.

`reg query HKLM\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`

핫픽스가 설치된 경우 다음 출력이 표시됩니다.

`HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\LanmanWorkstation\Parameters\Policies`
`{96c345ef-3cac-477b-8fcd-bea1a564241c}    REG_DWORD    0x1`

> [!NOTE]
> 2015년 12월부터 Azure Marketplace의 Windows Server 2012 R2 이미지에는 핫픽스 KB3114025가 기본적으로 설치되어 있습니다.
>
>

<a id="traceop"></a>

### <a name="how-to-trace-the-read-and-write-operations-in-azure-file-storage"></a>Azure File Storage에서 읽기 및 쓰기 작업을 추적하는 방법

[Microsoft Message Analyzer](https://www.microsoft.com/en-us/download/details.aspx?id=44226)는 클라이언트의 요청을 명확한 텍스트로 보여줄 수 있고 연결 요청 및 트랜잭션(여기서는 REST가 아니라 SMB로 가정) 간에 상당히 좋은 관계가 있습니다.  많은 IaaS VM 작업자가 있는 경우 각 클라이언트에서 이를 실행해야 하기 때문에 시간이 많이 소요된다는 단점이 있습니다.

ProcMon과 함께 Message Analyze를 사용하면 응용 프로그램 코드가 트랜잭션에 역할을 할 수 있는 좋은 아이디어를 얻을 수 있습니다.

<a id="additional"></a>

## <a name="additional-recommendations-to-optimize-performance"></a>성능 최적화를 위한 추가 권장 사항
쓰기 액세스를 요청하지만 읽기 액세스를 요청하지 않는 캐시된 I/O의 경우 파일을 만들거나 열지 마세요. 즉, **CreateFile()**을 호출할 때 **GENERIC_WRITE**를 지정하지 말고 항상 specify **GENERIC_READ | GENERIC_WRITE**를 지정하세요. 쓰기 전용 핸들은 파일의 유일한 열린 핸들이더라도 로컬에서 작은 쓰기를 캐시할 수 없습니다. 이로 인해 작은 쓰기에 대한 심각한 성능 저하가 발생합니다. CRT **fopen()**에 대한 “단일” 모드는 쓰기 전용 핸들을 엽니다.

<a id="error53"></a>

## <a name="error-53-or-error-67-when-you-try-to-mount-or-unmount-an-azure-file-share"></a>Azure 파일 공유를 탑재 또는 탑재 해제할 때 “오류 53” 또는 “오류 67” 발생
이 문제는 다음 조건에서 발생할 수 있습니다.

### <a name="cause-1"></a>원인 1
“시스템 오류 53이 발생했습니다. 액세스가 거부됩니다.” 보안상 이유로, 통신 채널이 암호화되지 않고 Azure 파일 공유가 있는 같은 데이터 센터에서 연결 시도가 이루어지지 않을 경우 Azure 파일 공유에 대한 연결이 차단됩니다. 사용자의 클라이언트 OS가 SMB 암호화를 지원하지 않을 경우 통신 채널 암호화가 제공되지 않습니다. 사용자가 온-프레미스 또는 다른 데이터 센터에서 파일 공유를 탑재할 때 “시스템 오류 53이 발생했습니다. 액세스가 거부됩니다.” 오류 메시지로 표시됩니다. Windows 8, Windows Server 2012 및 각각의 이후 버전은 암호화를 지원하는 SMB 3.0이 포함된 요청을 협상합니다.

### <a name="solution-for-cause-1"></a>원인 1의 해결 방법
Windows 8, Windows Server 2012 또는 이후 버전의 요구 사항을 충족하는 클라이언트에서 연결하거나, Azure 파일 공유에 사용되는 Azure Storage 계정과 같은 데이터 센터에 있는 가상 시스템에서 연결하세요.

### <a name="cause-2"></a>원인 2
Azure 파일 공유를 탑재할 때 수신되는 “시스템 오류 53” 또는 “시스템 오류 67”은 Azure Files 데이터 센터에 대한 포트 445 아웃바운드 통신이 차단될 경우 발생할 수 있습니다. 포트 445에서 시작되는 액세스를 허용하거나 거부하는 ISP에 대한 요약을 확인하려면 [여기](http://social.technet.microsoft.com/wiki/contents/articles/32346.azure-summary-of-isps-that-allow-disallow-access-from-port-445.aspx)를 클릭하세요.

Comcast 및 일부 IT 조직은 이 포트를 차단합니다. 이 이유로 "시스템 오류 53" 메시지가 수신되었는지 이해하려면 Portqry를 사용하여 TCP:445 끝점을 쿼리할 수 있습니다. TCP:445 끝점이 필터링됨으로 표시될 경우 TCP 포트가 차단됩니다. 다음은 예제 쿼리입니다.

`g:\DataDump\Tools\Portqry>PortQry.exe -n [storage account name].file.core.windows.net -p TCP -e 445`

TCP 445가 네트워크 경로를 따라 규칙을 통해 차단될 경우 다음 출력이 표시됩니다.

**TCP port 445 (microsoft-ds service): FILTERED**

Portqry 사용에 대한 자세한 내용은 [Portqry.exe 명령줄 유틸리티에 대한 설명](https://support.microsoft.com/kb/310099)을 참조하세요.

### <a name="solution-for-cause-2"></a>원인 2의 해결 방법
IT 조직과 협력하여 [Azure IP 범위](https://www.microsoft.com/download/details.aspx?id=41653)에 대한 포트 445 아웃바운드를 엽니다.

<a id="error87"></a>
### <a name="cause-3"></a>원인 3
클라이언트에서 NTLMv1 통신이 사용될 경우 “시스템 오류 53 또는 시스템 오류 87”이 수신될 수도 있습니다. NTLMv1을 사용하도록 설정하면 클라이언트 보안이 약화됩니다. 따라서 Azure Files에 대한 통신이 차단됩니다. 이것이 오류의 원인인지 확인하려면 다음 레지스트리 하위 키가 3의 값으로 설정되어 있는지 확인합니다.

HKLM\SYSTEM\CurrentControlSet\Control\Lsa > LmCompatibilityLevel.

자세한 내용은 TechNet에서 [LmCompatibilityLevel](https://technet.microsoft.com/library/cc960646.aspx) 항목을 참조하세요.

### <a name="solution-for-cause-3"></a>원인 3의 해결 방법
이 문제를 해결하려면 HKLM\SYSTEM\CurrentControlSet\Control\Lsa 레지스트리 키의 LmCompatibilityLevel 값을 기본값 3으로 되돌립니다.

Azure Files는 NTLMv2 인증만 지원합니다. 클라이언트에 그룹 정책이 적용되는지 확인합니다. 이렇게 하면 이 오류가 발생하지 않습니다. 이 방법은 보안 모범 사례로도 간주합니다. 자세한 내용은 [그룹 정책을 통해 NTLMv2를 사용하도록 클라이언트를 구성하는 방법](https://technet.microsoft.com/library/jj852207\(v=ws.11\).aspx)을 참조하세요.

권장되는 정책 설정은 **NTLMv2 응답만 보내기**입니다. 이는 레지스트리 값 3에 해당합니다. 클라이언트는 NTLMv2 인증만 사용하고, 서버에서 지원할 경우 NTLMv2 세션 보안을 사용합니다. 도메인 컨트롤러는 LM, NTLM 및 NTLMv2 인증을 수락합니다.

<a id="netuse"></a>

## <a name="net-use-was-successful-but-dont-see-the-azure-file-share-mounted-in-windows-explorer"></a>Net use에 성공했지만 Windows Explorer에 탑재된 Azure 파일 공유가 표시되지 않음
### <a name="cause"></a>원인
기본적으로 Windows Explorer는 관리자 권한으로 실행되지 않습니다. 관리자 명령 프롬프트에서 **net use**를 실행할 경우 네트워크 드라이브를 "관리자 권한으로" 매핑합니다. 매핑된 드라이브는 사용자 중심이므로 다른 사용자 계정으로 탑재될 경우 로그인된 사용자 계정에 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법
비관리자 명령줄에서 공유를 탑재하세요. 또는 [이 TechNet 항목](https://technet.microsoft.com/library/ee844140.aspx)에 따라 **EnableLinkedConnections** 레지스트리 값을 구성할 수 있습니다.

<a id="slashfails"></a>

## <a name="my-storage-account-contains--and-the-net-use-command-fails"></a>내 저장소 계정에 "/"가 포함되고 net use 명령이 실패함
### <a name="cause"></a>원인
**net use** 명령은 명령 프롬프트(cmd.exe)에서 실행될 경우 명령줄 옵션으로 "/"를 추가하여 구문 분석됩니다. 이로 인해 드라이브 매핑이 실패합니다.

### <a name="solution"></a>해결 방법
다음 단계 중 하나를 사용하여 문제를 해결할 수 있습니다.

•    다음 PowerShell 명령을 사용합니다.

`New-SmbMapping -LocalPath y: -RemotePath \\server\share  -UserName acountName -Password "password can contain / and \ etc"`

배치 파일에서 이 작업을 수행할 수 있습니다.

`Echo new-smbMapping ... | powershell -command –`

•    "/"가 첫 번째 문자가 아닐 경우 키 주위에 큰따옴표를 배치하여 이 문제를 해결합니다. “/”가 첫 번째 문자일 경우에는 대화형 모드를 사용하여 암호를 개별적으로 입력하거나 키를 재생성하여 슬래시(/) 문자로 시작되지 않는 키를 얻습니다.

<a id="accessfiledrive"></a>

## <a name="my-applicationservice-cannot-access-mounted-azure-files-drive"></a>내 응용 프로그램/서비스가 탑재된 Azure Files 드라이브에 액세스할 수 없음
### <a name="cause"></a>원인
드라이브는 사용자별로 탑재됩니다. 응용 프로그램이나 서비스가 다른 사용자 계정으로 실행될 경우 사용자에게 드라이브가 표시되지 않습니다.

### <a name="solution"></a>해결 방법
응용 프로그램을 실행하는 동일한 사용자 계정으로 드라이브를 탑재하세요. psexec 등의 도구를 사용하여 이 작업을 수행할 수 있습니다.

**net use**에 대한 또 다른 옵션은 **net use** 명령의 user name 및 password 매개 변수로 저장소 계정 이름 및 키를 전달하는 것입니다.

이러한 지침을 따른 후 “시스템 오류 1312가 발생했습니다. 지정된 로그온 세션이 없습니다. 이미 종료된 것 같습니다.”라는 오류 메시지가 시스템/네트워크 서비스 계정에 대해 **net use**를 실행할 때 수신될 수 있습니다. 이 문제가 발생하면 **net use**에 전달되는 사용자 이름에 도메인 정보(예: "[저장소 계정 이름].file.core.windows.net")가 포함되는지 확인합니다.

<a id="encryption"></a>

## <a name="error-you-are-copying-a-file-to-a-destination-that-does-not-support-encryption"></a>“암호화를 지원하지 않는 대상에 파일을 복사하는 중임” 오류 발생
### <a name="cause"></a>원인
Bitlocker로 암호화된 파일을 Azure Files로 복사할 수 있습니다. 하지만 File Storage는 NTFS EFS를 지원하지 않습니다. 따라서 이 경우 EFS가 사용될 수 있습니다. EFS를 통해 암호화된 파일이 있을 경우 복사 명령이 복사된 파일을 암호 해독하지 않는다면 File Storage에 대한 복사 작업이 실패할 수 있습니다.

### <a name="workaround"></a>해결 방법
파일을 File Storage로 복사하려면 먼저 파일을 암호 해독해야 합니다. 다음 방법 중 하나를 사용하여 이 작업을 수행할 수 있습니다.

•   **copy /d**를 사용합니다.

•   다음 레지스트리 키를 설정합니다.

* Path=HKLM\Software\Policies\Microsoft\Windows\System
* Value type=DWORD
* Name = CopyFileAllowDecryptedRemoteDestination
* Value = 1

하지만 레지스트리 키는 모든 복사 작업을 네트워크 공유에 적용한다는 점에 유의하세요.

<a id="errorhold"></a>

## <a name="host-is-down-error-112-on-existing-file-shares-or-the-shell-hangs-when-you-run-list-commands-on-the-mount-point"></a>기존 파일 공유에서 “호스트가 중단됨(오류 112)” 오류 발생 또는 탑재 지점에서 list 명령을 실행할 때 셸이 중단됨
### <a name="cause"></a>원인
Linux 클라이언트에서 클라이언트가 장시간 유휴 상태일 경우 이 오류가 발생합니다. 이 오류가 발생하면 클라이언트 연결이 끊어지고 클라이언트 연결 시간이 초과됩니다. 또한 이 오류는 기본값인 "소프트" 탑재 옵션을 사용하는 경우 서버에 TCP 연결을 다시 설정되지 않는 통신 오류를 나타낼 수 있습니다.

이 오류는 이전 커널의 알려진 일부 버그 또는 네트워크 오류와 같이 재연결을 막는 기타 문제로 인해 야기될 수 있는 Linux 재연결 문제를 나타낼 수 있습니다. 

### <a name="solution"></a>해결 방법

하드 탑재를 지정하면 클라이언트는 연결될 때까지 또는 명시적으로 중단될 때까지 대기하게 되어 네트워크 시간 제한으로 인해 오류가 발생하지 않도록 할 수 있습니다. 하지만 사용자는 무한 대기할 수 있고 필요에 따라 연결을 중단해야 함을 알아야 합니다.

Linux 커널의 이러한 재연결 문제는 현재 다음 변경 집합의 일부로 수정되었습니다.

* [소켓 재연결 후에 오랫 동안 smb3 세션 재연결이 지연되지 않도록 재연결 수정](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/fs/cifs?id=4fcd1813e6404dd4420c7d12fb483f9320f0bf93)

* [소켓 재연결 직후 에코 서비스 호출](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=b8c600120fc87d53642476f48c8055b38d6e14c7)

* [CIFS: 재연결 동안 가능한 메모리 손상 수정](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=53e0e11efe9289535b060a51d4cf37c25e0d0f2b)

* [CIFS: 재연결 동안 가능한 뮤텍스 이중 잠금 해결 - 커널 v4.9 이상](https://git.kernel.org/cgit/linux/kernel/git/torvalds/linux.git/commit/?id=96a988ffeb90dba33a71c3826086fe67c897a183) 

그러나 이 변경 내용이 모든 Linux 배포판에 아직 이식되지 않은 것일 수 있습니다. 이를 포함하는 재연결 수정 프로그램이 있는 잘 알려진 Linux 커널 목록은 4.4.40+ 4.8.16+ 4.9.1+입니다. 최신 수정 프로그램을 선택하기 위해 위의 권장 커널 버전으로 전환할 수 있습니다.

### <a name="workaround"></a>해결 방법
최신 커널 버전으로 전환할 수 없는 경우 30초보다 짧은 간격으로 쓰는 Azure 파일 공유에 파일을 보관하여 이 문제를 해결할 수 있습니다. 이 작업은 만든/수정된 날짜를 파일에 다시 쓰는 등의 쓰기 작업이어야 합니다. 그렇지 않으면 캐시된 결과를 얻을 수 있고 작업이 재연결을 트리거하지 않을 수 있습니다. 


<a id="error15"></a>

## <a name="mount-error-115-when-you-try-to-mount-azure-files-on-the-linux-vm"></a>Linux VM에 Azure 파일을 탑재할 때 “탑재 오류 115” 발생
### <a name="cause"></a>원인
Linux 배포는 아직 SMB 3.0의 암호화 기능을 지원하지 않습니다. 일부 배포에서는 사용자가 SMB 3.0을 사용하여 Azure 파일을 탑재할 경우 기능 누락으로 인해 “115” 오류 메시지를 수신할 수 있습니다.

### <a name="solution"></a>해결 방법
사용되는 Linux SMB 클라이언트가 암호화를 지원하지 않을 경우 File Storage 계정과 같은 데이터 센터의 Linux VM에서 SMB 2.1을 사용하여 Azure 파일을 탑재합니다.

<a id="delayproblem"></a>

## <a name="azure-file-share-mounted-on-linux-vm-experiencing-slow-performance"></a>Linux VM에 탑재된 Azure 파일 공유의 성능이 느려짐

성능 저하의 가능한 원인은 캐싱이 해제되었기 때문일 수 있습니다. 캐시를 사용하도록 설정했는지를 확인하려면 "cache="를 찾아봅니다.  *cache=none*은 캐싱이 비활성화되었음을 나타냅니다. 기본 캐싱이나 "엄격한" 캐싱 모드를 활성화하는 명령을 탑재하기 위해 기본 탑재 명령을 사용하거나 명시적으로 **cache=strict** 옵션을 추가하여 공유를 다시 탑재하세요.

일부 시나리오에서 serverino mount 옵션은 ls 명령이 모든 디렉터리 항목에 대해 통계를 실행하도록 할 수 있으며 큰 디렉터리를 나열할 때는 성능이 저하될 수 있습니다. "/etc/fstab" 항목에서 탑재 옵션을 확인할 수 있습니다.

`//azureuser.file.core.windows.net/cifs        /cifs   cifs vers=3.0,serverino,username=xxx,password=xxx,dir_mode=0777,file_mode=0777`

명령 **sudo mount | grep cifs**를 실행하고 출력을 확인하여 올바른 옵션이 사용되고 있는지 간단히 확인할 수도 있습니다.

`//mabiccacifs.file.core.windows.net/cifs on /cifs type cifs
(rw,relatime,vers=3.0,sec=ntlmssp,cache=strict,username=xxx,domain=X,uid=0,noforceuid,gid=0,noforcegid,addr=192.168.10.1,file_mode=0777,
dir_mode=0777,persistenthandles,nounix,serverino,mapposix,rsize=1048576,wsize=1048576,actimeo=1)`

cache=strict 또는 serverino 옵션이 없는 경우 [설명서](https://docs.microsoft.com/en-us/azure/storage/storage-how-to-use-files-linux#mount-the-file-share)의 mount 명령을 실행하여 은 Azure 파일을 분리했다가 탑재한 다음 "/etc/fstab" 항목에 올바른 옵션이 있는지 다시 확인합니다.

<a id="webjobs"></a>

## <a name="accessing-from-other-applications"></a>다른 응용 프로그램에서 액세스
### <a name="can-i-reference-the-azure-file-share-for-my-application-through-a-webjob"></a>Webjob을 통해 응용 프로그램용 Azure 파일 공유를 참조할 수 있습니까?
앱 서비스 샌드박스에서 SMB 공유를 탑재할 수 없습니다. 해결 방법으로 매핑된 드라이브로 Azure 파일 공유를 매핑할 수 있으며 응용 프로그램이 Azure 파일 공유를 드라이브 문자로서 액세스할 수 있도록 할 수 있습니다.
## <a name="learn-more"></a>자세한 정보
* [Windows에서 Azure File Storage 시작](storage-dotnet-how-to-use-files.md)
* [Linux에서 Azure File Storage 시작](storage-how-to-use-files-linux.md)

