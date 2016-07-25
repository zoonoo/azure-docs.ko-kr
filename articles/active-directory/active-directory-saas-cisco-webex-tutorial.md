<properties 
    pageTitle="자습서: Cisco Webex와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Cisco Webex를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Cisco Webex와 Azure Active Directory 통합

이 자습서는 Azure 및 Cisco Webex의 통합을 보여 주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Cisco Webex 테넌트

이 자습서를 완료한 후 Cisco Webex에 할당한 Azure AD 사용자가 Cisco Webex 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Cisco Webex에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-cisco-webex-tutorial/IC777614.png "시나리오")
##Cisco Webex에 응용 프로그램 통합 사용

이 섹션은 Cisco Webex에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Cisco Webex에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-cisco-webex-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-cisco-webex-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-cisco-webex-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-cisco-webex-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Cisco Webex**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-cisco-webex-tutorial/IC777615.png "응용 프로그램 갤러리")

7.  결과 창에서 **Cisco Webex**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Cisco Webex](./media/active-directory-saas-cisco-webex-tutorial/IC777616.png "Cisco Webex")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Cisco Webex에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base-64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Cisco Webex** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777617.png "Single Sign-On 구성")

2.  **Cisco Webex에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777618.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지에서 다음 단계를 수행하고 **다음**을 선택합니다.

    ![앱 URL 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777619.png "앱 URL 구성")

    1.  **URL에 로그온** 텍스트 상자에 Cisco Webex 테넌트 URL을 입력합니다(예: *http://contoso.webex.com*).
    2.  **URL Cisco Webex 회신** 텍스트 상자에 **Cisco Webex AssertionConsumerService URL**를 입력합니다(예: *https://company.webex.com/dispatcher/SAML2AuthService?siteurl=company*).

4.  **Cisco Webex에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777620.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Cisco Webex 회사 사이트에 관리자로 로그인합니다.

6.  위쪽의 메뉴에서 **사이트 관리**를 클릭합니다.

    ![사이트 관리](./media/active-directory-saas-cisco-webex-tutorial/IC777621.png "사이트 관리")

7.  **사이트 관리** 섹션에서 **SSO 구성**을 클릭합니다.

    ![SSO 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777622.png "SSO 구성")

8.  페더레이션된 웹 SSO 구성 섹션에서 다음 단계를 수행합니다.

    ![페더레이션된 SSO 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777623.png "페더레이션된 SSO 구성")

    1.  **페더레이션 프로토콜** 목록에서**SAML 2.0**을 선택합니다.
    2.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    3.  메모장에서 Base-64로 인코딩된 인증서를 열고 콘텐츠를 복사합니다.
    4.  **SAML 메타데이터 가져오기**를 클릭하고 Base-64로 인코딩된 인증서를 붙여 넣습니다.
    5.  Azure 클래식 포털의 **Cisco Webex에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **발급자 URL** 값을 복사한 다음 **SAML(IdP ID)의 발급자** 텍스트 상자에 붙여넣습니다.
    6.  Azure 클래식 포털의 **Cisco Webex에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **고객 SSO 서비스 로그인 URL 발급자** 텍스트 상자에 붙여넣습니다.
    7.  **NameID 형식** 목록에서 **전자 메일 주소**를 선택합니다.
    8.  **AuthnContextClassRef** 텍스트 상자에 **urn:oasis:names:tc:SAML:2.0:ac:classes:Password**를 입력합니다.
    9.  Azure 클래식 포털의 **Cisco Webex에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **고객 SSO 서비스 로그아웃 URL 발급자** 텍스트 상자에 붙여넣습니다.
    10. **업데이트**를 클릭합니다.

9.  Azure 클래식 포털의 **Cisco Webex에서 Single Sign-On 구성** 대화 상자 페이지에서 Single Sign-On 구성 확인을 선택한 다음 **완료**를 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-cisco-webex-tutorial/IC777624.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Cisco Webex에 로그인할 수 있도록 하려면 Cisco Webex로 프로비전되어야 합니다. Cisco Webex의 경우 프로비전은 수동 작업입니다.

###사용자 계정을 프로비전하려면 다음 단계를 수행합니다.

1.  **Cisco Webex** 테넌트에 로그인합니다.

2.  **사용자 관리 > 사용자 추가**로 이동합니다.

    ![사용자 추가](./media/active-directory-saas-cisco-webex-tutorial/IC777625.png "사용자 추가")

3.  사용자 추가 섹션에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-cisco-webex-tutorial/IC777626.png "사용자 추가")

    1.  **계정 유형**으로 **호스트**를 선택합니다.
    2.  **이름, 성**, **사용자 이름**, **전자 메일**, **암호**, **암호 확인** 텍스트 상자에 기존 Azure AD 사용자의 정보를 입력합니다.
    3.  **추가**를 클릭합니다.

>[AZURE.NOTE] 다른 Cisco Webex 사용자 계정 생성 도구 또는 Cisco Webex가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Cisco Webex에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Cisco Webex** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-cisco-webex-tutorial/IC777627.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-cisco-webex-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->