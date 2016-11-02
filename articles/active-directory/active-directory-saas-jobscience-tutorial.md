<properties 
    pageTitle="자습서: Jobscience와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Jobscience를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="07/09/2016" 
    ms.author="jeedes" />

#자습서: Jobscience와 Azure Active Directory 통합
  
이 자습서는 Azure와 Jobscience의 통합을 보여 주기 위한 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Jobscience Single Sign-On이 가능하도록 설정된 구독
  
이 자습서를 완료하면 Jobscience에 할당한 Azure AD 사용자가 Jobscience 회사 사이트(서비스 공급자가 제공한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On으로 로그인할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Jobscience에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-jobscience-tutorial/IC784341.png "시나리오")
##Jobscience에 응용 프로그램 통합 사용
  
이 섹션에서는 Jobscience에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명합니다.

###Jobscience에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-jobscience-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-jobscience-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-jobscience-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Jobscience**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-jobscience-tutorial/IC784342.png "응용 프로그램 갤러리")

7.  결과 창에서 **Jobscience**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##Single Sign-On 구성
  
이 섹션에서는 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Jobscience에 인증할 수 있게 하는 방법을 간략하게 설명합니다.  
Jobscience에 대한 Single Sign-On을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다.  
이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Jobscience 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **설정**으로 이동합니다.

    ![설정](./media/active-directory-saas-jobscience-tutorial/IC784358.png "설정")

3.  왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다.

    ![내 도메인](./media/active-directory-saas-jobscience-tutorial/IC767825.png "내 도메인")

4.  도메인이 올바르게 설정되었는지 확인하기 위해 “**4단계 사용자에게 배포**”에 있는지 확인하고 “**내 도메인 설정**”을 검토합니다.

    ![사용자에게 배포된 도메인](./media/active-directory-saas-jobscience-tutorial/IC784377.png "사용자에게 배포된 도메인")

5.  다른 웹 브라우저 창에서 Azure 클래식 포털에 로그인합니다.

6.  **Jobscience** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Single Sign-On 구성")

7.  **Jobscience에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Single Sign-On 구성")

8.  **앱 URL 구성** 페이지의 **Jobscience 로그인 URL** 텍스트 상자에 다음 패턴 "*http://company.my.salesforce.com*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-jobscience-tutorial/IC784362.png "앱 URL 구성")

9.  **Jobscience에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Single Sign-On 구성")

10. Jobscience 회사 사이트에서 **보안 제어**를 클릭한 다음 **Single Sign-On 설정**을 클릭합니다.

    ![보안 제어](./media/active-directory-saas-jobscience-tutorial/IC784364.png "보안 제어")

11. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.

    ![Singl Sign-On 설정](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Singl Sign-On 설정")

    1.  **SAML 사용**을 선택합니다.
    2.  **새로 만들기**를 클릭합니다.

12. **SAML Single Sign-On 설정 편집** 대화 상자에서 다음 단계를 수행합니다.

    ![SAML Single Sign-On 설정](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On 설정")

    1.  **이름** 텍스트 상자에 구성할 이름을 입력합니다.
    2.  Azure 클래식 포털의 **Jobscience에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **발급자** 텍스트 상자에 붙여넣습니다.
    3.  **엔터티 ID** 텍스트 상자에 **https://salesforce-jobscience.com**를 입력합니다.
    4.  **찾아보기**를 클릭하여 Azure AD 인증서를 업로드합니다.
    5.  **SAML ID 형식**으로 **사용자 개체에서 페더레이션 ID를 포함하는 어설션**을 선택합니다.
    6.  **SAML ID 위치**에서 **Subject 문의 NameIdentifier 요소에 ID 포함**을 선택합니다.
    7.  Azure 클래식 포털의 **Jobscience에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다.
    8.  Azure 클래식 포털의 **Jobscience에서 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    9.  **Save**를 클릭합니다.

13. 왼쪽 탐색창의 **관리** 섹션에서 **도메인 관리**를 클릭해 관련된 섹션을 확장한 다음 **내 도메인**을 클릭해 **내 도메인** 페이지를 엽니다.

    ![내 도메인](./media/active-directory-saas-jobscience-tutorial/IC767825.png "내 도메인")

14. **내 도메인** 페이지의 **로그인 페이지 브랜딩** 섹션에서 **편집**을 클릭합니다.

    ![로그인 페이지 브랜딩](./media/active-directory-saas-jobscience-tutorial/IC767826.png "로그인 페이지 브랜딩")

15. **로그인 페이지 브랜딩** 페이지의 **인증 서비스** 섹션에 **SAML SSO 설정** 이름이 표시됩니다. 이름을 선택하고 **저장**을 클릭합니다.

    ![로그인 페이지 브랜딩](./media/active-directory-saas-jobscience-tutorial/IC784366.png "로그인 페이지 브랜딩")

16. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Single Sign-On 구성")
  
서비스 공급자가 제공한 Single Sign-On URL을 가져오려면 **보안 제어** 메뉴 섹션의 **Single Sign On 설정**을 클릭합니다.

![보안 제어](./media/active-directory-saas-jobscience-tutorial/IC784368.png "보안 제어")
  
위 단계에서 만든 SSO 프로필을 클릭합니다.  
이 페이지에 회사의 Single Sign-On URL(예: *https://companyname.my.salesforce.com?so=companyid*)이 표시됩니다.
##사용자 프로비전 구성
  
Azure AD 사용자가 Jobscience에 로그인할 수 있도록 하려면 Jobscience로 프로비저닝되어야 합니다. Jobscience의 경우 프로비저닝은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면 다음 단계를 수행합니다.

1.  **Jobscience** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  설정으로 이동합니다.

    ![설정](./media/active-directory-saas-jobscience-tutorial/IC784358.png "설정")

3.  **사용자 관리 > 사용자**로 이동합니다.

    ![사용자](./media/active-directory-saas-jobscience-tutorial/IC784369.png "사용자")

4.  **새 사용자**를 클릭합니다.

    ![모든 사용자](./media/active-directory-saas-jobscience-tutorial/IC784370.png "모든 사용자")

5.  **사용자 편집** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 편집](./media/active-directory-saas-jobscience-tutorial/IC784371.png "사용자 편집")

    1.  프로비저닝하려는 Azure AD 사용자의 이름, 성, 별칭, 전자 메일, 애칭 속성을 해당하는 텍스트 상자에 입력합니다.
    2.  **Save**를 클릭합니다.

    >[AZURE.NOTE] Azure AD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.

>[AZURE.NOTE] 다른 Jobscience 사용자 계정 생성 도구 또는 Jobscience가 제공한 API를 사용하여 AAD 사용자 계정을 프로비저닝할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Jobscience에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Jobscience** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-jobscience-tutorial/IC784372.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-jobscience-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->