<properties 
	pageTitle="모바일 서비스에서 앱 서비스 모바일 앱으로 마이그레이션" 
	description="모바일 서비스 응용 프로그램을 앱 서비스 모바일 앱으로 쉽게 마이그레이션하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# 기존 Azure 모바일 서비스를 Azure 앱 서비스 모바일 앱으로 마이그레이션

이 항목에서는 기존 응용 프로그램을 Azure 모바일 서비스에서 새로운 앱 서비스 모바일 앱으로 마이그레이션하는 방법을 보여 줍니다. 기존의 모든 모바일 서비스 앱을 새로운 앱 서비스 모바일 앱으로 쉽게 마이그레이션할 수 있습니다. 마이그레이션 중에 기존 모바일 서비스 응용 프로그램이 계속 작동할 수 있습니다. 시간이 지나면 마이그레이션 프로세스가 훨씬 더 쉬워지겠지만 지금 마이그레이션하려는 경우 다음 단계를 사용할 수 있습니다.

>[AZURE.NOTE] 마이그레이션은 현재 모바일 서비스 .NET 백 엔드를 사용하는 고객에 대해서만 지원됩니다. Node.JS 백 엔드를 사용하는 응용 프로그램은 아직 모바일 서비스에 유지해야 합니다.

##<a name="understand"></a>앱 서비스 모바일 앱 이해

앱 서비스 모바일 앱은 Microsoft Azure를 사용하여 모바일 응용 프로그램을 빌드하는 새로운 방법입니다. 모바일 앱에 대한 자세한 내용은 [모바일 앱 정의?] 항목을 참조하세요.

모바일 앱으로 마이그레이션하는 경우 기존 앱 기능(및 코드)을 모두 보존할 수 있습니다. 또한 응용 프로그램에서 새로운 기능을 사용할 수 있습니다. 모바일 앱 모델에서 코드는 실제로 웹앱(새 버전의 Azure 웹 사이트)에서 실행됩니다. 웹앱과 웹앱 작동 방식을 완전히 제어할 수 있습니다. 뿐만 아니라 이전에 모바일 서비스 고객이 사용할 수 없었던 웹앱 기능(예: 트래픽 관리자 및 개발 슬롯)을 이제 사용할 수 있습니다. 

새 모델은 모바일 서비스 작업의 주요 문제 중 하나도 해결합니다. 이제 종속성 충돌을 염려하지 않고 모든 버전의 NuGet 패키지를 배포할 수 있습니다. 마이그레이션의 이점에 대한 자세한 내용은 [웹 사이트와 모바일 서비스를 이미 사용하고 있습니다. 앱 서비스가 내게 어떤 도움을 주나요?] 항목을 참조하세요.

앱 서비스 모바일 앱을 만드는 경우 다음을 사용할 수 있습니다.

- 새로운 기능을 포함하는 모바일 앱 리소스 
- 모바일 앱 서버 SDK를 사용하여 웹 API 프로젝트를 실행하는 모바일 앱 코드 사이트
- 로그인을 처리하고 앱 서비스 API 앱을 응용 프로그램에 추가하는 데 도움이 되는 앱 서비스 게이트웨이

##<a name="overview"></a>기본 마이그레이션 개요
가장 간단한 마이그레이션 방법은 앱 서비스 모바일 앱의 새 인스턴스를 만드는 것입니다. 대부분의 경우 새 서버 SDK로 전환한 다음 새 모바일 앱에 코드를 다시 게시하기만 하면 간단히 마이그레이션할 수 있습니다. 그러나 고급 인증 시나리오 및 예약된 작업 사용과 같이 추가 구성이 필요한 일부 시나리오도 있습니다. 각 시나리오는 다음 섹션에서 설명합니다.

>[AZURE.NOTE] 마이그레이션을 시작하기 전에 이 항목의 나머지 부분을 읽고 완전히 이해하는 것이 좋습니다. 아래 설명선에 표시된 사용하는 기능을 모두 기록해 두세요.

편리할 때 코드를 이동하여 테스트할 수 있습니다. 모바일 앱 백 엔드가 준비되면 새 버전의 클라이언트 응용 프로그램을 릴리스할 수 있습니다. 이제 두 개의 응용 프로그램 복사본이 나란히 실행됩니다. 버그 수정 내용이 둘 다에 적용되는지 확인해야 합니다. 끝으로, 사용자가 최신 버전으로 업데이트한 후 원본 모바일 서비스를 삭제할 수 있습니다.

