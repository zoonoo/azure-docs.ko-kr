<properties 
    pageTitle="자습서: Benefitsolver와 Azure Active Directory 통합 | Microsoft Azure"
    description="Azure Active Directory에서 Benefitsolver를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="10/10/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-active-directory-integration-with-benefitsolver"></a>자습서: Benefitsolver와 Azure Active Directory 통합

이 자습서는 Azure 및 Benefitsolver의 통합을 보여 주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Benefitsolver Single Sign-on이 설정된 구독

이 자습서를 완료한 후 Benefitsolver에 할당한 Azure AD 사용자가 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Benefitsolver에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-benefitsolver-tutorial/IC804820.png "Scenario")
##<a name="enabling-the-application-integration-for-benefitsolver"></a>Benefitsolver에 응용 프로그램 통합 사용

이 섹션은 Benefitsolver에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###<a name="to-enable-the-application-integration-for-benefitsolver,-perform-the-following-steps:"></a>Benefitsolver에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-benefitsolver-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-benefitsolver-tutorial/IC700994.png "Applications")

4.  페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-benefitsolver-tutorial/IC749321.png "Add application")

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-benefitsolver-tutorial/IC749322.png "Add an application from gallerry")

6.  **검색 상자**에 **Benefitsolver**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-benefitsolver-tutorial/IC804821.png "Application Gallery")

7.  결과 창에서 **Benefitsolver**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Benefitssolver](./media/active-directory-saas-benefitsolver-tutorial/IC804822.png "Benefitssolver")
##<a name="configuring-single-sign-on"></a>Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Benefitsolver에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
Benefitsolver 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며, **SAML 토큰 특성** 구성에 사용자 지정 특성 매핑을 추가해야 합니다.  
다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![특성](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Benefitsolver** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-on 구성](./media/active-directory-saas-benefitsolver-tutorial/IC804824.png "Configure Single Sign-On")

2.  **Benefitsolver에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-benefitsolver-tutorial/IC804825.png "Configure Single Sign-On")

3.  **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 설정 구성](./media/active-directory-saas-benefitsolver-tutorial/IC804826.png "Configure App Settings")

    1.  **로그온 URL** 텍스트 상자에 **http://azure.benefitsolver.com**을 입력합니다.
    2.  **회신 URL** 텍스트 상자에 **https://www.benefitsolver.com/benefits/BenefitSolverView?page_name=single_signon_saml**을 입력합니다.  


    3.  **다음**을 클릭합니다.

4.  **Benefitsolver에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-benefitsolver-tutorial/IC804827.png "Configure Single Sign-On")

5.  다운로드한 메타데이터 파일을 Benefitsolver 지원팀에 보냅니다.

    >[AZURE.NOTE] Benefitsolver 지원팀은 실제 SSO 구성을 수행해야 합니다.
구독에 SSO를 사용하도록 설정하면 알림을 받을 수 있습니다.

6.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-benefitsolver-tutorial/IC804828.png "Configure Single Sign-On")

7.  위쪽 메뉴에서 **특성** to open the **SAML Token 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-benefitsolver-tutorial/IC795920.png "Attributes")

8.  필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-benefitsolver-tutorial/IC804823.png "Attributes")

  	|특성 이름|특성 값|
  	|---|---|
  	|ClientID|Benefitsolver 지원팀에서 이 값을 가져와야 합니다.|
  	|ClientKey|Benefitsolver 지원팀에서 이 값을 가져와야 합니다.|
  	|LogoutURL|Benefitsolver 지원팀에서 이 값을 가져와야 합니다.|
  	|EmployeeID|Benefitsolver 지원팀에서 이 값을 가져와야 합니다.|

    1.  위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    3.  **특성 값** 텍스트 상자에서 해당 행에 표시된 특성 값을 선택합니다.
    4.  **완료**를 클릭합니다.

9.  **변경 내용 적용**을 클릭합니다.

##<a name="configuring-user-provisioning"></a>사용자 프로비전 구성

Azure AD 사용자가 Benefitsolver에 로그인할 수 있도록 하려면 Benefitsolver로 프로비전되어야 합니다.  
Benefitsolver의 경우 직원 데이터는 HRIS 시스템의 인구 조사 파일을 통해 주로 밤에 채워지는 응용 프로그램에 있습니다.  

>[AZURE.NOTE] 다른 Benefitsolver 사용자 계정 생성 도구 또는 Benefitsolver가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##<a name="assigning-users"></a>사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###<a name="to-assign-users-to-benefitsolver,-perform-the-following-steps:"></a>Benefitsolver에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Benefitsolver** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-benefitsolver-tutorial/IC804829.png "Assign Users")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-benefitsolver-tutorial/IC767830.png "Yes")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.



<!--HONumber=Oct16_HO2-->


