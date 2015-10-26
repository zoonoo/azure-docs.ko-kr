<properties pageTitle="자습서: Bonus.ly와 Azure Active Directory 통합 | Microsoft Azure" description="Azure Active Directory에서 Bonus.ly를 사용하여 Single Sign-On, 자동화된 프로비전 등을 사용하도록 설정하는 방법을 알아봅니다." services="active-directory" authors="MarkusVi"  documentationCenter="na" manager="stevenpo"/>
<tags ms.service="active-directory" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="identity" ms.date="08/01/2015" ms.author="markvi" />
#자습서: Bonus.ly와 Azure Active Directory 통합
>[AZURE.TIP]사용자 의견을 보내려면 [여기](http://go.microsoft.com/fwlink/?LinkId=523806)를 클릭하십시오.

이 자습서는 Azure 및 Bonus.ly의 통합을 보여주기 위한 것입니다. 이 자습서에 설명된 시나리오에서는 사용자에게 이미 다음 항목이 있다고 가정합니다.

-   유효한 Azure 구독
-   Bonus.ly의 테스트 테넌트

이 자습서에 설명된 시나리오는 다음 구성 요소로 이루어져 있습니다.

1.  Bonus.ly에 응용 프로그램 통합 사용
2.  Single Sign-On 구성
3.  사용자 프로비전 구성
4.  사용자 할당

![시나리오](./media/active-directory-saas-bonus-tutorial/IC773679.png "시나리오")
##Bonus.ly에 응용 프로그램 통합 사용

이 섹션은 Bonus.ly에 응용 프로그램 통합을 사용하도록 설정하는 방법을 간략하게 설명하기 위한 것입니다.

###Bonus.ly에 응용 프로그램 통합을 사용하도록 설정하려면 다음 단계를 수행합니다.

1.  Azure 관리 포털의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

    ![Single Sign-On 사용](./media/active-directory-saas-bonus-tutorial/IC773680.png "Single Sign-On 사용")

2.  **디렉터리** 목록에서 디렉터리 통합을 사용하도록 설정할 디렉터리를 선택합니다.

3.  응용 프로그램 보기를 열려면 디렉터리 보기의 최상위 메뉴에서 **응용 프로그램**을 클릭합니다.

    ![응용 프로그램](./media/active-directory-saas-bonus-tutorial/IC700994.png "응용 프로그램")

4.  페이지 맨 아래에 있는 **추가**를 클릭합니다.

    ![응용 프로그램 추가](./media/active-directory-saas-bonus-tutorial/IC749321.png "응용 프로그램 추가")

5.  **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

    ![갤러리에서 응용 프로그램 추가](./media/active-directory-saas-bonus-tutorial/IC749322.png "갤러리에서 응용 프로그램 추가")

6.  **검색 상자**에 **Bonus.ly**를 입력합니다.

    ![응용 프로그램 갤러리](./media/active-directory-saas-bonus-tutorial/IC773681.png "응용 프로그램 갤러리")

7.  결과 창에서 **Bonus.ly**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773682.png "Bonusly")
##Single Sign-On 구성

이 섹션은 사용자가 SAML 프로토콜 기반 페더레이션을 사용하여 Azure AD의 계정으로 Bonus.ly에 인증할 수 있게 하는 방법을 간략하게 설명하기 위한 것입니다. Bonus.ly에 대한 Single Sign-on을 구성하려면 인증서의 손도장(thumbprint) 값을 검색해야 합니다. 이 절차를 잘 모르는 경우 [인증서의 손도장 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

###Single Sign-On을 구성하려면 다음 단계를 수행합니다.

1.  Azure AD 포털의 **Bonus.ly** 응용 프로그램 통합 페이지에서 **Single Sign-on 구성**을 클릭하여 **Single Sign-on 구성** 대화 상자를 엽니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonus-tutorial/IC749323.png "Single Sign-On 구성")

2.  **Bonus.ly에 대한 사용자 로그온 방법을 선택하십시오.** 페이지에서 **Microsoft Azure AD Single Sign-on**을 선택하고 **다음**을 클릭합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonus-tutorial/IC773683.png "Single Sign-On 구성")

3.  **앱 URL 구성** 페이지의 **Bonus.ly 테넌트 URL** 텍스트 상자에 다음 패턴 "*https://\<tenant-name>.Bonus.ly*"을 사용하여 URL을 입력한 후 **다음**을 클릭합니다.

    ![앱 URL 구성](./media/active-directory-saas-bonus-tutorial/IC773684.png "앱 URL 구성")

4.  **Bonus.ly에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 **c:\\Bonusly.cer**에 로컬로 인증서 파일을 저장합니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonus-tutorial/IC773685.png "Single Sign-On 구성")

