<properties
	pageTitle="자습서: O.C.와 Azure Active Directory 통합 Tanner - AppreciateHub | Microsoft Azure"
	description="Azure Active Directory와 O.C. 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다. Tanner - AppreciateHub."
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
	ms.date="08/16/2016"
	ms.author="jeedes"/>


# 자습서: O.C.와 Azure Active Directory 통합 Tanner - AppreciateHub

이 자습서에서는 O.C.를 통합하는 방법을 보여줍니다. Tanner - Azure AD(Azure Active Directory)가 AppreciateHub. O.C. 통합 Tanner - Azure AD가 포함된 AppreciateHub는 다음과 같은 이점을 제공합니다.

- O.C.에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다. Tanner - AppreciateHub
- 사용자가 자동으로 O.C.에 로그온하도록 설정할 수 있습니다. Tanner - Azure AD 계정이 포함된 AppreciateHub(Single Sign-On)
- 단일 중앙 위치인 Azure 클래식 포털에서 계정을 관리할 수 있습니다.

Azure AD와의 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.

## 필수 조건 

O.C.와 Azure AD 통합을 구성하려면 Tanner - AppreciateHub, 다음 사항이 필요합니다.

- Azure AD 구독
- O.C. Tanner - AppreciateHub Single-Sign On이 설정된 구독


> [AZURE.NOTE] 이 자습서의 단계를 테스트하기 위해 프로덕션 환경을 사용하는 것은 바람직하지 않습니다.


이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 않도록 합니다.
- Azure AD 평가판 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 얻을 수 있습니다.

 
## 시나리오 설명
이 자습서는 테스트 환경에서 Azure AD Single Sign-on을 테스트하는 데 도움을 주기 위해 제공되었습니다. 이 자습서에 설명된 시나리오는 다음 세 가지 주요 구성 요소로 이루어져 있습니다.

1. O.C. 추가 Tanner - 갤러리에서 AppreciateHub
2. Azure AD Single Sign-on 구성 및 테스트


## O.C. 추가 Tanner - 갤러리에서 AppreciateHub
O.C.의 통합을 구성하려면 Tanner - Azure AD에서 AppreciateHub, O.C.를 추가해야 합니다. Tanner - 갤러리에서 관리된 SaaS 앱 목록에 AppreciateHub

**O.C.를 추가하려면 Tanner - 갤러리에서 AppreciateHub, 다음 단계를 수행합니다.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Active Directory][1]

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램][2]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![응용 프로그램][3]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![응용 프로그램][4]

6. 검색 상자에 **O.C.를 입력합니다. Tanner - AppreciateHub**.

	![응용 프로그램][5]

7. 결과 창에서 **O.C.를 선택합니다. Tanner - AppreciateHub**를 선택한 후 **Complete**를 클릭하여 응용 프로그램을 추가합니다.

	![응용 프로그램][25]




##  Azure AD Single Sign-on 구성 및 테스트

이 섹션은 O.C.에서 Azure AD Single Sign-On을 구성하고 테스트하는 방법을 보여 주기 위해 작성되었습니다. Tanner - "Britta Simon"이라는 테스트 사용자를 기반으로 한 AppreciateHub.

Single Sign-On이 작동하려면 Azure AD는 O.C.에서 해당 사용자가 누군지 알고 있어야 합니다. Tanner - Azure AD의 사용자에 AppreciateHub. 즉, Azure AD 사용자와 O.C.의 관련 사용자 간에 연결이 형성되어야 합니다. Tanner - AppreciateHub를 설정해야 합니다. 이 연결 관계는 Azure AD의 **사용자 이름** 값을 O.C.의 **Username** 값으로 할당하여 설정합니다. Tanner - AppreciateHub.
 
O.C.를 사용하여 Azure AD Single Sign-On을 구성하고 테스트하려면 Tanner - AppreciateHub, 다음과 같은 구성 블록을 완료하는데 필요합니다.

