---
title: "자습서: JIRA SAML SSO by Microsoft와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 JIRA SAML SSO by Microsoft 사이에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 0dc847b9-eec4-4c31-845e-0144ddedc4a7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: b5f7813c8244d2964b6894ae49cd64e0ee71b704
ms.contentlocale: ko-kr
ms.lasthandoff: 07/21/2017

---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft"></a>자습서: JIRA SAML SSO by Microsoft와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 JIRA SAML SSO by Microsoft를 통합하는 방법에 대해 알아봅니다.

Azure AD와 JIRA SAML SSO by Microsoft를 통합하면 다음과 같은 이점이 있습니다.

- JIRA SAML SSO by Microsoft에 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 JIRA SAML SSO by Microsoft에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

JIRA SAML SSO by Microsoft와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- JIRA 서버 응용 프로그램이 Windows 64비트 서버(온-프레미스 또는 클라우드 IaaS 인프라)에 설치되어 있어야 합니다.
- JIRA 서버에서 HTTPS를 사용해야 합니다.
- 지원되는 JIRA 플러그 인 버전은 아래 섹션에 설명되어 있습니다.
- JIRA 서버가 인터넷에 연결되어 있고 인증을 위해 특히 Azure AD 로그인 페이지에 접속되고 Azure AD에서 토큰을 받을 수 있어야 합니다.
- JIRA에 관리자 자격 증명이 설정되어 있어야 합니다.
- JIRA에서 WebSudo를 사용하지 않아야 합니다.
- JIRA 서버 응용 프로그램에서 생성된 테스트 사용자

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 JIRA의 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 응용 프로그램 개발 또는 스테이징 환경에서 통합을 먼저 테스트 한 다음 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [평가판 제품](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="supported-versions-of-jira"></a>지원되는 JIRA 버전 

현재 기준으로 다음 버전의 JIRA가 지원됩니다.

- JIRA 코어 및 소프트웨어: 6.0 ~ 7.2.0
- JIRA 서비스 데스크: 3.0 ~ 3.2

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 JIRA SAML SSO by Microsoft 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-jira-saml-sso-by-microsoft-from-the-gallery"></a>갤러리에서 JIRA SAML SSO by Microsoft 추가
JIRA SAML SSO by Microsoft가 Azure AD에 통합되도록 구성하려면 갤러리에서 JIRA SAML SSO by Microsoft를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 JIRA SAML SSO by Microsoft를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **JIRA SAML SSO by Microsoft**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_search.png)

5. 결과 패널에서 **JIRA SAML SSO by Microsoft**를 선택한 다음 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 “Britta Simon”이라는 테스트 사용자를 기반으로 JIRA SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD의 사용자에 해당하는 JIRA SAML SSO by Microsoft의 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 JIRA SAML SSO by Microsoft의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

JIRA SAML SSO by Microsoft에서 Azure AD의 **사용자 이름** 값을 **Username** 값으로 할당하여 링크 관계를 설정합니다.

JIRA SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[JIRA SAML SSO by Microsoft 테스트 사용자 만들기](#creating-a-jira-saml-sso-by-microsoft-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 JIRA SAML SSO by Microsoft에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 JIRA SAML SSO by Microsoft 응용 프로그램에서 Single Sign-On을 구성합니다.

**JIRA SAML SSO by Microsoft에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **JIRA SAML SSO by Microsoft** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_samlbase.png)

3. **JIRA SAML SSO by Microsoft 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_url.png)

    a. **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://<domain:port>/plugins/servlet/saml/auth`

    b. **식별자** 텍스트 상자에서 `https://<domain:port>/` 패턴을 사용하여 URL을 입력합니다.

    c. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 명명된 URL인 경우 포트는 선택 사항입니다. 이러한 값은 JIRA 플러그 인 구성 중에 수신되며 자습서의 뒷부분에 설명되어 있습니다.
 
