---
title: '자습서: SuccessFactors와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 SuccessFactors 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.openlocfilehash: 465bdf3aaa8ab6fecddef6784bf1adead7f0c585
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/23/2019
ms.locfileid: "54822215"
---
# <a name="tutorial-azure-active-directory-integration-with-successfactors"></a>자습서: SuccessFactors와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SuccessFactors를 통합하는 방법에 대해 알아봅니다.
SuccessFactors를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* SuccessFactors에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 SuccessFactors에 자동으로 로그인(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)을 참조하세요.
Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

## <a name="prerequisites"></a>필수 조건

SuccessFactors와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SuccessFactors Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SuccessFactors에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-successfactors-from-the-gallery"></a>갤러리에서 SuccessFactors 추가

SuccessFactors의 Azure AD 통합을 구성하려면 갤러리의 SuccessFactors를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 SuccessFactors를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)** 의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure Active Directory 단추](common/select-azuread.png)

2. **엔터프라이즈 응용 프로그램**으로 이동한 다음, **모든 응용 프로그램** 옵션을 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

3. 새 애플리케이션을 추가하려면 대화 상자 맨 위 있는 **새 애플리케이션** 단추를 클릭합니다.

    ![새 애플리케이션 단추](common/add-new-app.png)

4. 검색 상자에 **SuccessFactors**를 입력하고 결과 패널에서 **SuccessFactors**를 선택한 후 **추가** 단추를 클릭하여 애플리케이션을 추가합니다.

     ![결과 목록의 SuccessFactors](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

이 섹션에서는 **Britta Simon**이라는 테스트 사용자를 기반으로 하여 SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트합니다.
Single Sign-On이 작동하려면 Azure AD 사용자와 SuccessFactors의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SuccessFactors에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configure-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[SuccessFactors Single Sign-On 구성](#configure-successfactors-single-sign-on)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[SuccessFactors 테스트 사용자 만들기](#create-successfactors-test-user)** - Azure AD 표현과 연결된 Britta Simon에 해당하는 사용자를 SuccessFactors에 만듭니다.
6. **[Single Sign-On 테스트](#test-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정합니다.

SuccessFactors에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **SuccessFactors** 애플리케이션 통합 페이지에서 **Single Sign-On**을 선택합니다.

    ![Single Sign-On 구성 링크](common/select-sso.png)

2. **Single Sign-On 방법 선택** 대화 상자에서 **SAML/WS-Fed** 모드를 선택하여 Single Sign-On을 사용하도록 설정합니다.

    ![Single Sign-On 선택 모드](common/select-saml-option.png)

3. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 아이콘을 클릭하여 **기본 SAML 구성** 대화 상자를 엽니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![SuccessFactors 도메인 및 URL Single Sign-On 정보](common/sp-identifier-reply.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다.
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. **식별자** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.
    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트합니다. 이러한 값을 얻으려면 [SuccessFactors 클라이언트 지원 팀](https://www.successfactors.com/content/ssf-site/en/support.html)에 문의하세요.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드**를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **SuccessFactors 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

    a. 로그인 URL

    b. Azure AD 식별자

    다. 로그아웃 URL

### <a name="configure-successfactors-single-sign-on"></a>SuccessFactors Single Sign-On 구성

1. 다른 웹 브라우저 창에서 **SuccessFactors 관리 포털**에 관리자로 로그인합니다.

2. **애플리케이션 보안**을 방문하고 **Single Sign On 기능**으로 이동합니다.

3. **토큰 재설정**에 값을 입력하고 **토큰 저장**을 클릭하여 SAML SSO를 사용하도록 설정합니다.

    ![앱 쪽에서 Single Sign-On 구성][11]

    > [!NOTE]
    > 이 값은 설정/해제 스위치로 사용됩니다. 값이 저장된 경우 SAML SSO는 ON입니다. 빈 값이 저장된 경우 SAML SSO는 OFF입니다.

4. 아래 스크린샷으로 이동하고 다음 작업을 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성][12]
  
    a. **SAML v2 SSO** 라디오 단추를 선택합니다.
  
    b. **SAML 어설션 파티 이름**(예: SAML 발급자 + 회사 이름)을 설정합니다.

    다. **발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    d. **필수 서명 요구**로 **어설션**을 선택합니다.

    e. **SAML 플래그 사용**으로 **사용**을 선택합니다.

    f. **로그인 요청 서명(SF 생성/SP/RP)** 으로 **아니요**를 선택합니다.

    g. **SAML 프로필**로 **브라우저/게시 프로필**을 선택합니다.

    h. **인증서 유효 기간 적용**으로 **아니요**를 선택합니다.

    i. Azure Portal에서 다운로드한 인증서 파일의 내용을 복사한 다음 **SAML 확인 인증서** 텍스트 상자에 붙여넣습니다.

    > [!NOTE] 
    > 인증서 내용에는 시작 인증서 및 끝 인증서 태그가 있어야 합니다.

5. SAML V2로 이동한 후 다음 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성][13]

    a. **SP 시작 전역 로그아웃 지원**으로 **예**를 선택합니다.

    b. **전역 로그아웃 서비스 URL(LogoutRequest 대상)** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    다. **요청 SP는 모든 NameID 요소를 암호화해야 합니다.** 로 **아니요**를 선택합니다.

    d. **NameID 형식**으로 **지정되지 않음**을 선택합니다.

    e. **SP 시작 로그인 사용(AuthnRequest)** 으로 **예**를 선택합니다.

    f. Azure Portal에서 복사한 **로그인 URL** 값을 **전사적 발급자로 요청 보내기** 텍스트 상자에 붙여넣습니다.

6. 로그인 사용자 이름의 대/소문자를 구분하지 않으려면 이 단계를 수행합니다.

    ![Configure Single Sign-On][29]

    a. a. **회사 설정**(아래쪽)을 방문합니다.

    b. **대/소문자를 구분하지 않는 사용자 이름 사용** 근처의 확인란을 선택합니다.

    다. **저장**을 클릭합니다.

    > [!NOTE]
    > 이 기능을 사용하려고 하면 시스템에서 중복되는 SAML 로그인 이름이 만들어지는지 확인합니다. 예를 들어 고객의 사용자 이름에 User1 및 user1이 있는 경우입니다. 대/소문자를 구분하지 않으면 이러한 중복 항목을 만듭니다. 시스템에서 오류 메시지를 제공하고 기능이 비활성화됩니다. 다른 철자가 되도록 고객은 사용자 이름 중 하나를 변경해야 합니다.

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

    다. **암호 표시** 확인란을 선택한 다음, [암호] 상자에 표시된 값을 적어둡니다.

    d. **만들기**를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 SuccessFactors에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 및 **SuccessFactors**를 차례로 선택합니다.

    ![엔터프라이즈 애플리케이션 블레이드](common/enterprise-applications.png)

2. 애플리케이션 목록에서 **SuccessFactors**를 선택합니다.

    ![애플리케이션 목록의 SuccessFactors 링크](common/all-applications.png)

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 선택합니다.

    !["사용자 및 그룹" 링크](common/users-groups-blade.png)

4. **사용자 추가** 단추를 클릭한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![할당 추가 창](common/add-assign-user.png)

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택하고 화면 아래쪽에서 **선택** 단추를 클릭합니다.

6. SAML 어설션 및 **역할 선택** 대화 상자에서 모든 역할 값이 필요한 경우 목록에서 적절한 사용자 역할을 선택한 다음, 화면 맨 아래에 있는 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-successfactors-test-user"></a>SuccessFactors 테스트 사용자 만들기

Azure AD 사용자가 SuccessFactors에 로그인할 수 있도록 하려면 SuccessFactors로 프로비전되어야 합니다. SuccessFactors의 경우 프로비전은 수동 작업입니다.

SuccessFactors에서 사용자를 생성하려면 [SuccessFactors 지원 팀](https://www.successfactors.com/content/ssf-site/en/support.html)에 문의해야 합니다.

### <a name="test-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 SuccessFactors 타일을 클릭하면 SSO를 설정한 SuccessFactors에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS 앱을 통합하는 방법에 대한 자습서 목록](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Azure Active Directory의 조건부 액세스란?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png