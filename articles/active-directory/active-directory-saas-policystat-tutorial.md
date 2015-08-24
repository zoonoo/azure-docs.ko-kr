<properties pageTitle="자습서: PolicyStat와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 PolicyStat를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: PolicyStat와 Azure Active Directory 통합
>[AZURE.TIP]피드백을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=616313)를 클릭하십시오.
  
이 자습서는 Azure 및 PolicyStat의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   PolicyStat 테넌트
  
이 자습서를 완료한 후 PolicyStat에 할당한 Azure AD 사용자가 PolicyStat 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On할 수 있습니다.
  
이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  PolicyStat에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-policystat-tutorial/IC808662.png "시나리오")
##PolicyStat에 응용 프로그램 통합 사용
  
이 섹션은 PolicyStat에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###PolicyStat에 응용 프로그램 통합을 사용하도록 설정하려면

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-policystat-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-policystat-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-policystat-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **PolicyStat**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-policystat-tutorial/IC808627.png "응용 프로그램 갤러리")

7.  결과 창에서 **PolicyStat**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##Single Sign-On 구성
  
이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 PolicyStat에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. PolicyStat 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 할당 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![특성](./media/active-directory-saas-policystat-tutorial/IC808628.png "특성")

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **PolicyStat** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808629.png "Single Sign-On 구성")

2.  **PolicyStat에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808630.png "Single Sign-On 구성")

3.  **앱 설정 구성** 페이지의 **로그온 URL** 텍스트 상자에 URL PolicyStat 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: *“https://demo-azure.policystat.com”*)을 입력한 후 **다음**을 클릭합니다.

    ![앱 설정 구성](./media/active-directory-saas-policystat-tutorial/IC808631.png "앱 설정 구성")

4.  **PolicyStat에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808632.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 PolicyStat 회사 사이트에 관리자로 로그인합니다.

6.  **관리** 탭을 클릭 한 후 왼쪽 탐색 창에서 **Single Sign-On 구성**을 클릭합니다.

    ![관리자 메뉴](./media/active-directory-saas-policystat-tutorial/IC808633.png "관리자 메뉴")

7.  **설정** 섹션에서 **Single Sign-On 통합 사용**을 선택합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808634.png "Single Sign-On 구성")

8.  **특성 구성**을 클릭한 다음 **특성 구성** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On 구성")

    1.  **사용자 이름 특성** 텍스트 상자에 **uid**를 입력합니다.
    2.  **이름 속성** 텍스트 상자에 **firstname**을 입력합니다.
    3.  **성 속성** 텍스트 상자에 **lastname**을 입력합니다.
    4.  **이메일 속성** 텍스트 상자에 **emailaddress**를 입력합니다.
    5.  **변경 내용 저장**을 클릭합니다.

9.  **IDP 메타데이터**를 클릭한 다음, **IDP 메타데이터** 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On 구성")

    1.  다운로드한 메타데이터 파일을 열고 내용을 복사한 다음 **ID 공급자 메타데이터** 텍스트 상자에 붙여넣습니다.
    2.  **변경 내용 저장**을 클릭합니다.

10. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-policystat-tutorial/IC771723.png "Single Sign-On 구성")

11. 12. 위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-policystat-tutorial/IC795920.png "특성")

13. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

    ![특성](./media/active-directory-saas-policystat-tutorial/IC804823.png "특성")

    1.  **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에 **uid**를 입력합니다.
    3.  **특성 값** 텍스트 상자에서 **ExtractMailPrefix()**를 선택합니다.
    4.  **메일** 목록에서 **User.mail**을 선택합니다.
    5.  **완료**를 클릭합니다.
##사용자 프로비전 구성
  
Azure AD 사용자가 PolicyStat에 로그인할 수 있도록 하려면 PolicyStat로 프로비전되어야 합니다. PolicyStat는 사용자 프로비전 시간에만 지원합니다. 즉, PolicyStat에 사용자를 수동으로 추가하지 않아도 됩니다. Single sign on을 통해 첫 번째 로그인 시 사용자가 자동으로 추가됩니다.

>[AZURE.NOTE]다른 PolicyStat 사용자 계정 생성 도구 또는 PolicyStat가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당
  
구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###PolicyStat에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **PolicyStat ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-policystat-tutorial/IC808636.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-policystat-tutorial/IC767830.png "예")
  
Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->