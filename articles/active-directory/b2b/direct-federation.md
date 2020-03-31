---
title: B2B - Azure AD에 대한 ID 공급자와의 직접 페더레이션
description: SAML 또는 WS-Fed ID 공급자와 직접 페더레이트하여 게스트가 Azure AD 앱에 로그인할 수 있도록 합니다.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050893"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>게스트 사용자를 위한 AD FS 및 타사 공급자와의 직접 페더레이션(미리 보기)
|     |
| --- |
| 직접 페더레이션은 Azure Active Directory의 공개 미리 보기 기능입니다. 미리 보기에 대한 자세한 내용은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관을](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)참조하십시오.|
|     |

이 문서에서는 B2B 공동 작업을 위해 다른 조직과 직접 페더레이션을 설정하는 방법에 대해 설명합니다. IDP(IDP)가 SAML 2.0 또는 WS-Fed 프로토콜을 지원하는 조직과 직접 페더레이션을 설정할 수 있습니다.
파트너의 IdP로 직접 페더레이션을 설정하면 해당 도메인의 새 게스트 사용자가 자체 IdP 관리 조직 계정을 사용하여 Azure AD 테넌트에 로그인하고 사용자와 공동 작업을 시작할 수 있습니다. 게스트 사용자가 별도의 Azure AD 계정을 만들 필요가 없습니다.
> [!NOTE]
> 직접 페더레이션 게스트 사용자는 테넌트 컨텍스트(예: `https://myapps.microsoft.com/?tenantid=<tenant id>` 또는 `https://portal.azure.com/<tenant id>`확인된 도메인의 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`경우)가 포함된 링크를 사용하여 로그인해야 합니다. 애플리케이션 및 리소스에 대한 직접 링크는 테넌트 컨텍스트를 포함하는 한 작동합니다. 직접 페더레이션 사용자는 현재 테넌트 컨텍스트가 없는 공통 끝점을 사용하여 로그인할 수 없습니다. 예를 들어 `https://myapps.microsoft.com`을 `https://portal.azure.com`사용하거나 `https://teams.microsoft.com` 오류가 발생합니다.
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>게스트 사용자는 언제 직접 페더레이션으로 인증되나요?
조직과 직접 페더레이션을 설정한 후 초대하는 모든 새 게스트 사용자는 직접 페더레이션을 사용하여 인증됩니다. 직접 페더레이션을 설정해도 초대장을 이미 사용한 게스트 사용자의 인증 방법은 변경되지 않습니다. 예를 들어 다음과 같은 노래를 선택할 수 있다.
 - 게스트 사용자가 이미 초대를 사용했으며 이후에 조직과 직접 페더레이션을 설정한 경우 해당 게스트 사용자는 직접 페더레이션을 설정하기 전에 사용한 것과 동일한 인증 방법을 계속 사용합니다.
 - 파트너 조직과 직접 페더레이션을 설정하고 게스트 사용자를 초대한 다음 파트너 조직이 나중에 Azure AD로 이동하면 초대를 이미 사용한 게스트 사용자는 직접 페더레이션을 계속 사용합니다. 테넌트에 페더레이션 정책이 있습니다.
 - 파트너 조직과의 직접 페더레이션을 삭제하면 현재 직접 페더레이션을 사용하는 게스트 사용자는 로그인할 수 없습니다.

이러한 시나리오에서 디렉터리에서 게스트 사용자 계정을 삭제하고 다시 초대하여 게스트 사용자의 인증 방법을 업데이트할 수 있습니다.

직접 페더레이션은 contoso.com 및 fabrikam.com 같은 도메인 네임스페이스에 연결됩니다. AD FS 또는 타사 IdP를 사용하여 직접 페더레이션 구성을 설정하는 경우 조직은 하나 이상의 도메인 네임스페이스를 이러한 IdP에 연결합니다. 

## <a name="end-user-experience"></a>최종 사용자 환경 
직접 페더레이션을 사용하면 게스트 사용자가 자체 조직 계정을 사용하여 Azure AD 테넌트에 로그인합니다. 공유 리소스에 액세스하고 로그인하라는 메시지가 표시되면 직접 페더레이션 사용자가 IdP로 리디렉션됩니다. 로그인에 성공하면 Azure AD로 반환되어 리소스에 액세스합니다. 직접 페더레이션 사용자의 새로 고침 토큰은 Azure AD에서 [통과 새로 고침 토큰의 기본 길이인](../develop/active-directory-configurable-token-lifetimes.md#exceptions) 12시간 동안 유효합니다. 페더레이션된 IdP에 SSO가 활성화된 경우 사용자는 SSO를 경험하게 되며 초기 인증 후 로그인 프롬프트가 표시되지 않습니다.

## <a name="limitations"></a>제한 사항

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD에서 DNS 인증 도메인
페더레이를 사용할 도메인은 Azure AD에서 DNS 를 ***확인해서는*** 안 됩니다. DNS가 확인되지 않으므로 관리되지 않는 Azure AD 테넌트(전자 메일 확인 또는 "바이러스")로 직접 페더레이션을 설정할 수 있습니다.

### <a name="authentication-url"></a>인증 URL
직접 페더레이션은 인증 URL의 도메인이 대상 도메인과 일치하거나 인증 URL이 허용된 ID 공급자 중 하나인 정책에 대해서만 허용됩니다(이 목록은 변경될 수 있음).
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

예를 들어 **fabrikam.com**대한 직접 페더레이션을 `https://fabrikam.com/adfs` 설정할 때 인증 URL은 유효성 검사를 전달합니다. 예를 들어 `https://sts.fabrikam.com/adfs`동일한 도메인의 호스트도 전달됩니다. 그러나 인증 URL `https://fabrikamconglomerate.com/adfs` `https://fabrikam.com.uk/adfs` 또는 동일한 도메인에 대 한 전달 되지 않습니다.

### <a name="signing-certificate-renewal"></a>인증서 갱신 서명
ID 공급자 설정에서 메타데이터 URL을 지정하면 Azure AD가 만료되면 서명 인증서가 자동으로 갱신됩니다. 그러나 만료 시간 전에 어떤 이유로든 인증서가 회전되거나 메타데이터 URL을 제공하지 않는 경우 Azure AD는 인증서를 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트해야 합니다.

### <a name="limit-on-federation-relationships"></a>페더레이션 관계 제한
현재 최대 1,000개의 페더레이션 관계가 지원됩니다. 이 제한에는 [내부 페더레이션과](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0) 직접 페더레이션이 모두 포함됩니다.

### <a name="limit-on-multiple-domains"></a>여러 도메인에 대한 제한
현재 동일한 테넌트의 여러 도메인을 가진 직접 페더레이션을 지원하지 않습니다.

## <a name="frequently-asked-questions"></a>질문과 대답
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>관리되지 않는(전자 메일 확인) 테넌트가 있는 도메인으로 직접 페더레이션을 설정할 수 있습니까? 
예. 도메인이 확인되지 않았고 테넌트가 [관리자 인계를](../users-groups-roles/domains-admin-takeover.md)거치지 않은 경우 해당 도메인과 직접 페더레이션을 설정할 수 있습니다. 관리되지 않거나 전자 메일로 확인된 테넌트는 사용자가 B2B 초대를 사용하거나 현재 존재하지 않는 도메인을 사용하여 Azure AD에 대한 셀프 서비스 등록을 수행할 때 만들어집니다. 이러한 도메인을 통해 직접 페더레이션을 설정할 수 있습니다. Azure 포털또는 PowerShell을 통해 DNS가 인증한 도메인으로 직접 페더레이션을 설정하려고 하면 오류가 표시됩니다.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>직접 페더레이션과 전자 메일 일회성 암호 인증이 모두 활성화되어 있는 경우 어떤 메서드가 우선합니까?
파트너 조직과 직접 페더레이션을 설정하면 해당 조직의 새 게스트 사용자에 대한 전자 메일 일회성 암호 인증보다 우선합니다. 게스트 사용자가 직접 페더레이션을 설정하기 전에 일회용 암호 인증을 사용하여 초대를 사용한 경우 일회성 암호 인증을 계속 사용합니다. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>부분적으로 동기화된 테넌시로 인해 직접 페더레이션이 로그인 문제를 해결합니까?
아니요, 이 시나리오에서는 [전자 메일 일회용 암호](one-time-passcode.md) 기능을 사용해야 합니다. "부분적으로 동기화된 테넌시"는 온-프레미스 사용자 ID가 클라우드에 완전히 동기화되지 않는 파트너 Azure AD 테넌트를 나타냅니다. 아직 클라우드에 ID가 없지만 B2B 초대장을 사용하려고 시도하는 게스트는 로그인할 수 없습니다. 일회용 암호 기능을 사용하면 이 게스트가 로그인할 수 있습니다. 직접 페더레이션 기능은 게스트가 자체 IdP 관리 조직 계정을 가지고 있지만 조직에 Azure AD 가 전혀 없는 시나리오를 해결합니다.

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>1단계: 파트너 조직의 ID 공급자 구성
먼저 파트너 조직은 필요한 클레임및 신뢰하는 파티 트러스트를 사용하여 ID 공급자를 구성해야 합니다. 

> [!NOTE]
> 직접 페더레이션에 대한 ID 공급자를 구성하는 방법을 설명하기 위해 AD FS(Active Directory Federation 서비스)를 예로 들어 보겠습니다. 직접 페더레이션을 준비하기 위해 AD FS를 SAML 2.0 또는 WS-Fed ID 공급자로 구성하는 방법의 예를 제공하는 [AD FS와 직접 페더레이션 구성](direct-federation-adfs.md)문서를 참조하십시오.

### <a name="saml-20-configuration"></a>SAML 2.0 구성

Azure AD B2B는 아래에 나열된 특정 요구 사항과 함께 SAML 프로토콜을 사용하는 ID 공급자와 페더레이트하도록 구성할 수 있습니다. SAML ID 공급자와 Azure AD 간에 트러스트를 설정하는 방법에 대한 자세한 내용은 단일 사인온 에 [대한 SAML 2.0 ID 공급자(IdP) 사용을](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)참조하십시오.  

> [!NOTE]
> 직접 페더레이션의 대상 도메인은 Azure AD에서 DNS를 확인할 수 없습니다. 인증 URL 도메인은 대상 도메인과 일치해야 하거나 허용된 ID 공급자의 도메인이어야 합니다. 자세한 내용은 [제한](#limitations) 섹션을 참조하십시오. 

#### <a name="required-saml-20-attributes-and-claims"></a>필요한 SAML 2.0 속성 및 클레임
다음 표에는 타사 ID 공급자에서 구성해야 하는 특정 특성 및 클레임에 대한 요구 사항이 표시됩니다. 직접 페더레이션을 설정하려면 ID 공급자의 SAML 2.0 응답에서 다음 특성을 수신해야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다.

IdP의 SAML 2.0 응답에 필요한 특성:

|특성  |값  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 발급자 URI`http://www.example.com/exk10l6w90DHM0yi...`         |


IdP에서 발행한 SAML 2.0 토큰에 대한 필수 클레임:

|특성  |값  |
|---------|---------|
|이름 ID 형식     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 구성 
Azure AD B2B는 아래에 나열된 몇 가지 특정 요구 사항과 함께 WS-Fed 프로토콜을 사용하는 ID 공급자와 페더레이트하도록 구성할 수 있습니다. 현재 두 WS-Fed 공급자는 Azure AD와의 호환성테스트를 거쳤으며 AD FS 및 Shibboleth를 포함합니다. Azure AD를 사용하여 WS-Fed 호환 공급자 간에 신뢰 당사자 트러스트를 설정하는 자세한 내용은 [Azure AD ID 공급자 호환성 문서에서](https://www.microsoft.com/download/details.aspx?id=56843)사용할 수 있는 "WS 프로토콜을 사용한 STS 통합 문서"를 참조하십시오.

> [!NOTE]
> 직접 페더레이션의 대상 도메인은 Azure AD에서 DNS를 확인할 수 없습니다. 인증 URL 도메인은 허용된 ID 공급자의 대상 도메인 또는 도메인과 일치해야 합니다. 자세한 내용은 [제한](#limitations) 섹션을 참조하십시오. 

#### <a name="required-ws-fed-attributes-and-claims"></a>필요한 WS-Fed 속성 및 클레임

다음 표에는 타사 WS-Fed ID 공급자에서 구성해야 하는 특정 특성 및 클레임에 대한 요구 사항이 표시됩니다. 직접 페더레이션을 설정하려면 ID 공급자의 WS-Fed 메시지에서 다음 특성을 수신해야 합니다. 이러한 특성은 온라인 보안 토큰 서비스 XML 파일에 연결하거나 수동으로 입력하여 구성할 수 있습니다.

IDP의 WS-Fed 메시지에 필요한 특성:
 
|특성  |값  |
|---------|---------|
|수동 요청자 엔드포인트     |`https://login.microsoftonline.com/login.srf`         |
|사용자     |`urn:federation:MicrosoftOnline`         |
|발급자     |예를 들어 파트너 IdP의 발급자 URI`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP에서 발행한 WS-Fed 토큰에 대한 필수 클레임:

|특성  |값  |
|---------|---------|
|불변ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>2단계: Azure AD에서 직접 페더레이션 구성 
다음으로 Azure AD의 1단계에서 구성된 ID 공급자를 사용한 페더레이션을 구성합니다. Azure AD 포털 또는 PowerShell을 사용할 수 있습니다. 직접 페더레이션 정책이 적용되기까지 5~10분이 걸릴 수 있습니다. 이 시간 동안직접 페더레이션 도메인에 대한 초대를 사용하려고 시도하지 마십시오. 다음과 같은 특성이 필요합니다.
- 파트너 IdP의 발급자 URI
- 파트너 IdP의 수동 인증 끝점(https만 지원)
- 인증서

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Azure AD 포털에서 직접 페더레이션을 구성하려면

1. [Azure 포털로](https://portal.azure.com/)이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **ID 공급자를**선택한 다음 **새 SAML/WS-Fed IdP를**선택합니다.

    ![새로운 SAML 또는 WS-Fed IdP를 추가하기 위한 스크린샷 표시 버튼](media/direct-federation/new-saml-wsfed-idp.png)

4. 새 **SAML/WS-Fed IdP** 페이지에서 **ID 공급자 프로토콜에서** **SAML** 또는 **WS-FED**를 선택합니다.

    ![SAML 또는 WS-Fed IdP 페이지에서 구문 분석 단추를 보여주는 스크린샷](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 직접 페더레이션의 대상 도메인 이름이 될 파트너 조직의 도메인 이름을 입력합니다.
6. 메타데이터 파일을 업로드하여 메타데이터 세부 정보를 채울 수 있습니다. 메타데이터를 수동으로 입력하도록 선택한 경우 다음 정보를 입력합니다.
   - 파트너 IdP의 도메인 이름
   - 파트너 IdP의 엔터티 ID
   - 파트너 IdP의 수동 요청자 끝점
   - 인증서
   > [!NOTE]
   > 메타데이터 URL은 선택 사항이지만 권장됩니다. 메타데이터 URL을 제공하는 경우 Azure AD는 만료될 때 서명 인증서를 자동으로 갱신할 수 있습니다. 만료 시간 전에 어떤 이유로든 인증서가 회전되거나 메타데이터 URL을 제공하지 않는 경우 Azure AD는 인증서를 갱신할 수 없습니다. 이 경우 서명 인증서를 수동으로 업데이트해야 합니다.

7. **저장**을 선택합니다. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>PowerShell을 사용하여 Azure AD에서 직접 페더레이션을 구성하려면

1. 그래프 모듈에 대한 Azure AD PowerShell의 최신 버전을 설치합니다([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). 자세한 단계가 필요한 경우 게스트 사용자를 추가하기 위한 빠른 시작에는 [최신 AzureADPreview 모듈 설치 섹션이](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)포함됩니다. 
2. 다음 명령 실행: 
   ```powershell
   Connect-AzureAD
   ```
1. 로그인 프롬프트에서 관리되는 글로벌 관리자 계정으로 로그인합니다. 
2. 페더레이션 메타데이터 파일의 값을 대체하여 다음 명령을 실행합니다. AD FS 서버 및 Okta의 경우 페더레이션 파일은 `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`페더레이션메타디에이티드.xml입니다. 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>3단계: Azure AD에서 직접 페더레이션 테스트
이제 새 B2B 게스트 사용자를 초대하여 직접 페더레이션 설정을 테스트합니다. 자세한 내용은 [Azure 포털의 Azure AD B2B 공동 작업 사용자 추가를](add-users-administrator.md)참조하십시오.
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>직접 페더레이션 관계를 편집하려면 어떻게 해야 합니까?

1. [Azure 포털로](https://portal.azure.com/)이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **ID 공급자** 선택
4. **SAML/WS-Fed ID 공급자에서**공급자를 선택합니다.
5. ID 공급자 세부 정보 창에서 값을 업데이트합니다.
6. **저장**을 선택합니다.


## <a name="how-do-i-remove-direct-federation"></a>직접 페더레이션을 제거하려면 어떻게 해야 합니까?
직접 페더레이션 설정을 제거할 수 있습니다. 이렇게 하면 초대장을 이미 사용한 직접 페더레이션 게스트 사용자는 로그인할 수 없습니다. 그러나 디렉터리에서 리소스를 삭제하고 다시 초대하여 리소스에 다시 액세스할 수 있습니다. Azure AD 포털에서 ID 공급자와의 직접 페더레이션을 제거하려면 다음을 수행하십시오.

1. [Azure 포털로](https://portal.azure.com/)이동합니다. 왼쪽 창에서 **Azure Active Directory**를 선택합니다. 
2. **조직 관계**를 선택합니다.
3. **ID 공급자를**선택합니다.
4. ID 공급자를 선택한 다음 **삭제를**선택합니다. 
5. **예**를 선택하여 삭제를 확인합니다. 

PowerShell을 사용하여 ID 공급자와의 직접 페더레이션을 제거하려면 다음을 수행합니다.
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
