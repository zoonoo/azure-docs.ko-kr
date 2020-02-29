---
title: PIM의 Azure AD 역할에 대 한 PowerShell-Azure AD | Microsoft Docs
description: Azure AD Privileged Identity Management (PIM)에서 PowerShell cmdlet을 사용 하 여 Azure AD 역할을 관리 합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/26/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: efb85b4a54b8f61e44f1f8bc75f893f93a0feb8a
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164959"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대 한 PowerShell

이 문서에는 Azure Active Directory (Azure AD) PowerShell cmdlet을 사용 하 여 PIM (Privileged Identity Management)에서 Azure AD 역할을 관리 하는 지침이 포함 되어 있습니다. 또한 Azure AD PowerShell 모듈을 사용하여 설정하는 방법을 설명합니다.

> [!Note]
> 공식 PowerShell은 새 버전의 Azure AD Privileged Identity Management에 있는 경우에만 지원 됩니다. Privileged Identity Management로 이동 하 여 빠른 시작 블레이드에 다음 배너가 있는지 확인 하세요.
> [![Privileged Identity Management 버전을 확인 합니다](media/pim-how-to-add-role-to-user/pim-new-version.png "Azure AD > Privileged Identity Management를 선택 합니다.")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) . 이 배너가 없으면 다음 몇 주 동안이 업데이트 된 환경을 롤아웃 하는 중입니다.
> Privileged Identity Management PowerShell cmdlet은 Azure AD 미리 보기 모듈을 통해 지원 됩니다. 다른 모듈을 사용 중이 고 해당 모듈이 이제 오류 메시지를 반환 하는 경우이 새 모듈을 사용 하 여 시작 하세요. 다른 모듈을 기반으로 구축 된 프로덕션 시스템이 있는 경우 pim_preview@microsoft.com에 문의 하세요.

## <a name="installation-and-setup"></a>설치 및 설정

1. Azure AD 미리 보기 모듈 설치

        Install-module AzureADPreview

1. 계속 하기 전에 필요한 역할 권한이 있는지 확인 하십시오. 역할 할당 또는 역할 업데이트 설정을 제공 하는 등의 관리 작업을 수행 하려는 경우 전역 관리자 또는 권한 있는 역할 관리자 역할이 있는지 확인 합니다. 사용자 고유의 할당을 활성화 하려는 경우에는 기본 사용자 권한을 초과 하는 사용 권한이 필요 하지 않습니다.

