<properties pageTitle="자습서: Abintegro와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Abintegro를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Abintegro와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=529073)를 클릭하십시오.

이 자습서는 Azure 및 Abintegro의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Abintegro Single Sign-on이 설정된 구독

이 자습서를 완료한 후 Abintegro에 할당한 Azure AD 사용자가 Abintegro 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Abintegro에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-abintegro-tutorial/IC790076.png "시나리오")
##Abintegro에 응용 프로그램 통합 사용

이 섹션은 Abintegro에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Abintegro에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-abintegro-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-abintegro-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-abintegro-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-abintegro-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Abintegro**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-abintegro-tutorial/IC790077.png "응용 프로그램 갤러리")

7.  결과 창에서 **Abintegro**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Abintegro](./media/active-directory-saas-abintegro-tutorial/IC790078.png "Abintegro")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Abintegro에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Abintegro** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single SignOn 구성](./media/active-directory-saas-abintegro-tutorial/IC790079.png "Single SignOn 구성")

2.  **Abintegro에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single SignOn 구성](./media/active-directory-saas-abintegro-tutorial/IC790080.png "Single SignOn 구성")

3.  **앱 URL 구성** 페이지의 **Abintegro Sign On URL** 텍스트 상자에 Abintegro에 로그온하기 위해 사용자가 사용하는 URL(예: `https://dev.abintegro.com/Shibboleth.sso/Login?entityID=<Issuer>&target=https://dev.abintegro.com/secure/`)을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-abintegro-tutorial/IC790081.png "앱 URL 구성")

4.  **Abintegro에서 Single Sign-On 구성** 페이지에서 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 메타데이터 파일을 저장합니다.

    ![Single SignOn 구성](./media/active-directory-saas-abintegro-tutorial/IC790082.png "Single SignOn 구성")

5.  메타데이터를 Abintegro 지원팀에 보냅니다.

    >[AZURE.NOTE]Single sign-on 구성을 Abintegro 지원팀에서 수행할 수 있습니다. 구성이 완료되는 즉시 알림을 받아볼 수 있습니다.

6.  Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single SignOn 구성](./media/active-directory-saas-abintegro-tutorial/IC790083.png "Single SignOn 구성")
##사용자 프로비전 구성

Abintegro를 프로비전하는 사용자를 구성할 작업 항목이 없습니다. 할당된 사용자가 액세스 패널을 사용하여 Abintegro에 로그인하려는 경우 Abintegro는 사용자가 존재하는지를 확인합니다. 사용할 수 있는 사용자 계정이 없으면 자동으로 Abintegro에 의해 생성됩니다.
##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Abintegro에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Abintegro ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-abintegro-tutorial/IC790084.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-abintegro-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->