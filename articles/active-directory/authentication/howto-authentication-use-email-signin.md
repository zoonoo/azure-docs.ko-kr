---
title: Azure Active Directory의 대체 로그인 ID로 메일 로그인 사용
description: 사용자가 대체 로그인 ID로 메일 주소를 사용하여 Azure Active Directory에 로그인하도록 구성하고 설정하는 방법을 알아봅니다(미리 보기).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 06/24/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: 0a7048e79ddd4a86d7e14e573cf5b8556f462f03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85550330"
---
# <a name="sign-in-to-azure-active-directory-using-email-as-an-alternate-login-id-preview"></a>대체 로그인 ID (미리 보기)로 전자 메일을 사용 하 여 Azure Active Directory 로그인

많은 조직에서 사용자가 온-프레미스 디렉터리 환경과 동일한 자격 증명을 사용 하 여 Azure Active Directory (Azure AD)에 로그인 하도록 하려고 합니다. 하이브리드 인증이라고 하는 이 방법을 사용하면 사용자는 한 세트의 자격 증명만 기억하면 됩니다.

일부 조직에서는 다음과 같은 이유로 하이브리드 인증으로 전환하지 않습니다.

* 기본적으로 Azure AD UPN (사용자 계정 이름)은 온-프레미스 디렉터리와 동일한 UPN으로 설정 됩니다.
* Azure AD UPN을 변경 하면 특정 응용 프로그램 및 서비스에서 문제를 일으킬 수 있는 온-프레미스 및 Azure AD 환경 간에 잘못 된 일치가 생성 됩니다.
* 비즈니스 또는 규정 준수 때문에 조직은 온-프레미스 UPN을 사용 하 여 Azure AD에 로그인 하지 않으려고 합니다.

하이브리드 인증으로 이동 하는 데 도움이 되도록 이제 사용자가 확인 된 도메인에서 대체 로그인 ID로 전자 메일에 로그인 할 수 있도록 Azure AD를 구성할 수 있습니다. 예를 들어 *Contoso*가 레거시 `balas@contoso.com` UPN을 사용한 로그인을 지원하는 대신 *Fabrikam*으로 리브랜딩되었다면 이제 메일을 대체 로그인 ID로 사용할 수 있습니다. 응용 프로그램 또는 서비스에 액세스 하기 위해 사용자는 할당 된 전자 메일 (예:)을 사용 하 여 Azure AD에 로그인 `balas@fabrikam.com` 합니다.

> [!NOTE]
> 메일을 대체 로그인 ID로 사용하여 Azure AD에 로그인하는 것은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Azure AD 로그인 방법 개요

Azure AD에 로그인 하려면 사용자에 게 계정을 고유 하 게 식별 하는 이름을 입력 합니다. 지금까지 Azure AD UPN을 로그인 이름으로 사용할 수 있었습니다.

온-프레미스 UPN이 사용자의 기본 설정 된 로그인 전자 메일 인 조직의 경우이 접근 방식을 사용 하는 것이 좋습니다. 이러한 조직은 Azure AD UPN을 온-프레미스 UPN과 정확히 동일한 값으로 설정 하 고 사용자는 일관 된 로그인 환경을 제공 합니다.

그러나 일부 조직에서는 온-프레미스 UPN을 로그인 이름으로 사용 하지 않습니다. 온-프레미스 환경에서 대체 로그인 ID를 사용 하 여 로그인 할 수 있도록 로컬 AD DS를 구성 합니다. Azure ad UPN을 온-프레미스 UPN과 동일한 값으로 설정 하는 것은 Azure AD에서 사용자가 해당 값을 사용 하 여 로그인 해야 하기 때문에 옵션이 아닙니다.

이 문제에 대 한 일반적인 해결 방법은 사용자가 로그인 할 것으로 예상 하는 전자 메일 주소로 Azure AD UPN을 설정 하는 것입니다. 이 방법은 작동 하지만, 온-프레미스 AD와 Azure AD 간에 다른 Upn이 발생 하 고,이 구성은 모든 Microsoft 365 워크 로드와 호환 되지 않습니다.

다른 방법은 Azure AD와 온-프레미스 Upn을 동일한 값으로 동기화 한 다음 사용자가 확인 된 전자 메일을 사용 하 여 Azure AD에 로그인 할 수 있도록 Azure AD를 구성 하는 것입니다. 이 기능을 제공 하기 위해 온-프레미스 디렉터리의 사용자 *ProxyAddresses* 특성에 하나 이상의 전자 메일 주소를 정의 합니다. *ProxyAddresses* 는 Azure AD Connect을 사용 하 여 Azure AD에 자동으로 동기화 됩니다.

## <a name="preview-limitations"></a>미리 보기 제한 사항

