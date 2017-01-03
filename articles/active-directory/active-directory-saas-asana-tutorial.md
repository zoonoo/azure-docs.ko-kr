---
title: "자습서: Ariba와 Azure Active Directory 통합 | Microsoft Docs"
description: "Azure Active Directory와 Asana 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: 837e38fe-8f55-475c-87f4-6394dc1fee2b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2016
ms.author: jeedes
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 583141e7543cb5e824204a49ff4400fe462ff28b


---
# <a name="tutorial-azure-active-directory-integration-with-asana"></a>자습서: Asana와 Azure Active Directory 통합
이 자습서에서는 Azure AD(Azure Active Directory)와 Asana를 통합하는 방법에 대해 알아봅니다.

Asana를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

* Asana에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Asana에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
Asana와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독
* **Asana** Single Sign-On이 설정된 구독

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.
> 
> 

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

* 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
* Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다. 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Asana 추가
2. Azure AD Single Sign-on 구성 및 테스트

## <a name="adding-asana-from-the-gallery"></a>갤러리에서 Asana 추가
Asana의 Azure AD 통합을 구성하려면 갤러리의 Asana를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Asana를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
   
    ![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가** 를 클릭합니다.
   
    ![응용 프로그램][3]
5. **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.
   
    ![응용 프로그램][4]
6. 검색 상자에 **Asana**를 입력합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/tutorial_asana_01.png)
7. 결과 창에서 **Asana**를 선택한 다음 **완료**를 클릭하여 응용 프로그램을 추가합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/tutorial_asana_02.png)

## <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Asana에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Asana 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Asana의 관련 사용자 간에 연결이 형성되어야 합니다.
이 연결 관계는 Azure AD의 **사용자 이름** 값을 Asana의 **Username** 값으로 할당하여 설정합니다.

Asana에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Configuring Azure AD Single Sign-On](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Asana 테스트 사용자 만들기](#creating-an-Asana-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Asana에 만듭니다.
4. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Testing Single Sign-On](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### <a name="configuring-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성
이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Asana 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

**Asana에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. 위쪽 메뉴에서 **빠른 시작**을 클릭합니다.
   
    ![Single Sign-On 구성][6]
2. Azure 클래식 포털의 **Asana** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
   
    ![Single Sign-On 구성][7] 
3. **Asana에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택한 후 **다음**을 클릭합니다.
   
    ![Single Sign-on 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_06.png)
4. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다. 
   
    ![Single Sign-On 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_07.png)

    a. 로그인 URL 텍스트 상자에서 다음과 같은 패턴을 사용하여 URL을 입력합니다. `https://app.asana.com`

    c. **다음**을 클릭합니다.

1. **Asana에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 파일을 컴퓨터에 저장합니다. 또한 SAML SSO URL에 대한 값을 복사합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_08.png)
2. 인증서를 마우스 오른쪽 단추로 클릭한 후 메모장 또는 원하는 텍스트 편집기를 사용하여 인증서 파일을 엽니다. 시작 및 끝 인증서 제목 사이의 콘텐츠를 복사합니다. Asana에서 SSO를 구성하는 데 사용할 X.509 인증서입니다.
3. 다른 브라우저 창에서 Asana 응용 프로그램에 로그온합니다. Asana에서 SSO를 구성하려면 화면 오른쪽 위 모서리에서 작업 영역 이름을 클릭하여 작업 영역 설정에 액세스합니다. 그런 다음 **\<작업 영역 이름\> 설정**을 클릭합니다. 
   
    ![Single Sign-on 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_09.png)
4. **조직 설정** 창에서 **관리**를 클릭합니다. 그런 다음 **멤버는 SAML을 통해 로그인해야 합니다** 를 클릭하여 SSO 구성을 활성화합니다. 다음 단계를 수행합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_10.png)
   
    a. **로그인 페이지 URL** 텍스트 상자에서 Azure AD의 SAML 로그인 URL을 붙여 넣습니다.
   
    b. **X.509 인증서** 텍스트 상자에서 Azure AD에서 복사한 X.509 인증서를 붙여 넣습니다.
