---
title: Windows 가상 데스크톱 세션 호스트 문제 해결 - Azure
description: Windows 가상 데스크톱 세션을 구성할 때 문제를 해결하는 방법 가상 시스템 호스트입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127595"
---
# <a name="session-host-virtual-machine-configuration"></a>세션 호스트 가상 머신 구성

이 문서를 사용하여 Windows 가상 데스크톱 세션 호스트 가상 시스템(VM)을 구성할 때 겪고 있는 문제를 해결합니다.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="vms-are-not-joined-to-the-domain"></a>VM이 도메인에 가입되지 않았습니다.

도메인에 VM을 가입하는 데 문제가 있는 경우 다음 지침을 따르십시오.

- [Windows Server 가상 컴퓨터 조인에서 관리되는 도메인에](../active-directory-domain-services/join-windows-vm.md) 가상 컴퓨터 가입 또는 도메인 [조인 템플릿을](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)사용하여 수동으로 VM에 가입합니다.
- VM의 명령줄에서 도메인 이름을 ping해 보십시오.
- 도메인 조인 오류 메시지 [에서](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)도메인 조인 오류 메시지 목록을 검토합니다.

### <a name="error-incorrect-credentials"></a>오류: 잘못된 자격 증명

**원인:** Azure 리소스 관리자 템플릿 인터페이스 수정에 자격 증명을 입력할 때 만든 오타가 있었습니다.

**수정 사항:** 다음 작업 중 하나를 수행하여 해결합니다.

- VM을 도메인에 수동으로 추가합니다.
- 자격 증명이 확인되면 템플릿을 다시 배포합니다. [PowerShell을 사용하여 호스트 풀 만들기를](create-host-pools-powershell.md)참조하십시오.
- [기존 Windows VM을 AD 도메인에 조인하는 템플릿을 사용하여 VM을 도메인에 조인합니다.](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)

### <a name="error-timeout-waiting-for-user-input"></a>오류: 사용자 입력을 기다리는 시간 시간

**원인:** 도메인 가입을 완료하는 데 사용되는 계정에는 MFA(다단계 인증)가 있을 수 있습니다.

**수정 사항:** 다음 작업 중 하나를 수행하여 해결합니다.

- 계정의 MFA를 일시적으로 제거합니다.
- 서비스 계정을 사용합니다.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>오류: 프로비저닝 중에 사용된 계정에 작업을 완료할 수 있는 권한이 없습니다.

**원인:** 사용 중인 계정에는 규정 준수 및 규정으로 인해 VM을 도메인에 가입할 수 있는 권한이 없습니다.

**수정 사항:** 다음 작업 중 하나를 수행하여 해결합니다.

- 관리자 그룹의 구성원인 계정을 사용합니다.
- 사용 중인 계정에 필요한 권한을 부여합니다.

### <a name="error-domain-name-doesnt-resolve"></a>오류: 도메인 이름이 확인되지 않습니다.

**원인 1:** VM은 도메인이 있는 VNET(가상 네트워크)과 연결되지 않은 가상 네트워크에 있습니다.

**수정 1:** VM이 프로비전된 VNET과 도메인 컨트롤러(DC)가 실행 중인 VNET 간에 VNET 피어링을 만듭니다. [가상 네트워크 피어링 만들기 - 리소스 관리자, 다른 구독](../virtual-network/create-peering-different-subscriptions.md)을 참조하십시오.

**원인 2:** Azure Active Directory 도메인 서비스(Azure AD DS)를 사용하는 경우 가상 네트워크에는 관리되는 도메인 컨트롤러를 가리키도록 업데이트된 DNS 서버 설정이 없습니다.

**수정 2:** Azure AD DS를 포함하는 가상 네트워크의 DNS 설정을 업데이트하려면 [Azure 가상 네트워크에 대한 DNS 업데이트 설정을](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)참조하십시오.

**원인 3:** 네트워크 인터페이스의 DNS 서버 설정은 가상 네트워크의 적절한 DNS 서버를 가리키지 않습니다.

