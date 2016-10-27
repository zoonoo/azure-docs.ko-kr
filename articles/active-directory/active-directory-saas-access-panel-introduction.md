<properties
    pageTitle="액세스 패널 소개 | Microsoft Azure"
    description="다양한 액세스 패널(웹 브라우저, Android 앱, iPhone 및 iPad 응용 프로그램)을 사용해 할당된 SaaS 앱에 액세스하는 방법을 알아봅니다."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi"/>



# <a name="introduction-to-the-access-panel"></a>액세스 패널 소개


액세스 패널은 최종 사용자가 Azure Active Directory의 조직 계정을 사용하여 Azure AD 관리자가 액세스 권한을 부여한 클라우드 기반의 응용 프로그램을 보고 시작할 수 있도록 하는 웹 기반 포털입니다. Azure Active Directory 버전을 사용하는 최종 사용자인 경우 액세스 패널을 통해 셀프 서비스 그룹 관리 기능도 활용할 수 있습니다. <br>
 액세스 패널은 Azure 관리 포털과 별개이며, 사용자가 Azure 구독을 하지 않아도 됩니다. 


![액세스 패널][1] 


액세스 패널은 사용자가 다음의 기능을 포함한 프로필 설정의 일부를 편집할 수 있도록 합니다.

- 조직 계정과 관련된 암호 변경

- 암호 재설정 편집

- 다단계 인증 관련 연락처 및 기본 설정 (관리자에 의해 사용되어야 하는 계정) 편집

- 사용자 ID, 대체 메일, 모바일 및 사무실 전화번호 등을 포함한 계정 세부 정보 보기

