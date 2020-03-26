---
title: '자습서: Akamai와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Akamai 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979139"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>자습서: Akamai와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Akamai를 통합하는 방법에 대해 알아봅니다. Azure AD와 Akamai를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Akamai에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Akamai에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Akamai SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Slack에서 IDP 시작 SSO를 지원합니다.

## <a name="adding-akamai-from-the-gallery"></a>갤러리에서 Akamai 추가

Akamai가 Azure AD에 통합되도록 구성하려면 갤러리의 Akamai를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Akamai**를 입력합니다.
1. 결과 패널에서 **Akamai**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Akamai에 대한 Azure AD Single Sign-On 구성 및 테스트

**B. Simon**이라는 테스트 사용자를 사용하여 Akamai에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Akamai의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Akamai에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    * **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    * **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Akamai SSO 구성](#configure-akamai-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * **[Akamai 테스트 사용자 만들기](#create-akamai-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Akamai에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Akamai** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<Yourapp>.login.go.akamai-access.com/sp/response` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https:// <Yourapp>.login.go.akamai-access.com/sp/response` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Akamai 클라이언트 지원 팀](https://www.akamai.com/us/en/contact-us/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML**을 찾고, **다운로드**를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Akamai 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자**를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자**를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Akamai에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Akamai**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-akamai-sso"></a>Akamai SSO 구성

### <a name="setting-up-idp"></a>IDP 설정

1. **Akamai Enterprise Application Access**(Akamai 엔터프라이즈 애플리케이션 액세스) 콘솔에 로그인합니다.
1. **Akamai EAA 콘솔**에서 **ID** > **Identity Providers**(ID 공급자)를 차례로 선택합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure01.png)

1. **Add Identity Provider**(ID 공급자 추가)를 클릭합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure02.png)

    a. **Unique Name**(고유 이름)을 지정합니다.
    
    b. **Third Party SAML**(타사 SAML)을 선택하고, **Create Identity Provider and Configure**(ID 공급자 만들기 및 구성)를 클릭합니다.

### <a name="general-settings"></a>General Settings(일반 설정)

1. **Identity Intercept**(ID 가로채기) - Azure AD 구성에 사용되는 SP 기본 URL의 이름을 지정합니다.

    > [!NOTE]
    > 고유한 사용자 지정 도메인을 사용하도록 선택할 수 있습니다(DNS 항목과 인증서가 필요함). 이 예에서는 Akamai 도메인을 사용하고 있습니다.

1. **Akamai Cloud Zone**(Akamai 클라우드 영역) - 적절한 클라우드 영역을 선택합니다.
1. **Certificate Validation**(인증서 유효성 검사) - Akamai 설명서를 확인합니다(선택 사항).

    ![Akamai 구성](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Authentication Configuration(인증 구성)

1. URL – ID 가로채기와 동일한 URL(인증 후 사용자가 리디렉션되는 위치임)을 지정합니다.
2. Logout URL(로그아웃 URL): 로그아웃 URL을 업데이트합니다.
3. Sign SAML Request(SAML 요청 서명): 기본적으로 선택 취소되어 있습니다.
4. IDP 메타데이터 파일의 경우 Azure AD 콘솔에서 애플리케이션을 추가합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>헤더 기반 인증

Akamai 헤더 기반 인증

1. Add Applications(애플리케이션 추가) 마법사에서 **Custom HTTP**(사용자 지정 HTTP)를 선택합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure05.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure06.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure07.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>인증

![Akamai 구성](./media/header-akamai-tutorial/configure09.png)

![Akamai 구성](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Services

1. [Save and Go to Authentication]\(저장 후 인증으로 이동)을 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>고급 설정

1. **Customer HTTP Headers**(고객 HTTP 헤더)에서 **CustomerHeader** 및 **SAML Attribute**(SAML 특성)를 지정합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure12.png)

1. **Save and go to Deployment**(저장 후 배포로 이동) 단추를 클릭합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>애플리케이션 배포

1. **Deploy Application**(애플리케이션 배포) 단추를 클릭합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure14.png)

1. 애플리케이션이 성공적으로 배포되었는지 확인합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos 인증

#### <a name="remote-desktop"></a>원격 데스크톱

1. ADD Applications(애플리케이션 추가) 마법사에서 **RDP**를 선택합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure16.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure17.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure18.png)

1. 이 서비스를 제공할 커넥터를 지정합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>인증

**Save and go to Services**(저장 후 서비스로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Services

**Save and go to Advanced Settings**(저장 후 서비스로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>고급 설정

**Save and go to Deployment**(저장 후 배포로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure22.png)

![Akamai 구성](./media/header-akamai-tutorial/configure23.png)

![Akamai 구성](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>배포

#### <a name="ssh"></a>SSH

1. [Add Applications]\(애플리케이션 추가)로 이동하여 **SSH**를 선택합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure25.png)

    ![Akamai 구성](./media/header-akamai-tutorial/configure26.png)

1. [Application Identity]\(애플리케이션 ID)를 구성합니다.

    ![Akamai 구성](./media/header-akamai-tutorial/configure27.png)

    a. [Name/Description]\(이름/설명)을 지정합니다.

    b. SSH에 대한 [Application Server IP/FQDN]\(애플리케이션 서버 IP/FQDN) 및 포트를 지정합니다.

    다. SSH 사용자 이름/암호를 지정합니다. *Akamai EAA를 확인하세요.

    d. [External host Name]\(외부 호스트 이름)을 지정합니다.

    e. 커넥터의 [Location]\(위치)을 지정하고, 커넥터를 선택합니다.

#### <a name="authentication"></a>인증

**Save and go to Services**(저장 후 서비스로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Services

**Save and go to Advanced Settings**(저장 후 서비스로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>고급 설정

[Save and go to Deployment]\(저장 후 배포로 이동)를 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure30.png)

![Akamai 구성](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>배포

**Deploy Application**(애플리케이션 배포)을 클릭합니다.

![Akamai 구성](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos 애플리케이션

#### <a name="adding-directory"></a>디렉터리 추가

![Akamai 구성](./media/header-akamai-tutorial/configure33.png)

![Akamai 구성](./media/header-akamai-tutorial/configure34.png)

![Akamai 구성](./media/header-akamai-tutorial/configure35.png)

![Akamai 구성](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Akamai 테스트 사용자 만들기

이 섹션에서는 Akamai에서 B.Simon이라는 사용자를 만듭니다.  [Akamai 클라이언트 지원 팀](https://www.akamai.com/us/en/contact-us/)과 협력하여 사용자를 Akamai 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다. 

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Akamai 타일을 클릭하면 SSO를 설정한 Akamai에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD에서 Akamai 사용해보기](https://aad.portal.azure.com/)
