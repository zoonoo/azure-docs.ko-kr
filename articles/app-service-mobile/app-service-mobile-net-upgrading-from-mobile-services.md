<properties 
	pageTitle="모바일 서비스에서 Azure 앱 서비스로 업그레이드" 
	description="모바일 서비스 응용 프로그램을 앱 서비스 모바일 앱으로 쉽게 업그레이드하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="mahender"/>

# 기존 .NET Azure 모바일 서비스를 앱 서비스로 업그레이드

앱 서비스 모바일은 Microsoft Azure를 사용하여 모바일 응용 프로그램을 빌드하는 새로운 방법입니다. 자세한 내용은 [모바일 앱 정의]를 참조하세요.

이 항목에서는 기존 .NET 백 엔드 응용 프로그램을 Azure 모바일 서비스에서 새로운 앱 서비스 모바일 앱으로 업그레이드하는 방법을 설명합니다. 이 업그레이드를 수행할 때 기존 모바일 서비스 응용 프로그램이 계속 작동할 수 있습니다.

모바일 백 엔드가 Azure 앱 서비스로 업그레이드되면 모든 앱 서비스 기능에 액세스할 수 있고 모바일 서비스 가격 책정이 아닌 [앱 서비스 가격 책정]에 따라 요금이 청구됩니다.

##마이그레이션과 업그레이드 비교

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

###모바일 앱 .NET 서버 SDK에서 향상된 기능

새 [모바일 앱 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)의 업그레이드는 다음과 같은 이점을 제공합니다.

- NuGet 종속성에서 유연성이 증가합니다. 호스팅 환경이 고유한 버전의 NuGet 패키지를 더 이상 제공하지 않으므로 호환되는 대체 버전을 사용할 수 있습니다. 그러나 모바일 서버 SDK 또는 종속성에 대한 새 주요 오류수정 또는 보안 업데이트가 있는 경우 서비스를 수동으로 업데이트해야 합니다.

- 새로운 앱 서비스 인증 기능을 지원하며 이는 웹 및 모바일 앱에서 일반 인증 구성을 사용할 수 있게 합니다. 또한 인증 기능은 고유한 OWIN 미들웨어 구성 요소에 위치하며 이는 동일한 인증 설치 프로그램이 모바일 또는 웹 경로에 사용될 수 있다는 것을 의미합니다.

- 다른 ASP.NET 프로젝트 형식 및 경로를 지원합니다. 이제 모바일 백 엔드 프로젝트와 동일한 프로젝트에서 MVC 및 Web API 컨트롤러를 호스팅할 수 있습니다.

