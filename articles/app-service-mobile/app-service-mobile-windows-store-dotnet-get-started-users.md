<properties
	pageTitle="Windows Runtime 8.1 범용 앱에 인증 추가 | Azure 모바일 앱"
	description="Azure 앱 서비스 모바일 앱을 사용하여 AAD, Google, Facebook, Twitter, Microsoft 등의 다양한 ID 공급자를 사용해서 Windows 앱 사용자를 인증하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Windows 앱에 인증 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목에서는 모바일 앱에 클라우드 기반 인증을 추가하는 방법을 보여줍니다. 이 자습서에서는 Azure 앱 서비스가 지원하는 ID 공급자를 사용하여 모바일 앱 퀵 스타트 프로젝트에 인증을 추가합니다. 모바일 앱 백 엔드에서 인증되고 권한이 부여된 후 사용자 ID 값이 표시됩니다.

이 자습서는 모바일 앱 퀵 스타트를 기반으로 합니다. 먼저 [모바일 앱 시작](app-service-mobile-windows-store-dotnet-get-started.md) 자습서를 완료해야 합니다.

##<a name="register"></a>인증을 위해 앱 등록 및 앱 서비스 구성

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>사용 권한을 인증된 사용자로 제한

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

시작 프로젝트로 설정된 Windows 스토어 앱 프로젝트를 사용하여 F5 키를 눌러 앱을 실행합니다. 앱이 시작된 후 상태 코드 401(인증되지 않음)의 처리되지 않은 예외가 발생하는지 확인합니다. 이 예외는 앱이 인증되지 않은 사용자로 모바일 앱 코드에 액세스하려고 시도하는데 *TodoItem* 테이블에서 이제 인증을 요구하기 때문에 발생합니다.

다음에는 앱 서비스에서 리소스를 요청하기 전에 사용자를 인증하도록 앱을 업데이트합니다.

##<a name="add-authentication"></a>앱에 인증 추가

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>클라이언트에 인증 토큰 저장

이전 예제에서는 앱이 시작될 때마다 클라이언트가 ID 공급자와 앱 서비스 둘 다에 접근해야 하는 표준 로그인을 보여 주었습니다. 이 방법은 비효율적일 뿐 아니라 많은 고객이 동시에 앱을 시작하려고 할 경우 사용 관련 문제가 발생할 수도 있습니다. 더 나은 접근 방법은 앱 서비스에서 반환된 권한 부여 토큰을 캐시한 다음 공급자 기반 로그인을 사용하기 전에 이 토큰을 먼저 사용하는 것입니다.

>[AZURE.NOTE]클라이언트 관리 인증 또는 서비스 관리 인증을 사용하는지에 관계없이 앱 서비스에서 발급된 토큰을 캐시할 수 있습니다. 이 자습서에서는 서비스 관리 인증을 사용합니다.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##다음 단계

이 기본 인증 자습서를 완료했으므로 다음 자습서 중 하나를 계속하는 것을 고려해보세요.

+ [Windows 앱에 푸시 알림 추가](app-service-mobile-windows-store-dotnet-get-started-push.md) 앱에 푸시 알림 지원을 추가하고 푸시 알림을 보내도록 Azure 알림 허브를 사용하여 푸시 알림을 보내도록 모바일 앱 백엔드를 구성하는 방법을 알아봅니다.

+ [Windows 앱에 대한 오프라인 동기화 사용](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) 모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법에 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015--->