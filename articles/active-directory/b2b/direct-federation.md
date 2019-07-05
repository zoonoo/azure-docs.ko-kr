---
title: B2B-Azure Active Directory에 대 한 id 공급자와의 직접 페더레이션을 설정 | Microsoft Docs
description: 직접 게스트가 Azure AD 앱에 로그인 할 수 있도록 SAML 또는 Ws-fed id 공급자와 페더레이션
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4dadc68e78fbaa979751d5bcd04ef481c3ab886
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/03/2019
ms.locfileid: "67544349"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>AD FS 및 게스트 사용자 (미리 보기)에 대 한 타사 공급자를 사용 하 여 직접 페더레이션
|     |
| --- |
| 직접 페더레이션에는 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.|
|     |

이 문서에서는 B2B 공동 작업에 대 한 다른 조직과 직접 페더레이션을 설정 하는 방법을 설명 합니다. 해당 id 공급자 (IdP)에 SAML 2.0 또는 Ws-fed 프로토콜을 지 원하는 모든 조직과 직접 페더레이션을 설정할 수 있습니다.
파트너의 IdP와의 직접 페더레이션을 설정할 때 해당 도메인에서 새 게스트 사용자를 Azure AD 테 넌 트에 로그인 하 여 사용자와 공동 작업을 시작 IdP로 관리 되는 자신의 조직 계정을 사용할 수 있습니다. 필요는 없습니다 만들려면 별도 게스트 사용자에 대 한 Azure AD 계정.
> [!NOTE]
> 직접 페더레이션 게스트 사용자를 테 넌 트 컨텍스트가 포함 된 링크를 사용 하 여 로그인 해야 합니다 (예를 들어 `https://myapps.microsoft.com/?tenantid=<tenant id>` 나 `https://portal.azure.com/<tenant id>`, 또는 확인된 된 도메인의 경우 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`). 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 직접 페더레이션 사용자가 현재 컨텍스트가 없습니다. 테 넌 트에 있는 공용 끝점을 사용 하 여 로그인 할지 않습니다. 예를 들어,를 사용 하 여 `https://myapps.microsoft.com`, `https://portal.azure.com`, 또는 `https://teams.microsoft.com` 오류가 발생 합니다.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>직접 페더레이션을 사용 하 여 게스트 사용자를 인증 하는 경우
조직과 직접 페더레이션을 설정 하면 직접 페더레이션을 사용 하 여 초대 하는 모든 새 게스트 사용자 인증 됩니다. 직접 페더레이션 설정에서 초대를 이미 사용 하셨습니다 게스트 사용자에 대 한 인증 방법을 변경 되지 않는지 확인 하는 것이 반드시 합니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.
 - 게스트 사용자 초대를 하 고 이후에 조직과 직접 페더레이션을 설정할 수를 이미 사용 하셨습니다, 하는 경우 이러한 게스트 사용자 직접 페더레이션을 설정 하기 전에 사용 되는 동일한 인증 방법을 사용 하려면 계속 됩니다.
 - 게스트 사용자 초대를 이미 사용한 직접으로 직접 페더레이션을 사용 하도록 계속 파트너 조직과 직접 페더레이션을 설정 하 고 게스트 사용자를 초대 하 고 나중에 파트너 조직의 Azure AD로 이동 하는 다음을 하는 경우 테 넌 트의 페더레이션 정책이 있습니다.
 - 파트너 조직과 직접 페더레이션을 삭제 하면 현재 직접 페더레이션을 사용 하 여 모든 게스트 사용자 로그인 수 없습니다.

이러한 시나리오에서 디렉터리에서 게스트 사용자 계정 삭제 및 다시 하 여 게스트 사용자의 인증 방법을 업데이트할 수 있습니다.

직접 페더레이션은 contoso.com과 fabrikam.com 등의 도메인 네임 스페이스에 연결 됩니다. AD FS 또는 타사 IdP를 사용 하 여 직접 페더레이션을 구성을 설정할 때 조직은 이러한 Idp에 하나 이상의 도메인 네임 스페이스를 연결 합니다. 

## <a name="end-user-experience"></a>최종 사용자 환경 
직접 페더레이션을 사용 하 여 게스트 사용자가 자신의 조직 계정을 사용 하 여 Azure AD 테 넌 트에 로그인 합니다. 공유 리소스에 액세스 하는 로그인에 대 한 메시지가 표시 되는 때를 직접 페더레이션 사용자가 해당 IdP로 리디렉션됩니다. 성공적인 로그인 후 리소스에 액세스 하려면 Azure AD로 반환 됩니다. 직접 페더레이션 사용자의 새로 고침 토큰은 12 시간 동안 유효 합니다 [길이 통과 새로 고침 토큰에 대 한 기본](../develop/active-directory-configurable-token-lifetimes.md#exceptions) Azure AD에서. 페더레이션된 IdP SSO가 설정 된 경우 사용자는 SSO를 경험 하 고 초기 인증 후 모든 로그인 프롬프트를 표시 되지 않습니다.

## <a name="limitations"></a>제한 사항

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD의 도메인 DNS 확인
직접 페더레이션 도메인에 대해서만 허용 됩니다 ***되지*** Azure AD에서 DNS 확인 합니다. DNS를 확인할 수 없기 때문에 관리 되지 않는 (전자 메일 확인 또는 "바 이럴") Azure AD 테 넌 트에 대 한 직접 페더레이션을 허용 됩니다.
### <a name="authentication-url"></a>인증 URL
직접 페더레이션은 인증 URL의 도메인 대상 도메인에 일치 하는 위치 또는 인증 URL이 이러한 허용 된 id 공급자 (이 목록은 변경 될 수 있습니다) 중 하나에 정책에 대 한 허용 됩니다.
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

예를 들어에 대 한 직접 페더레이션을 설정할 때 **fabrikam.com**, 인증 URL `https://fabrikam.com/adfs` 유효성 검사를 통과 합니다. 동일한 도메인에 호스트를 전달할 수도, 예를 들어 `https://sts.fabrikam.com/adfs`합니다. 그러나 인증 URL `https://fabrikamconglomerate.com/adfs` 또는 `https://fabrikam.com.uk/adfs` 에 동일한 도메인에 전달 되지 않습니다.

### <a name="signing-certificate-renewal"></a>서명 인증서 갱신
Id 공급자 설정에서 메타 데이터 URL을 지정 하면 만료 되 면 Azure AD 서명 인증서를 갱신 자동으로 됩니다. 그러나 인증서가 만료 시간 전에 어떤 이유로 회전 또는 메타 데이터 URL을 제공 하지 않으면 Azure AD 없게 됩니다 갱신 합니다. 이 경우 서명 인증서를 수동으로 업데이트 해야 합니다.
## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="can-i-set-up-direct-federation-with-an-unmanaged-email-verified-tenant"></a>관리 되지 않는 (전자 메일 확인) 테 넌와 직접 페더레이션의 설정할 수 있나요? 
예. 도메인 확인 되지 않은 및 테 넌 트 수행 하지 않은 경우는 [관리자 인수](../users-groups-roles/domains-admin-takeover.md), 직접 페더레이션을 설정할 수 있습니다. 관리 되지 않는 또는 전자 메일 확인 테 넌 트 사용자 B2B 초대를 교환 하거나 현재 존재 하지 않는 도메인을 사용 하 여 Azure AD의 셀프 서비스 등록을 수행 하는 경우에 생성 됩니다. 이러한 도메인을 사용 하 여 직접 페더레이션을 설정할 수 있습니다. DNS 확인 된 도메인을 Azure portal 또는 PowerShell을 통해 직접 페더레이션 설정 하려고 하면 오류가 표시 됩니다.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>직접 페더레이션 및 전자 메일 (일회성 암호) 인증 모두에 설정 된 경우 메서드는 우선?
직접 페더레이션 파트너 조직과 설정 된 경우 우선 해당 조직의 새 게스트 사용자에 게 전자 메일 (일회성 암호) 인증 합니다. 게스트 사용자의 직접 페더레이션을 설정 하기 전에 (일회성 암호) 인증을 사용 하 여 초대에 사용한 경우 (일회성 암호) 인증을 사용 하도록 계속 됩니다. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>페더레이션 로그인 문제는 부분적으로 동기화 된 테 넌 트로 인해 직접?
아니요, 합니다 [일회용 암호 전자 메일](one-time-passcode.md) 기능은이 시나리오에서는 사용 해야 합니다. "부분적으로 동기화 된 테 넌 트" 클라우드로 온-프레미스 사용자 id를 동기화 완벽 하 게 되지 않습니다 파트너 Azure AD 테 넌 트를 가리킵니다. 게스트 id 클라우드에서 아직 존재 하지 않지만 B2B 초대를 사용 하려고 하는 로그인 할 수 없습니다. (일회성 암호) 기능을 로그인이 게스트를 허용 합니다. 직접 페더레이션 기능 게스트가 자신의 IdP에서 관리 하는 조직 계정 있었지만 조직에 Azure AD 표시 되지 않습니다 전혀 시나리오를 해결 합니다.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1단계: 파트너 조직의 id 공급자 구성
먼저 파트너 조직 필수 클레임 및 신뢰 당사자 트러스트를 사용 하 여 해당 id 공급자를 구성 해야 합니다. 

> [!NOTE]
> 직접 페더레이션에 대 한 id 공급자를 구성 하는 방법을 설명 하기 위해, Active Directory Federation Services (AD FS)를 예로 사용 하겠습니다. 문서를 참조 하세요 [AD FS와의 직접 페더레이션을 구성](direct-federation-adfs.md), SAML 2.0 또는 Ws-fed id 공급자로 직접 페더레이션 위한 준비 과정에서 AD FS를 구성 하는 방법의 예제를 제공 하는 합니다.

### <a name="saml-20-configuration"></a>SAML 2.0 구성

아래에 나열 된 특정 요구를 사용 하 여 SAML 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 azure AD B2B은 구성할 수 있습니다. SAML id 공급자와 Azure AD 간의 트러스트를 설정 하는 방법에 대 한 자세한 내용은 참조 하세요. [SAML 2.0 Id 공급자 (IdP)에 대 한 Single Sign-on 사용](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)합니다.  

> [!NOTE]
> 직접 페더레이션에 대 한 대상 도메인 참고 아니어야 DNS 확인 된 Azure AD에서 합니다. 인증 URL 도메인에 대상 도메인과 일치 해야 합니다 또는 허용 된 id 공급자의 도메인 이어야 합니다. 참조 된 [제한 사항](#limitations) 세부 정보에 대 한 섹션입니다. 

#### <a name="required-saml-20-attributes-and-claims"></a>필수 SAML 2.0 특성 및 클레임
다음 표에 특정 특성 및 타사 id 공급자에서 구성 해야 하는 클레임에 대 한 요구 사항을 보여 줍니다. 직접 페더레이션을 설정 하려면 다음 특성을 id 공급자 SAML 2.0 응답에 수신 되어야 합니다. 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 이러한 특성을 구성할 수 있습니다.

IdP에서 SAML 2.0 응답에 대 한 필수 속성:

|특성  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 URI 발급자 `http://www.example.com/exk10l6w90DHM0yi...`         |


IdP에서 발급 한 SAML 2.0 토큰에 대 한 필수 클레임:

|특성  |값  |
|---------|---------|
|NameID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Ws-fed 구성 
아래에 나열 된 몇 가지 특정 요구 사항을 Ws-fed 프로토콜을 사용 하는 id 공급자와 페더레이션 하도록 azure AD B2B은 구성할 수 있습니다. 현재 AD FS 및 Shibboleth를 포함 하는 Azure AD와의 호환성에 대 한 두 Ws-fed 공급자 테스트 되었습니다. Azure AD 사용 하 여 Ws-fed 규격 공급자 간에 신뢰 당사자 트러스트를 설정 하는 방법에 대 한 자세한 내용은 "STS 통합 문서 WS 프로토콜을 사용 하 여"을 참조 하세요. 사용할 수 있습니다 합니다 [Azure AD Identity Provider 호환성 문서](https://www.microsoft.com/download/details.aspx?id=56843)합니다.

> [!NOTE]
> 직접 페더레이션에 대 한 대상 도메인 아니어야 DNS 확인 된 Azure AD에서 합니다. 대상 도메인 또는 허용 된 id 공급자의 도메인에 인증 URL 도메인 일치 해야 합니다. 참조 된 [제한 사항](#limitations) 세부 정보에 대 한 섹션입니다. 

#### <a name="required-ws-fed-attributes-and-claims"></a>필수 Ws-fed 특성 및 클레임

다음 표에 특정 특성 및 Ws-fed와 타사 id 공급자에 구성 해야 하는 클레임에 대 한 요구 사항을 보여 줍니다. 직접 페더레이션을 설정 하려면 다음 특성을 id 공급자의 Ws-fed 메시지에서 수신 되어야 합니다. 온라인 보안 토큰 서비스 XML 파일에 연결 하거나 수동으로 입력 하 여 이러한 특성을 구성할 수 있습니다.

IdP에서 Ws-fed 메시지에 필요한 특성:
 
|특성  |값  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|대상     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 URI 발급자 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발급 한 Ws-fed 토큰에 대 한 필수 클레임:

|특성  |값  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2단계: Azure AD에서 직접 페더레이션 구성 
다음으로, Azure AD에서 1 단계에서 구성 하는 id 공급자를 사용 하 여 페더레이션을 구성 합니다. Azure AD 포털 또는 PowerShell 중 하나를 사용할 수 있습니다. 직접 페더레이션의 정책을 적용 하기 전에 5-10 분 정도 걸릴 수 있습니다. 이 시간 동안 직접 페더레이션 도메인에 대 한 초대를 교환 하는 하지 마세요. 다음과 같은 특성이 필요합니다.
- 발급자 IdP 파트너의 URI
- 파트너 (https만 지원 됨)는 IdP의 수동 인증 끝점
- 인증서

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Azure AD 포털의 직접 페더레이션을 구성 하려면

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. 선택 **Id 공급자**를 선택한 후 **새 SAML/Ws-fed IdP**합니다.

    ![새 SAML 또는 Ws-fed IdP를 추가 하는 것에 대 한 스크린 샷 표시 단추](media/direct-federation/new-saml-wsfed-idp.png)

4. 에 **새 SAML/Ws-fed IdP** 페이지의 **Id 공급자 프로토콜**를 선택 **SAML** 하거나 **WS-FED**.

    ![SAML 또는 Ws-fed IdP 페이지의 스크린 샷 보여 주는 구문 분석 단추](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 파트너 조직의 도메인 이름을 직접 페더레이션에 대 한 대상 도메인 이름 입력
6. 메타 데이터 정보를 채우는 데 메타 데이터 파일을 업로드할 수 있습니다. 메타 데이터를 수동으로 입력 하려는 경우 다음 정보를 입력 합니다.
   - 파트너 IdP의 도메인 이름
   - 파트너 IdP 엔터티 ID
   - 파트너 IdP의 수동 요청자 끝점
   - 인증서
   > [!NOTE]
   > 메타 데이터 URL은 강력한 권장 되지만 선택 사항입니다. 메타 데이터 URL을 제공 하는 경우 만료 되 면 Azure AD 서명 인증서를 갱신 자동으로 수 있습니다. 인증서가 만료 시간 전에 어떤 이유로 회전 또는 메타 데이터 URL을 제공 하지 않는 경우 Azure AD 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트 해야 합니다.

7. **저장**을 선택합니다. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>PowerShell을 사용 하 여 Azure AD에서 직접 페더레이션을 구성 하려면

1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (게스트 사용자를 추가 하기 위한 빠른 시작 섹션을 포함 하는 자세한 단계에 필요한 경우 [최신 AzureADPreview module 설치](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module).) 
2. 다음 명령 실행: 
   ```powershell
   Connect-AzureAD
   ```
1. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다. 
2. 페더레이션 메타 데이터 파일의 값을 바꾸는 다음 명령을 실행 합니다. AD FS 서버 및 Okta에 대 한 페더레이션 파일이 federationmetadata.xml, 예를 들어: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`합니다. 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3단계: Azure AD에서 직접 페더레이션을 테스트합니다
이제 새 B2B 게스트 사용자를 초대 하 여 직접 페더레이션을 설정을 테스트 합니다. 자세한 내용은 참조 하세요 [Azure portal에서 추가 Azure AD B2B 공동 작업 사용자](add-users-administrator.md)합니다.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>직접 페더레이션 관계를 편집 하는 방법

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. 선택 **Id 공급자**
4. 아래 **id 공급자 SAML/Ws-fed**, 공급자를 선택 합니다.
5. Id 공급자 세부 정보 창에서 값을 업데이트 합니다.
6. **저장**을 선택합니다.


## <a name="how-do-i-remove-direct-federation"></a>직접 페더레이션을 제거 하려면 어떻게 해야 하나요?
직접 페더레이션 설치 프로그램을 제거할 수 있습니다. 이렇게 하면 직접 페더레이션을 게스트 사용자가 초대를 교환 하는 로그인 할 수 없습니다. 하지만 액세스 제공할 수 있습니다 이러한 리소스에 다시 디렉터리에서 삭제 하 고 다시 합니다. Azure AD 포털의 id 공급자와의 직접 페더레이션을 제거:

1. [Azure 포털](https://portal.azure.com/)로 이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. 선택 **Id 공급자**합니다.
4. Id 공급자를 선택 하 고 선택한 **삭제**합니다. 
5. **예**를 선택하여 삭제를 확인합니다. 

PowerShell를 사용 하 여 id 공급자와의 직접 페더레이션을 제거 합니다.
1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. 다음 명령 실행: 
   ```powershell
   Connect-AzureAD
   ```
3. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다. 
4. 다음 명령을 입력합니다.
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