4. **메타데이터** URL을 생성하려면 다음 단계를 수행합니다.

    a. **앱 등록**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/appregistrations.png)
   
    b. **끝점**을 클릭하여 **끝점** 대화 상자를 엽니다.  
    
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/endpointicon.png)

    c. 복사 단추를 클릭하여 **페더레이션 메타데이터 문서** URL을 복사하여 메모장에 붙여넣습니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/endpoint.png)
     
    d. 이제 **JIRA SAML SSO by Microsoft**의 속성 페이지로 이동하고 **복사** 단추를 사용하여 **응용 프로그램 ID**를 복사하여 메모장에 붙여넣습니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/appid.png)

    e. `<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 패턴을 사용하여 **메타데이터 URL**을 생성하고 이 값을 메모장에 복사합니다. 나중에 플러그 인 구성에 사용되기 때문입니다.

5. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_400.png)

6. JIRA 플러그 인에 대한 다음 정보로 [Microsoft](mailto:waadpartners@microsoft.com)에 문의합니다.
    
    *   고객 이름:
    *   주 도메인 이름:
    *   Azure AD Premium: 예/아니요(플러그 인은 무료, 기본 및 프리미엄 SKU에 해당하는 모든 고객이 사용할 수 있습니다.)
    *   이 통합을 사용할 사용자의 수:
    *   JIRA 버전:
    *   설명:

7. 다른 웹 브라우저 창에서 JIRA 인스턴스에 관리자로 로그인합니다.

8. 마우스로 선 위를 가리키고 **추가 기능**을 클릭합니다.
    
    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/addon1.png)

9. 추가 기능 탭 섹션에서 **추가 기능 관리**를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/addon7.png)

10. Microsoft에서 제공하는 플러그 인을 수동으로 업로드합니다. 플러그 인이 설치되면 **추가 기능 관리** 섹션의 **사용자가 설치한** 추가 기능 섹션에 표시됩니다.

11. **구성**을 클릭하여 새 플러그 인을 구성합니다.

12. 구성 페이지에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/addon5.png)
 
    a. **메타데이터 URL**에 Azure AD에서 생성된 **메타데이터 URL**을 붙여넣고 **해결** 단추를 클릭합니다. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

    > [!Note]
    > 기본 SAML 사용자 ID 위치는 이름 식별자입니다. 이것을 특성 옵션으로 변경하고 적절한 특성 이름을 입력할 수 있습니다.

    > [!TIP]
    > 메타데이터를 확인하는 데 오류가 없도록 앱에 매핑된 인증서가 하나만 있는지 확인합니다. 인증서가 여러 개 있으면 메타데이터를 확인할 때 관리자에게 오류가 표시됩니다.
    
    b. **식별자, 회신 URL 및 로그인 URL** 값을 복사하여 Azure Portal에서 **JIRA SAML SSO by Microsoft 도메인 및 URL** 섹션의 **식별자, 회신 URL 및 로그인 URL** 텍스트 상자에 각각 붙여넣습니다.

    c. **Login Button Name**(로그인 단추 이름)에 조직이 사용자의 로그인 화면에 표시하려는 단추의 이름을 입력합니다.

    d. **SAML User ID Locations**(SAML 사용자 ID 위치)에서 **User ID is in the NameIdentifier element of the Subject statement**(사용자 ID는 Subject 문의 NameIdentifier 요소에 있습니다.) 또는 **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.)를 선택합니다.  이 ID는 JIRA 사용자 ID여야 합니다. 사용자 ID가 일치하지 않으면 시스템에서 사용자 로그인을 허용하지 않습니다. 
    
    e. **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.) 옵션을 선택하는 경우 **특성 이름** 텍스트 상자에 사용자 ID가 필요한 특성의 이름을 입력합니다. 

    f. Azure AD에서 페더레이션된 도메인(예: ADFS 등)을 사용하는 경우 **Enable Home Realm Discovery**(홈 영역 검색 사용) 옵션을 클릭하고 **도메인 이름**을 구성합니다.
    
    g. **도메인 이름**에 ADFS 기반 로그인인 경우 여기에 도메인 이름을 입력합니다.

    h. 사용자가 JIRA에서 로그아웃할 때 Azure AD에서 로그아웃하려면 **Enable Single Sign out**(Single Sign-Out 사용)을 선택합니다. 

    i. **저장** 단추를 클릭하여 설정을 저장합니다.

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jiramicrosoft-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-jira-saml-sso-by-microsoft-test-user"></a>JIRA SAML SSO by Microsoft 테스트 사용자 만들기

Azure AD 사용자가 JIRA 온-프레미스 서버에 로그인할 수 있게 하려면 사용자를 JIRA SAML SSO by Microsoft에 프로비전해야 합니다. JIRA SAML SSO by Microsoft의 경우 프로비전이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. JIRA 온-프레미스 서버에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **사용자 관리**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-jiramicrosoft-tutorial/user1.png) 

3. 리디렉션된 [관리자 액세스] 페이지에서 **암호**를 입력하고 **확인** 단추를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-jiramicrosoft-tutorial/user2.png) 

4. **사용자 관리** 탭 섹션 아래에서  **사용자 만들기**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-jiramicrosoft-tutorial/user3.png) 

5. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/active-directory-saas-jiramicrosoft-tutorial/user4.png) 

    a. **이메일 주소** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    b. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    c. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.

    e. **사용자 만들기**를 클릭합니다.   

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 JIRA SAML SSO by Microsoft에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 JIRA SAML SSO by Microsoft에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **JIRA SAML SSO by Microsoft**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_jiramicrosoft_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 JIRA SAML SSO by Microsoft 타일을 클릭하면 JIRA SAML SSO by Microsoft 응용 프로그램에 자동으로 로그온됩니다.
액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-jiramicrosoft-tutorial/tutorial_general_203.png