1. **[Azure AD Single Sign-on 구성](#configuring-azure-ad-single-single-sign-on)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Azure AD 테스트 사용자 만들기](#creating-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On 테스트하는 데 사용합니다.
4. **[O.C. 만들기 Tanner - O.C.에서 Britta Simon에 해당하는 사용자가 있는 AppreciateHub 테스트 사용자](#creating-a-halogen-software-test-user)**. Tanner - 해당 사용자의 Azure AD 표현에 연결된 AppreciateHub.
5. **[Azure AD 테스트 사용자 할당](#assigning-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
5. **[Single Sign-On 테스트](#testing-single-sign-on)** - 구성이 작동하는지 확인합니다.

### Azure AD Single Sign-On 구성

이 섹션은 Azure 클래식 포털에서 Azure AD Single Sign-On을 사용하도록 설정하고 O.C에서 Single Sign-On을 구성하는 방법을 설명하기 위한 것입니다. Tanner - AppreciateHub 응용 프로그램.


**O.C.를 사용하여 Azure AD Single Sign-On을 구성하려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

1. Azure 클래식 포털의 **O.C. Tanner - AppreciateHub** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

	![Single Sign-On 구성][6]

2. **사용자가 O.C.에 어떻게 로그온하는지에서 Tanner - AppreciateHub** 페이지, **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Azure AD Single Sign-On][7]

3. **앱 설정 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![앱 설정 구성][8]
 
     a. 다음 링크를 사용하여 메타데이터 파일을 엽니다. [https://fed.appreciatehub.com/fed/sp/metadata](https://fed.appreciatehub.com/fed/sp/metadata)

     b. **md:AssertionConsumerService** 노드를 찾습니다.

     c. **위치** 특성의 값을 복사합니다.

     ![앱 설정 구성][12]
     
     d. **Sign-on URL** 텍스트 상자에서 이전 단계에 얻은 값보다 큽니다.

     > [AZURE.NOTE] 메타데이터 파일에서 회신 URL을 가져오는 문제가 발생하는 경우 O.C.에 문의합니다. Tanner - [sso@octanner.com](mailto:sso@octanner.com)을 통한 AppreciateHub 지원팀.

     e. **Next**를 클릭합니다.
 
4. O.C.의 **Single Sign-On 구성에서 Tanner - AppreciateHub** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬로 메타데이터 파일을 저장합니다.

	![Azure AD Connect의 정의][9]

5. O.C.에 문의 Tanner - xyz를 통한 AppreciateHub 지원팀, 메타데이터 파일을 제공하고 사용자를 위해 SSO를 사용하도록 해야 한다는 점을 통보합니다.


6. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **다음**을 클릭합니다.

	![Azure AD Connect의 정의][10]

7. **Single Sign-On 확인** 페이지에서 **완료**를 클릭합니다.

	![Azure AD Connect의 정의][11]




### Azure AD 테스트 사용자 만들기
이 섹션의 목적은 Azure 클래식 포털에서 Britta Simon이라는 테스트 사용자를 만드는 것입니다.

![Azure AD 사용자 만들기][20]

**Azure AD에서 테스트 사용자를 만들려면 다음 단계를 수행하세요.**

1. **Azure 클래식 포털**의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_02.png)

2. **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 사용자 목록을 표시하려면 위쪽 메뉴에서 **사용자**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_03.png)
 
4. **사용자 추가** 대화 상자를 열려면 아래쪽 도구 모음에서 **사용자 추가**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_04.png)

5. **이 사용자에 대한 정보 입력** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_05.png)

    a. 사용자 유형에서 조직의 새 사용자를 선택합니다.

    b. 사용자 이름 **텍스트 상자**에 **BrittaSimon**을 입력합니다.

    c. **Next**를 클릭합니다.

6.  **사용자 프로필** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_06.png)
 
    a. **이름** 텍스트 상자에 **Britta**를 입력합니다.

    b. **성** 텍스트 상자에 **Simon**을 입력합니다.

    c. **표시 이름** 텍스트 상자에 **Britta Simon**을 입력합니다.

    d. **역할** 목록에서 **사용자**를 선택합니다. e. **다음**을 클릭합니다.

7. **임시 암호 가져오기** 대화 상자 페이지에서 **만들기**를 클릭합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_07.png)
 
8. **임시 암호 가져오기** 대화 상자 페이지에서 다음 단계를 수행합니다.

	![Azure AD 테스트 사용자 만들기](./media/active-directory-saas-oc-tanner-tutorial/create_aaduser_08.png)
  
    a. **새 암호** 값을 적어둡니다.

    b. **완료**를 클릭합니다.

  
 
### O.C. 만들기 Tanner - AppreciateHub 테스트 사용자.

이 섹션은 O.C.에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Tanner - AppreciateHub.

**O.C.에서 Britta Simon이라는 사용자를 만들려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

1. OC Tanner 지원팀에 요청하여 Azure AD에서 Britta Simon이라는 사용자 이름이 동일한 값인 nameID 특성을 가진 사용자를 만듭니다.


### Azure AD 테스트 사용자 할당

이 섹션의 목적은 Britta Simon에게 O.C.에 대한 액세스 권한을 부여하여 Single Sign-On을 사용할 수 있도록 하는 것입니다. Tanner - AppreciateHub.

![사용자 할당][200]

**Britta Simon를 O.C.에 할당하려면 Tanner - AppreciateHub, 다음 단계를 수행합니다.**

1. Azure 클래식 포털에서 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

	![사용자 할당][201]

2. 응용 프로그램 목록에서 **O.C.를 선택합니다. Tanner - AppreciateHub**.

	![사용자 할당][202]

1. 위쪽의 메뉴에서 **사용자**를 클릭합니다.

	![사용자 할당][203]

1. 사용자 목록에서 **Britta Simon**을 선택합니다.

2. 아래쪽 도구 모음에서 **할당**을 클릭합니다.

	![사용자 할당][205]



### Single Sign-On 테스트

이 섹션은 액세스 패널을 사용하여 Azure AD Single Sign-On 구성을 테스트하기 위한 것입니다. O.C.를 클릭할 때 Tanner - 액세스 패널에서 AppreciateHub 타일, O.C.에 자동으로 로그온되야 합니다. Tanner - AppreciateHub 응용 프로그램.


## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)


<!--Image references-->
[1]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_01.png
[25]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_25.png


[6]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_05.png
[7]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_02.png
[8]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_03.png
[9]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_04.png
[10]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_05.png
[11]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_06.png
[12]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_08.png
[20]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_octanner_07.png
[203]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-oc-tanner-tutorial/tutorial_general_205.png

<!---HONumber=AcomDC_0817_2016-->