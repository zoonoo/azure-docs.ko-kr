---
title: Azure AD에서 앱 동의 정책 관리
description: 기본 제공 및 사용자 지정 앱 동의 정책을 관리 하 여 동의를 부여할 수 있는 시기를 제어 하는 방법을 알아봅니다.
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
ms.openlocfilehash: 59e00d2bb47826bb4bfa381c42db551f44d84b71
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2020
ms.locfileid: "92427606"
---
# <a name="manage-app-consent-policies"></a>앱 동의 정책 관리

Azure AD PowerShell을 사용 하면 앱 동의 정책을 보고 관리할 수 있습니다.

앱 승인 정책은 하나 이상의 "포함" 조건 집합과 0 개 이상의 "제외" 조건 집합으로 구성 됩니다. 앱 동의 정책에서 고려 되는 이벤트는 하나 *이상의* "포함" 조건 집합과 일치 해야 하며, "제외" 조건 집합 *을 많이 설정* 하지 않아야 합니다.

각 조건 집합은 여러 조건으로 구성 됩니다. 조건 집합과 일치 하는 이벤트의 경우 조건 집합의 *모든* 조건을 충족 해야 합니다.

ID가 "microsoft-"로 시작 하는 앱 승인 정책은 기본 제공 정책입니다. 이러한 기본 제공 정책 중 일부는 기존 기본 제공 디렉터리 역할에 사용 됩니다. 예를 들어 `microsoft-application-admin` 앱 승인 정책은 응용 프로그램 관리자 및 클라우드 응용 프로그램 관리자 역할이 테 넌 트 전체 관리자 동의를 허용할 수 있는 조건을 설명 합니다. 기본 제공 정책은 사용자 지정 디렉터리 역할에서 사용 하 고 사용자 동의 설정을 구성할 수 있지만 편집 하거나 삭제할 수는 없습니다.

## <a name="pre-requisites"></a>필수 구성 요소

1. [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 모듈을 사용하고 있는지 확인하세요. [AzureAD](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true) 모듈 및 [AzureADPreview](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true) 모듈을 모두 설치한 경우 이 단계가 중요합니다.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>기존 앱 동의 정책 나열

조직에서 기존 앱 동의 정책에 대해 잘 알고 있는 것이 좋습니다.

1. 모든 앱 승인 정책을 나열 합니다.

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. 정책의 "포함" 조건 집합 보기:

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. "제외" 조건 집합을 표시 합니다.

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -Id "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>사용자 지정 앱 동의 정책 만들기

사용자 지정 앱 동의 정책을 만들려면 다음 단계를 따르세요.

1. 비어 있는 새 앱 동의 정책을 만듭니다.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. "포함" 조건 집합을 추가 합니다.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   이 단계를 반복 하 여 추가 "포함" 조건 집합을 추가 합니다.

1. 필요에 따라 "제외" 조건 집합을 추가 합니다.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   추가 "제외" 조건 집합을 추가 하려면이 단계를 반복 합니다.

앱 동의 정책을 만든 후에는이 정책에 따라 [사용자 동의를 허용할](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy) 수 있습니다.

## <a name="delete-a-custom-app-consent-policy"></a>사용자 지정 앱 동의 정책 삭제

1. 다음은 사용자 지정 앱 동의 정책을 삭제 하는 방법을 보여 줍니다. **이 작업은 취소할 수 없습니다.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> 삭제 된 앱 동의 정책을 복원할 수 없습니다. 사용자 지정 앱 동의 정책을 실수로 삭제 한 경우에는 정책을 다시 만들어야 합니다.

---

### <a name="supported-conditions"></a>지원 되는 조건

다음 표에서는 앱 승인 정책에 대해 지원 되는 조건 목록을 제공 합니다.

| 조건 | 설명|
|:---------------|:----------|
| 대 여 분류 | 부여 되는 권한에 대 한 [사용 권한 분류](configure-permission-classifications.md) 이거나, 사용 권한 분류 (분류 되지 않은 사용 권한 포함)와 일치 하는 "모두"입니다. 기본값은 "all"입니다. |
| 고 유형 | 부여 되는 사용 권한의 사용 권한 유형입니다. 응용 프로그램 사용 권한 (예: 앱 역할) 또는 위임 된 권한에 대해 "위임 됨"에 "응용 프로그램"을 사용 합니다. <br><br>**참고**: "delegatedUserConsentable" 값은 관리자 동의를 요구 하도록 API 게시자에서 구성 되지 않은 위임 된 사용 권한을 나타냅니다 .이 값은 기본 제공 권한 부여 정책에 사용 될 수 있지만 사용자 지정 권한 부여 정책에서는 사용할 수 없습니다. 필수 요소. |
| ResourceApplication | 사용 권한이 부여 되는 리소스 응용 프로그램 (예: API)의 **AppId** 또는 리소스 응용 프로그램 또는 API와 일치 하는 "any"입니다. 기본값은 "any"입니다. |
| 사용 권한 | 와 일치 하는 특정 사용 권한에 대 한 권한 Id의 목록 또는 모든 사용 권한과 일치 하는 단일 값 "모든"의 목록입니다. 기본값은 "all" 단일 값입니다. <ul><li>위임 된 권한 Id는 API의 ServicePrincipal 개체의 **OAuth2Permissions** 속성에서 찾을 수 있습니다.</li><li>응용 프로그램 권한 Id는 API의 ServicePrincipal 개체의 **Approles** 속성에서 찾을 수 있습니다.</li></ol> |
| ClientApplicationIds | 와 일치 하는 클라이언트 응용 프로그램에 대 한 **AppId** 값의 목록 이거나, 모든 클라이언트 응용 프로그램과 일치 하는 단일 값 "모두"가 있는 목록입니다. 기본값은 "all" 단일 값입니다. |
| ClientApplicationTenantIds | 클라이언트 응용 프로그램이 등록 된 Azure Active Directory 테 넌 트 Id의 목록 또는 모든 테 넌 트에 등록 된 클라이언트 앱과 일치 하는 단일 값 "모두"를 포함 하는 목록입니다. 기본값은 "all" 단일 값입니다. |
| Clientapplicationsd Ds | 클라이언트 응용 프로그램의 [확인 된 게시자](../develop/publisher-verification-overview.md) 에 대 한 MICROSOFT 파트너 네트워크 (MPN) id 목록 또는 모든 게시자의 클라이언트 앱과 일치 하는 단일 값 "모두"가 포함 된 목록입니다. 기본값은 "all" 단일 값입니다. |
| ClientApplicationsFromVerifiedPublisherOnly | `$true`클라이언트 응용 프로그램에서 [확인 된 게시자](../develop/publisher-verification-overview.md)와만 일치 하도록 설정 합니다. 확인 된 `$false` 게시자가 없는 경우에도 모든 클라이언트 앱에서 일치 하도록로 설정 합니다. 기본값은 `$false`입니다. |

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [사용자 승인 설정 구성](configure-user-consent.md)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/active-directory-v2-scopes.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.
* [StackOverflow의 Azure AD](https://stackoverflow.com/questions/tagged/azure-active-directory)
