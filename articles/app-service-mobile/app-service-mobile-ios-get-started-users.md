<properties
	pageTitle="Azure 모바일 앱을 사용하여 iOS에서 인증 추가"
	description="Azure 모바일 앱을 사용하여 AAD, Google, Facebook, Twitter 및 Microsoft를 포함한 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/27/2015"
	ms.author="krisragh"/>

# Azure 모바일 앱을 사용하여 iOS 인증

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 자습서에서는 지원되는 ID 공급자를 사용하여 [iOS 빠른 시작]에 인증을 추가합니다. 이 자습서는 먼저 완료해야 하는 [iOS 빠른 시작 안내서]를 기반으로 합니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 인증 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##<a name="create-gateway"></a>앱 서비스 게이트웨이 만들기

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway](../../includes/app-service-mobile-dotnet-backend-create-gateway.md)]

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Xcode에서 **실행**을 눌러 앱을 시작합니다. 앱이 인증되지 않은 사용자로 백 엔드에 액세스하려고 시도하지만 _TodoItem_ 테이블에서 이제 인증을 요구하기 때문에 예외가 발생합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS 빠른 시작]: app-service-mobile-ios-get-started.md
[iOS 빠른 시작 안내서]: app-service-mobile-ios-get-started.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Nov15_HO1-->