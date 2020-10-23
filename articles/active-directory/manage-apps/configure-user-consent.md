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
ms.date: 06/01/2020
ms.author: kenwith
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperfq2
ms.openlocfilehash: c1c0c3038c687b7f91d3c75d8c4c9589c5e245a3
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427625"
---
# <a name="configure-how-end-users-consent-to-applications"></a>최종 사용자가 애플리케이션에 동의하는 방법 구성

애플리케이션을 Microsoft ID 플랫폼과 통합하면 사용자가 회사 또는 학교 계정으로 로그인하고 조직의 데이터에 액세스하도록 하여 풍부한 데이터 기반 환경을 제공할 수 있습니다.

애플리케이션에서 조직의 데이터에 액세스하려면 먼저 사용자가 애플리케이션에 이 작업을 수행할 수 있는 권한을 부여해야 합니다. 여러 권한은 서로 다른 수준의 액세스를 허용합니다. 기본적으로 모든 사용자는 관리자의 동의가 필요하지 않은 권한을 애플리케이션에 부여하는 데 동의할 수 있습니다. 예를 들어, 사용자는 기본적으로 앱이 사서함에 액세스하는 데 동의할 수 있지만 앱이 조직의 모든 파일에 대한 읽기 및 쓰기 작업을 수행할 수 있는 무제한 액세스를 통해 허용하는 데 동의할 수 없습니다.

사용자가 앱에 데이터에 대한 액세스 권한을 부여하도록 허용하면 사용자가 유용한 애플리케이션을 손쉽게 획득하고 생산성을 높일 수 있습니다. 그러나 이 구성을 꼼꼼하게 모니터링하고 제어하기 않을 경우 위험이 발생할 수 있습니다.

> [!IMPORTANT]
> 악의적인 애플리케이션이 사용자에게 조직의 데이터에 대한 액세스 권한을 부여하도록 속이는 위험을 줄이려면 [인증된 게시자](../develop/publisher-verification-overview.md)가 게시한 애플리케이션에만 사용자 동의를 허용하는 것이 좋습니다.

## <a name="user-consent-settings"></a>사용자 동의 설정

앱 승인 정책은 앱을 동의한 수 있으려면 충족 되어야 하는 조건을 설명 합니다. 이러한 정책에는 앱이 요청 하는 권한 뿐만 아니라 액세스를 요청 하는 앱의 조건이 포함 될 수 있습니다.

모든 사용자에 대해 적용 되는 앱 동의 정책을 선택 하면 최종 사용자가 앱에 동의를 부여할 수 있는 경우 및 관리자 검토 및 승인을 요청 해야 하는 경우에 대 한 제한을 설정할 수 있습니다.

* **사용자 동의 사용 안 함** - 사용자가 애플리케이션에 권한을 부여할 수 없습니다. 사용자는 이전에 자신이 동의한 앱 또는 관리자가 사용자를 대신하여 동의한 앱에 계속 로그인할 수 있지만 새 권한이나 새 앱에 자체적으로 동의할 수는 없습니다. 동의를 부여할 수 있는 권한이 포함 된 디렉터리 역할이 부여 된 사용자만 새 앱에 동의할 수 있습니다.

* **사용자는 확인 된 게시자 또는 조직의 앱에 동의할 수 있지만, 선택한 권한에 대해서만** 모든 사용자가 [확인 된 게시자](../develop/publisher-verification-overview.md) 및 테 넌 트에 등록 된 앱에 의해 게시 된 앱에만 동의할 수 있습니다. 사용자는 "낮은 영향"으로 분류 한 사용 권한에만 동의할 수 있습니다. 사용자가 동의할 수 있는 권한을 선택 하려면 [사용 권한을 분류](configure-permission-classifications.md) 해야 합니다.

* **사용자가 모든 앱에 동의할 수 있음** -이 옵션을 선택 하면 모든 사용자가 모든 응용 프로그램에 대해 관리자 동의가 필요 없는 모든 권한에 동의할 수 있습니다.

