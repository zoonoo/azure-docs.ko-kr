---
title: Windows 가상 데스크톱에서 원격 데스크톱 클라이언트 연결-Azure
description: Windows 가상 데스크톱 테 넌 트 환경에서 클라이언트 연결을 설정할 때 발생 하는 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 4f325d9fc512fd9f6776fcd799b720aaf60ce472
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876756"
---
# <a name="remote-desktop-client-connections"></a>원격 데스크톱 클라이언트 연결

이 문서를 사용 하 여 Windows 가상 데스크톱 클라이언트 연결 문제를 해결할 수 있습니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="you-cant-open-a-web-client"></a>웹 클라이언트를 열 수 없습니다.

다른 웹 사이트를 열어 인터넷 연결이 있는지 확인 합니다. 예를 들면 [www.Bing.com](https://www.bing.com)입니다.

**Nslookup** 을 사용 하 여 DNS에서 FQDN을 확인할 수 있는지 확인 합니다.

```cmd
nslookup rdweb.wvd.microsoft.com
```

Windows 7 또는 Windows 10 용 원격 데스크톱 클라이언트와 같은 다른 클라이언트를 사용 하 여 연결을 시도 하 고 웹 클라이언트를 열 수 있는지 확인 합니다.

### <a name="error-opening-another-site-fails"></a>오류: 다른 사이트를 열지 못했습니다.

**원인:** 네트워크 문제 및/또는 중단.

**방법을** 네트워크 지원에 문의 하십시오.

### <a name="error-nslookup-cannot-resolve-the-name"></a>오류: Nslookup에서 이름을 확인할 수 없습니다.

**원인:** 네트워크 문제 및/또는 중단.

**방법을** 네트워크 지원에 문의

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>오류: 연결할 수는 없지만 다른 클라이언트는 연결할 수 있습니다.

**원인:** 브라우저가 예상 대로 작동 하지 않고 작동을 중지 했습니다.

**방법을** 브라우저 문제를 해결 하려면 다음 지침을 따르세요.

1. 브라우저를 다시 시작 합니다.
2. 브라우저 쿠키를 지웁니다. [Internet Explorer에서 쿠키 파일을 삭제 하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)참조 하세요.
3. 브라우저 캐시를 지웁니다. [브라우저에 대 한 브라우저 캐시 지우기를](https://binged.it/2RKyfdU)참조 하세요.
4. 비공개 모드에서 브라우저를 엽니다.

## <a name="web-client-stops-responding-or-disconnects"></a>웹 클라이언트가 응답을 중지 하거나 연결을 끊습니다.

다른 브라우저 또는 클라이언트를 사용 하 여 연결 해 보세요.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>오류: 다른 브라우저 및 클라이언트도 작동 하지 않거나 열리지 않습니다.

**원인:** 네트워크 및/또는 운영 체제 문제 또는 중단

**방법을** 지원 팀에 문의 하세요.

## <a name="web-client-keeps-prompting-for-credentials"></a>웹 클라이언트는 자격 증명을 묻는 메시지를 표시 합니다.

웹 클라이언트에서 자격 증명을 입력 하 라는 메시지가 표시 되 면 다음 지침을 따르세요.

1. 웹 클라이언트 URL이 올바른지 확인 합니다.
2. URL에 연결 된 Windows 가상 데스크톱 환경에 대 한 자격 증명이 있는지 확인 합니다.
3. 브라우저 쿠키를 지웁니다. [Internet Explorer에서 쿠키 파일을 삭제 하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)참조 하세요.
4. 브라우저 캐시를 지웁니다. [브라우저에 대 한 브라우저 캐시 지우기를](https://binged.it/2RKyfdU)참조 하세요.
5. 비공개 모드에서 브라우저를 엽니다.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 또는 Windows 10 용 원격 데스크톱 클라이언트가 응답을 중지 하거나 열 수 없습니다.

다음 PowerShell cmdlet을 사용 하 여 대역 외 (OOB) 클라이언트 레지스트리를 정리 합니다.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

**%AppData%\RdClientRadc** 으로 이동 하 여 모든 콘텐츠를 삭제 합니다.

Windows 7 및 Windows 10 용 원격 데스크톱 클라이언트를 제거 하 고 다시 설치 합니다.

## <a name="troubleshooting-end-user-connectivity"></a>최종 사용자 연결 문제 해결

사용자가 피드 및 로컬 리소스에 액세스할 수 있지만 원격 리소스에 액세스 하지 못하도록 하는 구성, 가용성 또는 성능 문제가 있는 경우도 있습니다. 이러한 경우 사용자는 다음과 유사한 메시지를 가져옵니다.

![원격 데스크톱 연결 오류 메시지입니다.](media/eb76b666808bddb611448dfb621152ce.png)

![게이트웨이 오류 메시지에 연결할 수 없습니다.](media/a8fbb9910d4672147335550affe58481.png)

클라이언트 연결 오류 코드에 대 한 일반적인 문제 해결 지침을 따릅니다.

1. 문제가 발생 한 사용자 이름 및 시간을 확인 합니다.
2. **PowerShell** 을 열고 문제가 보고 된 Windows 가상 데스크톱 테 넌 트에 대 한 연결을 설정 합니다.
3. RdsTenant를 사용 하 여 올바른 테 넌 트에 대 한 연결을 확인 **합니다.**
4. **Get RdsHostPool** 및 **RdsSessionHost** cmdlet을 사용 하 여 올바른 호스트 풀에서 문제 해결을 수행 하 고 있는지 확인 합니다.
5. 아래 명령을 실행 하 여 지정 된 기간 동안 연결 유형의 실패 한 모든 활동 목록을 가져옵니다.

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 이전 cmdlet 출력의 **활동** id를 사용 하 여 아래 명령을 실행 합니다.

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 명령은 다음과 같은 출력을 생성 합니다. **Errorcodesymbolic** 된 및 **ErrorMessage** 를 사용 하 여 근본 원인을 해결 합니다.

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>오류: O_ADD_USER_TO_GROUP_FAILED/사용자 = ≤ username ≥을 GROUP = Remote Desktop Users에 추가 하지 못했습니다. 원인: Win32.ERROR_NO_SUCH_MEMBER

**원인:** VM이 사용자 개체가 인 도메인에 가입 되어 있지 않습니다.

**방법을** VM을 올바른 도메인에 추가 합니다. [Windows Server 가상 컴퓨터를 관리 되는 도메인에 가입](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)을 참조 하세요.

### <a name="error-nslookup-cannot-resolve-the-name"></a>오류: Nslookup에서 이름을 확인할 수 없습니다.

**원인:** 네트워크 문제 또는 중단.

**방법을** 네트워크 지원에 문의

### <a name="error-connectionfailedclientprotocolerror"></a>오류: ConnectionFailedClientProtocolError

**원인:** 사용자가 연결 하려고 하는 Vm은 도메인에 가입 되지 않습니다.

**방법을** 호스트 풀의 일부인 모든 Vm을 도메인 컨트롤러에 가입 시킵니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자가 연결 되어 있지만 아무것도 표시 되지 않습니다 (피드 없음).

사용자가 원격 데스크톱 클라이언트를 시작 하 고 인증할 수 있지만 웹 검색 피드에 아이콘이 표시 되지 않습니다.

다음 명령줄을 사용 하 여 문제를 보고 하는 사용자가 응용 프로그램 그룹에 할당 되었는지 확인 합니다.

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

사용자가 올바른 자격 증명으로 로그인 하 고 있는지 확인 합니다.

웹 클라이언트를 사용 하는 경우 캐시 된 자격 증명 문제가 없는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 발생 하는 문제를 해결 하려면 [테 넌 트 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱과 함께 PowerShell을 사용할 때 발생 하는 문제를 해결 하려면 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)을 참조 하세요.
- 미리 보기 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)을 참조 하세요.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)문제를 해결 합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.
