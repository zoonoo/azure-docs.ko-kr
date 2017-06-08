---
title: "Azure Active Directory B2C: 사용자 지정 정책을 사용하여 Azure AD 공급자 추가 | Microsoft Docs"
description: "Azure Active Directory B2C 사용자 지정 정책에 대한 항목"
services: active-directory-b2c
documentationcenter: 
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 31f0dfe5-1ad0-4a25-a53b-8acc71bcea72
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: parakhj
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 652a2eb0db0e41c4706e06cd0cb2e97ce1eb6ab2
ms.contentlocale: ko-kr
ms.lasthandoff: 05/12/2017


---
# <a name="azure-active-directory-b2c-log-in-using-azure-ad-accounts"></a>Azure Active Directory B2C: Azure AD 계정을 사용하여 로그인

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

이 문서에서는 [사용자 지정 정책](active-directory-b2c-overview-custom.md)을 사용하여 특정 Azure AD 조직의 사용자에게 로그인할 수 있도록 하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건

[사용자 지정 정책을 사용하여 시작](active-directory-b2c-get-started-custom.md)하는 방법을 설명하는 단계를 모두 완료했는지 확인합니다.

다음 내용이 포함됩니다.

1. Azure AD B2C 테넌트 만들기
1. Azure AD B2C 응용 프로그램 만들기
1. 두 개의 정책 엔진 응용 프로그램 등록
1. 키 설정
1. 시작 팩 설정

## <a name="create-an-azure-ad-app"></a>Azure AD 앱 만들기

특정 Azure AD 조직의 사용자에게 로그인하도록 설정하려면 조직 Azure AD 테넌트 내에서 응용 프로그램을 등록해야 합니다.

>[!NOTE]
> 조직 Azure AD 테넌트를 `contoso.com`로 참조하고 Azure AD B2C 테넌트를 `fabrikamb2c.onmicrosoft.com`로 참조합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 오른쪽 위에서 계정을 클릭하고 **디렉터리** 목록에서 응용 프로그램을 등록하려는 조직 Azure AD 테넌트를 선택합니다(즉, contoso.com).
1. 왼쪽 탐색 창에서 **더 많은 서비스**를 클릭하고 **앱 등록**을 검색합니다.
1. **새 응용 프로그램 등록**을 선택합니다.
1. 응용 프로그램의 **이름**을 입력합니다(예: Azure AD B2C 앱).
1. 응용 프로그램 종류의 **웹앱/API**를 입력합니다.
1. '로그온 URL'에서 아래 URL을 입력합니다. 여기서 `yourtenant`는 Azure AD B2C 테넌트의 이름(즉, fabrikamb2c.onmicrosoft.com)으로 바꿔야 합니다.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. **응용 프로그램 ID**를 가져옵니다.
1. 새로 만든 앱을 클릭합니다.
1. 설정 블레이드에서 **키**를 클릭합니다.
1. 새 키를 만들고 다음 섹션에 저장합니다.

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Azure AD B2C에 Azure AD 키 추가

Azure AD B2C 테넌트에 `contoso.com` 응용 프로그램 키를 저장해야 합니다. 다음을 수행합니다.

1. Azure AD B2C 테넌트로 이동하고 [B2C 설정] > [ID 경험 프레임워크] > [정책 키]를 엽니다.
1. [+추가]를 클릭합니다.
1. 옵션:
 * [옵션] > `Manual`
 * [이름]: > `ContosoAppSecret`을 선택합니다. Azure AD 테넌트 이름과 일치하는 이름을 선택합니다.  키의 이름에 B2C_1A_ 접두사가 자동으로 추가됩니다.
 * `Secret` 텍스트 상자에 응용 프로그램 키를 붙여 넣습니다.
 * 서명을 선택합니다.
1. `Create`을 클릭합니다.
1. `B2C_1A_ContosoAppSecret` 키가 생성되었는지 확인합니다.


## <a name="add-a-claims-provider-in-your-base-policy"></a>기본 정책에서 클레임 공급자 추가

사용자가 Azure AD를 사용하여 로그인할 수 있으려면 Azure AD를 클레임 공급자로 정의해야 합니다. 즉, Azure AD B2C가 통신하는 끝점을 지정해야 합니다. 끝점은 Azure AD B2C에서 사용하는 일련의 *클레임*을 *제공*하여 특정 사용자가 인증했는지 확인합니다. 정책의 확장 파일에서 Azure AD를 `<ClaimsProvider>`으로 추가하여 수행할 수 있습니다.

