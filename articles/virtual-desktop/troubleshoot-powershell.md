---
title: Windows 가상 데스크톱 PowerShell-Azure
description: Windows 가상 데스크톱 환경을 설정할 때 PowerShell을 사용 하 여 문제를 해결 하는 방법입니다.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: ce19c670df5062a11bf86e9c383a322f9033818d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/30/2020
ms.locfileid: "82612013"
---
# <a name="windows-virtual-desktop-powershell"></a>Windows Virtual Desktop PowerShell

>[!IMPORTANT]
>이 콘텐츠는 Azure Resource Manager Windows 가상 데스크톱 개체를 사용 하 여 스프링 2020 업데이트에 적용 됩니다. Azure Resource Manager 개체 없이 Windows 가상 데스크톱 2019 릴리스를 사용 하는 경우 [이 문서](./virtual-desktop-fall-2019/troubleshoot-powershell-2019.md)를 참조 하세요.
>
> Windows 가상 데스크톱 스프링 2020 업데이트는 현재 공개 미리 보기로 제공 됩니다. 이 미리 보기 버전은 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에 사용 하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Windows 가상 데스크톱과 함께 PowerShell을 사용 하는 경우 오류 및 문제를 해결 하려면이 문서를 사용 합니다. 원격 데스크톱 서비스 PowerShell에 대 한 자세한 내용은 [Windows 가상 데스크톱 PowerShell](/powershell/module/windowsvirtualdesktop/)을 참조 하세요.

## <a name="provide-feedback"></a>피드백 제공

[Windows Virtual Desktop 기술 커뮤니티](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop)를 방문하여 제품 팀 및 활발하게 활동하는 커뮤니티 멤버들과 Windows Virtual Desktop 서비스에 대해 토론해 보세요.

## <a name="powershell-commands-used-during-windows-virtual-desktop-setup"></a>Windows 가상 데스크톱 설치 중에 사용 되는 PowerShell 명령

이 섹션에서는 Windows 가상 데스크톱을 설정 하는 동안 일반적으로 사용 되는 PowerShell 명령을 나열 하 고이를 사용 하는 동안 발생할 수 있는 문제를 해결 하는 방법을 제공 합니다.

### <a name="error-new-azroleassignment-the-provided-information-does-not-map-to-an-ad-object-id"></a>오류: AzRoleAssignment: 제공 된 정보가 AD 개체 ID에 매핑되지 않습니다.

```powershell
AzRoleAssignment -SignInName "admins@contoso.com" -RoleDefinitionName "Desktop Virtualization User" -ResourceName "0301HP-DAG" -ResourceGroupName 0301RG -ResourceType 'Microsoft.DesktopVirtualization/applicationGroups' 
```

**원인:** Windows 가상 데스크톱 환경에 연결 된 Azure Active Directory에서 *-SignInName* 매개 변수로 지정 된 사용자를 찾을 수 없습니다. 

**해결 방법:** 다음 사항을 확인 합니다.

- 사용자를 Azure Active Directory와 동기화 해야 합니다.
- 사용자는 B2B (B2C) 또는 B2B (기업 간) 상거래에 연결 되지 않아야 합니다.
- Windows 가상 데스크톱 환경은 올바른 Azure Active Directory 연결 되어야 합니다.

### <a name="error-new-azroleassignment-the-client-with-object-id-does-not-have-authorization-to-perform-action-over-scope-code-authorizationfailed"></a>오류: AzRoleAssignment: "개체 id의 클라이언트에 범위에 대 한 작업을 수행할 수 있는 권한이 없습니다 (코드: AuthorizationFailed)."

**원인 1:** 사용 중인 계정에 구독에 대 한 소유자 권한이 없습니다. 

**수정 1:** 소유자 권한이 있는 사용자는 역할 할당을 실행 해야 합니다. 또는 사용자를 응용 프로그램 그룹에 할당 하려면 사용자 액세스 관리자 역할에 사용자를 할당 해야 합니다.

**원인 2:** 사용 중인 계정에는 소유자 권한이 있지만 환경의 Azure Active Directory에 속하지 않거나 사용자가 있는 Azure Active Directory를 쿼리할 수 있는 권한이 없습니다.

