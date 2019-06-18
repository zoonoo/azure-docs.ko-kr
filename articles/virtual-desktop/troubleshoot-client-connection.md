---
title: Windows Virtual Desktop-Azure에서에서 원격 데스크톱 클라이언트 연결
description: Windows 가상 데스크톱 테 넌 트 환경에서 클라이언트 연결을 설정할 때 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: f88dee579e44a01dc1a7404ef6a670de34063552
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "65833564"
---
# <a name="remote-desktop-client-connections"></a>원격 데스크톱 클라이언트 연결

이 문서에서는 Windows 가상 데스크톱 클라이언트 연결을 사용 하 여 문제를 해결 하려면 사용 합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="you-cant-open-a-web-client"></a>웹 클라이언트를 열 수 없습니다.

다른 웹 사이트를 열어 인터넷 연결이 있는지 확인 합니다. 예를 들어 [www.Bing.com](https://www.bing.com)합니다.

사용 하 여 **nslookup** DNS FQDN을 확인할 수를 확인 하려면:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Windows 7 또는 Windows 10 및 웹 클라이언트를 열 수는 경우 참조 확인에 대 한 원격 데스크톱 클라이언트와 같은 다른 클라이언트 연결을 시도 합니다.

### <a name="error-opening-another-site-fails"></a>오류: 다른 사이트 실패 열기

**원인:** 네트워크 문제 및/또는 중단 합니다.

**해결 방법:** 네트워크 지원에 문의 합니다.

### <a name="error-nslookup-cannot-resolve-the-name"></a>오류: Nslookup 이름을 확인할 수 없습니다.

**원인:** 네트워크 문제 및/또는 중단 합니다.

**해결 방법:** 네트워크 지원에 문의

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>오류: 연결할 수 없는 경우 다른 클라이언트가 연결할 수 있지만

**원인:** 브라우저 예상 및 중지 작업으로 동작 하지 않습니다.

**해결 방법:** 브라우저의 문제를 해결 하려면 다음이 지침을 따릅니다.

1. 브라우저를 다시 시작 합니다.
2. 일반 브라우저 쿠키입니다. 참조 [Internet Explorer에서 쿠키 파일을 삭제 하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)합니다.
3. 브라우저 캐시를 지웁니다. 참조 [브라우저에 대 한 브라우저 캐시를 지우려면](https://binged.it/2RKyfdU)합니다.
4. 비공개 모드로 브라우저를 엽니다.

## <a name="web-client-stops-responding-or-disconnects"></a>웹 클라이언트 응답 하지 않거나 연결 해제

다른 브라우저 또는 클라이언트를 사용 하 여 연결을 시도 합니다.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>오류: 다른 브라우저 및 클라이언트도 오작동 하거나 열리지

**원인:** 네트워크 및/또는 작업 시스템 문제 또는 중단

**해결 방법:** 팀 지원에 문의 하세요입니다.

## <a name="web-client-keeps-prompting-for-credentials"></a>웹 클라이언트 자격 증명을 물을 유지합니다

웹 클라이언트 자격 증명을 묻는 유지, 하는 경우 다음이 지침을 따릅니다.

1. 웹 클라이언트 URL이 올바른지 확인 합니다.
2. URL에 연결 된 Windows 가상 데스크톱 환경에 대 한 자격 증명이 있는지 확인 합니다.
3. 일반 브라우저 쿠키입니다. 참조 [Internet Explorer에서 쿠키 파일을 삭제 하는 방법을](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer)합니다.
4. 브라우저 캐시를 지웁니다. 참조 [브라우저에 대 한 브라우저 캐시를 지우려면](https://binged.it/2RKyfdU)합니다.
5. 비공개 모드로 브라우저를 엽니다.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Windows 7 또는 Windows 10 용 원격 데스크톱 클라이언트에 응답 하지 않거나 열 수 없습니다.

대역의 (OOB) 클라이언트 레지스트리를 정리 하려면 다음 PowerShell cmdlet을 사용 합니다.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

이동할 **%AppData%\RdClientRadc** 및 모든 콘텐츠를 삭제 합니다.

Windows 7 및 Windows 10에 대 한 원격 데스크톱 클라이언트를 제거한 설정 합니다.

## <a name="troubleshooting-end-user-connectivity"></a>최종 사용자 연결 문제 해결

경우에 따라 사용자가 해당 피드 및 로컬 리소스에 액세스할 수 있지만 아직 구성, 가용성 또는 원격 리소스에 액세스 하지 못하도록 하는 성능 문제. 이러한 경우 사용자 메시지가 다음과 유사한 가져옵니다.

![원격 데스크톱 연결 오류 메시지입니다.](media/eb76b666808bddb611448dfb621152ce.png)

![게이트웨이 오류 메시지에 연결할 수 없습니다.](media/a8fbb9910d4672147335550affe58481.png)

클라이언트 연결 오류 코드는 이러한 일반적인 문제 해결 지침을 따릅니다.

1. 사용자 이름 및 문제가 발생 하는 시간을 확인 합니다.
2. 오픈 **PowerShell** 문제가 보고 된 위치는 Windows 가상 데스크톱 테 넌 트에 연결 하 고 있습니다.
3. 사용 하 여 올바른 테 넌 트 연결 확인 **Get RdsTenant 합니다.**
4. 사용 하 여 **Get RdsHostPool** 하 고 **Get RdsSessionHost** cmdlet는 문제 해결에서 수행 되는 올바른 호스트 풀을 확인 합니다.
5. 지정 된 기간에 대 한 형식 연결의 모든 실패 한 작업 목록을 가져오려면 다음 명령을 실행 합니다.

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. 사용 하는 **ActivityId** 이전 cmdlet 출력에서 아래 명령을 실행 합니다.

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. 명령은 아래에 표시 된 출력과 비슷한 출력을 생성 합니다. 사용 하 여 **ErrorCodeSymbolic** 하 고 **ErrorMessage** 근본 원인을 해결 합니다.

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

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>오류: O_ADD_USER_TO_GROUP_FAILED 사용자를 추가 하지 못했습니다 / 그룹에 ≤username≥ = = 원격 데스크톱 사용자입니다. 원인: Win32.ERROR_NO_SUCH_MEMBER

**원인:** VM에 사용자 개체가 있는 도메인에 조인 되지 합니다.

**해결 방법:** 올바른 도메인에 VM을 추가 합니다. 참조 [Windows Server 가상 머신을 관리 되는 도메인에 가입](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal)합니다.

### <a name="error-nslookup-cannot-resolve-the-name"></a>오류: Nslookup 이름을 확인할 수 없습니다.

**원인:** 네트워크 문제 또는 중단 합니다.

**해결 방법:** 네트워크 지원에 문의

### <a name="error-connectionfailedclientprotocolerror"></a>오류: ConnectionFailedClientProtocolError

**원인:** 에 연결 하는 사용자가 시도 Vm은 도메인에 가입 되지 않습니다.

**해결 방법:** 도메인 컨트롤러에 호스트 풀의 일부인 모든 Vm을 조인 합니다.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>사용자 연결 하지만 아무것도 표시 되지 않습니다 (용지 공급)

하지만 사용자 원격 데스크톱 클라이언트를 시작할 수 있으며 사용자 피드 웹 검색에 모든 아이콘을 표시 하지 않습니다 인증을 수 있습니다.

문제 보고 사용자가 명령줄을 사용 하 여 응용 프로그램 그룹에 할당 되어 있는지 확인 합니다.

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

올바른 자격 증명을 사용 하 여 사용자가 로그를 확인 합니다.

웹 클라이언트를 사용 하는 경우 캐시 된 자격 증명 문제가 있는지 확인 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 추적 문제 해결에 대 한 개요를 참조 하세요 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)합니다.
- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 문제를 해결 하려면 참조 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다.
- Windows 가상 데스크톱의 가상 머신 (VM)를 구성 하는 동안 문제를 해결 하려면 참조 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다.
- Windows 가상 데스크톱을 사용 하 여 PowerShell을 사용 하는 경우 문제를 해결 하려면 참조 [Windows 가상 데스크톱 PowerShell](troubleshoot-powershell.md)합니다.
- 미리 보기 서비스에 대 한 자세한 내용은 참조 하세요 [Windows 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?)합니다.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.
