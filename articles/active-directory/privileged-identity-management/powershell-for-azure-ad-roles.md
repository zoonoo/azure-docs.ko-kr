---
title: PIM에서 Azure AD 역할에 대한 PowerShell -Azure AD | Microsoft Docs
description: Azure AD PIM(Privileged Identity Management)에서 PowerShell cmdlet을 사용하여 Azure AD 역할을 관리합니다.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d04a2941888592ffa37acfe6cba52a33fda528
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92365513"
---
# <a name="powershell-for-azure-ad-roles-in-privileged-identity-management"></a>Privileged Identity Management에서 Azure AD 역할에 대한 PowerShell

이 문서에는 Azure AD(Azure Active Directory) PowerShell cmdlet을 사용하여 PIM(Privileged Identity Management)에서 Azure AD 역할을 관리하는 방법에 대한 지침이 포함되어 있습니다. 또한 Azure AD PowerShell 모듈을 사용하여 설정하는 방법을 설명합니다.

> [!Note]
> 공식 PowerShell은 새 버전의 Azure AD Privileged Identity Management를 사용하는 경우에만 지원됩니다. Privileged Identity Management로 이동하여 빠른 시작 블레이드에 다음 배너가 있는지 확인하세요.
> [![사용하는 Privileged Identity Management 버전 확인](media/pim-how-to-add-role-to-user/pim-new-version.png "Azure AD > Privileged Identity Management 선택")](media/pim-how-to-add-role-to-user/pim-new-version.png#lightbox) 이 배너가 없으면 기다려주세요. 앞으로 몇 주 동안 업데이트된 환경을 출시하기 위한 작업을 진행하고 있습니다.
> Privileged Identity Management PowerShell cmdlet은 Azure AD Preview 모듈을 통해 지원됩니다. 다른 모듈을 사용 중이고 해당 모듈이 오류 메시지를 반환하면 이 새 모듈로 시작하세요. 다른 모듈 위에 빌드된 프로덕션 시스템이 있는 경우에 문의 하세요 [pim_preview@microsoft.com](mailto:pim_preview@microsoft.com) .

## <a name="installation-and-setup"></a>설치 및 설정

1. Azure AD Preview 모듈 설치

    ```powershell
    Install-module AzureADPreview
    ```

1. 계속하기 전에 필요한 역할 권한을 갖고 있는지 확인하세요. 역할 할당 또는 역할 설정 업데이트 등의 관리 작업을 수행하려면 전역 관리자 또는 권한 있는 역할 관리자 역할이 있는지 확인하세요. 본인의 할당만 활성화하려는 경우에는 기본 사용자 권한만 있으면 됩니다.

1. Azure AD에 연결합니다.

    ```powershell
    $AzureAdCred = Get-Credential  
    Connect-AzureAD -Credential $AzureAdCred
    ```

1. **Azure Active Directory**  >  **속성**  >  **디렉터리 id**로 이동 하 여 Azure AD 조직에 대 한 테 넌 트 ID를 찾습니다. cmdlet 섹션에서 resourceId를 입력해야 할 때마다 이 ID를 사용합니다.

    ![Azure AD 조직의 속성에서 조직 ID 찾기](./media/powershell-for-azure-ad-roles/tenant-id-for-Azure-ad-org.png)

> [!Note]
> 다음 섹션은 실행하는 데 도움이 되는 간단한 예제입니다. 에서 다음 cmdlet에 대 한 자세한 설명서를 찾을 수 있습니다 [https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true](/powershell/module/azuread/?view=azureadps-2.0-preview#privileged_role_management&preserve-view=true) . 그러나 providerID 매개 변수에서 "azureResources"를 "aadRoles"로 바꾸어야 합니다. 또한 Azure AD 조직에 대 한 테 넌 트 ID를 resourceId 매개 변수로 사용 해야 합니다.

## <a name="retrieving-role-definitions"></a>역할 정의 검색

다음 cmdlet을 사용하여 Azure AD 조직의 모든 기본 제공 및 사용자 지정 Azure AD 역할을 가져옵니다. 이 단계는 역할 이름과 roleDefinitionId 간의 매핑을 제공하는 중요한 단계입니다. roleDefinitionId는 특정 역할을 참조하기 위해 이러한 cmdlet 전체에서 사용됩니다.

roleDefinitionId는 Azure AD 조직과 관련이 있으며 역할 관리 API에서 반환하는 roleDefinitionId와는 다릅니다.

```powershell
Get-AzureADMSPrivilegedRoleDefinition -ProviderId aadRoles -ResourceId 926d99e7-117c-4a6a-8031-0cc481e9da26
```

결과:

![Azure AD 조직의 모든 역할을 가져옵니다.](./media/powershell-for-azure-ad-roles/get-all-roles-result.png)

## <a name="retrieving-role-assignments"></a>역할 할당 검색

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 할당을 검색합니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26"
```

다음 cmdlet을 사용하여 특정 사용자의 모든 역할 할당을 검색합니다. 이 목록을 Azure AD 포털에서는 "내 역할"이라고 합니다. 여기서 유일한 차이점은 주체 ID의 필터를 추가했다는 것입니다. 이 컨텍스트에서 주체 ID는 사용자 ID 또는 그룹 ID입니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "subjectId eq 'f7d1887c-7777-4ba3-ba3d-974488524a9d'" 
```

다음 cmdlet을 사용하여 특정 역할의 모든 역할 할당을 검색합니다. 여기서 roleDefinitionId는 이전 cmdlet에서 반환된 ID입니다.

```powershell
Get-AzureADMSPrivilegedRoleAssignment -ProviderId "aadRoles" -ResourceId "926d99e7-117c-4a6a-8031-0cc481e9da26" -Filter "roleDefinitionId eq '0bb54a22-a3df-4592-9dc7-9e1418f0f61c'"
```

cmdlet의 결과로 아래의 역할 할당 개체 목록이 생성됩니다. 주체 ID는 역할이 할당된 사용자의 사용자 ID입니다. 할당 상태는 활성 또는 적격 중 하나입니다. 사용자가 활성 상태이고 LinkedEligibleRoleAssignmentId 필드에 ID가 있으면 역할이 현재 활성화되었다는 뜻입니다.

결과:

![Azure AD 조직의 모든 역할 할당 검색](./media/powershell-for-azure-ad-roles/get-all-role-assignments-result.png)

## <a name="assign-a-role"></a>역할 할당

다음 cmdlet을 사용하여 적격 할당을 만듭니다.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'ff690580-d1c6-42b1-8272-c029ded94dec' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'adminAdd' -AssignmentState 'Eligible' -schedule $schedule -reason "dsasdsas" 
```

할당의 시작 및 종료 시간을 정의하는 일정은 다음 예제처럼 만들 수 있는 개체입니다.

```powershell
$schedule = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedSchedule
$schedule.Type = "Once"
$schedule.StartDateTime = (Get-Date).ToUniversalTime().ToString("yyyy-MM-ddTHH:mm:ss.fffZ")
$schedule.endDateTime = "2020-07-25T20:49:11.770Z"
```
> [!Note]
> endDateTime 값이 null로 설정되면 영구적 할당이라는 의미입니다.

## <a name="activate-a-role-assignment"></a>역할 할당 활성화

다음 cmdlet을 사용하여 적격 할당을 활성화합니다.

```powershell
Open-AzureADMSPrivilegedRoleAssignmentRequest -ProviderId 'aadRoles' -ResourceId '926d99e7-117c-4a6a-8031-0cc481e9da26' -RoleDefinitionId 'f55a9a68-f424-41b7-8bee-cee6a442d418' -SubjectId 'f7d1887c-7777-4ba3-ba3d-974488524a9d' -Type 'UserAdd' -AssignmentState 'Active' -schedule $schedule -reason "dsasdsas"
``` 

이 cmdlet은 역할 할당을 만드는 cmdlet과 거의 동일합니다. cmdlet 간의 주요 차이점은 –Type 매개 변수에서 활성화는 "adminAdd"가 아닌 "userAdd"라는 점입니다. 다른 차이점으로 –AssignmentState 매개 변수는 "Eligible"이 아닌 "Active"입니다.

> [!Note]
> PowerShell을 통해 역할을 활성화할 때 두 가지 제한이 있습니다.
> 1. 역할 설정에서 티켓 시스템/티켓 번호가 필요한 경우 이를 매개 변수로 제공할 수 있는 방법이 없습니다. 따라서 Azure Portal의 범위를 벗어나서 역할을 활성화할 수 없습니다. 이 기능은 향후 몇 달 동안 PowerShell에 도입될 예정입니다.
> 1. 역할 활성화에 다단계 인증이 필요한 경우 현재는 사용자가 자신의 역할을 활성화할 때 PowerShell에서 사용자를 확인할 수 있는 방법이 없습니다. 대신 사용자는 Azure AD에 연결할 때 당사의 엔지니어 중 한 명이 작성한 [이 블로그 게시물](http://www.anujchaudhary.com/2020/02/connect-to-azure-ad-powershell-with-mfa.html)에 따라 MFA를 트리거해야 합니다. PIM용 애플리케이션을 개발 중인 경우에는 사용자가 "MfaRule" 오류를 받은 후 사용자를 확인하고 사용자를 모듈에 다시 연결하는 방법을 구현할 수 있습니다.

## <a name="retrieving-and-updating-role-settings"></a>역할 설정 검색 및 업데이트

다음 cmdlet을 사용하여 Azure AD 조직의 모든 역할 설정을 가져옵니다.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq '926d99e7-117c-4a6a-8031-0cc481e9da26'"
```

설정에는 네 가지 주요 개체가 있습니다. 이러한 개체 중 세 개만 현재 PIM에서 사용됩니다. UserMemberSettings는 활성화 설정이고, AdminEligibleSettings는 적격 할당에 대한 할당 설정이고, AdminmemberSettings는 활성 할당에 대한 할당 설정입니다.

[![역할 설정을 가져오고 업데이트 합니다.](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png)](media/powershell-for-azure-ad-roles/get-update-role-settings-result.png#lightbox)

역할 설정을 업데이트하려면 특정 역할의 기존 설정 개체를 가져와서 변경해야 합니다.

```powershell
Get-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Filter "ResourceId eq 'tenant id' and RoleDefinitionId eq 'role id'"
$settinga = New-Object Microsoft.Open.MSGraph.Model.AzureADMSPrivilegedRuleSetting
$settinga.RuleIdentifier = "JustificationRule"
$settinga.Setting = '{"required":false}'
```

그런 다음, 아래와 같이 특정 역할의 개체 중 하나에 설정을 적용할 수 있습니다. 여기서 ID는 목록 역할 설정 cmdlet의 결과에서 검색할 수 있는 역할 설정 ID입니다.

```powershell
Set-AzureADMSPrivilegedRoleSetting -ProviderId 'aadRoles' -Id 'ff518d09-47f5-45a9-bb32-71916d9aeadf' -ResourceId '3f5887ed-dd6e-4821-8bde-c813ec508cf9' -RoleDefinitionId '2387ced3-4e95-4c36-a915-73d803f93702' -UserMemberSettings $settinga 
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 사용자 지정 역할 할당](azure-ad-custom-roles-assign.md)
- [Azure AD 사용자 지정 역할 할당 제거 또는 업데이트](azure-ad-custom-roles-update-remove.md)
- [Azure AD 사용자 지정 역할 할당 구성](azure-ad-custom-roles-configure.md)
- [Azure AD의 역할 정의](../roles/permissions-reference.md)