이 마이그레이션의 전체 단계 집합은 다음과 같습니다.

1. 새 모바일 앱 만들기 및 구성
2. 인증 문제 해결
3. 새 버전의 클라이언트 응용 프로그램 릴리스
4. 원본 모바일 서비스 인스턴스 삭제


##<a name="mobile-app-version"></a>모바일 앱 버전의 응용 프로그램 설정
마이그레이션의 첫 번째 단계는 새 버전의 응용 프로그램을 호스트할 앱 서비스를 만드는 것입니다. [Preview Azure 관리 포털]에서 새 모바일 앱을 만들 수 있습니다. 자세한 내용은 [모바일 앱 만들기] 항목을 참조하세요.

모바일 서비스와 동일한 데이터베이스 및 알림 허브를 사용하려는 경우가 많습니다. [Azure 관리 포털] 모바일 서비스 섹션의 **구성** 탭에서 이러한 값을 복사할 수 있습니다. **연결 문자열**에서 `MS_NotificationHubConnectionString` 및 `MS_TableConnectionString`을 복사합니다. 모바일 앱 코드 사이트로 이동하고 **설정**, **응용 프로그램 설정**을 차례로 클릭한 다음 이 연결 문자열을 추가하여 기존 값을 덮어씁니다. 모바일 앱 리소스에도 이 값을 추가해야 합니다. 이렇게 하려면 모바일 앱 블레이드로 이동한 다음 **설정**, **속성**을 차례로 선택합니다. **API 앱 호스트** 레이블이 지정된 링크를 클릭하여 모바일 앱 리소스를 호스트하는 사이트를 표시합니다. **설정**, **응용 프로그램 설정**으로 이동한 다음 코드 사이트와 마찬가지로 연결 문자열을 붙여넣습니다. 모바일 앱 기능이 손상될 수 있으므로 다른 값은 변경하지 마세요. 지금은 이 구성 단계 후에도 기존 연결이 모바일 앱 브레이드에 계속 표시됩니다. 모바일 앱 환경이 업데이트된 후 추가 작업이 필요할 수 있습니다.

모바일 앱은 모바일 서비스 런타임과 동일한 기능을 대부분 제공하는 새로운 [모바일 앱 서버 SDK]를 제공합니다. 기존 프로젝트에서 모바일 서비스 NuGet을 제거하고 서버 SDK를 대신 포함해야 합니다. Visual Studio의 도움을 받아 일부 using 문을 수정해야 할 수도 있습니다. 서버 SDK에서 누락된 주요 항목은 PushRegistrationHandler 클래스입니다. 모바일 앱에서는 등록이 약간 다르게 처리되며, 기본적으로 태그 없는 등록이 사용됩니다. 사용자 지정 API를 통해 태그를 관리할 수 있습니다. 자세한 내용은 [모바일 앱에 푸시 알림 추가] 항목을 참조하세요.

예약된 작업은 모바일 앱에 빌드되지 않으므로 .NET 백 엔드에 있는 기존 작업을 모두 개별적으로 마이그레이션해야 합니다. 한 가지 옵션은 모바일 앱 코드 사이트에 예약된 [웹 작업]을 만드는 것입니다. 작업 코드를 포함하는 컨트롤러를 설정하고 예상 일정에 따라 해당 끝점에 도달하도록 [Azure 스케줄러]를 구성할 수도 있습니다.


##<a name="authentication"></a>인증 고려 사항
모바일 앱과 모바일 서비스 간의 가장 큰 차이점 중 하나는 모바일 앱의 경우 로그인이 코드 사이트가 아니라 앱 서비스 게이트웨이에서 처리된다는 것입니다. 대부분의 응용 프로그램에서는 이 기능에 추가 작업이 필요하지 않지만 유의해야 하는 몇 가지 고급 시나리오가 있습니다.

대부분의 앱은 간단히 대상 ID 공급자와 함께 새 등록을 사용하기만 해도 되지만 [모바일 앱에 인증 추가] 자습서에 따라 앱 서비스 앱에 ID를 추가하는 방법을 알아볼 수 있습니다.

