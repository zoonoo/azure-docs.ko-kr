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
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f70a11165f6433e580fd857f2d5a620deb6640c0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91604338"
---
# <a name="configure-token-lifetime-policies-preview"></a>토큰 수명 정책 구성 (미리 보기)
앱, 서비스 주체 및 전체 조직의 토큰 수명을 만들고 관리할 수 있다면 Azure AD에서 다양한 시나리오가 가능합니다. 자세히 알아보려면 [Microsoft id 플랫폼에서 구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조 하세요. 

> [!IMPORTANT]
> 미리 보기 중에 고객의 의견을 확인 한 후 Azure AD 조건부 액세스에서 [인증 세션 관리 기능](../conditional-access/howto-conditional-access-session-lifetime.md) 을 구현 했습니다. 이 새로운 기능을 사용 하 여 로그인 빈도를 설정 하 여 새로 고침 토큰 수명을 구성할 수 있습니다. 5 월 30 2020 일 이후에는 새 테 넌 트가 구성 가능한 토큰 수명 정책을 사용 하 여 세션 및 새로 고침 토큰을 구성할 수 없습니다. 이후 몇 개월 이내에 사용 중단이 발생 합니다. 즉, 기존 세션을 중단 하 고 토큰 정책을 새로 고치는 것이 중지 됩니다. 사용 중단 후에도 액세스 토큰 수명을 구성할 수 있습니다.


이 섹션에서는 새 규칙을 적용하는 데 도움이 되는 몇 가지 일반적인 정책 시나리오를 살펴보겠습니다.

* 토큰 수명
* 토큰 최대 비활성 시간
* 토큰 최대 기간

이 예제에서는 다음 작업을 수행하는 방법을 배울 수 있습니다.

* 조직의 기본 정책 관리
* 웹 로그인에 대한 정책 만들기
* web API를 호출하는 네이티브 앱에 대한 정책 만들기
* 고급 정책 관리

## <a name="prerequisites"></a>사전 요구 사항
다음 예제에서는 앱, 서비스 주체 및 조직 전체에 대한 정책을 만들고, 업데이트하고, 연결하고, 삭제해 보겠습니다. Azure AD를 처음 사용 하는 경우 이러한 예제를 진행 하기 전에 [AZURE ad 테 넌 트를 가져오는 방법](quickstart-create-new-tenant.md) 에 대해 알아보는 것이 좋습니다.  

시작하려면 다음 단계를 수행합니다.

1. 최신 [AZURE AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드 합니다.
2. `Connect` 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. 조직에서 만든 모든 정책을 확인하려면 다음 명령을 실행합니다. 다음 시나리오에서 대부분의 작업 후에 이 명령을 실행합니다. 이 명령을 실행하면 정책의 ** **을(를) 가져오는 데도 도움이 됩니다.

    ```powershell
    Get-AzureADPolicy
    ```

## <a name="manage-an-organizations-default-policy"></a>조직의 기본 정책 관리
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

    1. 새 정책을 보고 정책의 **ObjectId**를 가져오려면 다음 명령을 실행합니다.

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. 정책을 업데이트합니다.

    이 예에서 설정한 첫 번째 정책이 서비스에 필요한 만큼 엄격한지 확인할 수 있습니다. 2 일 후에 만료 되는 단일 단계 새로 고침 토큰을 설정 하려면 다음 명령을 실행 합니다.

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

## <a name="create-a-policy-for-web-sign-in"></a>웹 로그인에 대한 정책 만들기

이 예에서는 사용자가 웹앱에 보다 자주 인증하도록 요구하는 정책을 만듭니다. 이 정책은 액세스/ID 토큰의 수명 및 다단계 세션 토큰의 최대 기간을 웹앱의 서비스 주체로 설정합니다.

1. 토큰 수명 정책을 만듭니다.

    웹 로그인에 대한 이 정책은 액세스/ID 토큰 수명 및 최대 단일 단계 세션 토큰 기간을 2시간으로 설정합니다.

    1. 정책을 만들려면 [new-azureadpolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. 새 정책을 보고 정책 **ObjectId**를 가져오려면 [new-azureadpolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행 합니다.

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

## <a name="create-a-policy-for-a-native-app-that-calls-a-web-api"></a>web API를 호출하는 네이티브 앱에 대한 정책 만들기
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

1. web API에 정책을 할당합니다. 애플리케이션의 **ObjectId**도 가져와야 합니다. [Azureadapplication.applicationid](/powershell/module/azuread/get-azureadapplication) cmdlet을 사용 하 여 앱의 **ObjectId**를 찾거나 [Azure Portal](https://portal.azure.com/)를 사용 합니다.

    앱의 **ObjectId** 를 가져오고 정책을 할당 합니다.

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

## <a name="manage-an-advanced-policy"></a>고급 정책 관리
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