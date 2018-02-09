---
title: "AD FS 온-프레미스 앱을 Azure로 마이그레이션 | Microsoft Docs"
description: "이 문서는 조직에서 페더레이션된 SaaS 응용 프로그램에 중점을 두고 온-프레미스 응용 프로그램을 Azure AD로 마이그레이션하는 방법을 이해할 수 있도록 돕기 위한 것입니다."
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/29/2018
ms.author: billmath
ms.openlocfilehash: ec0731534da2543d48bedc575bf882b790fa136b
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/01/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>AD FS 온-프레미스 앱을 Azure로 마이그레이션 

이 문서는 조직에서 응용 프로그램을 Azure AD로 마이그레이션하는 방법을 이해할 수 있도록 돕기 위한 것입니다.  페더레이션된 SaaS 응용 프로그램에 중점을 두고 있습니다.  단계별 지침은 이 문서에서 제공하지 않습니다.  대신 온-프레미스 구성이 Azure AD로 변환되는 방식을 이해함으로써 마이그레이션을 수행하는 데 도움이 되는 개념적 지침을 제공합니다. 또한 가장 일반적인 시나리오에 필요한 내용도 다루고 있습니다.

## <a name="introduction"></a>소개

사용자 계정이 포함된 온-프레미스 디렉터리가 있는 경우 적어도 한두 개의 응용 프로그램이 있을 가능성이 있습니다.  이러한 앱은 사용자가 해당 ID로 로그온하여 액세스할 수 있도록 구성되어 있습니다.

그리고 대부분의 조직과 마찬가지로 클라우드 응용 프로그램 및 ID를 도입하는 과정에 있을 가능성도 있습니다.  아마도 Office 365 및 Azure AD Connect를 사용하여 실행하고 있을 것입니다.  일부 주요 작업을 위해 클라우드 기반 SaaS 응용 프로그램을 설치했을 것입니다.  

많은 조직에는 Office 365 및 Azure AD 기반 응용 프로그램과 함께 SaaS 또는 사용자 지정 LoB(기간 업무) 응용 프로그램이 온-프레미스 로그온 서비스(예: AD FS(Active Directory Federation Service))에 직접 페더레이션되어 있습니다.  이 마이그레이션 가이드에서는 온-프레미스 응용 프로그램을 Azure AD로 마이그레이션하는 이유와 방법에 대해 설명합니다.

>[!NOTE]
>이 가이드는 사용자 지정 LoB 응용 프로그램에 대한 고급 정보와 함께 SaaS 응용 프로그램 구성 및 마이그레이션에 대한 자세한 정보를 제공합니다.  사용자 지정 LoB 앱에 대한 자세한 지침은 나중에 제공할 예정입니다.

그림 1: 온-프레미스에 직접 연결된 응용프로그램 ![온-프레미스](media/migrate-adfs-apps-to-azure/migrate1.png)

그림 2: Azure AD를 통해 페더레이션된 응용 프로그램 ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>앱을 Azure AD로 마이그레이션해야 하는 이유

조직에서 이미 AD FS, Ping 또는 다른 온-프레미스 인증 공급자를 사용하는 경우, 앱을 Azure AD로 마이그레이션하면 다음과 같은 이점이 있습니다.

**더 안전한 액세스**
- [Azure AD 조건부 액세스](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)를 사용하여 MFA(Multi-Factor Authentication)를 포함한 세부적인 응용 프로그램별 액세스 제어를 구성합니다.  이 정책은 Office 365와 동일한 방식으로 SaaS 및 사용자 지정 앱에 적용할 수 있습니다.
- 위험한 트래픽을 식별하는 기계 학습 및 추론에 따라 위협을 탐지하고 로그온을 보호하려면 [Azure AD ID 보호](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)를 사용하여 Azure AD의 기본 제공 기능 및 지속적으로 발전하는 기능을 활용합니다.

