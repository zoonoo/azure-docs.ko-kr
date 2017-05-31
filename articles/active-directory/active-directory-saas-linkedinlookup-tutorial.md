---
title: "자습서: LinkedIn Lookup과 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 LinkedIn Lookup 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: a2757a39-1ead-4a3e-91e4-270be3055683
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.translationtype: Human Translation
ms.sourcegitcommit: 95b8c100246815f72570d898b4a5555e6196a1a0
ms.openlocfilehash: a4160a89c6a06222a9239d996fcca822e1a50382
ms.contentlocale: ko-kr
ms.lasthandoff: 05/18/2017


---
# <a name="tutorial-azure-active-directory-integration-with-linkedin-lookup"></a>자습서: LinkedIn Lookup과 Azure Active Directory 통합

이 자습서에서는 LinkedIn Lookup을 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다.

LinkedIn Lookup을 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- LinkedIn Lookup에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자의 Azure AD 계정으로 LinkedIn Lookup에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리할 수 있습니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory의 응용 프로그램 액세스 및 Single Sign-On이란 무엇인가요?](active-directory-appssoaccess-whatis.md)를 참조하세요.

## <a name="prerequisites"></a>필수 조건

LinkedIn Lookup과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- LinkedIn Lookup Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 LinkedIn Lookup 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-linkedin-lookup-from-the-gallery"></a>갤러리에서 LinkedIn Lookup 추가
LinkedIn Lookup이 Azure AD에 통합되도록 구성하려면 갤러리에서 LinkedIn Lookup을 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 LinkedIn Lookup을 추가하려면 다음 단계를 수행합니다.**

1. **[Azure Portal](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 맨 위 있는 **새 응용 프로그램** 단추를 클릭하여 새 응용 프로그램을 추가합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **LinkedIn Lookup**을 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_search.png)

5. 결과 패널에서 **LinkedIn Lookup**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 LinkedIn Lookup에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 대응하는 LinkedIn Lookup 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 LinkedIn Lookup의 관련 사용자 간에 연결 관계가 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 LinkedIn Lookup의 **Username** 값으로 할당하여 설정합니다.

LinkedIn Lookup에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[LinkedIn Lookup 테스트 사용자 만들기](#creating-an-linkedin-lookup-test-user)** - Britta Simon의 Azure AD 표현과 연결되는 대응 사용자를 LinkedIn Lookup에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure Portal에서 Azure AD Single Sign-On을 사용하도록 설정하고 LinkedIn Lookup 응용 프로그램에서 Single Sign-On을 구성합니다.

**LinkedIn Lookup에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. Azure Portal의 **LinkedIn Lookup** 응용 프로그램 통합 페이지에서 **Single Sign-On**을 클릭합니다.

    ![Single Sign-on 구성][4]

2. **Single Sign-On** 대화 상자의 **모드**에서 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_samlbase.png)

3. 다른 웹 브라우저 창에서 **LinkedIn Lookup** 웹 사이트에 관리자로 로그온합니다.

4. **계정 센터**의 **설정** 아래에서 **전역 설정**을 클릭합니다. 또한 드롭다운 목록에서 **Lookup**을 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_011.png)

5. **OR Click Here to load and copy individual fields from the form(또는 폼에서 개별 필드를 로드하여 복사하려면 여기를 클릭)**을 클릭하고 **엔터티 ID** 및 **ACS(Assertion Consumer Access) URL**을 복사합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_LinkedIn_admin_032.png)

6. Azure Portal의 **LinkedIn Lookup 도메인 및 URL** 섹션에서 **IDP** 시작 모드로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url.png)

    a. **식별자** 텍스트 상자에 LinkedIn 포털에서 복사한 **엔티티 ID**를 입력합니다. 

    b. **회신 URL** 텍스트 상자에 LinkedIn 포털에서 복사한 **ACS(Assertion Consumer Access) URL**을 입력합니다.

