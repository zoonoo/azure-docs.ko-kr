---
title: Windows 가상 데스크톱-Azure에서에서 호스트 및 테 넌 트 풀 만들기
description: Windows 가상 데스크톱 환경에서 테 넌 트 및 세션 호스트 가상 컴퓨터 (VM)를 구성 하는 경우 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928124"
---
# <a name="tenant-and-host-pool-creation"></a>테 넌 트 및 호스트 풀 만들기

이 문서를 사용 하 여 Windows 가상 데스크톱 세션 호스트 virtual machines (Vm)를 구성 하는 경우 겪고 있는 문제를 해결 합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="vms-are-not-joined-to-the-domain"></a>Vm은 도메인에 가입 되지

문제가 발생 하는 도메인에 Vm을 가입 하는 경우 다음이 지침을 따릅니다.

- 프로세스를 사용 하 여 수동으로 VM을 가입 [Windows Server 가상 머신을 관리 되는 도메인에 가입](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) 를 사용 하 여 또는 [도메인 조인 템플릿은](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)합니다.
- VM의 명령줄에서 도메인 이름을 ping 합니다.
- 도메인 조인 오류 메시지 목록을 검토 [도메인 조인 오류 메시지 문제 해결](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx)합니다.

### <a name="error-incorrect-credentials"></a>오류: 잘못 된 자격 증명

**원인:** 템플릿 인터페이스 수정 Azure Resource Manager에서 자격 증명을 입력 하는 경우를 잘못 입력 했습니다.

**해결 방법:** 자격 증명을 수정 하려면 다음이 지침을 따릅니다.

1. 도메인에 Vm을 수동으로 추가 합니다.
2. 자격 증명 확인 한 후 다시 배포 합니다. 참조 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)합니다.
3. Vm을 사용 하 여 템플릿을 사용 하 여 도메인 가입 [AD 도메인에 기존 Windows VM을 조인](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/)합니다.

### <a name="error-timeout-waiting-for-user-input"></a>오류: 사용자 입력을 기다리는 동안 시간이 초과

**원인:** 도메인 가입을 완료 하는 데 사용 된 계정에 multi-factor authentication (MFA) 있을 수 있습니다.

**해결 방법:** 도메인 가입을 완료 하려면 다음이 지침을 따릅니다.

1. 계정에 대해 MFA를 일시적으로 제거 합니다.
2. 서비스 계정을 사용 합니다.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>오류: 프로 비전 하는 동안 사용 된 계정 작업을 완료할 수 있는 권한이 없는 합니다.

**원인:** 사용 중인 계정이 규정 준수 및 규정으로 인해 도메인에 Vm에 연결할 권한이 없습니다.

**해결 방법:** 다음이 지침을 따릅니다.

1. 관리자 그룹의 구성원 인 계정을 사용 합니다.
2. 사용 중인 계정에 필요한 권한을 부여 합니다.

### <a name="error-domain-name-doesnt-resolve"></a>오류: 도메인 이름이 확인 되지 않습니다.

**원인 1:** Vm이 도메인 위치한 가상 네트워크 (VNET)를 사용 하 여 연결 되지 않는 리소스 그룹입니다.

**1의 해결 방법:** Vm 프로 비전 된 있는 VNET과 도메인 컨트롤러 (DC) 실행 되 고 있는 VNET 간에 VNET 피어 링을 만듭니다. 참조 [가상 네트워크 피어 링 만들기-Resource Manager, 다른 구독](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)합니다.

**원인 2:** AadService (AADS)를 사용 하면 DNS 항목이 설정 되지 않았습니다.

**2 해결 방법:** 도메인 서비스를 설정 하려면을 참조 하세요 [Azure Active Directory Domain Services 활성화](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns)합니다.

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 부팅 로더 설치 되어 있지 않습니다.

Vm 프로 비전 하는 권장된 방법은 Azure Resource Manager를 사용 하 되 **만들기 및 프로 비전 Windows 가상 데스크톱 풀 호스트** 템플릿. 템플릿을 자동으로 Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부팅 로더를 설치합니다.

구성 요소가 설치 되었는지 확인 하 고 오류 메시지에 대 한 확인 하려면 다음이 지침을 따릅니다.

