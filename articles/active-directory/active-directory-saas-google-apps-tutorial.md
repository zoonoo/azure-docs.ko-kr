<properties
    pageTitle="자습서: Google Apps와 Azure Active Directory 통합 | Microsoft Azure"
    description="Azure Active Directory에서 Google Apps를 사용하여 Single Sign-On, 자동화된 프로비저닝 등을 사용하도록 설정하는 방법을 알아봅니다."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="05/16/2016"
    ms.author="asmalser-msft"/>

#자습서: Azure Active Directory와 Google Apps를 통합하는 방법

이 자습서에서는 Google Apps 환경을 Azure Active Directory(Azure AD)에 연결하는 방법을 보여줍니다. Single Sign-On을 Google Apps에 구성하는 방법, 자동화된 사용자 프로비저닝을 사용하도록 설정하는 방법 및 사용자에게 Google Apps에 대한 액세스 권한을 할당하는 방법을 알아봅니다.

##필수 조건

1. [Azure 클래식 포털](https://manage.windowsazure.com)을 통해 Azure Active Directory에 액세스하려면 먼저 유효한 Azure 구독이 있어야 합니다.

2. [Google Apps for Work](https://www.google.com/work/apps/) 또는 [Google Apps for Education](https://www.google.com/edu/products/productivity-tools/)에 유효한 테넌트가 있어야 합니다. 어느 서비스에나 무료 평가판 계정을 사용할 수 있습니다.

##비디오 자습서

2분 안에 Google Apps에 Single Sign-On을 사용하는 방법

> [AZURE.VIDEO enable-single-sign-on-to-google-apps-in-2-minutes-with-azure-ad]

##질문과 대답

1. **Q: Chromebooks 및 기타 크롬 장치는 Azure AD Single Sign-On과 호환되나요?**

	A: 예, 사용자는 Azure AD 자격 증명을 사용하여 Chromebook 장치에 로그인할 수 있습니다. 사용자가 자격 증명을 두 번 입력해야 하는 이유는 이 [Google Apps 지원 문서](https://support.google.com/chrome/a/answer/6060880)를 참조하세요.

2. **Q: Single Sign-On을 사용하도록 설정한 경우 사용자는 Google Classroom, GMail, Google 드라이브, YouTube 등과 같은 모든 Google 제품에 로그인하는 데 자신의 Azure AD 자격 증명을 사용할 수 있나요?**

	A: 예, [Google 앱](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583)에 따라 조직에 사용하거나 사용하지 않도록 설정할 수 있습니다.

3. **Q: 내 Google 앱 사용자의 하위 집합에만 Single Sign-On을 사용할 수 있나요?**

	A: 아니요, Single Sign-On을 켜는 즉시 모든 Google 앱 사용자가 Azure AD 자격 증명으로 인증해야 합니다. Google 앱은 여러 ID 공급자를 지원하지 않으므로 Google 앱 환경의 ID 공급자는 Azure AD 또는 Google 중 하나가 될 수 있지만 동시에 둘 다가 될 수는 없습니다.

4. **Q: 사용자가 Windows를 통해 로그인하는 경우 암호 입력 메시지를 받지 않고도 Google 앱에 자동으로 인증하나요?**

	A: 이 시나리오에는 두 가지 옵션을 사용할 수 있습니다. 첫째, [Azure Active Directory 조인](active-directory-azureadjoin-overview.md)을 통해 Windows 10 장치에 로그인할 수 있습니다. 또는 [AD FS(Active Directory Federation Services)](active-directory-aadconnect-user-signin.md) 배포를 통해 Azure AD에 Single Sign-On을 사용할 수 있도록 설정한 온-프레미스 Active Directory에 도메인 가입한 Windows 장치에 로그인할 수 있습니다. 물론 두 옵션 모두 다음 자습서를 따라 Azure AD와 Google 앱 간에 Single Sign-On을 사용하도록 설정해야 합니다.

##1단계: 디렉터리에 Google Apps 추가

1. [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다.

	![왼쪽 탐색 창에서 Active Directory를 선택합니다.][0]

2. **디렉터리** 목록에서 Google Apps를 추가할 디렉터리를 선택합니다.

3. 상단 메뉴에서 **응용 프로그램**을 클릭합니다.

	![응용 프로그램을 클릭합니다.][1]

4. 페이지 맨 아래에 있는 **추가**를 클릭합니다.

	![추가 클릭하여 새 응용 프로그램을 추가합니다.][2]

5. **원하는 작업을 선택하세요.** 대화 상자에서 **갤러리에서 응용 프로그램 추가**를 클릭합니다.

	![갤러리에서 응용 프로그램 추가를 클릭합니다.][3]

6. **검색 상자**에 **Google Apps**를 입력합니다. 그런 다음 결과에서 **Google Apps**를 선택하고, **완료**를 클릭하여 응용 프로그램을 추가합니다.

	![Google Apps를 추가합니다.][4]

7. 이제 Google Apps에 대한 빠른 시작 페이지가 나타납니다.

	![Azure AD의 Google Apps 빠른 시작 페이지][5]

##2단계: Single Sign-On 사용

1. Azure AD에서 Google Apps에 대한 빠른 시작 페이지에 있는 **Single Sign-On 구성** 단추를 클릭합니다.

	![Single Sign-On 구성 단추][6]

2. 대화 상자가 열리고 "Google Apps에 대한 사용자 로그온 방법을 선택하십시오."를 묻는 화면이 표시됩니다. **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Azure AD Single Sign-On 선택][7]

	> [AZURE.NOTE] 다른 Single Sign-On 옵션에 대해 자세히 알아보려면 [여기를 클릭](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)하세요.

3. **앱 설정 구성** 페이지에서 **로그온 URL** 필드에 다음 형식으로 Google Apps 테넌트 URL을 입력합니다.`https://mail.google.com/a/<yourdomain>`

	![테넌트 URL 입력][8]

4. **자동 Single Sign-On 구성** 페이지에서 Google Apps 테넌트에 대한 도메인에 입력합니다. **구성** 단추를 입력합니다.

	![도메인 이름을 입력하고 구성을 누릅니다.](./media/active-directory-saas-google-apps-tutorial/ga-auto-config.png)

	> [AZURE.NOTE] Single sign-on을 수동으로 구성하는 것을 선호하는 경우 [선택적 단계: 수동으로 Single Sign-on 구성](#optional-step-manually-configure-single-sign-on)을 참조하세요.

5. Google Apps 관리자 계정에 로그인합니다. Azure Active Directory를 제출하기 위해 **허용**을 클릭하여 Google Apps 구독에서 구성을 변경할 수 있습니다.

	![도메인 이름을 입력하고 구성을 누릅니다.](./media/active-directory-saas-google-apps-tutorial/ga-consent.PNG)

6. Azure Active Directory가 Google Apps 테넌트를 구성하는 동안 몇 초 정도 기다립니다. 구성이 완료되면 **다음**을 클릭합니다.

10. 대화 상자의 마지막 페이지에서 이 Single Sign-On 구성 유지 관리와 관련된 오류 및 경고에 대한 전자 메일 알림을 수신하려는 경우 전자 메일 주소에 입력합니다.

	![전자 메일 주소를 입력합니다.][14]

11. **완료**를 클릭하여 대화 상자를 닫습니다. 구성을 테스트하려면 [Google Apps에 사용자 할당](#step-4-assign-users-to-google-apps) 섹션을 참조하세요.

##선택적 단계: 수동으로 Single Sign-on 구성

Single Sign-On을 수동으로 설정하는 것을 선호하는 경우 다음 단계를 완료합니다.

1. Azure AD에서 Google Apps에 대한 빠른 시작 페이지에 있는 **Single Sign-On 구성** 단추를 클릭합니다.

	![Single Sign-On 구성 단추][6]

2. 대화 상자가 열리고 "Google Apps에 대한 사용자 로그온 방법을 선택하십시오."를 묻는 화면이 표시됩니다. **Azure AD Single Sign-On**을 선택하고 **다음**을 클릭합니다.

	![Azure AD Single Sign-On 선택][7]

	> [AZURE.NOTE] 다른 Single Sign-On 옵션에 대해 자세히 알아보려면 [여기를 클릭](../active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)하세요.

3. **앱 설정 구성** 페이지에서 **로그온 URL** 필드에 다음 형식으로 Google Apps 테넌트 URL을 입력합니다.`https://mail.google.com/a/<yourdomain>`

	![테넌트 URL 입력][8]

4. **Single Sign-On 자동 구성** 페이지에서 **수동으로 Single Sign On하도록 이 응용 프로그램 구성**이라는 확인란을 선택합니다. 그런 후 **Next**를 클릭합니다.

	![수동 구성을 선택합니다.](./media/active-directory-saas-google-apps-tutorial/ga-auto-skip.PNG)

4. **Google Apps에서 Single Sign-On 구성** 페이지에서 **인증서 다운로드**를 클릭한 다음 컴퓨터에 로컬로 인증서 파일을 저장합니다.

	![인증서를 다운로드합니다.][9]

5. 브라우저에서 새 탭을 열고 관리자 계정을 사용하여 [ 관리 콘솔](http://admin.google.com/)에 로그인합니다.

6. **보안**을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

	![보안을 클릭합니다.][10]

7. **보안** 페이지에서 **SSO(Single Sign-On) 설정**을 클릭합니다.

	![SSO를 클릭합니다.][11]

8. 다음 구성을 변경합니다.

	![SSL 구성][12]

	- **Setup SSO with third party identity provider(타사 ID 공급자로 SSO 설정)**을 선택합니다.

	- Azure AD에서 **Single Sign-On 서비스 URL**을 복사하여 Google Apps의 **로그인 페이지 URL** 필드에 붙여 넣습니다.

	- Azure AD에서 **Single Sign-On 서비스 URL**을 복사하여 Google Apps의 **로그아웃 페이지 URL** 필드에 붙여 넣습니다.

	- Azure AD에서 **암호 변경 URL**을 복사하여 Google Apps의 **암호 변경 URL** 필드에 붙여 넣습니다.

	- Google Apps의 **확인 인증서**에 대해 4단계에서 다운로드한 인증서를 업로드합니다.

	- **변경 내용 저장**을 클릭합니다.

9. Azure AD에서 Google Apps에 업로드한 인증서를 사용하도록 설정하려면 Single Sign-On 구성 확인 확인란을 선택합니다. 그런 후 **Next**를 클릭합니다.

	![확인 확인란 선택][13]

10. 대화 상자의 마지막 페이지에서 이 Single Sign-On 구성 유지 관리와 관련된 오류 및 경고에 대한 전자 메일 알림을 수신하려는 경우 전자 메일 주소에 입력합니다.

	![전자 메일 주소를 입력합니다.][14]

11. **완료**를 클릭하여 대화 상자를 닫습니다. 구성을 테스트하려면 [Google Apps에 사용자 할당](#step-4-assign-users-to-google-apps) 섹션을 참조하세요.

##3단계: 자동화된 사용자 프로비저닝 사용

> [AZURE.NOTE] Google Apps로 사용자 프로비저닝을 자동화하는 데 실행 가능한 다른 옵션은 온-프레미스 Active Directory ID를 Google Apps에 프로비전하는 [GADS(Google Apps Directory Sync)](https://support.google.com/a/answer/106368?hl=en)를 사용하는 것입니다. 반대로 이 자습서의 솔루션은 Azure Active Directory(클라우드) 사용자 및 메일 사용이 가능한 그룹을 Google Apps에 프로비전합니다.

1. 관리자 계정을 사용하여 [Google Apps 관리 콘솔](http://admin.google.com/)에 로그인하고 **보안**을 클릭합니다. 링크가 보이지 않으면 화면 아래쪽에 있는 **기타 컨트롤** 메뉴에 숨겨져 있을 수 있습니다.

	![보안을 클릭합니다.][10]

2. **보안**페이지에서 **API 참조**를 클릭합니다.

	![API 참조를 클릭합니다.][15]

3. **API 액세스 사용**을 선택합니다.

	![API 참조를 클릭합니다.][16]

	> [AZURE.IMPORTANT] Google Apps로 프로비전하려는 모든 사용자에 대해 Azure Active Directory에서 해당 사용자 이름을 사용자 지정 도메인에 연결*해야* 합니다. 예를 들면 bob@contoso.onmicrosoft.com와 같은 사용자 이름은 Google Apps에서 허용되지 않지만 bob@contoso.com은 허용됩니다. Azure AD에서 해당 속성을 편집하여 기존 사용자의 도메인을 변경할 수 있습니다. Azure Active Directory 및 Google Apps 모두에 대한 사용자 지정 도메인을 설정하는 방법에 대한 지침이 아래에 포함되어 있습니다.

4. Azure Active Directory에 사용자 지정 도메인 이름을 아직 추가하지 않은 경우에는 아래 단계를 따릅니다.

	- [Azure 클래식 포털](https://manage.windowsazure.com)의 왼쪽 탐색 창에서 **Active Directory**를 클릭합니다. 디렉터리 목록에서 해당 디렉터리를 선택합니다.

	- 최상위 메뉴에서 **도메인**을 클릭하고 **사용자 지정 도메인 추가**를 클릭합니다.

		![사용자 지정 도메인 추가][17]

	- **도메인 이름** 필드에 사용자 도메인 이름을 입력합니다. 이 경우 Google Apps에 사용하려는 같은 도메인 이름이이어야 합니다. 준비가 되면 **추가** 단추를 클릭합니다.

		![도메인 이름을 입력합니다.][18]

	- **다음**을 클릭하여 확인 페이지로 이동합니다. 이 도메인을 소유했는지 확인하려면 이 페이지에 제공된 값에 따라 도메인의 DNS 레코드를 편집해야 합니다. **레코드 종류** 옵션에 대해 선택한 항목에 따라 **MX 레코드** 또는 **TXT 레코드**를 사용하여 확인하도록 선택할 수 있습니다. Azure AD에서 도메인 이름을 확인하는 방법에 대한 포괄적인 지침은 [Azure AD에 고유한 도메인 이름 추가](https://go.microsoft.com/fwLink/?LinkID=278919&clcid=0x409)를 참조하세요.

		![도메인 이름을 확인합니다.][19]

	- 디렉터리에 추가하려는 모든 도메인에 대해 위의 단계를 반복합니다.

5. Azure AD에서 모든 도메인을 확인했으므로 이제 Google Apps에서 다시 확인해야 합니다. Google Apps에 아직 등록되지 않은 각 도메인에 대해 다음 단계를 수행합니다.

	- [Google Apps 관리 콘솔](http://admin.google.com/)에서 **도메인**을 클릭합니다.

		![도메인을 클릭합니다.][20]

	- **Add a domain or a domain alias(도메인 또는 도메인 별칭 추가)**를 클릭합니다.

		![새 도메인 추가][21]

	- **다른 도메인 추가**를 선택하고 추가하려는 도메인 이름을 입력합니다.

		![사용자의 도메인 이름을 입력합니다.][22]

	- **Continue and verify domain ownership(계속 도메인 소유권 확인)**을 클릭합니다. 그런 다음 도메인 이름을 소유하고 있는지 확인하는 단계를 따릅니다. Google Apps에서 도메인을 확인하는 방법에 대한 포괄적인 지침에 대해서는 [Google Apps에서 사이트 소유권 확인](https://support.google.com/webmasters/answer/35179)(영문)을 참조하세요.

	- Google Apps에 추가하려는 모든 추가 도메인에 대해 위의 단계를 반복합니다.

	> [AZURE.WARNING] Google Apps 테넌트에 대한 기본 도메인을 변경하는 경우 및 Azure AD에서 Single Sign-On을 이미 구성한 경우 [2단계: Single Sign-On 사용](#step-two-enable-single-sign-on)에 있는 3단계를 반복해야 합니다.

6. [Google Apps 관리 콘솔](http://admin.google.com/)에서 **관리자 역할**을 클릭합니다.

	![Google Apps 클릭][26]

7. 사용자 프로비저닝을 관리하는 데 사용할 관리자 계정을 결정합니다. 해당 계정의 **관리자 역할**에서 해당 역할에 대한 **권한**을 편집합니다. 이 계정을 프로비전하는 데 사용할 수 있도록 모든 **관리자 API 권한**이 설정되었는지 확인합니다.

	![Google Apps 클릭][27]

	> [AZURE.NOTE] 프로덕션 환경을 구성하는 경우 가장 좋은 방법은 이 단계에 대해 구체적으로 Google Apps에서 새 관리자 계정을 만드는 것입니다. 이러한 계정에는 필요한 API 권한이 있는 계정과 연결된 관리자 역할이 있어야 합니다.

8. Azure Active Directory에서 최상위 메뉴에 있는 **응용 프로그램**을 클릭한 다음 **Google Apps**를 클릭합니다.

	![Google Apps 클릭][23]

9. Google Apps의 빠른 시작 페이지에서 클릭**사용자 프로비전 구성**을 클릭합니다.

	![사용자 프로비저닝 구성][24]

10. 열리는 대화 상자에서 **사용자 프로비전 사용**을 클릭하여 프로 비저닝을 관리하는 데 사용할 Google Apps 관리자 계정으로 인증합니다.

	![프로비저닝 사용][25]

11. Azure Active Directory에 Google Apps 테넌트를 변경할 권한을 제공할 것인지 확인합니다.

	![사용 권한을 확인합니다.][28]

12. **완료**를 클릭하여 대화 상자를 닫습니다.

##4단계: Google Apps에 사용자 할당

1. 구성을 테스트하려면 디렉터리에 새 테스트 계정 만들기를 시작합니다.

2. Google Apps 빠른 시작 페이지에서 **사용자 할당** 단추를 클릭합니다.

	![사용자 할당을 클릭합니다.][29]

3. 테스트 사용자를 선택하고 화면 아래쪽에 있는 **할당** 단추를 클릭합니다.

 - 자동화된 사용자 프로비저닝 사용하지 않도록 설정하면 다음을 확인하는 메시지가 표시됩니다.

		![Confirm the assignment.][30]

 - 자동화된 사용자 프로비저닝 사용하도록 설정하면 Google Apps에서 사용자가 갖고 있어야 하는 역할 유형을 정의하라는 메시지가 표시됩니다. 잠시 후에 새로 프로비저닝된 사용자가 Google Apps 환경에 표시됩니다.

4. Single Sign-On 설정을 테스트하려면 [https://myapps.microsoft.com](https://myapps.microsoft.com/)에서 액세스 패널을 연 다음 테스트 계정에 로그인하고 **Google Apps**를 클릭합니다.

## 관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)

[0]: ./media/active-directory-saas-google-apps-tutorial/azure-active-directory.png
[1]: ./media/active-directory-saas-google-apps-tutorial/applications-tab.png
[2]: ./media/active-directory-saas-google-apps-tutorial/add-app.png
[3]: ./media/active-directory-saas-google-apps-tutorial/add-app-gallery.png
[4]: ./media/active-directory-saas-google-apps-tutorial/add-gapps.png
[5]: ./media/active-directory-saas-google-apps-tutorial/gapps-added.png
[6]: ./media/active-directory-saas-google-apps-tutorial/config-sso.png
[7]: ./media/active-directory-saas-google-apps-tutorial/sso-gapps.png
[8]: ./media/active-directory-saas-google-apps-tutorial/sso-url.png
[9]: ./media/active-directory-saas-google-apps-tutorial/download-cert.png
[10]: ./media/active-directory-saas-google-apps-tutorial/gapps-security.png
[11]: ./media/active-directory-saas-google-apps-tutorial/security-gapps.png
[12]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-config.png
[13]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-confirm.png
[14]: ./media/active-directory-saas-google-apps-tutorial/gapps-sso-email.png
[15]: ./media/active-directory-saas-google-apps-tutorial/gapps-api.png
[16]: ./media/active-directory-saas-google-apps-tutorial/gapps-api-enabled.png
[17]: ./media/active-directory-saas-google-apps-tutorial/add-custom-domain.png
[18]: ./media/active-directory-saas-google-apps-tutorial/specify-domain.png
[19]: ./media/active-directory-saas-google-apps-tutorial/verify-domain.png
[20]: ./media/active-directory-saas-google-apps-tutorial/gapps-domains.png
[21]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-domain.png
[22]: ./media/active-directory-saas-google-apps-tutorial/gapps-add-another.png
[23]: ./media/active-directory-saas-google-apps-tutorial/apps-gapps.png
[24]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning.png
[25]: ./media/active-directory-saas-google-apps-tutorial/gapps-provisioning-auth.png
[26]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin.png
[27]: ./media/active-directory-saas-google-apps-tutorial/gapps-admin-privileges.png
[28]: ./media/active-directory-saas-google-apps-tutorial/gapps-auth.png
[29]: ./media/active-directory-saas-google-apps-tutorial/assign-users.png
[30]: ./media/active-directory-saas-google-apps-tutorial/assign-confirm.png

<!---HONumber=AcomDC_0706_2016-->