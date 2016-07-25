<properties 
    pageTitle="자습서: Coupa와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Coupa를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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
    ms.date="07/11/2016" 
    ms.author="jeedes" />

#자습서: Coupa와 Azure Active Directory 통합

이 자습서는 Azure 및 Coupa의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Coupa Single Sign-on이 설정된 구독

이 자습서를 완료한 후 Coupa에 할당한 Azure AD 사용자가 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Coupa에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-coupa-tutorial/IC791897.png "시나리오")
##Coupa에 응용 프로그램 통합 사용

이 섹션은 Coupa에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Coupa에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-coupa-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-coupa-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-coupa-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-coupa-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Coupa**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-coupa-tutorial/IC791898.png "응용 프로그램 갤러리")

7.  결과 창에서 **Coupa**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Coupa](./media/active-directory-saas-coupa-tutorial/IC791899.png "Coupa")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Coupa에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Coupa에 대한 Single Sign-on을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Coupa 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **설치 > 보안 제어**로 이동합니다.

    ![보안 제어](./media/active-directory-saas-coupa-tutorial/IC791900.png "보안 제어")

3.  컴퓨터에 Coupa 메타데이터 파일을 다운로드하려면 **SP 메타데이터 다운로드 및 가져오기**를 클릭합니다.

    ![Coupa SP 메타데이터](./media/active-directory-saas-coupa-tutorial/IC791901.png "Coupa SP 메타데이터")

4.  다른 브라우저 창에서 Azure 클래식 포털에 로그온합니다.

5.  **Coupa** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-coupa-tutorial/IC791902.png "Single Sign-On 구성")

6.  **Coupa에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-coupa-tutorial/IC791903.png "Single Sign-On 구성")

7.  **앱 URL 구성** 페이지에서 다음 단계를 수행합니다.

    ![앱 URL 구성](./media/active-directory-saas-coupa-tutorial/IC791904.png "앱 URL 구성")

    1.  **로그온 URL** 텍스트 상자에 사용자가 Coupar 응용 프로그램에 로그인하는 데 사용하는 URL(예: “*http://company.Coupa.com*”)을 입력합니다.
    2.  다운로드한 Coupa 메타데이터 파일을 연 다음 **AssertionConsumerService index/URL**을 복사합니다.
    3.  **Coupa 회신 URL** 텍스트 상자에 **AssertionConsumerService index/URL** 값을 붙여 넣습니다.
    4.  **다음**을 클릭합니다.

8.  **Coupa에서 Single Sign-On 구성** 페이지에서 메타데이터를 다운로드 하려면 **메타데이터 다운로드**를 클릭한 다음 컴퓨터에 로컬 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-coupa-tutorial/IC791905.png "Single Sign-On 구성")

9.  Coupa 회사 사이트에서 **설치 > 보안 제어**로 이동합니다.

    ![보안 제어](./media/active-directory-saas-coupa-tutorial/IC791900.png "보안 제어")

10. **Coupa 자격 증명을 사용하여 로그인** 섹션에서 다음 단계를 수행합니다.

    ![Coupa 자격 증명을 사용하여 로그인](./media/active-directory-saas-coupa-tutorial/IC791906.png "Coupa 자격 증명을 사용하여 로그인")

    1.  **SAML을 사용하여 로그인**을 선택합니다.
    2.  **찾아보기**를 클릭하여 다운로드한 Azure Active 메타데이터 파일을 업로드합니다.
    3.  **Save**를 클릭합니다.

11. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-coupa-tutorial/IC791907.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Coupa에 로그인할 수 있도록 하려면 Coupa로 프로비전되어야 합니다. Coupa의 경우 프로비전은 수동 작업입니다.

###사용자 프로비전을 구성하려면

1.  **Coupa** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  위쪽 메뉴에서 **설정**을 클릭한 다음 **사용자**를 클릭합니다.

    ![사용자](./media/active-directory-saas-coupa-tutorial/IC791908.png "사용자")

3.  **만들기**를 클릭합니다.

    ![사용자 만들기](./media/active-directory-saas-coupa-tutorial/IC791909.png "사용자 만들기")

4.  **사용자 만들기** 섹션에서 다음 단계를 수행합니다.

    ![사용자 세부 정보](./media/active-directory-saas-coupa-tutorial/IC791910.png "사용자 세부 정보")

    1.  관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 계정의 **로그인**, **이름**, **성**, **Single Sign-On ID**, **전자 메일** 특성을 입력합니다.
    2.  **만들기**를 클릭합니다.

    >[AZURE.NOTE] Azure Active Directory 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크가 포함된 전자 메일을 받습니다.

>[AZURE.NOTE] 다른 Coupa 사용자 계정 생성 도구 또는 Coupa가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Coupa에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Coupa** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-coupa-tutorial/IC791911.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-coupa-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->