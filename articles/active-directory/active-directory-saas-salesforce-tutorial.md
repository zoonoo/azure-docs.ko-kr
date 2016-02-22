<properties
    pageTitle="자습서: Salesforce와 Azure Active Directory 통합 | Microsoft Azure"
    description="Azure Active Directory에서 Salesforce를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다."
    services="active-directory"
    documentationCenter=""
    authors="liviodlc"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="liviodlc"/>

#자습서: Azure Active Directory와 Salesforce를 통합하는 방법

이 자습서에서는 Salesforce 환경을 Azure Active Directory에 연결하는 방법을 보여줍니다. Single Sign-On을 Salesforce에 구성하는 방법, 자동화된 사용자 프로비저닝을 사용하도록 설정하는 방법 및 사용자에게 Salesforce에 대한 액세스 권한을 할당하는 방법을 알아봅니다.

##필수 조건

1. [Azure 관리 포털](https://manage.windowsazure.com)을 통해 Azure Active Directory에 액세스하려면 먼저 유효한 Azure 구독이 있어야 합니다.

2. [Salesforce.com](https://www.salesforce.com/)에 유효한 테넌트가 있어야 합니다.

> [AZURE.IMPORTANT] Salesforce.com **평가판**을 사용하는 경우 자동화된 사용자 프로비저닝을 구성할 수 없습니다. 평가판 계정은 구입할 때까지 필요한 API 액세스가 활성화되지 않습니다.
> 
> 이 자습서를 완료하기 위해 [무료 개발자 계정](https://developer.salesforce.com/signup)을 사용하여 이러한 제한을 해결할 수 있습니다.

Salesforce 샌드박스 환경을 사용하는 경우 [Salesforce 샌드박스 통합 자습서](https://go.microsoft.com/fwLink/?LinkID=521879)를 참조하세요.

##비디오 자습서

아래 비디오를 사용하여 이 자습서를 따를 수 있습니다.

**비디오 자습서 1부: Single Sign-on을 사용하도록 설정하는 방법**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-enable-single-sign-on]

**비디오 자습서 2부: 사용자 프로비저닝을 자동화하는 방법**

> [AZURE.VIDEO integrating-salesforce-with-azure-ad-how-to-automate-user-provisioning]

##1단계: 디렉터리에 Salesforce 추가

1. [Azure 관리 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![왼쪽 탐색 창에서 Active Directory를 선택합니다.][0]

2. **디렉터리**목록에서 Salesforce에 추가할 디렉터리를 선택합니다.

3. 상단 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램을 클릭합니다.][1]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![추가 클릭하여 새 응용 프로그램을 추가합니다.][2]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![갤러리에서 응용 프로그램 추가를 클릭합니다.][3]

6. **검색 상자**에 **Salesforce**를 입력합니다. 그런 다음 결과에서 **Salesforce**를 선택하고 **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![Salesforce를 추가합니다.][4]

7. 이제 Salesforce에 대한 빠른 시작 페이지가 표시됩니다.

	![Azure AD의 Salesforce 빠른 시작 페이지][5]

##2단계: Single Sign-On 사용

1. Single Sign-On을 구성하려면 Salesforce 환경에 대한 사용자 지정 도메인을 설정하고 배포해야 합니다. 그러한 방법에 대한 지침은 [도메인 이름 설정](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_setup.htm&language=en_US)을 참조하세요.

2. Azure AD의 Salesforce 빠른 시작 페이지에서 **Single Sign-On 구성** 단추를 클릭합니다.

	![Single Sign-On 구성 단추][6]

3. 대화 상자가 열리고 "Salesforce에 대한 사용자 로그온 방법을 선택하십시오."를 묻는 화면이 표시됩니다. **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Azure AD Single Sign-On 선택][7]

	> [AZURE.NOTE] 다른 Single Sign-On 옵션에 대해 자세히 알아보려면 [여기를 클릭](../active-directory-appssoaccess-whatis/#how-does-single-sign-on-with-azure-active-directory-work)하세요.

4. **앱 설정 구성** 페이지에서 다음 형식으로 Salesforce 도메인 URL을 입력하여 **로그온 URL**을 작성합니다.
 - 엔터프라이즈 계정: `https://<domain>.my.salesforce.com`
 - 개발자 계정: `https://<domain>-dev-ed.my.salesforce.com` 

	![로그온 URL 입력][8]

5. **Salesforce에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

	![인증서 다운로드][9]

6. 브라우저에서 새 탭을 열고 Salesforce 관리자 계정으로 로그인합니다.

7. **관리자** 탐색 창에서 **보안 제어**를 클릭하여 관련된 섹션을 확장합니다. 그런 다음 **Single Sign-on 설정**을 클릭합니다.

	![보안 컨트롤에서 Single Sign-On 설정 클릭][10]

8. **Single Sign-on 설정** 페이지에서 **편집** 단추를 클릭합니다.

	![편집 단추 클릭][11]

	> [AZURE.NOTE] Salesforce 계정에 대해 Single Sign-on 설정을 사용하도록 설정할 수 없는 경우 사용자에 기능을 사용하도록 설정하려면 Salesforce의 지원 팀에 문의해야 합니다.

9. **SAML Enabled(SAML 사용)**을 선택한 다음 **저장**을 클릭합니다.

	![SAML 사용 선택][12]

10. SAML Single Sign-On 설정을 구성하려면 **새로 만들기**를 클릭합니다.

	![SAML 사용 선택][13]

11. **SAML Single Sign-On Setting Edit(SAML Single Sign-on 설정 편집)** 페이지에서 다음 구성을 확인합니다.

	![만들어야 하는 구성의 스크린샷][14]

 - **이름** 필드에 이 구성에 대한 이름을 입력합니다. **이름**에 값을 제공하면 **API 이름** 입력란이 자동으로 채워집니다.

 - Azure AD에서 **발급자 URL** 값을 복사한 다음 Salesforce의 **발급자** 필드에 붙여 넣습니다.

 - **엔터티 ID** 입력란에 다음 패턴을 사용하여 Salesforce 도메인 이름을 입력합니다.
     - 엔터프라이즈 계정: `https://<domain>.my.salesforce.com`
     - 개발자 계정: `https://<domain>-dev-ed.my.salesforce.com`

 - **찾아보기** 또는 **파일 선택**을 클릭하여 **업로드할 파일 선택** 대화 상자를 열고, Salesforce 인증서를 선택한 다음, **열기**를 클릭하여 인증서를 업로드합니다.

 - **SAML Identity Type(SAML ID 형식)**에서 **Assertion contains User's salesforce.com username(어설션에 사용자의 salesforce.com 사용자 이름 포함)**을 선택합니다.

 - **SAML Identity Location(SAML ID 위치)**에서 **Identity is in the NameIdentifier element of the Subject statement(Subject 문의 NameIdentifier 요소에 ID 포함)**을 선택합니다.

 - Azure AD에서 **원격 로그인 URL** 값을 복사한 다음 Salesforce의 **ID 공급자 로그인 URL** 필드에 붙여 넣습니다.

 - **Service Provider Initiated Request Binding(서비스 공급자가 시작한 요청 바인딩)**에서 **HTTP 리디렉션**을 선택합니다.

 - 마지막으로 **저장**을 클릭하여 SAML Single Sign-On 설정을 적용합니다.

12. Salesforce에서 왼쪽 탐색 창의 클릭**도메인 관리**관련된 섹션을 확장 한 다음 클릭**내 도메인**.

	![내 도메인 클릭][15]

13. **인증 구성** 섹션으로 스크롤하여 **편집** 단추를 클릭합니다.

	![편집 단추 클릭][16]

14. **인증 서비스**섹션에서 SAML SSO 구성 이름을 선택한 다음 **저장**을 클릭합니다.

	![SSO 구성 선택][17]

	> [AZURE.NOTE] 둘 이상의 인증 서비스를 선택하는 경우 사용자가 Salesforce 환경으로 Single Sign-On을 시작하려고 하면 로그인하려고 하는 인증 서비스를 선택하라는 메시지가 표시됩니다. 이 메시지가 표시되지 않도록 하려면 **다른 모든 인증 서비스를 선택하지 않은 상태로 유지**해야 합니다.

15. Azure AD에서 Salesforce에 업로드한 인증서를 사용하도록 설정하려면 Single Sign-On 구성 확인 확인란을 선택합니다. 그런 후 **Next**를 클릭합니다.

	![확인 확인란 선택][18]

16. 대화 상자의 마지막 페이지에서 이 Single Sign-On 구성 유지 관리와 관련된 오류 및 경고에 대한 전자 메일 알림을 수신하려는 경우 전자 메일 주소에 입력합니다.

	![전자 메일 주소를 입력합니다.][19]

17. **완료**를 클릭하여 대화 상자를 닫습니다. 구성을 테스트하려면 [Salesforce에 사용자 할당](#step-4-assign-users-to-salesforce) 섹션을 참조하세요.

##3단계: 자동화된 사용자 프로비저닝 사용

1. Salesforce의 Azure AD 빠른 시작 페이지에서 **사용자 프로비저닝 구성** 단추를 클릭합니다.

	![사용자 프로비저닝 구성 단추 클릭][20]

2. **사용자 프로비저닝 구성** 대화 상자에 Salesforce 관리자 사용자 이름 및 암호를 입력합니다.

	![관리자 사용자 이름 또는 암호 입력][21]

	> [AZURE.NOTE] 프로덕션 환경을 구성하는 경우 가장 좋은 방법은 이 단계에 대해 구체적으로 Salesforce에서 새 관리자 계정을 만드는 것입니다. 이 계정은 **시스템 관리자** 프로필을 Salesforce에 할당해야 합니다.

3. Salesforce 보안 토큰을 얻으려면 새 탭을 열고 동일한 Salesforce 관리자 계정에 로그인합니다. 페이지의 오른쪽 위 모서리에 있는 사용자 이름을 클릭하고 **내 설정**을 클릭합니다.

	![사용자의 이름을 클릭한 다음 내 설정 클릭][22]

4. 왼쪽 탐색 창에서 **개인**을 클릭하여 관련된 섹션을 확장한 다음 **Reset My Security Token(내 보안 토큰 재설정)**을 클릭합니다.

	![사용자의 이름을 클릭한 다음 내 설정 클릭][23]

5. **내 보안 토큰 재설정** 페이지에서 **보안 토큰 재설정** 단추를 클릭합니다.

	![경고를 읽습니다.][24]

6. 이 관리자 계정과 연결된 전자 메일 받은 편지함을 확인합니다. Salesforce.com에서 새 보안 토큰이 포함된 전자 메일을 찾습니다.

7. 토큰을 복사하고 Azure AD 창으로 이동한 다음 **사용자 보안 토큰** 필드에 붙여 넣습니다. 그런 후 **Next**를 클릭합니다.

	![보안 토큰에 붙여넣기][25]

8. 확인 페이지에서 프로비저닝 오류가 발생할 경우에 전자 메일 알림을 받도록 선택할 수 있습니다. **완료**를 클릭하여 대화 상자를 닫습니다.

	![알림을 받을 전자 메일 주소 입력][26]

##4단계: Salesforce에 사용자 할당

1. 구성을 테스트하려면 디렉터리에 새 테스트 계정을 만들어 시작합니다.

2. Salesforce 빠른 시작 페이지에서 **사용자 할당** 단추를 클릭합니다.

	![사용자 할당을 클릭합니다.][27]

3. 테스트 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.

 - 자동화된 사용자 프로비저닝 사용하지 않도록 설정하면 다음을 확인하는 메시지가 표시됩니다.

		![Confirm the assignment.][28]

 - 자동화된 사용자 프로비저닝 사용하도록 설정하면 사용자가 갖고 있어야 하는 Salesforce 프로파일 유형을 정의하라는 메시지가 표시됩니다. 잠시 후에 새로 프로비저닝된 사용자가 Salesforce 환경에 표시됩니다.

		![Confirm the assignment.][29]

		> [AZURE.IMPORTANT] If you are provisioning to a Salesforce **developer** environment, you will have a very limited number of licenses available for each profile. Therefore, it's best to provision users to the **Chatter Free User** profile, which has 4,999 licenses available.

4. Single Sign-On 설정을 테스트하려면 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 액세스 패널을 연 다음 테스트 계정에 로그인하고 **Salesforce**를 클릭합니다.

##관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-salesforce-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-salesforce-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-salesforce-tutorial/add-app.png
[3]: ./media/active-directory-saas-salesforce-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-salesforce-tutorial/add-salesforce.png
[5]: ./media/active-directory-saas-salesforce-tutorial/salesforce-added.png
[6]: ./media/active-directory-saas-salesforce-tutorial/config-sso.png
[7]: ./media/active-directory-saas-salesforce-tutorial/select-azure-ad-sso.png
[8]: ./media/active-directory-saas-salesforce-tutorial/config-app-settings.png
[9]: ./media/active-directory-saas-salesforce-tutorial/download-certificate.png
[10]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso.png
[11]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-edit.png
[12]: ./media/active-directory-saas-salesforce-tutorial/sf-enable-saml.png
[13]: ./media/active-directory-saas-salesforce-tutorial/sf-admin-sso-new.png
[14]: ./media/active-directory-saas-salesforce-tutorial/sf-saml-config.png
[15]: ./media/active-directory-saas-salesforce-tutorial/sf-my-domain.png
[16]: ./media/active-directory-saas-salesforce-tutorial/sf-edit-auth-config.png
[17]: ./media/active-directory-saas-salesforce-tutorial/sf-auth-config.png
[18]: ./media/active-directory-saas-salesforce-tutorial/sso-confirm.png
[19]: ./media/active-directory-saas-salesforce-tutorial/sso-notification.png
[20]: ./media/active-directory-saas-salesforce-tutorial/config-prov.png
[21]: ./media/active-directory-saas-salesforce-tutorial/config-prov-dialog.png
[22]: ./media/active-directory-saas-salesforce-tutorial/sf-my-settings.png
[23]: ./media/active-directory-saas-salesforce-tutorial/sf-personal-reset.png
[24]: ./media/active-directory-saas-salesforce-tutorial/sf-reset-token.png
[25]: ./media/active-directory-saas-salesforce-tutorial/got-the-token.png
[26]: ./media/active-directory-saas-salesforce-tutorial/prov-confirm.png
[27]: ./media/active-directory-saas-salesforce-tutorial/assign-users.png
[28]: ./media/active-directory-saas-salesforce-tutorial/assign-confirm.png
[29]: ./media/active-directory-saas-salesforce-tutorial/assign-sf-profile.png

<!---HONumber=AcomDC_0211_2016-->