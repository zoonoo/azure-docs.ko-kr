---
title: Azure AD 앱 프록시를 사용한 온-프레미스 앱에 대한 SAML 단일 사인온
description: SAML 인증을 통해 보호되는 온-프레미스 응용 프로그램에 대해 단일 사인온을 제공하는 방법을 알아봅니다. 응용 프로그램 프록시를 사용하여 온-프레미스 앱에 대한 원격 액세스를 제공합니다.
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
ms.openlocfilehash: d3d2117e913f292e92f37f31d2e123587c70a189
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803300"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 고 온-프레미스 응용 프로그램에 대 한 SAML 단일 사인온

SAML 인증으로 보호되는 온-프레미스 응용 프로그램에 단일 사인온(SSO)을 제공하고 응용 프로그램 프록시를 통해 이러한 응용 프로그램에 대한 원격 액세스를 제공할 수 있습니다. SAML 단일 사인온을 사용하면 Azure Active Directory(Azure AD)가 사용자의 Azure AD 계정을 사용하여 응용 프로그램에 인증됩니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. SAML 클레임에서 정의한 규칙에 따라 사용자를 특정 응용 프로그램 역할에 매핑할 수도 있습니다. SAML SSO 이외에 응용 프로그램 프록시를 사용하도록 설정하면 사용자는 응용 프로그램에 대한 외부 액세스 및 원활한 SSO 환경을 사용할 수 있습니다.

응용 프로그램은 **Azure Active Directory**에서 발급한 SAML 토큰을 사용할 수 있어야 합니다. 이 구성은 온-프레미스 ID 공급자를 사용하는 응용 프로그램에는 적용되지 않습니다. 이러한 시나리오에서는 [응용 프로그램을 Azure AD로 마이그레이션하기 위한 리소스를 검토하는](migration-resources.md)것이 좋습니다.

응용 프로그램 프록시가 있는 SAML SSO는 SAML 토큰 암호화 기능에서도 작동합니다. 자세한 내용은 [Azure AD SAML 토큰 암호화 구성을](howto-saml-token-encryption.md)참조하십시오.

