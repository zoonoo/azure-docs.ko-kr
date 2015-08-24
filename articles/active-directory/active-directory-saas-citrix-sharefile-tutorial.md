<properties pageTitle="자습서: Citrix ShareFile과 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Citrix ShareFile를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Citrix ShareFile과 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=523754)를 클릭하십시오.

이 자습서는 Azure 및 Citrix ShareFile의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Citrix ShareFile 테넌트

이 자습서를 완료한 후 Citrix ShareFile에 할당한 Azure AD 사용자가 Citrix ShareFile 회사 사이트 (서비스 공급자가 시작한 로그온)에서나 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Citrix ShareFile에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-citrix-sharefile-tutorial/IC773620.png "시나리오")
##Citrix ShareFile에 응용 프로그램 통합 사용

이 섹션은 Citrix ShareFile에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Citrix ShareFile에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-citrix-sharefile-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-citrix-sharefile-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-citrix-sharefile-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하십시오.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-citrix-sharefile-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에서 **Citrix ShareFile**을 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-citrix-sharefile-tutorial/IC773621.png "응용 프로그램 갤러리")

7.  결과 창에서 **Citrix ShareFile**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Citrix ShareFile](./media/active-directory-saas-citrix-sharefile-tutorial/IC773622.png "Citrix ShareFile")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Citrix ShareFile에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Citrix ShareFile** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성 ** 대화 상자를 엽니다.

    ![Single Sign-On 사용](./media/active-directory-saas-citrix-sharefile-tutorial/IC773623.png "Single Sign-On 사용")

2.  **Citrix ShareFile에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-citrix-sharefile-tutorial/IC773624.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Citrix ShareFile Sign-on URL** 텍스트 상자에 다음 패턴 `https://<tenant-name>.shareFile.com`을 사용하여 URL을 입력한 다음 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-citrix-sharefile-tutorial/IC773625.png "앱 URL 구성")

4.  **Citrix ShareFile에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-citrix-sharefile-tutorial/IC773626.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 **Citrix ShareFile** 회사 사이트에 관리자로 로그인합니다.

6.  위쪽에 도구 모음에서 **관리자**를 클릭합니다.

7.  왼쪽 탐색 창에서 **Single Sign-On 구성**을 선택합니다.

    ![계정 관리](./media/active-directory-saas-citrix-sharefile-tutorial/IC773627.png "계정 관리")

8.  **기본 설정**의 **Single Sign-on / SAML 2.0 구성** 대화 상자 페이지에서 다음 단계를 수행 합니다.

    ![SSO(Single sign-on)](./media/active-directory-saas-citrix-sharefile-tutorial/IC773628.png "SSO(Single sign-on)")

    1.  **SAML 사용**을 클릭합니다.
    2.  Azure 포털의 **Citrix ShareFile에 대한 Single Sign-on 구성** 대화 상자 페이지에서 **엔터티 ID** 값을 복사한 다음 **IDP 발급자/엔터티 ID** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **Citrix ShareFile에 대한 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **로그인 URL** 텍스트 상자에 붙여넣습니다.
    4.  Azure 포털의 **Citrix ShareFile에 대한 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    5.  **X.509 인증서** 필드 옆에 **변경**을 클릭한 다음 Azure AD 포털에서 다운로드한 인증서를 업로드합니다.![기본 설정](./media/active-directory-saas-citrix-sharefile-tutorial/IC773629.png "기본 설정")

9.  Citrix ShareFile 관리 포털에서 **저장**을 클릭합니다.

10. Azure AD 포털에서 Single Sign-on 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-citrix-sharefile-tutorial/IC773630.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Citrix ShareFile에 로그인할 수 있도록 하려면 Citrix ShareFile로 프로비전되어야 합니다. Citrix ShareFile의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **Citrix ShareFile** 테넌트에 로그인합니다.

2.  **사용자 관리 > 사용자 홈 관리 > + 직원 만들기**를 클릭합니다.

    ![직원 만들기](./media/active-directory-saas-citrix-sharefile-tutorial/IC781050.png "직원 만들기")

3.  프로비전하려는 유효한 Azure AD의 계정 **전자 메일**, **이름** 및 **성**을 입력합니다.

    ![기본 정보](./media/active-directory-saas-citrix-sharefile-tutorial/IC799951.png "기본 정보")

4.  **사용자 추가**를 클릭합니다.

    >[AZURE.NOTE]AAD 계정 보유자는 활성화되기 전에 전자 메일을 받고 링크를 따라 계정을 확인합니다.

>[AZURE.NOTE]다른 Citrix ShareFile 사용자 계정 생성 도구 또는 Citrix ShareFile이 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수도 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Citrix ShareFile에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  **Citrix ShareFile ** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-citrix-sharefile-tutorial/IC773631.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-citrix-sharefile-tutorial/IC767830.png "예")

Single sign-on 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=August15_HO7-->