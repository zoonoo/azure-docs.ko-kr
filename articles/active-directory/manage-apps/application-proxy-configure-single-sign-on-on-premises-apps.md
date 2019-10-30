---
title: Azure AD 앱 프록시를 사용 하는 온-프레미스 앱에 대 한 SAML Single Sign-On | Microsoft Docs
description: SAML 인증으로 보호 되는 온-프레미스 응용 프로그램에 대 한 Single Sign-On를 제공 하는 방법을 알아봅니다. 응용 프로그램 프록시를 사용 하 여 온-프레미스 앱에 대 한 원격 액세스를 제공 합니다.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d004d1183c9f5c1aec2dae7ab2d638528f164cd9
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2019
ms.locfileid: "73062509"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>응용 프로그램 프록시를 사용 하는 온-프레미스 응용 프로그램에 대 한 SAML Single Sign-On

SAML 인증으로 보호 되는 온-프레미스 응용 프로그램에 SSO (Single Sign-On)를 제공 하 고 응용 프로그램 프록시를 통해 이러한 응용 프로그램에 대 한 원격 액세스를 제공할 수 있습니다. SAML Single Sign-On를 사용 하 여 Azure Active Directory (Azure AD)는 사용자의 Azure AD 계정을 사용 하 여 응용 프로그램을 인증 합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. 사용자가 SAML 클레임에서 정의한 규칙에 따라 특정 응용 프로그램 역할에 사용자를 매핑할 수도 있습니다. 사용자는 SAML SSO 외에도 응용 프로그램 프록시를 사용 하도록 설정 하 여 응용 프로그램에 대 한 외부 액세스와 원활한 SSO 환경을 제공 합니다.

응용 프로그램은 **Azure Active Directory**에서 발급 한 SAML 토큰을 사용할 수 있어야 합니다. 이 구성은 온-프레미스 id 공급자를 사용 하는 응용 프로그램에는 적용 되지 않습니다. 이러한 시나리오에서는 [응용 프로그램을 AZURE AD로 마이그레이션하기 위한 리소스](migration-resources.md)를 검토 하는 것이 좋습니다.

응용 프로그램 프록시를 사용 하는 SAML SSO는 SAML 토큰 암호화 기능 에서도 작동 합니다. 자세한 내용은 [AZURE AD SAML 토큰 암호화 구성](howto-saml-token-encryption.md)을 참조 하세요.

