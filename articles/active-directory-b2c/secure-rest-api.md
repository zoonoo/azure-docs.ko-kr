---
title: Azure AD B2C에서 편안한 서비스 보호
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 REST API 클레임 교환을 보호합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 37d1c181c18f69c040040da2be138eaad3a61693
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80396849"
---
# <a name="secure-your-restful-services"></a>RESTful 서비스 보안 

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C 사용자 여정 내에서 REST API를 통합할 때는 인증으로 REST API 끝점을 보호해야 합니다. 이렇게 하면 Azure AD B2C와 같은 적절한 자격 증명이 있는 서비스만 REST API 끝점을 호출할 수 있습니다.

Azure AD B2C 사용자 여정에 REST API를 사용자 [입력 유효성 검사](custom-policy-rest-api-claims-validation.md) 및 사용자 지정 정책 아티클에 REST API 클레임 교환 [추가에 통합하는](custom-policy-rest-api-claims-exchange.md) 방법을 알아봅니다.

이 문서에서는 HTTP 기본, 클라이언트 인증서 또는 OAuth2 인증을 사용하여 REST API를 보호하는 방법을 살펴봅습니다. 

## <a name="prerequisites"></a>사전 요구 사항

다음 '방법' 가이드 중 하나의 단계를 완료합니다.

- [Azure AD B2C 사용자 여정에서 REST API 클레임 교환을 통합하여 사용자 입력의 유효성을 검사합니다.](custom-policy-rest-api-claims-validation.md)
- [사용자 지정 정책에 REST API 클레임 교환 추가](custom-policy-rest-api-claims-exchange.md)

## <a name="http-basic-authentication"></a>HTTP 기본 인증

