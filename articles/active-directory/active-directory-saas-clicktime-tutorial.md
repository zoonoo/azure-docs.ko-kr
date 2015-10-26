<properties pageTitle="자습서: ClickTime과 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 ClickTime을 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: ClickTime과 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=%20524782)를 클릭하십시오.

이 자습서는 Azure 및 ClickTime의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   ClickTime 테넌트

이 자습서를 완료한 후 ClickTime에 할당한 Azure AD 사용자가 ClickTime 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  ClickTime에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-clicktime-tutorial/IC777274.png "시나리오")
##ClickTime에 응용 프로그램 통합 사용

이 섹션은 ClickTime에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###ClickTime에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-clicktime-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-clicktime-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-clicktime-tutorial/IC749321.png "응용 프로그램 추가")

5.  **수행할 작업** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-clicktime-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **ClickTime**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-clicktime-tutorial/IC777275.png "응용 프로그램 갤러리")

7.  결과 창에서 **ClickTime**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![ClickTime](./media/active-directory-saas-clicktime-tutorial/IC777276.png "ClickTime")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 ClickTime에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 ClickTime 테넌트에 업로드해야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

>[AZURE.IMPORTANT]ClickTime 테넌트에 대한 single sign-on을 구성하기 위해 먼저 ClickTime 기술 지원에 문의하여 이 기능을 사용하도록 설정합니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **ClickTime** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single Sign-On 사용](./media/active-directory-saas-clicktime-tutorial/IC777277.png "Single Sign-On 사용")

2.  **ClickTime에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/IC777278.png "Single Sign-On 구성")

3.  **ClickTime에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/IC777279.png "Single Sign-On 구성")

4.  다른 웹 브라우저 창에서 ClickTime 회사 사이트에 관리자로 로그인합니다.

5.  위쪽에 도구 모음에서 **기본 설정**을 클릭한 다음 **보안 설정** 클릭합니다.

6.  **Single Sign-On 선호도** 섹션에서 다음 단계를 수행합니다.

    ![보안 설정](./media/active-directory-saas-clicktime-tutorial/IC777280.png "보안 설정")

    1.  **OneLogin**와 SSO(Single Sign-On)를 사용하여 로그인 **허용**을 선택합니다.
    2.  Azure 포털의 **ClickTime에 대한 Single Sign-on 구성** 대화 상자 페이지에서 **Single Sign-On 서비스 URL** 값을 복사한 다음 **ID 공급자 끝점** 텍스트 상자에 붙여넣습니다.
    3.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.  

        >[AZURE.TIP]자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    4.  Base 64로 인코딩된 인증서를 **메모장**에서 열고, 내용을 복사한 다음 전체 인증서를 **X.509 인증서** 텍스트 상자에 붙여넣습니다.
    5.  **Save**를 클릭합니다.

7.  Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-clicktime-tutorial/IC777281.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 ClickTime에 로그인할 수 있도록 하려면 ClickTime로 프로비전되어야 합니다. ClickTime의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **ClickTime** 테넌트에 로그인합니다.

2.  위쪽에 도구 모음에서 **회사**를 클릭한 다음 **피플**을 클릭합니다.

    ![피플](./media/active-directory-saas-clicktime-tutorial/IC777282.png "피플")

3.  **사람 추가**를 클릭합니다.

    ![사람 추가](./media/active-directory-saas-clicktime-tutorial/IC777283.png "사람 추가")

4.  새 사람 섹션에서 다음 단계를 수행합니다.

    ![피플](./media/active-directory-saas-clicktime-tutorial/IC777284.png "피플")

    1.  **전자 메일** 텍스트 상자에 Azure AD 계정의 전자 메일 주소를 입력합니다.
    2.  **전체 이름** 텍스트 상자에 Azure AD 계정의 이름을 입력합니다.  

        >[AZURE.NOTE]원한다면 새 사람 개체의 추가 속성을 설정할 수 있습니다.

    3.  **Save**를 클릭합니다.

>[AZURE.NOTE]다른 ClickTime 사용자 계정 생성 도구 또는 ClickTime가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###ClickTime에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **ClickTime ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-clicktime-tutorial/IC777285.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-clicktime-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=Oct15_HO3-->