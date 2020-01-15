---
title: '자습서: Citrix NetScaler와 Azure Active Directory SSO(Single Sign-On) 통합(Kerberos 기반 인증) | Microsoft Docs'
description: Azure Active Directory 및 Citrix NetScaler 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431184"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>자습서: Citrix NetScaler와 Azure Active Directory SSO(Single Sign-On) 통합(Kerberos 기반 인증)

이 자습서에서는 Azure AD(Azure Active Directory)와 Citrix NetScaler를 통합하는 방법에 대해 알아봅니다. Azure AD와 Citrix NetScaler를 통합하면 다음을 수행할 수 있습니다.

* Citrix NetScaler에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Citrix NetScaler에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Citrix NetScaler SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Citrix NetScaler에서 **SP** 시작 SSO를 지원합니다.

* Citrix NetScaler에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

- [Kerberos 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [헤더 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>갤러리에서 Citrix NetScaler 추가

Citrix NetScaler가 Azure AD에 통합되도록 구성하려면 갤러리의 Citrix NetScaler를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션**으로 이동한 다음, **모든 애플리케이션**을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션**을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Citrix NetScaler**를 입력합니다.
1. 결과 패널에서 **Citrix NetScaler**를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Citrix NetScaler에 대한 Azure AD Single Sign-On 구성 및 테스트

**B.Simon**이라는 테스트 사용자를 사용하여 Citrix NetScaler에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Citrix NetScaler의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Citrix NetScaler에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Citrix NetScaler SSO 구성](#configure-citrix-netscaler-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Citrix NetScaler 테스트 사용자 만들기](#create-citrix-netscaler-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Citrix NetScaler에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Citrix NetScaler** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On**을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML**을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성**에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<<Your FQDN>>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정**를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [Citrix NetScaler 클라이언트 지원 팀](https://www.citrix.com/contact/technical-support.html)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

    > [!NOTE]
    > SSO를 작동하려면 공용 사이트에서 이러한 URL에 액세스할 수 있어야 합니다. Azure AD에서 토큰을 구성된 ACS URL에 게시할 수 있도록 하려면 Netscaler 쪽에서 방화벽 또는 다른 보안 설정을 사용하도록 설정해야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **앱 페더레이션 메타데이터 URL**을 찾아서 URL을 복사한 후 메모장에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Citrix NetScaler 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Citrix NetScaler에 대한 액세스 권한을 부여하여 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**을 선택한 다음, **모든 애플리케이션**을 선택합니다.
1. 애플리케이션 목록에서 **Citrix NetScaler**를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹**을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가**를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon**을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-citrix-netscaler-sso"></a>Citrix NetScaler SSO 구성

- [Kerberos 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [헤더 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>웹 서버 게시 

1. **가상 서버**를 만듭니다.

    a. **트래픽 관리 > 부하 분산 > 서비스**로 이동합니다.
    
    b. **추가**를 클릭합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/web01.png)

    다. 아래 애플리케이션을 실행하는 웹 서버의 세부 정보를 지정합니다.
    * **서비스 이름**
    * **서버 IP/기존 서버**
    * **프로토콜**
    * **포트**

     ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Load Balancer 구성

1. Load Balancer를 구성하려면 다음 단계를 수행합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/load01.png)

    a. **트래픽 관리 > 부하 분산 > 가상 서버**로 이동합니다.

    b. **추가**를 클릭합니다.

    다. 아래 세부 정보를 지정합니다.

    * **이름**
    * **프로토콜**
    * **IP 주소**
    * **포트**
    * **확인**을 클릭합니다.

### <a name="bind-virtual-server"></a>가상 서버 바인딩

Load Balancer를 이전에 만든 가상 서버에 바인딩합니다.

![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>인증서 바인딩

이 서비스를 SSL로 게시할 예정이므로 서버 인증서를 바인딩한 다음, 애플리케이션을 테스트합니다.

![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML 프로필

### <a name="create-authentication-policy"></a>인증 정책 만들기

1. **보안 > AAA – 애플리케이션 트래픽 > 정책 > 인증 > 인증 정책**으로 이동합니다.

2. **추가**를 클릭한 다음, 세부 정보를 지정합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/policy01.png)

    a. **인증 정책**의 이름

    b. 식: **true**

    c. 동작 유형 **SAML**

    d. 동작 = **추가** 클릭(인증 SAML 서버 생성 마법사의 단계 수행)
    
    e. **인증 정책**에서 만들기를 클릭합니다.

### <a name="create-authentication-saml-server"></a>인증 SAML 서버 만들기

1. 다음 단계를 수행합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/server01.png)

    a. **이름**을 지정합니다.

    b. 메타데이터를 가져옵니다(위에서 복사한 Azure SAML UI에서 페더레이션 메타데이터 URL을 지정).
    
    다. **발급자 이름**을 지정합니다.

    d. **만들기**를 클릭합니다.

### <a name="create-authentication-virtual-server"></a>인증 가상 서버 만들기

1.  **보안 > AAA - 애플리케이션 트래픽 >> 인증 가상 서버**로 이동합니다.

2.  **추가**를 클릭하고 다음 단계를 수행합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/server02.png)

    a.  **이름**을 제공합니다.

    b.  **Non-Addressable**(주소 지정 불가)을 선택합니다.

    다.  프로토콜에 **SSL**을 입력합니다.

    d.  **확인**을 클릭합니다.

    e.  **계속**을 클릭합니다.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Azure AD를 사용하도록 인증 가상 서버 구성

인증 가상 서버의 2개 섹션을 수정해야 합니다.

1.  **고급 인증 정책**

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/virtual01.png)

    a. 이전에 만든 **인증 정책**을 선택합니다.

    b. **바인딩**을 클릭합니다.

      ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/virtual02.png)

