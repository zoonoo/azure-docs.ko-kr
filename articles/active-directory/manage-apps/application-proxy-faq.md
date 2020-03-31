---
title: Azure AD 응용 프로그램 프록시자주 묻는 질문 | 마이크로 소프트 문서
description: Azure AD 응용 프로그램 프록시를 사용하여 원격 사용자에게 내부 온-프레미스 응용 프로그램을 게시하는 방법에 대해 자주 묻는 FAQ(질문)에 대한 답변을 알아봅니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 8bb77a217546f1abe2c28925e32464b8d61702b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481282"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>활성 디렉터리(Azure AD) 응용 프로그램 프록시자주 묻는 질문

이 페이지에서는 Azure Active Directory(Azure AD) 응용 프로그램 프록시에 대해 자주 묻는 질문에 대한 답변을 제공합니다.

## <a name="enabling-azure-ad-application-proxy"></a>Azure AD 애플리케이션 프록시 사용

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 사용하려면 어떤 라이선스가 필요합니까?

Azure AD 응용 프로그램 프록시를 사용하려면 Azure AD 프리미엄 P1 또는 P2 라이선스가 있어야 합니다. 라이선스에 대한 자세한 내용은 [Azure Active Directory 가격 책정을](https://azure.microsoft.com/pricing/details/active-directory/) 참조하십시오.

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>"응용 프로그램 프록시 활성화 단추가 회색으로 표시되는 이유는 무엇입니까?

최소한 Azure AD 프리미엄 P1 또는 P2 라이선스와 Azure AD 응용 프로그램 프록시 커넥터가 설치되어 있는지 확인합니다. 첫 번째 커넥터를 성공적으로 설치하면 Azure AD 응용 프로그램 프록시 서비스가 자동으로 활성화됩니다.

## <a name="connector-configuration"></a>커넥터 구성

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>응용 프로그램 프록시 커넥터 서비스가 기본값과 다른 사용자 컨텍스트에서 실행될 수 있습니까?

아니요, 이 시나리오는 지원되지 않습니다. 기본 설정은 다음과 같습니다.

- 마이크로 소프트 AAD 응용 프로그램 프록시 커넥터 - WAPCSvc - 네트워크 서비스
- 마이크로 소프트 AAD 응용 프로그램 프록시 커넥터 업데이터 - WAPCUpdaterSvc - NT 기관 \시스템

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>백 엔드 응용 프로그램은 여러 웹 서버에서 호스팅되며 사용자 세션 지속성(끈기)이 필요합니다. 세션 지속성을 달성하면 어떻게 해야 합니까? 

권장 사항은 [응용 프로그램 프록시 커넥터 및 응용 프로그램의 고가용성 및 부하 분산을](application-proxy-high-availability-load-balancing.md)참조하십시오.

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>커넥터 서버와 백 엔드 응용 프로그램 서버 사이에 정방향 프록시 장치를 배치할 수 있습니까?

아니요, 이 시나리오는 지원되지 않습니다. Azure로의 아웃바운드 트래픽에 대한 전달 프록시를 사용하도록 커넥터 및 업데이트 서비스만 구성할 수 있습니다. [기존 온-프레미스 프록시 서버 작업](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers) 보기

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>커넥터 서버에서 Azure로의 트래픽에 대한 TLS 종료(TLS/HTTPS 검사 또는 가속)가 지원되고 있습니까?

응용 프로그램 프록시 커넥터는 Azure에 대한 인증서 기반 인증을 수행합니다. TLS 종료(TLS/HTTPS 검사 또는 가속)는 이 인증 방법을 중단하며 지원되지 않습니다. 커넥터에서 Azure로의 트래픽은 TLS 종료를 수행하는 모든 장치를 우회해야 합니다.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시에 커넥터를 등록하기 위해 전용 계정을 만들어야 합니까?

그럴 이유가 없습니다. 모든 글로벌 관리자 또는 응용 프로그램 관리자 계정이 작동합니다. 설치 중에 입력한 자격 증명은 등록 프로세스 후에 사용되지 않습니다. 대신 해당 시점부터 인증에 사용되는 커넥터에 인증서가 발급됩니다.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Azure AD 응용 프로그램 프록시 커넥터의 성능을 모니터링하는 방법은 무엇입니까?

커넥터와 함께 설치된 성능 모니터 카운터가 있습니다. 이 내용을 확인하려면 다음을 수행하십시오.  

1. **시작,**"Perfmon"을 입력하고 ENTER를 누릅니다.
2. 성능 모니터를 선택하고 **+** 녹색 아이콘을 **클릭합니다.**
3. 모니터링할 **Microsoft AAD 응용 프로그램 프록시 커넥터** 카운터를 추가합니다.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Azure AD 응용 프로그램 프록시 커넥터가 리소스와 동일한 서브넷에 있어야 합니까?

커넥터가 동일한 서브넷에 있을 필요는 없습니다. 그러나 리소스및 필요한 네트워크 연결(리소스로 라우팅, 리소스에서 열리는 포트 등)에 대한 이름 확인(DNS, 호스트 파일)이 필요합니다. 권장 사항은 [Azure Active Directory 응용 프로그램 프록시를 사용할 때 네트워크 토폴로지 고려 사항을](application-proxy-network-topology.md)참조하십시오.

## <a name="application-configuration"></a>애플리케이션 구성

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>기본값과 "긴" 백 엔드 시간 설정의 길이는 얼마입니까? 시간 시간을 연장할 수 있습니까?

기본 길이는 85초입니다. "긴" 설정은 180초입니다. 시간 제한은 연장할 수 없습니다.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>응용 프로그램이 로드하는 방문 페이지를 변경하려면 어떻게 해야 합니까?

응용 프로그램 등록 페이지에서 홈페이지 URL을 방문 페이지의 원하는 외부 URL로 변경할 수 있습니다. 내 앱 또는 Office 365 포털에서 응용 프로그램을 시작할 때 지정된 페이지가 로드됩니다. 구성 단계는 [Azure AD 응용 프로그램 프록시를 사용하여 게시된 앱에 대한 사용자 지정 홈 페이지 설정을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page) 참조하세요.

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>IIS 기반 응용 프로그램만 게시할 수 있습니까? Windows 이외의 웹 서버에서 실행되는 웹 응용 프로그램은 어떻습니까? 커넥터를 IIS가 설치된 서버에 설치해야 합니까?

아니요, 게시된 응용 프로그램에 대한 IIS 요구 사항은 없습니다. Windows Server 가 아닌 다른 서버에서 실행되는 웹 응용 프로그램을 게시할 수 있습니다. 그러나 웹 서버가 협상(Kerberos 인증)을 지원하는지 여부에 따라 Windows 서버가 아닌 서버에서 사전 인증을 사용하지 못할 수 있습니다. 커넥터가 설치된 서버에서는 IIS가 필요하지 않습니다.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Kerberos 제한 위임 (KCD)을 설정할 때 언제 PrincipalsAllowedToDelegateToAccount 메서드를 사용해야 합니까?

PrincipalsAllowedToDelegateToAccount 방법은 커넥터 서버가 웹 응용 프로그램 서비스 계정과 다른 도메인에 있을 때 사용됩니다. 리소스 기반 제한 위임을 사용해야 합니다.
커넥터 서버와 웹 응용 프로그램 서비스 계정이 동일한 도메인에 있는 경우 Active Directory 사용자 및 컴퓨터를 사용하여 각 커넥터 컴퓨터 계정에 대한 위임 설정을 구성하여 대상 SPN에 위임할 수 있습니다.

커넥터 서버와 웹 응용 프로그램 서비스 계정이 서로 다른 도메인에 있는 경우 리소스 기반 위임이 사용됩니다. 위임 권한은 대상 웹 서버 및 웹 응용 프로그램 서비스 계정에서 구성됩니다. 구속된 위임의 이 방법은 비교적 새로운 것입니다. 이 메서드는 리소스(웹 서비스) 소유자가 해당 계정에 위임할 수 있는 컴퓨터 및 서비스 계정을 제어할 수 있도록 하여 도메인 간 위임을 지원하는 Windows Server 2012에서 도입되었습니다. 이 구성을 지원할 UI가 없으므로 PowerShell을 사용해야 합니다.
자세한 내용은 응용 프로그램 [프록시를 사용하여 Kerberos 제한된 위임 이해](https://aka.ms/kcdpaper)백서를 참조하십시오.

## <a name="pass-through-authentication"></a>통과 인증

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>통과 인증으로 게시된 응용 프로그램에 조건부 액세스 정책을 사용할 수 있습니까?

조건부 액세스 정책은 Azure AD에서 성공적으로 사전 인증된 사용자에 대해서만 적용됩니다. 통과 인증은 Azure AD 인증을 트리거하지 않으므로 조건부 액세스 정책을 적용할 수 없습니다. 통과 인증을 사용하는 경우 가능하면 온-프레미스 서버에서 MFA 정책을 구현하거나 Azure AD 응용 프로그램 프록시를 사용하여 사전 인증을 사용하도록 설정해야 합니다.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>클라이언트 인증서 인증 요구 사항이 있는 웹 응용 프로그램을 게시할 수 있습니까?

아니요, 응용 프로그램 프록시가 TLS 트래픽을 종료하기 때문에 이 시나리오는 지원되지 않습니다.  

## <a name="remote-desktop-gateway-publishing"></a>원격 데스크톱 게이트웨이 게시

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 통해 원격 데스크톱 게이트웨이를 게시하는 방법은 무엇입니까?

Azure [AD 응용 프로그램 프록시를 사용하여 원격 데스크톱 게시](application-proxy-integrate-with-remote-desktop-services.md)를 참조하십시오.

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>원격 데스크톱 게이트웨이 게시 시나리오에서 Kerberos 제한된 위임을 사용할 수 있습니까?

아니요, 이 시나리오는 지원되지 않습니다.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>사용자가 Internet Explorer 11을 사용하지 않으며 사전 인증 시나리오가 작동하지 않습니다. 이것은 예상된 동작인가요?

예, 예상됩니다. 사전 인증 시나리오에는 타사 브라우저에서 지원되지 않는 ActiveX 컨트롤이 필요합니다.

### <a name="is-the-remote-desktop-web-client-supported"></a>원격 데스크톱 웹 클라이언트가 지원되고 있습니까?

아니요, 이 시나리오는 현재 지원되지 않습니다. 이 기능에 대한 업데이트는 [UserVoice](https://aka.ms/aadapuservoice) 피드백 포럼을 따르십시오.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>사전 인증 시나리오를 구성한 후 사용자가 먼저 Azure AD 로그인 양식에서 인증한 다음 RDWeb 로그인 양식에서 두 번 인증해야 한다는 것을 깨달았습니다. 이것은 예상된 동작인가요? 로그인을 한 번으로 줄이려면 어떻게 해야 합니까?

예, 예상됩니다. 사용자의 컴퓨터가 Azure AD에 가입된 경우 사용자는 Azure AD에 자동으로 로그인합니다. 사용자는 RDWeb 로그인 양식에서만 자격 증명을 제공해야 합니다.

## <a name="sharepoint-publishing"></a>쉐어포인트 게시

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Azure AD 응용 프로그램 프록시를 통해 SharePoint를 게시하는 방법은 무엇입니까?

Azure [AD 응용 프로그램 프록시를 사용하여 SharePoint에 대한 원격 액세스 활성화를](application-proxy-integrate-with-sharepoint-server.md)참조하십시오.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>AD FS(활성 디렉터리 페더레이션 서비스) 게시 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Azure AD 응용 프로그램 프록시를 웹 응용 프로그램 프록시와 같은 AD FS 프록시로 사용할 수 있습니까?

아니요. Azure AD 응용 프로그램 프록시는 Azure AD와 함께 작동하도록 설계되었으며 AD FS 프록시 역할을 하는 요구 사항을 충족하지 않습니다.

## <a name="websocket"></a>Websocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>WebSocket 지원은 QlikSense 이외의 응용 프로그램에 대해 작동합니까?

현재 WebSocket 프로토콜 지원은 아직 공개 미리 보기 중이며 다른 응용 프로그램에서는 작동하지 않을 수 있습니다. 일부 고객은 WebSocket 프로토콜을 다른 응용 프로그램과 혼합하여 성공했습니다. 이러한 시나리오를 테스트하는 경우 결과를 듣고 싶습니다. 의견을 보내주십시오. aadapfeedback@microsoft.com

WINDOWS 관리 센터(WAC) 또는 원격 데스크톱 웹 클라이언트의 기능(이벤트로그, PowerShell 및 원격 데스크톱 서비스)은 현재 Azure AD 응용 프로그램 프록시를 통해 작동하지 않습니다.

## <a name="link-translation"></a>링크 번역

### <a name="does-using-link-translation-affect-performance"></a>링크 번역을 사용하면 성능에 영향을 미칩니까?

예. 링크 변환은 성능에 영향을 줍니다. 응용 프로그램 프록시 서비스는 응용 프로그램에서 하드 코딩된 링크를 검색하고 사용자에게 표시하기 전에 게시된 각각의 게시된 외부 URL로 대체합니다. 

최상의 성능을 위해 [사용자 지정 도메인을](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain)구성하여 동일한 내부 및 외부 URL을 사용하는 것이 좋습니다. 사용자 지정 도메인을 사용할 수 없는 경우 내 앱 보안 로그인 확장 또는 모바일의 Microsoft Edge 브라우저를 사용하여 링크 번역 성능을 향상시킬 수 있습니다. [Azure AD 응용 프로그램 프록시로 게시된 앱에 대한 하드코딩된 링크 리디렉션을](application-proxy-configure-hard-coded-link-translation.md)참조하십시오.

## <a name="wildcards"></a>와일드카드

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>와일드카드를 사용하여 동일한 사용자 지정 도메인 이름을 사용하지만 다른 프로토콜을 사용하여 HTTP용 및 HTTPS용 응용 프로그램을 게시하려면 어떻게 해야 합니까?

이 시나리오는 직접 지원되지 않습니다. 이 시나리오에 대한 옵션은 다음과 같습니다.

1. HTTP URL과 HTTPS URL을 와일드카드가 있는 별도의 응용 프로그램으로 게시하지만 각각 다른 사용자 지정 도메인을 제공합니다. 이 구성은 서로 다른 외부 URL을 가지고 있기 때문에 작동합니다.

2. 와일드카드 응용 프로그램을 통해 HTTPS URL을 게시합니다. 다음 응용 프로그램 프록시 PowerShell cmdlet을 사용하여 HTTP 응용 프로그램을 별도로 게시합니다.
   - [응용 프로그램 프록시 응용 프로그램 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [응용 프로그램 프록시 커넥터 관리](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
