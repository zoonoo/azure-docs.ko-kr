---
title: "Azure ACS(Access Control Service)에서 마이그레이션"
description: "Azure Access Control Service에서 앱 및 서비스를 이전하기 위한 옵션 | Microsoft Azure"
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Azure ACS(Access Control Service)에서 마이그레이션

Microsoft Azure Active Directory Access Control(Access Control Service 또는 ACS)이 2018년 11월에 사용 중지됩니다.  현재 ACS를 사용하는 응용 프로그램과 서비스는 2018년 11월 전까지 다른 인증 메커니즘으로 마이그레이션이 완료되어야 합니다. 이 문서에서는 ACS의 사용 중지를 계획 중인 기존 고객에게 몇 가지 권장 사항을 안내합니다. 현재 ACS를 사용하고 있지 않다면 아무런 조치를 취할 필요가 없습니다.


## <a name="brief-acs-overview"></a>ACS의 간략한 개요

ACS는 사용자가 웹 응용 프로그램 및 서비스에 액세스하도록 인증하고 권한을 부여하는 간편한 방법을 제공하는 클라우드 인증 서비스입니다. 이때 코드에서 여러 인증 및 권한 부여 기능을 고려하지 않을 수 있게 됩니다. ACS는 .NET 클라이언트, ASP.NET 웹 응용 프로그램 및 WCF 웹 서비스의 개발자와 설계자가 주로 사용합니다.

ACS의 용도는 다음과 같은 세 가지 범주로 나누어집니다.

- Azure Service Bus, Dynamics CRM을 비롯한 특정 Microsoft 클라우드 서비스를 인증합니다. 클라이언트 응용 프로그램은 ACS로부터 획득한 토큰으로 이러한 서비스를 인증하고 다양한 작업을 수행합니다.
- 사용자 지정 빌드된 웹 응용 프로그램과 (SharePoint와 같이) 미리 패키징된 웹 응용 프로그램에 인증을 추가합니다. 웹 응용 프로그램은 ACS의 “수동” 인증을 사용하여 Google, Facebook, Yahoo, Microsoft 계정(Live ID), Azure Active Directory 및 ADFS의 계정을 사용한 로그인을 지원할 수 있습니다.
- ACS가 발행하는 토큰으로 사용자 지정 빌드된 웹 서비스를 보호합니다. 웹 서비스는 “능동” 인증을 사용하여 ACS에서 인증한 클라이언트의 액세스만 허용할 수 있습니다.

이어지는 섹션에서는 이러한 용도와 각각의 권장 마이그레이션 전략을 설명합니다. 대부분의 경우 기존 앱과 서비스를 신기술로 마이그레이션할 때는 코드를 대폭 수정해야 합니다. 시스템 중단이나 가동 중지 시간을 방지하기 위해서는 가능한 한 빨리 마이그레이션을 계획하고 이행하는 것이 좋습니다.

> [!WARNING]
> 대부분의 경우 기존 앱과 서비스를 신기술로 마이그레이션할 때는 코드를 대폭 수정해야 합니다. 시스템 중단이나 가동 중지 시간을 방지하기 위해서는 가능한 한 빨리 마이그레이션을 계획하고 이행하는 것이 좋습니다.

아키텍처 측면에서 볼 때 ACS는 다음과 같은 구성 요소로 이루어집니다.

- 보안 토큰 서비스(STS): 인증 요청을 받고 요청에 대한 응답으로서 보안 토큰을 발행합니다.
- 클래식 Azure Portal: ACS 네임스페이스의 생성, 삭제, 활성화/비활성화에 사용됩니다.
- 별도의 ACS 관리 포털: ACS 네임스페이스의 동작을 사용자 지정하고 구성하는 데 사용합니다.
- 관리 서비스: 포털의 기능을 자동화하는 데 사용합니다.
- 토큰 변환 규칙 엔진: ACS에서 발행한 토큰의 출력 형식을 조작하는 복잡한 로직을 정의하는 데 사용합니다.

