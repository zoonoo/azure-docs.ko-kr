---
title: Azure AD B2C에서 Restful 서비스를 보호 합니다.
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 REST API 클레임 교환을 보호 합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 34ed6d043f713aa55bfe464c48d4332364df805d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81680372"
---
# <a name="secure-your-restful-services"></a>RESTful 서비스 보호 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C 사용자 경험 내에서 REST API를 통합 하는 경우 인증을 사용 하 여 REST API 끝점을 보호 해야 합니다. 이렇게 하면 Azure AD B2C와 같이 적절 한 자격 증명이 있는 서비스만 REST API 끝점에 대 한 호출을 수행할 수 있습니다.

사용자 [입력 유효성 검사](custom-policy-rest-api-claims-validation.md) 및 사용자 [지정 정책에 REST API 클레임 교환 추가](custom-policy-rest-api-claims-exchange.md) 문서에서 Azure AD B2C 사용자 경험 내에서 REST API를 통합 하는 방법에 대해 알아봅니다.

이 문서에서는 HTTP 기본, 클라이언트 인증서 또는 OAuth2 인증을 사용 하 여 REST API를 보호 하는 방법을 알아봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

다음 ' 방법 ' 가이드 중 하나의 단계를 완료 합니다.

- [Azure AD B2C 사용자 경험에 REST API 클레임 교환을 통합 하 여 사용자 입력의 유효성을 검사](custom-policy-rest-api-claims-validation.md)합니다.
- [사용자 지정 정책에 클레임 교환 REST API 추가](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 기본 인증

HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의 되어 있습니다. 기본 인증은 다음과 같이 작동 합니다. Azure AD B2C는 인증 헤더에 클라이언트 자격 증명을 사용 하 여 HTTP 요청을 보냅니다. 자격 증명은 b a s e 64로 인코딩된 문자열 "name: password"로 형식이 지정 됩니다.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API 사용자 이름 및 암호 정책 키 추가

HTTP 기본 인증을 사용 하 여 REST API 기술 프로필을 구성 하려면 다음 암호화 키를 만들어 사용자 이름 및 암호를 저장 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**를 선택 하 고 **추가**를 선택 합니다.
1. **옵션**에서 **수동**을 선택합니다.
1. **이름**에 **RestApiUsername**를 입력 합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 사용자 이름을 입력 합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.
1. **정책 키** 를 다시 선택 합니다.
1. **추가**를 선택합니다.
1. **옵션**에서 **수동**을 선택합니다.
1. **이름**에 **RestApiPassword**를 입력 합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 암호를 입력 합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>HTTP 기본 인증을 사용 하도록 REST API 기술 프로필 구성

필요한 키를 만든 후에 REST API 기술 프로필 메타 데이터를 구성 하 여 자격 증명을 참조 합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색 합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을로 `Basic`변경 합니다.
1. *Allowinsecureauthinproduction* 를로 `false`변경 합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

다음은 HTTP 기본 인증을 사용 하 여 구성 된 RESTful 기술 프로필의 예입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="https-client-certificate-authentication"></a>HTTPS 클라이언트 인증서 인증

클라이언트 인증서 인증은 클라이언트 Azure AD B2C에서 해당 id를 증명 하기 위해 클라이언트 인증서를 서버에 제공 하는 상호 인증서 기반 인증입니다. 이는 SSL 핸드셰이크의 일부로 발생 합니다. Azure AD B2C와 같이 적절 한 인증서가 있는 서비스만 REST API 서비스에 액세스할 수 있습니다. 클라이언트 인증서는 x.509 디지털 인증서입니다. 프로덕션 환경에서는 인증 기관에서 서명 해야 합니다.

### <a name="prepare-a-self-signed-certificate-optional"></a>자체 서명 된 인증서를 준비 합니다 (선택 사항).

비프로덕션 환경의 경우 인증서가 아직 없는 경우 자체 서명 된 인증서를 사용할 수 있습니다. Windows에서는 PowerShell의 [new-selfsignedcertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용 하 여 인증서를 생성할 수 있습니다.

1. 이 PowerShell 명령을 실행 하 여 자체 서명 된 인증서를 생성 합니다. 인수를 `-Subject` 응용 프로그램에 적절 하 게 수정 하 고 테 넌 트 이름을 Azure AD B2C 합니다. 또한 `-NotAfter` 날짜를 조정 하 여 인증서에 대해 다른 만료를 지정할 수 있습니다.
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
1. **사용자 인증서** > 관리**현재 사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com*를 엽니다.
1. 인증서 > **작업** > **모든 태스크** > **내보내기**를 선택 합니다.
1. **예** > **다음****Next****Yes, export the private key** > 예, 개인 키를 내보냅니다 .를 선택 합니다. > 
1. **내보내기 파일 형식**에 대 한 기본값을 적용 합니다.
1. 인증서에 대 한 암호를 제공 합니다.

### <a name="add-a-client-certificate-policy-key"></a>클라이언트 인증서 정책 키 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**를 선택 하 고 **추가**를 선택 합니다.
1. **옵션** 상자에서 **업로드**를 선택합니다.
1. **이름** 상자에 **RestApiClientCertificate**을 입력 합니다.
    *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. **파일 업로드** 상자에서 프라이빗 키가 있는 인증서 .pfx 파일을 선택합니다.
1. **암호** 상자에 인증서의 암호를 입력합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>클라이언트 인증서 인증을 사용 하도록 REST API 기술 프로필 구성

필요한 키를 만든 후 클라이언트 인증서를 참조 하도록 REST API 기술 프로필 메타 데이터를 구성 합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색 합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을로 `ClientCertificate`변경 합니다.
1. *Allowinsecureauthinproduction* 를로 `false`변경 합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

다음은 HTTP 클라이언트 인증서를 사용 하 여 구성 된 RESTful 기술 프로필의 예입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 전달자 인증 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

전달자 토큰 인증은 [oauth 2.0 권한 부여 프레임 워크: 전달자 토큰 사용 (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)에 정의 되어 있습니다. 전달자 토큰 인증에서 Azure AD B2C는 토큰을 사용 하 여 인증 헤더에 HTTP 요청을 보냅니다.

```http
Authorization: Bearer <token>
```

전달자 토큰은 불투명 문자열입니다. 이 토큰은 JWT 액세스 토큰 이거나 REST API에서 인증 헤더에 보낼 Azure AD B2C 것으로 예상 하는 문자열일 수 있습니다. Azure AD B2C 지원 되는 형식은 다음과 같습니다.

- **전달자 토큰**입니다. Restful 기술 프로필에서 전달자 토큰을 보낼 수 있으려면 먼저 정책에서 전달자 토큰을 획득 한 다음 RESTful 기술 프로필에서 사용 해야 합니다.  
- **정적 전달자 토큰**입니다. REST API 장기 액세스 토큰을 발급 하는 경우이 방법을 사용 합니다. 정적 전달자 토큰을 사용 하려면 정책 키를 만들고 RESTful 기술 프로필에서 정책 키로 참조를 만듭니다. 


## <a name="using-oauth2-bearer"></a>OAuth2 전달자 사용  

다음 단계에서는 클라이언트 자격 증명을 사용 하 여 전달자 토큰을 가져와서 REST API 호출의 Authorization 헤더에 전달 하는 방법을 보여 줍니다.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>전달자 토큰을 저장할 클레임을 정의 합니다.

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공 합니다. 클레임 [스키마](claimsschema.md) 는 클레임을 선언 하는 위치입니다. 액세스 토큰은 나중에 사용 하기 위해 클레임에 저장 되어야 합니다. 

1. 정책의 확장 파일을 엽니다. 예를 <em> `SocialAndLocalAccounts/` </em>들면입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가 합니다.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>액세스 토큰 획득 

액세스 토큰을 가져오는 [방법, 액세스](idp-pass-through-custom.md)토큰을 반환 하는 REST API를 호출 하거나 [ropc 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)을 사용 하거나 [클라이언트 자격 증명 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)을 사용 하 여 여러 가지 방법 중 하나로 액세스 토큰을 가져올 수 있습니다.  

다음 예제에서는 REST API 기술 프로필을 사용 하 여 HTTP 기본 인증으로 전달 된 클라이언트 자격 증명을 사용 하 여 Azure AD 토큰 끝점에 대 한 요청을 만듭니다. Azure AD에서이를 구성 하려면 [Microsoft id 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)을 참조 하세요. Id 공급자와 상호 작용 하도록이를 수정 해야 할 수도 있습니다. 

ServiceUrl에서-테 넌 트 이름을 Azure AD 테 넌 트의 이름으로 바꿉니다. 사용 가능한 모든 옵션에 대해서는 [RESTful 기술 프로필](restful-technical-profile.md) 참조를 참조 하세요.

```xml
<TechnicalProfile Id="SecureREST-AccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.microsoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://secureb2cfunction.azurewebsites.net/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>전달자 토큰 인증을 사용 하도록 REST 기술 프로필 변경

사용자 지정 정책에서 전달자 토큰 인증을 지원 하려면 REST API 기술 프로필을 다음과 같이 수정 합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 확장 정책 파일을 엽니다.
1. `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 검색합니다.
1. `<Metadata>` 요소를 찾습니다.
1. 다음과 같이 *AuthenticationType* 을 *전달자*로 변경 합니다.
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 다음과 같이 *UseClaimAsBearerToken* 를 *bearerToken*로 변경 하거나 추가 합니다. *BearerToken* 는 전달자 토큰을 검색할 클레임의 이름입니다 (의 `SecureREST-AccessToken`출력 클레임).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 위에서 사용 된 클레임을 입력 클레임으로 추가 해야 합니다.

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

위의 코드 조각을 추가한 후 기술 프로필은 다음 XML 코드와 같이 표시 됩니다.

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>정적 OAuth2 전달자 사용 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>OAuth2 전달자 토큰 정책 키 추가

전달자 토큰 값을 저장할 정책 키를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테 넌 트를 포함 하는 디렉터리를 사용 하 고 있는지 확인 합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택 하 고 Azure AD B2C 디렉터리를 선택 합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. **정책 키**를 선택 하 고 **추가**를 선택 합니다.
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `RestApiBearerToken`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀**에 입력합니다.
1. **키 사용**에서 `Encryption`를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>전달자 토큰 정책 키를 사용 하도록 REST API 기술 프로필 구성

필요한 키를 만든 후 전달자 토큰을 참조 하도록 REST API 기술 프로필 메타 데이터를 구성 합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색 합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을로 `Bearer`변경 합니다.
1. *Allowinsecureauthinproduction* 를로 `false`변경 합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

다음은 전달자 토큰 인증을 사용 하 여 구성 된 RESTful 기술 프로필의 예입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [Restful 기술 프로필](restful-technical-profile.md) 요소에 대해 자세히 알아보세요. 
