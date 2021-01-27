---
title: 토큰에 대 한 수명 설정
titleSuffix: Microsoft identity platform
description: Microsoft id 플랫폼에서 발급 한 토큰의 수명을 설정 하는 방법에 대해 알아봅니다. 조직의 기본 정책을 관리 하 고, 웹 로그인에 대 한 정책을 만들고, web API를 호출 하는 네이티브 앱에 대 한 정책을 만들고, 고급 정책을 관리 하는 방법에 대해 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 01/04/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: 2529c6c3b0f9d188e1ce8062c05f62f3e980ef50
ms.sourcegitcommit: fc8ce6ff76e64486d5acd7be24faf819f0a7be1d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/26/2021
ms.locfileid: "98805214"
---
# <a name="configure-token-lifetime-policies-preview"></a>토큰 수명 정책 구성 (미리 보기)
Microsoft id 플랫폼에서 발급 한 액세스, SAML 또는 ID 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 애플리케이션 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다. 자세한 내용은 [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조 하세요.

이 섹션에서는 토큰 수명에 대 한 새 규칙을 적용 하는 데 도움이 될 수 있는 일반적인 정책 시나리오를 안내 합니다. 예제에서는 사용자가 웹 앱에서 더 자주 인증 하도록 요구 하는 정책을 만드는 방법에 대해 알아봅니다.

## <a name="get-started"></a>시작하기
시작하려면 다음 단계를 수행합니다.

1. 최신 [AZURE AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드 합니다.
1. `Connect` 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

    ```powershell
    Connect-AzureAD -Confirm
    ```

1. 조직에서 만든 모든 정책을 확인 하려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.  위에 나열 된 기본값과 다른 정의 된 속성 값이 있는 결과는 사용 중지 범위에 있습니다.

    ```powershell
    Get-AzureADPolicy -All
    ```

1. 지정 된 특정 정책에 연결 된 앱 및 서비스 주체를 확인 하려면 **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** 를 정책 id로 바꿔서 다음 [AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다. 그런 다음 조건부 액세스 로그인 빈도를 구성할 지 아니면 Azure AD 기본값을 유지할지 결정할 수 있습니다.

    ```powershell
    Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
    ```

테 넌 트에 새로 고침 및 세션 토큰 구성 속성에 대 한 사용자 지정 값을 정의 하는 정책이 있는 경우 이러한 정책을 위에서 설명한 기본값을 반영 하는 값으로 업데이트 하는 것이 좋습니다. 변경 내용이 없는 경우 Azure AD는 기본값을 자동으로 적용 합니다.

## <a name="create-a-policy-for-web-sign-in"></a>웹 로그인에 대한 정책 만들기

이 예에서는 사용자가 웹앱에 보다 자주 인증하도록 요구하는 정책을 만듭니다. 이 정책은 액세스/ID 토큰의 수명 및 다단계 세션 토큰의 최대 기간을 웹앱의 서비스 주체로 설정합니다.

1. 토큰 수명 정책을 만듭니다.

    웹 로그인에 대한 이 정책은 액세스/ID 토큰 수명 및 최대 단일 단계 세션 토큰 기간을 2시간으로 설정합니다.

    1. 정책을 만들려면 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 새 정책을 보고 정책 **ObjectId** 를 가져오려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 **ObjectId** 도 가져와야 합니다.

    1. [Get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 사용 하 여 조직의 모든 서비스 주체 또는 단일 서비스 주체를 확인 합니다.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. 서비스 사용자가 있는 경우 [AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

## <a name="create-token-lifetime-policies-for-refresh-and-session-tokens"></a>새로 고침 및 세션 토큰에 대 한 토큰 수명 정책 만들기
> [!IMPORTANT]
> 2020 년 5 월 새 테 넌 트는 새로 고침 및 세션 토큰 수명을 구성할 수 없습니다.  기존 구성이 있는 테 넌 트는 2021 년 1 월 30 일까 지 새로 고침 및 세션 토큰 정책을 수정할 수 있습니다.  Azure Active Directory은 2021 년 1 월 30 일 이후에 정책에서 기존 새로 고침 및 세션 토큰 구성을 다시 설정 하는 것을 중지 합니다. 사용 중지 후에도 액세스, SAML 및 ID 토큰 수명을 구성할 수 있습니다.
>
> 사용자가 다시 로그인 하도록 요청 하는 시간 간격을 계속 정의 해야 하는 경우 조건부 액세스에서 로그인 빈도를 구성 합니다. 조건부 액세스에 대 한 자세한 내용은 [조건부 액세스를 사용 하 여 인증 세션 관리 구성](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조 하세요.
>
> 사용 중지 날짜 후 조건부 액세스를 사용 하지 않으려는 경우 새로 고침 및 세션 토큰이 해당 날짜의 [기본 구성](active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties-after-the-retirement) 으로 설정 되 고 더 이상 수명을 변경할 수 없게 됩니다.

### <a name="manage-an-organizations-default-policy"></a>조직의 기본 정책 관리
이 예제에서는 사용자가 전체 조직에서 더 덜 자주 로그인 할 수 있도록 하는 정책을 만듭니다. 이렇게 하려면 조직 전체에 적용 되는 단일 단계 새로 고침 토큰에 대 한 토큰 수명 정책을 만듭니다. 이 정책은 조직의 모든 애플리케이션과 아직 정책이 설정되지 않은 각 서비스 주체에 적용됩니다.

1. 토큰 수명 정책을 만듭니다.

    1. 단일 단계 새로 고침 토큰을 "-취소 됨"으로 설정 합니다. 이 토큰은 액세스가 해지될 때까지 만료되지 않습니다. 다음 정책 정의를 만듭니다.

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. 정책을 만들려면 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 공백을 제거 하려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. 새 정책을 보고 정책의 **ObjectId** 를 가져오려면 다음 명령을 실행합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 정책을 업데이트합니다.

    이 예에서 설정한 첫 번째 정책이 서비스에 필요한 만큼 엄격한지 확인할 수 있습니다. 2 일 후에 만료 되는 단일 단계 새로 고침 토큰을 설정 하려면 다음 명령을 실행 합니다.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>web API를 호출하는 네이티브 앱에 대한 정책 만들기
이 예에서는 사용자가 보다 적게 인증하도록 요구하는 정책을 만듭니다. 또한 이 정책은 사용자가 다시 인증해야 할 때까지 걸리는 비활성 시간을 연장합니다. 이 정책은 web API에 적용됩니다. 네이티브 앱이 리소스로 web API를 요청하면 이 정책이 적용됩니다.

1. 토큰 수명 정책을 만듭니다.

    1. Web API에 대 한 엄격한 정책을 만들려면 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 새 정책을 보려면 다음 명령을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. web API에 정책을 할당합니다. 애플리케이션의 **ObjectId** 도 가져와야 합니다. [Azureadapplication.applicationid](/powershell/module/azuread/get-azureadapplication) cmdlet을 사용 하 여 앱의 **ObjectId** 를 찾거나 [Azure Portal](https://portal.azure.com/)를 사용 합니다.

    앱의 **ObjectId** 를 가져오고 정책을 할당 합니다.

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="manage-an-advanced-policy"></a>고급 정책 관리
이 예에서는 몇 가지 정책을 만들어 우선 순위 시스템의 작동 방식을 알아봅니다. 여러 개체에 적용 되는 여러 정책을 관리 하는 방법에 대해서도 알아봅니다.

1. 토큰 수명 정책을 만듭니다.

    1. 단일 단계 새로 고침 토큰 수명을 30 일로 설정 하는 조직 기본 정책을 만들려면 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. 새 정책을 확인 하려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 서비스 주체에게 정책을 할당합니다.

    이제 조직 전체에 적용되는 정책이 생겼습니다. 특정 서비스 주체에 대해 이 30일 정책을 유지하지만 조직 기본 정책을 "until-revoked"의 상한으로 변경하고 싶은 경우가 있습니다.

    1. 조직의 모든 서비스 주체를 보려면 [get-azureadserviceprincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 사용 합니다.

    1. 서비스 사용자가 있는 경우 [AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. `IsOrganizationDefault` 플래그를 false로 설정합니다.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. 새로운 조직 기본 정책을 만듭니다.

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    이제 서비스 주체에 연결된 원래 정책과 조직 기본 정책으로 설정된 새 정책이 있습니다. 서비스 주체에 적용되는 정책은 조직 기본 정책보다 우선한다는 점을 기억해야 합니다.

## <a name="next-steps"></a>다음 단계
Azure AD 조건부 액세스의 [인증 세션 관리 기능](../conditional-access/howto-conditional-access-session-lifetime.md) 에 대해 알아봅니다.
