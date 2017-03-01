---
title: "자습서: Bonusly와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory 및 Bonusly 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: 42875d53-0769-4520-a6af-7308b5240d6b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: af00ed428dda846a06466df1e30ee7e8a7e5758c
ms.openlocfilehash: c160829519a14a55e76a2ecedcb286663dc89d65
ms.lasthandoff: 02/15/2017


---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>자습서: Bonusly와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Bonusly를 통합하는 방법에 대해 알아봅니다.

Bonusly를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Bonusly에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Bonusly SSO(Single Sign-On)에 자동으로 로그온되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 관리 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건

Bonusly와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Bonusly Single Sign-on이 설정된 구독


> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서&1;개월 평가판을 얻을 수 있습니다.


## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Bonusly 추가
2. Azure AD Single Sign-on 구성 및 테스트


## <a name="adding-bonusly-from-the-gallery"></a>갤러리에서 Bonusly 추가
Bonusly의 Azure AD 통합을 구성하려면 갤러리의 Bonusly를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Bonusly를 추가하려면 다음 단계를 수행합니다.**

1. **[Azure 관리 포털](https://portal.azure.com)**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다. 

    ![Active Directory][1]

2. **엔터프라이즈 응용 프로그램**으로 이동합니다. 그런 후 **모든 응용 프로그램**으로 이동합니다.

    ![응용 프로그램][2]
    
3. 대화 상자 위쪽에 있는 **추가** 단추를 클릭합니다.

    ![응용 프로그램][3]

4. 검색 상자에 **Bonusly**를 입력합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_001.png)

5. 결과 창에서 **Bonusly**을 선택하고 **추가** 단추를 클릭하여 응용 프로그램을 추가합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_0001.png)


##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Bonusly에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Bonusly 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Bonusly의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Bonusly의 **Username** 값으로 할당하여 설정합니다.

Bonusly에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Bonusly 테스트 사용자 만들기](#creating-a-bonusly-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Bonusly에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성

이 섹션에서는 Azure 관리 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Bonusly 응용 프로그램에서 Single Sign-On을 구성합니다.

**Bonusly에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털의 **Bonusly** 응용 프로그램 통합 페이지에서 **Single sign-on**을 클릭합니다.

    ![Single Sign-On 구성][4]

2. **Single sign on** 대화 상자에서 **모드**로 **SAML 기반 로그온**을 선택하여 Single Sign-On을 사용하도록 설정합니다.
 
    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_01.png)

3. **Bonusly 도메인 및 URL** 섹션에서 **IDP 시작 모드**로 응용 프로그램을 구성하려는 경우 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_02.png)

    a. **식별자** 텍스트 상자에 해당 값으로 `bonusly`을 입력합니다.
    
    b. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://bonus.ly/saml/<APP-ID>/consume`
    
4. **SP 시작 모드**에서 응용 프로그램을 구성하려는 경우 **Bonusly 도메인 및 URL** 섹션에서 다음 단계를 수행합니다.
    
    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_03.png)

    a. **고급 URL 설정 표시** 옵션을 클릭합니다.

    b. **로그온 URL** 텍스트 상자에서 다음 패턴 `https://bonus.ly/saml/<your_subdomain>/consume`을 사용하여 URL을 입력합니다.

    > [!NOTE] 
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 식별자 및 회신 URL로 값을 업데이트해야 합니다. 식별자에는 고유한 문자열 값을 사용하는 것이 좋습니다. 이러한 값을 얻으려면 [Bonusly 지원 팀](mailto:sales@easyterritory.com)에 문의하세요.

5. **SAML 서명 인증서** 섹션에서 **새 인증서 만들기**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_04.png)     

6. **새 인증서 만들기** 대화 상자에서 달력 아이콘을 클릭하고 **만료 날짜**를 선택합니다. 그런 후 **저장** 단추를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_general_300.png)

7. **SAML 서명 인증서** 섹션에서 **새 인증서 활성화**를 선택한 후 **저장** 단추를 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_05.png)

8. 팝업 **롤오버 인증서** 창에서 **확인**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_general_400.png)

9. **SAML 서명 인증서** 섹션에서 **인증서(Base64)**를 클릭한 후 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_06.png) 

10. **Bonusly 구성** 섹션에서 **Bonusly 구성**을 클릭하여 **로그온 구성** 창을 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_07.png) 

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_08.png)