1. 작업 디렉터리(TrustFrameworkExtensions.xml)에서 확장 파일을 엽니다.
1. `<ClaimsProviders>` 섹션을 찾습니다. 존재하지 않는 경우 루트 노드에 추가합니다.
1. 다음과 같이 새로운 `<ClaimsProvider>`을 추가합니다.

    ```XML
    <ClaimsProvider>
        <Domain>Contoso</Domain>
        <DisplayName>Login using Contoso</DisplayName>
        <TechnicalProfiles>
            <TechnicalProfile Id="ContosoProfile">
                <DisplayName>Contoso Employee</DisplayName>
                <Description>Login with your Contoso account</Description>
                <Protocol Name="OpenIdConnect"/>
                <OutputTokenFormat>JWT</OutputTokenFormat>
                <Metadata>
                    <Item Key="METADATA">https://login.windows.net/contoso.com/.well-known/openid-configuration</Item>
                    <Item Key="ProviderName">https://sts.windows.net/00000000-0000-0000-0000-000000000000/</Item>
                    <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="IdTokenAudience">00000000-0000-0000-0000-000000000000</Item>
                    <Item Key="response_types">id_token</Item>
                    <Item Key="UsePolicyInRedirectUri">false</Item>
                </Metadata>
                <CryptographicKeys>
                    <Key Id="client_secret" StorageReferenceId="B2C_1A_ContosoAppSecret"/>
                </CryptographicKeys>
                <OutputClaims>
                    <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="oid"/>
                    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
                    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
                    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
                    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
                    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="contosoAuthentication" />
                    <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="AzureADContoso" />
                </OutputClaims>
                <OutputClaimsTransformations>
                    <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
                    <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
                    <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
                    <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
                </OutputClaimsTransformations>
                <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
            </TechnicalProfile>
        </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. `<ClaimsProvider>` 노드 아래에서 `<Domain>` 값을 다른 ID 공급자를 구분하기 위해 사용할 수 있는 고유한 값으로 업데이트합니다.
1. `<ClaimsProvider>` 노드 아래에서 `<DisplayName>` 값을 클레임 공급자에게 친숙한 이름으로 업데이트합니다. 이 값은 현재 사용되지 않습니다.

### <a name="update-the-technical-profile"></a>기술 프로필 업데이트

Azure AD 끝점에서 토큰을 가져오기 위해 Azure AD B2C에서 Azure AD와 통신하는 데 사용해야 하는 프로토콜을 정의해야 합니다. `<ClaimsProvider>`의 `<TechnicalProfile>` 요소 내부에서 수행됩니다.

1. `<TechnicalProfile>` 노드의 ID를 업데이트합니다. 정책의 다른 부분에서 이 기술 프로필을 참조하는 데 이 ID를 사용합니다.
1. `<DisplayName>` 값을 업데이트합니다. 이 값은 로그인 화면의 로그인 단추에 표시됩니다.
1. `<Description>` 값을 업데이트합니다.
1. Azure AD는 OpenID Connect 프로토콜을 사용하므로 `<Protocol>`이 "OpenIDConnect"이어야 합니다.

위의 XML에서 `<Metadata>` 섹션을 업데이트하여 특정 Azure AD 테넌트의 구성 설정을 반영해야 합니다. XML에서 다음과 같이 메타데이터 값을 업데이트합니다.

1. `<Item Key="METADATA">`를 `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`로 설정합니다. 여기서 `yourAzureADtenant`는 Azure AD 테넌트 이름입니다(예: contoso.com).
1. 브라우저를 열고 방금 업데이트한 `Metadata` URL로 이동합니다.
1. 브라우저에서 '발급자' 개체를 찾아 해당 값을 복사합니다. 다음 `https://sts.windows.net/{tenantId}/`과 같아야 합니다.
1. XML에서 `<Item Key="ProviderName">` 값을 붙여 넣습니다.
1. 앱 등록에서 `<Item Key="client_id">`을 `Application ID`로 설정합니다.
1. 앱 등록에서 `<Item Key="IdTokenAudience">`을 `Application ID`로 설정합니다.
1. `<Item Key="response_types">`을 `id_token`로 설정했는지 확인합니다.
1. `<Item Key="UsePolicyInRedirectUri">`을 `false`로 설정했는지 확인합니다.

또한 Azure AD B2C 테넌트에 등록한 Azure AD 비밀을 Azure AD `<ClaimsProvider>`로 연결해야 합니다.

* 앞서 XML의 `<CryptographicKeys>` 섹션에서 `StorageReferenceId` 값을 정의한 비밀의 참조 ID(예: ContosoAppSecret)로 업데이트합니다.

### <a name="upload-the-extension-file-for-verification"></a>확인을 위한 확장 파일 업로드

