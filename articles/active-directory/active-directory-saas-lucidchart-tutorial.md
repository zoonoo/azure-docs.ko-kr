<properties 
    pageTitle="자습서: Lucidchart와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Lucidchart를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="07/08/2016" 
    ms.author="jeedes" />

#자습서: Lucidchart와 Azure Active Directory 통합
  
이 자습서는 Azure 및 Lucidchart의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Lucidchart Single Sign-on이 설정된 구독
  
이 자습서를 완료한 후 Lucidchart에 할당한 Azure AD 사용자가 Lucidchart 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Lucidchart에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "시나리오")
##Lucidchart에 응용 프로그램 통합 사용
  
이 섹션은 Lucidchart에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Lucidchart에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Lucidchart**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "응용 프로그램 갤러리")

7.  결과 창에서 **Lucidchart**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Lucidchart에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Lucidchart** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Single Sign-On 구성")

2.  **Lucidchart에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Lucidchart 로그온 URL** 텍스트 상자에 Lucidchart 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: "*https://chart2.office.lucidchart.com/saml/sso/azure*")을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "앱 URL 구성")

4.  **Lucidchart에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드하려면 **메타데이터 다운로드**를 클릭한 다음 데이터 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Lucidchart 회사 사이트에 관리자로 로그인합니다.

6.  위쪽의 메뉴에서 **팀**을 클릭합니다.

    ![팀](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "팀")

7.  **응용 프로그램 > SAML 관리**를 클릭합니다.

    ![SAML 관리](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "SAML 관리")

8.  **SAML 인증 설정** 대화 상자에서 다음 단계를 수행합니다.

    1.  **SAML 인증 사용**을 선택한 다음 **옵션**을 클릭합니다. ![SAML 인증 설정](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML 인증 설정")
    2.  **도메인** 텍스트 상자에서 도메인을 입력한 다음 **인증서 변경**을 클릭합니다. ![인증서 변경](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "인증서 변경")
    3.  다운로드한 메타데이터 파일을 열고 내용을 복사한 다음 **메타데이터 업로드** 텍스트 상자에 붙여넣습니다. ![메타데이터 업로드](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "메타데이터 업로드")
    4.  **신규 사용자를 자동으로 팀에 추가**를 선택한 다음 **변경 내용 저장**을 클릭합니다. ![변경 내용 저장](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "변경 내용 저장")

9.  Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Lucidchart를 프로비전하는 사용자를 구성할 작업 항목이 없습니다. 할당된 사용자가 액세스 패널을 사용하여 Lucidchart에 로그인하려는 경우 Lucidchart는 사용자가 존재하는지를 확인합니다. 사용할 수 있는 사용자 계정이 없으면 자동으로 Lucidchart에 의해 생성됩니다.
##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Lucidchart에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Lucidchart** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->