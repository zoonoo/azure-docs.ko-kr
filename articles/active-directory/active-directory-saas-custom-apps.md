<properties 
    pageTitle="Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성 | Microsoft Azure" 
    description="셀프 서비스가 SAML 및 암호 기반 SSO로 Azure Active Directory에 앱을 연결하는 방법을 알아봅니다." 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="11/18/2015" 
    ms.author="asmalser" />

#Azure Active Directory 응용 프로그램 갤러리에 있지 않은 응용 프로그램에 Single Sign-On 구성

Azure Active Directory 응용 프로그램 갤러리는 [이 문서]( https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)에 설명한 대로 Azure Active Directory와의 Single Sign-On 형식을 지원하는 것으로 알려진 응용 프로그램 목록을 제공합니다. (조직에서 IT 전문가 또는 시스템 통합자인 경우)연결한 응용 프로그램을 찾으면 Azure 관리 포털에 Single Sign-On 활성화에 대해 제공된 단계별 지침을 따라 시작할 수 있습니다.

[Azure Active Directory Premium](https://msdn.microsoft.com/library/azure/dn532272.aspx) 라이선스를 가진 고객은 Azure AD 응용 프로그램 갤러리의 **사용자 지정** 범주에서 호출할 수 있는 이러한 추가 기능을 가져옵니다.

* SAML 2.0 ID 공급자를 지원하는 응용 프로그램의 셀프 서비스 연결
* 암호 기반 SSO를 사용하여 HTML 기반 로그인 페이지가 있는 웹 응용 프로그램의 셀프 서비스 연결
* [Office 365 앱 시작 관리자]( https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널]( https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users/)에서 응용 프로그램에 대한 링크를 추가하는 기능
* 사용자 프로비전에 SCIM 프로토콜을 사용하는 응용 프로그램의 셀프 서비스 연결([여기에 설명됨](active-directory-scim-provisioning))

사용하지만 아직된 Azure AD 응용 프로그램 갤러리에 등록되지 않은 SaaS 응용 프로그램 뿐만 아니라 조직이 클라우드 또는 온-프레미스에서 제어하는 서버에 배포된 타사 웹 응용 프로그램을 포함할 수 있습니다.

참고: 해당 응용 프로그램과 이 기능 간의 호환성을 테스트하려는 응용 프로그램 개발자는 [Azure Active Directory Premium의 무료 평가판](https://azure.microsoft.com/trial/get-started-active-directory/)을 사용하여 수행할 수 있지만 [내부 사용권 라이선스](https://mspartner.microsoft.com/en/us/pages/membership/internal-use-software.aspx)를 획득하는 것이 좋습니다.

##목록에 없거나 사용자 지정된 응용 프로그램 추가 

응용 프로그램을 구성하려면 Azure Active Directory 관리자 계정을 사용하여 Azure 관리 포털에 로그인하고 **Active Directory > [Directory] > 응용 프로그램** 섹션으로 이동하며 **추가** 및 **갤러리에서 응용 프로그램 추가**를 차례로 선택합니다.

![][1]

원하는 응용 프로그램을 찾을 수 없는 경우 앱 갤러리의 왼쪽에서 **사용자 지정** 범주를 사용하거나 검색 결과에 표시된 **목록에 없는 응용 프로그램 추가** 링크를 선택하여 사용자 지정 응용 프로그램을 추가할 수 있습니다 응용 프로그램에 이름을 입력한 후에 Single Sign On 옵션 및 동작을 구성할 수 있습니다.

**빠른 팁**: 모범 사례로 응용 프로그램 갤러리에 응용 프로그램이 이미 존재하는지 확인하려면 검색 기능을 사용합니다. 앱이 발견되고 해당 설명이 "Single Sign On"을 언급하는 경우 응용 프로그램은 이미 페더레이션된 Single Sign-On에 대해 지원됩니다.

![][2]

사용자 지정 응용 프로그램을 추가하면 사전 통합된 응용 프로그램에 사용할 수 있는 데에 매우 비슷한 환경을 제공합니다. 시작하려면 **Single Sign-On 구성**을 선택합니다. 다음 화면에서는 다음 섹션에서 설명하는 을 Single Sign On을 구성하기 위해 다음 세 가지 옵션을 나타냅니다.

![][3]


##Azure AD Single Sign-On

이 옵션을 선택하여 응용 프로그램에 대한 SAML 기반 인증을 구성합니다. 응용 프로그램이 SAML 2.0를 지원해야 하고 계속하기 전에 응용 프로그램의 SAML 기능을 사용하는 방법에 대한 정보를 수집해야 합니다. **다음**을 선택한 후에 응용 프로그램에 대한 SAML 끝점에 해당하는 세 개의 URL을 입력하라는 메시지가 표시됩니다.

![][4]

대화 상자에서 도구 설명 아이콘은 각 URL의 정의 및 사용하는 방법에 대한 세부 정보를 제공합니다. 이러한 내용을 입력한 후에 **다음**을 클릭하여 다음 화면으로 진행합니다. 이 화면은 응용 프로그램쪽에서 구성되어야 하는 사항에 대한 정보를 제공하여 Azure AD에서 SAML 토큰을 수락하도록 설정합니다.

![][5]

어떤 값이 필요한지는 응용 프로그램에 따라 달라지므로 세부 정보에 대한 응용 프로그램의 SAML 설명서를 확인합니다. **로그인** 및 **로그아웃** 서비스 URL은 모두 동일한 끝점으로 해결하며 이는 Azure AD의 인스턴스에 대한 SAML 요청 처리 끝점입니다. 발급자 URL은 응용 프로그램에 발급된 SAML 토큰 내에서 "발급자"로 나타나는 값입니다.

응용 프로그램을 구성한 후에 **다음** 단추 및 **완료**를 차례로 클릭하여 대화 상자를 닫습니다.

##SAML 응용 프로그램에 사용자 및 그룹 할당 

응용 프로그램이 Azure AD를 SAML 기반 ID 공급자로 사용하도록 구성되면 테스트할 준비가 다 됩니다. 보안 컨트롤인 Azure AD는 Azure AD를 사용하여 액세스를 허용한 경우가 아니면 응용 프로그램에 로그인하도록 허용하는 토큰을 발급하지 않습니다. 사용자는 직접적으로 또는 멤버인 그룹을 통해 액세스를 부여받을 수 있습니다.

응용 프로그램에 사용자 또는 그룹을 할당하려면 **사용자 할당** 단추를 클릭합니다. 할당하려는 사용자 또는 그룹을 선택한 다음 **할당** 단추를 선택합니다.

![][6]

사용자를 할당하면 Azure AD가 사용자의 액세스 패널에 표시할 이 응용 프로그램의 타일을 발생시킬 뿐만 아니라 사용자에 토큰을 발급하도록 합니다. 또한 사용자가 Office 365를 사용하는 경우 응용 프로그램 타일은 Office 365 응용 프로그램 시작 관리자에 표시됩니다.

응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다.

###SAML 토큰에 발급된 클레임 사용자 지정 

사용자가 응용 프로그램에 인증할 때 Azure AD는 고유하게 식별하는 사용자에 대한 정보(또는 클레임)를 포함하는 앱에 SAML 토큰을 발급합니다. 기본적으로 사용자의 사용자 이름, 전자 메일 주소, 이름 및 성을 포함합니다.

**특성** 탭에서 응용 프로그램의 SAML 토큰에 전송된 클레임을 보거나 편집할 수 있습니다.

![][7]

SAML 토큰에서 발급된 클레임을 편집해야 할 수도 있는 두 가지 가능한 이유는 다음과 같습니다. •응용 프로그램이 다른 클레임 URI 또는 클레임 값의 집합이 필요하도록 작성되었습니다. •NameIdentifier 클레임이 Azure Active Directory에 저장된 사용자 이름(즉, 사용자 계정 이름)이 아닌 다른 항목이어야 하는 방식으로 응용 프로그램이 배포되었습니다.

이러한 시나리오에 대한 클레임을 추가하고 편집하는 방법에 대한 정보는 [클레임 사용자 지정에 대한 문서](active-directory-saml-claims-customization.md)를 확인합니다.

###SAML 응용 프로그램 테스트 

SAML URL 및 인증서가 Azure AD 및 응용 프로그램에서 구성되면 사용자 또는 그룹은 Azure에서 응용 프로그램에 할당되고 클레임은 필요한 경우 검토 및 편집되며 사용자는 응용 프로그램에 로그인하도록 준비됩니다.

응용 프로그램에 할당된 사용자 계정을 사용하여 https://myapps.microsoft.com에서 Azure AD 액세스 패널에 로그인하는지를 테스트하려면 응용 프로그램에 대한 타일을 클릭하여 Single Sign-On 프로세스를 시작합니다. 또는 여기에서 응용 프로그램 및 로그인에 대한 로그인 URL을 직접 찾아볼 수 있습니다.

디버깅 팁은 [SAML 기반 Single Sign-On 응용 프로그램을 디버깅하는 방법에 대 한 문서](active-directory-saml-debugging.md)를 참조하세요.

##암호 Single Sign-On

이 옵션을 선택하여 HTML 로그인 페이지를 포함하는 웹 응용 프로그램에 [암호 기반 Single Sign-On](active-directory-appssoaccess-whatis.md)을 구성합니다. 암호 보관이라고도 하는 암호 기반 SSO를 사용하면 ID 페더레이션을 지원하지 않는 웹 응용 프로그램에 대한 사용자 액세스 및 암호를 관리할 수 있습니다. 또한 여러 사용자가 조직의 소셜 미디어 앱 계정과 같은 단일 계정을 공유해야 하는 시나리오에 유용합니다.

**다음**을 선택한 후에 응용 프로그램의 웹 기반 로그인 페이지의 URL을 입력하라는 메시지가 표시됩니다. 사용자 이름 및 암호 입력 필드를 포함하는 페이지여야 합니다. 입력하면 Azure AD는 사용자 이름 및 암호 입력에 대한 로그인 페이지를 구문 분석하는 프로세스를 시작합니다. 프로세스가 실패한 경우 필드를 수동으로 캡처할 수 있도록 브라우저 확장을 설치하는 다른 과정을 안내합니다.(Internet Explorer, Chrome 또는 Firefox 필요)

로그인 페이지가 캡처되면 사용자 및 그룹은 할당될 수 있고 일반 [암호 SSO 앱](active-directory-appssoaccess-whatis.md)과 마찬가지로 자격 증명 정책을 설정할 수 있습니다 .

참고: 응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다.

##기존 Single Sign-On

이 옵션을 선택하여 조직의 Azure AD 액세스 패널 또는 Office 365 포털에 응용 프로그램에 대한 링크를 추가합니다. 인증에 대한 Azure AD 대신 현재 Azure Active Directory Federation Services(또는 다른 페더레이션 서비스)를 사용하는 사용자 지정 웹앱에 대한 링크를 추가하는 데 사용할 수 있습니다. 혹은 사용자의 액세스 패널에 표시하려는 특정 SharePoint 페이지 또는 다른 웹 페이지에 딥 링크를 추가할 수 있습니다.

**다음**을 선택한 후에 링크할 응용 프로그램의 URL을 입력하라는 메시지가 표시됩니다. 완료되면 사용자 및 그룹은 응용 프로그램에 할당될 수 있으며 이는 응용 프로그램이 해당 사용자에 대한 [Office 365 앱 시작 관리자]( https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) 또는 [Azure AD 액세스 패널]( https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/#deploying-azure-ad-integrated-applications-to-users/)에 표시되도록 합니다.

참고: 응용 프로그램에 **구성** 탭의 **로고 업로드** 단추를 사용하여 응용 프로그램에 대한 타일 로고를 업로드할 수 있습니다.

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png

<!---HONumber=AcomDC_0128_2016-->