현재 미리 보기 상태에서는 UPN이 아닌 전자 메일을 대체 로그인 ID로 사용 하 여 사용자가 로그인 할 때 다음과 같은 제한 사항이 적용 됩니다.

* UPN이 아닌 전자 메일을 사용 하 여 로그인 한 경우에도 사용자에 게 UPN이 표시 될 수 있습니다. 다음 예제 동작을 볼 수 있습니다.
    * 사용자에 게 Azure AD 로그인을 사용 하 여 로그인 할 때 UPN을 사용 하 여 로그인 하 라는 메시지가 표시 됩니다 `login_hint=<non-UPN email>` .
    * 사용자가 UPN이 아닌 전자 메일을 사용 하 여 로그인 하 고 잘못 된 암호를 입력 하면 *"암호 입력"* 페이지가 변경 되어 upn이 표시 됩니다.
    * 및 Microsoft Office와 같은 일부 Microsoft 사이트 및 앱에서 [https://portal.azure.com](https://portal.azure.com) , 일반적으로 오른쪽 위에 표시 되는 **계정 관리자** 컨트롤은 로그인 하는 데 사용 되는 upn이 아닌 전자 메일 대신 사용자의 upn을 표시할 수 있습니다.

* 일부 흐름은 현재 다음과 같은 비 UPN 메일과 호환 되지 않습니다.
    * Id 보호는 현재 전자 메일 대체 로그인 Id와 일치 하지 않는 *자격 증명* 위험 검색을 사용 합니다. 이 위험 감지는 UPN을 사용 하 여 누출 된 자격 증명을 일치 시킵니다. 자세한 내용은 [Azure AD ID 보호 위험 검색 및 수정][identity-protection]을 참조 하세요.
    * 대체 로그인 ID 전자 메일에 전송 된 B2B 초대는 완전히 지원 되지 않습니다. 전자 메일에 대체 로그인 ID로 보낸 초대를 수락한 후에는 테 넌 트 끝점의 사용자에 대해 대체 전자 메일을 사용 하 여 로그인 할 수 없습니다.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>로그인 메일 주소를 Azure AD에 동기화

기존 AD DS(Active Directory Domain Services) 또는 AD FS(Active Directory Federation Services) 인증은 네트워크에서 직접 수행되며 AD DS 인프라에서 처리됩니다. 하이브리드 인증을 사용하면 사용자가 대신 Azure AD에 직접 로그인할 수 있습니다.

이 하이브리드 인증 방법을 지원하려면 [Azure AD Connect][azure-ad-connect]를 사용하여 온-프레미스 AD DS 환경을 Azure AD에 동기화하고 PHS(암호 해시 동기화) 또는 PTA(통과 인증)를 사용하도록 구성해야 합니다.

두 가지 구성 옵션에서 모두 사용자가 사용자 이름과 암호를 Azure AD에 제출하면 Azure AD가 자격 증명의 유효성을 검사하고 티켓을 발급합니다. 사용자가 Azure AD에 로그인하면 조직에서 AD FS 인프라를 호스트하고 관리할 필요가 없어집니다.

![암호 해시 동기화를 사용하는 Azure AD 하이브리드 ID 다이어그램](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![통과 인증을 사용하는 Azure AD 하이브리드 ID 다이어그램](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Azure AD Connect에 의해 자동으로 동기화되는 사용자 특성 중 하나는 *ProxyAddresses*입니다. 사용자의 메일 주소가 *ProxyAddresses* 특성의 일부로 온-프레미스 AD DS 환경에 정의된 경우 이 특성이 Azure AD에 자동으로 동기화됩니다. 이 메일 주소는 Azure AD 로그인 프로세스에서 대체 로그인 ID로 직접 사용될 수 있습니다.

> [!IMPORTANT]
> 테넌트에 대해 확인된 도메인의 메일만 Azure AD에 동기화됩니다. 각 Azure AD 테넌트에는 소유권이 입증되어 테넌트에 고유하게 바인딩된 확인된 도메인이 하나 이상 있습니다.
>
> 자세한 내용은 [Azure AD에서 사용자 지정 도메인 이름 추가 및 확인][verify-domain]을 참조하세요.

자세한 내용은 [Azure AD 하이브리드 ID 솔루션에 적합한 인증 방법 선택][hybrid-auth-methods]을 참조하세요.

## <a name="enable-user-sign-in-with-an-email-address"></a>메일 주소를 사용한 사용자 로그인 사용

*ProxyAddresses* 특성이 적용된 사용자가 Azure AD Connect를 사용하여 Azure AD와 동기화되면 사용자가 테넌트의 대체 로그인 ID로 메일을 사용하여 로그인하는 기능을 사용하도록 설정해야 합니다. 이 기능은 Azure AD 로그인 서버에 UPN 값을 기준으로 로그인 이름을 확인하고 *ProxyAddresses* 값을 기준으로 메일 주소를 확인하라고 명령합니다.

미리 보기 단계에서는 오직 PowerShell만 사용하여 대체 로그인 ID 기능으로 메일을 사용한 로그인을 사용하도록 설정할 수 있습니다. 다음 단계를 완료하려면 *테넌트 관리자* 권한이 필요합니다.

1. 관리자 권한으로 PowerShell 세션을 연 다음 [Install-Module][Install-Module] cmdlet을 사용하여 *AzureADPreview* 모듈을 설치합니다.

    ```powershell
    Install-Module AzureADPreview
    ```

    메시지가 표시되면 **Y**를 선택하여 NuGet을 설치하거나 신뢰할 수 없는 리포지토리에서 설치합니다.

1. [Connect-AzureAD][Connect-AzureAD] cmdlet을 사용하여 Azure AD 테넌트에 *테넌트 관리자*로 로그인합니다.

    ```powershell
    Connect-AzureAD
    ```

    이 명령은 계정, 환경 및 테넌트 ID에 대한 정보를 반환합니다.

1. 다음과 같이 [Get-AzureADPolicy][Get-AzureADPolicy] cmdlet을 사용하여 *HomeRealmDiscoveryPolicy* 정책이 테넌트에 이미 있는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. 현재 구성된 정책이 없는 경우 이 명령은 아무것도 반환하지 않습니다. 정책이 반환되면 이 단계를 건너뛰고 다음 단계로 넘어가서 기존 정책을 업데이트합니다.

    테넌트에 *HomeRealmDiscoveryPolicy* 정책을 추가하려면 다음 예에서와 같이 [New-AzureADPolicy][New-AzureADPolicy] cmdlet을 사용하여 *AlternateIdLogin* 특성을 *“Enabled”: true*로 설정합니다.

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    정책을 성공적으로 만들면 다음 예제 출력에서와 같이 명령이 정책 ID를 반환합니다.

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. 구성된 정책이 이미 있는 경우 다음 예제의 정책 출력에서와 같이  *AlternateIdLogin*  특성을 사용하도록 설정했는지 확인합니다.

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    정책이 있지만 *AlternateIdLogin* 특성이 없거나 사용하도록 설정되지 않은 경우, 또는 정책에 유지하고 싶은 다른 특성이 존재하는 경우 [Set-AzureADPolicy][Set-AzureADPolicy] cmdlet을 사용하여 기존 정책을 업데이트합니다.

    > [!IMPORTANT]
    > 정책을 업데이트할 때 이전 설정과 새로운  *AlternateIdLogin* 특성이 포함되었는지 확인하세요.

    다음 예제에서는  *AlternateIdLogin* 특성을 추가하고  *AllowCloudPasswordValidation* 특성을 유지합니다(이 특성은 이미 설정되었을 수 있습니다).

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    업데이트된 정책에 변경 내용이 표시되며 *AlternateIdLogin* 특성이 이제 사용하도록 설정되었는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>메일을 사용하여 사용자 로그인 테스트

사용자가 메일을 사용하여 로그인할 수 있는지 테스트하려면 [https://myprofile.microsoft.com][my-profile]으로 이동하여 `balas@contoso.com` 과 같은 UPN이 아닌 `balas@fabrikam.com` 과 같은 메일 주소 기반 사용자 계정으로 로그인합니다. 로그인 환경은 UPN 기반 로그인 이벤트와 모양과 느낌이 동일합니다.

## <a name="troubleshoot"></a>문제 해결

메일 주소를 사용한 로그인 이벤트에 문제가 있는 경우 다음과 같은 문제 해결 단계를 검토하세요.

1. 사용자 계정에서 온-프레미스 AD DS 환경의 *ProxyAddresses* 특성에 메일 주소가 설정되었는지 확인합니다.
1. Azure AD Connect가 구성되어 있고 온-프레미스 AD DS 환경에서 Azure AD로 사용자 계정을 성공적으로 동기화하는지 확인합니다.
1. Azure AD *HomeRealmDiscoveryPolicy* 정책의 *AlternateIdLogin* 특성이 *“Enabled”: true*로 설정되었는지 확인합니다.

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>다음 단계

Azure AD 앱 프록시 또는 Azure AD Domain Services와 같은 하이브리드 ID에 대해 자세히 알아보려면 [온-프레미스 워크로드의 액세스 및 관리에 Azure AD 하이브리드 ID 사용][hybrid-overview]을 참조하세요.

하이브리드 ID 작업에 대한 자세한 내용은 [암호 해시 동기화][phs-overview] 또는 [통과 인증][pta-overview] 동기화가 작동하는 방식을 참조하세요.

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md
[identity-protection]: ../identity-protection/overview-identity-protection.md#risk-detection-and-remediation

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