아래 프로토콜 다이어그램은 서비스 공급자가 시작한(SP-시작) 흐름과 ID 공급자가 시작한 ID 공급자(IdP-시작) 흐름모두에 대한 단일 사인온 시퀀스를 설명합니다. 응용 프로그램 프록시는 온-프레미스 응용 프로그램에서 SAML 요청 및 응답을 캐싱하여 SAML SSO와 함께 작동합니다.

  ![SAML SP 흐름](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![SAML SP 흐름](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>응용 프로그램 만들기 및 SAML SSO 설정

1. Azure 포털에서 **Azure Active Directory > 엔터프라이즈 응용 프로그램을** 선택하고 새 응용 **프로그램을**선택합니다.

2. 새 응용 프로그램의 표시 이름을 입력하고 **갤러리에서 찾을 수 없는 다른 응용 프로그램 통합을**선택한 다음 **만들기를**선택합니다.

3. 앱의 **개요** 페이지에서 **단일 사인온을**선택합니다.

4. **SAML을** 단일 사인온 방법으로 선택합니다.

5. 먼저 SAML SSO를 회사 네트워크에서 작업하도록 설정합니다. **SAML을 사용하여 단일 사인온 설정** 페이지에서 **기본 SAML 구성** 제목으로 이동하여 **편집** 아이콘(연필)을 선택합니다. [기본 SAML 구성 입력의](configure-single-sign-on-non-gallery-applications.md#step-1-edit-the-basic-saml-configuration) 단계를 수행하여 응용 프로그램에 대한 SAML 기반 인증을 구성합니다.

6. 응용 프로그램에 한 명 이상의 사용자를 추가하고 테스트 계정에서 응용 프로그램에 액세스할 수 있는지 확인합니다. 회사 네트워크에 연결된 동안 테스트 계정을 사용하여 응용 프로그램에 대한 단일 로그온이 있는지 확인합니다. 

   > [!NOTE]
   > 응용 프로그램 프록시를 설정하면 SAML **회신 URL을**다시 업데이트합니다.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>응용 프로그램 프록시를 사용 하 고 온-프레미스 응용 프로그램 게시

온-프레미스 응용 프로그램에 SSO를 제공하려면 응용 프로그램 프록시를 사용하도록 설정하고 커넥터를 설치해야 합니다. 온-프레미스 환경을 준비하고, 커넥터를 설치 및 등록하고, 커넥터를 테스트하는 방법을 알아보려면 [Azure AD의 응용 프로그램 프록시를 통해 원격 액세스를 위한 온-프레미스 응용 프로그램 추가](application-proxy-add-on-premises-application.md) 자습서를 참조하십시오. 그런 다음 다음 다음 단계를 수행하여 응용 프로그램 프록시를 사용하여 새 응용 프로그램을 게시합니다. 아래에 언급되지 않은 다른 설정의 경우 자습서의 [Azure AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 섹션을 참조하십시오.

1. Azure 포털에서 응용 프로그램이 계속 열려 있는 경우 **응용 프로그램 프록시를**선택합니다. 응용 프로그램의 **내부 URL을** 제공합니다. 사용자 지정 도메인을 사용하는 경우 응용 프로그램에 대한 TLS/SSL 인증서도 업로드해야 합니다. 
   > [!NOTE]
   > 최적화된 사용자 환경을 위해 가능하면 사용자 지정 도메인을 사용하는 것이 가장 좋습니다. [Azure AD 응용 프로그램 프록시에서 사용자 지정 도메인 작업에](application-proxy-configure-custom-domain.md)대해 자세히 알아보기

2. 응용 프로그램에 대한 **사전 인증** 방법으로 Azure **Active Directory를** 선택합니다.

3. 응용 프로그램의 **외부 URL을** 복사합니다. SAML 구성을 완료하려면 이 URL이 필요합니다.

4. 테스트 계정을 사용하여 **외부 URL을** 사용하여 응용 프로그램을 열어 응용 프로그램 프록시가 올바르게 설정되어 있는지 확인합니다. 문제가 있는 경우 [응용 프로그램 프록시 문제 및 오류 메시지 해결을](application-proxy-troubleshoot.md)참조하십시오.

## <a name="update-the-saml-configuration"></a>SAML 구성 업데이트

1. Azure 포털에서 응용 프로그램이 계속 열려 있는 경우 **Single 사인온**을 선택합니다. 

2. **SAML을 사용하여 단일 사인온 설정** 페이지에서 **기본 SAML 구성** 제목으로 이동하여 **편집** 아이콘(연필)을 선택합니다. 응용 프로그램 프록시에서 구성한 **외부 URL이** **식별자,** **회신 URL**및 **로그아웃 URL** 필드에 채워져 있는지 확인합니다. 이러한 URL은 응용 프로그램 프록시가 올바르게 작동하려면 필요합니다. 

3. 응용 프로그램 프록시를 통해 인터넷에서 도메인에 연결할 수 있도록 이전에 구성된 **회신 URL을** 편집합니다. 예를 들어 **외부 URL이** `https://contosotravel-f128.msappproxy.net` 있고 원래 **회신 URL이** `https://contosotravel.com/acs`있는 경우 원래 **회신 URL을** `https://contosotravel-f128.msappproxy.net/acs`로 업데이트해야 합니다.

    ![기본 SAML 구성 데이터 입력](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 업데이트된 **회신 URL** 옆의 확인란을 선택하여 기본값으로 표시합니다.

   * 필요한 **회신 URL을** 기본값으로 표시한 후 내부 URL을 사용한 이전에 구성된 **회신 URL을** 삭제할 수도 있습니다.

   * SP 시작 흐름의 경우 백 엔드 응용 프로그램에서 인증 토큰을 받기 위한 올바른 **회신 URL** 또는 어설션 소비자 서비스 URL을 지정해야 합니다.

    > [!NOTE]
    > 백 엔드 응용 프로그램에서 **회신 URL이** 내부 URL이 될 것으로 예상되는 경우 [사용자 지정 도메인을](application-proxy-configure-custom-domain.md) 사용하여 내부 및 외부 URL이 일치하도록 하거나 사용자의 장치에 내 앱 보안 로그인 확장을 설치해야 합니다. 이 확장은 자동으로 해당 응용 프로그램 프록시 서비스로 리디렉션됩니다. 확장을 설치하려면 [내 앱 보안 로그인 확장을](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)참조하십시오.
    
## <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 앱을 테스트하려면 다음을 수행하십시오.

1. 브라우저를 열고 앱을 게시할 때 만든 **외부 URL로** 이동합니다. 
1. 앱에 할당한 테스트 계정으로 로그인합니다. 응용 프로그램을 로드하고 응용 프로그램에 SSO를 사용할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시에서 Single Sign-On을 제공하는 방법](application-proxy-single-sign-on.md)
- [애플리케이션 프록시 문제 해결](application-proxy-troubleshoot.md)
