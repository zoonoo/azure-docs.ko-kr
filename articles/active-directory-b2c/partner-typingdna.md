---
title: Azure Active Directory B2C를 사용 하 여 TypingDNA
titleSuffix: Azure AD B2C
description: 사용자 입력 패턴을 기반으로 Id 확인 및 교정에 도움을 주기 위해 TypingDNA와 Azure AD B2C 인증을 통합 하는 방법에 대해 알아봅니다. ID 확인 솔루션은 multi-factor authentication을 강제 적용 하 고 결제 서비스 지시어 2 (PSD2)에 대 한 SCA 요구 사항을 준수 하는 데 도움이 됩니다.
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f7d89942ad5209b854b8df486ad3e59a3976edfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259054"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용 하 여 TypingDNA 구성 자습서

이 연습에서는 Azure Active Directory B2C의 샘플 온라인 지불 앱을 TypingDNA 앱과 통합 하는 방법에 대해 알아봅니다. Azure AD B2C 고객은 TypingDNA 앱을 사용 하 여 키 입력 dynamics 및 강력한 고객 인증을 통해 PSD2 ( [결제 서비스 지시어 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) ) 트랜잭션 요구 사항을 준수할 수 있습니다. [여기](https://www.typingdna.com/)에서 TypingDNA에 대해 자세히 알아보세요.

 Azure AD B2C는 TypingDNA의 기술을 사용 하 여 사용자 입력 특성을 캡처하고 각 인증에 대해 친숙 하 게 기록 하 고 분석 합니다. 이렇게 하면 인증 riskiness 관련 된 보호 계층이 추가 되 고 위험 수준이 평가 됩니다. 다른 메커니즘을 호출 하 여 사용자가 Azure MFA를 호출 하거나, 전자 메일 확인을 강제로 수행 하거나, 시나리오에 대 한 기타 사용자 지정 논리를 호출 하 여 사용자가 자신에 게 주장 하는 사용자를 제공할 수 Azure AD B2C.

>[!NOTE]
> 이 샘플 정책은 [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) 스타터 팩을 기반으로 합니다.

## <a name="scenario-description"></a>시나리오 설명

![TypingDNA 아키텍처 다이어그램의 스크린샷](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>등록

1. Azure AD B2C 페이지에서는 TypingDNA의 JavaScript 라이브러리를 사용 하 여 사용자의 입력 패턴을 기록 합니다. 예를 들어, 사용자 이름 및 암호는 초기 등록에 등록 한 후 확인을 위해 로그인 할 때마다 기록 됩니다.

2. 사용자가 페이지를 제출 하면 TypingDNA 라이브러리가 사용자의 입력 특성을 계산 합니다. 그런 다음 Azure AD B2C 렌더링 된 숨겨진 텍스트 필드에 정보를 삽입 합니다. 이 필드는 CSS를 사용 하 여 숨겨집니다.  

    이 샘플은 JavaScript 및 CSS 수정이 [포함 된 HTML 파일을 포함](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) 하며 `api.selfasserted.tdnasignin` 및 콘텐츠 정의에서 참조 됩니다 `api.selfasserted.tdnasignup` . HTML 파일을 호스트 하려면 [페이지 콘텐츠 호스팅](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content) 을 참조 하세요.

3. 이제 사용자가 자격 증명을 전송할 때 클레임 모음 내에 Azure AD B2C 입력 패턴이 있습니다. API를 호출 하 여이 데이터를 TypingDNA REST API 끝점에 전달 해야 합니다. 이 API는 [샘플 (typingDNA-API 인터페이스)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)에 포함 되어 있습니다.
4. 그러면 중간 계층 API는 입력 패턴 데이터를 TypingDNA REST API 전달 합니다. 등록 시 사용자가 존재 하지 않는지 확인 하기 위해 [사용자 끝점 확인](https://api.typingdna.com/index.html#api-API_Services-GetUser) 이 호출 되 고 [저장 패턴](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 끝점이 호출 되어 사용자의 첫 번째 입력 패턴을 저장 합니다.

> [!NOTE]
> TypingDNA REST API 끝점에 대 한 모든 호출에서 UserId를 보냅니다. 해시 된 값 이어야 합니다. Azure AD B2C는 클레임 변환을 사용 하 여 `HashObjectIdWithEmail` 임의 솔트 및 암호를 사용 하 여 전자 메일을 해시 합니다.  

REST API 호출은 내에서 모델링 됩니다 `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` .

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>로그인

이후 로그인에서 사용자의 입력 패턴은 [사용자 지정 HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)을 사용 하 여 등록할 때와 동일한 방식으로 계산 됩니다. 입력 프로필이 Azure AD B2C 된 클레임 모음 내에 있으면 Azure AD B2C API를 호출 하 여 TypingDNA의 REST API 엔드포인트를 호출 합니다. 사용자가 존재 하는지 확인 하기 위해 [사용자](https://api.typingdna.com/index.html#api-API_Services-GetUser) 끝점이 호출 됩니다. 그런 다음를 반환 하기 위해 패턴 끝점이 호출 [되는지 확인](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) `net_score` 합니다. 이는 `net_score` 등록 시 원래에 입력 패턴을 닫는 방법을 나타냅니다.

이 입력 패턴은 `validationTechnicalProfiles` 다음 내에서 모델링 됩니다 `SelfAsserted-LocalAccountSignin-Email-TDNA` .

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 사용자가 높은 형식으로 입력 패턴을 가져오는 경우 `net_score` TypingDNA [저장 입력 패턴](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 끝점을 사용 하 여이를 저장할 수 있습니다.  

Api를  `saveTypingPattern` 통해 Azure AD B2C (api를 통해)에 의해 호출 되는 TypingDNA 저장 형식 패턴 끝점을 원하는 경우 api는 클레임을 반환 해야 합니다.

리포지토리의 예제에는 다음 속성으로 구성 된 API (TypingDNA-인터페이스)가 포함 되어 있습니다.

- 학습 모드-사용자가 두 개 미만의 패턴을 저장 한 경우 항상 MFA를 확인 합니다.

- 사용자가 2-5 패턴을 저장 하 고 `net_score` 가 50 보다 낮으면 MFA를 확인 합니다.

- 사용자에 게 5 + 패턴이 저장 `net_score` 되 고가 65 보다 낮으면 MFA를 확인 합니다.

이러한 임계값은 사용 사례에서 조정 해야 합니다.

- API가를 평가한 후 `net_score` B2C-로 부울 클레임을 반환 해야 합니다 `promptMFA` .

- `promptMFA`클레임은 사전 조건 내에서 조건부로 AZURE MFA를 실행 하는 데 사용 됩니다.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>TypingDNA로 등록

1. [여기](https://www.typingdna.com/) 에서 TypingDNA에 등록 합니다.
2. TypingDNA 대시보드에 로그인 하 고 **api 키** 및 **api 암호**를 가져옵니다. API 인터페이스 설정에서 나중에 필요 합니다.

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Azure AD B2C를 사용 하 여 TypingDNA 통합

1. 선택한 호스팅 공급자에서 [Typingdna-API 인터페이스](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 를 호스팅합니다.
2. Typingdna의 모든 인스턴스와 `apiKey` `apiSecret` [API 인터페이스](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 솔루션을 사용자의 자격 증명으로 바꿉니다.
3. [여기](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors) 에 CORS 요구 사항에 따라 선택한 공급자에서 HTML 파일을 호스팅합니다.
4. 파일의 및 콘텐츠 정의에 대 한 LoadURI 요소를 `api.selfasserted.tdnasignup` `api.selfasserted.tdnasignin` `TrustFrameworkExtensions.xml` 각각 호스팅된 HTML 파일의 uri로 바꿉니다.
5. **Azure Portal**Azure AD 블레이드의 id 경험 프레임 워크에서 B2C 정책 키를 만듭니다. `Generate`옵션을 사용 하 고이 키의 이름을로 `tdnaHashedId` 합니다.
6. 정책 파일에서 TenantId를 바꿉니다.
7. 모든 TypingDNA의 ServiceURLs REST API 기술 프로필 (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser)을 [Typingdna Api 인터페이스](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)에 대 한 끝점으로 바꿉니다.
8. 테 넌 트에 [정책 파일](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) 을 업로드 합니다.

## <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. B2C 테 넌 트를 열고 id 경험 프레임 워크를 선택 합니다.
2. 이전에 만든 **사용자 흐름**을 선택 합니다.
3. 사용자 흐름 **실행** 선택

    a. **응용 프로그램** -등록 된 앱 선택 (JWT는 샘플)

    b. **회신 url** -리디렉션 url을 선택 합니다.

    다. **사용자 흐름 실행**을 선택합니다.
  
4. 등록 흐름으로 이동 하 여 계정 만들기
5. 로그아웃
6. 로그인 흐름을 통해 이동
7. 결과 JWT 결과에 TypingDNA 결과가 표시 됩니다.

## <a name="live-version"></a>라이브 버전

• MFA는이 테스트 버전에서 사용 하지 않도록 설정 되었지만 인증 후에 클레임에서 MFA에 메시지가 표시 되는지 여부에 대 한 결과를 볼 수 있습니다 `promptMFA` .

• [여기에서 등록 하 고](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) [여기](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) 에 로그인 하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조 하세요.

- [AAD B2C의 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [AAD B2C에서 사용자 지정 정책 시작](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
