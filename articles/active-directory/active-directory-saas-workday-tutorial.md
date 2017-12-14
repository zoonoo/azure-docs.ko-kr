---
title: "자습서: Workday와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Workday 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: jeedes
ms.openlocfilehash: 5e4d46f9a3954698fbbe3c80fd8a95f4cd87465b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-workday"></a>자습서: Workday와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workday를 통합하는 방법에 대해 알아봅니다.

Workday를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Workday에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 자신의 Azure AD 계정으로 Workday에 자동으로 로그온(Single Sign-On) 되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

Workday와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Workday Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Workday 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-workday-from-the-gallery"></a>갤러리에서 Workday 추가
Workday가 Azure AD에 통합되도록 구성하려면 갤러리에서 Workday를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Workday를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 새 응용 프로그램을 추가하려면 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Workday**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/tutorial_workday_search.png)

5. 결과 창에서 **Workday**를 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/tutorial_workday_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Workday에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD 사용자에 해당하는 Workday 사용자가 누구인지 Azure AD에서 알고 있어야 합니다. 즉, Azure AD 사용자와 Workday의 관련 사용자 간에 링크 관계가 설정되어야 합니다.

이 링크 관계는 Azure AD의 **사용자 이름** 값을 Workday의 **Username** 값으로 할당하여 설정합니다.

Workday에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Workday 테스트 사용자 만들기](#creating-a-workday-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 Workday에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 Workday 응용 프로그램에서 Single Sign-On을 구성합니다.

**Workday에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **Workday** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자에서 **모드**를 **SAML 기반 로그온**으로 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-on 구성](./media/active-directory-saas-workday-tutorial/tutorial_workday_samlbase.png)

3. **Workday 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-workday-tutorial/tutorial_workday_url.png)

    a. **로그인 URL** 텍스트 상자에서 값으로 `https://impl.workday.com/<tenant>/login-saml2.htmld`를 입력합니다.

    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 회신 URL에 하위 도메인(예: www, wd2, wd3, wd3-impl, wd5, wd5-impl)이 있어야 합니다. "*http://www.myworkday.com*"과 같은 것은 작동하지만 "*http://myworkday.com*"은 작동하지 않습니다. 이러한 값을 얻으려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의하세요. 
 

4. **SAML 서명 인증서** 섹션에서 **인증서(Base64)**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-workday-tutorial/tutorial_workday_certificate.png) 

5. **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-workday-tutorial/tutorial_general_400.png)

6. **Workday 구성** 섹션에서 **Workday 구성**을 클릭하여 **로그온 구성** 창을 엽니다. **빠른 참조 섹션**에서 **로그아웃 URL, SAML 엔터티 ID 및 SAML Single Sign-On 서비스 URL**을 복사합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-workday-tutorial/tutorial_workday_configure.png) 
<CS>
7. 다른 웹 브라우저 창에서 Workday 회사 사이트에 관리자로 로그인합니다.

8. **메뉴 \> 워크벤치**로 이동합니다.
   
    ![워크벤치](./media/active-directory-saas-workday-tutorial/IC782923.png "워크벤치")

9. **계정 관리**로 이동합니다.
   
    ![계정 관리](./media/active-directory-saas-workday-tutorial/IC782924.png "계정 관리")

10. **테넌트 설정 편집 - 보안**으로 이동합니다.
   
    ![테넌트 보안 편집](./media/active-directory-saas-workday-tutorial/IC782925.png "테넌트 보안 편집")

