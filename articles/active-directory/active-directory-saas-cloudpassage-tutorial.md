<properties
	pageTitle="자습서: CloudPassage와 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory 및 CloudPassage 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
	services="active-directory"
	documentationCenter=""
	authors="markusvi"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/26/2015"
	ms.author="markvi"/>


# 자습서: CloudPassage와 Azure Active Directory 통합

이 자습서는 Azure AD(Azure Active Directory)에 CloudPassage를 통합하는 방법을 보여 주기 위한 것입니다.<br>Azure AD에 CloudPassage를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- CloudPassage에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
- 사용자가 해당 Azure AD 계정으로 CloudPassage에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Active Directory 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건 

CloudPassage와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- CloudPassage One Single Sign-On이 설정된 구독


> [AZURE.NOTE]이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 테스트 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)서 1개월 무료 Azure 평가판 구독을 얻을 수 있습니다. 

 
## 시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다. <br> 이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 CloudPassage 추가 
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 CloudPassage 추가
CloudPassage의 Azure AD 통합을 구성하려면 갤러리의 CloudPassage를 관리되는 SaaS 앱 목록에 추가해야 합니다.

### 갤러리에서 CloudPassage를 추가하려면 다음 단계를 수행합니다.

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. <br><br> ![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.<br><br> ![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.<br><br> ![응용 프로그램][3]
5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.<br><br> ![응용 프로그램][4]
6. 검색 상자에 **CloudPassage**를 입력합니다.<br><br> ![응용 프로그램][5]
7. 결과 창에서 **CloudPassage**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.<br><br> ![응용 프로그램][6]



##  Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 CloudPassage에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-On이 작동하려면 Azure AD에서 Azure AD 사용자에 해당하는 CloudPassage 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 CloudPassage의 관련 사용자 간에 연결이 형성되어야 합니다.<br> 이 연결 관계는 Azure AD의 **사용자 이름** 값을 CloudPassage의 **Username** 값으로 할당하여 설정합니다.
 
CloudPassage에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-On 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[CloudPassage 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 CloudPassage에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션은 Azure AD 포털에서 Azure AD Single Sign-on을 사용하도록 설정하고 CloudPassage에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다.<br> CloudPassage 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다. <br><br> ![Single Sign-On 구성][21]

**CloudPassage에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure AD 포털의 **CloudPassage** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.<br><br> ![Single Sign-On 구성][7]

2. **CloudPassage에 대한 사용자 로그온 방법 선택** 페이지에서 **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.<br><br> ![Single Sign-On 구성][8]

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.<br><br>![앱 설정 구성][9]
 
     3\.1. **로그온 URL** 텍스트 상자에 사용자가 CloudPassage 앱에 로그인하는 데 사용하는 URL을 입력합니다.(예: *https://portal.cloudpassage.com/saml/init/accountid*))

     3\.2. URL 회신 텍스트 상자에 AssertionConsumerService URL(예: *https://portal.cloudpassage.com/saml/consume/accountid*))을 입력합니다. <br> CloudPassage 포털의 **Single Sign-On 설정** 섹션에서 **SSO 설치 설명서**를 클릭하여 이 특성의 값을 가져올 수 있습니다. <br><br>![Single Sign-On 구성][10]

     3\.3. **다음**을 클릭합니다.



4. **CloudPassage에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다. <br><br>![Single Sign-On 구성][11]

5. 다른 브라우저 창에서 CloudPassage 회사 사이트에 관리자 권한으로 로그인합니다.

6. 위쪽 메뉴에서 **설정**을 클릭한 다음 **사이트 관리**를 클릭합니다. <br><br> ![Single Sign-On 구성][12]

7. **인증 설정** 탭을 클릭합니다. <br><br> ![Single Sign-On 구성][13]


8. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다. <br><br> ![Single Sign-On 구성][14]


     8\.1. Azure 포털의 **CloudPassage에 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **SAML 발급자 URL** 텍스트 상자에 붙여넣습니다.

     8\.2. Azure 포털의 **CloudPassage에서 Single Sign-On 구성** 대화 상자 페이지에서 **SP(서비스 공급자) 시작 끝점** 값을 복사한 다음 **SAML 끝점 URL** 텍스트 상자에 붙여넣습니다.

     8\.3. Azure 포털의 **CloudPassage에서 Single Sign-On 구성** 대화 상자 페이지에서 **로그아웃 URL** 값을 복사한 다음 **로그아웃 방문 페이지** 텍스트 상자에 붙여넣습니다.

     8\.4. 다운로드한 인증서에서 **Base-64**로 인코드된 파일을 만듭니다.
          
      >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하세요.

     8\.5. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **X.509 인증서** 텍스트 상자에 붙여넣습니다.

     8\.6. **Save**를 클릭합니다.


9. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.<br><br> ![Single Sign-On 구성][15]


10. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다. <br><br> ![Single Sign-On 구성][16]



11. 위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다. <br><br> ![Single Sign-On 구성][17]

12. 필요한 사용자 특성을 추가하려면 아래 표의 각 행에 대해 다음 단계를 수행합니다. <br>

| 특성 이름 | 특성 값 |
| --- | --- |
| firstname | user.givenname |
| lastname | user.surname |
| email | user.mail |  

     12.1. Click **add user attribute**. <br><br> ![Configure Single Sign-On][18]

     12.2. In the **Attribute Name** textbox, type the attribute name shown for that row and as **Attribure Value**, select the attribute value shown for that row . <br><br> ![Configure Single Sign-On][19]
     
     12.2.3 Click **Complete**.


13. 아래쪽 도구 모음에서 **변경 내용 적용**을 클릭합니다. <br><br> ![Single Sign-On 구성][20]



### Azure AD 테스트 사용자 만들기

이 섹션은 Azure 포털에서 Britta Simon이라는 테스트 사용자를 만들기 위한 것입니다.<br><br> 사용자 목록에서 **Britta Simon**을 선택합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_01.png)

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.<br> ![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_02.png) 

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_03.png)
 
4. **사용자 추가** 대화 상자를 열려면 아래쪽의 도구 모음에서 **사용자 추가**를 클릭합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_05.png)
  1. 사용자 유형에서 조직의 새 사용자를 선택합니다.
  2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
  3. 다음을 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다. <br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_06.png)
  1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
  2. **성** 텍스트 상자에 **Simon**을 입력합니다.
  3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
  4. **역할** 목록에서 **사용자**를 선택합니다.
  5. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_07.png)
 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.<br>![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-cloudpassage-tutorial/create_aaduser_08.png)
  1. **새 암호** 값을 적어둡니다.
  2. **완료**를 클릭합니다.   


  
 