11. 다른 웹 브라우저 창에서 Bonusly 회사 사이트에 관리자로 로그인합니다.

12. 위쪽에 도구 모음에서 **설정**을 클릭하고 **통합 및 앱**을 선택합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_002.png)

13. **Single Sign-On**에서 **SAML**을 선택합니다.

14. **SAML** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_003.png)

    a. **IdP SSO 대상 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-On 서비스 URL** 값을 입력합니다.

    b. **IdP 로그인 URL** 텍스트 상자에 Azure AD 응용 프로그램 구성 창의 **SAML Single Sign-On 서비스 URL** 값을 입력합니다.

    c. **IdP URL** 텍스트 상자에서 Azure AD 응용 프로그램 구성 창의 **SAML 엔터티 ID** 값을 입력합니다.

    d. 다운로드한 인증서에서 지문 값을 복사한 다음 **Cert 지문** 텍스트 상자에 붙여 넣습니다.

    e. **Save**를 클릭합니다.

    > [!NOTE] 
    > 자세한 내용은 [인증서의 지문 값을 검색하는 방법](https://www.youtube.com/watch?v=YKQF266SAxI&feature=youtu.be)을 참조하세요.



### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 관리 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][100]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Azure Active Directory** 아이콘을 클릭합니다.

    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/create_aaduser_01.png) 

2. **사용자 및 그룹**으로 이동한 후 **모든 사용자**를 클릭하여 사용자 목록을 표시합니다.
    
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/create_aaduser_02.png) 

3. 대화 상자 위쪽에서 **추가**를 클릭하여 **사용자** 대화 상자를 엽니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/create_aaduser_03.png) 

4. **사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-bonusly-tutorial/create_aaduser_04.png) 

    a. **이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    b. **사용자 이름** 텍스트 상자에 BrittaSimon의 **전자 메일 주소**를 입력합니다.

    c. **암호 표시**를 선택하고 **암호** 값을 적어둡니다.

    d. **만들기**를 클릭합니다. 



### <a name="creating-a-bonusly-test-user"></a>Bonusly 테스트 사용자 만들기

Azure AD 사용자가 Bonusly에 로그인할 수 있도록 하려면 Bonusly로 프로비전되어야 합니다.
Bonusly의 경우 프로비전은 수동 작업입니다.

####<a name="to-provision-a-user-account-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. Bonusly 회사 사이트에 관리자 권한으로 로그인합니다.

2. **설정**을 클릭합니다.

    ![새 사용자](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_010.png "새 사용자")

3. **사용자 및 보너스** 탭을 클릭합니다.

    ![새 사용자](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_011.png "새 사용자")

4. **사용자 관리**를 클릭합니다.

    ![새 사용자](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_012.png "새 사용자")

5. **사용자 추가**를 클릭합니다.

    ![새 사용자](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_013.png "새 사용자")

6. **사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![새 사용자](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_014.png "새 사용자")

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **전자 메일** 텍스트 상자에 Britta Simon 계정의 전자 메일 주소를 입력합니다.

    d. **Save**를 클릭합니다.

    > [!NOTE] 
    > AAD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다. 다른 Bonus.ly 사용자 계정 생성 도구 또는 Bonus.ly가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.



### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Bonusly에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Bonusly에 할당하려면 다음 단계를 수행합니다.**

1. Azure 관리 포털에서 응용 프로그램 보기를 열고 디렉터리 보기로 이동하고 **엔터프라이즈 응용 프로그램**으로 이동한 후 **모든 응용 프로그램**을 클릭합니다.

    ![사용자 할당][201] 

2. 응용 프로그램 목록에서 **Bonusly**를 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonusly-tutorial/tutorial_bonusly_50.png) 

3. 왼쪽 메뉴에서 **사용자 및 그룹**을 클릭합니다.

    ![사용자 할당][202] 

4. **추가** 단추를 클릭합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹**을 선택합니다.

    ![사용자 할당][203]

5. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon**을 선택합니다.

6. **사용자 및 그룹** 대화 상자에서 **선택** 단추를 클릭합니다.

7. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.
    


### <a name="testing-single-sign-on"></a>Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Bonusly 타일을 클릭하면 Bonusly 응용 프로그램에 자동으로 로그온됩니다.


## <a name="additional-resources"></a>추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-bonusly-tutorial/tutorial_general_203.png
