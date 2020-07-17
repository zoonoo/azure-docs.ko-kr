---
title: Azure AD를 사용하여 최종 사용자가 애플리케이션에 동의하는 방법 구성
description: 사용자가 조직의 데이터에 액세스할 수 있는 애플리케이션에 동의하는 방법 및 시기를 관리하는 방법을 알아봅니다.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.openlocfilehash: 0c9844d5e3f65dba5e51170367cfd16715a08883
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763468"
---
# <a name="configure-how-end-users-consent-to-applications"></a>최종 사용자가 애플리케이션에 동의하는 방법 구성

애플리케이션을 Microsoft ID 플랫폼과 통합하면 사용자가 회사 또는 학교 계정으로 로그인하고 조직의 데이터에 액세스하도록 하여 풍부한 데이터 기반 환경을 제공할 수 있습니다.

애플리케이션에서 조직의 데이터에 액세스하려면 먼저 사용자가 애플리케이션에 이 작업을 수행할 수 있는 권한을 부여해야 합니다. 여러 권한은 서로 다른 수준의 액세스를 허용합니다. 기본적으로 모든 사용자는 관리자의 동의가 필요하지 않은 권한을 애플리케이션에 부여하는 데 동의할 수 있습니다. 예를 들어, 사용자는 기본적으로 앱이 사서함에 액세스하는 데 동의할 수 있지만 앱이 조직의 모든 파일에 대한 읽기 및 쓰기 작업을 수행할 수 있는 무제한 액세스를 통해 허용하는 데 동의할 수 없습니다.

사용자가 앱에 데이터에 대한 액세스 권한을 부여하도록 허용하면 사용자가 유용한 애플리케이션을 손쉽게 획득하고 생산성을 높일 수 있습니다. 그러나 이 구성을 꼼꼼하게 모니터링하고 제어하기 않을 경우 위험이 발생할 수 있습니다.

## <a name="user-consent-settings"></a>사용자 동의 설정

사용자가 애플리케이션에 동의할 수 있는 사례를 제어하려면 모든 사용자에게 적용되는 동의 정책을 선택합니다. 세 가지 동의 정책 옵션은 다음과 같습니다.

* **사용자 동의 사용 안 함** - 사용자가 애플리케이션에 권한을 부여할 수 없습니다. 사용자는 이전에 자신이 동의한 앱 또는 관리자가 사용자를 대신하여 동의한 앱에 계속 로그인할 수 있지만 새 권한이나 새 앱에 자체적으로 동의할 수는 없습니다. 동의할 수 있는 권한이 포함된 디렉터리 역할이 부여된 사용자만 새 권한 또는 새 앱에 동의할 수 있습니다.