7. **SP** 시작 모드에서 응용 프로그램을 구성하려면 **고급 URL 설정 표시**를 선택하세요.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_url1.png)

    **로그온 URL** 텍스트 상자에서 다음 패턴으로 URL을 입력합니다. `https://www.linkedIn.com/checkpoint/enterprise/login/<AccountId>?application=lookup`
     
    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 로그온 URL로 업데이트해야 합니다. 이 값을 얻으려면 [LinkedIn Lookup 지원 팀](https://business.LinkedIn.com/lookup)에 문의하세요.

8. **LinkedIn Lookup** 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷은 예제를 보여 줍니다. **사용자 ID**의 기본값은 **user.userprincipalname**이지만 LinkedIn Lookup에서는 이것이 사용자의 전자 메일 주소와 매핑되어야 합니다. 목록에서 **user.mail** 특성을 사용하거나 조직 구성을 기반으로 적절한 특성 값을 사용할 수 있습니다. 

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/updateusermail.png)
    
9. **사용자 특성** 섹션에서 **기타 모든 사용자 특성 보기 및 편집**을 클릭하고 특성을 설정합니다. 사용자는 **department**라는 또 다른 클레임을 추가해야 하고 값은 **user.department**와 매핑됩니다.

    | 특성 이름 | 특성 값 |
    | --- | --- |    
    | department| user.department |

   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/userattribute.png)

   a. **특성 추가**를 클릭하여 특성 세부 정보 페이지를 열고, 아래와 같이 부서 특성을 추가합니다.

   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/adduserattribute.png)
   
   b. **확인**을 클릭하여 특성을 저장합니다.

10. **SAML 서명 인증서** 섹션에서 **메타데이터 XML**을 클릭한 후 컴퓨터에 XML 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_certificate.png) 

11. **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_400.png)

12. **LinkedIn 관리 설정** 섹션으로 이동합니다. **Upload XML file(XML 파일 업로드)** 옵션을 클릭하여 Azure Portal에서 다운로드한 XML 파일을 업로드합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_metadata_03.png)

13. **설정**을 클릭하여 SSO를 사용하도록 설정합니다. SSO 상태가 **연결 안 됨**에서 **연결됨**으로 변경됩니다.

    ![Single Sign-On 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedIn_admin_05.png)

> [!TIP]
> 이제 앱을 설정하는 동안 [Azure Portal](https://portal.azure.com) 내에서 이러한 지침의 간결한 버전을 읽을 수 있습니다.  **Active Directory > 엔터프라이즈 응용 프로그램** 섹션에서 이 앱을 추가한 후에는 **Single Sign-On** 탭을 클릭하고 맨 아래에 있는 **구성** 섹션을 통해 포함된 설명서에 액세스하면 됩니다. 포함된 설명서 기능에 대한 자세한 내용은 [Azure AD 포함된 설명서]( https://go.microsoft.com/fwlink/?linkid=845985)에서 확인할 수 있습니다.
> 

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure Portal**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동하여 **모든 사용자**를 클릭합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_02.png) 

3. **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 Britta Simon의 **메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다.
 
### <a name="creating-an-linkedin-lookup-test-user"></a>LinkedIn Lookup 테스트 사용자 만들기

LinkedIn Lookup 응용 프로그램이 JIT(Just-in-time) 사용자 프로비전을 지원하며 인증 후에 응용 프로그램에서 사용자가 자동으로 만들어집니다. **Automatically assign licenses**(라이선스 자동 할당)를 활성화하여 사용자에게 라이선스를 할당합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedin_admin_license.png)

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 LinkedIn Lookup에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 LinkedIn Lookup에 할당하려면 다음 단계를 수행합니다.**

1. Azure Portal에서 응용 프로그램 보기를 연 다음 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **LinkedIn Lookup**을 선택합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_linkedInlookup_app.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    
### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 LinkedIn Lookup 타일을 클릭하면 개인 LinkedIn 계정 세부 정보를 제공해야 하는 조직 페이지로 리디렉션됩니다. 개인 계정이 LinkedIn 비즈니스 계정과 연결됩니다. 

액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하세요. 

## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-LinkedInlookup-tutorial/tutorial_general_203.png


