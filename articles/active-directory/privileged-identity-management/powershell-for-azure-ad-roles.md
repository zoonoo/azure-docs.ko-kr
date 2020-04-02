---
title: PIM - Azure AD에서 Azure AD 역할에 대한 PowerShell | 마이크로 소프트 문서
description: Azure AD 권한 ID 관리(PIM)에서 PowerShell cmdlet을 사용하여 Azure AD 역할을 관리합니다.
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
ms.openlocfilehash: 5fa241a261b8dcb21dd39b5dacacac9aa4889304
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/01/2020
ms.locfileid: "80519661"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>권한 있는 ID 관리에서 Azure AD 역할에 대한 PowerShell

이 문서에는 AZURE Active Directory(Azure AD) PowerShell cmdlet을 사용하여 PIM(권한 있는 ID 관리)에서 Azure AD 역할을 관리하는 방법에 대한 지침이 포함되어 있습니다. 또한 Azure AD PowerShell 모듈을 사용하여 설정하는 방법을 설명합니다.

> [!Note]
> 공식 PowerShell은 Azure AD 권한 ID 관리의 새 버전에 있는 경우에만 지원됩니다. 권한 있는 ID 관리로 이동하여 빠른 시작 블레이드에 다음 배너가 있는지 확인하십시오.
> [![권한이 있는 ID 관리 버전 확인](media/pim-how-to-add-role-to-user/pim-new-version.png "Azure AD > 권한 있는 ID 관리 선택")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) 이 배너가 없는 경우, 현재 향후 몇 주 동안 이 업데이트된 환경을 롤아웃하는 중이기 때문에 기다려 주십시오.
> 권한 있는 ID 관리 PowerShell cmdletAzure AD 미리 보기 모듈을 통해 지원됩니다. 다른 모듈을 사용하고 있고 해당 모듈이 이제 오류 메시지를 반환하는 경우 이 새 모듈을 사용하기 시작하십시오. 다른 모듈 위에 생산 시스템이 내장되어 있는 경우,pim_preview@microsoft.com

## <a name="installation-and-setup"></a>설치 및 설정

1. Azure AD 미리 보기 모듈 설치

        Install-module AzureADPreview

1. 계속하기 전에 필요한 역할 사용 권한이 있는지 확인합니다. 역할 할당 제공 또는 역할 설정 업데이트와 같은 관리 작업을 수행하려는 경우 글로벌 관리자 또는 권한 있는 역할 관리자 역할이 있는지 확인합니다. 사용자 고유의 할당을 활성화하려는 경우 기본 사용자 사용 권한을 초과하는 권한이 필요하지 않습니다.

1. Azure AD에 연결합니다.

        $AzureAdCred = Get-Credential  
        Connect-AzureAD -Credential $AzureAdCred

