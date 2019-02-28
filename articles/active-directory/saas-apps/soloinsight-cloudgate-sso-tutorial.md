---
title: '자습서: Soloinsight-CloudGate SSO와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Soloinsight-CloudGate SSO 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2447d7220abdcd53787cb18a89ae0e22c5e4150
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/26/2019
ms.locfileid: "56882776"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>자습서: Soloinsight-CloudGate SSO와 Azure Active Directory 통합

이 자습서에서는 Soloinsight-CloudGate SSO를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.
Soloinsight-CloudGate SSO를 Azure AD와 통합하면 다음과 같은 이점이 있습니다.

* Soloinsight-CloudGate SSO에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Soloinsight-CloudGate SSO에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

Soloinsight-CloudGate SSO와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* Soloinsight-CloudGate SSO Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Soloinsight-CloudGate SSO에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>갤러리에서 Soloinsight-CloudGate SSO 추가

Soloinsight-CloudGate SSO가 Azure AD로 통합되도록 구성하려면 Soloinsight-CloudGate SSO를 갤러리에서 관리형 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Soloinsight-CloudGate SSO를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **Soloinsight-CloudGate SSO**를 입력하고, 결과 패널에서 **Soloinsight-CloudGate SSO**를 선택한 다음, **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 Soloinsight-CloudGate SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 Soloinsight-CloudGate SSO에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 Soloinsight-CloudGate SSO의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Soloinsight-CloudGate SSO에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Soloinsight-CloudGate SSO Single Sign-On 구성](#configure-soloinsight-cloudgate-sso-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Soloinsight-CloudGate SSO 테스트 사용자 만들기](#create-soloinsight-cloudgate-sso-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Soloinsight-CloudGate SSO에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

Soloinsight-CloudGate SSO에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Soloinsight-CloudGate SSO** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![Soloinsight-CloudGate SSO 도메인 및 URL Single Sign-On 정보](common/sp-identifier.png)

    a. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.sigateway.com/login` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<SUBDOMAIN>.sigateway.com/process/sso` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 이 자습서 뒷부분의 **Soloinsight-CloudGate SSO Single Sign-On 구성** 섹션에 설명되어 있는 실제 로그온 URL 및 식별자로 업데이트합니다.

4. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Soloinsight-CloudGate SSO 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Soloinsight-CloudGate SSO Single Sign-On 구성

1. 기본 SAML을 구성할 때 Azure Portal에 붙여넣을 값을 얻으려면 자격 증명을 사용하여 CloudGate 웹 포털에 로그인한 다음, SSO 설정(**홈>관리>시스템 설정>일반**)에 액세스합니다.

    ![CloudGate SSO 설정](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **SAML 소비자 URL**

    * **Saml 소비자 URL** 및 **리디렉션 URL** 필드에 대해 제공되는 링크를 복사하여 각각 **식별자(엔터티 ID)** 및 **회신 URL** 필드의 Azure Portal **기본 SAML 구성** 섹션에 붙여넣습니다.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **SAML 서명 인증서**

    * Azure Portal SAML 서명 인증서 목록에서 다운로드한 인증서(Base64) 파일의 원본으로 이동하여 마우스 오른쪽 단추로 클릭합니다. 목록에서 **메모장++를 사용하여 편집** 옵션을 선택합니다. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * 인증서(Base64) 메모장++ 파일의 콘텐츠를 복사합니다.

        ![인증서 복사](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * 복사한 콘텐츠를 CloudGate 웹 포털 SSO 설정 **인증서** 필드에 붙여넣고 [저장] 단추를 클릭합니다.

        ![인증서 포털](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **기본 그룹**

    * CloudGate 웹 포털의 **기본 그룹** 옵션 드롭다운 목록에서 **비즈니스 관리자**를 선택합니다.

        ![기본 그룹](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **AD 식별자 및 로그인 URL**

    * Azure Portal **Soloinsight-CloudGate SSO 설정** 구성에서 복사한 **로그인 URL**은 CloudGate 웹 포털 SSO 설정 섹션에 입력할 것입니다. 

    * Azure Portal의 **로그인 URL** 링크를 CloudGate 웹 포털 **AD 로그인 URL** 필드에 붙여넣습니다.
     
    * Azure Portal의 **Azure AD 식별자** 링크를 CloudGate 웹 포털 **AD 식별자** 필드에 붙여넣습니다.

        ![Ad 로그인](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Soloinsight-CloudGate SSO에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션**, **Soloinsight-CloudGate SSO**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **Soloinsight-CloudGate SSO**를 선택합니다.

    ![애플리케이션 목록의 Soloinsight-CloudGate SSO 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션에서 역할 값이 필요한 경우 **역할 선택** 대화 상자에서 목록에서 사용자에 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO 테스트 사용자 만들기

테스트 사용자를 만들려면 CloudGate 웹 포털의 주 메뉴에서 **직원**을 선택하고 [새 직원 추가] 양식을 작성합니다. 테스트 사용자에게 할당할 권한 수준은 **비즈니스 관리자**입니다. 필수 필드를 모두 채웠으면 **만들기**를 클릭합니다.

![직원 테스트](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Single Sign-On 테스트 

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Soloinsight-CloudGate SSO 타일을 클릭하면 SSO를 설정한 Soloinsight-CloudGate SSO에 자동으로 로그인되어야 합니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

