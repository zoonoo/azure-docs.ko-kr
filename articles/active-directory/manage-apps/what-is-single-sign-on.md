---
title: 애플리케이션에 대한 Single Sign-On - Azure Active Directory | Microsoft Docs
description: Azure AD(Azure Active Directory)에서 애플리케이션을 구성하는 경우 Single Sign-On 방법을 선택하는 방법에 대해 알아봅니다. Single Sign-On을 사용하므로 사용자는 모든 애플리케이션에 대한 암호를 기억할 필요가 없어 계정 관리가 간소화됩니다.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 452ec35f9a759bd09befb8c5a03a7fc5b3fb8ed2
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54463357"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory의 애플리케이션에 대한 Single Sign-On
Azure AD(Azure Active Directory)에서 애플리케이션을 구성하는 경우 가장 적합한 Single Sign-On 방법을 선택하는 방법에 대해 알아봅니다. 

- **Single Sign-On을 사용하면** 사용자는 하나의 계정으로 한 번 로그인으로 도메인 가입 디바이스, 회사 리소스, SaaS(Software as a Service) 애플리케이션 및 웹 애플리케이션에 액세스할 수 있습니다. 로그인한 후 사용자는 Office 365 포털 또는 Azure AD MyApps 액세스 패널에서 애플리케이션을 시작할 수 있습니다. 관리자는 사용자 계정 관리를 중앙 집중화하고, 그룹 멤버 자격에 따라 애플리케이션에 대한 사용자 액세스를 자동으로 추가하거나 제거할 수 있습니다. 

- **Single Sign-On을 사용하지 않으면** 사용자는 애플리케이션별 암호를 기억하고 각 애플리케이션마다 로그인해야 합니다. IT 직원은 Office 365, Box 및 Salesforce와 같은 각 애플리케이션에 대한 사용자 계정을 만들고 업데이트해야 합니다. 사용자가 암호를 기억해야 하는 것과 동시에 각 애플리케이션에 로그인하는 시간도 소비됩니다.

이 문서는 Single Sign-On 방법을 설명하고 사용자 애플리케이션에 가장 적합한 방법을 선택하는 데 유용합니다.

## <a name="choosing-a-single-sign-on-method"></a>Single Sign-On 방법 선택

Single Sign-On을 위해 애플리케이션을 구성하는 방법은 여러 가지가 있습니다. 애플리케이션에 대해 Single Sign-On 방법을 선택하는 것은 애플리케이션이 인증에 대해 어떻게 구성되었는지에 따라 달라집니다. Single Sign-On 방법은 모두(사용하지 않도록 설정된 경우 제외) 자동으로 애플리케이션에 사용자를 로그인하여 두 번째 로그온이 필요하지 않습니다.  

- 클라우드 애플리케이션은 Single Sign-On에 대해 SAML, 암호 기반, 연결됨 또는 사용 안 함 방법을 사용할 수 있습니다. SAML은 가장 안전한 Single Sign-On 방법입니다.
- 온-프레미스 애플리케이션은 Single Sign-On에 대해 암호 기반, Windows 통합 인증, 헤더 기반, 연결됨 또는 사용 안 함 방법을 사용할 수 있습니다. 온-프레미스 선택은 애플리케이션 프록시에 대해 애플리케이션을 구성할 때 작동합니다. 

이 순서도는 사용자 상황에 가장 적합한 Single Sign-On 방법을 결정하는 데 도움이 됩니다. 

