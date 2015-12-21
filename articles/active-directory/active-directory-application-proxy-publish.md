<properties
	pageTitle="Azure AD 응용 프로그램 프록시를 사용하여 앱 게시 | Microsoft Azure"
	description="Azure AD 응용 프로그램 프록시를 사용하여 온-프레미스 응용 프로그램을 게시하는 방법을 설명합니다."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="kgremban"/>


# Azure AD 응용 프로그램 프록시를 사용하여 응용 프로그램 게시

> [AZURE.NOTE]응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

Microsoft Azure AD 응용 프로그램 프록시를 사용하도록 설정한 후에 응용 프로그램을 게시하여 개인 네트워크 외부에서 사용자가 액세스하도록 만들 수 있습니다.

이 문서에서는 네트워크 외부에서 안전한 원격 액세스를 사용하도록 설정하려는 로컬 네트워크에 실행 중인 응용 프로그램을 게시하는 단계를 안내합니다.

> [AZURE.NOTE]커넥터가 제대로 실행되고 있는지 확인하려면 사용자가 실제 응용 프로그램을 게시하기 전에 인터넷에서 액세스하도록 하기 위해 게시하는 첫 번째 응용 프로그램이 개인 네트워크 내에서 액세스할 수 있는 웹 사이트이어야 합니다.


## 마법사를 사용하여 앱 게시

