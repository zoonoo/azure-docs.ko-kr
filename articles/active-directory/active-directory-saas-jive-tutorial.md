<properties
	pageTitle="자습서: Jive와 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory와 Jive 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="jeevansd"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/01/2016"
	ms.author="jeedes"/>


# 자습서: Jive와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Jive를 통합하는 방법에 대해 알아봅니다.

Jive를 Azure AD와 통합하면 다음과 같은 이점이 제공됩니다.

- Jive에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
- 사용자가 해당 Azure AD 계정으로 Jive에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건

Jive와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Jive Single Sign-On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.


## 시나리오 설명
이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 테스트 합니다.

이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Jive 추가
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 Jive 추가
Jive의 Azure AD 통합을 구성하려면 갤러리의 Jive를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Jive를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Active Directory][1]
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **Jive**를 입력합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/tutorial_jive_01.png)
7. 결과 창에서 **Jive**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/tutorial_jive_02.png)


##  Azure AD Single Sign-on 구성 및 테스트
이 섹션에서는 "Britta Simon"이라는 테스트 사용자를 기반으로 Jive에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 Jive 사용자가 누구인지 알고 있어야 합니다. 즉, Azure AD 사용자와 Jive의 관련 사용자 간에 연결이 형성되어야 합니다.

이 연결 관계는 Azure AD의 **사용자 이름** 값을 Jive의 **Username** 값으로 할당하여 설정합니다.

Jive에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
3. **[Jive 테스트 사용자 만들기](#creating-a-jive-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Jive에 만듭니다.
4. **[사용자 프로비전 구성](#configuring-user-provisioning)** - Jive에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명합니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
6. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 Jive 응용 프로그램에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.

**Jive에서 Azure AD Single Sign-On을 구성하려면 다음 단계를 수행합니다.**

1. 클래식 포털의 **Jive** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.
	 
	![Single Sign-On 구성][6]

2. **Jive에 대한 사용자 로그인 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_03.png)

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_04.png)

    a. **로그인 URL** 텍스트 상자에 **https://\<고객 이름>.jivecustom.com** 패턴을 사용하여 사용자가 Jive 응용 프로그램에 로그인하는 데 사용할 URL을 입력합니다.
	
	b. **다음**을 클릭합니다.
 
4. **Jive에서 Single Sign-On 구성** 페이지에서 다음 단계를 수행합니다.

	![Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_05.png)

    a. **인증서 다운로드**를 클릭하고 파일을 컴퓨터에 저장합니다.

    b. **Next**를 클릭합니다.


5. Jive 테넌트에 관리자 권한으로 로그온합니다.

6. 메뉴 위쪽에서 "**SAML**"을 클릭합니다.

	![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_002.png)

	a. **일반** 탭에서 **사용**을 선택합니다.

	b. "**모든 SAML 설정 저장**" 단추를 클릭합니다.

7. "**IdP 메타데이터**" 탭으로 이동합니다.

	![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_003.png)

	a. 다운로드한 메타데이터 XML 파일의 내용을 복사한 다음 **IDP(ID 공급자) 메타데이터** 텍스트 상자에 붙여 넣습니다.

	b. "**모든 SAML 설정 저장**" 단추를 클릭합니다.

8. "**사용자 특성 매핑**" 탭으로 이동합니다.

	![앱 쪽에서 Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_004.png)

	a. **전자 메일** 텍스트 상자에서 **mail** 값의 특성 이름을 복사한 후 붙여 넣습니다.

	b. **이름** 텍스트 상자에서 **givenname** 값의 특성 이름을 복사한 후 붙여 넣습니다.

	c. **성** 텍스트 상자에서 **surname** 값의 특성 이름을 복사한 후 붙여 넣습니다.
	
9. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다. ![Azure AD Single Sign-On][10]

10. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다. ![Azure AD Single Sign-On][11]


### Azure AD 테스트 사용자 만들기
이 섹션에서는 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만듭니다.


![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_09.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_03.png)

4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다. ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_05.png)

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **Next**를 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_06.png)

    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다.

    e. **Next**를 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_07.png)

8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-jive-tutorial/create_aaduser_08.png)

    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.



###Jive 테스트 사용자 만들기

이 섹션에서는 Jive에서 Britta Simon이라는 사용자를 만듭니다. Jive 플랫폼에서 사용자를 추가하려면 Jive 지원 팀에 문의하세요.


###사용자 프로비전 구성
  
이 섹션에서는 Jive에 Active Directory 사용자 계정을 프로비저닝할 수 있도록 설정하는 방법을 간략하게 설명합니다. 이 절차의 일부로 Jive.com에 요청해야 하는 사용자 보안 토큰을 입력해야 합니다.
  
다음 스크린샷은 Azure AD의 관련 대화 상자 예를 보여줍니다.

![사용자 프로비저닝 구성](./media/active-directory-saas-jive-tutorial/IC698794.png "사용자 프로비저닝 구성")

####사용자 프로비저닝을 구성하려면

1.  Azure 관리 포털의 **Jive** 응용 프로그램 통합 페이지에서 **사용자 프로비저닝 구성**을 클릭하여 **사용자 프로비저닝 구성** 대화 상자를 엽니다.

2.  **Jive 자격 증명을 입력하여 자동 사용자 프로비저닝 사용** 페이지에서 다음 구성 설정을 입력합니다.

    1.  **Jive 관리자 사용자 이름** 텍스트 상자에 Jive.com에서 할당된 **시스템 관리자** 프로필을 가진 Jive 계정 이름을 입력합니다.

    2.  **Jive 관리자 암호** 텍스트 상자에 이 계정의 암호를 입력합니다.

    3.  **Jive 테넌트 URL** 텍스트 상자에 Jive 테넌트 URL을 입력합니다.

        >[AZURE.NOTE] Jive 테넌트 URL은 조직에서 Jive에 로그인할 때 사용하는 URL입니다. 일반적으로 URL의 형식은 **www.<organization>.jive.com**입니다.

    4.  **유효성 검사**를 클릭하여 구성을 확인합니다.

    5.  **다음** 단추를 클릭하여 **확인** 페이지를 엽니다.

3.  **확인** 페이지에서 확인 표시를 클릭하여 구성을 저장합니다.
  
이제 테스트 계정을 만들 수 있습니다. 10분 동안 기다린 후 계정이 Jive.com에 동기화되었는지 확인해 보십시오.




### Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Jive에 대한 액세스 권한을 부여합니다.

![사용자 할당][200]

**Britta Simon을 Jive에 할당하려면 다음 단계를 수행합니다.**

1. 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![사용자 할당][201]

2. 응용 프로그램 목록에서 **Jive**를 선택합니다.

	![Single Sign-On 구성](./media/active-directory-saas-jive-tutorial/tutorial_jive_50.png)

3. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

4. 사용자 목록에서 **Britta Simon**을 선택합니다.

5. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

	![사용자 할당][205]


### Single Sign-On 테스트

이 섹션에서는 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

액세스 패널에서 Jive 타일을 클릭하면 Jive 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-jive-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-jive-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-jive-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-jive-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-jive-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-jive-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-jive-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-jive-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-jive-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-jive-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-jive-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-jive-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-jive-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0907_2016-->