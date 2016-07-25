<properties 
    pageTitle="자습서: Adobe EchoSign과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Adobe EchoSign을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#자습서: Adobe EchoSign과 Azure Active Directory 통합

이 자습서는 Azure 및 Adobe EchoSign의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Adobe EchoSign Single sign on이 설정된 구독

이 자습서를 완료한 후 Adobe EchoSign에 할당한 Azure AD 사용자가 Adobe EchoSign 회사 사이트 (서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Adobe EchoSign에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-adobe-echosign-tutorial/IC789511.png "시나리오")
##Adobe EchoSign에 응용 프로그램 통합 사용

이 섹션은 Adobe EchoSign에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Adobe EchoSign에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-adobe-echosign-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-adobe-echosign-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-adobe-echosign-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-adobe-echosign-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색** 상자에 **Adobe EchoSign**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-adobe-echosign-tutorial/IC789514.png "응용 프로그램 갤러리")

7.  결과 창에서 **Adobe EchoSign**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Adobe EchoSign](./media/active-directory-saas-adobe-echosign-tutorial/IC789515.png "Adobe EchoSign")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Adobe EchoSign에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base 64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Adobe EchoSign** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-adobe-echosign-tutorial/IC789516.png "Single Sign-On 구성")

2.  **Adobe EchoSign에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-adobe-echosign-tutorial/IC789517.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Adobe EchoSign Sign On URL** 텍스트 상자에 다음 패턴 "*https://company.echosign.com/*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-adobe-echosign-tutorial/IC789518.png "앱 URL 구성")

4.  **Adobe EchoSign에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-adobe-echosign-tutorial/IC789519.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Adobe EchoSign 회사 사이트에 관리자로 로그인합니다.

6.  위쪽의 메뉴에서 **계정**을 클릭한 다음 왼쪽 주사위의 탐색 창에서 **계정 설정**의 **SAML 설정**을 클릭합니다.

    ![계정](./media/active-directory-saas-adobe-echosign-tutorial/IC789520.png "계정")

7.  SAML 설정 섹션에서 다음 단계를 수행합니다.

    ![SAML 설정](./media/active-directory-saas-adobe-echosign-tutorial/IC789521.png "SAML 설정")

    1.  **SAML 모드**로 **SAML 필수**를 선택합니다.
    2.  **EchoSign 계정 관리자가 EchoSign 자격 증명을 사용하여 로그인할 수 있게 허용**을 선택합니다.
    3.  **사용자 만들기**로 **SAML을 통해 인증된 사용자를 자동으로 추가**를 선택합니다.

8.  다음 단계를 수행하기 위해 이동합니다.

    ![SAML 설정](./media/active-directory-saas-adobe-echosign-tutorial/IC789522.png "SAML 설정")

    1.  Azure 클래식 포털의 **Adobe EchoSign에서 Single Sign-On 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **IdP 엔터티 ID** 텍스트 상자에 붙여넣습니다.
    2.  Azure 클래식 포털의 **Adobe EchoSign에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **IdP 로그인 URL** 텍스트 상자에 붙여넣습니다.
    3.  Azure 클래식 포털의 **Adobe EchoSign에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **IdP 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    4.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

		>[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.
    5.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **IdP 인증서** 텍스트 상자에 붙여 넣습니다.
    6.  **변경 내용 저장**을 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-adobe-echosign-tutorial/IC789523.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Adobe EchoSign에 로그인할 수 있도록 하려면 Adobe EchoSign으로 프로비전되어야 합니다. Adobe EchoSign의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **Adobe EchoSign** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  위쪽의 메뉴에서 **계정**을 클릭한 다음 왼쪽 주사위의 탐색 창에서 **사용자 및 그룹**과 **새 사용자 만들기**를 차례로 클릭합니다.

    ![계정](./media/active-directory-saas-adobe-echosign-tutorial/IC789524.png "계정")

3.  **새 사용자 만들기** 섹션에서 다음 단계를 수행합니다.

    ![사용자 만들기](./media/active-directory-saas-adobe-echosign-tutorial/IC789525.png "사용자 만들기")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **전자 메일 주소**, **이름** 및 **성**을 입력합니다.
    2.  **사용자 만들기**를 클릭합니다.

		>[AZURE.NOTE] Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.

>[AZURE.NOTE] 다른 Adobe EchoSign 사용자 계정 생성 도구 또는 Adobe EchoSign가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Adobe EchoSign에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Adobe EchoSign** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-adobe-echosign-tutorial/IC789526.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-adobe-echosign-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->