1. 선택한 브라우저를 열고 Azure 클래식 포털로 이동합니다.
2. Azure 클래식 포털의 왼쪽 창에서 Active Directory 탭을 클릭합니다.
3. 응용 프로그램 프록시를 사용하도록 설정하고 응용 프로그램(예: Wingtip Toys)을 게시할 디렉터리를 클릭합니다.
4. **응용 프로그램** 탭을 클릭한 다음 화면 맨 위에 있는 **추가** 단추를 클릭합니다. ![응용 프로그램 추가](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)
5. 원하는 작업을 선택하세요 대화 상자에서 **Publish an application that will be accessible outside your network(네트워크 외부에서 액세스할 수 있는 응용 프로그램 게시)**를 클릭합니다. ![네트워크 외부에서 액세스할 수 있는 새 응용 프로그램](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

6. 화면의 지침에 따라 응용 프로그램에 대한 다음 정보를 제공하세요. ![응용 프로그램 속성](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)

**설정** | **세부 정보**
---|---
외부 URL | 개인 네트워크 외부에서 응용 프로그램에 액세스하는 데 사용되는 클라우드 서비스의 URL입니다. URL은 지정한 이름을 기반으로 하여 자동으로 생성되며, msappproxy.net 접미사가 추가됩니다.
사전 인증 방법 | <p>응용 프로그램에서 사용할 인증 방법에 대한 유형을 설정합니다.</p><p>a. Microsoft Azure Active Directory(Microsoft Azure AD) – 사용자가 응용 프로그램에 액세스하려고 하면 응용 프로그램 프록시는 사용자를 인증할 Azure AD로 로그인하도록 리디렉션하여 사용자에게 디렉터리 및 응용 프로그램에 대해 필요한 권한이 있는지 확인합니다.</p> <p>b. 통과 – 사전 인증이 수행되지 않습니다.</p>
외부 URL 프로토콜 | <p>기본적으로 응용 프로그램은 HTTPS 프로토콜을 사용하여 게시됩니다. 서비스는 http를 사용하여 URL을 입력하는 사용자를 자동으로 리디렉션합니다.</p> <p>내부 응용 프로그램에 HTTP를 사용하도록 설정하려면 사전 인증 메서드를 통과로 설정해야만 그 다음에 외부 URL 프로토콜을 HTTPS에서 HTTP로 변경할 수 있습니다. HTTP를 사용하여 응용 프로그램을 게시하면 응용 프로그램 및 사용자에 대한 보안 문제가 발생할 수 있습니다.</p> <p>기본 접미사 msappproxy.net을 사용하지 않고 사용자 지정 도메인을 삽입할 수 있습니다. 자세한 내용은 [사용자 지정 도메인으로 작업](active-directory-application-proxy-custom-domains.md)을 참조하세요.</p>
내부 URL | <p>응용 프로그램 프록시 커넥터가 응용 프로그램에 내부적으로 액세스하는 데 사용하는 내부 URL입니다. 개인 네트워크 내에서 응용 프로그램에 액세스하는 데 사용되는 게시된 응용 프로그램의 URL이어야 합니다. 공백 또는 기호가 없는 유효한 URL입니다.</p> <p>나머지 서버는 게시되지 않은 반면 게시할 백 앤드 서버에 특정 경로를 지정할 수 있습니다. 예를 들어 다른 이름 및 액세스 규칙을 사용하여 동일한 SharePoint 서버에 있는 다른 사이트를 게시할 수 있습니다.</p> <p>경로는 내부 URL 필드에 지정되고 외부 URL에 표시됩니다. 내부 경로와 외부 경로는 동일해야 합니다.</p>

마법사를 마치려면 화면 아래쪽에 있는 확인 표시를 클릭합니다. 이제 응용 프로그램이 Azure AD에 정의되었습니다.


## 응용 프로그램에 사용자 및 그룹 할당

1. 사전 인증된 앱에 대해 앱에 액세스할 수 있는 사용자 및 그룹을 할당해야 합니다. <p>통과하는 앱의 경우 모든 사용자가 액세스할 수 있습니다. 그러나 사용자가 해당 응용 프로그램 목록에 있는 앱을 볼 수 있도록 하려면 앱을 해당 사용자에게 할당해야 합니다.
2. 앱 추가 마법사를 마치면 App Proxy Quick Start(앱 프록시 빠른 시작) 페이지가 표시됩니다. 사용자를 할당하려면 **사용자 할당**을 클릭합니다. ![응용 프로그램 프록시 빠른 시작 화면](./media/active-directory-application-proxy-publish/quickstart.png)
3. 이 앱에 할당하려는 각 사용자나 그룹을 선택하고 **할당**을 클릭합니다.

> [AZURE.NOTE]Windows 통합 인증 앱에 대해 온-프레미스 Active Directory에서 동기화되는 사용자와 그룹만 할당할 수 있습니다. Microsoft 계정 및 게스트를 사용하여 로그인하는 사용자는 Azure Active Directory 응용 프로그램 프록시를 사용하여 게시된 앱에 할당할 수 없습니다. 할당한 사용자가 게시하는 앱과 동일한 도메인에 속하는 자신의 자격 증명을 사용하여 로그인하는지 확인합니다.


## 고급 구성

1. 구성 페이지에서 게시된 앱을 수정하거나 온-프레미스 응용 프로그램에 대한 SSO 같은 고급 옵션을 구성할 수 있습니다. ![고급 구성](./media/active-directory-application-proxy-publish/advancedconfig.png)

2. 앱을 선택하고 **구성**을 클릭합니다. 사용할 수 있는 옵션은 다음과 같습니다.

**설정** | **세부 정보**
---|---
이름 | 응용 프로그램에 대한 설명이 포함된 이름을 제공합니다.
외부 URL | 개인 네트워크 외부에서 응용 프로그램에 액세스하는 데 사용되는 클라우드 서비스의 URL입니다. URL은 지정한 이름을 기반으로 하여 자동으로 생성되며, msappproxy.net 접미사가 추가됩니다.
사전 인증 방법 | <p>응용 프로그램에서 사용할 사전 인증 방법의 형식을 설정합니다.</p><p>a. Microsoft Azure Active Directory(Microsoft Azure AD) – 사용자가 응용 프로그램에 액세스하려고 하면 응용 프로그램 프록시는 사용자를 인증할 Azure AD로 로그인하도록 리디렉션하여 사용자에게 디렉터리 및 응용 프로그램에 대해 필요한 권한이 있는지 확인합니다.</p> <p>b. 통과 – 사전 인증이 수행되지 않습니다.</p>
외부 URL 프로토콜 | <p>기본적으로 응용 프로그램은 HTTPS 프로토콜을 사용하여 게시됩니다. 서비스는 http를 사용하여 URL을 입력하는 사용자를 자동으로 리디렉션합니다.</p> <p>내부 응용 프로그램에 HTTP를 사용하도록 설정하려면 사전 인증 메서드를 통과로 설정해야만 그 다음에 외부 URL 프로토콜을 HTTPS에서 HTTP로 변경할 수 있습니다. HTTP를 사용하여 응용 프로그램을 게시하면 응용 프로그램 및 사용자에 대한 보안 문제가 발생할 수 있습니다.</p> <p>기본 접미사 msappproxy.net을 사용하지 않고 사용자 지정 도메인을 삽입할 수 있습니다. 자세한 내용은 [사용자 지정 도메인으로 작업](active-directory-application-proxy-custom-domains.md)을 참조하세요.</p>
내부 URL | <p>응용 프로그램 프록시 커넥터가 응용 프로그램에 내부적으로 액세스하는 데 사용하는 내부 URL입니다. 개인 네트워크 내에서 응용 프로그램에 액세스하는 데 사용되는 게시된 응용 프로그램의 URL이어야 합니다. 공백 또는 기호가 없는 유효한 URL입니다.</p> <p>나머지 서버는 게시되지 않은 반면 게시할 백 앤드 서버에 특정 경로를 지정할 수 있습니다. 예를 들어 다른 이름 및 액세스 규칙을 사용하여 동일한 SharePoint 서버에 있는 다른 사이트를 게시할 수 있습니다.</p> <p>경로는 내부 URL 필드에 지정되고 외부 URL에 표시됩니다. 내부 경로와 외부 경로는 동일해야 합니다.</p>
헤더에서 URL 변환 | HTTP 호스트 헤더를 변환하지 않아도 되는 응용 프로그램(예: 일부 SharePoint 구성)의 경우 **아니요**로 설정합니다. 이 경우 요청과 응답 헤더에 대한 헤더 변환을 사용할 수 없게 됩니다.
내부 인증 방법 | <p>사전 인증에 응용 프로그램 프록시를 사용하는 경우 내부 인증 방법을 설정하여 이 응용 프로그램에 SSO(Single-Sign On)의 장점을 활용하도록 할 수 있습니다.</p> <p> 응용 프로그램이 IWA를 사용하고 KCD(Kerberos 제한 위임)를 구성하여 이 응용 프로그램에 SSO를 사용하도록 한 경우 **IWA(Windows 통합 인증)**를 선택합니다. IWA를 사용하는 응용 프로그램은 KCD를 사용하여 구성해야 합니다. 그렇지 않으면 응용 프로그램 프록시가 이러한 응용 프로그램을 게시할 수 없습니다.</p> <p>응용 프로그램이 IWA를 사용하지 않는 경우 **없음**을 선택합니다.</p> <p>자세한 내용은 [앱 프록시를 사용하는 Single Sign-On](active-directory-application-proxy-sso-using-kcd.md)을 참조하세요.</p>
내부 응용 프로그램 SPN | <p>온-프레미스 응용 프로그램 프록시에 구성된 대로 구성된 내부 응용 프로그램의 SPN(서비스 보안 주체 이름)입니다. SPN은 응용 프로그램 프록시 커넥터가 KCD(Kerberos 제한 위임)를 사용하여 응용 프로그램에 대한 Kerberos 토큰을 가져오는 데 사용합니다.</p> <p>자세한 내용은 [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)을 참조하세요.</p>

Azure Active Directory 응용 프로그램 프록시를 사용하여 응용 프로그램을 게시하면 Azure AD의 응용 프로그램 목록에 표시되므로 해당 위치에서 관리할 수 있습니다.

응용 프로그램을 게시한 후 응용 프로그램 프록시 서비스를 사용하지 않도록 설정하면 응용 프로그램이 삭제되지 않지만, 개인 네트워크 외부에서 액세스할 수 없습니다.

응용 프로그램을 확인하고 액세스할 수 있는지 확인하려면 응용 프로그램의 이름을 두 번 클릭합니다. 응용 프로그램 프록시 서비스를 사용할 수 없고 응용 프로그램을 사용할 수 없는 경우 화면 위쪽에 경고 메시지가 나타납니다.

응용 프로그램을 삭제하려면 목록에서 응용 프로그램을 선택한 다음 **삭제**를 클릭합니다.

## 참고 항목
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [응용 프로그램 프록시 사용](active-directory-application-proxy-enable.md)
- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [응용 프로그램 프록시에서 발생한 문제 해결](active-directory-application-proxy-troubleshoot.md)

## 응용 프로그램 프록시에 대해 자세히 알아보기
- [온라인 도움말에서 살펴보기](active-directory-application-proxy-enable.md)
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스

* [Kerberos 제한 위임 정보](http://technet.microsoft.com/library/cc995228.aspx)

<!---HONumber=AcomDC_1210_2015-->