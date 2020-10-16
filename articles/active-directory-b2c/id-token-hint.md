---
title: 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필을 정의 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 708ec35524f25314ca568944b738ba2cdf60d55c
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132077"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필을 정의 합니다.

Azure AD B2C를 사용 하면 신뢰 당사자 응용 프로그램이 OAuth2 권한 부여 요청의 일부로 인바운드 JWT를 보낼 수 있습니다. JWT 토큰은 신뢰 당사자 응용 프로그램 또는 id 공급자에서 발급할 수 있으며 사용자 또는 권한 부여 요청에 대 한 힌트를 전달할 수 있습니다. Azure AD B2C는 서명, 발급자 이름 및 토큰 대상의 유효성을 검사 하 고 인바운드 토큰에서 클레임을 추출 합니다.

## <a name="use-cases"></a>사용 사례

이 솔루션을 사용 하 여 단일 JWT 토큰으로 캡슐화 된 Azure AD B2C에 데이터를 보낼 수 있습니다. 시스템 관리자가 서명 된 초대를 사용자에 게 보낼 수 있는 [전자 메일 초대 솔루션 등록](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)은 id_token_hint를 기반으로 합니다. 초대 전자 메일에 대 한 액세스 권한이 있는 사용자만 디렉터리에 계정을 만들 수 있습니다.

## <a name="token-signing-approach"></a>토큰 서명 방법

Id_token_hint를 사용 하 여 토큰 발급자 (신뢰 당사자 앱 또는 id 공급자)는 토큰을 작성 한 다음 서명 키를 사용 하 여 토큰을 서명 하 고 신뢰할 수 있는 원본에서 오는 토큰을 증명 합니다. 서명 키는 대칭 또는 비대칭 일 수 있습니다. 대칭 암호화 또는 개인 키 암호화는 공유 암호를 사용 하 여 서명을 서명 하 고 유효성을 검사 합니다. 비대칭 암호화 또는 공개 키 암호화는 개인 키와 공개 키를 모두 사용 하는 암호화 시스템입니다. 개인 키는 토큰 발급자에만 알려져 있으며 토큰에 서명 하는 데 사용 됩니다. 공개 키는 토큰 서명의 유효성을 검사 하는 Azure AD B2C 정책과 공유 됩니다.

## <a name="token-format"></a>토큰 형식

Id_token_hint은 유효한 JWT 토큰 이어야 합니다. 다음 표에서는 필수 클레임을 나열 합니다. 추가 클레임은 선택적입니다.

| 속성 | 클레임 | 예제 값 | Description |
| ---- | ----- | ------------- | ----------- |
| 사용자 | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | 토큰의 의도한 수신자를 식별합니다. 토큰 발급자에 의해 정의 되는 임의의 문자열입니다. Azure AD B2C는이 값의 유효성을 검사 하 고 토큰이 일치 하지 않을 경우 토큰을 거부 합니다.  |
| 발급자 | `iss` |`https://localhost` | 보안 토큰 서비스 (토큰 발급자)를 식별 합니다. 토큰 발급자에 의해 정의 되는 임의의 URI입니다. Azure AD B2C는이 값의 유효성을 검사 하 고 토큰이 일치 하지 않을 경우 토큰을 거부 합니다.  |
| 만료 시간 | `exp` | `1600087315` | epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. Azure AD B2C는이 클레임의 유효성을 검사 하지 않습니다. |
| 이전이 아님 | `nbf` | `1599482515` | epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 일반적으로 토큰이 발급된 시간과 같습니다. Azure AD B2C는이 클레임의 유효성을 검사 하지 않습니다. |

 다음 토큰은 유효한 ID 토큰의 예입니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. 예를 들어 **IdTokenHint_ExtractClaims** 기술 프로필에 대 한 프로토콜은 `None` 다음과 같습니다.

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

기술 프로필은 유형이 인 오케스트레이션 단계에서 호출 됩니다 `GetClaims` .

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 JWT 토큰에서 추출할 클레임 목록을 포함 합니다. 정책에 정의 된 클레임의 이름을 JWT 토큰에 정의 된 이름에 매핑해야 할 수 있습니다. 또한 특성을 설정 하는 동안 JWT 토큰에서 반환 되지 않은 클레임을 포함할 수 있습니다 `DefaultValue` .