- 모바일 SDK에서 유연성이 증가합니다. 인증, 테이블 API 및 푸시 등록 끝점 등 어떤 기능 및 경로를 설정할 것인지 명시적으로 제어할 수 있습니다. 자세한 내용은 [Azure 모바일 앱에 .NET 서버 SDK를 사용하는 방법](app-service-mobile-net-upgrading-from-mobile-services.md#server-project-setup)을 참조하세요.

- DI 프레임워크에 대한 옵션이 증가합니다. 모바일 서비스 .NET 서버 SDK는 autofac에 종속되었지만 모바일 앱 서버 SDK를 사용하면 [Unity](https://unity.codeplex.com/)와 같은 다른 프레임워크를 사용할 수 있습니다.

>[AZURE.NOTE]모바일 서비스 클라이언트 SDK는 새 모바일 앱 서버 SDK와 호환할 수 **없습니다**. 기존 모바일 서비스에 연결하는 모바일 클라이언트 앱이 있는 경우 모든 모바일 클라이언트가 모바일 앱 **클라이언트 SDK**에 업그레이드될 때까지 서비스를 실행하도록 유지해야 합니다.
> 
> 기존 모바일 서비스는 기존 클라이언트 앱을 변경하지 않고 앱 서비스에 자동으로 *마이그레이션*될 수 있습니다. 마이그레이션에 대한 자세한 내용은 [기존 모바일 서비스를 앱 서비스로 마이그레이션]을 참조하세요.


##<a name="overview"></a>기본 업그레이드 개요
가장 간단한 업그레이드 방법은 앱 서비스 모바일 앱의 새 인스턴스를 만드는 것입니다. 대부분의 경우 새 모바일 앱 서버 SDK로 전환한 다음 새 모바일 앱에 코드를 다시 게시하기만 하면 간단히 업그레이드할 수 있습니다. 그러나 고급 인증 시나리오 및 예약된 작업 사용과 같이 추가 구성이 필요한 일부 시나리오도 있습니다. 각 시나리오는 다음 섹션에서 설명합니다.

>[AZURE.NOTE]업그레이드를 시작하기 전에 이 항목의 나머지 부분을 읽고 완전히 이해하는 것이 좋습니다. 아래 설명선에 표시된 사용하는 기능을 모두 기록해 두세요.

편리할 때 코드를 이동하여 테스트할 수 있습니다. 모바일 앱 백 엔드가 준비되면 새 버전의 클라이언트 응용 프로그램을 릴리스할 수 있습니다. 이제 두 개의 응용 프로그램 백엔드 복사본이 나란히 실행됩니다. 버그 수정 내용이 둘 다에 적용되는지 확인해야 합니다. 끝으로, 사용자가 최신 버전으로 업데이트한 후 원본 모바일 서비스를 삭제할 수 있습니다.

이 업그레이드의 전체 단계 집합은 다음과 같습니다.

1. 새 모바일 앱 만들기 및 구성
2. 인증 문제 해결
3. 새 버전의 클라이언트 응용 프로그램 릴리스
4. 원본 모바일 서비스 인스턴스 삭제


##<a name="mobile-app-version"></a>모바일 앱 버전의 응용 프로그램 설정
업그레이드의 첫 번째 단계는 새 버전의 응용 프로그램을 호스트할 모바일 앱 리소스를 만드는 것입니다. [Preview Azure 관리 포털]에서 새 모바일 앱을 만들 수 있습니다. 자세한 내용은 [모바일 앱 만들기] 항목을 참조하세요.

모바일 서비스와 동일한 데이터베이스 및 알림 허브를 사용하려는 경우가 많습니다. **Azure 관리 포털** 모바일 서비스 섹션의 [구성] 탭에서 이러한 값을 복사할 수 있습니다. **연결 문자열**에서 `MS_NotificationHubConnectionString` 및 `MS_TableConnectionString`을 복사합니다. 모바일 앱 사이트로 이동하고 **설정**, **응용 프로그램 설정**을 차례로 선택한 다음 **연결 문자열** 섹션에 추가하여 기존 값을 덮어씁니다.

모바일 앱은 모바일 서비스 런타임과 동일한 기능을 대부분 제공하는 새로운 [모바일 앱 서버 SDK]를 제공합니다. 먼저, 기존 프로젝트에서 모바일 서비스 NuGet을 제거하고 서버 SDK를 대신 포함해야 합니다. 이 업그레이드를 위해 대부분의 개발자는 `Microsoft.Azure.Mobile.Server.Quickstart` 패키지를 다운로드하고 설치할 수 있으며, 전체 필수 집합으로 가져오게 됩니다. 그런 다음 WebApiConfig.cs에서

    // Use this class to set configuration options for your mobile service
    ConfigOptions options = new ConfigOptions();
    
    // Use this class to set WebAPI configuration options
    HttpConfiguration config = ServiceConfig.Initialize(new ConfigBuilder(options));

다음으로 바꿀 수 있습니다.

    HttpConfiguration config = new HttpConfiguration();

    new MobileAppConfiguration()
	    .UseDefaultConfiguration()
	    .ApplyTo(config);


>[AZURE.NOTE]앱에서 기능을 추가/제거하는 방법 및 새 .NET 서버 SDK에 대한 자세한 내용을 보려면, [.NET 서버 SDK를 사용하는 방법] 항목을 참조하세요.

모바일 서비스 및 모바일 앱 SDK 사이의 다른 몇가지 변경 사항이 있지만 처리하기 쉽습니다. 사용자 프로젝트에서 일부 using 문을 수정해야 할 수 있으며, Visual Studio가 지원합니다.

해당 decorator를 클래스 정의 바로 앞에 배치하여 `[MobileAppController]` 특성을 ApiControllers 모두에 추가해야 합니다.

`[AuthorizeLevel]` 특성은 더 이상 없으며 대신 표준 ASP.NET `[Authorize]` 특성과 함께 컨트롤러 및 메소드를 장식합니다. `[AuthorizeLevel]`이 없는 컨트롤러는 더 이상 응용 프로그램 키로 보호되지 않으며 공용으로 처리됩니다. 새 SDK는 응용 프로그램 키 및 마스터 키를 더 이상 사용하지 않습니다.

푸시를 위해 서버 SDK에서 누락된 주요 항목은 PushRegistrationHandler 클래스입니다. 모바일 앱에서는 등록이 약간 다르게 처리되며, 기본적으로 태그 없는 등록이 사용됩니다. 사용자 지정 API를 통해 태그를 관리할 수 있습니다. 자세한 내용은 [모바일 앱에 푸시 알림 추가] 항목을 참조하세요.

예약된 작업은 모바일 앱에 빌드되지 않으므로 .NET 백 엔드에 있는 기존 작업을 모두 개별적으로 업그레이드해야 합니다. 한 가지 옵션은 모바일 앱 코드 사이트에 예약된 [웹 작업]을 만드는 것입니다. 작업 코드를 포함하는 컨트롤러를 설정하고 예상 일정에 따라 해당 끝점에 도달하도록 [Azure 스케줄러]를 구성할 수도 있습니다.

`ApiServices` 개체는 더이상 SDK의 일부가 아닙니다. 모바일 앱 설정에 액세스하려면 다음을 사용할 수 있습니다.

    MobileAppSettingsDictionary settings = this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings(); 

마찬가지로, 로깅은 이제 표준 ASP.NET 추적 쓰기를 사용하여 수행됩니다.

    ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
    traceWriter.Info("Hello, World");  

##<a name="authentication"></a>인증 고려 사항
모바일 앱과 모바일 서비스 간의 가장 큰 차이점 중 하나는 모바일 앱의 경우 로그인이 코드를 실행하는 사이트가 아닌 앱 서비스 게이트웨이에서 처리된다는 것입니다. 리소스 그룹에 없는 경우, 관리 포털에서 Azure 모바일 앱으로 이동하여 게이트웨이를 프로비전할 수 있습니다. 그런 다음 **설정**, **사용자 인증**을 **모바일** 범주에서 선택합니다. **만들기**를 클릭하여 모바일 앱과 함께 게이트웨이를 연결합니다.

이 외에 대부분의 응용 프로그램에서는 추가 작업이 필요하지 않지만 유의해야 하는 몇 가지 고급 시나리오가 있습니다.

대부분의 앱은 간단히 대상 ID 공급자와 함께 새 등록을 사용하기만 해도 되지만 [모바일 앱에 인증 추가] 자습서에 따라 앱 서비스 앱에 ID를 추가하는 방법을 알아볼 수 있습니다.

데이터베이스에 사용자 ID를 저장하는 경우와 같이 응용 프로그램이 사용자 ID에 종속된 경우 모바일 서비스와 앱 서비스 모바일 앱 간에 사용자 ID가 서로 다르다는 것에 유의해야 합니다. 그러나 다음을 사용하여 앱 서비스 모바일 앱 응용 프로그램에서 모바일 서비스 사용자 ID를 가져올 수 있습니다(Facebook을 예제로 사용).

    MobileAppUser = (MobileAppUser) this.User;
    FacebookCredentials creds = await user.GetIdentityAsync<FacebookCredentials>();
    string mobileServicesUserId = creds.Provider + ":" + creds.UserId;

또한 사용자 ID에 종속된 경우 가능하면 ID 공급자와 함께 동일한 등록을 활용하는 것이 중요합니다. 일반적으로 사용자 ID의 범위는 사용된 응용 프로그램 등록으로 지정되므로 새 등록을 도입하면 사용자를 해당 데이터에 일치시킬 때 문제가 발생할 수 있습니다. 어떤 이유로든 응용 프로그램이 동일한 ID 공급자 등록을 사용해야 하는 경우 다음 단계를 따를 수 있습니다.

1. 응용 프로그램에서 사용하는 각 공급자에 대한 클라이언트 ID 및 클라이언트 보안 연결 정보를 복사합니다.
2. 게이트웨이의 /signin-* 끝점을 각 공급자에 대한 추가 리디렉션 URI로 추가합니다. 

>[AZURE.NOTE]Twitter 및 Microsoft 계정과 같은 일부 공급자는 서로 다른 도메인의 리디렉션 URI를 여러 개 지정할 수 있도록 허용하지 않습니다. 앱에서 이러한 공급자 중 하나를 사용하며 사용자 ID에 종속된 경우 지금은 업그레이드를 시도하지 않는 것이 좋습니다.

##<a name="updating-clients"></a>클라이언트 업데이트
작동하는 모바일 앱 백 엔드가 있으면 새 모바일 앱을 사용하도록 클라이언트 응용 프로그램을 업데이트할 수 있습니다. 모바일 앱에는 개발자가 새로운 앱 서비스 기능을 이용할 수 있게 하는 새 버전의 모바일 서비스 클라이언트 SDK도 포함됩니다. 모바일 앱 버전의 백 엔드가 있으면 새 SDK 버전을 활용하는 새 버전의 클라이언트 응용 프로그램을 릴리스할 수 있습니다.

클라이언트 코드에서 필요한 주요 변경 내용은 생성자에 있습니다. 모바일 앱 코드 사이트의 URL 외에, 인증 설정을 호스트하는 앱 서비스 게이트웨이의 URL을 제공해야 합니다.

    public static MobileServiceClient MobileService = new MobileServiceClient(
        "https://contoso.azurewebsites.net", // URL of the Mobile App
        "https://contoso-gateway.azurewebsites.net", // URL of the App Service Gateway
        "" // Formerly app key. To be removed in future client SDK update
    );

이렇게 하면 클라이언트가 모바일 앱의 구성 요소에 요청을 라우팅할 수 있습니다. 대상 플랫폼과 관련된 자세한 내용은 해당 [모바일 앱 만들기] 항목을 참조하세요.

동일한 업데이트에서 수행하는 모든 푸시 알림 등록 호출을 조정해야 합니다. 등록 프로세스를 향상시키는 새로운 API가 있습니다(Windows를 예제로 사용).

    var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    await MobileService.GetPush().Register(channel.Uri); 

대상 플랫폼과 관련된 자세한 내용은 [모바일 앱에 푸시 알림 추가] 및 [플랫폼 간 푸시 알림 전송] 항목을 참조하세요.

고객이 이러한 업데이트를 받고 나면 모바일 서비스 버전의 앱을 삭제할 수 있습니다. 이 시점에서 최신 모바일 앱 서버 SDK를 사용하여 앱 서비스 모바일 앱의 업그레이드를 완전히 완료했습니다.

<!-- URLs. -->

[Preview Azure 관리 포털]: https://portal.azure.com/
[구성]: https://manage.windowsazure.com/
[모바일 앱 정의]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ko-KR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[모바일 앱 서버 SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[모바일 앱 만들기]: app-service-mobile-xamarin-ios-get-started.md
[모바일 앱에 푸시 알림 추가]: app-service-mobile-xamarin-ios-get-started-push.md
[모바일 앱에 인증 추가]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure 스케줄러]: /ko-KR/documentation/services/scheduler/
[웹 작업]: ../app-service-web/websites-webjobs-resources.md
[플랫폼 간 푸시 알림 전송]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[.NET 서버 SDK를 사용하는 방법]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Migrate from Mobile Services to an App Service Mobile App]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[기존 모바일 서비스를 앱 서비스로 마이그레이션]: app-service-mobile-dotnet-backend-migrating-from-mobile-services.md
[앱 서비스 가격 책정]: https://azure.microsoft.com/ko-KR/pricing/details/app-service/

<!---HONumber=Nov15_HO3-->