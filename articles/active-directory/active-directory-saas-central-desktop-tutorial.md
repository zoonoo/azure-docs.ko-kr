<properties pageTitle="자습서: Central Desktop와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Central Desktop을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>

<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi"/>

#자습서: Central Desktop와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=522411)를 클릭하십시오.

이 자습서는 Azure 및 Central Desktop의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Central desktop single sign on가 설정된 구독 / Central desktop 테넌트

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Central Desktop에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-central-desktop-tutorial/IC769558.png "시나리오")
##Central Desktop에 응용 프로그램 통합 사용

이 섹션은 Central Desktop에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Central Desktop에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-central-desktop-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-central-desktop-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-central-desktop-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-central-desktop-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Central Desktop**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-central-desktop-tutorial/IC769559.png "응용 프로그램 갤러리")

7.  결과 창에서 **Central Desktop**을 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Central Desktop](./media/active-directory-saas-central-desktop-tutorial/IC769560.png "Central Desktop")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Central Desktop에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.  
이 절차의 일부로 base-64로 인코딩된 인증서 파일을 Central Desktop 테넌트에 업로드해야 합니다.  
이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Central Desktop** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-central-desktop-tutorial/IC749323.png "Single Sign-On 구성")

2.  **Central Desktop에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-central-desktop-tutorial/IC777628.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행하고 **다음**을 선택합니다.

    -   **Central Desktop 로그인 URL** 텍스트 상자에서 Central Desktop 테넌트의 URL을 입력합니다.(예: **http://contoso.centraldesktop.com*))
    -   Central Desktop 회신 URL 텍스트 상자에 Central Desktop AssertionConsumerService URL을 입력합니다.(예:https://contoso.centraldesktop.com/saml2-assertion.php))

    >[AZURE.NOTE]Central Desktop 메타데이터에서 값을 가져올 수 있습니다.(예: **http://contoso.centraldesktop.com*))

    ![앱 URL 구성](./media/active-directory-saas-central-desktop-tutorial/IC769561.png "앱 URL 구성")

4.  **Central Desktop에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-central-desktop-tutorial/IC769562.png "Single Sign-On 구성")

5.  **Central Desktop** 테넌트에 로그인합니다.

6.  **설정**으로 이동하고 **고급**을 클릭한 다음 **Single Sign On**을 클릭합니다.

    ![설정 - 고급](./media/active-directory-saas-central-desktop-tutorial/IC769563.png "설정 - 고급")

7.  **Single Sign On 설정** 페이지에서 다음 단계를 수행합니다.

    ![Singl Sign On 설정](./media/active-directory-saas-central-desktop-tutorial/IC769564.png "Singl Sign On 설정")

    1.  **SAML v2 Single Sign-On 사용**을 선택합니다.
    2.  Azure AD 포털의 **Central Desktop에 대한 Single Sign-on 구성** 페이지에서 **발급자 URL** 값을 복사한 다음 **SSO URL** 텍스트 상자에 붙여넣습니다.
    3.  Azure AD 포털의 **Central Desktop에 대한 Single Sign-on 구성** 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **SSO 로그인 URL** 텍스트 상자에 붙여넣습니다.
    4.  Azure AD 포털의 **Central Desktop에 대한 Single Sign-on 구성** 페이지에서 **Single Sign-Out 서비스 URL** 값을 복사한 다음 **SSO 로그아웃 URL** 텍스트 상자에 붙여넣습니다.

8.  **메시지 서명 확인 방법** 섹션에서 다음 단계를 수행합니다.

    ![메시지 서명 인증 방법](./media/active-directory-saas-central-desktop-tutorial/IC769565.png "메시지 서명 인증 방법")

    1.  **인증서**를 선택합니다.
    2.  **SSO 인증서**목록에서 **RSH SHA256**을 선택합니다.
    3.  다운로드한 인증서에서 텍스트 파일을 만들고 텍스트 파일의 내용을 복사한 다음 **SSO 인증서** 필드를 붙여 넣습니다.  

        >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    4.  **SAMLv2 로그인 페이지의 링크 표시**를 선택합니다.

9.  **업데이트**를 클릭합니다.

10. Azure AD 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-central-desktop-tutorial/IC769566.png "Single Sign-On 구성")
##사용자 프로비전 구성

AAD 사용자가 로그인할 수 있도록 Central Desktop 응용 프로그램에 프로비전되어야 합니다. 이 섹션은 Central Desktop에 AAD 사용자 계정을 만드는 방법을 설명합니다.

###Central Desktop에 사용자 계정을 프로 비전하려면

1.  Central Desktop 테넌트에 로그인합니다.

2.  **피플 > 내부 멤버**로 이동합니다.

3.  **내부 멤버 추가**를 클릭합니다.

    ![피플](./media/active-directory-saas-central-desktop-tutorial/IC781051.png "피플")

4.  **새 멤버의 전자 메일 주소** 텍스트 상자에서 프로비전하려는 AAD 계정을 입력하고 **다음**을 클릭합니다.

    ![새 멤버의 전자 메일 주소](./media/active-directory-saas-central-desktop-tutorial/IC781052.png "새 멤버의 전자 메일 주소")

5.  **내부 멤버 추가**를 클릭합니다.

    ![내부 멤버 추가](./media/active-directory-saas-central-desktop-tutorial/IC781053.png "내부 멤버 추가")

    >[AZURE.NOTE]추가한 사용자는 계정을 활성화하기 위해 클릭해야 할 확인 링크를 포함하는 전자 메일을 받게 됩니다.

>[AZURE.NOTE]다른 Central Desktop 사용자 계정 생성 도구 또는 Central Desktop가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Central Desktop에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Central Desktop** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-central-desktop-tutorial/IC769567.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-central-desktop-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!-----HONumber=September15_HO1-->