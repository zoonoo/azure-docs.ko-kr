---
title: Azure Active Directory B2C에서 애플리케이션으로 액세스 토큰의 사용자 지정 정책 통과 | Microsoft Docs
description: Azure Active Directory B2C에서 애플리케이션으로 사용자 지정 정책의 클레임을 사용하여 OAuth2.0 ID 공급자에 대한 액세스 토큰을 통과하는 방법을 알아봅니다.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d9420398c012b8da18e3b035cb845db6ce8c942d
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511059"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 애플리케이션으로 액세스 토큰의 사용자 지정 정책 통과

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure AD(Azure Active Directory) B2C의 [사용자 지정 정책](active-directory-b2c-get-started-custom.md)은 애플리케이션 사용자에게 ID 공급자를 등록하거나 로그인할 수 있는 기회를 제공합니다. 이 과정에서 Azure AD B2C는 ID 공급자로부터 [액세스 토큰](active-directory-b2c-reference-tokens.md)을 받습니다. Azure AD B2C는 이 토큰을 사용하여 해당 사용자에 대한 정보를 검색합니다. 사용자 지정 정책에 클레임 유형 및 출력 클레임을 추가하여 Azure AD B2C에서 등록한 애플리케이션에 토큰을 전달합니다. 

Azure AD B2C에서는 [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) 및 [OpenID Connect](active-directory-b2c-reference-oidc.md) ID 공급자의 액세스 토큰을 전달할 수 있습니다. 다른 모든 ID 공급자에 대한 클레임은 빈 상태로 반환됩니다.

## <a name="prerequisites"></a>필수 조건

- 사용자 지정 정책은 OAuth 2.0 또는 OpenID Connect ID 공급자를 사용하여 구성됩니다.

## <a name="add-the-claim-elements"></a>클레임 요소 추가 

1. TrustframeworkExtensions.xml 파일을 열고 `identityProviderAccessToken` 식별자를 사용하여 다음 **ClaimType** 요소를 **ClaimsSchema** 요소에 추가합니다. 

    ```XML
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. 액세스 토큰을 사용할 각 OAuth 2.0 공급자에 대해 **TechnicalProfile** 요소에 **OutputClaim** 요소를 추가합니다. 다음 예제는 Facebook 기술 프로필에 추가된 요소를 보여 줍니다.

    ```XML
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. TrustframeworkExtensions.xml 파일을 저장합니다. 
4. 신뢰 당사자 정책 파일(예: SignUpOrSignIn.xml)을 열고 **OutputClaim** 요소를 **TechnicalProfile**에 추가합니다. 

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. 정책 파일을 저장합니다.

## <a name="test-your-policy"></a>정책 테스트

Azure AD B2C에서 애플리케이션을 테스트하는 경우 포함된 클레임을 검토할 수 있도록 Azure AD B2C 토큰이 `https://jwt.ms`에 반환되도록 하는 것이 유용할 수 있습니다.

### <a name="upload-the-files"></a>파일 업로드

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트를 포함하는 디렉터리를 사용하려면 위쪽 메뉴에서 **디렉터리 및 구독 필터**를 클릭하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 위에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
4. **ID 경험 프레임워크**를 선택합니다.
5. 사용자 지정 정책 페이지에서 **정책 업로드**를 클릭합니다.
6. **정책이 있는 경우 덮어쓰기**를 선택한 후 TrustFrameworkExtensions.xml 파일을 찾아서 선택합니다. 
7. **업로드**를 클릭합니다.
8. 신뢰 당사자 파일(예: SignUpOrSignIn.xml)에 대해 5~7단계를 반복합니다. 

### <a name="run-the-policy"></a>정책 실행

1. 변경한 정책을 엽니다. 예를 들어 B2C_1A_signup_signin입니다  .
2. **애플리케이션**은 이전에 등록한 애플리케이션을 선택합니다. 아래 예제에서 토큰을 보려면 **회신 URL**에서 `https://jwt.ms`가 표시되어야 합니다.
3. **지금 실행**을 클릭합니다.

    다음 예제와 비슷한 내용이 표시됩니다.

    ![디코딩된 토큰](./media/idp-pass-through-custom/idp-pass-through-custom-token.png)

## <a name="next-steps"></a>다음 단계

[Azure Active Directory 토큰 참조](active-directory-b2c-reference-tokens.md)에서 토큰에 대해 자세히 알아봅니다.





