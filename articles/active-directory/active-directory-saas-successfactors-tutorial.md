<properties 
    pageTitle="자습서: SuccessFactors와 Azure Active Directory 통합 | Microsoft Azure"
    description="Azure Active Directory에서 SuccessFactors를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="01/12/2016" 
    ms.author="markvi" />

#자습서: SuccessFactors와 Azure Active Directory 통합
  
이 자습서의 목표는 **SP 시작 Single Sign-On 모드**에서 Azure와 SuccessFactors의 통합을 보여주는 것입니다.  
이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   SP 시작 모드에서 SuccessFactors Single Sign-On의 사용이 설정된 구독
  
이 자습서를 완료한 후 SuccessFactors에 할당한 Azure AD 사용자가 SuccessFactors 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  SuccessFactors에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-successfactors-tutorial/IC791135.png "시나리오")

##SuccessFactors에 응용 프로그램 통합 사용
  
이 섹션은 SuccessFactors에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###SuccessFactors에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-successfactors-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-successfactors-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-successfactors-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-successfactors-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **SuccessFactors**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-successfactors-tutorial/IC791136.png "응용 프로그램 갤러리")

7.  결과 창에서 **SuccessFactors**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![SuccessFactors](./media/active-directory-saas-successfactors-tutorial/IC791137.png "SuccessFactors")

##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 SuccessFactors에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.
  
Single Sign-On을 구성하려면 SuccessFactors 지원 팀에 문의해야 합니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **SuccessFactors** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-successfactors-tutorial/IC791138.png "Single Sign-On 구성")

2.  **SuccessFactors에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-successfactors-tutorial/IC791139.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행하고 **다음**을 선택합니다.

    ![앱 URL 구성](./media/active-directory-saas-successfactors-tutorial/IC791140.png "앱 URL 구성")

    1.  **SuccessFactors 로그온 URL** 텍스트 상자에 사용자가 SuccessFactors 응용 프로그램에 로그인하는 데 사용하는 URL(예: "*https://performancemanager4.successfactors.com/sf/home?company=CompanyName&loginMethod=SSO*")을 입력합니다.
    2.  **SuccessFactors 회신 URL** 텍스트 상자에 **https://performancemanager4.successfactors.com/saml2/SAMLAssertionConsumer?company=CompanyName**을 입력합니다.

        >[AZURE.NOTE]이 값은 임시 자리 표시자입니다.  
        >SuccessFactors 지원팀에서 실제 값을 얻을 수 있습니다.  
        >이 자습서의 뒷부분에서 SuccessFactors 지원 팀의 연락처 정보를 찾을 수 있습니다.  
        >이 대화의 컨텍스트에서 실제 SuccessFactors 회신 URL을 받게 됩니다.

4.  **SuccessFactors에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-successfactors-tutorial/IC791141.png "Single Sign-On 구성")

5.  SAML 기반 Single Sign-On을 구성하려면 SuccessFactors 지원 팀에 연락하여 다음 항목을 제공하십시오.

    1.  다운로드한 인증서
    2.  원격 로그인 URL
    3.  원격 로그아웃 URL

    >[AZURE.IMPORTANT]SuccessFactors 지원 팀에 연락하여 NameId 형식 매개 변수를 *"Unspecified(지정되지 않음)"* 로 설정할 것을 요청합니다.

    Successfactors 지원 팀이 **앱 URL 구성** 대화 상자에 필요한 **Successfactors 회신 URL**을 전송합니다.

6.  Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-successfactors-tutorial/IC791142.png "Single Sign-On 구성")

##사용자 프로비전 구성
  
Azure AD 사용자가 SuccessFactors에 로그인할 수 있도록 하려면 SuccessFactors로 프로비전되어야 합니다.  
SuccessFactors의 경우 프로비전은 수동 작업입니다.
  
SuccessFactors에서 사용자를 생성하려면 SuccessFactors 지원 팀에 연락해야 합니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###SuccessFactors에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **SuccessFactors** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-successfactors-tutorial/IC791143.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-successfactors-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!-----HONumber=AcomDC_0114_2016-->