* **사용자가 인증된 게시자의 앱에 동의할 수 있지만 관리자가 선택한 권한만 동의할 수 있음(미리 보기)** - 모든 사용자가 [인증된 게시자](../develop/publisher-verification-overview.md)가 게시한 앱 및 테넌트에 등록된 앱에만 동의할 수 있습니다. 사용자가 "낮은 영향"으로 분류된 권한에만 동의할 수 있습니다.

  사용자가 동의할 수 있는 권한을 선택하려면 [권한을 분류](#configure-permission-classifications-preview)해야 합니다.

* **사용자가 모든 앱에 동의할 수 있음** - 이 옵션을 사용하면 모든 사용자가 모든 애플리케이션에 관리자 동의가 필요하지 않은 모든 권한을 동의할 수 있습니다. 

   악의적인 애플리케이션이 사용자에게 조직의 데이터에 대한 액세스 권한을 부여하도록 속이는 위험을 줄이려면 [인증된 게시자](../develop/publisher-verification-overview.md)가 게시한 애플리케이션에만 사용자 동의를 허용하는 것이 좋습니다.

### <a name="configure-user-consent-settings-from-the-azure-portal"></a>Azure Portal에서 사용자 동의 설정 구성

Azure Portal을 통해 사용자 동의 설정을 구성하려면 다음을 수행합니다.

1. [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **사용자 동의 설정**을 선택합니다.
1. **애플리케이션에 대한 사용자 동의**에서 모든 사용자에 대해 구성할 동의 설정을 선택합니다.
1. **저장**을 선택하여 설정을 저장합니다.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="사용자 동의 설정":::

> [!TIP]
> 사용자 동의가 사용하지 않도록 설정된 경우 또는 애플리케이션이 사용자가 부여할 수 없는 권한을 요청하는 경우 등에 사용자가 동의할 수 없는 애플리케이션에 대한 검토 및 승인을 관리자에게 요청할 수 있도록 허용하려면 [관리자 동의 워크플로를 사용하도록 설정](configure-admin-consent-workflow.md)하는 것이 좋습니다.

### <a name="configure-user-consent-settings-using-powershell"></a>PowerShell을 사용하여 사용자 동의 설정 구성

최신 Azure AD PowerShell 미리 보기 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)를 사용하여 애플리케이션에 대한 사용자 동의를 제어하는 동의 정책을 선택할 수 있습니다.

* **사용자 동의를 사용하지 않도록 설정** - 사용자 동의를 사용하지 않도록 설정하려면 사용자 동의를 제어하는 동의 정책을 빈 상태로 설정합니다.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

* **인증된 게시자의 앱에 대한 사용자 동의를 선택한 권한에 한해 허용(미리 보기)** - 인증된 게시자의 앱과 테넌트에 등록된 앱에 대한 제한적 사용자 동의를 "낮은 영향"으로 분류된 권한에 한해 허용하려면 `microsoft-user-default-low`라는 기본 제공 동의 정책을 구성합니다.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-low")
  ```

   사용자가 동의할 수 있는 권한을 선택하려면 [권한을 분류](#configure-permission-classifications-preview)해야 합니다.

* **모든 앱에 대한 사용자 동의 허용** - 모든 앱에 대한 사용자 동의를 허용하려면 다음을 수행해야 합니다.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("microsoft-user-default-legacy")
  ```

   이 옵션을 사용하면 모든 사용자가 모든 애플리케이션에 관리자 동의가 필요하지 않은 모든 권한을 동의할 수 있습니다. 인증된 게시자의 앱에만 사용자 동의를 허용하는 것이 좋습니다.

## <a name="configure-permission-classifications-preview"></a>권한 분류 구성(미리 보기)

권한 분류를 사용하면 조직의 정책 및 위험 평가에 따라 서로 다른 권한에 적용되는 영향을 식별할 수 있습니다. 예를 들어 동의 정책에서 권한 분류를 사용하여 사용자가 동의할 수 있는 권한 세트를 식별할 수 있습니다.

> [!NOTE]
> 현재는 "낮은 영향" 권한 분류만 지원됩니다. 관리자 동의가 필요하지 않은 위임된 권한만 "낮은 영향"으로 분류할 수 있습니다.

### <a name="classify-permissions-using-the-azure-portal"></a>Azure Portal을 사용하여 권한 분류

1. [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **권한 분류**를 선택합니다.
1. 다른 권한을 "낮은 영향"으로 분류하려면 **권한 추가**를 선택합니다. 
1. API를 선택한 후 위임된 권한을 선택합니다.

이 예에서는 Single Sign-On에 필요한 최소 권한 세트를 분류했습니다.

:::image type="content" source="media/configure-user-consent/permission-classifications.png" alt-text="권한 분류":::

> [!TIP]
> Microsoft Graph API의 경우 기본 Single Sign-On을 수행하는 데 필요한 최소 권한은 `openid`, `profile`, `User.Read` 및 `offline_access`입니다. 이러한 권한을 사용하면 앱에서 로그인한 사용자의 프로필 세부 정보를 읽을 수 있으며, 사용자가 더 이상 앱을 사용하지 않는 경우에도 이 액세스를 유지할 수 있습니다.

### <a name="classify-permissions-using-powershell"></a>PowerShell을 사용하여 권한 분류

최신 Azure AD PowerShell 미리 보기 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)를 사용하여 권한을 분류할 수 있습니다. 권한 분류는 권한을 게시하는 API의 **ServicePrincipal** 개체에 구성됩니다.

#### <a name="to-read-the-current-permission-classifications-for-an-api"></a>API의 현재 사용 권한 분류를 읽으려면 다음을 수행합니다.

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. API의 위임된 권한 분류를 읽습니다.

   ```powershell
   Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId | Format-Table Id, PermissionName, Classification
   ```

#### <a name="to-classify-a-permission-as-low-impact"></a>권한을 "낮은 영향"으로 분류하려면 다음을 수행합니다.

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 분류하려는 위임된 권한을 찾습니다.

   ```powershell
   $delegatedPermission = $api.OAuth2Permissions | Where-Object { $_.Value -eq "User.ReadBasic.All" }
   ```

1. 권한 이름 및 ID를 사용하여 권한 분류를 설정합니다.

   ```powershell
   Add-AzureADMSServicePrincipalDelegatedPermissionClassification `
      -ServicePrincipalId $api.ObjectId `
      -PermissionId $delegatedPermission.Id `
      -PermissionName $delegatedPermission.Value `
      -Classification "low"
   ```

#### <a name="to-remove-a-delegated-permission-classification"></a>위임된 권한 분류를 제거하려면 다음을 수행합니다.

1. API의 **ServicePrincipal** 개체를 검색합니다. 여기서는 Microsoft Graph API의 ServicePrincipal 개체를 검색합니다.

   ```powershell
   $api = Get-AzureADServicePrincipal `
       -Filter "servicePrincipalNames/any(n:n eq 'https://graph.microsoft.com')"
   ```

1. 제거하려는 위임된 권한 분류를 찾습니다.

   ```powershell
   $classifications = Get-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId
   $classificationToRemove = $classifications | Where-Object {$_.PermissionName -eq "User.ReadBasic.All"}
   ```

1. 권한 분류를 삭제합니다.

   ```powershell
   Remove-AzureADMSServicePrincipalDelegatedPermissionClassification `
       -ServicePrincipalId $api.ObjectId `
       -Id $classificationToRemove.Id
   ```

## <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>그룹 데이터에 액세스하는 앱에 대한 그룹 소유자 동의 구성

그룹 소유자는 타사 공급업체에서 게시한 애플리케이션과 같은 애플리케이션에 권한을 부여하여 그룹과 연결된 조직의 데이터에 액세스하도록 할 수 있습니다. 예를 들어 Microsoft 팀 소유자는 앱이 팀의 모든 팀 메시지를 읽거나 그룹 멤버의 기본 프로필을 나열하도록 허용할 수 있습니다.

해당 그룹의 데이터에 액세스하는 앱에 동의할 수 있는 사용자를 구성하거나 이 기능을 사용하지 않도록 설정할 수 있습니다.

### <a name="configure-group-owner-consent-using-the-azure-portal"></a>Azure Portal을 사용하여 그룹 소유자 동의 구성

1. [전역 관리자](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator)로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **사용자 동의 설정**을 선택합니다.
3. **데이터에 액세스하는 앱에 대한 그룹 소유자 동의** 아래에서 사용하도록 설정하려는 옵션을 선택합니다.
4. **저장**을 선택하여 설정을 저장합니다.

이 예제에서 모든 그룹 소유자는 해당 그룹의 데이터에 액세스하는 앱에 동의할 수 있습니다.

:::image type="content" source="media/configure-user-consent/group-owner-consent.png" alt-text="그룹 소유자 동의 설정":::

### <a name="configure-group-owner-consent-using-powershell"></a>PowerShell을 사용하여 그룹 소유자 동의 구성

Azure AD PowerShell Preview 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)를 사용하여 소유하고 있는 그룹의 조직 데이터에 액세스하는 애플리케이션에 동의하는 그룹 소유자의 권한을 허용하거나 허용하지 않을 수 있습니다.

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 사용하고 있는지 확인하세요. [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0) 모듈 및 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview) 모듈을 모두 설치한 경우 이 단계가 중요합니다.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

1. 테넌트에서 **동의 정책 설정** 디렉터리 설정의 현재 값을 검색합니다. 그러기 위해서는 이 기능의 디렉터리 설정이 생성되었는지 확인하고, 생성되지 않았다면 해당하는 디렉터리 설정 템플릿의 값을 사용해야 합니다.

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

1. 설정 값을 이해합니다. 앱에서 해당 그룹의 데이터에 액세스하도록 허용할 수 있는 사용자를 정의하는 두 가지 설정 값이 있습니다.

    | 설정       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | 부울 | 그룹 소유자가 그룹 관련 권한을 부여할 수 있는지 여부를 나타내는 플래그입니다. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | _EnableGroupSpecificConsent_를 "True"로 설정하고 이 값을 그룹의 개체 ID로 설정하면 식별된 그룹의 멤버가 자신이 소유한 그룹에 대한 그룹 관련 권한을 부여할 수 있습니다. |

1. 원하는 구성의 설정 값을 업데이트합니다.

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

1. 설정을 저장합니다.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

## <a name="configure-risk-based-step-up-consent"></a>위험 기반 상향 동의 구성

위험 기반 상향 동의는 [불법 동의 요청](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)을 보내는 악성 앱에 대한 사용자 노출을 줄이는 데 도움이 됩니다. Microsoft가 위험한 최종 사용자 동의 요청을 감지하면 이 요청에서 관리자 동의로의 상향을 대신 요구합니다. 이 기능은 기본적으로 사용하도록 설정되어 있지만 최종 사용자 동의가 허용된 경우에만 동작이 변경됩니다.

위험한 동의 요청이 감지되면 동의 프롬프트에 관리자 승인이 필요함을 나타내는 메시지가 표시됩니다. [관리자 동의 요청 워크플로](configure-admin-consent-workflow.md)가 설정된 경우 사용자는 동의 프롬프트에서 직접 관리자에게 추가 검토가 필요한 요청을 보낼 수 있습니다. 사용하도록 설정되어 있지 않으면 다음 메시지가 표시됩니다.

* **AADSTS90094:** &lt;clientAppDisplayName&gt;에는 관리자만 권한을 부여할 수 있는 조직의 리소스에 대한 액세스 권한이 필요합니다. 이 앱을 사용하려면 먼저 관리자에게 앱의 사용 권한을 부여하도록 요청하세요.

이 경우 감사 이벤트는 범주 "ApplicationManagement", 활동 유형 "애플리케이션에 동의" 및 상태 이유 "위험한 애플리케이션 감지됨"으로도 로깅됩니다.

> [!IMPORTANT]
> 관리자는 요청을 승인하기 전에 [모든 동의 요청을 신중하게 평가](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)해야 합니다(특히 Microsoft에서 위험을 감지한 경우).

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>PowerShell을 사용하여 위험 기반 상향 동의 사용 안 함 또는 다시 사용

Azure AD PowerShell 미리 보기 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview)를 사용하여 Microsoft에서 위험을 감지하거나 이전에 사용하지 않도록 설정되어 다시 사용하도록 설정하는 경우에 필요한 관리자 동의로의 상향 기능을 사용하지 않도록 설정할 수 있습니다.

위에 표시된 것과 동일한 단계를 사용하여 [PowerShell을 통해 그룹 소유자 동의를 구성](#configure-group-owner-consent-using-powershell)하되, 다른 설정 값으로 대체하는 방식으로 이 작업을 수행할 수 있습니다. 이 단계에는 세 가지 차이점이 있습니다. 

1. 위험 기반 상향 동의의 설정 값을 이해합니다.

    | 설정       | Type         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | 부울 |  위험한 요청이 감지될 때 사용자 동의가 차단되는지 나타내는 플래그입니다. |

1. 3단계에서 다음 값을 대체합니다.

    ```powershell
    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```
    
1. 5단계에서 다음 중 하나를 대체합니다.

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
    ```

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.
* [StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
