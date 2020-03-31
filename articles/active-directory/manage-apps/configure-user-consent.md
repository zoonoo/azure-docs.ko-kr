---
title: Azure AD를 사용하여 최종 사용자가 응용 프로그램에 동의하는 방식 구성
description: 사용자가 조직의 데이터에 액세스할 수 있는 응용 프로그램에 동의할 수 있는 방법과 시기를 관리하는 방법을 알아봅니다.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/22/2018
ms.author: mimart
ms.reviewer: arvindh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5bd305d2943d1b12756171748f28d32300081d71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443400"
---
# <a name="configure-how-end-users-consent-to-applications"></a>최종 사용자가 응용 프로그램에 동의하는 방식 구성

응용 프로그램은 Microsoft Id 플랫폼과 통합하여 사용자가 Azure Active Directory(Azure AD)에서 자신의 업무 또는 학교 계정을 사용하여 로그인하고 조직의 데이터에 액세스하여 풍부한 데이터 기반 환경을 제공할 수 있도록 할 수 있습니다. 사용 권한이 다르면 응용 프로그램이 사용자 및 조직의 데이터에 대한 액세스 수준이 다를 수 있습니다.

기본적으로 사용자는 일부 권한에 대해서만 조직의 데이터에 액세스하는 응용 프로그램에 동의할 수 있습니다. 예를 들어 기본적으로 사용자는 앱이 사용자가 소유한 팀의 사서함 또는 Teams 대화에 액세스할 수 있도록 허용하는 데 동의할 수 있지만 앱이 조직의 모든 SharePoint 사이트에 읽기 및 쓰기를 허용하는 데 동의할 수는 없습니다. 사용자가 스스로 동의할 수 있도록 허용하면 사용자가 Microsoft 365, Azure 및 기타 서비스와 통합되는 유용한 응용 프로그램을 쉽게 획득할 수 있지만 신중하게 사용하고 모니터링하지 않으면 위험을 나타낼 수 있습니다.

향후 사용자 동의 작업을 사용하지 않도록 설정하여 노출 영역을 줄이고 이러한 위험을 완화하는 것이 좋습니다. 사용자 동의가 비활성화된 경우 이전 동의 권한 부여는 계속 적용되지만 향후 모든 동의 작업은 관리자가 수행해야 합니다. 통합된 [관리자 동의 요청 워크플로를](configure-admin-consent-workflow.md) 통해 또는 자체 지원 프로세스를 통해 테넌트 전체 관리자 동의를 요청할 수 있습니다. 자세한 [내용은 ID 인프라를 보호하는 5단계를](../../security/fundamentals/steps-secure-identity.md) 참조하십시오.

## <a name="configure-user-consent-to-applications"></a>응용 프로그램에 대한 사용자 동의 구성
### <a name="disable-or-enable-user-consent-from-the-azure-portal"></a>Azure 포털에서 사용자 동의를 사용하지 않도록 설정하거나 사용하도록 설정

Azure 포털을 사용하여 조직의 데이터에 액세스하는 응용 프로그램에 대한 사용자의 동의 기능을 사용하지 않도록 설정하거나 활성화할 수 있습니다.