**Azure AD B2B 공동 작업**
- SaaS 앱에 대한 로그온이 Azure AD를 기반으로 하는 경우 [Azure AD B2B 공동 작업](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)을 사용하여 파트너에게 클라우드 리소스에 대한 액세스 권한을 부여할 수 있습니다.

**더 쉬운 관리 환경 및 Azure AD의 추가 기능**
- SaaS 앱에 대한 ID 공급자인 Azure AD는 응용 프로그램당 토큰 서명 인증서, [구성 가능한 인증서 만료 날짜](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs), 키 갤러리 앱의 사용자 계정 [자동 프로비전](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning)과 같은 추가 기능을 지원합니다.

**온-프레미스 ID 공급자의 혜택 유지**
- Azure AD 혜택을 확보하는 동안 인증에 온-프레미스 솔루션을 계속 사용할 수 있으므로 온-프레미스 MFA(Multi-Factor Authentication) 솔루션, 로깅 및 감사와 같은 혜택이 그대로 유지됩니다. 

**온-프레미스 ID 공급자를 사용 중지하는 경우 활성화**
- 온-프레미스 인증 제품을 사용 중지하려는 조직의 경우 앱을 Azure AD로 마이그레이션하면 일부 작업을 수행함으로써 더 쉽게 전환할 수 있습니다 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>온-프레미스의 앱 유형을 Azure AD의 앱 유형에 매핑
대부분의 앱은 사용하는 로그온 유형에 따라 몇 가지 범주 중 하나에 해당합니다.  이러한 범주는 Azure AD에서 앱이 표시되는 방법을 결정합니다.

즉, SAML 2.0 응용 프로그램은 Azure AD 응용 프로그램 갤러리를 통하거나 비갤러리 응용 프로그램으로 Azure AD와 통합할 수 있습니다.  OAuth 2.0 또는 OpenID Connect를 사용하는 앱은 "앱 등록"과 마찬가지로 Azure AD와 통합할 수 있습니다.  자세한 내용을 보려면 계속 읽어보세요.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>페더레이션된 SaaS 앱 및 사용자 지정 LoB 앱
페더레이션된 앱에는 나열된 범주에 속하는 앱이 포함됩니다.

- SaaS 앱 
    - 사용자가 SaaS 앱(예: Salesforce, ServiceNow 또는 Workday)에 로그온하고 온-프레미스 ID 공급자(예: AD FS 또는 Ping)에 통합되는 경우 SaaS 앱에 대해 페더레이션 로그온을 사용하게 됩니다.
    - 앱은 일반적으로 페더레이션 로그온에 대해 SAML 2.0 프로토콜을 사용합니다.
    - 이 범주에 속하는 응용 프로그램은 갤러리에서 엔터프라이즈 응용 프로그램 또는 비갤러리 응용 프로그램으로 Azure AD와 통합할 수 있습니다.
- 사용자 지정 LoB 응용 프로그램
    - 이는 조직에서 내부적으로 개발한 비SaaS 앱을 나타내거나 데이터 센터에 설치된 표준 패키지 제품으로 사용할 수 있습니다.  여기에는 SharePoint 앱 및 WIF(Windows Identity Foundation)에서 작성된 앱이 포함됩니다.
    - 앱에서는 페더레이션 로그온에 SAML 2.0, WS-Federation, OAuth 또는 OpenID Connect를 사용할 수 있습니다.
    - Oauth 2.0, OpenID Connect 또는 WS-Federation을 사용하는 사용자 지정 앱은 Azure AD와 App 등록으로 통합할 수 있으며, SAML 2.0 또는 WS-Federation을 사용하는 사용자 지정 앱은 엔터프라이즈 응용 프로그램 내에서 비갤러리 응용 프로그램으로 통합할 수 있습니다.

