<properties 
    pageTitle="자습서: Clever와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Clever를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Clever와 Azure Active Directory 통합

이 자습서는 Azure 및 Clever의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Clever 테넌트

이 자습서를 완료한 후 Clever에 할당한 Azure AD 사용자가 Clever 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Clever에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-clever-tutorial/IC798977.png "시나리오")
##Clever에 응용 프로그램 통합 사용

이 섹션은 Clever에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Clever에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-clever-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-clever-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-clever-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-clever-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Clever**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-clever-tutorial/IC798978.png "응용 프로그램 갤러리")

7.  결과 창에서 **Clever**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Clever](./media/active-directory-saas-clever-tutorial/IC798979.png "Clever")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Clever에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Clever 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![특성](./media/active-directory-saas-clever-tutorial/IC798980.png "특성")

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Clever** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign -On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clever-tutorial/IC784682.png "Single Sign-On 구성")

2.  **Clever에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clever-tutorial/IC798981.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Clever 로그온 URL** 텍스트 상자에 Clever 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: *https://clever.com/in/azsandbox*)을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-clever-tutorial/IC798982.png "앱 URL 구성")

4.  **Clever에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 로컬로 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clever-tutorial/IC798983.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Clever 회사 사이트에 관리자로 로그인합니다.

6.  도구 모음에서 **인스턴트 로그인**을 클릭합니다.

    ![인스턴트 로그인](./media/active-directory-saas-clever-tutorial/IC798984.png "인스턴트 로그인")

7.  **인스턴트 로그인** 페이지에서 다음 단계를 수행합니다.

    ![인스턴트 로그인](./media/active-directory-saas-clever-tutorial/IC798985.png "인스턴트 로그인")

    1.  **로그인 URL**을 입력합니다.

        >[AZURE.NOTE] **로그인 URL**은 사용자 지정 값입니다. Clever 지원팀에서 실제 값을 얻을 수 있습니다.

    2.  **ID 시스템**으로 **ADFS**를 선택합니다.
    3.  **Save**를 클릭합니다.

8.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clever-tutorial/IC798986.png "Single Sign-On 구성")

9.  위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-clever-tutorial/IC795920.png "특성")

10. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ![SAML 토큰 특성](./media/active-directory-saas-clever-tutorial/IC795921.png "SAML 토큰 특성")

	|특성 이름|특성 값|
    |---|---|
    |clever.student.credentials.district\_username|User.userprincipalname|

    1.  위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    3.  **특성 값** 텍스트 상자에서 해당 행에 표시된 특성 값을 선택합니다.
    4.  **완료**를 클릭합니다.

11. **변경 내용 적용**을 클릭합니다.

##사용자 프로비전 구성

Azure AD 사용자가 Clever에 로그인할 수 있도록 하려면 Clever로 프로비전되어야 합니다. Clever의 경우 프로비전은 Clever 지원팀에서 수행 되어야 하는 수동 작업입니다.

>[AZURE.NOTE] 다른 Clever 사용자 계정 생성 도구 또는 Clever가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Clever에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Clever** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-clever-tutorial/IC798987.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-clever-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->