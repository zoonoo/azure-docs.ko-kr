---
title: '자습서: Citrix Netscaler와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Citrix Netscaler 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 6d434295a6a46ee5b7089608cbf788ff91589fb7
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281678"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-netscaler"></a>자습서: Citrix Netscaler와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Citrix Netscaler를 통합하는 방법에 대해 알아봅니다.
Citrix Netscaler를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Azure AD에서 Citrix Netscaler에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Citrix Netscaler에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Citrix Netscaler와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Citrix Netscaler Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Citrix Netscaler에서 **SP** 시작 SSO를 지원합니다.

* Citrix Netscaler에서 **Just-In-Time** 사용자 프로비전을 지원합니다.

## <a name="adding-citrix-netscaler-from-the-gallery"></a>갤러리에서 Citrix Netscaler 추가

Citrix Netscaler가 Azure AD에 통합되도록 구성하려면 갤러리의 Citrix Netscaler를 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Citrix Netscaler를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에서 **Citrix Netscaler**를 입력하고, 결과 패널에서 **Citrix Netscaler**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Citrix Netscaler](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 Citrix Netscaler에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Citrix Netscaler의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Citrix Netscaler에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Citrix Netscaler Single Sign-On 구성](#configure-citrix-netscaler-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Citrix Netscaler 테스트 사용자 만들기](#create-citrix-netscaler-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Citrix Netscaler에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Citrix Netscaler에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Citrix Netscaler** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Citrix Netscaler 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    a. **로그온 URL** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    b. **식별자(엔터티 ID)** 텍스트 상자에 다음 패턴을 사용하여 URL을 입력합니다. `https://<<Your FQDN>>`

    다. **Reply URL(Assertion Consumer Service URL)** 텍스트 상자에 다음 패턴을 사용하는 URL을 입력합니다. `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`
    
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Citrix Netscaler 클라이언트 지원 팀](https://www.citrix.com/contact/technical-support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

    > [!NOTE]
    > SSO를 작동하려면 공용 사이트에서 이러한 URL에 액세스할 수 있어야 합니다. Azure AD에서 토큰을 구성된 ACS URL에 게시할 수 있도록 하려면 Netscaler 쪽에서 방화벽 또는 다른 보안 설정을 사용하도록 설정해야 합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML**을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

6. **Citrix Netscaler 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-citrix-netscaler-single-sign-on"></a>Citrix Netscaler Single Sign-On 구성

1. 다른 웹 브라우저 창에서 Citrix Netscaler 테넌트에 관리자 권한으로 로그인합니다.

2. **NetScaler 펌웨어 버전 = NS12.1: 빌드 48.13.nc**인지 확인합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure01.png)

3. **VPN Virtual Server**(VPN 가상 서버) 페이지에서 다음 단계를 수행합니다.

     ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure02.png)

    a. **ICA Only**(ICA만) 게이트웨이 설정을 **true**로 설정합니다.
    
    b. **Enable Authentication**(인증 사용)을 **true**로 설정합니다.
    
    다. **DTLS**는 선택 사항입니다.
    
    d. **SSLv3**이 **Disabled**(사용 안 함)인지 확인합니다.

4. https://www.ssllabs.com에서 A+를 얻을 수 있도록 아래와 같이 사용자 지정된 **SSL Ciphers**(SSL 암호) 그룹이 만들어집니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure03.png)

5. **Configure Authentication SAML Server**(인증 SAML 서버 구성) 대화 상자에서 다음 단계를 수행합니다.

      ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure04.png)

    a. **Name**(이름) 텍스트 상자에서 서버의 이름을 입력합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **Redirect URL**(리디렉션 URL) 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그아웃 URL** 값을 **Single Logout URL**(단일 로그아웃 URL) 텍스트 상자에 붙여넣습니다.

    d. **IDP Certificate Name**(IDP 인증서 이름)에서 **"+"** 기호를 클릭하여 Azure Portal에서 다운로드한 인증서를 추가합니다. 업로드가 완료되면 드롭다운에서 해당 인증서를 선택합니다.

    e. 다음 페이지에서 더 많은 필드를 설정해야 합니다.

      ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure24.png)

    f. **Requested Authentication Context**(요청된 인증 컨텍스트)를 **Exact**(정확하게 일치)로 선택합니다.

    g. **Signature Algorithm**(서명 알고리즘)을 **RSA-SHA256**으로 선택합니다.

    h. **Digest Method**(다이제스트 방법)를 **SHA256**으로 선택합니다.

    i. **Enforce Username**(사용자 적용)을 선택합니다.

    j. **확인**

6. **세션 프로필**을 구성하려면 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure06.png)

    a. **이름**(Name) 텍스트 상자에서 세션 프로필의 이름을 입력합니다.

    b. **Client Experience**(클라이언트 환경) 탭에서 아래 스크린샷과 같이 변경합니다.

    다. 아래와 같이 **General(일반) 탭**에서 계속 변경하고 **확인**을 클릭합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure07.png)

    d. **Published Applications**(게시된 애플리케이션) 탭에서 아래 스크린샷에 표시된 대로 변경하고 **확인**을 클릭합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure08.png)

    e. **Security**(보안) 탭에서 아래 스크린샷과 같이 변경하고 **확인**을 클릭합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure09.png)

7. 아래 스크린샷과 같이 ICA Connections(ICA 연결)를 **2598** 세션 안정성 포트에 연결합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure10.png)

8. 아래 스크린샷과 같이 **SAML** 섹션에서 **Servers**(서버)를 추가합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure11.png)

9. 아래 스크린샷과 같이 **SAML** 섹션에서 **Policies**(정책)를 추가합니다.

     ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure12.png)