**수정 2:** Active Directory 권한이 있는 사용자는 역할 할당을 실행 해야 합니다.

### <a name="error-new-azwvdhostpool----the-location-is-not-available-for-resource-type"></a>오류: AzWvdHostPool--리소스 종류에 대 한 위치를 사용할 수 없습니다.

```powershell
New-AzWvdHostPool_CreateExpanded: The provided location 'southeastasia' is not available for resource type 'Microsoft.DesktopVirtualization/hostpools'. List of available regions for the resource type is 'eastus,eastus2,westus,westus2,northcentralus,southcentralus,westcentralus,centralus'. 
```

원인: Windows 가상 데스크톱은 특정 위치에 서비스 메타 데이터를 저장할 호스트 풀, 응용 프로그램 그룹 및 작업 영역의 위치 선택을 지원 합니다. 옵션은이 기능을 사용할 수 있는 위치로 제한 됩니다. 이 오류는 선택한 위치에서 기능을 사용할 수 없음을 의미 합니다.

Fix: 오류 메시지에서 지원 되는 지역 목록이 게시 됩니다. 지원 되는 지역 중 하나를 대신 사용 하세요.

### <a name="error-new-azwvdapplicationgroup-must-be-in-same-location-as-host-pool"></a>오류: AzWvdApplicationGroup는 호스트 풀과 동일한 위치에 있어야 합니다.

```powershell
New-AzWvdApplicationGroup_CreateExpanded: ActivityId: e5fe6c1d-5f2c-4db9-817d-e423b8b7d168 Error: ApplicationGroup must be in same location as associated HostPool
```

**원인:** 위치가 일치 하지 않습니다. 모든 호스트 풀, 응용 프로그램 그룹 및 작업 영역에는 서비스 메타 데이터를 저장할 위치가 있습니다. 서로 연결 된 개체는 모두 동일한 위치에 있어야 합니다. 예를 들어 호스트 풀이에 `eastus`있으면에서 `eastus`응용 프로그램 그룹도 만들어야 합니다. 이러한 응용 프로그램 그룹을에 등록 하는 작업 영역을 만드는 경우 해당 작업 영역에 `eastus` 도 있어야 합니다.

**해결 방법:** 호스트 풀이 만들어진 위치를 검색 한 다음, 만든 응용 프로그램 그룹을 동일한 위치에 할당 합니다.

## <a name="next-steps"></a>다음 단계

- Windows 가상 데스크톱 및 에스컬레이션 트랙 문제 해결에 대 한 개요는 [문제 해결 개요, 사용자 의견 및 지원](troubleshoot-set-up-overview.md)을 참조 하세요.
- Windows 가상 데스크톱 환경 및 호스트 풀을 설정 하는 동안 문제를 해결 하려면 [환경 및 호스트 풀 만들기](troubleshoot-set-up-issues.md)를 참조 하세요.
- Windows 가상 데스크톱에서 VM (가상 컴퓨터)을 구성 하는 동안 발생 하는 문제를 해결 하려면 [세션 호스트 가상 컴퓨터 구성](troubleshoot-vm-configuration.md)을 참조 하세요.
- Windows 가상 데스크톱 클라이언트 연결 문제를 해결 하려면 [Windows 가상 데스크톱 서비스 연결](troubleshoot-service-connection.md)을 참조 하세요.
- 원격 데스크톱 클라이언트와 관련 된 문제를 해결 하려면 [원격 데스크톱 클라이언트 문제 해결](troubleshoot-client.md) 을 참조 하세요.
- 서비스에 대 한 자세한 내용은 [Windows 가상 데스크톱 환경](environment-setup.md)을 참조 하세요.
- 감사 작업에 대해 알아보려면 [리소스 관리자로 작업 감사](../azure-resource-manager/management/view-activity-logs.md)를 참조하세요.
- 배포 중 오류를 확인하는 작업에 대해 알아보려면 [배포 작업 보기](../azure-resource-manager/templates/deployment-history.md)를 참조하세요.
