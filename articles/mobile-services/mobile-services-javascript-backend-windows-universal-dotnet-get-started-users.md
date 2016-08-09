<properties 
	pageTitle="유니버설 Windows 8.1 앱에 인증 추가 | Azure 모바일 서비스"
	description="모바일 서비스를 사용하여 Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 Windows 스토어 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2016" 
	ms.author="glenga"/>

# 유니버설 Windows 8.1 앱에 인증 추가

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> 이 항목에 해당하는 모바일 앱 버전은 [Windows 앱에 인증 추가](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-users.md)를 참조하세요.

이 항목에서는 유니버설 Windows 8.1 앱에서 Azure 모바일 서비스의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 모바일 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 서비스에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 먼저 [모바일 서비스 시작] 자습서를 완료해야 합니다.

>[AZURE.NOTE]이 자습서에서는 Windows 스토어 및 Windows Phone 스토어 8.1 앱에서 사용자를 인증하는 방법을 보여 줍니다. Windows Phone 8.0 또는 Windows Phone Silverlight 8.1 앱의 경우 [모바일 서비스에서 인증 시작](mobile-services-windows-phone-get-started-users.md) 버전을 참조하세요.

##<a name="register"></a> 인증을 위해 앱 등록 및 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a> 사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]
 
>[AZURE.NOTE] Visual Studio Tools를 사용하여 앱을 모바일 서비스에 연결하면 각 클라이언트 플랫폼에 대해 하나씩 두 개의 **MobileServiceClient** 정의 집합을 생성합니다. 이 시점에서 `#if...#endif` 래핑 **MobileServiceClient** 정의를 앱의 두 버전 모두에서 사용하는 래핑 해제된 단일 정의로 통합하여 생성되는 코드를 간소화할 수 있습니다. [Azure 클래식 포털]에서 퀵 스타트 앱을 다운로드할 때는 이 작업을 수행할 필요가 없습니다.

##<a name="add-authentication"></a> 앱에 인증 추가

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]

이제, 신뢰할 수 있는 ID 공급자가 인증된 모든 사용자가 *TodoItem* 테이블에 액세스할 수 있습니다. 사용자 특정 데이터의 보안을 강화하려면 권한 부여도 구현해야 합니다. 이를 위해 지정된 사용자의 사용자 ID를 가져옵니다. 이는 사용자가 지정된 리소스에 대해 갖고 있는 액세스 단계를 결정하는 데 사용할 수 있습니다.

##<a name="tokens"></a>클라이언트에 권한 부여 토큰 저장

이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 모바일 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 모바일 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

>[AZURE.NOTE]클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 모바일 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

## <a name="next-steps"> </a>다음 단계

다음 자습서인 [모바일 서비스 사용자의 서비스 쪽 권한 부여](mobile-services-javascript-backend-service-side-authorization.md)에서는 인증된 사용자를 기반으로 모바일 서비스에서 제공된 사용자 ID 값을 사용하여 모바일 서비스에서 반환되는 데이터를 필터링합니다.

##참고 항목

+ [향상된 사용자 기능](http://go.microsoft.com/fwlink/p/?LinkId=506605)<br/> 서버 스크립트에서 **user.getIdentities()** 함수를 호출하여 모바일 서비스의 ID 공급자로 유지 관리되는 추가 사용자 데이터를 가져올 수 있습니다.

+ [모바일 서비스 .NET 방법 개념 참조] <br/>.NET 클라이언트에서 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[모바일 서비스 시작]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md
[Get started with authentication]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-users.md
[Get started with push notifications]: ../mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md
[Authorize users with scripts]: ../mobile-services-windows-store-dotnet-authorize-users-in-scripts.md

[Azure 클래식 포털]: https://manage.windowsazure.com/
[모바일 서비스 .NET 방법 개념 참조]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md
 

<!---HONumber=AcomDC_0727_2016-->