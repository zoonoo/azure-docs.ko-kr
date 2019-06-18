---
title: Windows 가상 데스크톱 PowerShell-Azure
description: Windows 가상 데스크톱 테 넌 트 환경을 설정 하는 경우 PowerShell 사용 하 여 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: ad32f7ff883812830dbcf2ed900c4034bd90abfc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/13/2019
ms.locfileid: "64927509"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

이 문서를 사용 하 여 Windows 가상 데스크톱을 사용 하 여 PowerShell을 사용 하는 경우 오류 및 문제 해결. 원격 데스크톱 서비스 PowerShell에 대 한 자세한 내용은 참조 하세요. [Windows 가상 데스크톱 Powershell](https://docs.microsoft.com/powershell/module/windowsvirtualdesktop/)합니다.

## <a name="provide-feedback"></a>피드백 제공

Windows Virtual Desktop이 미리 보기로 제공되는 기간에는 지원 사례를 접수하지 않습니다. [Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 가상 데스크톱 설치 하는 동안 사용 되는 PowerShell 명령

이 섹션에서는 Windows 가상 데스크톱을 설정 하는 동안 일반적으로 사용 되 고 사용 하는 동안 발생할 수 있는 문제를 해결 하는 방법을 제공 하는 PowerShell 명령을 나열 합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-is-already-assigned-to-a-remoteapp-app-group-in-the-specified-host-pool"></a>오류: 추가 RdsAppGroupUser 명령-지정 된 UserPrincipalName가 이미 지정된 된 호스트 풀에서 RemoteApp 앱 그룹에 할당

```Powershell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName 'Desktop Application Group' -UserPrincipalName <UserName>
```

**원인:** 다른 종류의 앱 그룹에 사용 되는 사용자 이름은 이미 할당 되었습니다. 동일한 세션 호스트 풀 아래에 있는 원격 데스크톱 및 원격 앱 그룹 모두에 사용자를 할당할 수 없습니다.

**해결 방법:** 원격 앱과 원격 데스크톱 사용자에 게 필요한 경우 다른 호스트 풀 만들기 또는 원격 데스크톱 세션 호스트 VM에서 응용 프로그램의 사용을 허용 하는에 대 한 사용자 액세스 권한을 부여 합니다.

### <a name="error-add-rdsappgroupuser-command----the-specified-userprincipalname-doesnt-exist-in-the-azure-active-directory-associated-with-the-remote-desktop-tenant"></a>오류: 원격 데스크톱 테 넌 트와 연결 된 Azure Active Directory에 없으면 추가 RdsAppGroupUser 명령-지정 된 UserPrincipalName

```PowerShell
Add-RdsAppGroupUser -TenantName <TenantName> -HostPoolName <HostPoolName> -AppGroupName “Desktop Application Group” -UserPrincipalName <UserPrincipalName>
```

**원인:** -UserPrincipalName 하 여 지정 된 사용자는 Windows 가상 데스크톱 테 넌 트에 연결 된 Azure Active Directory에서 찾을 수 없습니다.

**해결 방법:** 다음 목록에 항목을 확인 합니다.

- 사용자는 Azure Active Directory에 동기화 됩니다.
- 사용자는 비즈니스 b2c 또는 B2B 상거래 비즈니스-에 연결 되지 않습니다.
- Windows 가상 데스크톱 테 넌 트 올바른 Azure Active Directory에 연결 됩니다.

### <a name="error-get-rdsdiagnosticactivities----user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities-관리 서비스를 쿼리할 권한이 없는 사용자

```PowerShell
Get-RdsDiagnosticActivities -ActivityId <ActivityId>
```

**원인:** -TenantName 매개 변수

**해결 방법:** Get-RdsDiagnosticActivities-TenantName를 사용 하 여 발급 <TenantName>합니다.

### <a name="error-get-rdsdiagnosticactivities----the-user-isnt-authorized-to-query-the-management-service"></a>오류: Get-RdsDiagnosticActivities-관리 서비스를 쿼리할 권한이 없는 사용자는

```PowerShell
Get-RdsDiagnosticActivities -Deployment -username <username>
```

**원인:** 사용 하 여 배포 스위치입니다.

**해결 방법:** -배포 관리자에 의해서만 배포 스위치를 사용할 수 있습니다. 이러한 관리자는 일반적으로 원격 데스크톱 서비스/Windows 가상 데스크톱 팀의 멤버입니다. 대체-TenantName를 사용 하 여 배포 스위치- <TenantName>합니다.

### <a name="error-new-rdsroleassignment----the-user-isnt-authorized-to-query-the-management-service"></a>오류: 새-RdsRoleAssignment-관리 서비스를 쿼리할 권한이 없는 사용자는

**원인 1:** 테 넌 트에서 사용 되는 계정에 원격 데스크톱 서비스 소유자 권한이 없습니다.

**1의 해결 방법:** 원격 데스크톱 서비스 소유자 권한이 있는 사용자를 역할 할당을 실행 해야 합니다.

**원인 2:** 사용 중인 계정에 원격 데스크톱 서비스 소유자 권한이 있지만 테 넌 트의 Azure Active Directory의 일부가 아닌 또는 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**2 해결 방법:** Active Directory 권한 가진 사용자는 역할 할당을 실행 해야 합니다.

>[!Note]
>RdsRoleAssignment 새에서 Azure Active Directory (AD) 존재 하지 않는 사용자에 게 권한을 부여할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 추적 문제 해결에 대 한 개요를 참조 하세요 [문제 해결 개요, 피드백 및 지원](troubleshoot-set-up-overview.md)합니다.
- Windows 가상 데스크톱 환경에서 테 넌 트 및 호스트 풀을 만드는 동안 문제를 해결 하려면 참조 [테 넌 트 및 호스트 풀 생성](troubleshoot-set-up-issues.md)합니다.
- Windows 가상 데스크톱의 가상 머신 (VM)를 구성 하는 동안 문제를 해결 하려면 참조 [세션 호스트 가상 머신 구성을](troubleshoot-vm-configuration.md)합니다.
- Windows 가상 데스크톱 클라이언트 연결을 사용 하 여 문제를 해결 하려면 참조 [원격 데스크톱 클라이언트 연결](troubleshoot-client-connection.md)합니다.
- 미리 보기 서비스에 대 한 자세한 내용은 참조 하세요 [Windows 데스크톱 미리 보기 환경](https://docs.microsoft.com/azure/virtual-desktop/environment-setup)합니다.
- 문제 해결 자습서를 진행하려면 [자습서: Resource Manager 템플릿 배포 문제 해결](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)합니다.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations)를 참조하세요.