5. **Save**를 클릭합니다. 추가 지원이 필요한 경우 [SSO 설정을 위한 Asana 가이드](https://asana.com/guide/help/premium/authentication#gl-saml) 로 이동합니다.
6. Azure AD에서 **Asana에서 Single Sign-On 구성** 페이지로 이동하고 Single Sign-On 구성 확인을 선택한 후 **다음**을 클릭합니다.
   
    ![Azure AD Single Sign-On][10]
7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.  
   
    ![Azure AD Single Sign-On][11]

### <a name="creating-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_09.png) 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_03.png) 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_04.png) 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_05.png) 
   
    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.
   
    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
   
    c. **다음**을 클릭합니다.
6. **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
   ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_06.png) 
   
   a. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
   
   b. **성** 텍스트 상자에 **Simon**을 입력합니다.
   
   c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
   
   d. **역할** 목록에서 **사용자**를 선택합니다.
   
   e. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_07.png) 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/create_aaduser_08.png) 
   
    a. **새 암호**값을 적어둡니다.
   
    b. **완료**를 클릭합니다.   

### <a name="creating-an-asana-test-user"></a>Asana 테스트 사용자 만들기
이 섹션에서는 Asana에서 Britta Simon이라는 사용자를 만듭니다.

1. **Asana**에서 왼쪽 패널의 **팀** 섹션으로 이동합니다. 더하기 기호 단추를 클릭합니다. 
   
    ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-asana-tutorial/tutorial_asana_12.png) 
2. 텍스트 상자에 전자 메일 britta.simon@contoso.com을(를) 입력한 다음 **초대**를 선택합니다.
3. **초대 보내기**를 클릭합니다. 새 사용자는 자신의 전자 메일 계정에 전자 메일을 받게 됩니다. 계정을 만들고 계정의 유효성을 검사해야 합니다.

### <a name="assigning-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당
이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Asana에 대한 액세스 권한을 부여합니다.

![사용자 할당][200] 

**Britta Simon을 Asana에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.
   
    ![사용자 할당][201] 
2. 응용 프로그램 목록에서 **Asana**를 선택합니다.
   
    ![Single Sign-On 구성](./media/active-directory-saas-asana-tutorial/tutorial_asana_50.png) 
3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.
   
    ![사용자 할당][203] 
4. 모든 사용자 목록에서 **Britta Simon**을 선택합니다.
5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
   
    ![사용자 할당][205]

### <a name="testing-single-sign-on"></a>Single Sign-On 테스트
이 섹션은 Azure AD Single Sign-On을 테스트하기 위한 것입니다.

Asana 로그인 페이지로 이동합니다. 전자 메일 주소 텍스트 상자에서 전자 메일 주소 britta.simon@contoso.com을(를) 삽입합니다. 암호 텍스트 상자를 비워 둔 다음 **로그인**을 클릭합니다. Azure AD 로그인 페이지로 리디렉션됩니다. Azure AD 자격 증명을 완료합니다. 이제 Asana에 로그인되었습니다.

## <a name="additional-resources"></a>추가 리소스
* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-asana-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-asana-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-asana-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-asana-tutorial/tutorial_general_04.png


[5]: ./media/active-directory-saas-asana-tutorial/tutorial_general_05.png
[6]: ./media/active-directory-saas-asana-tutorial/tutorial_general_06.png
[7]:  ./media/active-directory-saas-asana-tutorial/tutorial_general_050.png
[10]: ./media/active-directory-saas-asana-tutorial/tutorial_general_060.png
[11]: ./media/active-directory-saas-asana-tutorial/tutorial_general_070.png
[20]: ./media/active-directory-saas-asana-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-asana-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-asana-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-asana-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-asana-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-asana-tutorial/tutorial_general_205.png



<!--HONumber=Dec16_HO5-->


