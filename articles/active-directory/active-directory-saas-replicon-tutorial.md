<properties 
    pageTitle="자습서: Replicon과 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Replicon을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Replicon과 Azure Active Directory 통합
  
이 자습서는 Azure 및 Replicon의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Replicon 테넌트
  
이 자습서를 완료한 후 Replicon에 할당한 Azure AD 사용자가 Replicon 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Replicon에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-replicon-tutorial/IC777798.png "시나리오")
##Replicon에 응용 프로그램 통합 사용
  
이 섹션은 Replicon에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Replicon에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-replicon-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-replicon-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-replicon-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-replicon-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Replicon**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-replicon-tutorial/IC777799.png "응용 프로그램 갤러리")

7.  결과 창에서 **Replicon**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Replicon](./media/active-directory-saas-replicon-tutorial/IC777800.png "Replicon")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Replicon에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Replicon** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-replicon-tutorial/IC777801.png "Single Sign-On 구성")

2.  **Replicon에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-replicon-tutorial/IC777802.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-replicon-tutorial/IC777803.png "앱 URL 구성")

    1.  **Replicon 로그온 URL** 텍스트 상자에 Replicon 테넌트 URL(예: *https://na2.replicon.com/company/saml2/sp-sso/post*)을 입력합니다.
    2.  **Replicon 회신 URL** 텍스트 상자에 Replicon **AssertionConsumerService** URL(예: *https://global.replicon.com/!/saml2/company/sso/post*)을 입력합니다.

        >[AZURE.NOTE] **https://global.replicon.com/!/saml2/\<YourCompanyKey>**의 Replicon 메타데이터에서 URL을 가져올 수 있습니다.

    3.  **다음**를 클릭합니다.

4.  **Replicon에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터를 컴퓨터에 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-replicon-tutorial/IC777804.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Replicon 회사 사이트에 관리자로 로그인합니다.

6.  다음 단계를 수행하여 SAML 2.0을 구성합니다.

    ![SAML 인증 사용](./media/active-directory-saas-replicon-tutorial/IC777805.png "SAML 인증 사용")

    1.  **EnableSAML Authentication2** 대화 상자를 표시하려면, 사용자의 회사 키 다음에 있는 URL에 **/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**를 추가합니다. 다음 주소는 전체 URL의 스키마를 표시합니다. **https://na2.replicon.com/\<YourCompanyKey>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2**
    2.  **+**를 클릭하여 **v20Configuration** 섹션을 확장합니다.
    3.  **+**를 클릭하여 **metaDataConfiguration** 섹션을 확장합니다.
    4.  **파일 선택**을 클릭하여 ID 공급자 메타데이터 XML 파일을 선택하고 **제출**을 클릭합니다.

7.  Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-replicon-tutorial/IC778418.png "Single Sign-On 구성")
##사용자 프로비전 구성
  
Azure AD 사용자가 Replicon에 로그인할 수 있도록 하려면 Replicon으로 프로비전되어야 합니다. Replicon의 경우 프로비전은 수동 작업입니다.

###사용자 프로비저닝을 구성하려면

1.  웹 브라우저 창에서 Replicon 회사 사이트에 관리자로 로그인합니다.

2.  **관리 > 사용자**로 이동합니다.

    ![사용자](./media/active-directory-saas-replicon-tutorial/IC777806.png "사용자")

3.  **+사용자 추가**를 클릭합니다.

    ![사용자 추가](./media/active-directory-saas-replicon-tutorial/IC777807.png "사용자 추가")

4.  **사용자 프로필** 섹션에서 다음 단계를 수행합니다.

    ![사용자 프로필](./media/active-directory-saas-replicon-tutorial/IC777808.png "사용자 프로필")

    1.  **로그인 이름** 텍스트 상자에 프로비전하려는 Azure AD 사용자의 Azure AD 메일 주소를 입력합니다.
    2.  **인증 유형**으로 **SSO**를 선택합니다.
    3.  **부서** 텍스트 상자에 사용자의 부서를 입력합니다.
    4.  **직원 형식**으로 **관리자**를 선택합니다.
    5.  **사용자 프로필 저장**을 클릭합니다.

>[AZURE.NOTE]다른 Replicon 사용자 계정 생성 도구 또는 Replicon이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Replicon에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Replicon** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-replicon-tutorial/IC777809.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-replicon-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0928_2016-->