이러한 구성 요소를 사용하려면 하나 이상의 ACS **네임스페이스**를 만들어야 합니다. 네임스페이스는 응용 프로그램 및 서비스와 상호 통신하는 전용 ACS 인스턴스입니다. 네임스페이스는 각각 자체 네임스페이스를 사용하는 다른 모든 ACS 고객으로부터 격리됩니다. ACS의 네임스페이스에는 아래와 같은 전용 URL이 있습니다.

```HTTP
https://mynamespace.accesscontrol.windows.net
```

이 URL에서 STS 및 관리 작업과의 통신이 이루어지며, 용도별로 서로 다른 경로를 갖습니다. 응용 프로그램 또는 서비스에서 ACS를 사용하는지 확인하려면 `https://{namespace}.accesscontrol.windows.net`으로 전달되는 트래픽이 있는지 모니터링합니다.  이 URL로 전달되는 트래픽은 모두 ACS에서 처리하는 것으로, 사용 중지 계획을 세워야 합니다.  단, `https://accounts.accesscontrol.windows.net`으로 전달되는 트래픽은 예외입니다. 이 URL로 전달되는 트래픽은 다른 서비스에 의해 처리되기 때문에 ACS 사용 중지의 영향을 받지 않습니다.  클래식 Azure Portal에 로그인하여 보유 중인 구독에 ACS 네임스페이스가 있는지도 확인해야 합니다.  ACS 네임스페이스는 **Access Control 네임스페이스** 탭 아래의 **Active Directory** 서비스에서 확인할 수 있습니다.

