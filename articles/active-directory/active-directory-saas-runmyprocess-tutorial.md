<properties 
    pageTitle="자습서: RunMyProcess와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 RunMyProcess를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="07/07/2016" 
    ms.author="jeedes" />

#자습서: RunMyProcess와 Azure Active Directory 통합
  
이 자습서는 Azure 및 RunMyProcess의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   RunMyProcess 테넌트
  
이 자습서를 완료한 후 RunMyProcess에 할당한 Azure AD 사용자가 RunMyProcess 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  RunMyProcess에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-runmyprocess-tutorial/IC789614.png "시나리오")
##RunMyProcess에 응용 프로그램 통합 사용
  
이 섹션은 RunMyProcess에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###RunMyProcess에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-runmyprocess-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-runmyprocess-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-runmyprocess-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-runmyprocess-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **RunMyProcess**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-runmyprocess-tutorial/IC789615.png "응용 프로그램 갤러리")

7.  결과 창에서 **RunMyProcess**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![RunMyProcess](./media/active-directory-saas-runmyprocess-tutorial/IC789616.png "RunMyProcess")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 RunMyProcess에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **RunMyProcess** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-runmyprocess-tutorial/IC789617.png "Single Sign-On 구성")

2.  **RunMyProcess에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-runmyprocess-tutorial/IC789622.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **RunMyProcess Sign-on URL** 텍스트 상자에 다음 패턴 "*http://company.runmyprocess.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-runmyprocess-tutorial/IC789623.png "앱 URL 구성")

4.  **RunMyProcess에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-runmyprocess-tutorial/IC789624.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 RunMyProcess 회사 사이트에 관리자로 로그인합니다.

6.  **계정 > 구성**으로 이동합니다.

    ![계정](./media/active-directory-saas-runmyprocess-tutorial/IC789625.png "계정")

7.  **인증 방법** 탭을 클릭합니다.

8.  **인증 방법** 섹션에서 다음 단계를 수행합니다.

    ![SSO](./media/active-directory-saas-runmyprocess-tutorial/IC789626.png "SSO")

    1.  **메서드**로 **Samlv2를 사용한 SSO**를 선택합니다.
    2.  Azure 클래식 포털의 **RunMyProcess에서 Single Sign-On 구성** 대화 상자 페이지에서 **SAML SSO URL** 값을 복사한 다음 **SSO 리디렉션** 텍스트 상자에 붙여넣습니다.
    3.  Azure 클래식 포털의 **RunMyProcess에서 Single Sign-On 구성** 대화 상자 페이지에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **로그아웃 리디렉션** 텍스트 상자에 붙여넣습니다.
    4.  **이름 ID 형식** 텍스트 상자에 **urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress**를 입력합니다.
    5.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    6.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 전체 인증서를 **인증서** 텍스트 상자에 붙여넣습니다.
    7.  **Save**를 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-runmyprocess-tutorial/IC789627.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 RunMyProcess에 로그인할 수 있도록 하려면 RunMyProcess로 프로비전되어야 합니다. RunMyProcess의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **RunMyProcess** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **계정 > 사용자**로 이동한 다음 **새로운 사용자**를 클릭합니다.

    ![새 사용자](./media/active-directory-saas-runmyprocess-tutorial/IC789631.png "새 사용자")

3.  **사용자 설정** 섹션에서 다음 단계를 수행합니다.

    ![프로필](./media/active-directory-saas-runmyprocess-tutorial/IC789632.png "프로필")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **이름** 및 **전자 메일**을 입력합니다.
    2.  **IDE 언어**, **언어** 및 **프로필**을 선택합니다.
    3.  **나에게 계정 만들기 메일 보내기**를 선택합니다.
    4.  **Save**를 클릭합니다.

>[AZURE.NOTE]RunMyProcess에서 제공하는 다른 RunMyProcess 사용자 계정 만들기 도구 또는 API를 사용하여 Azure Active Directory 사용자 계정를 프로비전합니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###RunMyProcess에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **RunMyProcess** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-runmyprocess-tutorial/IC789633.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-runmyprocess-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->