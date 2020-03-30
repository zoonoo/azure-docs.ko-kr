---
title: 앱 등록 만들기 제한 제거 - Azure AD | 마이크로 소프트 문서
description: Azure AD Active Directory에서 무제한 앱 등록을 부여하는 사용자 지정 역할 할당
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7acd76ff45f783f614b2a1d3f0d5c10d800a1ea9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77559048"
---
# <a name="quickstart-grant-permission-to-create-unlimited-app-registrations"></a>빠른 시작: 무제한 앱 등록을 만들 수 있는 권한 부여

이 빠른 시작에서는 무제한의 앱 등록을 만들 수 있는 권한이 있는 사용자 지정 역할을 만든 다음 해당 역할을 사용자에게 할당합니다. 그런 다음 할당된 사용자는 Azure AD 포털, Azure AD PowerShell 또는 Microsoft 그래프 API를 사용하여 응용 프로그램 등록을 만들 수 있습니다. 기본 제공 응용 프로그램 개발자 역할과 달리 이 사용자 지정 역할은 무제한의 응용 프로그램 등록을 만들 수 있는 기능을 부여합니다. 응용 프로그램 개발자 역할은 기능을 부여하지만 [디렉터리 전체](directory-service-limits-restrictions.md)개체 할당량에 부딪히지 않도록 만든 개체의 총 수는 250개로 제한됩니다.

