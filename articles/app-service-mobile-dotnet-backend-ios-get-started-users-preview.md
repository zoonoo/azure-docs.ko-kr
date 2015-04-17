<properties 
	pageTitle="iOS에서 모바일 앱에 대한 인증 시작" 
	description="모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 통해 iOS 앱 사용자를 인증하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="mattchenderson,krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# iOS 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

이 항목에서는 클라이언트 응용 프로그램에서 앱 서비스 모바일 앱의 사용자를 인증하는 방법을 보여 줍니다. 이 자습서에서는 앱 서비스가 지원하는 ID 공급자를 사용하여 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 또한 [iOS 앱 만들기] 자습서를 먼저 완료해야 합니다. 

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>Xcode에서 프로젝트를 엽니다. <b>실행</b> 단추를 눌러 앱을 시작합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 예외가 발생하는지 확인합니다.</p>
   
   	<p>이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 <em>TodoItem</em> 테이블에서 이제 인증을 요구하기 때문에 발생합니다.</p></li>
</ol>

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>앱에 인증 토큰 저장

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[iOS 앱 만들기]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure 관리 포털]: https://portal.azure.com

<!--HONumber=49-->