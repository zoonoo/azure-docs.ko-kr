<properties
	pageTitle="프록시 응용 프로그램를 사용하여 네이티브 클라이언트 앱의 게시를 사용하 방법 | Microsoft Azure"
	description="Azure AD 응용 프로그램 프록시 커넥터와 통신하는 네이티브 클라이언트 앱을 사용하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다."
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
	ms.date="02/09/2016"
	ms.author="kgremban"/>

# 네이티브 클라이언트 앱을 사용하여 프록시 응용 프로그램과 상호 작용하는 방법

> [AZURE.NOTE] 응용 프로그램 프록시는 Premium 또는 Basic 버전의 Azure Active Directory로 업그레이드하는 경우에만 사용할 수 있는 기능입니다. 자세한 내용은 [Azure Active Directory 버전](active-directory-editions.md)을 참조하세요.

Azure Active Directory 응용 프로그램 프록시는 비즈니스 응용 프로그램의 SharePoint, Outlook Web Access 및 사용자 지정 선과 같은 브라우저 응용 프로그램을 게시하는 데 널리 사용됩니다. 또한 네이티브 클라이언트를 사용하여 사용되는 HTTP 백 엔드 응용 프로그램을 게시하는 데 사용될 수 있습니다. 표준 권한 부여 HTTP 헤더에서 전송된 Azure AD가 발급한 토큰을 지원하여 이루어집니다.

![최종 사용자, Azure Active Directory 및 게시된 응용 프로그램 간의 관계](./media/active-directory-application-proxy-native-client/richclientflow.png)