### <a name="non-federated-apps"></a>페더레이션되지 않은 앱
페더레이션되지 않은 앱도 Azure AD 응용 프로그램 프록시 및 관련 기능을 사용하여 Azure AD와 통합할 수 있습니다.  이 기능에 대한 자세한 내용은 다음 링크를 참조하세요.
- Active Directory에 WIA(Windows 통합 인증)를 직접 사용하는 앱
    - 이러한 앱은 [Azure AD 응용 프로그램 프록시](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal)를 통해 Azure AD에 통합할 수 있습니다.
- 에이전트를 통해 Single Sign-On 공급자와 통합하고 권한 부여에 대한 헤더를 사용하는 앱
    - 로그온 및 헤더 기반 권한 부여를 위해 설치된 에이전트를 사용하는 온-프레미스 앱은 [Azure AD에 대한 PingAccess](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/)가 있는 Azure AD 응용 프로그램 프록시를 사용하여 Azure AD 기반 로그온에 대해 구성할 수 있습니다.

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>페더레이션된 온-프레미스 앱을 Azure AD로 변환 
다행히도 AD FS와 Azure AD는 비슷하게 작동하므로 두 경우 모두에 신뢰, 로그온 및 로그아웃 URL 및 식별자를 구성하는 개념이 적용됩니다.  그러나 전환할 때 알고 있어야 하는 몇 가지 작은 차이점이 있습니다.

다음 표에는 AD FS, Azure AD 및 SaaS 앱에서 공유하여 변환하는 데 유용한 몇 가지 주요 아이디어가 매핑되어 있습니다. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Azure AD 또는 AD FS의 앱 표현
마이그레이션은 온-프레미스에서 응용 프로그램을 구성하는 방법을 평가하고 해당 구성을 Azure AD에 매핑하는 것으로 시작됩니다.  AD FS 신뢰 당사자 구성 요소와 이에 해당하는 Azure AD 요소는 다음과 같이 매핑됩니다.  
- AD FS 용어: 신뢰 당사자 또는 신뢰 당사자 트러스트
- Azure AD 용어: 엔터프라이즈 응용 프로그램 또는 앱 등록(앱 유형에 따라 다름)

