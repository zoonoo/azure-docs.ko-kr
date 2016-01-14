<properties
	pageTitle="자습서: Halogen Software와 Azure Active Directory 통합"
	description="Azure Active Directory 및 Halogen Software 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
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
	ms.date="12/18/2015"
	ms.author="jeedes"/>


# 자습서: Halogen Software와 Azure Active Directory 통합

이 자습서는 Azure AD(Azure Active Directory)에 Halogen Software를 통합하는 방법을 보여 주기 위해 제공되었습니다.<br>Azure AD에 Halogen Software를 통합하면 다음과 같은 이점을 얻을 수 있습니다.

- Halogen Software에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. 
- 사용자가 해당 Azure AD 계정으로 Halogen Software에 자동으로 로그온(Single Sign-on)되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Active Directory 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건 

Halogen Software와의 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- Halogen Software Single Sign-on이 설정된 구독


> [AZURE.NOTE]이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다. 

 
## 시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다. <br>
이 자습서에 설명된 시나리오는 다음 두 가지 주요 구성 요소로 이루어져 있습니다.

1. 갤러리에서 Halogen Software 추가 
2. Azure AD Single Sign-on 구성 및 테스트


## 갤러리에서 Halogen Software 추가
Halogen Software의 Azure AD 통합을 구성하려면 갤러리의 Halogen Software를 관리되는 SaaS 앱 목록에 추가해야 합니다.