이러한 응용 프로그램을 게시하는 방법은 모든 인증을 처리하고 다양한 클라이언트 환경을 지원하는 Azure AD 인증 라이브러리를 사용하는 것이 좋습니다. 응용 프로그램 프록시는 [Web API 시나리오에 대한 네이티브 응용 프로그램](active-directory-authentication-scenarios.md#native-application-to-web-api)에 맞습니다. 이 작업을 수행하는 프로세스는 다음과 같습니다.

## 1단계: 응용 프로그램 게시

다른 응용 프로그램과 마찬가지로 프록시 응용 프로그램을 게시하고 사용자를 할당하며 프리미엄 또는 기본 라이선스를 제공합니다. 자세한 내용은 [응용 프로그램 프록시를 사용하여 응용 프로그램 게시](active-directory-application-proxy-publish.md)를 참조하세요.

## 2단계: 응용 프로그램 구성

네이티브 응용 프로그램을 다음과 같이 구성합니다.

1. Azure 클래식 포털에 로그인합니다.
2. 왼쪽 메뉴에 있는 Active Directory 아이콘을 클릭하고 원하는 디렉터리를 클릭합니다.
3. 최상위 메뉴에서 **응용 프로그램**을 클릭합니다. 디렉터리에 추가한 앱이 없는 경우 이 페이지는 **앱 추가** 링크만 표시합니다. 이 링크를 클릭하거나 명령 모음에서 **추가** 단추를 클릭할 수 있습니다.
4. **무엇을 하고 싶나요** 페이지에서 **조직에서 개발 중인 응용 프로그램 추가** 링크를 클릭합니다.
5. **응용 프로그램에 대해 알려주세요** 페이지에서 응용 프로그램에 대한 이름을 지정하고 전화 또는 컴퓨터와 같은 장치에 설치된 응용 프로그램을 나타내는 **네이티브 클라이언트 응용 프로그램**을 선택합니다. 완료하면 페이지 오른쪽 아래 모서리에 있는 화살표 아이콘을 클릭합니다.
6. **앱 속성** 페이지에서 네이티브 클라이언트 응용 프로그램에 대한 **리디렉션 URI**를 제공하고 페이지의 맨 아래 오른쪽 모서리에 있는 확인란을 클릭합니다.

그러면 응용 프로그램이 추가되고 응용 프로그램의 빠른 시작 페이지로 이동합니다.

## 3단계: 다른 응용 프로그램에 액세스 허용

디렉터리에서 다른 응용 프로그램에 노출될 네이티브 응용 프로그램을 사용하도록 설정합니다.

1. 최상위 메뉴에서 **응용 프로그램**을 클릭하고 새 네이티브 응용 프로그램을 선택한 다음 **구성**을 클릭합니다.
2. 아래에 있는 **다른 응용 프로그램에 대한 권한** 섹션으로 스크롤합니다. **응용 프로그램을 추가** 단추를 클릭하고 네이티브 응용 프로그램이 액세스 권한을 부여하려는 프록시 응용 프로그램을 선택한 다음 오른쪽 아래 모서리에 있는 확인 표시를 클릭합니다. **위임된 권한** 드롭다운 메뉴에서 새 사용 권한을 선택합니다.

![다른 응용 프로그램 스크린샷에 대한 권한 - 응용 프로그램 추가](./media/active-directory-application-proxy-native-client/delegate_native_app.png)

## 4단계: Active Directory 인증 라이브러리 편집

Active Directory 인증 라이브러리(ADAL)의 인증 컨텍스트에서 네이티브 응용 프로그램 코드를 편집하여 다음을 포함합니다.

		// Acquire Access Token from AAD for Proxy Application
		AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/<TenantId>");
		AuthenticationResult result = authContext.AcquireToken("< Frontend Url of Proxy App >",
                                                        "< Client Id of the Native app>",
                                                        new Uri("< Redirect Uri of the Native App>"),
                                                        PromptBehavior.Never);

		//Use the Access Token to access the Proxy Application
		HttpClient httpClient = new HttpClient();
		httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
		HttpResponseMessage response = await httpClient.GetAsync("< Proxy App API Url >");

변수는 다음과 같이 대체되어야 합니다.

- **TenantId**는 응용 프로그램의 “/Directory/”의 바로 뒤 **구성**페이지의 URL에 있는 GUID에서 찾을 수 있습니다.
- **프런트 엔드 URL**은 프록시 응용 프로그램에 입력한 프런트 엔드 URL이고 프록시 앱의 **구성** 페이지에서 찾을 수 있습니다.
- 네이티브 앱의 **클라이언트 ID**는 네이티브 응용 프로그램의 **구성** 페이지에서 찾을 수 있습니다.
- **네이티브 앱의 리디렉션 URI**는 네이티브 응용 프로그램의 **구성** 페이지에서 찾을 수 있습니다.

![새 네이티브 응용 프로그램 구성 페이지 스크린샷](./media/active-directory-application-proxy-native-client/new_native_app.png)

네이티브 응용 프로그램 흐름에 대한 자세한 내용은 [Web API에 대한 네이티브 응용 프로그램](active-directory-authentication-scenarios.md#native-application-to-web-api)을 참조하세요.


## 다음 작업
응용 프로그램 프록시를 사용하여 수행할 수 있는 작업은 많습니다.

- [고유한 도메인 이름을 사용하여 응용 프로그램 게시](active-directory-application-proxy-custom-domains.md)
- [Single Sign-On 사용](active-directory-application-proxy-sso-using-kcd.md)
- [클레임 인식 응용 프로그램으로 작업](active-directory-application-proxy-claims-aware-apps.md)
- [조건부 액세스 사용](active-directory-application-proxy-conditional-access.md)


### 응용 프로그램 프록시에 대해 자세히 알아보기
- [온라인 도움말에서 살펴보기](active-directory-application-proxy-enable.md)
- [응용 프로그램 프록시 블로그 확인](http://blogs.technet.com/b/applicationproxyblog/)
- [Channel 9에서 비디오 시청](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 추가 리소스
- [Azure Active Directory의 응용 프로그램 관리를 위한 문서 인덱스](active-directory-apps-index.md)
- [조직으로 Azure에 등록](sign-up-organization.md)
- [Azure ID](fundamentals-identity.md)

<!---HONumber=AcomDC_0211_2016-->