- Azure AD 관리자가 액세스 권한을 부여한 클라우드 기반의 응용 프로그램을 보거나 시작하기 최종 사용자 관점에서 액세스 패널에 대한 자세한 정보를 보려면 [액세스 패널 사용하기](https://msdn.microsoft.com/library/azure/dn756411.aspx)를 참조하십시오.

- 그룹을 셀프 관리합니다. 보다 구체적으로, 보안 그룹을 만들거나 관리할 수 있으며 Azure AD에서의 보안 그룹 자격을 요청할 수 있습니다. 자세한 내용은 [Azure AD의 사용자를 위한 셀프 서비스 그룹 관리](active-directory-accessmanagement-self-service-group-management.md)와 [그룹 관리](active-directory-manage-groups.md)를 참조하세요. 




## <a name="accessing-the-access-panel"></a>액세스 패널 액세스


사용자는 웹 브라우저에서 다음의 URL을 방문하여 액세스 패널에 액세스합니다. <br> 
**http://myapps.microsoft.com**

로그인 페이지에서 사용자 지정 브랜딩을 구성한 경우 URL의 끝에 조직의 도메인을 추가하여 기본값으로 해당 브랜딩을 로드할 수 있습니다. <br> 
**http://myapps.microsoft.com/contosobuild.com**

이 경우 Azure 관리 포털에서 디렉터리의 도메인 탭 아래에 구성된 활성화 또는 확인된 도메인 이름은 아래 스크린샷에 설명된 것처럼 사용될 수 있습니다.


![Wingtip Toys][2]  


이 URL은 Azure AD와 통합된 응용 프로그램에 로그인할 모든 사용자에게 배포되어야 합니다.
 




## <a name="authentication"></a>인증

액세스 패널에 도달하기 위해 사용자는 Azure AD에서 조직 계정을 사용해 인증되어야 합니다. <br>
 사용자는 Azure AD에서 직접 인증을 받을 수 있습니다. <br>
 또는 조직이 ADFS나 다른 기술을 사용하여 페더레이션을 구성한 경우 Windows Server Active Directory에서 인증을 받을 수도 있습니다.

사용자가 Azure 또는 Office 365를 구독하고 Azure 관리 포털이나 Office 365 응용 프로그램을 사용하고 있으면 다시 로그인할 필요 없이 응용 프로그램의 목록이 나타납니다. 인증되지 않은 사용자는 Azure AD 계정에 대한 사용자 이름 및 암호를 사용하여 로그인할 것을 요청받습니다. 조직이 페더레이션을 구성한 경우 사용자 이름을 입력하면 충분합니다.

인증이 되면 사용자는 관리자에 의해 디렉터리와 통합된 응용 프로그램을 조작할 수 있습니다. 응용 프로그램을 Azure AD와 통합하는 방법을 보려면 [Azure Active Directory를 사용한 응용 프로그램 액세스 및 Single Sign-On](active-directory-appssoaccess-whatis.md)을 참조하세요.
 




## <a name="web-browser-requirements"></a>웹 브라우저 요구 사항

액세스 패널은 최소한 JavaScript 및 CSS를 지원하는 브라우저가 필요합니다. 사용자가 암호 기반의 SSO를 사용하여 응용 프로그램에 로그인하려면 액세스 패널 확장이 사용자의 브라우저에 설치되어있어야 합니다. 이 액세스 패널 확장은 사용자가 암호 기반의 SSO에 대해 구성된 응용 프로그램을 선택한 경우 자동으로 다운로드됩니다.

현재 액세스 패널 확장은 Internet Explorer 8 이상, Chrome 및 Firefox 브라우저에서 사용 가능합니다.





## <a name="mobile-app-support"></a>모바일 앱 지원

iOS 및 Android 장치에서 암호 기반의 SSO 응용 프로그램에 로그인하려면 사용자는 Azure Active Directory 팀이 게시한 My Apps 모바일 앱을 설치해야 합니다.





### <a name="my-apps-for-android"></a>Android 용 My Apps


Android용 My Apps는 Android 버전 4.1 이상을 실행하는 모든 Android 장치에서 지원되며 현재 [Google Play 스토어](https://play.google.com/store/apps/details?id=com.microsoft.myapps)에서 다운로드할 수 있습니다.


![My apps][3]   






### <a name="my-apps-for-iphone-and-ipad"></a>iPhone 및 iPad 용 My Apps


iOS 용 My Apps는 iOS 버전 7 이상을 실행하는 모든 iPhone이나 iPad에서 지원되며, 현재 Apple 앱스토어에서 다운로드 가능합니다.


![응용 프로그램 프로필][4]    




> [AZURE.NOTE] Azure AD로 페더레이션을 지원하는 응용 프로그램은 플러그인 또는 모바일 앱을 요구하지 않고 모든 장치에서 거의 모든 웹 브라우저에서 (Salesforce, Google Apps, Dropbox, 상자, Concur, Workday, Office 365 등 70 포함) 로그인할 수 있습니다. [https://myapps.microsoft.com](https://myapps.microsoft.com/) 의 다른 액세스 패널 환경의 경우에도 모바일 장치에서 My Apps 모바일 앱을 사용할 필요가 없습니다.
 


 

## <a name="tips-for-testing-the-end-user-experience"></a>최종 사용자 환경 테스트를 위한 팁

Azure 관리자이고 디렉터리에 있는 계정을 사용하여 Azure 관리 포털에 로그인한 경우 액세스 패널에 현재 관리자 계정으로 자동 로그인됩니다. 이 경우 해당 계정에 할당된 모든 응용 프로그램을 볼 수 있습니다.

**다른 사용자 계정으로 테스트하기**

1. Azure 포털이나 액세스 패널의 오른쪽 위에 있는 사용자 메뉴를 클릭하고 "**Sign Out**"을 선택합니다. 이로써 Azure AD에서 로그아웃합니다.

2. **http://myapps.microsoft.com**에서 액세스 패널로 이동합니다.

3. 로그인 페이지에서 테스트하고자 하는 디렉터리의 계정에 대한 사용자 이름 및 암호를 입력합니다.
 
## <a name="launching-applications"></a>응용 프로그램 시작하기

액세스 패널에서 나타날 수 있는 응용 프로그램에는 여러 종류가 있습니다.
 
### <a name="office-365-applications"></a>Office 365 응용 프로그램

조직이 Office 365 응용 프로그램을 사용하고 사용자가 허가를 받은 경우 Office 365 응용 프로그램이 사용자의 액세스 패널에 나타납니다.

사용자가 Office 365 응용 프로그램에 대한 응용 프로그램 타일을 클릭한 경우 해당 응용 프로그램으로 리디렉션되고 자동 로그인됩니다.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>페더레이션 기반의 SSO로 구성된 Microsoft 및 타사 응용 프로그램

관리자가 Azure 관리 포털의 Active Directory 섹션에 “*Azure AD Single Sign-On*”으로 설정된 Single Sign-On 모드로 추가한 응용 프로그램입니다. 응용 프로그램에 대한 액세스가 관리자에 의해 명시적으로 부여된 경우 사용자는 이 응용 프로그램들만 볼 수 있습니다.

사용자가 이 응용 프로그램에 대한 응용 프로그램 타일을 클릭한 경우 해당 응용 프로그램으로 리디렉션되고 자동 로그인됩니다.

### <a name="password-based-sso-without-identity-provisioning"></a>ID 프로비전 없는 암호 기반 SSO

관리자가 Azure 관리 포털의 Active Directory 섹션에 “*Password-based Single Sign-On*”으로 설정된 Single Sign-On 모드로 추가한 응용 프로그램입니다. <br>  디렉터리의 모든 사용자가 이 모드에 구성된 모든 응용 프로그램을 볼 수 있습니다.

처음에 사용자가 이 응용 프로그램 중의 하나인 응용 프로그램 타일을 클릭하면 Internet Explorer 또는 Chrome에 대한 암호 SSO 플러그인 설치가 요청되며 웹 브라우저를 재시작해야 할 수도 있습니다. 액세스 패널로 돌아와 응용 프로그램 타일을 다시 클릭하면 응용 프로그램에 대한 사용자 이름 및 암호 요청 메시지가 보입니다. 사용자 이름 및 암호를 입력하면 이 자격 증명은 Azure AD에 보안 저장되며 Azure AD에 있는 계정에 연결되고, 액세스 패널은 이 자격 증명을 사용하여 응용 프로그램에 사용자 로그인을 자동화합니다.

다음에 사용자가 응용 프로그램 타일을 클릭하면 자격 증명을 다시 입력하거나 암호 SSO 플러그인을 다시 설치할 필요 없이 자동으로 응용 프로그램에 로그인됩니다.

사용자의 자격 증명이 타사 응용 프로그램에서 변경된 경우 사용자는 Azure AD에 저장된 자격 증명 또한 업데이트해야 합니다. 자격 증명을 업데이트하려면 사용자는 응용 프로그램 타일의 오른쪽 아래에 있는 아이콘을 선택하고 “자격 증명 업데이트”를 클릭하여 응용 프로그램에 대한 사용자 이름과 암호를 재입력합니다.

### <a name="password-based-sso-with-identity-provisioning"></a>ID 프로비전을 사용한 암호 기반 SSO

관리자가 Azure 관리 포털의 Active Directory 섹션에 “*Password-based Single Sign-On*”으로 설정된 Single Sign-On 모드 및 ID 프로비전으로 추가한 응용 프로그램입니다.

처음에 사용자가 이 응용 프로그램 중의 하나인 응용 프로그램 타일을 클릭하면 Internet Explorer 또는 Chrome에 대한 암호 SSO 플러그인 설치가 요청되며 웹 브라우저를 재시작해야 할 수도 있습니다. 액세스 패널로 돌아와 응용 프로그램 타일을 다시 클릭하면 자동으로 응용 프로그램에 로그인됩니다.

일부 응용 프로그램에서는 사용자가 처음으로 로그인할 때 암호 변경이 필요할 수 있습니다. 사용자의 자격 증명이 타사 응용 프로그램에서 변경된 경우 사용자는 Azure AD에 저장된 자격 증명 또한 업데이트해야 합니다. 자격 증명을 업데이트하려면 사용자는 응용 프로그램 타일의 오른쪽 아래에 있는 아이콘을 선택하고 “자격 증명 업데이트”를 클릭하여 응용 프로그램에 대한 사용자 이름과 암호를 재입력합니다.

### <a name="application-with-existing-sso-solutions"></a>기존 SSO 솔루션을 사용한 응용 프로그램

응용 프로그램에 대한 Single Sign-On을 구성할 때 Azure 관리 포털은 "기존 Single Sign-On”의 세 번째 옵션을 제공합니다. 이 옵션을 사용하면 관리자가 응용 프로그램에 대한 링크를 만들고 선택한 사용자에 대한 액세스 패널에 배치할 수 있습니다. 예를 들어 Active Directory Federation Services 2.0을 사용하여 사용자를 인증하도록 구성된 응용 프로그램이 있는 경우 관리자가 "기존 Single Sign-On" 옵션을 사용하여 액세스 패널에서 이에 대한 링크를 만들 수 있습니다. 사용자가 이 링크에 액세스하는 경우 Active Directory Federation Services 2.0을 사용하거나 응용 프로그램에서 제공하는 기존 Single Sign-On 솔루션을 사용하여 인증을 받습니다.

##<a name="related-articles"></a>관련 문서

- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [SaaS App을 통합하는 방법에 대한 자습서 목록](active-directory-saas-tutorial-list.md)
- [Azure Active Directory를 통한 Single Sign-on 및 응용 프로그램 액세스 관리 소개](active-directory-appssoaccess-whatis.md)
- [SaaS 응용 프로그램에 자동화된 사용자 프로비저닝 및 프로비저닝 해제](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png



<!--HONumber=Oct16_HO2-->


