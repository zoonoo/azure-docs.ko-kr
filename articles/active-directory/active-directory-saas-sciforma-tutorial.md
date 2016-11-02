<properties 
    pageTitle="자습서: Sciforma와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Sciforma를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="09/26/2016" 
    ms.author="jeedes" />


#<a name="tutorial:-azure-ad-integration-with-sciforma"></a>자습서: Azure AD와 Sciforma 통합
  
이 자습서는 Azure와 Sciforma의 통합을 보여주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Sciforma 테넌트
  
이 자습서를 완료한 후 Sciforma에 할당한 Azure AD 사용자가 Sciforma 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Sciforma에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-sciforma-tutorial/IC777369.png "Scenario")
##<a name="enabling-the-application-integration-for-sciforma"></a>Sciforma에 응용 프로그램 통합 사용
  
이 섹션은 Sciforma에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###<a name="to-enable-the-application-integration-for-sciforma,-perform-the-following-steps:"></a>Sciforma에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-sciforma-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-sciforma-tutorial/IC700994.png "Applications")

4.  페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-sciforma-tutorial/IC749321.png "Add application")

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-sciforma-tutorial/IC749322.png "Add an application from gallerry")

6.  **검색 상자**에 **Sciforma**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-sciforma-tutorial/IC777370.png "Application gallery")

7.  결과 창에서 **Sciforma**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Sciforma](./media/active-directory-saas-sciforma-tutorial/IC777371.png "Sciforma")
##<a name="configuring-single-sign-on"></a>Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Sciforma에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Sciforma** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sciforma-tutorial/IC777372.png "Configure single sign-on")

2.  **Sciforma에 대한 사용자 로그온 방법** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sciforma-tutorial/IC777373.png "Configure single sign-on")

3.  **앱 URL 구성** 페이지의 **Sciforma 로그인 URL** 텍스트 상자에 다음 패턴 "*https://\<tenant-name\>.Sciforma.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-sciforma-tutorial/IC777374.png "Configure app URL")

4.  **Sciforma에서 Single Sign-On 구성** 페이지에서 **메타데이터를 다운로드**하려면 메타데이터 다운로드를 클릭한 다음 데이터 파일을 **c:\\SciformaMetaData.xml**에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sciforma-tutorial/IC777375.png "Configure single sign-on")

5.  Sciforma 지원팀에 해당 메타데이터 파일을 전달합니다. 지원팀은 Single Sign-On을 구성합니다.

6.  Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-sciforma-tutorial/IC777376.png "Configure single sign-on")
##<a name="configuring-user-provisioning"></a>사용자 프로비전 구성
  
Sciforma를 프로비전하는 사용자를 구성할 작업 항목이 없습니다.  
할당된 사용자가 액세스 패널을 사용하여 Sciforma에 로그인하려는 경우 Sciforma는 사용자가 존재하는지를 확인합니다.  
사용할 수 있는 사용자 계정이 없으면 자동으로 Sciforma에 의해 생성됩니다.
##<a name="assigning-users"></a>사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###<a name="to-assign-users-to-sciforma,-perform-the-following-steps:"></a>Sciforma에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Sciforma** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-sciforma-tutorial/IC777377.png "Assign users")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-sciforma-tutorial/IC767830.png "Yes")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


<!--HONumber=Oct16_HO2-->