## <a name="metadata"></a>메타데이터

다음 메타 데이터는 대칭 키를 사용할 때 관련 됩니다. 

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 발급자 | 예 | 보안 토큰 서비스 (토큰 발급자)를 식별 합니다. 이 값은 `iss` JWT 토큰 클레임 내의 클레임과 동일 해야 합니다. | 
| IdTokenAudience | 예 | 토큰의 의도한 수신자를 식별합니다. JWT 토큰 클레임을 트 내의 하는 클레임과 동일 해야 합니다 `aud` . | 

다음 메타 데이터는 비대칭 키를 사용 하는 경우와 관련이 있습니다. 

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| METADATA| 예 | Openid connect 잘 알려진 구성 끝점이 라고도 하는 토큰 발급자 구성 문서를 가리키는 URL입니다.   |
| 발급자 | No | 보안 토큰 서비스 (토큰 발급자)를 식별 합니다. 이 값은 메타 데이터에 구성 된 값을 덮어쓰는 데 사용할 수 있으며 `iss` JWT 토큰 클레임 내의 클레임과 동일 해야 합니다. |  
| IdTokenAudience | No | 토큰의 의도한 수신자를 식별합니다. JWT 토큰 클레임을 트 내의 하는 클레임과 동일 해야 합니다 `aud` . |  

## <a name="cryptographic-keys"></a>암호화 키

대칭 키를 사용 하는 경우 **CryptographicKeys** 요소에는 다음 특성이 포함 됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| client_secret | 예 | JWT 토큰 서명의 유효성을 검사 하는 데 사용 되는 암호화 키입니다.|


## <a name="how-to-guide"></a>방법 가이드

### <a name="issue-a-token-with-symmetric-keys"></a>대칭 키가 있는 토큰 발급

#### <a name="step-1-create-a-shared-key"></a>1단계. 공유 키 만들기 

토큰에 서명 하는 데 사용할 수 있는 키를 만듭니다. 예를 들어 다음 PowerShell 코드를 사용 하 여 키를 생성 합니다.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

이 코드는와 같은 비밀 문자열을 만듭니다 `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=` .

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>2단계. Azure AD B2C에 서명 키를 추가 합니다.

토큰 발급자에 사용 되는 것과 동일한 키를 Azure AD B2C 정책 키에 만들어야 합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C**를 검색하고 선택합니다.
1. 개요 페이지의 **정책**에서 **Identity Experience Framework**를 선택합니다.
1. **정책 키** 선택 
1. **수동**을 선택 합니다.
1. **이름**에는 `IdTokenHintKey`를 사용합니다.  
   `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀** 상자에 이전에 생성 한 로그인 키를 입력 합니다.
1. **키 사용**에는 **암호화**를 사용합니다.
1. **만들기**를 선택합니다.
1. `B2C_1A_IdTokenHintKey` 키를 만들었는지 확인합니다.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>3단계: ID 토큰 힌트 기술 프로필 추가

다음 기술 프로필은 토큰의 유효성을 검사 하 고 클레임을 추출 합니다. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>4단계. 정책 준비

[정책 구성](#configure-your-policy) 단계를 완료 합니다.

#### <a name="step-5-prepare-the-code"></a>5단계. 코드 준비  

[GitHub 샘플](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key) 은 대칭 키를 사용 하 여 서명 된 ID 토큰을 생성 하는 ASP.NET 웹 응용 프로그램 및 콘솔 앱입니다. 


### <a name="issue-a-token-with-asymmetric-keys"></a>비대칭 키를 사용 하 여 토큰 발급

비대칭 키를 사용 하 여 토큰은 RSA 인증서를 사용 하 여 서명 됩니다. 이 응용 프로그램은 Azure AD B2C에서 ID 토큰 서명의 유효성을 검사 하는 데 사용 되는 Open ID Connect 메타 데이터 끝점과 JWKs (JSON 웹 키) 끝점을 호스팅합니다.

토큰 발급자는 다음 끝점을 제공 해야 합니다.

* `/.well-known/openid-configuration` -토큰 발급자 이름 및 JWK 끝점에 대 한 링크와 같이 토큰에 대 한 관련 정보가 포함 된 잘 알려진 구성 끝점입니다. 
* `/.well-known/keys` -키에 서명 하는 데 사용 되는 공개 키가 있는 JWK (JSON 웹 키) 끝점 (인증서의 개인 키 부분 포함)입니다.

[TokenMetadataController.cs](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .net MVC controller 샘플을 참조 하세요.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>1단계. 자체 서명 된 인증서 준비

인증서가 아직 없는 경우이 방법 가이드에 대해 자체 서명 된 인증서를 사용할 수 있습니다. Windows에서는 PowerShell의 [New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성할 수 있습니다.

이 PowerShell 명령을 실행 하 여 자체 서명 된 인증서를 생성 합니다. `-Subject` 인수를 애플리케이션 및 Azure AD B2C 테넌트 이름에 적절하게 수정합니다. `-NotAfter` 날짜를 조정하여 인증서에 다른 만료 날짜를 지정할 수도 있습니다.

```PowerShell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>2단계. ID 토큰 힌트 기술 프로필 추가 