1. [Azure 포털에](https://portal.azure.com) 글로벌 [관리자로](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)로그인합니다.
2. **Azure Active Directory를**선택한 다음 엔터프라이즈 응용 프로그램 , **다음 사용자 설정을** **선택합니다.**
3. 사용자 로 표시된 컨트롤을 사용하여 사용자 동의를 활성화 하거나 **비활성화하면 사용자를 대신하여 회사 데이터에 액세스하는 앱에 동의할 수 있습니다.**
4. (선택 사항) 앱에 동의할 수 없는 사용자가 승인을 요청할 수 있도록 [관리자 동의 요청 워크플로우를](configure-admin-consent-workflow.md) 구성합니다.

> [!TIP]
> 사용자가 동의할 수 없는 응용 프로그램의 관리자 검토를 요청할 수 있도록 하려면(예: 사용자 동의가 비활성화되었거나 응용 프로그램이 사용자에게 허용되지 않는 권한을 요청하기 때문에) [관리자 동의 워크플로를 구성하는](configure-admin-consent-workflow.md)것이 좋습니다.

### <a name="disable-or-enable-user-consent-using-powershell"></a>PowerShell을 사용하여 사용자 동의를 비활성화하거나 사용하도록 설정

Azure AD PowerShell v1[모듈(MSOnline)을](https://docs.microsoft.com/powershell/module/msonline/?view=azureadps-1.0)사용하여 조직의 데이터에 액세스하는 응용 프로그램에 대한 동의에 대한 사용자의 동의 기능을 사용하거나 비활성화할 수 있습니다.

1. 이 cmdlet을 실행하여 조직에 로그인합니다.

    ```powershell
    Connect-MsolService
    ```

2. 이 cmdlet을 실행하여 사용자 동의가 활성화되어 있는지 확인합니다.

    ```powershell
    Get-MsolCompanyInformation | Format-List UsersPermissionToUserConsentToAppEnabled
    ```

3. 사용자 동의를 활성화하거나 비활성화합니다. 예를 들어 사용자 동의를 비활성화하려면 다음 cmdlet을 실행합니다.

    ```powershell
    Set-MsolCompanySettings -UsersPermissionToUserConsentToAppEnabled $false
    ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>그룹 데이터에 액세스하는 앱에 대한 그룹 소유자 동의 구성

> [!IMPORTANT]
> 다음 정보는 그룹 소유자가 응용 프로그램에 그룹의 데이터에 대한 액세스 권한을 부여할 수 있도록 하는 향후 기능에 대한 것입니다. 이 기능이 해제되면 기본적으로 활성화됩니다. 이 기능은 아직 널리 릴리스되지는 않았지만 이 지침을 사용하여 릴리스 전에 기능을 비활성화할 수 있습니다.

그룹 소유자는 응용 프로그램(예: 타사 공급업체가 게시한 응용 프로그램)에 그룹과 연결된 조직의 데이터에 액세스할 수 있는 권한을 부여할 수 있습니다. 예를 들어 팀 소유자(팀의 Office 365 그룹의 소유자)는 앱이 팀의 모든 Teams 메시지를 읽거나 그룹 구성원의 기본 프로필을 나열하도록 허용할 수 있습니다.

> [!NOTE]
> 이 설정과 는 별개로 그룹 소유자는 항상 다른 사용자 나 앱을 그룹 소유자로 직접 추가할 수 있습니다.

### <a name="configure-group-owner-consent-using-powershell"></a>PowerShell을 사용하여 그룹 소유자 동의 구성

Azure AD PowerShell 미리 보기[모듈(AzureADPreview)을](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)사용하여 그룹 소유자가 소유한 그룹에 대한 조직의 데이터에 액세스하는 응용 프로그램에 동의하는 그룹 소유자의 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다.

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 사용하고 있는지 확인합니다(이 단계는 [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) 모듈과 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 모두 설치한 경우 중요합니다).

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

2. Azure AD PowerShell에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

3. 테넌트의 동의 *정책 설정* 디렉터리 설정에 대한 현재 값을 검색합니다. 이렇게 하려면 이 기능에 대한 디렉터리 설정이 만들어졌는지, 그렇지 않은 경우 해당 디렉터리 설정 템플릿의 값을 사용하여 확인해야 합니다.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

4. 설정 값을 이해합니다. 앱이 그룹의 데이터에 액세스하도록 허용할 사용자를 정의하는 두 가지 설정 값이 있습니다.

    | 설정       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _활성화 그룹특정동의_   | 부울 |  그룹 소유자가 그룹별 권한을 부여할 수 있는지 나타내는 플래그입니다. |
    | _제약그룹특정회원그룹_ | Guid | _EnableGroupSpecificConsent가_ "True"로 설정되고 이 값이 그룹의 개체 ID로 설정된 경우 식별된 그룹의 구성원은 소유한 그룹에 그룹별 권한을 부여할 수 있는 권한이 부여됩니다. |

5. 원하는 구성에 대한 설정 값을 업데이트합니다.

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

6. 설정을 저장합니다.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="next-steps"></a>다음 단계

[관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)

[응용 프로그램에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)

[Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

[스택 오버플로에서 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