|앱 구성 요소|설명|AD FS 구성 내|Azure AD 구성의 해당 위치|SAML 토큰 요소|
|-----|-----|-----|-----|-----|
|앱 로그온 URL|이 응용 프로그램의 로그온 페이지 URL이며, 사용자가 SP에서 시작된 SAML 흐름에서 앱 로그인을 시작하는 위치입니다.|해당 없음|Azure AD에서 로그온 URL은 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에서 로그온 URL로 구성됩니다.</br></br>(로그온 URL을 보려면 [고급 URL 설정 표시]를 클릭해야 할 수도 있습니다.)||
|앱 회신 URL|IdP의 관점에서 본 앱의 URL이며,  IDP에서 사용자가 서명한 후에 사용자와 토큰을 보내는 위치입니다.</br></br>  때로는 SAML 어설션 소비자 엔드포인트라고도 합니다.|앱에 대한 AD FS 신뢰 당사자 트러스트에 있습니다.  신뢰 당사자를 마우스 오른쪽 단추로 클릭하고, "속성" -> "엔드포인트" 탭을 차례로 선택합니다.|Azure AD에서 회신 URL은 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에서 회신 URL로 구성됩니다.</br></br>(회신 URL을 보려면 [고급 URL 설정 표시]를 클릭해야 할 수도 있습니다.)|SAML 토큰의 Destination 요소에 매핑됩니다.</br></br>  예제 값: https://contoso.my.salesforce.com|
|앱 로그아웃 URL|사용자가 앱에서 로그아웃할 때 "로그아웃 정리" 요청을 보내는 URL이며, IDP에서 사용자에게 서명한 다른 모든 앱에서 로그아웃합니다.|AD FS 관리의 신뢰 당사자 트러스트에 있습니다.  RP를 마우스 오른쪽 단추로 클릭하고 "속성" -> "엔드포인트" 탭을 차례로 클릭합니다.|해당 없음 - Azure AD는 모든 앱에서 로그아웃한다는 의미인 "단일 로그아웃"을 지원하지 않습니다.  단순히 사용자를 Azure AD 자체에서 로그아웃하도록 지원할 뿐입니다.|해당 없음|
|앱 식별자|IdP의 관점에서 앱의 식별자 로그온 URL 값은 종종 식별자에 사용되지만 항상 그렇지는 않습니다.</br></br>  때로는 앱에서 이를 "엔터티 ID"라고 합니다.|AD FS에서 이는 신뢰 당사자 ID입니다. 신뢰 당사자 트러스트를 마우스 오른쪽 단추로 클릭하고 "속성" -> "식별자" 탭을 차례로 클릭합니다.|Azure AD에서 식별자는 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에 있는 [도메인 및 URL] 아래에서 식별자로 구성됩니다("고급 URL 설정 표시" 확인란을 클릭해야 할 수도 있음).|SAML 토큰의 Audience 요소에 해당합니다.|
|앱 페더레이션 메타데이터|앱의 페더레이션 메타데이터 위치입니다.  IdP에서 엔드포인트 또는 암호화 인증서와 같은 특정 구성 설정을 자동 업데이트하는 데 사용합니다.|앱의 페더레이션 메타데이터 URL은 앱의 AD FS 신뢰 당사자 트러스트에 있습니다.  트러스트를 마우스 오른쪽 단추로 클릭하고, [속성]을 선택한 다음, [모니터링] 탭을 클릭합니다.|해당 없음 - Azure AD는 응용 프로그램 페더레이션 메타데이터를 직접 사용하도록 지원하지 않습니다.|해당 없음|
|사용자 식별자/NameID|Azure AD 또는 AD FS의 사용자 ID를 앱에 고유하게 표시하는 데 사용되는 특성입니다.</br></br>  일반적으로 사용자의 UPN 또는 이메일 주소입니다.|AD FS에서 이는 신뢰 당사자에 대한 클레임 규칙으로 발견됩니다.  대부분의 경우 "nameidentifier"로 끝나는 유형의 클레임을 발급하는 클레임 규칙입니다.|Azure AD에서 사용자 식별자는 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에 있는 [사용자 특성] 헤더 아래에 있습니다.</br></br>UPN이 기본적으로 사용됩니다.|SAML 토큰의 "NameID" 요소로 IDP에서 앱으로 전달됩니다.|
|앱으로 보내는 다른 클레임|사용자 식별자/NameID 외에도 일반적으로 다른 클레임 정보(예: 이름, 성, 이메일 주소 및 사용자가 속한 그룹)를 IDP에서 앱으로 보냅니다.|AD FS에서 이는 신뢰 당사자에 대한 다른 클레임 규칙으로 발견됩니다.|Azure AD에서 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에 있는 [사용자 특성] 헤더 아래에서 [기타 모든 사용자 특성 보기 및 편집]을 클릭합니다.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>SaaS 앱에서 Azure AD를 IdP(ID 공급자)로 표현
마이그레이션의 일부로 앱은 Azure AD(온-프레미스 ID 공급자와 비교)를 가리키도록 구성되어야 합니다.  이 섹션에서는 주로 SAML 프로토콜을 사용하고 사용자 지정/LOB 앱이 아닌 SaaS 앱에 대해 설명합니다. 그러나 설명된 개념은 사용자 지정/LOB 앱으로 확장됩니다. 

상위 수준에서 SaaS 앱을 Azure AD로 가리키도록 하는 몇 가지 중요한 사항이 있습니다.
- ID 공급자 발급자: https&#58;//sts.windows.net/{tenant-id}/
- ID 공급자 로그인 URL: https&#58;//login.microsoftonline.com/{tenant-id}/saml2
- ID 공급자 로그아웃 URL: https&#58;//login.microsoftonline.com/{tenant-id}/saml2 
- 페더레이션 메타데이터 위치: https&#58;//login.windows.net/{tenant-id} <tenant-id>/federationmetadata/2007-06/federationmetadata.xml?appid={<application-id} 