ACS에 대한 자세한 내용은 [MSDN의 아카이브 문서](https://msdn.microsoft.com/library/hh147631.aspx)를 참조하세요.

## <a name="retirement-schedule"></a>사용 중지 일정

2017년 11월 현재, 모든 ACS 구성 요소가 지원되고 정상 작동됩니다. 단, [클래식 Azure Portal을 통한 신규 ACS 네임스페이스의 생성은 지원되지 않습니다](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

해당 구성 요소의 사용 중지 일정은 다음과 같습니다.

- **2017년 11월**: 클래식 Azure Portal의 Azure AD 관리자 환경이 [사용 중지됩니다](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). 이 시점부터 새로운 전용 URL `http://manage.windowsazure.com?restoreClassic=true`에서 ACS 네임스페이스를 관리할 수 있습니다. 여기서 기존 네임스페이스를 보거나 활성화/비활성화하거나 원하는 경우 완전히 삭제할 수 있습니다.
- **2018년 4월**: 위에 명시한 전용 URL에서 더 이상 ACS 네임스페이스를 관리할 수 없습니다. 이 시점부터는 ACS 네임스페이스를 활성화/비활성화하거나 삭제하거나 열거할 수 없습니다. 단, `https://{namespace}.accesscontrol.windows.net`의 ACS 관리 포털은 정상적으로 작동합니다. ACS의 다른 모든 구성 요소도 계속해서 정상적으로 작동합니다.
- **2018년 11월**: 모든 ACS 구성 요소가 영구적으로 종료됩니다. 즉, ACS 관리 포털, 관리 서비스, STS, 토큰 변환 규칙 엔진이 종료됩니다. 이 시점부터 (`{namespace}.accesscontrol.windows.net`에 위치한) ACS로 전송되는 요청은 처리되지 않습니다. 이 시점 전까지 기존 앱과 서비스를 다른 기술로 모두 마이그레이션 완료해야 합니다.


## <a name="migration-strategies"></a>마이그레이션 전략

이어지는 섹션에서는 ACS에서 다른 Microsoft 기술로 마이그레이션하기 위한 대략적인 권장 사항을 안내합니다.

### <a name="clients-of-microsoft-cloud-services"></a>Microsoft 클라우드 서비스의 클라이언트

ACS에서 발행하는 토큰을 이용하는 각 Microsoft 클라우드 서비스는 현재 하나 이상의 대체 인증 수단을 지원합니다. 서비스별로 인증 메커니즘이 다르므로 각 서비스의 공식 지침 문서를 확인하시기 바랍니다. 참고를 위해 아래에서 각 서비스의 설명서를 안내하고 있습니다.

| 서비스 | 지침 |
| ------- | -------- |
| Azure Service Bus | [공유 액세스 서명으로 마이그레이션](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Azure Relay | [공유 액세스 서명으로 마이그레이션](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Azure Cache | [Azure Redis Cache로 마이그레이션](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data Market | [Cognitive Services API로 마이그레이션](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| BizTalk Services | [Azure Logic Apps로 마이그레이션](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Azure Media Services | [Azure AD 인증으로 마이그레이션](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Azure Backup 에이전트 업그레이드](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>수동 인증을 사용하는 웹 응용 프로그램

사용자 인증을 위해 ACS를 사용하는 웹 응용 프로그램의 경우, 웹 응용 프로그램 개발자 및 설계자를 위해 ACS는 다음과 같은 특징과 기능을 제공했습니다.

- WIF(Windows Identity Foundation)와의 심층적인 통합.
- Google, Facebook, Yahoo, Microsoft 계정(Live ID), Azure Active Directory 및 ADFS와의 페더레이션.
- OAuth 2.0 초안 13, Ws-Trust, Ws-Federation 인증 프로토콜 지원.
- JWT(JSON Web Token), SAML 1.1, SAML 2.0, SWT(단순 웹 토큰) 지원.
- 사용자가 로그인할 계정 유형을 직접 선택할 수 있는, WIF에 통합된 홈 영역 검색 환경. 이 환경은 웹 응용 프로그램에 의해 호스팅되었으며 원하는 대로 사용자 지정이 가능했습니다.
- 웹 응용 프로그램이 ACS로부터 수신한 클레임을 다음과 같이 사용자 지정할 수 있는 토큰 변환.
    - ID 공급자의 클레임 전달
    - 사용자 지정 클레임 추가
    - 특정 조건이 충족되면 클레임을 발행하는 단순한 if-then 로직

안타깝지만 위의 모든 기능을 제공하는 단일한 서비스는 없습니다.  ACS의 기능 중 어느 기능이 필요한지 검토한 다음 [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/)(Azure AD), [**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/) 또는 그 밖의 클라우드 인증 서비스 중 어느 것을 사용할지 선택해야 합니다.

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory로 마이그레이션

앱과 서비스를 Azure Active Directory에 직접 통합하는 방법도 있습니다. Azure AD는 Microsoft 회사 및 학교 계정의 클라우드 기반 ID 공급자이자 Office 365, Azure를 비롯한 다양한 서비스의 ID 공급자입니다. Azure AD는 ACS와 비슷한 페더레이션 인증 기능을 제공하긴 하나, ACS의 모든 기능을 지원하지는 않습니다. 페더레이션은 주로 Facebook, Google, Yahoo와 같은 소셜 ID 공급자에 대해 실시됩니다. 사용자가 이러한 유형의 자격 증명을 이용하여 로그인하는 경우가 많다면 Azure AD는 적합하지 않습니다. Azure AD와 ACS가 인증 프로토콜을 지원하는 방식이 동일하지도 않습니다. 예를 들어, ACS와 Azure AD 모두 OAuth를 지원하긴 하나, 각 구현 사이에 미세한 차이가 있기 때문에 마이그레이션할 때 코드를 수정해야 합니다.

한편 Azure AD는 ACS 고객에게 몇 가지 이점을 제공합니다. Azure AD는 클라우드 호스팅된 Microsoft 회사 및 학교 계정을 지원합니다. Microsoft 회사 및 학교 계정은 ACS 고객이 일반적으로 사용하는 계정이기도 합니다.  Azure AD 테넌트는 ADFS를 통해 온-프레미스 Active Directory의 하나 이상의 인스턴스와 페더레이션될 수 있습니다. 따라서 앱에서 클라우드 기반 사용자와 온-프레미스에 호스팅된 사용자를 모두 인증할 수 있습니다.  Ws-Federation 프로토콜도 지원하기 때문에 WIF(Windows Identity Foundation)를 사용하여 웹 응용 프로그램과 비교적 간단하게 통합할 수 있습니다.

다음 표에서는 웹 응용 프로그램과 관련 있는 ACS의 기능과 Azure AD의 기능을 비교하고 있습니다. 대략적으로 봤을 때 **사용자가 Microsoft 회사 및 학교 계정으로만 로그인하는 경우에는 Azure Active Directory를 선택하는 것이 좋습니다**.

| 기능 | ACS 지원 | Azure AD 지원 |
| ---------- | ----------- | ---------------- |
| **계정 유형** | | |
| Microsoft 회사 및 학교 계정 | 지원됨 | 지원됨 |
| Windows Server Active Directory 및 ADFS의 계정 | Azure AD 테넌트와의 페더레이션을 통해 지원 <br /> ADFS와의 직접 페더레이션을 통해 지원 | Azure AD 테넌트와의 페더레이션을 통해서만 지원 | 
| 다른 엔터프라이즈 ID 관리 시스템의 계정 | Azure AD 테넌트와의 페더레이션을 통해 지원 <br />직접 페더레이션을 통해 지원 | Azure AD 테넌트와의 페더레이션을 통해 지원 |
| 개인용 Microsoft 계정(Windows Live ID) | 지원됨 | Azure AD의 v2.0 OAuth 프로토콜을 통해 지원(다른 프로토콜은 지원 불가) | 
| Facebook, Google, Yahoo 계정 | 지원됨 | 지원되지 않음 |
| **프로토콜 및 SDK 호환성** | | |
| WIF(Windows Identity Foundation) | 지원됨 | 지원됨(제한된 명령만 사용 가능) |
| Ws-Federation | 지원됨 | 지원됨 |
| OAuth 2.0 | 초안 13 지원 | 최신 사양인 RFC 6749 지원 |
| Ws-Trust | 지원됨 | 지원되지 않음 |
| **토큰 형식** | | |
| JWT(JSON Web Token) | 베타에서 지원 | 지원됨 |
| SAML 1.1 토큰 | 지원됨 | 지원됨 |
| SAML 2.0 토큰 | 지원됨 | 지원됨 |
| 단순 웹 토큰(SWT) | 지원됨 | 지원되지 않음 |
| **사용자 정의** | | |
| 사용자 지정 가능한 홈 영역 검색/계정 선택 UI | 코드를 다운로드하여 앱에 통합 | 지원되지 않음 |
| 사용자 지정 토큰 서명 인증서 업로드 | 지원됨 | 지원됨 |
| 토큰의 클레임 사용자 지정 | ID 공급자의 입력 클레임 전달<br />ID 공급자의 액세스 토큰을 클레임으로서 가져오기<br />입력 클레임의 값을 바탕으로 출력 클레임 발행<br />상수 값을 사용하여 출력 클레임 발행 | 페더레이션된 ID 공급자의 클레임 전달 불가<br />ID 공급자의 액세스 토큰을 클레임으로서 가져오기 불가<br />입력 클레임의 값을 바탕으로 출력 클레임 발행 불가<br />상수 값을 사용하여 출력 클레임 발행 가능<br />Azure AD에 동기화된 사용자의 속성을 바탕으로 출력 클레임 발행 가능 |
| **Automation** | | |
| 구성/관리 작업 자동화 | ACS 관리 서비스를 통해 지원 | Microsoft Graph 및 Azure AD Graph API를 통해 지원 |

응용 프로그램과 서비스에 Azure AD가 적합하다고 판단한 경우, 앱에 Azure AD를 통합하는 두 가지 방법을 모두 알고 있어야 합니다.

Ws-Federation/WIF를 사용하여 Azure AD와의 통합을 수행할 때는 [이 문서에서 설명하는 방법](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)을 사용하는 것이 좋습니다. 문서에서는 SAML 기반 Single Sign-On을 위해 Azure AD를 구성하는 방법을 설명하고 있지만, Ws-Federation을 구성할 때도 마찬가지 방법을 사용할 수 있습니다. 이 방법을 사용하려면 Azure AD Premium 라이선스가 필요합니다. 이 방법에는 다음과 같은 두 가지 이점이 있습니다.

- Azure AD 토큰 사용자 지정의 유연성이 제공됩니다. Azure AD에서 발행한 클레임(특히 사용자 ID 클레임이나 이름 식별자 클레임)을 ACS에서 발행한 클레임과 매칭되도록 사용자 지정할 수 있습니다. ACS에서 다른 기술로 마이그레이션한 다음에도 사용자들에 대해 전과 동일하게 사용자 식별자를 받기 위해서는 Azure AD에서 발행한 사용자 ID가 ACS에서 발생한 사용자 ID와 일치해야 합니다.
- 사용자가 직접 수명을 제어하는 응용 프로그램에 맞게 토큰 서명 인증서를 구성할 수 있습니다.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> 이 방법을 사용하려면 Azure AD Premium 라이선스가 필요합니다. 응용 프로그램에 Single Sign-On을 설정하기 위해 Premium 라이선스가 필요한 ACS 고객이 Microsoft에 문의해 주시면 개발자 라이선스를 제공해 드리겠습니다.

또 다른 방법은 [이 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation)을 따르는 것입니다. 코드 샘플에서는 Ws-Federation을 설정하는 다른 방법을 설명합니다. 코드 샘플에서는 WIF가 아닌 ASP.NET 4.5 OWIN 미들웨어를 사용하지만 WIF를 사용하는 앱에서도 유효한 앱 등록 방법을 사용할 수 있으며, 이때 Azure AD Premium 라이선스가 필요하지 않습니다.  이 방법을 선택한 경우에는 [Azure AD의 서명 키 롤오버](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover)에 대해 이해해야 합니다. 이 방법에서는 Azure AD 글로벌 서명 키를 사용하여 토큰을 발행합니다. 기본적으로 WIF는 자동으로 서명 키를 업데이트하지 않습니다. Azure AD가 글로벌 서명 키를 교대시킬 때 변화된 사항이 적용될 수 있도록 WIF 구현이 준비가 되어 있어야 합니다.

OpenID Connect 또는 OAuth 프로토콜을 통해 Azure AD와 통합할 수 있다면 그렇게 하는 것이 좋습니다.  [Azure AD 개발자 가이드](http://aka.ms/aaddev)에서 웹 응용 프로그램에 Azure AD를 통합하는 방법에 대한 방대한 설명서와 자료를 확인할 수 있습니다.

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Azure AD B2C로 마이그레이션

또 다른 마이그레이션 방법으로 Azure AD B2C가 있습니다.  B2C는 ACS와 마찬가지로 개발자들이 인증 및 ID 관리 로직을 클라우드 서비스로 아웃소싱할 수 있도록 해 주는 클라우드 인증 서비스입니다.  B2C는 엄청나게 많은 활성 사용자를 갖는 소비자용 응용 프로그램을 위해 설계된 유료 서비스로, 무료 등급과 프리미엄 등급이 제공됩니다.

B2C는 ACS와 마찬가지로 다양한 계정 유형을 지원합니다. B2C를 이용하면 Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo를 비롯한 다양한 계정을 갖는 사용자가 로그인할 수 있습니다. B2C는 이에 더해 사용자가 특정 응용 프로그램을 위해 만드는 사용자 이름과 암호, 즉 “로컬 계정”을 지원합니다. Azure AD B2C는 로그인 흐름을 사용자 지정할 수 있도록 확장성도 제공합니다. 단, ACS 고객이 필요로 할 수 있는 다양한 인증 프로토콜과 토큰 형식은 지원하지 않습니다. ID 공급자, Microsoft 등으로부터 토큰을 받고 사용자에 관한 추가 정보를 쿼리하는 데도 사용할 수 없습니다.

다음 표에서는 웹 응용 프로그램과 관련 있는 ACS의 기능과 Azure AD B2C의 기능을 비교하고 있습니다. 대략적으로 봤을 때 **응용 프로그램이 소비자용이거나 다양한 계정 유형을 지원하는 경우 Azure AD B2C를 선택하는 것이 좋습니다.**

| 기능 | ACS 지원 | Azure AD B2C 지원 |
| ---------- | ----------- | ---------------- |
| **계정 유형** | | |
| Microsoft 회사 및 학교 계정 | 지원됨 | 사용자 지정 정책을 통한 지원  |
| Windows Server Active Directory 및 ADFS의 계정 | ADFS와의 직접 페더레이션을 통해 지원 | 사용자 지정 정책을 사용하여 SAML 페더레이션을 통해 지원 |
| 다른 엔터프라이즈 ID 관리 시스템의 계정 | Ws-Federation을 바탕으로 직접 페더레이션을 통해 지원 | 사용자 지정 정책을 사용하여 SAML 페더레이션을 통해 지원 |
| 개인용 Microsoft 계정(Windows Live ID) | 지원됨 | 지원됨 | 
| Facebook, Google, Yahoo 계정 | 지원됨 | Facebook 및 Google은 기본적으로 지원, Yahoo는 사용자 지정 정책을 사용하여 OpenID Connect 페더레이션을 통해 지원 |
| **프로토콜 및 SDK 호환성** | | |
| WIF(Windows Identity Foundation) | 지원됨 | 지원되지 않습니다. |
| Ws-Federation | 지원됨 | 지원되지 않습니다. |
| OAuth 2.0 | 초안 13 지원 | 최신 사양인 RFC 6749 지원 |
| Ws-Trust | 지원됨 | 지원되지 않습니다. |
| **토큰 형식** | | |
| JWT(JSON Web Token) | 베타에서 지원 | 지원됨 |
| SAML 1.1 토큰 | 지원됨 | 지원되지 않음 |
| SAML 2.0 토큰 | 지원됨 | 지원되지 않음 |
| 단순 웹 토큰(SWT) | 지원됨 | 지원되지 않음 |
| **사용자 정의** | | |
| 사용자 지정 가능한 홈 영역 검색/계정 선택 UI | 코드를 다운로드하여 앱에 통합 | 사용자 지정 CSS를 이용하여 UI 사용자 지정 가능 |
| 사용자 지정 토큰 서명 인증서 업로드 | 지원됨 | 사용자 정책을 통해 사용자 지정 서명 키 지원(인증서는 지원 불가) |
| 토큰의 클레임 사용자 지정 | ID 공급자의 입력 클레임 전달<br />ID 공급자의 액세스 토큰을 클레임으로서 가져오기<br />입력 클레임의 값을 바탕으로 출력 클레임 발행<br />상수 값을 사용하여 출력 클레임 발행 | ID 공급자의 클레임 전달 가능. 일부 클레임의 경우 사용자 지정 정책 필요<br />ID 공급자의 액세스 토큰을 클레임으로서 가져오기 불가<br />사용자 지정 정책을 통해 입력 클레임의 값을 바탕으로 출력 클레임 발행 가능<br />사용자 지정 정책을 통해 상수 값을 바탕으로 출력 클레임 발행 가능 |
| **Automation** | | |
| 구성/관리 작업 자동화 | ACS 관리 서비스를 통해 지원 | Azure AD Graph API를 통한 사용자 생성 허용. B2C 테넌트, 응용 프로그램 또는 정책을 프로그래밍 방식으로 생성 불가 |

응용 프로그램과 서비스에 Azure AD B2C가 적합하다고 판단한 경우, 다음 리소스를 살펴보세요.

- [Azure AD B2C 설명서](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Azure AD B2C 사용자 지정 정책](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Azure AD B2C 가격](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>그 밖의 마이그레이션 옵션

Azure AD와 Azure AD B2C 모두 웹 응용 프로그램의 요구 사항을 충족하지 않는다면 Microsoft와 마이그레이션 방법을 상의하시기 바랍니다.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>능동 인증을 사용하는 웹 서비스

ACS에서 발행하는 토큰으로 보안되는 웹 서비스의 경우, ACS는 다음과 같은 특징과 기능을 제공했습니다.

- ACS 네임스페이스에 토큰 요청을 위해 사용할 수 있는 하나 이상의 **서비스 ID** 등록.
- 다음과 같은 자격 증명을 사용하여 토큰 요청 시 OAuth WRAP 및 OAuth 2.0 초안 13 프로토콜 지원.
    - 서비스 ID를 위해 만든 단순한 암호
    - 대칭 키 또는 X509 인증서를 사용하는 서명된 SWT
    - 신뢰할 수 있는 ID 공급자(주로 ADFS 인스턴스)가 발행한 SAML 토큰
- JWT(JSON Web Token), SAML 1.1, SAML 2.0, SWT(단순 웹 토큰) 지원.
- 단순 토큰 변환 규칙

ACS의 서비스 ID는 일반적으로 인증과 같은 서버-투-서버(S2S) 기능을 구현하는 데 사용됩니다.  

#### <a name="migrating-to-azure-active-directory"></a>Azure Active Directory로 마이그레이션

이러한 인증 흐름에 대해 Microsoft에서 권장하는 방법은 [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/)(Azure AD)로 마이그레이션하는 것입니다. Azure AD는 Microsoft 회사 및 학교 계정의 클라우드 기반 ID 공급자이자 Office 365, Azure를 비롯한 다양한 서비스의 ID 공급자입니다. Azure AD는 OAuth 클라이언트 자격 증명 부여에 대한 Azure AD의 구현을 사용하여 서버-투-서버 인증에도 사용할 수 있습니다.  다음 표에서는 서버-투-서버 인증과 관련 있는 ACS의 기능과 Azure AD의 기능을 비교하고 있습니다.

| 기능 | ACS 지원 | Azure AD 지원 |
| ---------- | ----------- | ---------------- |
| 웹 서비스를 등록하는 방법 | ACS 관리 포털에서 신뢰 당사자 만들기 | Azure Portal에서 Azure AD 웹 응용 프로그램 만들기 |
| 클라이언트를 등록하는 방법 | ACS 관리 포털에서 서비스 ID 만들기 | Azure Portal에서 또 다른 Azure AD 웹 응용 프로그램 만들기 |
| 사용되는 프로토콜 | OAuth WRAP 프로토콜<br />OAuth 2.0 초안 13 클라이언트 자격 증명 부여 | OAuth 2.0 클라이언트 자격 증명 부여 |
| 클라이언트 인증 방법 | 단순한 암호<br />서명된 SWT<br />페더레이션된 IDP의 SAML 토큰 | 단순한 암호<br />서명된 JWT |
| 토큰 형식 | JWT<br />SAML 1.1<br />SAML 2.0<br />SWT<br /> | JWT만 |
| 토큰 변환 | 사용자 지정 클레임 추가<br />단순한 if-then 클레임 발행 로직 | 사용자 지정 클레임 추가 | 
| 구성/관리 작업 자동화 | ACS 관리 서비스를 통해 지원 | Microsoft Graph 및 Azure AD Graph API를 통해 지원 |

서버-투-서버 시나리오를 구현하는 방법은 아래의 리소스를 참조하세요.

- [Azure AD 개발자 가이드의 서비스-투-서비스 섹션](https://aka.ms/aaddev).
- [단순한 암호 클라이언트 자격 증명을 사용하는 디먼 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [인증서 클라이언트 자격 증명을 사용하는 디먼 코드 샘플](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>그 밖의 마이그레이션 옵션

Azure AD가 웹 서비스의 요구 사항을 충족하지 않는다면 Microsoft가 최적의 마이그레이션 방법을 안내해 드릴 수 있도록 댓글을 남겨 주세요.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>질문, 우려 사항 및 피드백

이 문서를 모두 읽은 다음에도 명확한 마이그레이션 방법을 결정하지 못하여 최적의 전략을 결정하는 데 도움이 필요한 ACS 고객이 계실 것이라 생각합니다. 마이그레이션 시나리오를 상의하고 궁금한 점을 물어보려면 이 페이지에 댓글을 남겨 주세요.