데이터베이스에 사용자 ID를 저장하는 경우와 같이 응용 프로그램이 사용자 ID에 종속된 경우 모바일 서비스와 앱 서비스 모바일 앱 간에 사용자 ID가 서로 다르다는 것에 유의해야 합니다. 그러나 다음을 사용하여 앱 서비스 모바일 앱 응용 프로그램에서 모바일 서비스 사용자 ID를 가져올 수 있습니다(Facebook을 예제로 사용).

    ServiceUser user = (ServiceUser) this.User;
    FacebookCredentials creds = (await user.GetIdentitiesAsync()).OfType< FacebookCredentials >().FirstOrDefault();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

또한 사용자 ID에 종속된 경우 가능하면 ID 공급자와 함께 동일한 등록을 활용하는 것이 중요합니다. 일반적으로 사용자 ID의 범위는 사용된 응용 프로그램 등록으로 지정되므로 새 등록을 도입하면 사용자를 해당 데이터에 일치시킬 때 문제가 발생할 수 있습니다. 어떤 이유로든 응용 프로그램이 동일한 ID 공급자 등록을 사용해야 하는 경우 다음 단계를 따를 수 있습니다.

1. 응용 프로그램에서 사용하는 각 공급자에 대한 클라이언트 ID 및 클라이언트 보안 연결 정보를 복사합니다.
2. 게이트웨이의 /signin-* 끝점을 각 공급자에 대한 추가 리디렉션 URI로 추가합니다. 

>[AZURE.NOTE] Twitter 및 Microsoft 계정과 같은 일부 공급자는 서로 다른 도메인의 리디렉션 URI를 여러 개 지정할 수 있도록 허용하지 않습니다. 앱에서 이러한 공급자 중 하나를 사용하며 사용자 ID에 종속된 경우 지금은 마이그레이션을 시도하지 않는 것이 좋습니다.

##<a name="updating clients"></a>클라이언트 업데이트
작동하는 모바일 앱 백 엔드가 있으면 새 모바일 앱을 사용하도록 클라이언트 응용 프로그램을 업데이트할 수 있습니다. 모바일 앱에는 개발자가 새로운 앱 서비스 기능을 이용할 수 있게 하는 새 버전의 모바일 서비스 클라이언트 SDK도 포함됩니다. 모바일 앱 버전의 백 엔드가 있으면 새 SDK 버전을 활용하는 새 버전의 클라이언트 응용 프로그램을 릴리스할 수 있습니다.

클라이언트 코드에서 필요한 주요 변경 내용은 생성자에 있습니다. 모바일 앱 코드 사이트의 URL과 응용 프로그램 키뿐 아니라 인증 설정을 호스트하는 앱 서비스 게이트웨이의 URL도 제공해야 합니다.

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso-code.azurewebsites.net", //URL of the Mobile App Code
        "https://contosogateway.azurewebsites.net", //URL of the App Service Gateway
        "983682c4-f043-483e-a75b-8a8545fc1846"
    );

이렇게 하면 클라이언트가 모바일 앱의 구성 요소에 요청을 라우팅할 수 있습니다. 대상 플랫폼과 관련된 자세한 내용은 해당 [모바일 앱 만들기] 항목을 참조하세요.

동일한 업데이트에서 수행하는 모든 푸시 알림 등록 호출을 조정해야 합니다. 등록 프로세스를 향상시키는 새로운 API가 있습니다(Windows를 예제로 사용).

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

대상 플랫폼과 관련된 자세한 내용은 [모바일 앱에 푸시 알림 추가] 및 [크로스 플랫폼 푸시 알림 전송] 항목을 참조하세요.

고객이 이러한 업데이트를 받고 나면 모바일 서비스 버전의 앱을 삭제할 수 있습니다. 이제 앱 서비스 모바일 앱으로 완전히 마이그레이션했습니다.

<!-- URLs. -->

[Preview Azure 관리 포털]: https://portal.azure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 앱 정의?]: app-service-mobile-value-prop-preview.md
[웹 사이트와 모바일 서비스를 이미 사용하고 있습니다. 앱 서비스가 내게 어떤 도움을 주나요?]: /ko-kr/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services-preview
[모바일 앱 서버 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[모바일 앱 만들기]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md
[모바일 앱에 푸시 알림 추가]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[모바일 앱에 인증 추가]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Azure 스케줄러]: /ko-kr/documentation/services/scheduler/
[웹 작업]: websites-webjobs-resources.md
[크로스 플랫폼 푸시 알림 전송]: app-service-mobile-dotnet-backend-xamarin-ios-push-notifications-to-user-preview.md

<!--HONumber=49-->