1. 두 구성 요소에서 확인 하 여 설치 되어 있는지 확인 **Control Panel** > **프로그램** > **프로그램 및 기능**합니다. 하는 경우 **Windows 가상 데스크톱 에이전트** 및 **Windows 가상 데스크톱 에이전트 부팅 로더** 표시 되지 않습니다, VM에 설치 되지 않습니다.
2. 오픈 **파일 탐색기** 이동할 **C:\Windows\Temp\scriptlogs.log**합니다. 파일이 없는 경우 두 구성 요소를 설치 하는 PowerShell DSC에서 제공 된 보안 컨텍스트를 실행 하지 못했음을 나타냅니다.
3. 하는 경우 파일 **C:\Windows\Temp\scriptlogs.log** 이 제공 하 고 열어서 오류 메시지를 확인 합니다.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>오류: Windows 가상 데스크톱 에이전트 및 Windows 가상 데스크톱 에이전트 부팅 로더가 없습니다. C:\Windows\Temp\scriptlogs.log 누락 됩니다.

**원인 1:** Azure Resource Manager 템플릿에 대 한 입력 중에 제공 된 자격 증명이 잘못 되었거나 충분 한 권한이 없습니다. 합니다.

**1의 해결 방법:** 수동으로 누락 된 구성 요소에 사용 하 여 Vm을 추가할 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)합니다.

**원인 2:** PowerShell DSC에서 시작 하 고 실행할 수 있지만 Windows 가상 데스크톱에 로그인 하 고 필요한 정보를 가져올 수 없습니다.를 완료 하지 못했습니다.

**2 해결 방법:** 다음 목록에 항목을 확인 합니다.

- 계정에 MFA 없는 있는지 확인 합니다.
- 테 넌 트 이름이 정확 하 게 테 넌 트 Windows 가상 데스크톱에 존재 하는지 확인 합니다.
- 계정에 확인 RDS 참가자 권한.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>오류: 인증 실패, C:\Windows\Temp\scriptlogs.log 하는 동안 오류가 발생 했습니다.

**원인:** PowerShell DSC에서 실행할 수 있지만 Windows 가상 데스크톱에 연결할 수 없습니다.

**해결 방법:** 다음 목록에 항목을 확인 합니다.

- 가상 데스크톱 Windows 서비스를 사용 하 여 Vm을 수동으로 등록 합니다.
- Windows 가상 데스크톱에 연결 하는 데 사용 되는 계정 호스트 풀을 만들려면 테 넌 트에 대 한 사용 권한을 확인 합니다.
- 계정에 MFA 없는 확인 합니다.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>가상 데스크톱 Windows 서비스를 사용 하 여 Windows 가상 데스크톱 에이전트 등록 하지

Windows 가상 데스크톱 에이전트 세션에 처음 설치 되는 경우 Vm을 호스팅합니다 (중 하나 또는 통해 수동으로 Azure Resource Manager 템플릿 및 PowerShell DSC), 등록 토큰을 제공 합니다. 다음 섹션에서는 가상 데스크톱 Windows 에이전트 및 토큰에 해당 문제를 설명합니다.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>오류: Get RdsSessionHost cmdlet에서 제출 상태도 사용할 수 없음 상태를 보여 줍니다.