5.  다른 브라우저 창에서 **Bonus.ly** 테넌트에 로그인합니다.

6.  위쪽에 도구 모음에서 **설정**을 클릭하고 **통합 및 앱**을 선택합니다.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773686.png "Bonusly")

7.  **Single Sign-on**에서 **SAML**을 선택합니다.

8.  **SAML** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![Bonusly](./media/active-directory-saas-bonus-tutorial/IC773687.png "Bonusly")

    1.  Azure 포털의 **Bonus.ly에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **IdP SSO 대상 URL** 텍스트 상자에 붙여넣습니다.
    2.  Azure 포털의 **Bonus.ly에서 Single Sign-on 구성** 대화 상자 페이지에서 **발급자 ID** 값을 복사한 다음 **IdP 발급자** 텍스트 상자에 붙여넣습니다.
    3.  Azure 포털의 **Bonus.ly에서 Single Sign-on 구성** 대화 상자 페이지에서 **원격 로그인 URL** 값을 복사한 다음 **IdP 로그인 URL** 텍스트 상자에 붙여넣습니다.
    4.  내보낸 인증서에서 **지문** 값을 복사한 다음 **인증서 지문** 텍스트 상자에 붙여 넣습니다.

        >[AZURE.TIP]자세한 내용은 [인증서의 지문 값을 검색하는 방법](http://youtu.be/YKQF266SAxI)을 참조하십시오.

9.  **저장**을 클릭합니다.

10. Microsoft Azure AD 포털에서 구성 확인을 선택하고 **완료**를 클릭하여 **Single Sign-on 구성** 대화 상자를 닫습니다.

    ![Single Sign-On 구성](./media/active-directory-saas-bonus-tutorial/IC773689.png "Single Sign-On 구성")
##사용자 프로비전 구성

Azure AD 사용자가 Bonus.ly에 로그인할 수 있도록 하려면 Bonus.ly로 프로비전되어야 합니다. Bonus.ly의 경우 프로비전은 수동 작업입니다.

###사용자 프로비전을 구성하려면

1.  브라우저 창에서 Bonus.ly 테넌트에 로그인합니다.

2.  **설정**을 클릭합니다.

    ![설정](./media/active-directory-saas-bonus-tutorial/IC781041.png "설정")

3.  **사용자 및 보너스** 탭을 클릭합니다.

    ![사용자 및 보너스](./media/active-directory-saas-bonus-tutorial/IC781042.png "사용자 및 보너스")

4.  **사용자 관리**를 클릭합니다.

    ![사용자 관리](./media/active-directory-saas-bonus-tutorial/IC781043.png "사용자 관리")

5.  **사용자 추가**를 클릭합니다.

    ![사용자 추가](./media/active-directory-saas-bonus-tutorial/IC781044.png "사용자 추가")

6.  **사용자 추가** 대화 상자에서 다음 단계를 수행합니다.

    ![사용자 추가](./media/active-directory-saas-bonus-tutorial/IC781045.png "사용자 추가")

    1.  관련된 텍스트 상자에 프로비전할 유효한 AAD 계정의 “**전자 메일**,**이름**, **성**을 입력합니다.
    2.  **Save**를 클릭합니다.

    >[AZURE.NOTE]AAD 계정 보유자는 활성화되기 전에 계정을 확인하기 위한 링크를 포함한 전자 메일을 받습니다.

>[AZURE.NOTE]다른 Bonus.ly 사용자 계정 생성 도구 또는 Bonus.ly가 제공한 API를 사용하여 AAD 사용자 계정을 프로비전할 수 있습니다.

##사용자 할당

구성을 테스트하려면 응용 프로그램 사용을 허용하려는 Azure AD 사용자를 할당하여 액세스 권한을 부여해야 합니다.

###Bonus.ly에 사용자를 할당하려면 다음 단계를 수행합니다.

1.  Azure AD 포털에서 테스트 계정을 만듭니다.

2.  Bonus.ly 응용 프로그램 통합 페이지에서 **사용자 할당**을 클릭합니다.

    ![사용자 할당](./media/active-directory-saas-bonus-tutorial/IC773690.png "사용자 할당")

3.  테스트 사용자를 선택하고 **할당**을 클릭한 다음 **예**를 클릭하여 사용자의 할당을 확인합니다.

    ![예](./media/active-directory-saas-bonus-tutorial/IC767830.png "예")

Single Sign-On 설정을 테스트하려면 액세스 패널을 엽니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://msdn.microsoft.com/library/dn308586)를 참조하십시오.

<!---HONumber=Oct15_HO3-->