* 사용자 **지정 앱 동의 정책** -사용자 동의가 적용 되는 조건에 대 한 더 많은 옵션을 위해 사용자 [지정 앱 동의 정책을 만들고](manage-app-consent-policies.md#create-a-custom-app-consent-policy)사용자 동의에 적용 되도록 구성할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure Portal을 통해 사용자 동의 설정을 구성하려면 다음을 수행합니다.

1. [전역 관리자](../roles/permissions-reference.md#global-administrator--company-administrator)로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **사용자 동의 설정**을 선택합니다.
1. **애플리케이션에 대한 사용자 동의**에서 모든 사용자에 대해 구성할 동의 설정을 선택합니다.
1. **저장**을 선택하여 설정을 저장합니다.

:::image type="content" source="media/configure-user-consent/setting-for-all-users.png" alt-text="사용자 동의 설정":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

최신 Azure AD PowerShell 미리 보기 모듈인 [AzureADPreview](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview&preserve-view=true)를 사용 하 여 응용 프로그램에 대 한 사용자 동의를 제어 하는 앱 승인 정책을 선택할 수 있습니다.

#### <a name="disable-user-consent"></a>사용자 동의 사용 안 함

사용자 동의를 사용 하지 않도록 설정 하려면 사용자 동의가 다음과 같이 비어 있는 동의 정책을 설정 합니다.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @()
  ```

#### <a name="allow-user-consent-subject-to-an-app-consent-policy"></a>앱 동의 정책에 따라 사용자 동의 허용

사용자 동의를 허용 하려면 앱에 대 한 동의를 부여 하는 사용자의 권한 부여를 제어 하는 앱 동의 정책을 선택 합니다.

  ```powershell
  Set-AzureADMSAuthorizationPolicy `
     -Id "authorizationPolicy" `
     -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.{consent-policy-id}")
  ```

을 `{consent-policy-id}` 적용할 정책의 ID로 바꿉니다. 만든 [사용자 지정 앱 동의 정책을](manage-app-consent-policies.md#create-a-custom-app-consent-policy) 선택 하거나 다음 기본 제공 정책 중에서 선택할 수 있습니다.

| ID | 설명 |
|:---|:------------|
| microsoft-사용자-기본-낮음 | **선택한 권한에 대해 확인 된 게시자의 앱에 대 한 사용자 동의 허용**<br /> 사용자가 테 넌 트에 등록 된 앱 및 앱에 대 한 제한 된 사용자 동의가 허용 되며, "낮은 영향"으로 분류 한 사용 권한에 대해서만 허용 됩니다. 사용자가 동의할 수 있는 권한을 선택 하려면 [사용 권한을 분류](configure-permission-classifications.md) 해야 합니다. |
| microsoft-사용자-기본-레거시 | **앱에 대 한 사용자 동의 허용**<br /> 이 옵션을 사용 하면 모든 사용자가 모든 응용 프로그램에 대해 관리자 동의가 필요 하지 않은 모든 사용 권한에 동의할 수 있습니다. |
  
예를 들어 기본 제공 정책에 따라 사용자 동의를 사용 하도록 설정 하려면 `microsoft-user-default-low` 다음을 수행 합니다.

```powershell
Set-AzureADMSAuthorizationPolicy `
   -Id "authorizationPolicy" `
   -PermissionGrantPolicyIdsAssignedToDefaultUserRole @("managePermissionGrantsForSelf.microsoft-user-default-low")
```

---

> [!TIP]
> 사용자가 동의할 수 없는 응용 프로그램에 대 한 관리자의 검토 및 승인을 사용자가 허용할 수 있도록 관리자 [동의 워크플로를 사용 하도록 설정](configure-admin-consent-workflow.md) 합니다. 예를 들어 사용자 동의가 사용 하지 않도록 설정 되어 있거나 응용 프로그램에서 사용자에 게 허용 되지 않는 권한을 요청 하는 경우입니다.

## <a name="risk-based-step-up-consent"></a>위험 기반 단계 동의

위험 기반 상향 동의는 [불법 동의 요청](https://docs.microsoft.com/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants)을 보내는 악성 앱에 대한 사용자 노출을 줄이는 데 도움이 됩니다. Microsoft가 위험한 최종 사용자 동의 요청을 감지하면 이 요청에서 관리자 동의로의 상향을 대신 요구합니다. 이 기능은 기본적으로 사용하도록 설정되어 있지만 최종 사용자 동의가 허용된 경우에만 동작이 변경됩니다.

위험한 동의 요청이 감지되면 동의 프롬프트에 관리자 승인이 필요함을 나타내는 메시지가 표시됩니다. [관리자 동의 요청 워크플로](configure-admin-consent-workflow.md)가 설정된 경우 사용자는 동의 프롬프트에서 직접 관리자에게 추가 검토가 필요한 요청을 보낼 수 있습니다. 사용하도록 설정되어 있지 않으면 다음 메시지가 표시됩니다.

* **AADSTS90094:** &lt;clientAppDisplayName&gt;에는 관리자만 권한을 부여할 수 있는 조직의 리소스에 대한 액세스 권한이 필요합니다. 이 앱을 사용하려면 먼저 관리자에게 앱의 사용 권한을 부여하도록 요청하세요.

이 경우 감사 이벤트는 범주 "ApplicationManagement", 활동 유형 "애플리케이션에 동의" 및 상태 이유 "위험한 애플리케이션 감지됨"으로도 로깅됩니다.

> [!IMPORTANT]
> 관리자는 요청을 승인하기 전에 [모든 동의 요청을 신중하게 평가](manage-consent-requests.md#evaluating-a-request-for-tenant-wide-admin-consent)해야 합니다(특히 Microsoft에서 위험을 감지한 경우).

### <a name="disable-or-re-enable-risk-based-step-up-consent-using-powershell"></a>PowerShell을 사용하여 위험 기반 상향 동의 사용 안 함 또는 다시 사용

Azure AD PowerShell 미리 보기 모듈 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true)를 사용하여 Microsoft에서 위험을 감지하거나 이전에 사용하지 않도록 설정되어 다시 사용하도록 설정하는 경우에 필요한 관리자 동의로의 상향 기능을 사용하지 않도록 설정할 수 있습니다.

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 모듈을 사용하고 있는지 확인하세요. [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 모듈 및 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 모듈을 모두 설치한 경우 이 단계가 중요합니다.

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

    $riskBasedConsentEnabledValue = $settings.Values | ? { $_.Name -eq "BlockUserConsentForRiskyApps" }
    ```

1. 설정 값을 이해 합니다.

    | 설정       | 유형         | Description  |
    | ------------- | ------------ | ------------ |
    | _BlockUserConsentForRiskyApps_   | 부울 |  위험한 요청이 감지될 때 사용자 동의가 차단되는지 나타내는 플래그입니다. |

1. 원하는 구성의 설정 값을 업데이트 합니다.

    ```powershell
    # Disable risk-based step-up consent entirely
    $riskBasedConsentEnabledValue.Value = "False"
    ```

    ```powershell
    # Re-enable risk-based step-up consent, if disabled previously
    $riskBasedConsentEnabledValue.Value = "True"
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

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [사용자 승인 설정 구성](configure-user-consent.md)
* [앱 동의 정책 관리](manage-app-consent-policies.md)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.
* [StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
