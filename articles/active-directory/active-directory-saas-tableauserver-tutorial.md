<properties
	pageTitle="자습서: Tableau Server와 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory와 Tableau Server 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/12/2016"
	ms.author="jeedes"/>


# 자습서: Tableau Server와 Azure Active Directory 통합

이 자습서에서는 Tableau Server와 Azure AD(Azure Active Directory)를 통합하는 방법을 보여 줍니다.

Tableau Server를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Tableau Server에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Tableau Server에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Active Directory 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건

Tableau Server와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Tableau Server Single Sign-On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## 시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Tableau Server 추가
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 Tableau Server 추가
Tableau Server의 Azure AD 통합을 구성하려면 갤러리의 Tableau Server를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Tableau Server를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 
 
	![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **Tableau Server**를 입력합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_01.png)

7. 결과 창에서 **Tableau Server**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![갤러리에서 앱 선택](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_02.png)

##  Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 Tableau Server에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Tableau Server 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Tableau Server의 관련 사용자 간에 연결이 설정되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Tableau Server의 **Username** 값으로 할당하여 설정합니다.

Tableau Server에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Tableau Server 테스트 사용자 만들기](#creating-a-tableauserver-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Tableau Server에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-on을 사용하도록 설정하고 Tableau Server 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

Tableau Server 응용 프로그램은 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_51.png)

**Tableau Server에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**


1. Azure 클래식 포털의 **Tableau Server** 응용 프로그램 통합 페이지에 있는 상단 메뉴에서 **특성**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_81.png)


1. **SAML 토큰 특성** 대화 상자에서 다음 단계를 수행합니다.

	

	a. **사용자 특성 추가**를 클릭하여 **사용자 특성 추가** 대화 상자를 엽니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_82.png)


	b. **특성 이름** 텍스트 상자에 **username**을 입력합니다.

    c. **특성 값** 목록에서 **user.displayname**을 선택합니다.

    d. **완료**를 클릭합니다.
	



1. 위쪽 메뉴에서 **빠른 시작**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_general_83.png)








1. **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

	![Single Sign-On 구성][6]



2. **Tableau Server에 대한 사용자 로그인 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_03.png)


3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행하고 **다음**을 선택합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_04.png)



    a. **로그인 URL** 텍스트 상자에 Tableau Server의 URL을 입력합니다.

	b. 식별자 상자에서 다음을 복사합니다.

	c. **다음**를 클릭합니다.


4. **Tableau Server에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행하고 **다음**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_05.png)


    a. **메타데이터 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.

    b. **다음**을 클릭합니다.


6. 응용 프로그램에 대해 구성된 SSO를 가져오려면 관리자 권한으로 Tableau Server 테넌트에 로그온해야 합니다.

	a. Tableau Server 구성에서 **SAML** 탭을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_001.png)


	b. **Single Sign-On에 SAML 사용** 확인란을 선택합니다.

	c. Azure 관리 포털에서 다운로드한 페더레이션 메타데이터 파일을 찾은 다음 **SAML Idp 메타데이터 파일**에 업로드합니다.

	d. Tableau Server 반환 URL - Tableau Server 사용자가 액세스하는 URL(예: http://tableau_server)입니다. http://localhost는 사용하지 않는 것이 좋습니다. 후행 슬래시가 있는 URL(예: http://tableau_server/)은 지원되지 않습니다. **Tableau Server 반환 URL**을 복사하여 3단계에 표시된 대로 Azure AD **로그온 URL** 텍스트 상자에 붙여 넣습니다.

	e. SAML 엔터티 ID - 엔터티 ID는 IdP에 대한 Tableau Server 설치를 고유하게 식별합니다. 원하는 경우 여기에 Tableau Server URL을 다시 입력할 수 있지만 반드시 Tableau Server URL을 입력해야 하는 것은 아닙니다. **SAML 엔터티 ID**를 복사하여 3단계에 표시된 대로 Azure AD **식별자** 텍스트 상자에 붙여 넣습니다.

	f. **메타데이터 파일 내보내기**를 클릭하여 텍스트 편집기 응용 프로그램에서 엽니다. Http Post 및 인덱스 0이 포함된 어설션 소비자 서비스 URL을 찾아서 복사합니다. 이제 3단계에 표시된 대로 Azure AD **회신 URL** 텍스트 상자에 붙여 넣습니다.

	g. Tableau Server 구성 페이지에서 **확인** 단추를 클릭합니다.

	> [AZURE.NOTE] Tableau Server에서 SAML을 구성하는 데 도움이 필요한 경우 [SAML 구성](http://onlinehelp.tableau.com/current/server/ko-KR/config_saml.htm) 문서를 참조하세요.

6. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.

	![Azure AD Single Sign-On][10]

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.
 
	![Azure AD Single Sign-On][11]


### Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

사용자 목록에서 **Britta Simon**을 선택합니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_09.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.
 
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_03.png)


4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_05.png)

    a. **사용자 형식**에서 **조직의 새 사용자**를 선택합니다.

    b. **사용자 이름** 텍스트 상자에 **BrittaSimon**을 입력합니다.

    c. **다음**을 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_06.png)

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다.

    e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_07.png)


8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
 
	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-tableauserver-tutorial/create_aaduser_08.png)

    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.



### Tableau Server 테스트 사용자 만들기

이 섹션은 Tableau Server에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Tableau Server의 모든 사용자를 프로비전해야 합니다. 또한 사용자의 사용자 이름은 Azure AD에서 구성한 사용자 지정 특성 **username**의 값과 일치해야 합니다. 올바른 매핑을 사용해야 통합 시 [Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)이 작동합니다.

> [AZURE.NOTE] 사용자를 수동으로 만들어야 하는 경우 조직의 Tableau Server 관리자에게 문의해야 합니다.


### Azure AD 테스트 사용자 할당

이 섹션의 목적은 Britta Simon에게 Tableau Server에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다.

![사용자 할당][200]

**Britta Simon을 Tableau Server에 할당하려면 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.
 
	![사용자 할당][201]

2. 응용 프로그램 목록에서 **Tableau Server**를 선택합니다.

	![Single Sign-On 구성](./media/active-directory-saas-tableauserver-tutorial/tutorial_tableauserver_50.png)


1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

1. 사용자 목록에서 **Britta Simon**을 선택합니다.

2. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

![사용자 할당][205]



### Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.

액세스 패널에서 Tableau Server 타일을 클릭하면 Tableau Server 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-tableauserver-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0413_2016-->