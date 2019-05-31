---
title: SAML에서 single sign-on Azure Active Directory 응용 프로그램 프록시 (미리 보기)를 사용 하 여 온-프레미스 응용 프로그램에 대 한 | Microsoft Docs
description: 온-프레미스에 대 한 single sign on 제공 하는 방법은 응용 프로그램이 SAML 인증을 사용 하 여 보안이 유지 되는 응용 프로그램 프록시를 통해 게시 합니다.
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
ms.date: 05/20/2019
ms.author: mimart
ms.reviewer: japere
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 907cb598d708bfa26f53d2e43fef5456258c21b1
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393039"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy-preview"></a>SAML에서 single sign-on 응용 프로그램 프록시 (미리 보기)를 사용 하 여 온-프레미스 응용 프로그램에 대 한

SAML 인증을 사용 하 여 보안이 유지 되 고 응용 프로그램 프록시를 통해 이러한 응용 프로그램에 대 한 원격 액세스를 제공 하는 온-프레미스 응용 프로그램에서 single sign-on (SSO)을 제공할 수 있습니다. SAML single sign-on을 사용 하 여 Azure Active Directory (Azure AD)는 사용자의 Azure AD 계정을 사용 하 여 응용 프로그램에 인증 합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. 사용자에 SAML 클레임에서 정의 하는 규칙에 따라 특정 응용 프로그램 역할에 매핑할 수도 있습니다. SAML SSO 외에도 응용 프로그램 프록시를 사용 하 여 사용자에 게는 응용 프로그램 및 원활한 SSO 경험을 외부 액세스를 해야 합니다.

응용 프로그램에서 발급 한 SAML 토큰을 사용할 수 있어야 **Azure Active Directory**합니다. 이 구성은 온-프레미스 id 공급자를 사용 하 여 응용 프로그램에 적용 되지 않습니다. 이러한 시나리오에 대 한 검토 권장 [마이그레이션에 대 한 리소스 응용 프로그램을 Azure AD](migration-resources.md)합니다.

SAML 토큰 암호화 기능을 사용 하 여 응용 프로그램 프록시를 사용 하 여 SAML SSO 에서도 작동합니다. 자세한 내용은 참조 하세요. [Azure AD SAML 구성 토큰 암호화](howto-saml-token-encryption.md)합니다.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 여 온-프레미스 응용 프로그램 게시

온-프레미스 응용 프로그램에 SSO를 제공할 수 있습니다, 전에 응용 프로그램 프록시를 설정한 있고 커넥터가 설치 되어 있는지 확인 합니다. 참조 [Azure AD에서 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램을 추가 합니다.](application-proxy-add-on-premises-application.md) 알아보려면 방법입니다.

염두에 다음 자습서를 진행 하는 경우:

* 자습서의 지침에 따라 응용 프로그램을 게시 합니다. 선택 되어 있는지 확인 **Azure Active Directory** 으로 **사전 인증** 응용 프로그램에 대 한 메서드 (4 단계 [Azure AD에 온-프레미스 앱을 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad
)).
* 복사 합니다 **외부 URL** 응용 프로그램에 대 한 합니다.
* 모범 사례로, 최적화 된 사용자 환경을 위해 가능한 사용자 지정 도메인을 사용 합니다. 에 대해 자세히 알아보세요 [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)합니다.
* 응용 프로그램에 하나 이상의 사용자를 추가 하 고 테스트 계정에는 온-프레미스 응용 프로그램에 대 한 액세스 권한이 있는지 확인 합니다. 방문 하 여 응용 프로그램에 연결 하면 테스트 계정을 사용 하는 **외부 URL** 응용 프로그램 프록시를 유효성을 검사 하는 올바르게 설정 합니다. 문제 해결 정보를 참조 하세요 [문제를 해결 하는 응용 프로그램 프록시 문제 및 오류 메시지](application-proxy-troubleshoot.md)합니다.

## <a name="set-up-saml-sso"></a>SAML SSO 설정

1. Azure portal에서 선택 **Azure Active Directory > 엔터프라이즈 응용 프로그램** 목록에서 응용 프로그램을 선택 합니다.
1. 앱에서 **개요** 페이지에서 **Single sign on**합니다.
1. 선택 **SAML** single sign-on 방법으로 합니다.
1. 에 **구성에서 Single Sign-on SAML을 사용 하 여 설정** 페이지에서 편집를 **기본 SAML 구성** 데이터의 단계를 수행 하 고 [Enter 기본 SAML 구성](configure-single-sign-on-non-gallery-applications.md#saml-based-single-sign-on) SAML 기반을 구성 하려면 응용 프로그램에 대 한 인증입니다.

   * 있는지 확인 합니다 **회신 URL** 일치 하는 **외부 URL** 게시 응용 프로그램 프록시를 통해 또는 아래에 있는 경로 온-프레미스 응용 프로그램에 대 한를 **외부 URL**.
   * 응용 프로그램에 다른 필요한 위치를 IDP에서 시작한 흐름에 대 한 **회신 URL** 으로 SAML 구성 추가 **추가** 표시 옆에 있는 확인란 확인 하 고 목록에서 URL로 지정 합니다 주 **회신 URL**합니다.
   * SP에서 시작 된 흐름에 대 한 백 엔드 응용 프로그램이 올바른 지정 하는지 확인 합니다 **회신 URL** 또는 인증 토큰을 받기 위해 사용할 Assertion Consumer Service URL입니다.

     ![기본 SAML 구성 데이터 입력](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)

    > [!NOTE]
    > 백 엔드 응용 프로그램에서 예상 하는 경우는 **회신 URL** 사용 하 여 내부 URL 일을 해야 [사용자 지정 도메인](application-proxy-configure-custom-domain.md) 내부 및 외부 URL 일치 또는 내 앱 보안 로그인 확장 설치 사용자의 장치입니다. 이 확장은 적절 한 응용 프로그램 프록시 서비스를 자동으로 리디렉션됩니다. 확장을 설치 하려면 [My Apps 보안 로그인 확장](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)합니다.

## <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 앱을 테스트 합니다.

1. 브라우저를 열고 앱을 게시할 때 만든 외부 URL로 이동 합니다. 
1. 앱에 할당한 테스트 계정으로 로그인합니다. 응용 프로그램에 SSO를 응용 프로그램을 로드할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시에서 Single Sign-On을 제공하는 방법](application-proxy-single-sign-on.md)
- [애플리케이션 프록시 문제 해결](application-proxy-troubleshoot.md)