다음 기술 프로필은 토큰의 유효성을 검사 하 고 클레임을 추출 합니다. 메타 데이터 URI를 토큰 발급자의 잘 알려진 구성 끝점으로 변경 합니다.  

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item> -->
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>3단계: 정책 준비

[정책 구성](#configure-your-policy) 단계를 완료 합니다.

#### <a name="step-4-prepare-the-code"></a>4단계. 코드 준비 

이 [GitHub 샘플](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET 웹 응용 프로그램은 ID 토큰을 생성 하 고 Azure AD B2C에서 "id_token_hint" 매개 변수를 사용 하는 데 필요한 메타 데이터 끝점을 호스팅합니다.


### <a name="configure-your-policy"></a>정책 구성

대칭 및 비대칭 방식 모두에서 `id_token_hint` 기술 프로필은 유형으로 오케스트레이션 단계에서 호출 되며 `GetClaims` 신뢰 당사자 정책의 입력 클레임을 지정 해야 합니다.

1. IdTokenHint_ExtractClaims 기술 프로필을 확장 정책에 추가 합니다.
1. 다음 오케스트레이션 단계를 사용자 경험에 첫 번째 항목으로 추가 합니다.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 신뢰 당사자 정책에서 IdTokenHint_ExtractClaims 기술 프로필에서 구성한 것과 동일한 입력 클레임을 반복 합니다. 예를 들면 다음과 같습니다.
    ```xml
   <RelyingParty>
     <DefaultUserJourney ReferenceId="SignUp" />
     <TechnicalProfile Id="PolicyProfile">
       <DisplayName>PolicyProfile</DisplayName>
       <Protocol Name="OpenIdConnect" />
       <InputClaims>
         <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
       <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
       </OutputClaims>
       <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

비즈니스 요구 사항에 따라 토큰 만료, 전자 메일 주소 형식 등을 확인 하는 토큰 유효성 검사를 추가 해야 할 수도 있습니다. 이렇게 하려면 [클레임 변환 기술 프로필](claims-transformation-technical-profile.md)을 호출 하는 오케스트레이션 단계를 추가 합니다. 또한 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md) 을 추가 하 여 오류 메시지를 표시 합니다. 

### <a name="create-and-sign-a-token"></a>토큰 만들기 및 서명

GitHub 샘플은 나중에 `id_token_hint` 쿼리 문자열 매개 변수로 전송 되는 JWT에 이러한 토큰을 생성 하는 방법을 보여 줍니다. 다음은 id_token_hint 매개 변수를 사용 하는 권한 부여 요청의 예입니다.
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>다음 단계

- Azure AD B2C community GitHub 리포지토리에서 [초대 전자 메일을 사용 하 여 등록](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) 솔루션을 선택 합니다.