Azure 구독이 없는 경우 시작하기 전에 [무료 계정을 만드세요.](https://azure.microsoft.com/free/)

## <a name="prerequisite"></a>필수 요소

Azure AD 사용자 지정 역할을 만들고 할당하는 데 필요한 최소 권한 역할은 권한 있는 역할 관리자입니다.

## <a name="create-a-new-custom-role-using-the-azure-ad-portal"></a>Azure AD 포털을 사용하여 새 사용자 지정 역할 만들기

1. Azure AD 조직의 권한 있는 역할 관리자 또는 전역 관리자 권한을 사용하여 Azure [AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **Azure Active Directory를**선택하고 **역할 및 관리자를**선택한 다음 **새 사용자 지정 역할을**선택합니다.

    ![역할 및 관리자 페이지에서 역할 만들기 또는 편집](./media/roles-create-custom/new-custom-role.png)

1. **기본** 탭에서 역할 이름에 대해 "응용 프로그램 등록 작성자"를 제공하고 역할 설명에 대해 "응용 프로그램 등록을 무제한으로 만들 수 있습니다"를 제공한 다음 **다음을 선택합니다.**

    ![기본 탭에서 사용자 지정 역할에 대한 이름과 설명을 제공합니다.](./media/roles-quickstart-app-registration-limits/basics-tab.png)

1. 사용 **권한** 탭에서 검색 상자에 "microsoft.directory/application/create"를 입력한 다음 원하는 권한 옆에 있는 확인란을 선택한 다음 **다음**을 선택합니다.

    ![사용 권한 탭에서 사용자 지정 역할에 대한 사용 권한 선택](./media/roles-quickstart-app-registration-limits/permissions-tab.png)

1. 검토 **+ 만들기** 탭에서 사용 권한을 검토하고 **에서 만들기를**선택합니다.

### <a name="assign-the-role-to-a-user-using-the-azure-ad-portal"></a>Azure AD 포털을 사용하여 사용자에게 역할 할당

1. Azure AD 조직의 권한 있는 역할 관리자 또는 전역 관리자 권한을 사용하여 Azure [AD 관리자 센터에](https://aad.portal.azure.com) 로그인합니다.
1. **Azure Active 디렉터리를** 선택한 다음 **역할 및 관리자를**선택합니다.
1. 응용 프로그램 등록 작성자 역할을 선택하고 **할당 추가를**선택합니다.
1. 원하는 사용자를 선택하고 **선택을** 클릭하여 역할에 사용자를 추가합니다.

완료되었습니다. 이 빠른 시작에서는 무제한의 앱 등록을 만든 다음 해당 역할을 사용자에게 할당할 수 있는 권한이 있는 사용자 지정 역할을 성공적으로 만들었습니다.

> [!TIP]
> Azure AD 포털을 사용하여 응용 프로그램에 역할을 할당하려면 할당 페이지의 검색 상자에 응용 프로그램의 이름을 입력합니다. 응용 프로그램은 기본적으로 목록에 표시되지 않지만 검색 결과에 반환됩니다.

### <a name="app-registration-permissions"></a>앱 등록 권한

응용 프로그램 등록을 만들 수 있는 기능을 부여하는 데 사용할 수 있는 두 가지 권한이 있습니다.

- microsoft.directory/application/createAOwner: 이 권한을 할당하면 생성된 앱 등록의 첫 번째 소유자로 크리에이터가 추가되고 생성된 앱 등록은 생성자의 생성된 개체 할당량 250개에 계산됩니다.
- microsoft.directory/applicationPolicy/create: 이 권한을 할당하면 생성자가 생성된 앱 등록의 첫 번째 소유자로 추가되지 않으며 생성된 앱 등록은 생성자의 생성된 개체 할당량 250개에 포함되지 않습니다. 디렉터리 수준 할당량에 도달할 때까지 할당인이 앱 등록을 만드는 데 방해가 되는 것은 없으므로 이 권한을 신중하게 사용하십시오. 두 사용 권한이 모두 할당된 경우 이 사용 권한이 우선합니다.

## <a name="create-a-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용하여 사용자 지정 역할 만들기

다음 PowerShell 스크립트를 사용하여 새 역할을 만듭니다.

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/createAsOwner"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="assign-the-custom-role-using-azure-ad-powershell"></a>Azure AD PowerShell을 사용하여 사용자 지정 역할 할당

#### <a name="prepare-powershell"></a>파워쉘 준비

먼저 [PowerShell 갤러리에서](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.17)Azure AD PowerShell 모듈을 설치합니다. 그런 다음 다음 명령을 사용하여 Azure AD PowerShell 미리 보기 모듈을 가져옵니다.

```powershell
import-module azureadpreview
```

모듈을 사용할 준비가 되었는지 확인하려면 다음 명령으로 반환된 버전을 여기에 나열된 버전과 일치시면 됩니다.

```powershell
get-module azureadpreview
  ModuleType Version      Name                         ExportedCommands
  ---------- ---------    ----                         ----------------
  Binary     2.0.0.115    azureadpreview               {Add-AzureADAdministrati...}
```

#### <a name="assign-the-custom-role"></a>사용자 지정 역할 할당

아래 PowerShell 스크립트를 사용하여 역할을 할당합니다.

``` PowerShell
# Basic role information
$description = "Application Registration Creator"
$displayName = "Can create an unlimited number of application registrations."
$templateId = (New-Guid).Guid

# Set of permissions to grant
$allowedResourceAction =
@(
    "microsoft.directory/applications/create"
)
$resourceActions = @{'allowedResourceActions'= $allowedResourceAction}
$rolePermission = @{'resourceActions' = $resourceActions}
$rolePermissions = $rolePermission

# Create new custom admin role
$customRole = New-AzureAdRoleDefinition -RolePermissions $rolePermissions -DisplayName $displayName -Description $description -TemplateId $templateId -IsEnabled $true
```

### <a name="create-a-custom-role-using-microsoft-graph-api"></a>마이크로소프트 그래프 API를 사용 하 여 사용자 지정 역할 만들기

사용자 지정 역할을 만들기 위한 HTTP 요청입니다.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleDefinitions
```

본문

```HTTP
{
    "description":"Can create an unlimited number of application registrations.",
    "displayName":"Application Registration Creator",
    "isEnabled":true,
    "rolePermissions":
    [
        {
            "resourceActions":
            {
                "allowedResourceActions":
                [
                    "microsoft.directory/applications/create"
                ]
            },
            "condition":null
        }
    ],
    "templateId":"<PROVIDE NEW GUID HERE>",
    "version":"1"
}
```

### <a name="assign-the-custom-role-using-microsoft-graph-api"></a>Microsoft 그래프 API를 사용하여 사용자 지정 역할 할당

역할 할당은 보안 주체 ID(사용자 또는 서비스 주체일 수 있음), 역할 정의(ROLE) ID 및 Azure AD 리소스 범위를 결합합니다.

사용자 지정 역할을 할당하는 HTTP 요청입니다.

POST

``` HTTP
https://graph.microsoft.com/beta/roleManagement/directory/roleAssignments
```

본문

``` HTTP
{
    "principalId":"<PROVIDE OBJECTID OF USER TO ASSIGN HERE>",
    "roleDefinitionId":"<PROVIDE OBJECTID OF ROLE DEFINITION HERE>",
    "resourceScopes":["/"]
}
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 관리 역할 포럼](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)에서 경험을 자유롭게 공유하세요.
- 역할 및 관리자 역할 할당에 대한 자세한 내용은 [관리자 역할 할당](directory-assign-admin-roles.md)을 참조하세요.
- 기본 사용자 권한의 경우 [기본 게스트 및 멤버 사용자 권한 비교](../fundamentals/users-default-permissions.md)를 참조하세요.