### CloudPassage 테스트 사용자 만들기

이 섹션은 CloudPassage에서 Britta Simon이라는 사용자를 만들기 위한 것입니다.

#### CloudPassage에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.

1.	**CloudPassage** 회사 사이트에 관리자 권한으로 로그인합니다. 

2.	위쪽 도구 모음에서 **설정**을 클릭한 다음 **사이트 관리**를 클릭합니다. <br>![CloudPassage 테스트 사용자 만들기][22]

3.	**사용자** 탭을 클릭한 다음 **새 사용자 추가**를 클릭합니다. <br>![CloudPassage 테스트 사용자 만들기][23]
	
4.	**새 사용자 추가** 섹션에서 다음 단계를 수행합니다. <br>![CloudPassage 테스트 사용자 만들기][24]

     4\.1. **이름** 텍스트 상자에 Britta를 입력합니다.

     4\.2. **성** 텍스트 상자에 Simon을 입력합니다.

     4\.3. **사용자 이름** 텍스트 상자, **메일** 텍스트 상자 및 **메일 다시 입력** 텍스트 상자에 Azure AD의 Britta 사용자 이름을 입력합니다.

     4\.4. **액세스 유형**으로 **Halo 포털 액세스 사용**을 선택합니다.

     4\.5. **추가**를 클릭합니다.










### Azure AD 테스트 사용자 할당

이 섹션은 Britta Simon에게 CloudPassage에 대한 액세스 권한을 부여하여 Azure Single Sign-On을 사용하도록 하기 위한 것입니다. <br><br>![사용자 할당][30]

**Britta Simon을 CloudPassage에 할당하려면 다음 단계를 수행합니다.**

1. Azure 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.<br> <br><br>![사용자 할당][26]
2. 응용 프로그램 목록에서 **CloudPassage**를 선택합니다. <br><br>![사용자 할당][27]
1. 위쪽 메뉴에서 **사용자**를 클릭합니다.<br> <br><br>![사용자 할당][25]
1. 사용자 목록에서 **Britta Simon**을 선택합니다.

2. 아래쪽 도구 모음에서 **할당**을 클릭합니다. <br><br>![사용자 할당][29]



### Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다.<br> 액세스 패널에서 CloudPassage 타일을 클릭하면 CloudPassage 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_01.png
[6]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_02.png
[7]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_05.png
[8]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_03.png
[9]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_04.png
[10]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_05.png
[11]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_06.png
[12]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_10.png
[16]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_11.png
[17]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_10.png
[18]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_11.png
[19]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_12.png
[20]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_14.png
[21]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_14.png
[22]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_17.png
[25]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_15.png
[26]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_12.png
[27]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_13.png
[28]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_cloudpassage_15.png
[29]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_16.png
[30]: ./media/active-directory-saas-cloudpassage-tutorial/tutorial_general_17.png

<!---HONumber=Oct15_HO3-->