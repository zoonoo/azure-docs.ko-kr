<properties 
    pageTitle="자습서: Druva와 Azure Active Directory 통합 | Microsoft Azure" 
    description="Azure Active Directory에서 Druva를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." 
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

#자습서: Druva와 Azure Active Directory 통합

이 자습서는 Azure 및 Druva의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Druva Single Sign-on이 설정된 구독

이 자습서를 완료한 후 Druva에 할당한 Azure AD 사용자가 Druva 회사 사이트(서비스 공급자가 시작한 로그온)에서 또는 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 사용하여 응용 프로그램에 Single Sign-On 할 수 있습니다.

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Druva에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-druva-tutorial/IC795084.png "시나리오")
##Druva에 응용 프로그램 통합 사용

이 섹션은 Druva에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Druva에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Active Directory](./media/active-directory-saas-druva-tutorial/IC700993.png "Active Directory")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-druva-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-druva-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-druva-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Druva**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-druva-tutorial/IC795085.png "응용 프로그램 갤러리")

7.  결과 창에서 **Druva**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Druva](./media/active-directory-saas-druva-tutorial/IC795086.png "Druva")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Druva에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. 이 절차의 일부로 base 64로 인코딩된 인증서 파일을 만들어야 합니다. 이 절차를 잘 모르는 경우 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

Druva 응용 프로그램은 특정 서식에서 SAML 어설션을 예상하며 이는 **SAML 토큰 특성** 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷은 이에 대한 예제를 보여 줍니다.

![SAML 토큰 특성](./media/active-directory-saas-druva-tutorial/IC795087.png "SAML 토큰 특성")

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털의 **Druva** 응용 프로그램 통합 페이지에서 **Single Sign-On 구성**을 클릭하여 **Single Sign-On 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-druva-tutorial/IC795027.png "Single Sign-On 구성")

2.  **Druva에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-druva-tutorial/IC795088.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Druva 로그온 URL** 텍스트 상자에 Druva 응용 프로그램에 로그온하기 위해 사용자가 사용하는 URL(예: "*https://cloud.druva.com/home/*”)을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-druva-tutorial/IC795089.png "앱 URL 구성")

4.  **Druva에서 Single Sign-On 구성** 페이지에서 인증서를 다운로드하려면 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-druva-tutorial/IC795090.png "Single Sign-On 구성")

5.  다른 웹 브라우저 창에서 Druva 회사 사이트에 관리자로 로그인합니다.

6.  **관리 > 설정**으로 이동합니다.

    ![설정](./media/active-directory-saas-druva-tutorial/IC795091.png "설정")

7.  Single Sign-On 설정 대화 상자에서 다음 단계를 수행합니다.

    ![Singl Sign-on 설정](./media/active-directory-saas-druva-tutorial/IC795092.png "Singl Sign-on 설정")

    1.  Azure 클래식 포털의 **Druva에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **ID 공급자 로그인 URL** 텍스트 상자에 붙여넣습니다.
    2.  Azure 클래식 포털의 **Druva에 대한 Single Sign-On 구성** 대화 상자 페이지에서 **원격 로그아웃 URL** 값을 복사한 다음 **ID 공급자 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    3.  다운로드한 인증서에서 **Base-64로 인코딩된** 파일을 만듭니다.

        >[AZURE.TIP] 자세한 내용은 [이진 인증서를 텍스트 파일로 변환하는 방법](http://youtu.be/PlgrzUZ-Y1o)을 참조하십시오.

    4.  Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **ID 공급자 인증서** 텍스트 상자에 붙여 넣습니다.
    5.  **설정** 페이지를 열려면 **저장**을 클릭합니다.

8.  **설정** 페이지에서 **SSO 토큰 생성**을 클릭합니다.

    ![설정](./media/active-directory-saas-druva-tutorial/IC795093.png "설정")

9.  **Single Sign-On 인증 토큰** 대화 상자에서 다음 단계를 수행합니다.

    ![SSO 토큰](./media/active-directory-saas-druva-tutorial/IC795094.png "SSO 토큰")

    1.  **복사**를 클릭합니다.
    2.  **닫기**를 클릭합니다.

10. Azure 클래식 포털에서 Single Sign-On 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-On 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-druva-tutorial/IC795095.png "Single Sign-On 구성")

11. 위쪽 메뉴에서 **특성**을 클릭하여 **SAML 토큰 특성** 대화 상자를 엽니다.

    ![특성](./media/active-directory-saas-druva-tutorial/IC795096.png "특성")

12. 필요한 특성 매핑을 추가하려면 다음 단계를 수행합니다.

	|특성 이름|특성 값|
    |---|---|
    |insync\_auth\_token|<*클립보드 값*>|

    1.  위의 테이블의 각 데이터 행에서 **사용자 특성 추가**를 클릭합니다.
    2.  **특성 이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.
    3.  **특성 값** 텍스트 상자에서 해당 행에 표시된 특성 값을 입력합니다.
    4.  **완료**를 클릭합니다.

13. **변경 내용 적용**을 클릭합니다.
##사용자 프로비전 구성

Azure AD 사용자가 Druva에 로그인할 수 있도록 하려면 Druva로 프로비전되어야 합니다. Druva의 경우 프로비전은 수동 작업입니다.

###사용자 프로비전을 구성하려면

1.  **Druva** 회사 사이트에 관리자 권한으로 로그인합니다.

2.  **관리 > 사용자**로 이동합니다.

    ![사용자 관리](./media/active-directory-saas-druva-tutorial/IC795097.png "사용자 관리")

3.  **새로 만들기**를 클릭합니다.

    ![사용자 관리](./media/active-directory-saas-druva-tutorial/IC795098.png "사용자 관리")

4.  새 사용자 만들기 대화 상자에서 다음 단계를 수행합니다.

    ![NewUser 만들기](./media/active-directory-saas-druva-tutorial/IC795099.png "NewUser 만들기")

    1.  관련된 텍스트 상자에 프로비전할 유효한 Azure Active Directory 사용자 계정의 전자 메일 주소 및 이름을 입력합니다.
    2.  **사용자 만들기**를 클릭합니다.

>[AZURE.NOTE] 다른 Druva 사용자 계정 생성 도구 또는 Druva가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Druva에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure 클래식 포털에서 테스트 계정을 만듭니다.

2.  **Druva** 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-druva-tutorial/IC795100.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 할당을 확인합니다.

    ![예](./media/active-directory-saas-druva-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](active-directory-saas-access-panel-introduction.md)를 참조하십시오.

<!---HONumber=AcomDC_0713_2016-->