1. Azure AD에 연결 합니다.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. **Azure Active Directory** > **속성** > **디렉터리 id**로 이동 하 여 테 넌 트 ID를 찾습니다. Cmdlet 섹션에서 resourceId를 제공 해야 할 때마다이 ID를 사용 합니다.

    ![Azure AD 조직에 대 한 속성에서 테 넌 트 ID 찾기](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 다음 섹션에서는를 시작 하 고 실행 하는 데 도움이 되는 간단한 예제를 제공 합니다. https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management에서 다음 cmdlet에 대 한 자세한 설명서를 찾을 수 있습니다. 그러나 providerID 매개 변수에서 "azureResources"를 "aadRoles"로 바꾸어야 합니다. 또한 Azure AD 조직에 대 한 테 넌 트 ID를 resourceId 매개 변수로 사용 해야 합니다.

## <a name="retrieving-role-definitions"></a>역할 정의 검색

다음 cmdlet을 사용 하 여 Azure AD 조직 (테 넌 트)에서 모든 기본 제공 및 사용자 지정 Azure AD 역할을 가져옵니다. 이 중요 단계는 역할 이름과 roleDefinitionId 간의 매핑을 제공 합니다. RoleDefinitionId은 특정 역할을 참조 하기 위해 이러한 cmdlet 전체에서 사용 됩니다.

RoleDefinitionId는 Azure AD 조직과 관련이 있으며 역할 관리 API에서 반환 하는 roleDefinitionId와 다릅니다.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

Result:

![Azure AD 조직에 대 한 모든 역할을 가져옵니다.](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>역할 할당 검색

Azure AD 조직에서 모든 역할 할당을 검색 하려면 다음 cmdlet을 사용 합니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

특정 사용자에 대 한 모든 역할 할당을 검색 하려면 다음 cmdlet을 사용 합니다. 이 목록을 Azure AD 포털의 "내 역할"이 라고도 합니다. 여기에서 유일한 차이점은 주체 ID에 대 한 필터를 추가 했기 때문입니다. 이 컨텍스트의 주체 ID는 사용자 ID 또는 그룹 ID입니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

특정 역할에 대 한 모든 역할 할당을 검색 하려면 다음 cmdlet을 사용 합니다. 여기서 roleDefinitionId는 이전 cmdlet에서 반환 되는 ID입니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

Cmdlet은 아래에 표시 된 역할 할당 개체의 목록을 생성 합니다. 주체 ID는 역할이 할당 된 사용자의 사용자 ID입니다. 할당 상태는 활성 또는 적격 중 하나일 수 있습니다. 사용자가 활성 상태이 고 LinkedEligibleRoleAssignmentId 필드에 ID가 있는 경우이는 역할이 현재 활성화 되어 있음을 의미 합니다.

Result:

![Azure AD 조직에 대 한 모든 역할 할당 검색](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>역할 할당

적격 할당을 만들려면 다음 cmdlet을 사용 합니다.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

할당의 시작 및 종료 시간을 정의 하는 일정은 다음 예제와 같이 만들 수 있는 개체입니다.

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"

## <a name="activate-a-role-assignment"></a>역할 할당 활성화

다음 cmdlet을 사용 하 여 적격 할당을 활성화 합니다.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

이 cmdlet은 역할 할당을 만들기 위한 cmdlet과 거의 동일 합니다. Cmdlet 간의 주요 차이점은 – Type 매개 변수에서 활성화는 "adminAdd" 대신 "userAdd"입니다. 다른 차이점은 – AssignmentState 매개 변수는 "적격" 대신 "Active"입니다.

> [!Note]
> PowerShell을 통한 역할 활성화에는 두 가지 제한 시나리오가 있습니다.
> 1. 역할 설정에서 티켓 시스템/티켓 번호를 요구 하는 경우 매개 변수로 제공할 수 있는 방법이 없습니다. 따라서 Azure Portal을 초과 하 여 역할을 활성화할 수 없습니다. 이 기능은 다음 몇 달 동안 PowerShell로 롤아웃 됩니다.
> 1. 역할 활성화에 multi-factor authentication이 필요한 경우 현재 PowerShell에서 사용자가 자신의 역할을 활성화할 때 사용자를 확인할 수 있는 방법은 없습니다. 대신, 사용자는 엔지니어 중 한 곳의 [이 블로그 게시물](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) 에 따라 Azure AD에 연결할 때 MFA 챌린지를 트리거해야 합니다. PIM 용 응용 프로그램을 개발 하는 경우에는 사용자가 "MfaRule" 오류를 받은 후 사용자를 시도 하 고 모듈에 다시 연결 하는 것이 가능한 한 가지 구현입니다.

## <a name="retrieving-and-updating-role-settings"></a>역할 설정 검색 및 업데이트

다음 cmdlet을 사용 하 여 Azure AD 조직의 모든 역할 설정을 가져올 수 있습니다.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

이 설정에는 네 가지 주요 개체가 있습니다. 이러한 개체 중 세 개만 현재 PIM에서 사용 됩니다. UserMemberSettings는 활성화 설정이 고 적격 할당에 대 한 할당 설정 AdminEligibleSettings, AdminmemberSettings는 활성 할당에 대 한 할당 설정입니다.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

역할 설정을 업데이트 하려면 먼저 다음과 같이 설정 개체를 정의 해야 합니다.

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

그런 다음 아래와 같이 특정 역할에 대 한 개체 중 하나에 설정을 적용할 수 있습니다. 이 ID는 목록 역할 설정 cmdlet의 결과에서 검색할 수 있는 역할 설정 ID입니다.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId ‘aadRoles’ -Id ‘ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId ‘3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId ‘2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../users-groups-roles/directory-assign-admin-roles.md)
