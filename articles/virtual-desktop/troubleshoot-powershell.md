---
title: 윈도우 가상 데스크톱 파워 쉘 - Azure
description: Windows 가상 데스크톱 테넌트 환경을 설정할 때 PowerShell문제를 해결하는 방법.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3fb5436c2b5c30c5336385792d0597bdcea2b538
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127475"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

이 문서를 사용하여 Windows 가상 데스크톱에서 PowerShell을 사용할 때 오류 및 문제를 해결합니다. 원격 데스크톱 서비스 PowerShell에 대한 자세한 내용은 [Windows 가상 데스크톱 Powershell](/powershell/module/windowsvirtualdesktop/)을 참조하십시오.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 가상 데스크톱 설정 중에 사용되는 PowerShell 명령

이 섹션에서는 Windows 가상 데스크톱을 설정하는 동안 일반적으로 사용되는 PowerShell 명령을 나열하고 이를 사용하는 동안 발생할 수 있는 문제를 해결하는 방법을 제공합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>오류: RdsAppGroupUser 추가 명령 -- 지정된 사용자 PrincipalName이 지정된 호스트 풀의 RemoteApp 앱 그룹에 이미 할당되었습니다.

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**원인:** 사용된 사용자 이름이 이미 다른 유형의 앱 그룹에 할당되었습니다. 동일한 세션 호스트 풀에서 원격 데스크톱 및 원격 앱 그룹에 사용자를 할당할 수 없습니다.

**수정 사항:** 사용자가 원격 앱과 원격 데스크톱을 모두 필요로 하는 경우 다른 호스트 풀을 만들거나 원격 데스크톱에 대한 사용자 액세스 권한을 부여하여 세션 호스트 VM에서 모든 응용 프로그램을 사용할 수 있습니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>오류: RdsAppGroupUser 추가 명령 -- 지정된 UserPrincipalName 원격 데스크톱 테넌트와 연결 된 Azure 활성 디렉터리에 없는

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName "Desktop Application Group" -UserPrincipalName <UserPrincipalName>
```

**원인:** -UserPrincipalName에 의해 지정된 사용자는 Windows 가상 데스크톱 테넌트에 연결된 Azure Active Directory에서 찾을 수 없습니다.

**수정 사항:** 다음 목록에서 항목을 확인합니다.

- 사용자가 Azure Active Directory에 동기화됩니다.
- 사용자는 B2C(기업 간) 상거래또는 기업간 상거래에 연결되지 않습니다.
- Windows 가상 데스크톱 테넌트는 Azure Active Directory를 수정하는 데 연결됩니다.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities -- 사용자가 관리 서비스를 쿼리할 권한이 없습니다.

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**원인:** -TenantName 매개 변수

**수정 사항:** 문제 Get-RdsDiagnostic활동 -TenantName \<이름>.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities -- 사용자가 관리 서비스를 쿼리할 권한이 없습니다.

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**원인:** - 배포 스위치를 사용.

**해결 방법:** -배포 스위치는 배포 관리자만 사용할 수 있습니다. 이러한 관리자는 일반적으로 원격 데스크톱 서비스/Windows 가상 데스크톱 팀의 구성원입니다. -Deploy 스위치를 -TenantName \<테넌트 이름> 바꿉니다.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>오류: 새 RdsRoleAssignment -- 사용자가 관리 서비스를 쿼리할 권한이 없습니다.

**원인 1:** 사용 중인 계정에테넌트에 대한 원격 데스크톱 서비스 소유자 권한이 없습니다.

**수정 1:** 원격 데스크톱 서비스 소유자 권한이 있는 사용자는 역할 할당을 실행해야 합니다.

**원인 2:** 사용 중인 계정에는 원격 데스크톱 서비스 소유자 권한이 있지만 테넌트의 Azure Active Directory의 일부가 아니거나 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**수정 2:** Active Directory 권한이 있는 사용자는 역할 할당을 실행해야 합니다.

>[!Note]
>New-RdsRoleAssignment Azure Active Directory(AD)에 없는 사용자에게 권한을 부여할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대한 개요는 [문제 해결 개요, 피드백 및 지원을](troubleshoot-set-up-overview.md)참조하십시오.
- Windows 가상 데스크톱 환경에서 테넌트 및 호스트 풀을 만드는 동안 문제를 해결하려면 [테넌트 및 호스트 풀 만들기를](troubleshoot-set-up-issues.md)참조하십시오.
- Windows 가상 데스크톱에서 가상 시스템(VM)을 구성하는 동안 문제를 해결하려면 [세션 호스트 가상 시스템 구성을](troubleshoot-vm-configuration.md)참조하십시오.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결하려면 [Windows 가상 데스크톱 서비스 연결을](troubleshoot-service-connection.md)참조하십시오.
- 원격 데스크톱 클라이언트의 문제 해결을 위해 [원격 데스크톱 클라이언트 문제 해결을](troubleshoot-client.md) 참조하세요.
- 서비스에 대한 자세한 내용은 [Windows 가상 데스크톱 환경을](environment-setup.md)참조하십시오.
- 문제 해결 자습서를 진행하려면 [자습서: 리소스 관리자 템플릿 배포 문제를 해결 합니다.](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