1. **Azure Active 디렉터리** > **속성** > ID로 이동하여 테넌트 ID를 찾습니다.**Directory ID** cmdlet 섹션에서 resourceId를 제공해야 할 때마다 이 ID를 사용합니다.

    ![Azure AD 조직의 속성에서 테넌트 ID 찾기](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 다음 섹션은 시작하고 실행하는 데 도움이 되는 간단한 예제입니다. 다음 cmdlet에 대한 자세한 설명서를 찾을 수 있습니다. https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management 그러나 providerID 매개 변수의 "azureResources"를 "aadRoles"로 바꿔야 합니다. 또한 Azure AD 조직에 대한 테넌트 ID를 resourceId 매개 변수로 사용해야 합니다.

## <a name="retrieving-role-definitions"></a>역할 정의 검색

다음 cmdlet을 사용하여 Azure AD 조직(테넌트)에서 모든 기본 제공 및 사용자 지정 Azure AD 역할을 가져옵니다. 이 중요한 단계는 역할 이름과 역할 DefinitionId 사이의 매핑을 제공합니다. 역할DefinitionId는 특정 역할을 참조하기 위해 이러한 cmdlet 전체에서 사용됩니다.

역할DefinitionIdAzure AD 조직에 특정 하 고 역할 관리 API에 의해 반환 되는 역할DefinitionId와 다릅니다.

    Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26

결과:

![Azure AD 조직에 대한 모든 역할 받기](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>역할 할당 검색

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 할당을 검색합니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"

다음 cmdlet을 사용하여 특정 사용자에 대한 모든 역할 할당을 검색합니다. 이 목록은 Azure AD 포털에서 "내 역할"이라고도 합니다. 여기서 유일한 차이점은 제목 ID에 대한 필터를 추가했다는 것입니다. 이 컨텍스트의 제목 ID는 사용자 ID 또는 그룹 ID입니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 

다음 cmdlet을 사용하여 특정 역할에 대한 모든 역할 할당을 검색합니다. 역할DefinitionId 여기 이전 cmdlet에 의해 반환 되는 ID입니다.

    Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"

cmdlet은 아래 와 같은 역할 할당 개체 목록을 생성합니다. 주체 ID는 역할이 할당된 사용자의 사용자 ID입니다. 할당 상태가 활성 상태이거나 적격일 수 있습니다. 사용자가 활성 상태이고 LinkedActiveRoleAssignmentId 필드에 ID가 있는 경우 이는 역할이 현재 활성화되어 있음을 의미합니다.

결과:

![Azure AD 조직에 대한 모든 역할 할당 검색](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>역할 할당

다음 cmdlet을 사용하여 적격 할당을 만듭니다.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 

할당의 시작 및 종료 시간을 정의하는 일정은 다음 예제와 같이 만들 수 있는 개체입니다.

    $schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
    $schedule.Type = "Once"
    $schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
    $schedule.endDateTime = "2020-07-25T20:49:11.770Z"
> [!Note]
> endDateTime 값이 null로 설정된 경우 영구 할당을 나타냅니다.

## <a name="activate-a-role-assignment"></a>역할 할당 활성화

다음 cmdlet을 사용하여 적격 할당을 활성화합니다.

    Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas" 

이 cmdlet은 역할 할당을 만들기 위한 cmdlet과 거의 동일합니다. cmdlet 간의 주요 차이점은 -Type 매개 변수의 경우 활성화는 "adminAdd" 대신 "userAdd"라는 것입니다. 다른 차이점은 -AssignmentState 매개 변수가 "적격"이 아닌 "활성"이라는 것입니다.

> [!Note]
> PowerShell을 통해 역할 활성화에 대한 두 가지 제한 시나리오가 있습니다.
> 1. 역할 설정에서 티켓 시스템/티켓 번호가 필요한 경우 매개 변수로 제공할 수 있는 방법이 없습니다. 따라서 Azure 포털 이외의 역할을 활성화할 수 없습니다. 이 기능은 앞으로 몇 개월 동안 PowerShell에 출시될 예정입니다.
> 1. 역할 활성화를 위해 다단계 인증이 필요한 경우 현재 PowerShell이 해당 역할을 활성화할 때 사용자에게 이의를 제기할 수 있는 방법이 없습니다. 대신, 사용자는 엔지니어 중 한 명이 [이 블로그 게시물을](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html) 따라 Azure AD에 연결할 때 MFA 챌린지를 트리거해야 합니다. PIM용 앱을 개발하는 경우 사용자가 "MfaRule" 오류가 발생한 후 사용자에게 이의를 제기하고 모듈에 다시 연결하는 것이 가능한 구현입니다.

## <a name="retrieving-and-updating-role-settings"></a>역할 설정 검색 및 업데이트

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 설정을 가져옵니다.

    Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'" 

설정에는 네 가지 주요 개체가 있습니다. 현재 PIM에서 이러한 개체 중 3개만 사용됩니다. UserMemberSettings은 활성화 설정, Admin정규설정은 적격할당에 대한 할당 설정, AdminmemberSettings는 활성 할당에 대한 할당 설정입니다.

[![](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png "Get and update role settings")](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

역할 설정을 업데이트하려면 먼저 설정 개체를 다음과 같이 정의해야 합니다.

    $setting = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting 
    $setting.RuleIdentifier = "JustificationRule"
    $setting.Setting = "{'required':false}"

그런 다음 아래와 같이 특정 역할에 대한 개체 중 하나에 설정을 적용할 수 있습니다. 여기 ID는 목록 역할 설정 cmdlet의 결과에서 검색할 수 있는 역할 설정 ID입니다.

    Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $setting 

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../users-groups-roles/directory-assign-admin-roles.md)