![Single Sign-On 방법 선택](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

다음 표에는 Single Sign-On 방법이 요약되어 있으며 더 자세한 정보로 이어집니다. 

| Single Sign-On 방법 | 애플리케이션 형식 | 사용하는 경우 |
| :------ | :------- | :----- |
| [SAML](#saml-sso) | 클라우드 전용 | 가능하면 SAML을 사용합니다. SAML은 앱이 SAML 프로토콜 중 하나를 사용하도록 구성된 경우 작동합니다.|
| [암호 기반](#password-based-sso) | 클라우드 및 온-프레미스 | 애플리케이션이 사용자 이름 및 암호를 사용하여 인증하는 경우 사용합니다. 암호 기반 Single Sign-On을 사용하면 웹 브라우저 확장 또는 모바일 앱을 사용하여 안전하게 애플리케이션 암호를 저장하고 재생할 수 있습니다. 이 방법은 애플리케이션에서 제공하는 기존 로그인 프로세스를 사용하지만, 관리자가 암호를 관리할 수 있습니다. |
| [연결됨](#linked-sso) | 클라우드 및 온-프레미스 | 애플리케이션이 다른 ID 공급자 서비스에서 Single Sign-On에 대해 구성된 경우 연결된 Single Sign-On을 사용합니다. 이 옵션은 애플리케이션에 Single Sign-On을 추가하지 않습니다. 하지만 애플리케이션에 이미 Active Directory Federation Services와 같은 다른 서비스를 사용하여 Single Sign-On이 구현되어 있을 수도 있습니다.|
| [사용 안 함](#disabled-sso) | 클라우드 및 온-프레미스 | 앱을 Single Sign-On에 대해 구성할 준비가 되지 않은 경우 사용 안 함 Single Sign-On을 사용합니다. 사용자는 이 애플리케이션을 시작할 때마다 사용자 이름 및 암호를 입력해야 합니다.|
| [IWA(Windows 통합 인증)](#integrated-windows-authentication-iwa-sso) | 온-프레미스만 | 이 Single Sign-On 방법은 [IWA(Windows 통합 인증)](/aspnet/web-api/overview/security/integrated-windows-authentication)를 사용하는 애플리케이션 또는 클레임 인식 애플리케이션에 사용합니다. 애플리케이션 프록시 커넥터는 애플리케이션에 사용자를 인증하는 데 KCD(Kerberos 제한 위임)를 사용합니다. | 
| [헤더 기반](#header-based-sso) | 온-프레미스만 | 애플리케이션이 인증에 헤더를 사용하는 경우 헤더 기반 Single Sign-On을 사용합니다. 헤더 기반 Single Sign-On에는 Azure Active Directory용 PingAccess가 필요합니다. 애플리케이션 프록시는 Azure AD를 사용하여 사용자를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달합니다.  | 

## <a name="saml-sso"></a>SAML SSO
**SAML Single Sign-On**을 사용하는 Azure AD는 사용자의 Azure AD 계정을 사용하여 애플리케이션에 인증합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. SAML 기반 Single Sign-On을 사용하면 SAML 클레임에서 정의하는 규칙에 따라 특정 애플리케이션 역할에 사용자를 매핑할 수 있습니다.

SAML 기반 Single Sign-On은

- 암호 기반 Single Sign-On 및 다른 모든 로그온 방법보다 더 안전합니다.
- Single Sign-On에 대해 Microsoft에서 권장하는 방법입니다.

SAML 기반 Single Sign-On은 다음과 같은 프로토콜을 사용하는 애플리케이션에 대해 지원됩니다.

- SAML 2.0
- WS-Federation

애플리케이션을 SAML 기반 Single Sign-On에 대해 구성하려면 [SAML 기반 Single Sign-On 구성](configure-single-sign-on-portal.md)을 참조하세요. 또한 여러 애플리케이션에는 특정 애플리케이션을 위한 SAML 기반 Single Sign-On 구성 단계를 안내하는 [애플리케이션별 자습서](../saas-apps/tutorial-list.md)가 있습니다. 

SAML 프로토콜의 작동 방식에 대한 자세한 정보는 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md)을 참조하세요.

## <a name="password-based-sso"></a>암호 기반 SSO
암호 기반 로그온을 사용하는 경우 최종 사용자는 애플리케이션에 처음 액세스할 때 사용자 이름 및 암호를 사용하여 애플리케이션에 로그인합니다. 첫 번째 로그온 후에는 Azure Active Directory가 사용자 이름 및 암호를 애플리케이션에 제공합니다. 

암호 기반 Single Sign-On은 애플리케이션에서 제공한 기존 인증 프로세스를 사용합니다. 애플리케이션에서 암호 Single Sign-On을 사용하도록 설정하면 Azure AD는 애플리케이션에 대한 사용자 이름 및 암호를 수집하고 안전하게 저장합니다. 사용자 자격 증명은 암호화된 상태로 디렉터리에 저장됩니다. 

다음과 같은 경우에 암호 기반 Single Sign-On을 사용합니다.

- 애플리케이션이 SAML Single Sign-On 프로토콜을 지원할 수 없습니다.
- 애플리케이션이 액세스 토큰 및 헤더 대신 사용자 이름과 암호를 사용하여 인증합니다.

HTML 기반 로그인 페이지가 있는 클라우드 기반 애플리케이션에 암호 기반 Single Sign-On이 지원됩니다. 사용자는 다음 브라우저를 사용할 수 있습니다.

- Windows 7 이상의 Internet Explorer 11
- Windows 10 Anniversary Edition 이상의 Microsoft Edge 
- Windows 7 이상 및 Mac OS X 이상 Chrome
- Windows XP SP2 이상 및 Mac OS X 10.6 이상 Firefox 26.0 이상

암호 기반 Single Sign-On에 대해 클라우드 애플리케이션을 구성하려면 [암호 Single Sign-On에 대한 애플리케이션 구성](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on)을 참조하세요.

애플리케이션 프록시를 통해 Single Sign-On에 대한 온-프레미스 애플리케이션을 구성하려면 [애플리케이션 프록시를 사용하여 Single Sign-On에 대한 암호 자격 증명 모음 설정](application-proxy-configure-single-sign-on-password-vaulting.md)을 참조하세요

### <a name="managing-credentials-for-password-based-sso"></a>암호 기반 SSO에 대한 자격 증명 관리

애플리케이션에 사용자를 인증하기 위해 Azure AD는 디렉터리에서 사용자의 자격 증명을 검색하여 애플리케이션의 로그인 페이지에 입력합니다.  Azure AD는 웹 브라우저 확장 또는 모바일 앱을 통해 사용자 자격 증명을 안전하게 전달합니다. 이 프로세스에서는 사용자 자격 증명을 관리자가 관리할 수 있으며, 사용자가 자신의 암호를 기억할 필요가 없습니다.

> [!IMPORTANT]
> 자동화된 로그인 프로세스 도중 최종 사용자에 의해 자격 증명이 난독 처리됩니다. 그러나 자격 증명은 웹 디버깅 도구를 사용하여 검색할 수 있습니다. 사용자 및 관리자는 사용자가 직접 입력한 자격 증명과 동일한 보안 정책을 따라야 합니다.

각 애플리케이션에 대한 암호는 Azure AD 관리자 또는 사용자가 관리할 수 있습니다.

Azure AD 관리자가 자격 증명을 관리하는 경우:  

- 사용자는 다시 설정하거나 사용자 이름 및 암호를 기억할 필요가 없습니다. 사용자는 액세스 패널에서 클릭하거나 제공된 링크를 통해 애플리케이션에 액세스할 수 있습니다.
- 관리자는 자격 증명에서 관리 작업을 수행할 수 있습니다. 예를 들어, 관리자는 사용자 그룹 멤버 자격 및 직원 상태에 따라 애플리케이션 액세스를 업데이트할 수 있습니다.
- 관리자는 관리 자격 증명을 사용하여 많은 사용자들이 공유하는 애플리케이션에 대한 액세스를 제공할 수 있습니다. 예를 들어 관리자는 애플리케이션에 액세스할 수 있는 모든 사용자가 소셜 미디어나 문서 공유 애플리케이션에 액세스할 수 있도록 허용할 수 있습니다.

최종 사용자가 자격 증명을 관리하는 경우:

- 사용자는 필요에 따라 해당 암호를 업데이트 또는 삭제하여 관리할 수 있습니다. 
- 관리자는 계속 애플리케이션에 대한 새 자격 증명을 설정할 수 있습니다.


## <a name="linked-sso"></a>연결된 SSO
연결된 로그온을 통해 Azure AD는 이미 다른 서비스에서 Single Sign-On에 대해 구성된 애플리케이션에 Single Sign-On을 제공할 수 있습니다. 연결된 애플리케이션은 Office 365 포털 또는 Azure AD MyApps 포털에서 최종 사용자에게 표시될 수 있습니다. 예를 들어, 사용자는 AD FS(Active Directory Federation Services) 2.0의 Single Sign-On에 대해 구성된 애플리케이션을 Office 365 포털에서 시작할 수 있습니다. 추가적인 보고도 Office 365 포털 또는 Azure AD MyApps 포털에서 실행되는 연결된 애플리케이션에 제공됩니다. 

연결된 Single Sign-On을 사용하면 다음을 수행할 수 있습니다.

- 일정 기간 동안 애플리케이션을 마이그레이션하면서 일관된 사용자 경험을 제공합니다. 애플리케이션을 Azure Active Directory로 마이그레이션하는 경우 연결된 Single Sign-On을 사용하여 마이그레이션하려는 모든 애플리케이션에 대한 링크를 신속하게 게시할 수 있습니다.  사용자는 [MyApps 포털](../user-help/active-directory-saas-access-panel-introduction.md) 또는 [Office 365 애플리케이션 시작 관리자](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a)에서 모든 링크를 찾을 수 있습니다. 사용자는 연결된 애플리케이션 또는 마이그레이션된 애플리케이션에 액세스하는 것을 알지 못합니다.  

사용자가 연결된 애플리케이션을 사용하여 인증하고 나면, 최종 사용자에게 Single Sign-On 액세스를 제공하기 전에 먼저 계정 레코드를 만들어야 합니다. 이 계정 레코드의 프로비저닝은 자동으로 발생하거나 관리자에 의해 수동으로 발생할 수 있습니다.

## <a name="disabled-sso"></a>사용 안 함 SSO

사용 안 함 모드란 Single Sign-On이 애플리케이션에 사용되지 않았음을 의미합니다. Single Sign-On이 사용하지 않도록 설정되면 사용자는 두 번 인증해야 할 수도 있습니다. 먼저 사용자는 Azure AD에 인증한 다음, 애플리케이션에 로그인합니다. 

다음과 같은 경우 사용 안 함 Single Sign-On 모드를 사용합니다.

- Azure AD Single Sign-On을 사용하여 이 애플리케이션을 통합할 준비가 되지 않은 경우 또는
- 애플리케이션의 다른 측면을 테스트하는 경우 또는
- 사용자가 인증할 필요가 없는 온-프레미스 애플리케이션에 대한 보안 계층입니다. 사용 안 함을 사용하는 사용자는 인증해야 합니다. 

## <a name="integrated-windows-authentication-iwa-sso"></a>IWA(Windows 통합 인증) SSO

Azure AD 애플리케이션 프록시는 [IWA(Windows 통합 인증)](/aspnet/web-api/overview/security/integrated-windows-authentication) 또는 클레임 인식 애플리케이션을 사용하는 애플리케이션에 SSO(Single Sign-On)를 제공합니다. 애플리케이션에서 IWA를 사용하는 경우 애플리케이션 프록시는 KCD(Kerberos 제한 위임)를 사용하여 애플리케이션에 인증합니다. Azure Active Directory를 신뢰하는 클레임 인식 애플리케이션의 경우 사용자가 이미 Azure AD를 사용하여 인증되었으므로 Single Sign-On이 작동합니다.

다음과 같은 경우 Windows 통합 인증 Single Sign-On 모드를 사용합니다.

- IWA를 사용하여 인증하는 온-프레미스 앱에 Single Sign-On을 제공하려는 경우 

IWA에 대해 온-프레미스 앱을 구성하려면 [애플리케이션 프록시를 사용하여 애플리케이션에 Single Sign-On에 대한 Kerberos 제한 위임](application-proxy-configure-single-sign-on-with-kcd.md)을 참조하세요. 

### <a name="how-single-sign-on-with-kcd-works"></a>KCD를 사용하는 Single Sign-On 작동 방식
이 다이어그램은 IWA를 사용하는 온-프레미스 애플리케이션에 사용자가 액세스할 때 흐름을 설명합니다.

![Microsoft AAD 인증 흐름 다이어그램](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. 사용자가 애플리케이션 프록시를 통해 온-프레미스 애플리케이션에 액세스하기 위한 URL을 입력합니다.
2. 애플리케이션 프록시는 사전 인증을 위해 Azure AD 인증 서비스에 요청을 리디렉션합니다. 이 시점에서 Azure AD는 다단계 인증 등, 모든 적용 가능한 인증 및 권한 부여 정책을 적용합니다. 사용자가 확인되면 Azure AD에서 토큰을 만들어서 사용자에게 보냅니다.
3. 사용자는 토큰을 애플리케이션 프록시로 전달합니다.
4. 애플리케이션 프록시는 토큰의 유효성을 검사하고 토큰에서 UPN(사용자 주체 이름)을 검색합니다. 그런 다음, 이중으로 인증된 보안 채널을 통해 커넥터에 요청, UPN 및 SPN(서비스 주체 이름)을 보냅니다.
5. 커넥터는 온-프레미스 AD와 함께 KCD(Kerberos 제한 위임) 협상을 사용하여 사용자를 가장하여 Kerberos 토큰을 애플리케이션에 가져옵니다.
6. Active Directory는 애플리케이션에 대한 Kerberos 토큰을 커넥터로 보냅니다.
7. 커넥터는 AD에서 받은 Kerberos 토큰을 사용하여 원래 요청을 애플리케이션 서버에 보냅니다.
8. 애플리케이션은 응답을 커넥터로 보냅니다. 그러면 해당 응답이 애플리케이션 프록시 서비스를 거쳐 마지막으로 사용자에게 반환됩니다.

## <a name="header-based-sso"></a>헤더 기반 SSO

헤더 기반 Single Sign-On은 인증에 HTTP 헤더를 사용하는 애플리케이션에 작동합니다. 이 로그온 방법은 PingAccess라고 하는 타사 인증 서비스를 사용합니다. 사용자만 Azure AD에 인증해야 합니다. 

다음과 같은 경우 헤더 기반 Single Sign-On을 사용합니다.

- 애플리케이션 프록시 및 PingAccess가 애플리케이션에 대해 구성된 경우

헤더 기반 인증을 구성하려면 [애플리케이션 프록시를 사용하여 Single Sign-On에 대한 헤더 기반 인증](application-proxy-configure-single-sign-on-with-ping-access.md)을 참조하세요. 

### <a name="what-is-pingaccess-for-azure-ad"></a>Azure AD용 PingAccess는 무엇입니까?

Azure AD에 PingAccess를 사용하면 사용자는 인증에 헤더를 사용하는 애플리케이션에 액세스 및 Single Sign-On을 사용할 수 있습니다. 애플리케이션 프록시는 이러한 애플리케이션을 Azure AD를 사용하여 액세스를 인증한 다음, 커넥터 서비스를 통해 트래픽을 전달하는 방식으로 다른 앱과 유사하게 처리합니다. 인증이 발생한 후 PingAccess 서비스는 Azure AD 액세스 토큰을 애플리케이션에 전송되는 헤더 형식으로 변환합니다.

사용자는 회사 애플리케이션을 사용하기 위해 로그인할 때 다른 점을 알아차리지 못합니다. 여전히 어디에서든지 모든 디바이스에서 작업할 수 있습니다. 애플리케이션 프록시 커넥터는 모든 애플리케이션에 원격 트래픽을 보내므로 계속해서 부하 분산을 자동으로 수행합니다.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>PingAccess에 대한 라이선스를 가져오려면 어떻게 해야 하나요?

이 시나리오는 Azure Active Directory 및 PingAccess 간의 파트너 관계를 통해 제공되므로 두 서비스에 대한 라이선스가 필요합니다. 그러나 Azure Active Directory Premium 구독에는 최대 20개의 애플리케이션을 보장하는 기본 PingAccess 라이선스가 포함되어 있습니다. 헤더 기반 애플리케이션을 20개 넘게 게시해야 하는 경우 PingAccess에서 라이선스를 추가로 구입할 수 있습니다. 

자세한 내용은 [Azure Active Directory 버전](../fundamentals/active-directory-whatis.md)을 참조하세요.

## <a name="related-articles"></a>관련 문서
* [SaaS 애플리케이션과 Azure Active Directory 통합을 위한 자습서](../saas-apps/tutorial-list.md)
* [Single Sign-On 구성을 위한](configure-single-sign-on-portal.md)
* [애플리케이션에 대한 액세스 관리 소개](what-is-access-management.md)
* 다운로드 링크: [Single Sign-On 배포 계획](https://aka.ms/SSODeploymentPlan)