여기서 {tenant-id}는 Azure Portal의 Azure Active Directory -> 속성 아래에서 "디렉터리 ID"로 있는 테넌트 ID로 바꾸고, {application-id}는 응용 프로그램의 [속성] 아래에서 "응용 프로그램 ID"로 있는 응용 프로그램 ID로 바꿉니다.

다음 표에서는 앱의 SSO 설정과 AD FS 및 Azure AD 내의 해당 값 또는 위치를 구성하는 주요 IdP 구성 요소를 자세히 설명합니다.  이 표의 참조 프레임은 SaaS 앱이며, 인증 요청을 보내는 위치와 받은 토큰의 유효성을 검사하는 방법을 알아야 합니다.

|구성 요소|설명|AD FS|Azure AD|
|---|---|---|---|
|IdP </br>로그온 </br>URL|앱의 관점에서 본 IdP의 로그온 URL(사용자가 로그인을 위해 리디렉션되는 위치)입니다.|AD FS 로그온 URL은 "/adfs/ls/" 뒤에 오는 AD FS 페더레이션 서비스 이름입니다(예: https&#58;//fs.contoso.com/adfs/ls/).|Azure AD의 해당 값은 다음 패턴을 따릅니다. 여기서 {tenant-id}는 Azure Portal의 Azure Active Directory -> 속성 아래에서 "디렉터리 ID"로 있는 테넌트 ID로 바꿉니다.</br></br>SAML-P 프로토콜을 사용하는 앱의 경우: https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2 </br></br>WS-Federation 프로토콜을 사용하는 앱의 경우 https&#58;//login.microsoftonline.com</br>/{tenant-id}/wsfed|
|IdP </br>로그아웃 </br>URL|앱의 관점에서 IdP의 로그아웃 URL(사용자가 앱에서 로그아웃하도록 선택할 때 리디렉션되는 위치)입니다.|AD FS의 경우 로그아웃 URL은 로그온 URL과 동일하거나 "wa = wsignout1.0"이 추가된 URL(예: https&#58;//fs.contoso.com/adfs/ls /?wa=wsignout1.0)과 동일합니다.|Azure AD의 해당 값은 앱에서 SAML 2.0 로그아웃을 지원할 수 있는지 여부에 따라 다릅니다.</br></br>앱에서 SAML 로그아웃을 지원하는 경우 다음 패턴을 따릅니다. 여기서 {tenant-id} 값은 Azure Portal의 Azure Active Directory -> 속성 아래에서 "디렉터리 ID"로 있는 테넌트 ID로 바꿉니다. https&#58;//login.microsoftonline.com</br>/{tenant-id}/saml2</br></br>앱에서 SAML 로그아웃을 지원하지 않는 경우: https&#58;//login.microsoftonline.com</br>/common /wsfederation?wa=wsignout1.0|
|신뢰 </br>서명 </br>인증서|IDP에서 발급한 토큰에 서명하는 데 사용하는 개인 키가 있는 인증서입니다.  앱이 신뢰하도록 구성된 동일한 IDP에서 토큰이 제공되었는지 확인합니다.|AD FS 토큰 서명 인증서는 AD FS 관리의 [인증서] 아래에 있습니다.|Azure AD에서 토큰 서명 인증서는 Azure Portal 내의 응용 프로그램 Single Sign-On 속성에 있는 [SAML 서명 인증서] 헤더 아래에 있습니다. 여기서는 앱에 업로드할 인증서를 다운로드할 수 있습니다.</br></br>  응용 프로그램에 둘 이상의 인증서가 있는 경우 모든 인증서는 페더레이션 메타데이터 xml 파일에 있습니다.|
|식별자/ </br>"발급자"|앱의 관점에서 본 IdP의 식별자(때로는 "발급자" 또는 "발급자 ID"라고도 함)입니다.</br></br>SAML 토큰에서 값은 "Issuer" 요소로 표시됩니다.|AD FS에 대한 식별자는 일반적으로 AD FS 관리의 서비스 -> 페더레이션 서비스 속성 편집 아래에 있는 페더레이션 서비스 식별자입니다.  예: http&#58;//fs.contoso.com/adfs/services/trust|Azure AD의 해당 값은 다음 패턴을 따릅니다. 여기서 {tenant-id} 값은 Azure Portal의 Azure Active Directory -> 속성 아래에서 "디렉터리 ID"로 있는 테넌트 ID로 바꿉니다.  https&#58;//sts.windows.net/{tenant-id}/|
|IdP </br>페더레이션 </br>Metadata|IDP의 공개적으로 사용할 수 있는 페더레이션 메타데이터의 위치입니다.  (관리자가 URL, 식별자 및 토큰 서명 인증서를 개별적으로 구성하는 대신 일부 앱에서 페더레이션 메타데이터를 사용합니다)|AD FS 페더레이션 메타데이터 URL은 AD FS 관리의 서비스 -> 엔드포인트 -> 메타데이터 -> 형식: 페더레이션 메타데이터 아래에 있습니다. 예: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Azure AD의 해당 값은 다음 패턴을 따릅니다. https&#58;//login.microsoftonline.com</br>/{TenantDomainName}/FederationMetadata/2007-06/</br>FederationMetadata.xml. 여기서 {TenantDomainName} 값은 "contoso.onmicrosoft.com" 형식의 테넌트 이름으로 바꿉니다. </br></br>Azure AD의 페더레이션 메타데이터에 대한 [추가 정보](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="migrating-saas-apps"></a>SaaS 앱 마이그레이션
현재 SaaS 앱은 수동으로 AD FS 또는 다른 ID 공급자에서 Azure AD로 마이그레이션합니다. 앱별 지침은 [갤러리에 있는 SaaS 앱 통합에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)을 참조하세요.

통합 자습서에서는 녹색 필드 통합을 수행한다고 가정합니다.  마이그레이션과 관련하여 앱을 계획, 평가, 구성 및 다시 만들 때 알아야 하는 몇 가지 주요 개념이 있습니다.  
- 일부 앱은 쉽게 마이그레이션할 수 있지만, 사용자 지정 클레임과 같이 더 복잡한 요구 사항이 있는 앱의 경우 Azure AD 및/또는 Azure AD Connect에서 추가 구성이 필요할 수 있습니다
- 가장 일반적인 시나리오에서 앱에는 NameId 클레임 및 일반적인 다른 사용자 식별자 클레임만 필요합니다. 추가 클레임이 필요한지 확인하려면 AD FS 또는 타사 ID 공급자에서 발급한 클레임을 검사합니다.
- 추가 클레임이 필요하다고 판단되면 Azure AD에서 사용할 수 있는지 확인해야 합니다.  Azure AD Connect 동기화 구성을 확인하여 필요한 특성(예: samAccountName)이 Azure AD와 동기화되는지 확인해야 합니다
- Azure AD에서 특성을 사용할 수 있게 되면 Azure AD에 클레임 발급 규칙을 추가하여 발급된 토큰에 이러한 특성이 클레임으로 포함되도록 합니다.  Azure AD에 있는 앱의 Single Sign-On 속성 내에서 수행됩니다.

### <a name="assessing-what-can-be-migrated"></a>마이그레이션할 수 있는 항목 평가
SAML 2.0 응용 프로그램은 Azure AD 응용 프로그램 갤러리를 통하거나 비갤러리 응용 프로그램으로 Azure AD와 통합할 수 있습니다.  

Azure AD에서 구성하기 위해 추가 단계가 필요한 일부 구성과 현재 지원되지 않는 구성이 있습니다.  이동할 수 있는 구성을 결정하려면 각 앱의 현재 구성, 특히 다음 구성을 확인합니다.
- 구성된 클레임 규칙(발급 변환 규칙)
- SAML NameID 형식 및 특성
- 발급된 SAML 토큰 버전
- 기타 구성(예; 발급 권한 부여 규칙 또는 액세스 제어 정책 및 다단계 인증(추가 인증) 규칙)

#### <a name="what-can-be-migrated-today"></a>현재 마이그레이션할 수 있는 항목
현재 쉽게 마이그레이션할 수 있는 앱에는 표준 구성 요소 및 클레임 집합을 사용하는 SAML 2.0 앱이 포함됩니다.  이러한 앱은 다음으로 구성될 수 있습니다.
- 사용자 이름
- 이메일 주소
- 이름
- Surname
- Azure AD 메일 특성, 메일 접두사, employeeid, 확장자 특성(1-15) 또는 온-프레미스 SamAccountName을 포함하여 SAML NameID로 대체되는 특성([NameIdentifier 클레임 편집](./develop/active-directory-saml-claims-customization.md) 참조).
- 사용자 지정 클레임(지원되는 클레임 매핑에 대한 내용은 [여기](active-directory-claims-mapping.md) 및 [여기](./develop/active-directory-saml-claims-customization.md) 문서 참조)

사용자 지정 클레임 및 nameID 요소 외에도, Azure AD에서 마이그레이션의 일부로 추가 구성 단계가 필요한 구성은 다음과 같습니다.
- AD FS에서 사용자 지정 권한 부여 또는 MFA 규칙을 사용자 지정합니다([Azure AD 조건부 액세스](active-directory-conditional-access-azure-portal.md) 기능을 사용하여 구성됨).
- 여러 SAML 엔드포인트가 있는 앱은 PowerShell을 사용하여 Azure AD에서 구성할 수 있습니다(이 기능은 포털에서 사용할 수 없음)
- SAML 1.1 버전 토큰이 필요한 SharePoint 앱과 같은 WS-Federation 앱은 PowerShell을 사용하여 수동으로 구성해야 합니다.

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>현재 Azure AD에서 지원되지 않는 앱/구성
다음 기능이 필요한 앱은 현재 마이그레이션할 수 없습니다.  이러한 기능이 필요한 앱이 있으면 의견 섹션에 피드백을 제공해 주세요.
- 프로토콜 기능
    - 로그인한 모든 앱의 SAML SLO(단일 로그아웃) 지원
    - WS-Trust ActAs 패턴 지원
    - SAML 아티팩트 확인 
    - 서명된 SAML 요청의 서명 확인(서명된 요청은 수락되지만 해당 서명이 확인되지 않음)
 - 토큰 기능 
     - SAML 토큰 암호화 
     - SAML 프로토콜 응답 내의 SAML 버전 1.1 토큰 
- 토큰 기능의 클레임
    - 온-프레미스 그룹 이름을 클레임으로 발급
    - Azure AD 이외의 저장소에서 클레임
    - 복잡한 클레임 발급 변환 규칙(지원되는 클레임 매핑에 대한 내용은 이 문서 및 이 문서 참조)
    - 디렉터리 확장을 클레임으로 발급
    - NameID 형식의 사양 사용자 지정
    - 다중 값 특성의 발급

>[!NOTE]
>Azure AD는 이 영역에 추가 기능을 추가하기 위해 계속 진화하고 있습니다. 이 문서는 정기적으로 업데이트됩니다. 

### <a name="configuring-azure-ad"></a>Azure AD 구성    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>SaaS 앱에 대한 SSO(Single Sign-On) 설정 구성

Azure AD에서 앱의 요구에 따라 다음과 같이 앱의 Single Sign-On 속성에 있는 [사용자 특성] 아래에서 SAML 로그온을 구성합니다.

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- '기타 모든 사용자 특성 보기 및 편집'을 클릭하여 보안 토큰에 클레임으로 보낼 특성을 봅니다.

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- 편집할 특정 특성 행을 클릭하거나 새 특성에 대한 '특성 추가'를 클릭합니다. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>앱에 사용자 할당
Azure AD내의 사용자가 지정된 SaaS 앱에 로그인하려면 Azure AD에서 액세스 권한을 부여받아야 합니다.

Azure AD 포털에서 사용자를 할당하려면 포털에서 SaaS 앱의 화면으로 이동하고 세로 막대에서 "사용자 및 그룹"을 클릭합니다. 사용자 또는 그룹을 추가하려면 화면 위쪽의 "사용자 추가"를 클릭합니다. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png) 액세스를 확인하려면 사용자가 로그인할 때 [액세스 패널](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)에서 http://myapps.microsoft.com에 있는 SaaS 앱이 표시되어야 합니다. 예를 들어 Salesforce와 ServiceNow 모두에 대한 액세스가 사용자에게 성공적으로 할당되었습니다.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>SaaS 앱 구성
온-프레미스 페더레이션에서 Azure AD로의 다시 만들기 프로세스는 사용하는 SaaS 앱에서 여러 IdP(ID 공급자)를 지원하는지 여부에 따라 달라집니다.  여러 IdP 지원과 관련된 몇 가지 일반적인 질문은 다음과 같습니다.

   **Q: 앱에서 여러 IdP를 지원한다는 것은 무엇을 의미할까요?**
    
   A: 여러 IdP를 지원하는 SaaS 앱을 사용하면 로그온 환경을 변경하기 전에 새 IdP(여기서는 Azure AD)에 대한 모든 정보를 입력할 수 있습니다.  구성이 완료되면 Azure AD를 가리키도록 앱의 인증 구성을 전환할 수 있습니다.

   Q: SaaS 앱에서 여러 IdP를 지원하는지 여부가 중요한 이유는 무엇인가요?

   A: 여러 IdP가 지원되지 않는 경우, 관리자는 Azure AD를 앱의 새 IdP로 구성하는 동안 서비스 또는 유지 관리 중단으로 짧은 시간 범위를 별도로 설정해야 합니다. 이러한 중단의 경우 사용자는 자신의 계정에 로그인할 수 없다는 알림을 받게 됩니다.

   앱에서 여러 IdP를 지원하는 경우, 추가 IdP의 구성을 미리 수행할 수 있으므로 관리자는 Azure 다시 만들기에서 IdP를 간단히 전환할 수 있습니다.

   또한 앱에서 여러 IdP를 지원하고 로그인에 대한 인증을 동시에 처리하기 위해 여러 IdP를 선택하는 경우, 사용자는 로그인 페이지에서 인증할 IdP를 선택할 수 있습니다.

#### <a name="example-multiple-idp-support"></a>예: 여러 IdP 지원
예를 들어 Salesforce에서 IDP 구성은 설정 -> 회사 설정 -> 내 도메인 -> 인증 구성 아래에 있습니다.

![](media/migrate-adfs-apps-to-azure/migrate9.png)

이전에 ID -> Single Sign-On 설정 아래에서 만든 구성으로 인해 인증 구성에 대한 IdP를 AD FS에서 Azure AD로 변경할 수 있습니다. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>선택 사항: Azure AD에서 사용자 프로비전 구성
필요에 따라 Azure AD에서 지정된 SaaS 앱에 대한 사용자 프로비전을 직접 처리하려면, Azure AD에서 엔터프라이즈 앱에 대한 사용자 계정 프로비전 관리에 대한 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Active Directory로 응용 프로그램 관리](active-directory-enable-sso-scenario.md)
- [앱에 대한 액세스 관리](active-directory-managing-access-to-apps.md)
- [Azure AD Connect 페더레이션](active-directory-aadconnectfed-whatis.md)