![Get-RdsSessionHost cmdlet으로 사용할 수 없음 상태를 표시합니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** 에이전트를 자동으로 새 버전으로 업데이트할 수 없습니다.

**해결 방법:** 수동으로 에이전트를 업데이트 하려면 다음이 지침을 따릅니다.

1. 세션 호스트 VM에서 에이전트의 새 버전을 다운로드 합니다.
2. 작업 관리자를 시작 하 고 서비스 탭에서 RDAgentBootLoader 서비스를 중지 합니다.
3. 새 버전의 Windows 가상 데스크톱 에이전트 설치 관리자를 실행 합니다.
4. 등록 토큰에 대 한 메시지가 표시 되 면 제거 INVALID_TOKEN 항목 및 키를 눌러 다음 (새 토큰이 필요 하지 않습니다).
5. 설치 마법사를 완료 합니다.
6. 작업 관리자를 열고 RDAgentBootLoader 서비스를 시작 합니다.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>오류:  Windows 가상 데스크톱 에이전트 레지스트리 항목 IsRegistered 0 값을 보여 줍니다.

**원인:** 등록 토큰 만료 되었거나 999999 만료 값을 사용 하 여 생성 되었습니다.

**해결 방법:** 에이전트 레지스트리 오류를 해결 하려면 다음이 지침을 따릅니다.

1. 이미 등록 토큰 있으면 RDSRegistrationInfo 제거를 사용 하 여 제거 합니다.
2. Rds NewRegistrationInfo를 사용 하 여 새 토큰을 생성 합니다.
3. -ExpriationHours 매개 변수 72로 설정 되어 있는지 확인 (최 댓 값 99999를가 하는 데 사용).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>오류: Get-RdsSessionHost를 실행 하는 경우 Windows 가상 데스크톱 에이전트가 하트 비트를 보고 하지 않습니다.

**원인 1:** RDAgentBootLoader 서비스가 중지 되었습니다.

**1의 해결 방법:** 작업 관리자를 시작 하 고 서비스 탭 RDAgentBootLoader 서비스에 대 한 중지 된 상태를 보고 하면 서비스를 시작 합니다.

**원인 2:** 포트 443을 닫을 수 있습니다.

**2 해결 방법:** 포트 443을 열려면 다음이 지침을 따릅니다.

1. 확인에서 PSPing 도구를 다운로드 하 여 포트 443이 열려 [Sysinternal 도구](https://docs.microsoft.com/sysinternals/downloads/psping)합니다.
2. 세션 호스트 에이전트가 실행 되 고 있는 VM에서 PSPing을 설치 합니다.
3. 관리자 권한으로 명령 프롬프트를 열고 아래 명령을 실행 합니다.

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. RDBroker 으로부터의 PSPing 받은 정보를 확인 합니다.

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>가상 데스크톱 Windows side-by-side-스택 사용 하 여 문제 해결

Windows Server 2019를 사용 하 여 Windows 가상 데스크톱 side-by-side-스택 자동으로 설치 됩니다. Microsoft Installer (MSI)를 사용 하 여 Microsoft Windows Server 2016 또는 Windows Server 2012 R2에서 side-by-side-스택 설치. Microsoft Windows 10에 대 한 가상 데스크톱 Windows side-by-side-스택을 사용 하도록 설정 되었는지 **enablesxstackrs.ps1**합니다.

세 가지 주요 side-by-side-스택 설치 가져오거나 세션 호스트 풀 Vm에서 사용 하도록 설정 합니다.

- Azure Resource Manager를 사용 하 여 **만들기 및 프로 비전 새 Windows 가상 데스크톱 호스트 풀** 템플릿
- 포함 되 고 마스터 이미지에서 사용 하도록 설정 하 여
- 설치 하거나 수동으로 각 VM (또는 확장/PowerShell을 사용 하 여) 활성화

문제가 발생 하는 가상 데스크톱 Windows side-by-side-스택과 입력 합니다 **qwinsta** side-by-side-스택 설치 되었거나 사용 하도록 설정 되었음을 확인 하려면 명령 프롬프트에서 명령을 합니다.

출력 **qwinsta** 나열 됩니다 **rdp sxs** side-by-side-스택 설치 및 사용 하도록 설정 된 경우 출력에서 합니다.

![Side-by-side-스택 설치 또는 qwinsta rdp-sxs 출력에서으로 표시를 사용 하 여 설정 합니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

아래에 나열 된 레지스트리 항목을 검사 하 고 해당 값과 일치 하는지 확인 합니다. 지침에 따라 레지스트리 키가 없는 경우 값에 일치 하지 않습니다 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) side-by-side-스택을 다시 설치 하는 방법에 있습니다.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>오류: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE 오류 코드입니다.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**원인:** Side-by-side-스택 세션 호스트 VM에 설치 되지 않았습니다.

**해결 방법:** Side-by-side-스택 세션 호스트 VM에서 설치 하려면 다음이 지침을 따릅니다.

1. 원격 데스크톱 프로토콜 (RDP)를 사용 하 여 로컬 관리자 권한으로 세션 호스트 VM에 직접 가져옵니다.
2. 다운로드 및 가져오기 [The Windows 가상 데스크톱 PowerShell 모듈](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) 아직 없는 경우 PowerShell 세션에서 사용 하도록 합니다.
3. 사용 하 여 side-by-side-스택 설치 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell)합니다.

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>오작동 하는 가상 데스크톱 Windows side-by-side-스택을 수정 하는 방법

Side-by-side-스택 오작동을 일으킬 수 있는 상황으로 알려져 있습니다.

- Side-by-side-스택을 사용 하도록 올바른 단계 순서를 팔 로우 하지 않음
- 자동 업데이트를 Windows 10 향상 된 다양 한 디스크 (EVD)
- 세션 호스트 RDSH (원격 데스크톱) 역할이 없습니다.
- Enablesxsstackrc.ps1를 여러 번 실행
- 로컬 관리자 권한이 없는 계정에 enablesxsstackrc.ps1 실행

이 섹션의에서 지침에서는 가상 데스크톱 Windows side-by-side-스택을 제거할 수 있습니다. Side-by-side-스택을 제거한 후 "Windows 가상 데스크톱 호스트 풀을 사용 하 여 VM에 등록"으로 이동 [PowerShell을 사용 하 여 호스트 풀을 만들](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) side-by-side-스택 다시 설치 합니다.

VM 업데이트 관리를 실행 하는 데 작동 하지 않는 side-by-side-스택 사용 하 여 VM과 동일한 서브넷 및 도메인에 있어야 합니다.

동일한 서브넷 및 도메인에서 업데이트 관리를 실행 하려면 다음이 지침을 수행 합니다.

1. 수정이 적용 됩니다 위치에서 VM에 사용 하 여 표준 원격 데스크톱 프로토콜 (RDP)를 연결 합니다.
2. PsExec을 다운로드 https://docs.microsoft.com/sysinternals/downloads/psexec합니다.
3. 다운로드 한 파일을 압축을 풉니다.
4. 로컬 관리자 권한으로 명령 프롬프트를 시작 합니다.
5. PsExec가 압축 된 폴더로 이동 합니다.
6. 명령 프롬프트에서 다음 명령을 사용 합니다.

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname에 제대로 작동 하지 않는 side-by-side-스택 사용 하 여 VM의 컴퓨터 이름입니다.

7. 동의 클릭 하 여 PsExec 사용권 계약에 동의 합니다.

    ![소프트웨어 라이선스 계약 스크린샷입니다.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >이 대화 상자는 처음에 PsExec 실행 될 때만 표시 됩니다.

8. 명령 프롬프트 세션 오작동 side-by-side-스택 사용 하 여 VM에서 열리면 qwinsta를 실행 하 고 rdp sxs 라는 항목이 사용할 수 있는지 확인 합니다. 그렇지 않은 경우-병렬 스택 없는 VM에서 되므로 문제는 side-by-side-stack에 연결 되지 않습니다.

    ![관리자 명령 프롬프트](media/AdministratorCommandPrompt.png)

9. 있는 오작동 side-by-side-스택 사용 하 여 VM에 설치 하는 Microsoft 구성 요소를 나열 하는 다음 명령을 실행 합니다.

    ```cmd
        wmic product get name
    ```

10. 위의 단계에서 제품 이름을 사용 하 여 아래 명령을 실행 합니다.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. "원격 데스크톱"로 시작 하는 모든 제품을 제거

12. 모든 Windows 가상 데스크톱 구성 요소가 제거 된 후 운영 체제에 대 한 지침을 따르세요.

13. Windows Server 운영 체제 이면 (또는 Azure portal에서 PsExec 도구)는 작동 하지 않는 side-by-side-스택 있던 VM 다시 시작 합니다.

Microsoft Windows 10 운영 체제를 사용 하는 경우 아래 지침을 사용 하 여 계속 합니다.

14. PsExec를 실행 중인 VM에서 파일 탐색기를 열고 disablesxsstackrc.ps1 보내드리는 서비스 side-by-side-스택 사용 하 여 VM의 시스템 드라이브에 복사 합니다.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname에 제대로 작동 하지 않는 side-by-side-스택 사용 하 여 VM의 컴퓨터 이름입니다.

15. 권장 되는 프로세스: PsExec 도구에서 PowerShell 시작 및 이전 단계에서 폴더로 이동 하 고 disablesxsstackrc.ps1를 실행 합니다. 또는 다음 cmdlet을 실행할 수 있습니다.

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Cmdlet은 완료 되 면 제대로 작동 하지 않는 side-by-side-스택 사용 하 여 VM을 다시 시작을 실행 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 추적 문제 해결에 대 한 개요를 참조 하세요 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)합니다.
- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 문제를 해결 하려면 참조 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다.
- Windows 가상 데스크톱의 가상 머신 (VM)를 구성 하는 동안 문제를 해결 하려면 참조 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다.
- Windows 가상 데스크톱 클라이언트 연결을 사용 하 여 문제를 해결 하려면 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)합니다.
- Windows 가상 데스크톱을 사용 하 여 PowerShell을 사용 하는 경우 문제를 해결 하려면 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)합니다.
- 미리 보기 서비스에 대 한 자세한 내용은 참조 하세요 [Windows 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)합니다.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.