11. **리디렉션 URL** 섹션에서 다음 단계를 수행합니다.
   
    ![리디렉션 URL](./media/active-directory-saas-workday-tutorial/IC7829581.png "리디렉션 URL")
   
    a. **행 추가**를 클릭합니다.
   
    b. **로그인 리디렉션 URL** 텍스트 상자 및 **모바일 리디렉션 URL** 텍스트 상자에서 Azure Portal의 **Workday 도메인 및 URL** 섹션에 입력한 **로그온 URL**을 입력합니다.
   
    c. Azure Portal의 **로그온 구성** 창에서 **로그아웃 URL**을 복사한 다음 **Logout Redirect URL**(로그아웃 리디렉션 URL) 텍스트 상자에 붙여넣습니다.
   
    d.  **환경** 텍스트 상자에서 환경 이름을 입력합니다.  

    >[!NOTE]
    > 테넌트 URL의 값에 연결된 환경 특성의 값은:  
    >-Workday 테넌트 URL의 도메인 이름이 impl로 시작되는 경우(예: *https://impl.workday.com/\<tenant\>/login-saml2.htmld*) **환경** 특성이 Implementation으로 설정되어있어야 합니다.  
    >-도메인 이름이 다르게 시작되면 일치하는 **환경** 값을 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의해야 합니다.

12. **SAML 설정** 섹션에서 다음 단계를 수행합니다.
   
    ![SAML 설정](./media/active-directory-saas-workday-tutorial/IC782926.png "SAML 설정")
   
    a.  **SAML 인증 사용**을 선택합니다.
   
    b.  **행 추가**를 클릭합니다.

13. SAML ID 공급자 섹션에서 다음 단계를 수행합니다.
   
    ![SAML ID 공급자](./media/active-directory-saas-workday-tutorial/IC7829271.png "SAML ID 공급자")
   
    a. ID 공급자 이름 텍스트 상자에 공급자 이름(예: *SPInitiatedSSO*)을 입력합니다.
   
    b. Azure Portal의 **로그온 구성** 창에서 **SAML 엔터티 ID**  값을 복사한 다음 **발급자** 텍스트 상자에 붙여넣습니다.
   
    c. **Enable Workday Initiated Logout**(Workday에서 시작된 로그아웃 사용)을 선택합니다.
   
    d. Azure Portal의 **로그온 구성** 창에서 **로그아웃 URL** 값을 복사한 다음 **Logout Request URL**(로그아웃 요청 URL) 텍스트 상자에 붙여넣습니다.

    e. **ID 공급자 공개 키 인증서**를 클릭한 다음 **만들기**를 클릭합니다. 

    ![만들기](./media/active-directory-saas-workday-tutorial/IC782928.png "만들기")

    f. **x509 공개 키 만들기**를 클릭합니다. 

    ![만들기](./media/active-directory-saas-workday-tutorial/IC782929.png "만들기")


14. **x509 공개 키 보기** 섹션에서 다음 단계를 수행합니다. 
   
    ![x509 공개 키 보기](./media/active-directory-saas-workday-tutorial/IC782930.png "x509 공개 키 보기") 
   
    a. **이름** 텍스트 상자에 인증서 이름(예: *PPE\_SP*)을 입력합니다.
   
    b. **유효 시작** 텍스트 상자에 인증서의 유효 시작 특성 값을 입력합니다.
   
    c.  **유효 만료** 텍스트 상자에 인증서의 유효 만료 특성 값을 입력합니다.
   
    > [!NOTE]
    > 유효 시작일과 유효 만료일은 다운로드한 인증서를 두 번 클릭하여 확인할 수 있습니다.  날짜는 **세부 정보** 탭에 나열됩니다.
    > 
    >
   
    d.  메모장에서 Base-64로 인코딩된 인증서를 열고 콘텐츠를 복사합니다.
   
    e.  **인증서** 텍스트 상자에 클립보드의 내용을 붙여 넣습니다.
   
    f.  **확인**을 클릭합니다.

15. 다음 단계를 수행합니다. 
   
    ![SSO 구성](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguratio.png "SSO 구성")
   
    a.  **x509 개인 키 쌍**을 사용하도록 설정합니다.
   
    b.  **서비스 공급자 ID** 텍스트 상자에 **http://www.workday.com**을 입력합니다.
   
    c.  **SP가 시작한 SAML 인증 사용**을 선택합니다.
   
    d.  Azure Portal의 **로그온 구성** 창에서 **SAML Single Sign-On 서비스 URL** 값을 복사한 다음 **IdP SSO 서비스 URL** 텍스트 상자에 붙여넣습니다.
   
    e. **SP에서 시작한 인증 요청을 Deflate하지 않음**을 선택합니다.
   
    f. **인증 요청 서명 메서드**로 **SHA256**을 선택합니다. 
   
    ![인증 요청 서명 메서드](./media/active-directory-saas-workday-tutorial/WorkdaySSOConfiguration.png "인증 요청 서명 메서드") 
   
    g. **확인**을 클릭합니다. 
   
    ![확인](./media/active-directory-saas-workday-tutorial/IC782933.png "확인")
<CE>

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
>

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/create_aaduser_01.png) 

2. 사용자 목록을 표시하려면 **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/create_aaduser_02.png) 

3. **사용자** 대화 상자를 열려면 대화 상자 위쪽에서 **추가**를 클릭합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-workday-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-a-workday-test-user"></a>Workday 테스트 사용자 만들기

Workday에 테스트 사용자를 프로비전하려면 [Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)에 문의해야 합니다.
[Workday 클라이언트 지원 팀](https://www.workday.com/en-us/partners-services/services/support.html)이 사용자를 만들어 드립니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Britta Simon이 Azure Single Sign-On을 사용할 수 있도록 Workday에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Workday에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Workday**를 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-workday-tutorial/tutorial_workday_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)
* [사용자 프로비저닝 구성](active-directory-saas-workday-inbound-tutorial.md)



<!--Image references-->

[1]: ./media/active-directory-saas-workday-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-workday-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-workday-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-workday-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-workday-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-workday-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-workday-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-workday-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-workday-tutorial/tutorial_general_203.png