**수정 3:** [DNS 서버 변경]의 단계에 따라 다음 작업 중 하나를 해결합니다.
- 네트워크 인터페이스의 DNS 서버 설정을 [DNS 서버 변경](../virtual-network/virtual-network-network-interface.md#change-dns-servers) 단계와 함께 **사용자 지정으로** 변경하고 가상 네트워크에서 DNS 서버의 개인 IP 주소를 지정합니다.
- 네트워크 인터페이스의 DNS 서버 설정을 [변경 DNS 서버의](../virtual-network/virtual-network-network-interface.md#change-dns-servers)단계로 **가상 네트워크에서 상속으로** 변경한 다음 DNS 서버 변경 단계의 단계로 가상 네트워크의 DNS 서버 설정을 [변경합니다.](../virtual-network/manage-virtual-network.md#change-dns-servers)

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>윈도우 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 부트 로더 설치 되지 않습니다.

VM을 프로비전하는 권장 되는 방법은 Azure 리소스 관리자 **만들기 및 프로비전 Windows 가상 데스크톱 호스트 풀** 템플릿입니다. 템플릿은 Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부트 로더를 자동으로 설치합니다.

다음 지침에 따라 구성 요소가 설치되어 있는지 확인하고 오류 메시지를 확인합니다.

1. **제어판** > **프로그램** > **프로그램 및 기능을**체크 인하여 두 구성 요소가 설치되어 있는지 확인합니다. **Windows 가상 데스크톱 에이전트** 및 Windows 가상 데스크톱 에이전트 부트 **로더가** 표시되지 않으면 VM에 설치되지 않습니다.
2. **파일 탐색기를** 열고 **C:\Windows\Temp\ScriptLog.log 로그로**이동합니다. 파일이 없는 경우 두 구성 요소를 설치한 PowerShell DSC가 제공된 보안 컨텍스트에서 실행할 수 했음을 나타냅니다.
3. **파일 C:\Windows\Temp\ScriptLog.log가** 있는 경우 파일을 열고 오류 메시지가 있는지 확인합니다.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>오류: Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부트 로더가 없습니다. C:\Windows\Temp\ScriptLog.log도 없습니다.

**원인 1:** Azure 리소스 관리자 템플릿에 대한 입력 중에 제공된 자격 증명이 올바르지 않거나 사용 권한이 부족했습니다.

**수정 1:** PowerShell을 사용하여 호스트 풀 만들기를 사용하여 누락된 구성 요소를 VM에 수동으로 [추가합니다.](create-host-pools-powershell.md)

**원인 2:** PowerShell DSC를 시작하고 실행할 수 있었지만 Windows 가상 데스크톱에 로그인하여 필요한 정보를 얻을 수 없기 때문에 완료하지 못했습니다.

**수정 2:** 다음 목록에서 항목을 확인합니다.

- 계정에 MFA가 없는지 확인합니다.
- 테넌트 이름이 정확하고 테넌트가 Windows 가상 데스크톱에 있는지 확인합니다.
- 계정에 RDS 기여자 권한이 적어도 있는지 확인합니다.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>오류: 인증실패, C:\Windows\Temp\ScriptLog.log의 오류

**원인:** PowerShell DSC를 실행할 수 있었지만 Windows 가상 데스크톱에 연결할 수 없습니다.

**수정 사항:** 다음 목록에서 항목을 확인합니다.

- Windows 가상 데스크톱 서비스에 VM을 수동으로 등록합니다.
- Windows Virtual Desktop에 연결하는 데 사용된 계정에 테넌트에 호스트 풀을 만들 수 있는 권한이 있는지 확인합니다.
- 계정에 MFA가 없는지 확인합니다.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Windows 가상 데스크톱 에이전트가 Windows 가상 데스크톱 서비스에 등록되지 않음

Windows 가상 데스크톱 에이전트가 세션 호스트 VM(수동 또는 Azure 리소스 관리자 템플릿 및 PowerShell DSC을 통해)에 처음 설치되면 등록 토큰을 제공합니다. 다음 섹션에서는 Windows 가상 데스크톱 에이전트 및 토큰에 적용되는 문제 해결 문제를 다룹니다.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>오류: Get-RdsSessionHost cmdlet에 제출된 상태는 사용할 수 없음으로 상태를 표시합니다.

![Get-RdsSessionHost cmdlet은 상태를 사용할 수 없음으로 표시합니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 에이전트가 새 버전으로 업데이트할 수 없습니다.

**수정 사항:** 다음 지침에 따라 에이전트를 수동으로 업데이트합니다.

1. 세션 호스트 VM에서 에이전트의 새 버전을 다운로드합니다.
2. 작업 관리자를 시작하고 서비스 탭에서 RDAgentBootLoader 서비스를 중지합니다.
3. Windows 가상 데스크톱 에이전트의 새 버전에 대한 설치 관리자를 실행합니다.
4. 등록 토큰에 대한 메시지가 표시되면 항목 INVALID_TOKEN 제거하고 다음을 누릅니다(새 토큰은 필요하지 않습니다).
5. 설치 마법사를 완료합니다.
6. 작업 관리자를 열고 RDAgentBootLoader 서비스를 시작합니다.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>오류: Windows 가상 데스크톱 에이전트 레지스트리 항목 등록 되지 않습니다 0의 값을 표시 합니다.

**원인:** 등록 토큰이 만료되었거나 만료 값 999999로 생성되었습니다.

**수정 사항:** 에이전트 레지스트리 오류를 해결하려면 다음 지침을 따르십시오.

1. 이미 등록 토큰이 있는 경우 제거 RDSRegistrationInfo를 통해 제거합니다.
2. Rds-newRegistrationInfo를 통해 새 토큰을 생성합니다.
3. -ExpriationHours 매개 변수가 72로 설정되어 있는지 확인합니다(최대 값은 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>오류: Windows 가상 데스크톱 에이전트가 Get-RdsSessionHost를 실행할 때 하트비트를 보고하지 않습니다.

**원인 1:** RDAgentBootLoad서비스중지되었습니다.

**수정 1:** 작업 관리자를 시작하고 서비스 탭에서 RDAgentBootLoader 서비스에 대한 중지된 상태를 보고하는 경우 서비스를 시작합니다.

**원인 2:** 포트 443은 폐쇄될 수 있습니다.

**수정 2:** 이 지침에 따라 포트 443을 엽니다.

1. 포트 (443)가 [Sysinternal 도구에서](/sysinternals/downloads/psping/)PSPing 도구를 다운로드하여 열려 있는지 확인하십시오.
2. 에이전트가 실행 중인 세션 호스트 VM에 PSPing을 설치합니다.
3. 관리자로 명령 프롬프트를 열고 아래 명령을 내림을 내림하십시오.

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. PSPing이 RDBroker에서 정보를 다시 받았는지 확인합니다.

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Windows 가상 데스크톱 을 나란히 스택문제 해결

Windows 가상 데스크톱 나란히 스택은 Windows Server 2019와 함께 자동으로 설치됩니다. 마이크로소프트 설치 프로그램 (MSI)를 사용 하 여 마이크로소프트 윈도 서버2016 또는 윈도우 서버 2012 R2에 나란히 스택을 설치. 마이크로소프트 윈도 대 한 10, 윈도 즈 가상 데스크톱 나란히 스택 **enablesxstackrs.ps1와**함께 사용 됩니다.

세션 호스트 풀 VM에서 나란히 스택을 설치하거나 사용하도록 설정하는 방법에는 세 가지가 있습니다.

- Azure 리소스 관리자를 사용하여 새 Windows 가상 데스크톱 호스트 풀 템플릿 **을 만들고 프로비전합니다.**
- 마스터 이미지에 포함 및 활성화됨으로써
- 각 VM(또는 확장/PowerShell)에 수동으로 설치 또는 사용 가능)

Windows 가상 데스크톱 나란히 스택에 문제가 있는 경우 명령 프롬프트에서 **qwinsta** 명령을 입력하여 나란히 스택이 설치되어 있거나 활성화되어 있는지 확인합니다.

나란히 스택이 설치되고 활성화된 경우 **qwinsta의** 출력은 출력에 **rdp-sx를** 나열합니다.

![출력에 rdp-sxs로 나열된 qwinsta를 통해 나란히 스택을 설치하거나 사용할 수 있습니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

아래에 나열된 레지스트리 항목을 검사하고 해당 값이 일치하는지 확인합니다. 레지스트리 키가 없거나 값이 일치하지 않는 경우 [PowerShell을 사용하여 호스트 풀 만들기에서](create-host-pools-powershell.md) 나란히 스택을 다시 설치하는 방법에 대한 지침을 따릅니다.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>오류: O_REVERSE_CONNECT_STACK_FAILURE

![오류 코드가 O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 나란히 스택세션 호스트 VM에 설치되지 않습니다.

**수정 사항:** 다음 지침에 따라 세션 호스트 VM에 나란히 스택을 설치합니다.

1. RDP(원격 데스크톱 프로토콜)를 사용하여 세션 호스트 VM을 로컬 관리자로 직접 가져옵니다.
2. 아직 사용하지 않은 경우 PowerShell 세션에서 사용할 [Windows 가상 데스크톱 PowerShell 모듈을](/powershell/windows-virtual-desktop/overview/) 다운로드하여 가져온 다음 이 cmdlet을 실행하여 계정에 로그인합니다.

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. [PowerShell을 사용하여 호스트 풀 만들기를](create-host-pools-powershell.md)사용하여 나란히 스택을 설치합니다.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>오작동 윈도우 가상 데스크톱 나란히 스택을 해결 하는 방법

나란히 스택이 오작동할 수 있는 알려진 상황이 있습니다.

- 나란히 스택을 활성화하기 위한 단계의 올바른 순서를 따르지 않음
- 윈도우에 자동 업데이트 10 향상 된 다양 한 디스크 (EVD)
- RDSH(원격 데스크톱 세션 호스트) 역할이 누락되었습니다.
- 러닝 인에이크스스택rc.ps1 여러 번
- 로컬 관리자 권한이 없는 계정에서 enablesxstackrc.ps1 실행

이 섹션의 지침은 Windows 가상 데스크톱을 나란히 스택을 제거하는 데 도움이 될 수 있습니다. 나란히 스택을 제거하면 [PowerShell을 사용하여 호스트 풀 만들기에서](create-host-pools-powershell.md) "Windows 가상 데스크톱 호스트 풀로 VM 등록"으로 이동하여 나란히 스택을 다시 설치합니다.

업데이트 적용을 실행하는 데 사용되는 VM은 오작동이 나란히 스택이 있는 VM과 동일한 서브넷 및 도메인에 있어야 합니다.

다음 지침을 따라 동일한 서브넷 및 도메인에서 수정을 실행합니다.

1. 수정이 적용되는 VM에 표준 RDP(RdP)로 연결합니다.
2. 에서 PsExec을 다운로드합니다. https://docs.microsoft.com/sysinternals/downloads/psexec
3. 다운로드한 파일의 압축을 해제합니다.
4. 명령 프롬프트를 로컬 관리자로 시작합니다.
5. PsExec의 압축을 풀었던 폴더로 이동합니다.
6. 명령 프롬프트에서 다음 명령을 사용합니다.

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname은 오작동이 나란히 스택된 VM의 컴퓨터 이름입니다.

7. 동의를 클릭하여 PsExec 라이선스 계약에 동의합니다.

    ![소프트웨어 사용권 계약 스크린샷입니다.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >이 대화 상자는 PsExec이 처음 실행될 때만 표시됩니다.

8. 명령 프롬프트 세션이 VM에서 오작동이 나란히 스택된 후 qwinsta를 실행하고 rdp-sxs라는 항목을 사용할 수 있는지 확인합니다. 그렇지 않은 경우 VM에 나란히 스택이 없으므로 문제가 나란히 스택에 연결되지 않습니다.

    ![관리자 명령 프롬프트](media/AdministratorCommandPrompt.png)

9. 다음 명령을 실행하여 VM에 설치된 Microsoft 구성 요소를 나란히 스택으로 나열합니다.

    ```cmd
        wmic product get name
    ```

10. 위의 단계에서 제품 이름으로 아래 명령을 실행합니다.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "원격 데스크톱"으로 시작하는 모든 제품을 제거합니다.

12. 모든 Windows 가상 데스크톱 구성 요소가 제거된 후 운영 체제에 대한 지침을 따르십시오.

13. 운영 체제가 Windows Server인 경우 Azure 포털 또는 PsExec 도구에서 오작동이 있는 VM을 다시 시작합니다.

운영 체제가 마이크로 소프트 윈도우 인 경우 10, 아래의 지침을 계속 :

14. PsExec을 실행하는 VM에서 파일 탐색기를 열고 복사하여 비활성화sxsstackrc.ps1을 오작동한 나란히 스택이 있는 VM의 시스템 드라이브로 복사합니다.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname은 오작동이 나란히 스택된 VM의 컴퓨터 이름입니다.

15. 권장 프로세스 : PsExec 도구에서 PowerShell을 시작하고 이전 단계에서 폴더로 이동하여 disablesxsstackrc.ps1을 실행합니다. 또는 다음 cmdlet을 실행할 수 있습니다.

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. cmdlet 실행이 완료되면 오작동이 있는 스택을 통해 VM을 다시 시작합니다.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>원격 데스크톱 라이선스 모드가 구성되지 않았습니다.

관리 계정을 사용하여 Windows 10 Enterprise 다중 세션에 로그인하는 경우 "원격 데스크톱 라이선스 모드가 구성되지 않았으며 원격 데스크톱 서비스가 X일 동안 작동을 중지합니다. 연결 브로커 서버에서 서버 관리자를 사용하여 원격 데스크톱 라이선스 모드를 지정합니다."

시간 제한이 만료되면 "이 컴퓨터에 사용할 수 있는 원격 데스크톱 클라이언트 액세스 라이센스가 없기 때문에 원격 세션의 연결이 끊어졌습니다."라는 오류 메시지가 나타납니다.

이러한 메시지 중 하나가 표시되면 이미지에 최신 Windows 업데이트가 설치되어 있지 않거나 그룹 정책을 통해 원격 데스크톱 라이선스 모드를 설정하고 있음을 의미합니다. 다음 섹션의 단계에 따라 그룹 정책 설정을 확인하고 Windows 10 Enterprise 다중 세션 버전을 식별하고 해당 업데이트를 설치합니다.  

>[!NOTE]
>Windows 가상 데스크톱은 호스트 풀에 Windows Server 세션 호스트가 포함되어 있는 경우에만 RDS 클라이언트 액세스 라이선스(CAL)가 필요합니다. RDS CAL을 구성하는 방법을 알아보려면 [클라이언트 액세스 라이선스를 사용하여 RDS 배포 라이선스를 참조하세요.](/windows-server/remote/remote-desktop-services/rds-client-access-license/)

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>원격 데스크톱 라이선스 모드 그룹 정책 설정 사용 안 함

VM에서 그룹 정책 편집기를 열고 **관리 템플릿으로** > 이동하여 그룹 정책 설정을 확인하고 관리**템플릿 Windows 구성 요소** > **원격 데스크톱 서비스** > 원격**데스크톱 서비스 호스트** > **라이센스는** > **원격 데스크톱 라이선스 모드를 설정합니다.** 그룹 정책 설정이 **사용 중이면** **사용 안 함으로**변경합니다. 이미 비활성화된 경우 그대로 둡니다.

>[!NOTE]
>도메인을 통해 그룹 정책을 설정하는 경우 이러한 Windows 10 엔터프라이즈 다중 세션 VM을 대상으로 하는 정책에서 이 설정을 사용하지 않도록 설정합니다.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>사용 중인 Windows 10 엔터프라이즈 멀티 세션 버전 식별

Windows 10 엔터프라이즈 다중 세션 버전을 확인하려면 다음을 수행하십시오.

1. 관리자 계정으로 로그인합니다.
2. 시작 메뉴 옆의 검색 표시줄에 "About"를 입력합니다.
3. PC 정보 에서 를 **선택합니다.**
4. '버전' 옆의 번호를 확인합니다. 숫자는 다음 이미지와 같이 "1809" 또는 "1903"이어야 합니다.

    ![Windows 사양 창의 스크린샷입니다. 버전 번호는 파란색으로 강조 표시됩니다.](media/windows-specifications.png)

이제 버전 번호를 알고 있으므로 관련 섹션으로 건너뜁니다.

### <a name="version-1809"></a>버전 1809

버전 번호에 "1809"라고 적으면 [KB4516077 업데이트를](https://support.microsoft.com/help/4516077)설치합니다.

### <a name="version-1903"></a>버전 1903

Azure 갤러리에서 Windows 10 버전 1903 이미지의 최신 버전으로 호스트 운영 체제를 다시 배포합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 문제를 해결하려면 [테넌트 및 호스트 풀 만들기를](troubleshoot-set-up-issues.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결하려면 [Windows 가상 데스크톱 서비스 연결을](troubleshoot-service-connection.md)참조하십시오.
- 원격 데스크톱 클라이언트의 문제 해결을 위해 [원격 데스크톱 클라이언트 문제 해결을](troubleshoot-client.md) 참조하세요.
- Windows 가상 데스크톱에서 PowerShell을 사용할 때 문제를 해결하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조하십시오.
- 서비스에 대한 자세한 내용은 [Windows 가상 데스크톱 환경을](environment-setup.md)참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
