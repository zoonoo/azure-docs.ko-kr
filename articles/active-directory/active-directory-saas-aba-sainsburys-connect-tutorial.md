<properties pageTitle="자습서: ABa Sainsburys Connect와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 ABa Sainsburys Connect를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Aba Sainsburys Connect와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=615290)를 클릭하십시오.

이 자습서는 Azure 및 Aba Sainsburys Connect의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Aba Sainsburys Connect single sign-on이 설정된 구독

이 자습서를 완료한 후 Aba Sainsburys Connect에 할당한 Azure AD 사용자가 Aba Sainsburys Connect 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Aba Sainsburys Connect에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "시나리오")
##Aba Sainsburys Connect에 응용 프로그램 통합 사용

이 섹션은 Aba Sainsburys Connect에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Aba Sainsburys Connect에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Aba Sainsburys Connect**를 입력합니다.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "Aba Sainsburys Connect")

7.  결과 창에서 **Aba Sainsburys Connect**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "Aba Sainsburys Connect")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Aba Sainsburys Connect에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Aba Sainsburys Connect** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성 ** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Single Sign-On 구성")

2.  **Aba Sainsburys Connect에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Single Sign-On 구성")

3.  **앱 설정 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 설정 구성](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "앱 설정 구성")

    1.  **로그온 URL** 텍스트 상자에 사용자가 Aba Sainsburys Connect 응용 프로그램에 로그인하는 데 사용하는 URL(예: **https://myaba.co.uk/client-access/sainsburys/saml.php*))을 입력합니다.
2.  **다음**를 클릭합니다.

4.  **Aba Sainsburys Connect에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 메타데이터 파일을 컴퓨터에 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Single Sign-On 구성")

5.  다운로드한 메타데이터 파일을 Aba Sainsburys Connect 지원팀에 보냅니다.

    >[AZURE.NOTE]Aba Sainsburys Connect 지원팀은 실제 SSO 구성을 수행해야 합니다. 구독에 SSO를 사용하도록 설정하면 알림을 받을 수 있습니다.

6.  Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Single Sign-On 구성")
##사용자 프로비전 구성

Aba Sainsburys Connect에 로그인 하려는 사용자가 Azure AD를 사용하려면 Aba Sainsburys Connect에 프로비전되어야 합니다. Aba Sainsburys Connect의 경우 Aba Sainsburys Connect 지원팀에서 사용자 계정을 작성해야 합니다.

>[AZURE.NOTE]Aba Sainsburys Connect에서 제공하는 다른 Aba Sainsburys Connect 사용자 계정 만들기 도구 또는 API를 사용하여 Azure Active Directory 사용자 계정를 프로비전합니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Aba Sainsburys Connect에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Aba Sainsburys Connect ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->