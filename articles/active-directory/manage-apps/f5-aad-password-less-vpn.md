---
title: F5 VPN을 사용 하는 Azure AD 보안 하이브리드 액세스 | Microsoft Docs
description: 암호 없는 VPN에 대 한 F5 빅 IP와 Azure Active Directory SSO (Single Sign-on) 통합에 대 한 자습서
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2961f3f01f6ea4398fab6144b34fcb4409cdd96f
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96318169"
---
# <a name="tutorial-for-azure-active-directory-single-sign-on-integration-with-f5-big-ip-for-password-less-vpn"></a>암호 없는 VPN의 경우 F5 빅 IP와 Azure Active Directory Single Sign-On 통합에 대 한 자습서

이 자습서에서는 SHA (Secure Hybrid Access)를 위해 F 5 bigip 빅 IP 기반 Secure sockets layer VPN (가상 사설망) 솔루션을 AD (Azure Active Directory)와 통합 하는 방법에 대해 알아봅니다.

대규모 IP SSL VPN을 Azure AD와 통합 하면 다음과 같은 [주요 이점을 누릴 수](f5-aad-integration.md)있습니다.

- [AZURE AD 사전 인증 및 권한 부여를](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) 통한 제로 신뢰 관리 향상

- [VPN 서비스에 대 한 암호 없는 인증](https://www.microsoft.com/security/business/identity/passwordless)

- 단일 제어 평면에서 Id 및 액세스 관리- [Azure Portal](https://portal.azure.com/#home)

이러한 유용한 값 추가에도 불구 하 고, 클래식 VPN은 네트워크 경계에 대 한 개념을 기준으로 합니다. 여기에서 신뢰할 수 있는 위치는 외부에 있습니다. 회사 자산은 더 이상 엔터프라이즈 데이터 센터의 벽에 한정 되지 않고 고정 경계가 없는 다중 클라우드 환경에서 사용 되기 때문에이 모델은 더 이상 진정한 제로 신뢰 상태를 달성 하는 데 효과적이 지 않습니다. 따라서 고객은 [응용 프로그램 별로 액세스](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)를 관리 하는 데 더 많은 id 기반 방법으로 이동 하는 것이 좋습니다.

## <a name="scenario-description"></a>시나리오 설명

이 시나리오에서 SSL-VPN 서비스의 큰 IP APM 인스턴스는 SAML 서비스 공급자 (SP)로 구성 되 고 Azure AD는 신뢰할 수 있는 SAML IDP 되며, 사전 인증을 제공 합니다. Azure AD의 SSO (Single sign-on)는 클레임 기반 인증을 통해 빅 IP APM에 제공 되어 원활한 VPN 액세스 환경을 제공 합니다.

![Ssl-vpn 아키텍처를 보여 주는 이미지](media/f5-sso-vpn/ssl-vpn-architecture.png)

>[!NOTE]
>이 가이드 전체에서 참조 되는 모든 예제 문자열 또는 값은 실제 환경에 대 한 값으로 바꾸어야 합니다.

## <a name="prerequisites"></a>필수 구성 요소

그러나 F5의 이전 경험 또는 지식은 필요 하지 않습니다. 그러나 다음이 필요 합니다.

- Azure AD [무료 구독](https://azure.microsoft.com/trial/get-started-active-directory/) 이상

- 사용자 id는 [온-프레미스 디렉터리에서](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) Azure AD로 동기화 되어야 합니다.

- Azure AD 응용 프로그램 관리자 [권한이](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#application-administrator) 있는 계정

- 큰 ip에 대 한 클라이언트 트래픽을 라우팅하는 기존 대규모 IP 인프라 또는 [Azure에 빅 Ip 가상 버전을 배포](f5-bigip-deployment-guide.md)합니다.

- 테스트 하는 동안 대규모 IP 게시 된 VPN 서비스에 대 한 레코드가 공용 DNS 또는 테스트 클라이언트의 localhost 파일에 있어야 합니다.

- HTTPS를 통해 서비스를 게시 하는 데 필요한 SSL 인증서를 사용 하 여 큰 IP를 프로 비전 해야 합니다.

[F5 큰 IP 용어](https://www.f5.com/services/resources/glossary) 를 사용 하 여 직접 익숙해질 자습서에서 참조 되는 다양 한 구성 요소를 이해 하는 데도 도움이 됩니다.

>[!NOTE]
>Azure는 지속적으로 진화 하 고 있습니다 .이 가이드의 지침과 Azure Portal에 표시 되는 내용 사이에 미묘한 차이 있는 경우에는 걱정할 필요가 없습니다. 그러나 스크린샷은 빅 IP v15에서 가져온 것 이지만 v 13.1와 상대적으로 유사 하 게 유지 됩니다.

## <a name="add-f5-big-ip-from-the-azure-ad-gallery"></a>Azure AD 갤러리에서 F5 큰 IP 추가

단일 IP 간에 SAML 페더레이션 트러스트를 설정 하면 Azure AD에서 azure ad가 게시 된 VPN 서비스에 대 한 액세스 권한을 부여 하기 전에 Azure AD에 대 한 사전 인증 및 [조건부 액세스](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) 를 전달할 수 있습니다.

1. 응용 프로그램 관리자 권한이 있는 계정을 사용 하 여 Azure AD 포털에 로그인 합니다.

2. 왼쪽 탐색 창에서 **Azure Active Directory 서비스** 를 선택 합니다.

3. **엔터프라이즈 응용 프로그램** 으로 이동 하 고 위쪽 리본에서 **새 응용 프로그램** 을 선택 합니다.

4. 갤러리에서 F5 키를 검색 하 고 **F5 빅 IP APM AZURE AD 통합** 을 선택 합니다.

5. 응용 프로그램의 이름을 입력 한 다음 **추가/만들기** 를 통해 테 넌 트에 추가 합니다. 사용자는 Azure 및 Office 365 응용 프로그램 포털에서 이름을 아이콘으로 볼 수 있습니다. 이 이름은 해당 특정 서비스를 반영 해야 합니다. 예를 들면 VPN입니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. 새 F5 응용 프로그램 속성 보기에서 **Manage**  >  **Single sign-on** 관리로 이동 합니다.

2. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다. 아니요를 선택 하 여 Single Sign-On 설정을 저장 하 라는 메시지를 표시 **하지 않고 나중에 저장 하겠습니다**.

3. **Saml 설치 Single Sign-On** 메뉴에서 **기본 SAML 구성** 의 펜 아이콘을 선택 하 여 다음 세부 정보를 제공 합니다.

   - 미리 정의 된 **식별자 url** 을 큰 IP 게시 된 서비스의 url로 바꿉니다. 예, `https://ssl-vpn.contoso.com`

   - SAML 엔드포인트 경로를 포함 하 여 **회신 URL** 텍스트 상자를 사용 하 여 동일한 작업을 수행 합니다. 예, `https://ssl-vpn.contoso.com/saml/sp/profile/post/acs`

   - 이 구성 에서만 응용 프로그램은 IDP 시작 모드에서 작동 합니다. 여기서 Azure AD는 빅 IP SAML 서비스로 리디렉션하기 전에 사용자에 게 SAML 어설션을 발급 합니다. IDP 시작 모드를 지원 하지 않는 앱의 경우 큰 IP SAML 서비스에 대 한 로그온 **URL** 을 지정 합니다. 예: `https://ssl-vpn.contoso.com`.

   - 로그 아웃 Url의 경우 게시 되는 서비스의 호스트 헤더를 통해 대규모 IP APM SLO (단일 로그 아웃) 끝점을 입력 합니다. 예, `https://ssl-vpn.contoso.com/saml/sp/profile/redirect/slr`

   SLO URL을 제공 하면 사용자가 로그 아웃 한 후 사용자 세션이 두 끝, 즉 빅 IP와 Azure AD에서 모두 종료 됩니다. 또한 빅 IP APM은 특정 응용 프로그램 URL을 호출할 때 모든 세션을 종료 하는 [옵션](https://support.f5.com/csp/article/K12056) 을 제공 합니다.

![기본 saml 구성을 보여 주는 이미지](media/f5-sso-vpn/basic-saml-configuration.png).

>[!NOTE]
>TMOS v16에서 SAML SLO 끝점이/saml/sp/profile/redirect/slo로 변경 되었습니다.

4. SAML 구성 메뉴를 종료 하기 전에 **저장** 을 선택 하 고 SSO 테스트 프롬프트를 건너뜁니다.

Azure AD는 빅 IP APM 인증을 위해 사용자에 게이를 발급 하므로 **클레임 & 사용자 특성** 섹션의 속성을 관찰 합니다.

![사용자 특성 클레임을 보여 주는 이미지](media/f5-sso-vpn/user-attributes-claims.png)

Azure AD에 있는 경우에만 기본 집합에 정의 된 모든 클레임이 채워진 특성으로 발생 하는 경우에만 해당 하는 다른 특정 클레임을 추가 하면 됩니다. 마찬가지로, 디렉터리 [역할 또는 그룹](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) 멤버 자격은 클레임으로 발급 되기 전에 Azure AD의 사용자 개체에 대해 정의 해야 합니다.

![페더레이션 메타 데이터 다운로드 링크를 보여 주는 이미지](media/f5-sso-vpn/saml-signing-certificate.png)

Azure AD에서 만든 SAML 서명 인증서의 수명은 3 년 이며, 따라서 Azure AD 게시 된 지침을 사용 하 여 관리 해야 합니다.

### <a name="azure-ad-authorization"></a>Azure AD 권한 부여

기본적으로 Azure AD는 서비스에 대 한 액세스 권한이 부여 된 사용자 에게만 토큰을 발급 합니다.

1. 응용 프로그램의 구성 뷰에서 **사용자 및 그룹** 을 선택 합니다.

2. **+ 사용자 추가** 를 선택 하 고 할당 추가 메뉴에서 **사용자 및 그룹** 을 선택 합니다.

3. **사용자 및 그룹** 대화 상자에서 VPN에 액세스할 수 있는 권한이 있는 사용자 그룹을 추가한 다음 할당을 **선택** 합니다.  >  **Assign**

![사용자 링크 추가 이미지가 표시 됩니다. ](media/f5-sso-vpn/add-user-link.png)

4. 이는 SAML 페더레이션 트러스트의 Azure AD 부분을 완료 합니다. 이제 SAML 사전 인증을 위해 SSL VPN 서비스를 게시 하 고 해당 속성 집합으로 구성 하 여 트러스트를 완료할 수 있습니다.

## <a name="big-ip-apm-configuration"></a>큰 IP APM 구성

### <a name="saml-federation"></a>SAML 페더레이션

다음 섹션에서는 Azure AD와 VPN 서비스 페더레이션을 완료 하는 데 필요한 큰 IP SAML 서비스 공급자 및 해당 SAML IDP 개체를 만듭니다.

1. **액세스**  >  **페더레이션**  >  **SAML 서비스 공급자**  >  **로컬 SP 서비스** 로 이동 하 고 **만들기** 를 선택 합니다.

![큰 IP SAML 구성을 보여 주는 이미지](media/f5-sso-vpn/bigip-saml-configuration.png)

2. 이전에 Azure AD에서 정의한 **이름과** 동일한 **엔터티 ID** 값 및 응용 프로그램에 연결 하는 데 사용 되는 호스트 FQDN을 입력 합니다.

![새 SAML SP 서비스 만들기 이미지가 표시 됩니다.](media/f5-sso-vpn/create-new-saml-sp.png)

   SP **이름** 설정은 엔터티 ID가 게시 된 url의 호스트 이름 부분과 정확히 일치 하지 않거나 일반 호스트 이름 기반 url 형식이 아닌 경우에만 필요 합니다. 엔터티 ID가 인 경우 게시 되는 응용 프로그램의 외부 스키마 및 호스트 이름을 제공 `urn:ssl-vpn:contosoonline` 합니다.

3. 아래로 스크롤하여 새 **SAML SP 개체** 를 선택 하 고 **IDP 커넥터 바인딩/바인딩 해제** 를 선택 합니다.

![로컬 SP 서비스를 사용 하 여 페더레이션 만들기를 보여 주는 이미지](media/f5-sso-vpn/federation-local-sp-service.png)

4. **Create NEW IDP Connector** 를 선택 하 고 드롭다운 메뉴에서 **메타 데이터를** 선택 합니다.

![새 IDP 커넥터 만들기 이미지가 표시 됩니다.](media/f5-sso-vpn/create-new-idp-connector.png)

5. 앞에서 다운로드 한 페더레이션 메타 데이터 XML 파일을 찾아 외부 SAML IDP를 나타내는 APM 개체의 **Id 공급자 이름을** 제공 합니다.

6. 새 **행 추가** 를 선택 하 여 새 Azure AD external IDP 커넥터를 선택 합니다.

![외부 IDP 커넥터 표시 이미지](media/f5-sso-vpn/external-idp-connector.png)

7. **업데이트** 를 선택 하 여 saml SP 개체를 saml IDP 개체에 바인딩한 다음, **확인** 을 선택 합니다.

![SP를 사용 하 여 SAML IDP를 보여 주는 이미지](media/f5-sso-vpn/saml-idp-using-sp.png)

### <a name="webtop-configuration"></a>Webtop 구성

다음 단계를 통해 큰 IP의 소유 웹 포털을 통해 사용자에 게 SSL-VPN을 제공할 수 있습니다.

1. **액세스**  >  **webtops**  >  **Webtop 목록** 으로 이동 하 고 **만들기** 를 선택 합니다.

2. 포털에 이름을 지정 하 고 유형을 **Full** 로 설정 합니다. 예: `Contoso_webtop`.

3. 나머지 기본 설정을 조정 하 고 **완료** 를 선택 합니다.

![Webtop 구성 표시 이미지](media/f5-sso-vpn/webtop-configuration.png)

### <a name="vpn-configuration"></a>VPN 구성

VPN 기능은 여러 요소로 구성 되며, 각 요소는 전체 서비스의 다른 측면을 제어 합니다.

1. **Access**  >  **Connectivity/vpn**  >  **Network Access (vpn)**  >  **IPV4 임대 풀** 로 이동 하 고 **만들기** 를 선택 합니다.

2. VPN 클라이언트에 할당 되는 IP 주소 풀의 이름을 제공 합니다. 예를 들어 Contoso_vpn_pool

3. 유형을 **IP 주소 범위로** 설정 하 고 시작 및 종료 ip를 입력 한 다음 **추가** 및 **완료** 를 입력 합니다.

![Vpn 구성 표시 이미지](media/f5-sso-vpn/vpn-configuration.png)

네트워크 액세스 목록은 VPN 풀의 IP 및 DNS 설정, 사용자 라우팅 권한으로 서비스를 프로 비전 하 고 필요한 경우 응용 프로그램을 시작할 수도 있습니다.

1. **액세스**  >  **연결/vpn: 네트워크 액세스 (vpn)**  >  **네트워크 액세스 목록** 으로 이동 하 고 **만들기** 를 선택 합니다.

2. VPN 액세스 목록 및 캡션에 대 한 이름을 제공 합니다 (예: Contoso-VPN과 함께 **완료** 됨).

![네트워크 액세스 목록의 vpn 구성을 보여 주는 이미지](media/f5-sso-vpn/vpn-configuration-network-access-list.png)

3. 위쪽 리본에서 **네트워크 설정** 을 선택 하 고 아래 설정을 추가 합니다.

   • **지원 되는 IP 버전**: IPV4

   • **IPV4 임대 풀**: 이전에 만든 VPN 풀을 선택 합니다 (예: Contoso_vpn_pool

![Contoso vpn 풀을 보여 주는 이미지](media/f5-sso-vpn/contoso-vpn-pool.png)

   클라이언트 설정 옵션을 사용 하 여 VPN을 설정할 때 클라이언트 트래픽이 라우팅되는 방법에 대 한 제한을 적용할 수 있습니다.

4. **마침** 을 선택 하 고 DNS/호스트 탭으로 이동 하 여 설정을 추가 합니다.

   • **IPV4 기본 이름 서버**: 사용자 환경의 DNS 서버에 대 한 IP

   • **DNS 기본 도메인 접미사**:이 특정 VPN 연결에 대 한 도메인 접미사입니다. 예: contoso.com

![이미지가 기본 도메인 접미사를 표시 합니다.](media/f5-sso-vpn/domain-suffix.png)

[F5 문서](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-network-access-11-5-0/2.html) 는 기본 설정에 따라 나머지 설정을 조정 하는 방법에 대 한 세부 정보를 제공 합니다.

이제 VPN 서비스에서 지원 해야 하는 각 VPN 클라이언트 형식에 대 한 설정을 구성 하려면 큰 IP 연결 프로필이 필요 합니다. 예를 들어 Windows, OSX 및 Android가 있습니다.

1. **액세스**  >  **연결/VPN** 연결 프로필로 이동 하 여  >  **Connectivity**  >  **Profiles** **추가** 를 선택 합니다.

2. 프로필 이름을 입력 하 고 부모 프로필을 **/Common/connectivity** 로 설정 합니다 (예: Contoso_VPN_Profile).

![새 연결 프로필 만들기 이미지가 표시 됩니다.](media/f5-sso-vpn/create-connectivity-profile.png)

F 5 bigip [설명서](https://techdocs.f5.com/kb/en-us/bigip-edge-apps.html) 에서는 클라이언트 지원에 대 한 자세한 정보를 제공 합니다.

## <a name="access-profile-configuration"></a>액세스 프로필 구성

VPN 개체가 구성 된 상태에서 SAML 인증에 서비스를 사용 하도록 설정 하려면 액세스 정책이 필요 합니다.

1. **액세스**  >  **프로필/정책**  >  **액세스 프로필 (세션당 정책)** 로 이동 하 고 **만들기** 를 선택 합니다.

2. 프로필 이름을 입력 하 고 프로필 유형에 대해 **모두** 를 선택 합니다 (예: Contoso_network_access

3. 아래로 스크롤하여 **허용 된 언어** 목록에 언어를 하나 이상 추가 하 고 **마침** 을 선택 합니다.

![일반 속성을 보여 주는 이미지](media/f5-sso-vpn/general-properties.png)

4. 새 액세스 프로필의 Per-Session 정책 필드에서 **편집** 을 선택 하 여 별도의 브라우저 탭에서 시각적 정책 편집기를 시작 합니다.

![세션 별 정책 표시 이미지](media/f5-sso-vpn/per-session-policy.png)

5. 기호를 선택 **+** 하 고 팝업 **인증**  >  **SAML 인증**  >  **항목 추가** 를 선택 합니다.

6. SAML 인증 SP 구성에서 이전에 만든 VPN SAML SP 개체를 선택 하 고 **저장** 을 선택 합니다.

![Saml 인증을 보여 주는 이미지](media/f5-sso-vpn/saml-authentication.png)

7. **+** SAML 인증의 성공적인 분기에 대해를 선택 합니다.

8. 할당 탭에서 **고급 리소스 할당** 다음에 **항목 추가** 를 선택 합니다.

![고급 리소스 할당을 보여 주는 이미지](media/f5-sso-vpn/advance-resource-assign.png)

9. 팝업에서 **새 항목** 을 선택 하 고 **추가/삭제** 를 선택 합니다.

10. 자식 창에서 **네트워크 액세스** 를 선택한 다음 이전에 만든 네트워크 액세스 프로필을 선택 합니다.

![새 네트워크 액세스 항목을 추가 하는 이미지 표시](media/f5-sso-vpn/add-new-entry.png)

11. **Webtop** 탭으로 전환 하 고 이전에 만든 webtop 개체를 추가 합니다.

![이미지에 webtop 개체 추가 표시](media/f5-sso-vpn/add-webtop-object.png)

12. **업데이트** 를 선택 하 고 **저장** 을 선택 합니다.

13. 위쪽 거부 상자의 링크를 선택 하 여 성공 분기를 **허용** 으로 변경 하 고 **저장** 합니다.

![새 시각적 정책 편집기를 보여 주는 이미지](media/f5-sso-vpn/vizual-policy-editor.png)

14. **액세스 정책 적용** 을 선택 하 여 해당 설정을 커밋하고 시각적 정책 편집기 탭을 닫습니다.

![새 액세스 정책 관리자를 보여 주는 이미지](media/f5-sso-vpn/access-policy-manager.png)

## <a name="publish-the-vpn-service"></a>VPN 서비스 게시

모든 설정이 적용 된 상태에서 APM은 이제 VPN에 연결 하는 클라이언트를 수신 하는 프런트 엔드 가상 서버가 필요 합니다.

1. **로컬 트래픽**  >  **가상 서버**  >  **가상 서버 목록** 을 선택 하 고 **만들기** 를 선택 합니다.

2. VPN 가상 서버의 **이름을** 제공 합니다 (예: **VPN_Listener**).

3. 클라이언트 트래픽을 수신할 수 있는 라우팅을 사용 하는 사용 되지 않는 **IP 대상 주소** 를 가상 서버에 제공 합니다.

4. 서비스 포트를 **443 HTTPS** 로 설정 하 고 상태가 **사용** 으로 표시 되는지 확인 합니다.

![새 가상 서버를 보여 주는 이미지](media/f5-sso-vpn/new-virtual-server.png)

5. **Http 프로필** 을 http로 설정 하 고 필수 구성 요소의 일부로 프로 비전 한 공용 ssl 인증서에 대 한 ssl 프로필 (클라이언트)을 추가 합니다.

![Ssl 프로필을 보여 주는 이미지](media/f5-sso-vpn/ssl-profile.png)

6. 액세스 정책에서 만든 VPN 개체를 사용 하도록 **액세스 프로필** 및 **연결 프로필** 을 설정 합니다.

![액세스 정책 표시 이미지](media/f5-sso-vpn/access-policy.png)

7. 완료 되 면 **완료** 를 선택 합니다.

8.  SSL-VPN 서비스는 이제 해당 URL을 통해 직접 또는 Microsoft의 응용 프로그램 포털을 통해 SHA를 통해 게시 되 고 액세스할 수 있습니다.

## <a name="additional-resources"></a>추가 리소스

- [암호의 끝, 암호 없는 수준으로 이동 합니다.](https://www.microsoft.com/security/business/identity/passwordless)

- [조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [원격 작업을 사용 하도록 설정 하는 Microsoft 제로 신뢰 프레임 워크](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Azure AD와 전체 응용 프로그램 통합에 대 한 5 단계](https://docs.microsoft.com/azure/active-directory/fundamentals/five-steps-to-full-application-integration-with-azure-ad)

## <a name="next-steps"></a>다음 단계

원격 Windows 클라이언트에서 브라우저를 열고 **큰 IP VPN 서비스** 의 url로 이동 합니다. Azure AD에 인증 한 후에는 큰 IP webtop 포털 및 VPN 시작 관리자가 표시 됩니다.

![Vpn 시작 관리자 표시 이미지](media/f5-sso-vpn/vpn-launcher.png)

VPN 타일을 선택 하면 큰 IP에 지 클라이언트를 설치 하 고 SHA에 대해 구성 된 VPN 연결을 설정 합니다.
또한 F5 VPN 응용 프로그램은 Azure AD 조건부 액세스에서 대상 리소스로 표시 되어야 합니다. 조건부 액세스 정책을 작성 하 고 사용자가 Azure AD [암호 없는 인증](https://www.microsoft.com/security/business/identity/passwordless)에 대 한 사용자를 사용 하도록 설정 하기 위한 [지침](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policies) 을 참조 하세요.