**갤러리에서 Halogen Software를 추가하려면 다음 단계를 수행합니다.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. <br><br>
![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.<br><br>
![응용 프로그램][2]
4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.<br><br>
![응용 프로그램][3]
5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.<br><br>
![응용 프로그램][4]
6. 검색 상자에 **halogen software**를 입력합니다.<br>
![응용 프로그램][5]
7. 결과 창에서 **Halogen Software**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.<br>



##  Azure AD Single Sign-on 구성 및 테스트
이 섹션은 "Britta Simon"이라는 테스트 사용자를 기반으로 Halogen Software에서 Azure AD Single Sign-on을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다.

Single Sign-on이 작동되려면 Azure AD는 Azure AD의 사용자에 해당하는 Halogen Software의 사용자가 누군지 알고 있어야 합니다. 즉, Azure AD 사용자와 Halogen Software의 관련 사용자 간에 연결이 형성되어야 합니다.<br>
이 연결 관계는 Azure AD의 **사용자 이름** 값을 Halogen Software의 **Username** 값으로 할당하여 설정합니다.
 
Halogen Software에서 Azure AD Single Sign-on을 구성하고 테스트하려면 다음 구성 요소를 완료해야 합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-on 테스트하는 데 사용합니다.
4. **[Halogen Software 테스트 사용자 만들기](#creating-a-halogen-software-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Halogen Software에 만듭니다.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Single Sign-on 테스트](#testing-single-sign-on)** - 구성이 작동하는지 여부를 확인합니다.

### Azure AD Single Sign-on 구성

이 섹션은 Azure AD 포털에서 Azure AD Single Sign-on을 사용하도록 설정하고 Halogen Software에서 Single Sign-on을 구성하는 방법을 설명하기 위해 제공되었습니다.<br>

**Halogen Software에서 Azure AD Single Sign-on을 구성하려면 다음 단계를 수행합니다.**

1. Azure AD 포털의 **Halogen Software** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.<br><br>
![Single Sign-On 구성][8]

2. **Halogen Software에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Azure AD Single Sign-on**을 선택하고**다음**을 클릭합니다.<br><br>
![Azure AD Single Sign-On][9]

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.<br><br>![앱 설정 구성][10]
 
     3\.1 **로그온 URL** 텍스트 상자에 *https://global.hgncloud.com/fabrikam/welcome.jsp* 패턴을 사용하여 사용자가 Halogen Software 응용 프로그램에 로그온하는 데 사용하는 URL을 입력합니다.

     3\.2. **다음**을 클릭합니다.
 
4. **Halogen Software에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬로 메타데이터 파일을 저장합니다.<br><br>![Azure AD Connect의 정의][11]

5. 다른 브라우저 창에서 **Halogen Software** 응용 프로그램에 관리자 권한으로 로그온합니다.
6. **옵션** 탭을 클릭합니다. <br><br>![Azure AD Connect의 정의][12]
7. 왼쪽 탐색 창에서 **SAML 구성**을 클릭합니다. <br><br>![Azure AD Connect의 정의][13]
8. **SAML 구성** 페이지에서 다음 단계를 수행합니다. <br><br>![Azure AD Connect의 정의][14]

     8\.1. **고유 식별자**로 **NameID**를 선택합니다.

     8\.2. **고유 식별자 매핑 대상**으로 **Username**을 선택합니다.

     8\.3. 다운로드한 메타데이터 파일을 업로드하려면 **찾아보기**를 클릭하여 파일을 선택하고 **파일 업로드**를 클릭합니다.

     8\.4. 구성을 테스트하려면 **테스트 실행**을 클릭합니다. 

> [AZURE.NOTE]"*SAML 테스트가 완료되었습니다. 이 창을 닫으십시오.*" 메시지가 표시될 때까지 기다려야 합니다. 그런 다음 열려 있는 브라우저 창을 닫습니다. <br> **SAML 사용** 확인란은 테스트가 완료된 경우에만 사용하도록 설정됩니다.

     8\.5. **SAML 사용**을 선택합니다.
    
     8\.6. **변경 내용 저장**을 클릭합니다.


9. Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다. <br><br>![Azure AD Connect의 정의][15]
10. **Single Sign-on 확인** 페이지에서 **완료**를 클릭합니다. <br><br>![Azure AD Connect의 정의][16]




### Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 관리 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.
<br><br>![Azure AD Connect의 정의][100] 
2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.
3. 사용자 목록을 표시하려면 위쪽의 메뉴에서 **사용자**를 클릭합니다.
<br><br>![Azure AD Connect의 정의][101] 
4. **사용자 추가** 대화 상자를 열려면 아래쪽의 도구 모음에서 **사용자 추가**를 클릭합니다.
<br><br>![Azure AD Connect의 정의][102] 
5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.
<br><br>![Azure AD Connect의 정의][103] 
  1. **사용자 유형**에서 **조직의 새 사용자**를 선택합니다.
  2. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.
  3. 다음을 클릭합니다.
6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.
<br><br>![Azure AD Connect의 정의][104] 
  1. **이름** 텍스트 상자에 **Britta**를 입력합니다.  
  2. **성** 텍스트 상자에 **Simon**을 입력합니다.
  3. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.
  4. **역할** 목록에서 **사용자**를 선택합니다.
  5. **다음**을 클릭합니다.
7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.
<br><br>![Azure AD Connect의 정의][105]  
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.
<br><br>![Azure AD Connect의 정의][106]   
  1. **새 암호** 값을 적어둡니다.
  2. **완료**를 클릭합니다.   
  
 
### Halogen Software 테스트 사용자 만들기

이 섹션의 목적은 Halogen Software에서 Britta Simon이라는 사용자를 만드는 것입니다.

**Halogen Software에서 Britta Simon이라는 사용자를 만들려면 다음 단계를 수행합니다.**

1. **Halogen Software** 응용 프로그램에 관리자 권한으로 로그온합니다.
2. **사용자 센터** 탭을 클릭한 후 **사용자 만들기**를 클릭합니다.
<br><br>![Azure AD Connect의 정의][300]  
3. **새 사용자** 대화 상자 페이지에서 다음 단계를 수행합니다.
<br><br>![Azure AD Connect의 정의][301]
  1. **이름** 텍스트 상자에 **Britta**를 입력합니다. 
  2. **성** 텍스트 상자에 **Simon**을 입력합니다.
  3. **사용자 이름** 텍스트 상자에 **Azure AD 포털의 Brita Simon 사용자 이름**을 입력합니다.
  4. **암호** 텍스트 상자에 Britta에 대한 암호를 입력합니다.
  5. **Save**를 클릭합니다.


### Azure AD 테스트 사용자 할당

이 섹션의 목적은 Britta Simon에게 Halogen Software에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다.
<br><br>![Azure AD Connect의 정의][200]

**Britta Simon을 Halogen Software에 할당하려면 다음 단계를 수행합니다.**

1. Azure 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.<br>
<br><br>![Azure AD Connect의 정의][201]
2. 응용 프로그램 목록에서 **Halogen Software**를 선택합니다.
<br><br>![Azure AD Connect의 정의][202]
1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.<br>
<br><br>![Azure AD Connect의 정의][203]
1. 사용자 목록에서 **Britta Simon**을 선택합니다.
<br><br>![Azure AD Connect의 정의][204]
2. 아래쪽 도구 모음에서 **할당**을 클릭합니다.
<br><br>![Azure AD Connect의 정의][205]



### Single Sign-On 테스트

이 섹션의 목적은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하는 것입니다.<br>
액세스 패널에서 Halogen Software 타일을 클릭하면 Halogen Software 응용 프로그램에 자동으로 로그온됩니다.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->
[1]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_01.png
[2]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_02.png
[3]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_03.png
[4]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_04.png
[5]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_05.png
[6]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_06.png
[7]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_07.png
[8]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_08.png
[9]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_09.png
[10]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_10.png
[11]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_11.png
[12]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_12.png
[13]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_13.png
[14]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_14.png
[15]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_15.png
[16]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_16.png
[100]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_100.png
[101]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_101.png
[102]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_102.png
[103]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_103.png
[104]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_104.png
[105]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_105.png
[106]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_106.png
[200]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_200.png
[201]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_201.png
[202]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_202.png
[203]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_203.png
[204]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_204.png
[205]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_205.png
[300]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_300.png
[301]: ./media/active-directory-saas-halogen-software-tutorial/tutorial_halogen_301.png

<!---HONumber=AcomDC_1223_2015-->