10. **Global Settings**(글로벌 설정) 페이지에서 **Clientless Access**(클라이언트리스 액세스) 섹션으로 이동합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure13.png)

11. **Configuration**(구성) 탭에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure14.png)

    a. **Allow Domains**(도메인 허용)를 선택합니다.

    b. **Domain Name**(도메인 이름) 텍스트 상자에서 도메인을 선택합니다.

    다. **확인**을 클릭합니다.

12. 아래 스크린샷과 같이 **Receiver for Web Sites**(웹 사이트용 수신기)에서 **StoreFront**를 설정합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure15.png)

13. **Manage Authentication Methods - Corp**(인증 방법 관리 - 회사) 팝업에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure16.png)

    a. **사용자 이름 및 암호**를 선택합니다.

    b. **Pass-through from NetScaler Gateway**(NetScaler Gateway에서 통과)를 선택합니다.

    다. **확인**을 클릭합니다.

14. **Configure Trusted Domains**(신뢰할 수 있는 도메인 구성) 팝업에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure17.png)

    a. **Trusted domains only**(신뢰할 수 있는 도메인만)를 선택합니다.

    b. **Add**(추가)를 클릭하여 **Trusted domains**(신뢰할 수 있는 도메인) 텍스트 상자에서 도메인을 추가합니다.

    다. **Default domain**(기본 도메인) 목록에서 기본 도메인을 선택합니다.

    d. **Show domains list in logon page**(로그온 페이지에서 도메인 목록 표시)를 선택합니다.

    e. **확인**을 클릭합니다.

15. **Manage NetScaler Gateways**(NetScaler 게이트웨이 관리) 팝업에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure18.png)

    a. **Add**(추가)를 클릭하여 **NetScaler Gateways**(NetScaler 게이트웨이) 텍스트 상자에서 NetScaler 게이트웨이를 추가합니다.

    b. **닫기**를 클릭합니다.

16. **StoreFront General Settings**(StoreFront 일반 설정) 탭에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure19.png)

    a. **Display name**(표시 이름) 텍스트 상자에서 NetScaler 게이트웨이 이름을 입력합니다.

    b. **NetScaler Gateway URL**(NetScaler 게이트웨이 URL) 텍스트 상자에서 NetScaler 게이트웨이 URL을 입력합니다.

    다. **Usage or role**(사용량 또는 역할)을 **Authentication and HDX routing**(인증 및 HDX 라우팅)으로 선택합니다.

    d. **확인**을 클릭합니다.

17. **StoreFront Secure Ticket Authority**(StoreFront 보안 티켓 인증 기관) 탭에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure20.png)

    a. **Add**(추가) 단추를 클릭하여 텍스트 상자에 **Secure Ticket Authority URL's**(보안 티켓 인증 기관 URL)를 추가합니다.

    b. **Enable session reliability**(세션 안정성 사용)를 선택합니다.

    다. **확인**을 클릭합니다.

18. **StoreFront Authentication Settings**(StoreFront 인증 설정) 탭에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure21.png)

    a. **Version**(버전)을 선택합니다.

    b. **Logon type**(로그온 유형)을 **Domain**(도메인)으로 선택합니다.

    다. **Callback URL**(콜백 URL)을 입력합니다.

    d. **확인**을 클릭합니다.

19. **StoreFront Deploy Citrix Receiver**(StoreFront 배포 Citrix 수신기) 탭에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure22.png)

    a. **배포 옵션**을 **Use Receiver for HTML5 if local Receiver is unavailable**(로컬 수신기를 사용할 수 없는 경우 HTML5용 수신기 사용)로 선택합니다.

    b. **확인**을 클릭합니다.

20. **Manage Beacons**(탐지 장치 관리) 팝업에서 다음 단계를 수행합니다.

    ![Configure Single Sign-On](./media/citrix-netscaler-tutorial/configure23.png)

    a. **Internal beacon**(내부 탐지 장치)을 **Use the service URL**(서비스 URL 사용)로 선택합니다.

    b. **Add**(추가)를 클릭하여 **External beacons**(외부 탐지 장치) 텍스트 상자에서 URL을 추가합니다.

    다. **확인**을 클릭합니다.

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기 

이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**를 차례로 선택하고 **모든 사용자**를 선택합니다.

    !["사용자 및 그룹" 및 "모든 사용자" 링크](common/users.png)

2. 화면 위쪽에서 **새 사용자**를 선택합니다.

    ![새 사용자 단추](common/new-user.png)

3. 사용자 속성에서 다음 단계를 수행합니다.

    ![사용자 대화 상자](common/user-properties.png)

    a. **이름** 필드에 **BrittaSimon**을 입력합니다.
  
    b. **사용자 이름** 필드에 **brittasimon@yourcompanydomain.extension**을 입력합니다.  
    예를 들어 BrittaSimon@contoso.com

    c. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Citrix Netscaler에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Citrix Netscaler**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Citrix Netscaler**를 선택합니다.

    ![애플리케이션 목록의 Citrix Netscaler 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-citrix-netscaler-test-user"></a>Citrix Netscaler 테스트 사용자 만들기

이 섹션에서는 Citrix Netscaler에서 Britta Simon이라는 사용자를 만듭니다. Citrix Netscaler는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Citrix Netscaler에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

>[!NOTE]
>사용자를 수동으로 만들어야 하는 경우 [Citrix Netscaler 클라이언트 지원 팀](https://www.citrix.com/contact/technical-support.html)에 문의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Citrix Netscaler 타일을 클릭하면 SSO를 설정한 Citrix Netscaler에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On이란 무엇입니까?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