지금까지 Azure AD B2C에서 Azure AD 디렉터리와 통신하는 방법을 알 수 있도록 정책을 구성했습니다. 정책의 확장 파일을 업로드하여 지금까지 문제가 발생하지 않았는지 확인합니다. 이렇게 하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **모든 정책** 블레이드로 이동합니다.
1. **있는 경우 정책 덮어쓰기** 확인란을 선택합니다.
1. 확장 파일(TrustFrameworkExtensions.xml)을 업로드하고 유효성 검사가 실패하지 않도록 합니다.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>사용자 경험에 Azure AD 클레임 공급자 등록

이제 사용자 경험 중 하나에 Azure AD ID 공급자를 추가해야 합니다. 이 시점에서 ID 공급자가 설정되었지만 등록/로그인 화면에서 사용할 수는 없습니다. 그렇게 하려면 기존 템플릿 사용자 경험의 복제본을 만든 다음 Azure AD ID 공급자가 포함되도록 수정합니다.

1. 정책의 기본 파일 열기(예: TrustFrameworkBase.xml)
1. Id=”SignUpOrSignIn”을 사용하여 `<UserJourneys>` 요소를 찾고 전체 `<UserJourney>`를 복사합니다.
1. 확장 파일(예: TrustFrameworkExtensions.xml)을 열고 `<UserJourneys>` 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. 복사한 전체 `<UserJourney>`을 `<UserJourneys>` 요소의 자식으로 붙여넣습니다.
1. 새 `<UserJourney>` ID의 이름을 바꿉니다(즉, SignUpOrSignUsingContoso).

### <a name="display-the-button"></a>"단추" 표시

`<ClaimsProviderSelection>` 요소는 등록/로그인 화면에서 ID 공급자 단추를 사용하는 것과 유사합니다. Azure AD에 `<ClaimsProviderSelection>` 요소를 추가하여 사용자가 페이지를 열면 새 단추를 표시합니다. 다음을 수행합니다.

1. 방금 만든 `<UserJourney>`에서 `Order="1"`와 `<OrchestrationStep>`을 찾습니다.
1. 다음을 추가합니다.

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

1. `TargetClaimsExchangeId`을 적절한 값으로 설정합니다. 다른 값과 동일한 규칙인 *\[ClaimProviderName\]Exchange*를 사용하는 것이 좋습니다.

### <a name="link-the-button-to-an-action"></a>작업에 "단추" 연결

이제 "단추"를 만들었으므로 작업에 연결해야 합니다. 이 경우에 작업을 통해 Azure AD B2C에서 Azure AD와 통신하여 토큰을 수신할 수 있게 됩니다. 이렇게 하려면 Azure AD 클레임 공급자의 기술 프로필을 연결합니다.

1. `<UserJourney>` 노드에서 `Order="2"`와 `<OrchestrationStep>` 찾기
1. 다음을 추가합니다.

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="ContosoProfile" />
    ```

1. `Id`을 위에 `TargetClaimsExchangeId`와 동일한 값으로 업데이트합니다.
1. `TechnicalProfileReferenceId`을 이전에 만든 기술 프로필의 `Id`로 업데이트합니다(예: ContosoProfile).

### <a name="upload-the-updated-extension-file"></a>업데이트된 확장 파일 업로드

확장 파일을 수정하는 작업을 완료했습니다. 이 파일을 저장하고 업로드한 다음 모든 유효성 검사가 성공하도록 합니다.

### <a name="update-the-rp-file"></a>RP 파일 업데이트

이제 방금 만든 사용자 경험을 시작하는 RP 파일을 업데이트해야 합니다.

1. 작업 디렉터리에서 SignUpOrSignIn.xml의 복사본을 만들고 이름을 바꿉니다(예: SignUpOrSignInWithAAD.xml).
1. 새 파일을 열고 `<TrustFrameworkPolicy>`의 `PolicyId` 특성을 고유한 값(예: SignUpOrSignInWithAAD)으로 업데이트합니다. 이것이 정책의 이름입니다(예: B2C\_1A\_SignUpOrSignInWithAAD).
1. `<DefaultUserJourney>`에서 `ReferenceId` 특성을 수정하여 만든 새 사용자 경험의 ID와 일치시킵니다(예: SignUpOrSignUsingContoso).
1. 변경 내용을 저장하고 파일을 업로드합니다.

## <a name="troubleshooting"></a>문제 해결

해당 블레이드를 열고 "지금 실행"을 클릭하여 방금 업로드한 사용자 지정 정책을 테스트합니다. 문제가 발생하는 경우 [문제를 해결](active-directory-b2c-troubleshoot-custom.md)하는 방법을 참조하세요.

## <a name="next-steps"></a>다음 단계

AADB2CPreview@microsoft.com에 대한 피드백을 제공합니다.

