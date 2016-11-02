<properties 
    pageTitle="자습서: ShiftPlanning과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 ShiftPlanning을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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


#<a name="tutorial:-azure-active-directory-integration-with-shiftplanning"></a>자습서: ShiftPlanning과 Azure Active Directory 통합
  
이 자습서는 Azure 및 ShiftPlanning의 통합을 보여 주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   ShiftPlanning Single Sign-On이 설정된 구독
  
이 자습서를 완료한 후 ShiftPlanning에 할당한 Azure AD 사용자가 ShiftPlanning 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  ShiftPlanning에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-shiftplanning-tutorial/IC786612.png "Scenario")
##<a name="enabling-the-application-integration-for-shiftplanning"></a>ShiftPlanning에 응용 프로그램 통합 사용
  
이 섹션은 ShiftPlanning에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###<a name="to-enable-the-application-integration-for-shiftplanning,-perform-the-following-steps:"></a>ShiftPlanning에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-shiftplanning-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램** 을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-shiftplanning-tutorial/IC700994.png "Applications")

4.  페이지 맨 아래에 있는 **추가** 를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-shiftplanning-tutorial/IC749321.png "Add application")

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-shiftplanning-tutorial/IC749322.png "Add an application from gallerry")

6.  **검색 상자**에 **ShiftPlanning**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-shiftplanning-tutorial/IC786613.png "Application Gallery")

7.  결과 창에서 **ShiftPlanning**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![ShiftPlanning](./media/active-directory-saas-shiftplanning-tutorial/IC786614.png "ShiftPlanning")
##<a name="configuring-single-sign-on"></a>Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ShiftPlanning에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다.  
이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)

###<a name="to-configure-single-sign-on,-perform-the-following-steps:"></a>Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **ShiftPlanning** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shiftplanning-tutorial/IC786615.png "Configure Single Sign-On")

2.  **ShiftPlanning에 대한 사용자 로그온 방법을 선택** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shiftplanning-tutorial/IC786616.png "Configure Single Sign-On")

3.  **앱 URL 구성** 페이지의 **ShiftPlanning Sign-on URL** 텍스트 상자에 다음 패턴 “*https://company.shiftplanning.com/includes/saml/*”을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-shiftplanning-tutorial/IC786617.png "Configure App URL")

4.  **ShiftPlanning에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-on 구성](./media/active-directory-saas-shiftplanning-tutorial/IC786618.png "Configure Single Sign-On")

5.  다른 웹 브라우저 창에서 **ShiftPlanning** 회사 사이트에 관리자로 로그인합니다.

6.  위쪽의 메뉴에서 **관리자**를 클릭합니다.

    ![관리자](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

7.  **통합** 아래에서 **Single Sign-On**을 클릭합니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-shiftplanning-tutorial/IC786620.png "Single Sign-On")

8.  **Single Sign-On** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On](./media/active-directory-saas-shiftplanning-tutorial/IC786905.png "Single Sign-On")

    1.  **SAML 사용**을 선택합니다.
    2.   **암호 로그인 허용**
    3.  Azure 클래식 포털의 **ShiftPlanning에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **SAML 발급자 URL** 텍스트 상자에 붙여넣습니다.
    4.  Azure 클래식 포털의 **ShiftPlanning에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **원격 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    5.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.  

        >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)

    6.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **X.509 인증서** 텍스트 상자에 붙여넣습니다.
    7.  **설정 저장**을 클릭합니다.

9.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-shiftplanning-tutorial/IC786621.png "Configure Single Sign-On")
##<a name="configuring-user-provisioning"></a>사용자 프로비전 구성
  
Azure AD 사용자가 ShiftPlanning에 로그인할 수 있도록 하려면 ShiftPlanning으로 프로비전되어야 합니다.  
ShiftPlanning의 경우 프로비전은 수동 작업입니다.

###<a name="to-provision-a-user-accounts,-perform-the-following-steps:"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1.  **ShiftPlanning** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **Admin**을 클릭합니다.

    ![관리자](./media/active-directory-saas-shiftplanning-tutorial/IC786619.png "Admin")

3.  **Staff**를 클릭합니다.

    ![Staff](./media/active-directory-saas-shiftplanning-tutorial/IC786623.png "Staff")

4.  **작업** 아래에서 **직원 추가**를 클릭합니다.

    ![직원 추가](./media/active-directory-saas-shiftplanning-tutorial/IC786624.png "Add Employees")

5.  **직원 추가** 섹션에서 다음 단계를 수행합니다.

    ![직원 저장](./media/active-directory-saas-shiftplanning-tutorial/IC786625.png "Save Employees")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 **이름**, **성** 및 **전자 메일**을 입력합니다.
    2.  **직원 저장**을 클릭합니다.

>[AZURE.NOTE]다른 ShiftPlanning 사용자 계정 생성 도구 또는 ShiftPlanning이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##<a name="assigning-users"></a>사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###<a name="to-assign-users-to-shiftplanning,-perform-the-following-steps:"></a>ShiftPlanning에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **ShiftPlanning** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-shiftplanning-tutorial/IC786626.png "Assign Users")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-shiftplanning-tutorial/IC767830.png "Yes")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하세요.


<!--HONumber=Oct16_HO2-->


