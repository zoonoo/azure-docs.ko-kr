<properties 
    pageTitle="자습서: BlueJeans와 Azure AD 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 BlueJeans를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="jeevansd"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="01/14/2016" 
    ms.author="jeedes" />

#자습서: BlueJeans와 Azure AD 통합

이 자습서는 Azure 및 BlueJeans의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   BlueJeans Single Sign-on이 설정된 구독

이 자습서를 완료한 후 BlueJeans에 할당한 Azure AD 사용자가 BlueJeans 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  BlueJeans에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-bluejeans-tutorial/IC785860.png "시나리오")
##BlueJeans에 응용 프로그램 통합 사용

이 섹션은 BlueJeans에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###BlueJeans에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-bluejeans-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-bluejeans-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-bluejeans-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-bluejeans-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **BlueJeans**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-bluejeans-tutorial/IC785861.png "응용 프로그램 갤러리")

7.  결과 창에서 **BlueJeans**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![BlueJeans](./media/active-directory-saas-bluejeans-tutorial/IC785862.png "BlueJeans")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 BlueJeans에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **BlueJeans** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bluejeans-tutorial/IC785863.png "Single Sign-On 구성")

2.  **BlueJeans에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bluejeans-tutorial/IC785864.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **BlueJeans Sign On URL** 텍스트 상자에 다음 패턴 "**https://company.BlueJeans.com*"을 사용하여 URL을 입력한 다음 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-bluejeans-tutorial/IC785865.png "앱 URL 구성")

4.  **BlueJeans에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bluejeans-tutorial/IC785866.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 **BlueJeans** 회사 사이트에 관리자로 로그인합니다.

6.  **관리자 > 그룹 설정 > 보안**으로 이동합니다.

    ![관리자](./media/active-directory-saas-bluejeans-tutorial/IC785868.png "관리자")

7.  **보안** 섹션에서 다음 단계를 수행합니다.

    ![SAML Single Sign-on](./media/active-directory-saas-bluejeans-tutorial/IC785869.png "SAML Single Sign-on")

    1.  **SAML Single Sign On**을 선택합니다.
    2.  **자동 프로비전닝 사용**을 선택합니다.

8.  다음 단계로 이동합니다.

    ![인증서 경로](./media/active-directory-saas-bluejeans-tutorial/IC785870.png "인증서 경로")

    1.  **파일 선택**을 클릭한 다음 다운로드한 인증서를 업로드합니다.
    2.  Azure 포털의 **BlueJeans에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **BlueJeans에서 Single Sign-on 구성** 대화 상자 페이지에서 **변경 암호 URL** 값을 복사한 다음 **암호 변경 URL** 텍스트 상자에 붙여넣습니다.
    4.  Azure 포털의 **BlueJeans에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

9.  다음 단계로 이동합니다.

    ![변경 내용 저장](./media/active-directory-saas-bluejeans-tutorial/IC785874.png "변경 내용 저장")

    1.  **사용자 ID** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**를 입력합니다.
    2.  **이메일** 텍스트 상자에 **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name**를 입력합니다.
    3.  **변경 내용 저장**을 클릭합니다.

10. Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bluejeans-tutorial/IC785876.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 BlueJeans에 로그인할 수 있도록 하려면 BlueJeans로 프로비전되어야 합니다. BlueJeans의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **BlueJeans** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **관리자 > 사용자 관리 > 사용자 추가**로 이동합니다.

    ![관리자](./media/active-directory-saas-bluejeans-tutorial/IC785877.png "관리자")

    >[AZURE.IMPORTANT]**사용자 추가** 탭은 **보안 탭**, **자동 프로비전닝 사용**이 선택되지 않은 경우에만 사용할 수 있습니다.

3.  **사용자 추가** 섹션에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-bluejeans-tutorial/IC785886.png "사용자 추가")

    1.  관련된 텍스트 상자에 프로비전하려는 유효한 AAD 계정의 **BlueJeans 사용자 이름**, **전자 메일 주소**, **BlueJeans 모임 ID**, **중재자 암호**, **전체 이름**, **회사**를 입력합니다.
    2.  **사용자 추가**를 클릭합니다.

>[AZURE.NOTE]다른 BlueJeans 사용자 계정 생성 도구 또는 BlueJeans가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###BlueJeans에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **BlueJeans ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-bluejeans-tutorial/IC785887.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-bluejeans-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!----HONumber=AcomDC_0121_2016-->