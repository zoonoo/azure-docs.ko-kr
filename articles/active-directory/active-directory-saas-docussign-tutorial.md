<properties
	pageTitle="자습서: DocuSign과 Azure Active Directory 통합 | Microsoft Azure"
	description="Azure Active Directory 및 DocuSign 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다."
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
	ms.date="10/06/2015"
	ms.author="markvi"/>


# 자습서: DocuSign와 Azure Active Directory 통합

이 자습서는 Azure 및 DocuSign의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

- 유효한 Azure 구독
- DocuSign의 테넌트



이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1. [DocuSign에 응용 프로그램 통합 사용](#enabling-the-application-integration-for-docusign) 


2. [Single Sign-On 구성](#configuring-single-sign-on)


3. [계정 프로비전 구성](#configuring-account-provisioning)


4. [사용자 할당](#assigning-users)




<br><br>![Single Sign-On 구성][0]<br>


 

## DocuSign에 응용 프로그램 통합 사용

이 섹션은 DocuSign에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

### DocuSign에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. <br><br>![Single Sign-On 구성][1]<br>

2. 디렉터리 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3. 응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.<br><br>![Single Sign-On 구성][2]<br>

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.<br><br>![응용 프로그램][3]<br>

5. 원하는 작업을 선택하세요. 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.<br><br>![Single Sign-On 구성][4]<br>


6. 검색 상자에 **Docusign**를 입력합니다.<br><br>![Single Sign-On 구성][5]<br>

7. 결과 창에서 **Docusign**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다. <br><br>![Single Sign-On 구성][6]<br>




## Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Docusign에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.


### Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1. Azure AD 포털의 **Docusign 응용 프로그램 통합** 페이지에서 **Single Sign-On 구성**을 클릭하여 Single Sign-On 구성 대화 상자를 엽니다. <br><br>![Single Sign-On 구성][7]<br>

2. **Docusign에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 다음을 클릭합니다. <br><br>![Single Sign-On 구성][8]<br>

3. **앱 URL 구성** 페이지에 있는 **Docusign 로그인 URL** 텍스트 상자에 Docusign 테넌트를 입력하고 **다음**을 클릭합니다. URL에는 다음 스키마가 있습니다. *https://<yourcompanyname>.docusign.net/Member/MemberLogin.aspx?ssoname=<yourSSOInstanceName>* <br><br>![Single Sign-On 구성][9]<br>


    > [AZURE.TIP]테넌트에 대한 앱 URL이 무엇인지 모르는 경우 SSOSetup@Docusign.com을 통해 Docusign에 연결하여 테넌트에 SP에서 시작한 SSO URL을 가져옵니다.
 

4. **Docusign에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.<br><br>![Single Sign-On 구성][10]<br>


5. 다른 웹 브라우저 창에서 **Docusign** 회사 사이트에 관리자로 로그인합니다.


6. 위쪽에 있는 메뉴에서 사용자의 메뉴를 확장하고 **기본 설정**을 클릭한 다음 왼쪽의 탐색창에서 **계정 관리**를 확장하고 **기능**을 클릭합니다. <br><br>![Single Sign-On 구성][11]<br>

7. **SAML 구성**을 클릭한 다음 **SAML 구성** 링크를 클릭합니다.



8. **SAML 2.0 구성** 섹션에서 다음 단계를 수행합니다. <br><br>![Single Sign-On 구성][13]<br>


    a. Azure 포털의 **Docusign에서 Single Sign-On 구성** 대화 상자 페이지에서 발급자 URL** 값을 복사한 다음 **ID 공급자 끝점 URL** 텍스트 상자에 붙여넣습니다.

    > [AZURE.IMPORTANT]이 구성 옵션을 사용할 수 없는 경우 Docusign 계정 관리자에게 문의하거나 메일을 통해 SSO 지원팀에 문의하세요.([SSOSetup@docusign.com](mailto:SSOSetup@docusign.com))
 
    b. 다운로드한 인증서를 업로드하려면 **찾아보기**를 클릭합니다.


    c. **이름, 성 및 메일 주소 사용**을 선택합니다.


    d. **저장**을 클릭합니다.


9. Azure AD 포털에서 **Single Sign-On 구성 확인**을 선택하고 **다음**을 클릭합니다.<br><br>![응용 프로그램][14]<br>

10. **Single Sign-on 확인** 페이지에서 **완료**를 클릭합니다. <br><br>![응용 프로그램][15]<br>


 

## 계정 프로비전 구성

이 섹션에서는 DocuSign에 Active Directory 사용자 계정을 프로비전할 수 있도록 설정하는 방법을 간략하게 설명합니다.

### 사용자 프로비전을 구성하려면

1. **Azure 관리 포털**의 **DocuSign 응용 프로그램 통합** 페이지에서 **계정 프로비전 구성**을 클릭하여 사용자 프로비전 구성 대화 상자를 엽니다.<br><br>![계정 프로비전 구성][30]<br>
 

2. **설정 및 관리자 자격 증명** 페이지에서 자동 사용자 프로비전을 사용하려면 충분한 권한이 있는 DocuSign 계정의 자격 증명을 제공한 후 **다음**을 클릭합니다. <br><br>![계정 프로비전 구성][31]<br>

3. **테스트 연결** 대화 상자에서 **테스트 시작**을 클릭하고 테스트가 성공하면 **다음**을 클릭합니다. <br><br>![계정 프로비전 구성][32]<br>

3. **확인** 페이지에서 **완료**를 클릭합니다.

<br><br>![계정 프로비전 구성][33]<br>
 

## 사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

### Docusign에 사용자를 할당하려면 다음 단계를 수행합니다.

1. **Azure AD 포털**에서 테스트 계정을 만듭니다.

2. **Docusign 응용 프로그램 통합** 페이지에서 **사용자 할당**을 클릭합니다. <br><br>![사용자 할당][40]<br>
 

3. 테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

<br><br>![사용자 할당][41]<br>


이제 10분 동안 기다린 후 계정이 Docusign에 동기화되었는지 확인해야 합니다.

첫 번째 확인 단계로 Azure 관리 포털의 Docusign 응용 프로그램 통합 페이지에서 D의 대시보드를 클릭하여 프로비전 상태를 확인할 수 있습니다. <br><br>![사용자 할당][42]<br>

주기를 프로비전하는 성공적으로 완료된 사용자는 관련된 상태에서 표시됩니다. <br><br>![사용자 할당][43]<br>


Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다.

액세스 패널에 대한 자세한 내용은 액세스 패널 소개를 참조하세요.





## 추가 리소스

* [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
* [Azure Active Directory로 응용 프로그램 액세스 및 Single Sign-On이란 무엇입니까?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[0]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_00.png
[1]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_04.png
[5]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_01.png
[6]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_02.png
[7]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_03.png
[8]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_04.png
[9]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_05.png
[10]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_06.png
[11]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_07.png

[13]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_09.png
[14]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_10.png
[15]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_11.png

[30]: ./media/active-directory-saas-docussign-tutorial/tutorial_general_400.png
[31]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_12.png
[32]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_13.png
[33]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_14.png



[40]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_15.png
[41]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_16.png
[42]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_17.png
[43]: ./media/active-directory-saas-docussign-tutorial/tutorial_docusign_18.png

<!---HONumber=Oct15_HO2-->