아래 프로토콜 다이어그램은 서비스 공급자가 시작한 (SP에서 시작) 흐름과 id 공급자 시작 (IdP 시작) 흐름 모두에 대 한 Single Sign-On 시퀀스를 설명 합니다. 응용 프로그램 프록시는 온-프레미스 응용 프로그램에 대 한 SAML 요청 및 응답을 캐싱하여 SAML SSO와 작동 합니다.

  ![SAML SP 흐름](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 흐름](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>응용 프로그램 만들기 및 SAML SSO 설정

1. Azure Portal에서 **Azure Active Directory > Enterprise applications** 를 선택 하 고 **새 응용 프로그램**을 선택 합니다.

2. 온 **-프레미스 응용 프로그램** 섹션에서 **온-프레미스 응용 프로그램 추가**를 선택 합니다.

3. 새 응용 프로그램의 표시 이름을 입력 하 고 **추가**를 선택 합니다.

4. 앱의 **개요** 페이지에서 **Single sign-on**을 선택 합니다.

5. Single Sign-On 방법으로 **SAML** 을 선택 합니다.

6. 먼저 회사 네트워크에서 작업 하는 동안 SAML SSO를 설정 합니다. SAML을 **사용 하 여 Single Sign-on 설정** 페이지에서 **기본 SAML 구성** 제목으로 이동 하 여 **편집** 아이콘 (연필)을 선택 합니다. [기본 saml 구성 입력](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) 의 단계에 따라 응용 프로그램에 대 한 saml 기반 인증을 구성 합니다.

7. 응용 프로그램에 사용자를 한 명 이상 추가 하 고 테스트 계정에 응용 프로그램에 대 한 액세스 권한이 있는지 확인 합니다. 회사 네트워크에 연결 되어 있는 동안 테스트 계정을 사용 하 여 응용 프로그램에 Single Sign-On 있는지 확인 합니다. 

   > [!NOTE]
   > 응용 프로그램 프록시를 설정한 후에는 다시 돌아와서 SAML **회신 URL**을 업데이트 합니다.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 온-프레미스 응용 프로그램 게시

온-프레미스 응용 프로그램에 대 한 SSO를 제공 하려면 먼저 응용 프로그램 프록시를 사용 하도록 설정 하 고 커넥터를 설치 해야 합니다. 온-프레미스 환경을 준비 하 고, 커넥터를 설치 및 등록 하 고, 커넥터를 테스트 하는 방법에 대해 알아보려면 [AZURE AD에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md) 자습서를 참조 하세요. 그런 다음 응용 프로그램 프록시를 사용 하 여 새 응용 프로그램을 게시 하려면 다음 단계를 수행 합니다. 아래에 설명 되지 않은 다른 설정은 자습서의 [AZURE AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 섹션을 참조 하세요.

1. 응용 프로그램이 Azure Portal에서 열려 있는 상태에서 **응용 프로그램 프록시**를 선택 합니다. 응용 프로그램에 대 한 **내부 URL** 을 제공 합니다. 사용자 지정 도메인을 사용 하는 경우에는 응용 프로그램에 대 한 SSL 인증서도 업로드 해야 합니다. 
   > [!NOTE]
   > 최적화 된 사용자 환경을 위해 가능한 한 사용자 지정 도메인을 사용 하는 것이 가장 좋습니다. [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)에 대해 자세히 알아보세요.

2. 응용 프로그램에 대 한 **사전 인증** 방법으로 **Azure Active Directory** 를 선택 합니다.

3. 응용 프로그램에 대 한 **외부 URL** 을 복사 합니다. SAML 구성을 완료 하려면이 URL이 필요 합니다.

4. 테스트 계정을 사용 하 여 응용 프로그램 프록시가 올바르게 설정 되었는지 확인 하려면 **외부 URL** 을 사용 하 여 응용 프로그램을 열어 봅니다. 문제가 있는 경우 [응용 프로그램 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조 하세요.

## <a name="update-the-saml-configuration"></a>SAML 구성 업데이트

1. Azure Portal에서 응용 프로그램이 열려 있는 상태에서 **Single sign-on**을 선택 합니다. 

2. SAML을 **사용 하 여 Single Sign-on 설정** 페이지에서 **기본 SAML 구성** 제목으로 이동 하 여 **편집** 아이콘 (연필)을 선택 합니다. 응용 프로그램 프록시에서 구성한 **외부 url** 은 **식별자**, **회신 url**및 **로그 아웃 url** 필드를 자동으로 채웁니다. 이러한 Url은 응용 프로그램 프록시가 제대로 작동 하는 데 필요 하므로 편집 하지 마세요.

3. 응용 프로그램 프록시에서 도메인에 연결할 수 있도록 이전에 구성 된 **회신 URL** 을 편집 합니다. 예를 들어 **외부 URL** 이 `https://contosotravel-f128.msappproxy.net` 되 고 원래 **회신 url** 이 `https://contosotravel.com/acs`된 경우 원래 **회신 url** 을 `https://contosotravel-f128.msappproxy.net/acs`로 업데이트 해야 합니다. 

    ![기본 SAML 구성 데이터 입력](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 업데이트 된 **회신 URL** 옆에 있는 확인란을 선택 하 여 기본값으로 표시 합니다.

   * 필요한 **회신 url** 이 이미 나열 되어 있으면이 **회신 url** 을 기본값으로 표시 하 고 이전에 구성 된 **회신 url**을 삭제 합니다.

   * SP에서 시작한 흐름의 경우 백 엔드 응용 프로그램에서 올바른 **회신 URL** 을 지정 하는지 또는 인증 토큰을 수신 하기 위한 Assertion Consumer Service URL를 지정 하는지 확인 합니다.

    > [!NOTE]
    > 백 엔드 응용 프로그램에서 **회신 url** 이 내부 url이 될 것으로 예상 되는 경우에는 사용자 [지정 도메인](application-proxy-configure-custom-domain.md) 을 사용 하 여 내부 및 외부 url을 일치 시키거나 사용자의 장치에 My Apps 보안 로그인 확장을 설치 해야 합니다. 이 확장은 적절 한 응용 프로그램 프록시 서비스로 자동으로 리디렉션됩니다. 확장을 설치 하려면 [내 앱 보안 로그인 확장](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)을 참조 하세요.
    
## <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 앱을 테스트 하려면:

1. 브라우저를 열고 앱을 게시할 때 만든 **외부 URL** 로 이동 합니다. 
1. 앱에 할당한 테스트 계정으로 로그인합니다. 응용 프로그램을 로드 하 고 응용 프로그램에 SSO를 제공할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시에서 Single Sign-On을 제공하는 방법](application-proxy-single-sign-on.md)
- [애플리케이션 프록시 문제 해결](application-proxy-troubleshoot.md)
