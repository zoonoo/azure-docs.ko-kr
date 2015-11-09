<properties 
    pageTitle="자습서: Igloo Software와 Azure Active Directory 통합| Microsoft Azure" 
    description="Azure Active Directory에서 Igloo Software를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#자습서: Igloo Software와 Azure Active Directory 통합
  
이 자습서는 Azure와 Igloo Software의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   [Igloo Software](http://www.igloosoftware.com/) Single Sign-on이 가능하도록 설정된 구독
  
이 자습서를 완료하면 Igloo Software에 할당한 Azure AD 사용자가 Igloo Software 회사 사이트(서비스 공급자가 제공한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On으로 로그인할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Igloo Software에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-igloo-software-tutorial/IC783961.png "시나리오")
##Igloo Software에 응용 프로그램 통합 사용
  
이 섹션에서는 Igloo Software에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###Igloo Software에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-igloo-software-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-igloo-software-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-igloo-software-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-igloo-software-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Igloo Software**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-igloo-software-tutorial/IC783962.png "응용 프로그램 갤러리")

7.  결과 창에서 **Igloo Software**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Igloo](./media/active-directory-saas-igloo-software-tutorial/IC783963.png "Igloo")
##Single Sign-On 구성
  
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Igloo Software에 인증할 수 있게 하는 방법을 간략하게 설명합니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 Central Desktop 테넌트에 업로드해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Igloo Software** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-igloo-software-tutorial/IC783964.png "Single Sign-On 구성")

2.  **Igloo Software에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Microsoft Azure AD Single Sign-On](./media/active-directory-saas-igloo-software-tutorial/IC783965.png "Microsoft Azure AD Single Sign-On")

3.  **앱 URL 구성** 페이지의 **Igloo Software 로그인 URL** 텍스트 상자에 다음 패턴 "**https://company.igloocommunities.com/?signin*"을 사용하여 URL을 입력하고 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-igloo-software-tutorial/IC773625.png "앱 URL 구성")

4.  **Igloo Software에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-igloo-software-tutorial/IC783966.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Igloo Software 회사 사이트에 관리자로 로그인합니다.

6.  **제어판**으로 이동합니다.

    ![제어판](./media/active-directory-saas-igloo-software-tutorial/IC799949.png "제어판")

7.  **멤버 자격** 탭을 클릭하고 **로그인 설정**을 클릭합니다.

    ![로그인 설정](./media/active-directory-saas-igloo-software-tutorial/IC783968.png "로그인 설정")

8.  SAML 구성 섹션에서 **SAML 인증 구성**을 클릭합니다.

    ![SAML 구성](./media/active-directory-saas-igloo-software-tutorial/IC783969.png "SAML 구성")

9.  **일반 구성** 섹션에서 다음 단계를 수행합니다.

    ![일반 구성](./media/active-directory-saas-igloo-software-tutorial/IC783970.png "일반 구성")

    1.  **연결 이름** 텍스트 상자에 구성의 사용자 지정 이름을 입력합니다.
    2.  Azure 포털의 **Igloo Software에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **IdP 로그인 URL** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **Igloo Software에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **IdP 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    4.  **로그아웃 응답 및 요청 HTTP 유형**으로 **POST**를 선택합니다.
    5.  다운로드한 인증서에서 텍스트 파일을 만듭니다.
        
		>[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    6.  인증서의 텍스트 파일 버전에서 첫 번째 줄(**)과 마지막 줄(**)을 제거하고 나머지 인증서 텍스트를 복사한 다음 **공용 인증서** 텍스트 상자에 붙여넣습니다.

10. **응답 및 인증 구성**에서 다음 단계를 수행합니다.

    ![응답 및 인증 구성](./media/active-directory-saas-igloo-software-tutorial/IC783971.png "응답 및 인증 구성")

    1.  **ID 공급자**로 **Microsoft ADFS**를 선택합니다.
    2.  **ID 유형**으로 **전자 메일 주소**를 선택합니다.
    3.  **이메일 특성** 텍스트 상자에 **emailaddress**를 입력합니다.
    4.  **이름 특성** 텍스트 상자에 **givenname**을 입력합니다.
    5.  **성 특성** 텍스트 상자에 **surname**을 입력합니다.

11. 구성을 완료하려면 다음 단계를 수행합니다.

    ![로그인할 때 사용자 만들기](./media/active-directory-saas-igloo-software-tutorial/IC783972.png "로그인할 때 사용자 만들기")

    1.  **로그인할 때 사용자 만들기**에서 **로그인할 때 사이트에 새 사용자 만들기**를 선택합니다.
    2.  **로그인 설정**에서 **"로그인" 화면에서 SAML 단추 사용**을 선택합니다.
    3.  **Save**를 클릭합니다.

12. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-igloo-software-tutorial/IC783973.png "Single Sign-On 구성")
##사용자 프로비저닝 구성
  
Igloo Software를 프로비저닝하는 사용자를 구성할 작업 항목이 없습니다. 할당된 사용자가 액세스 패널을 사용하여 Igloo Software에 로그인을 시도하면 Igloo Software는 사용자가 존재하는지 확인합니다. 사용할 수 있는 사용자 계정이 없으면 자동으로 Igloo Software에서 계정을 만듭니다.
##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Igloo Software에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Igloo Software** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-igloo-software-tutorial/IC783974.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-igloo-software-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=Nov15_HO1-->