HTTP 기본 인증은 [RFC 2617에](https://tools.ietf.org/html/rfc2617)정의되어 있습니다. 기본 인증은 다음과 같이 작동합니다: Azure AD B2C는 권한 부여 헤더에 클라이언트 자격 증명을 통해 HTTP 요청을 보냅니다. 자격 증명은 base64 인코딩된 문자열 "name:password"로 서식이 지정됩니다.  

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API 사용자 이름 및 암호 정책 키 추가

HTTP 기본 인증으로 REST API 기술 프로필을 구성하려면 다음 암호화 키를 만들어 사용자 이름과 암호를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. 정책 키를 선택한 다음 **에 추가를** **선택합니다.**
1. **옵션**에서 **수동**을 선택합니다.
1. **이름의**경우 **RestApiUser name을**입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 사용자 이름을 입력합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.
1. 정책 키를 다시 **선택합니다.**
1. **추가**를 선택합니다.
1. **옵션**에서 **수동**을 선택합니다.
1. **이름에**대 **한, 입력 RestApi암호**.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 암호를 입력합니다.
1. **키 사용**에는 **암호화**를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>HTTP 기본 인증을 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후 자격 증명을 참조하도록 REST API 기술 프로필 메타데이터를 구성합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. 인증 *유형을* `Basic`로 변경합니다.
1. *허용인보안오트생산을* .로 `false`변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

다음은 HTTP 기본 인증으로 구성된 RESTful 기술 프로필의 예입니다.

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

클라이언트 인증서 인증은 상호 인증서 기반 인증으로, 클라이언트 Azure AD B2C는 클라이언트 인증서를 서버에 제공하여 ID를 증명합니다. 이것은 SSL 핸드셰이크의 일부로 발생합니다. Azure AD B2C와 같은 적절한 인증서가 있는 서비스만 REST API 서비스에 액세스할 수 있습니다. 클라이언트 인증서는 X.509 디지털 인증서입니다. 프로덕션 환경에서는 인증서 기관에서 서명해야 합니다.

### <a name="prepare-a-self-signed-certificate-optional"></a>자체 서명된 인증서 준비(선택 사항)

비프로덕션 환경의 경우 인증서가 아직 없는 경우 자체 서명된 인증서를 사용할 수 있습니다. Windows에서 PowerShell의 [새 자체 서명인증서](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성할 수 있습니다.

1. 이 PowerShell 명령을 실행하여 자체 서명된 인증서를 생성합니다. 응용 `-Subject` 프로그램 및 Azure AD B2C 테넌트 이름에 맞게 인수를 수정합니다. 인증서에 `-NotAfter` 대한 다른 만료를 지정하도록 날짜를 조정할 수도 있습니다.
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
1. 열기 **사용자 인증서** > 현재**사용자** > **개인** > **인증서** > *yourappname.yourtenant.onmicrosoft.com.*
1. **작업** > **모든 작업** > 내보내기에 > 인증서를**선택합니다.**
1. **예** > **다음** > **예, 개인 키** > **내보내기 다음을 선택합니다.**
1. **파일 형식 내보내기에**대한 기본값을 수락합니다.
1. 인증서에 대한 암호를 제공합니다.

### <a name="add-a-client-certificate-policy-key"></a>클라이언트 인증서 정책 키 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. 정책 키를 선택한 다음 **에 추가를** **선택합니다.**
1. **옵션** 상자에서 **업로드**를 선택합니다.
1. **이름** 상자에 **RestApiClient인증서를**입력합니다.
    *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. **파일 업로드** 상자에서 프라이빗 키가 있는 인증서 .pfx 파일을 선택합니다.
1. **암호** 상자에 인증서의 암호를 입력합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>클라이언트 인증서 인증을 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후 REST API 기술 프로필 메타데이터를 구성하여 클라이언트 인증서를 참조합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. 인증 *유형을* `ClientCertificate`로 변경합니다.
1. *허용인보안오트생산을* .로 `false`변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

다음은 HTTP 클라이언트 인증서로 구성된 RESTful 기술 프로필의 예입니다.

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

## <a name="oauth2-bearer-authentication"></a>OAuth2 베어러 인증 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

베어러 토큰 인증은 [OAuth2.0 권한 부여 프레임워크: 보유자 토큰 사용(RFC 6750)에](https://www.rfc-editor.org/rfc/rfc6750.txt)정의되어 있습니다. 전달자 토큰 인증에서 Azure AD B2C는 권한 부여 헤더에 토큰을 가진 HTTP 요청을 보냅니다.

```http
Authorization: Bearer <token>
```

베어러 토큰은 불투명 문자열입니다. JWT 액세스 토큰 또는 REST API가 Azure AD B2C가 권한 부여 헤더로 보낼 것으로 예상하는 모든 문자열일 수 있습니다. Azure AD B2C는 다음 유형을 지원합니다.

- **베어러 토큰**. 나머지 기술 프로필에서 베어러 토큰을 보낼 수 있도록 정책은 먼저 보유자 토큰을 획득한 다음 RESTful 기술 프로필에서 사용해야 합니다.  
- **정적 베어러 토큰**. REST API가 장기 액세스 토큰을 발행할 때 이 방법을 사용합니다. 정적 베어러 토큰을 사용하려면 정책 키를 만들고 RESTful 기술 프로필에서 정책 키에 대한 참조를 만듭니다. 


## <a name="using-oauth2-bearer"></a>OAuth2 베어러 사용  

다음 단계에서는 클라이언트 자격 증명을 사용하여 전달자 토큰을 가져와 REST API 호출의 권한 부여 헤더로 전달하는 방법을 보여 줍니다.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>보유자 토큰을 저장할 클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 저장소를 제공합니다. [클레임 스키마는](claimsschema.md) 클레임을 선언하는 위치입니다. 액세스 토큰은 나중에 사용할 수 있도록 클레임에 저장되어야 합니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어, <em> `SocialAndLocalAccounts/` </em>.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [클레임스키마](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **클레임스키마** 요소에 도시 보유자토큰을 추가합니다.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>액세스 토큰 획득 

여러 가지 방법 중 하나에서 액세스 토큰을 가져올 수 있습니다: [페더레이션된 ID 공급자에서](idp-pass-through-custom.md)액세스 토큰을 가져오거나, 액세스 토큰을 반환하는 REST API를 호출하거나, [ROPC 흐름을](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth-ropc)사용하거나, 클라이언트 자격 증명 흐름을 사용하여 액세스할 수 [있습니다.](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)  

다음 예제에서는 REST API 기술 프로필을 사용하여 HTTP 기본 인증으로 전달된 클라이언트 자격 증명을 사용하여 Azure AD 토큰 끝점에 요청을 합니다. Azure AD에서 이를 구성하려면 [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름을](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-client-creds-grant-flow)참조하십시오. ID 공급자와 인터페이스하려면 이 것을 수정해야 할 수 있습니다. 

ServiceUrl의 경우 테넌트 이름을 Azure AD 테넌트의 이름으로 바꿉니다. 사용 가능한 모든 옵션에 대한 [RESTful 기술 프로파일](restful-technical-profile.md) 참조를 참조하십시오.

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

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>REST 기술 프로필을 변경하여 베어러 토큰 인증을 사용합니다.

사용자 지정 정책에서 베어러 토큰 인증을 지원하려면 REST API 기술 프로필을 다음과 같은 방법으로 수정합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 확장 정책 파일을 엽니다.
1. `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 검색합니다.
1. `<Metadata>` 요소를 찾습니다.
1. 다음과 같이 *인증 유형을* *Bearer로*변경합니다.
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 다음과 같이 *useClaimAsBearerToken을* *베어러토큰에*변경하거나 추가합니다. *bearerToken은* 보유자 토큰이 (출력 클레임에서) 검색될 클레임의 이름입니다. `SecureREST-AccessToken`

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 위에서 입력 클레임으로 사용된 클레임을 추가해야 합니다.

    ```xml
    <InputClaim ClaimTyeReferenceId="bearerToken"/>
    ```    

위의 스니펫을 추가한 후 기술 프로필은 다음과 같은 XML 코드와 같아야 합니다.

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
        <InputClaim ClaimTyeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>정적 OAuth2 베어러 사용 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>OAuth2 베어러 토큰 정책 키 추가

베어러 토큰 값을 저장하는 정책 키를 만듭니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스**를 선택하고 **Azure AD B2C**를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크**를 선택합니다.
1. 정책 키를 선택한 다음 **에 추가를** **선택합니다.**
1. **옵션**으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름**을 입력합니다. `RestApiBearerToken`)을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀**에 입력합니다.
1. **키 사용**에서 `Encryption`를 선택합니다.
1. **만들기**를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>REST API 기술 프로필을 구성하여 보유자 토큰 정책 키를 사용하도록 구성합니다.

필요한 키를 만든 후 REST API 기술 프로필 메타데이터를 구성하여 베어러 토큰을 참조합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예를 들면 `REST-ValidateProfile` 또는 `REST-GetProfile`와 같은 인터페이스를 정의할 수 있습니다.
1. `<Metadata>` 요소를 찾습니다.
1. 인증 *유형을* `Bearer`로 변경합니다.
1. *허용인보안오트생산을* .로 `false`변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

다음은 베어러 토큰 인증으로 구성된 RESTful 기술 프로필의 예입니다.

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

- IEF 참조에서 [나머지 기술 프로파일](restful-technical-profile.md) 요소에 대해 자세히 알아보십시오. 