2. **양식 기반 가상 서버**

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  UI에 의해 적용되므로 **FQDN**을 제공해야 합니다.

    b.  Azure AD 인증을 통해 보호할 **Virtual Server Load Balancer**를 선택합니다.

    다.  **바인딩**을 클릭합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >인증 가상 서버 구성 페이지에서도 **완료**를 클릭해야 합니다.

3. 변경 내용을 확인합니다. 애플리케이션 URL로 이동합니다. 이전에 인증되지 않은 액세스 대신 테넌트 로그인 페이지가 표시됩니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Kerberos 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Citrix ADC에 대한 Kerberos 위임 계정 만들기

1. 사용자 계정(이 예제의 경우, AppDelegation)을 만듭니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos01.png)

2. 이 계정에 대한 HOST SPN을 설정합니다.

    * setspn -S HOST/AppDelegation.IDENTT.WORK identt\appdelegation
    
        위의 예제에서

        a. Identt.work    (도메인 FQDN)

        b. Identt        (도메인 NetBIOS 이름)

        다. AppDelegation(위임 사용자 계정 이름)

3. 웹 서버에 대한 위임 구성 
 
    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >위의 예에서 WIA 사이트를 실행하는 내부 웹 서버 이름은 cweb2입니다.

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA KCD(Kerberos 위임 계정)

1.  **Citrix 게이트웨이 > AAA KCD(Kerberos 제한된 위임) 계정**으로 이동합니다.

2.  추가를 클릭하고 아래 세부 정보를 지정합니다.

    a.  **이름** 지정

    b.  **영역**

    c.  **서비스 SPN**  `http/<host/fqdn>@DOMAIN.COM`.
    
    >[!NOTE]
    >@DOMAIN.com은 필수이며 대문자입니다.

    d.  **위임된 사용자 계정**을 지정합니다.

    e.  위임된 사용자의 암호를 확인하고 **암호**를 지정합니다.

    f.  **확인**을 클릭합니다.
 
    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix 트래픽 정책 및 트래픽 프로필

1.  **보안 > AAA - 애플리케이션 트래픽 > 정책 > 트래픽 정책, 프로필 및 양식 SSO 프로필 트래픽 정책**으로 이동합니다.

2.  **트래픽 프로필**을 선택합니다.

3.  **추가**를 클릭합니다.

4.  트래픽 프로필을 구성합니다.

    a.  **이름** 지정

    b.  **Single Sign-On**을 지정합니다.

    다.  드롭다운에서 이전 단계에서 만든 **KCD 계정**을 지정합니다.

    d.  **확인**을 클릭합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  **트래픽 정책**을 선택합니다.

6.  **추가**를 클릭합니다.

7.  트래픽 정책을 구성합니다.

    a.  **이름** 지정

    b.  드롭다운에서 이전에 만든 **트래픽 프로필**을 선택합니다.

    다.  식을 **true**로 설정합니다.

    d.  **Ok**를 클릭합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>가상 서버에 대한 Citrix 바인딩 트래픽 정책

GUI를 사용하여 특정 가상 서버에 트래픽 정책을 바인딩하려면,

* **트래픽 관리 > 부하 분산 > 가상 서버**로 이동합니다.

* 가상 서버의 세부 정보 창 목록에서 재작성 정책을 바인딩할 **가상 서버**를 선택한 다음, **열기**를 클릭합니다.

* 가상 서버 구성(부하 분산) 대화 상자에서 **정책 탭**을 선택합니다. NetScaler에 구성된 모든 정책이 목록에 나타납니다.
 
    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  이 가상 서버에 바인딩할 정책 이름 옆의 **확인란**을 선택합니다.
 
    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos09.png)

1. 해당 정책만 바인딩 되고, **완료**를 클릭합니다.
 
    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Windows 통합 웹 사이트를 사용하여 테스트합니다.

    ![Citrix NetScaler 구성](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Citrix NetScaler 테스트 사용자 만들기

이 섹션에서는 B.Simon이라는 사용자가 Citrix NetScaler에서 생성됩니다. Citrix NetScaler는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Citrix NetScaler에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> 사용자를 수동으로 만들어야 하는 경우 [Citrix NetScaler 클라이언트 지원 팀](https://www.citrix.com/contact/technical-support.html)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Citrix NetScaler 타일을 클릭하면 SSO를 설정한 Citrix NetScaler에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Azure AD를 통해 Citrix NetScaler 사용해보기](https://aad.portal.azure.com/)

- [헤더 기반 인증을 위한 Citrix NetScaler Single Sign-On 구성](header-citrix-netscaler-tutorial.md)
