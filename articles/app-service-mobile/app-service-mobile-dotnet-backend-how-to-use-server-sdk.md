<properties
	pageTitle="모바일 앱용 .NET 백 엔드 서버 SDK를 사용하는 방법 | Azure 앱 서비스"
	description="Azure 앱 서비스 모바일 앱용 .NET 백엔드 서버 SDK를 사용하는 방법에 대해 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Azure 모바일 앱용 .NET 백엔드 서버 SDK 사용

이 항목은 주요 Azure 앱 서비스 모바일 앱 시나리오에서 .NET 백 엔드 서버 SDK를 사용하는 방법을 보여줍니다. Azure 모바일 앱 SDK를 사용하면 ASP.NET 응용 프로그램에서 모바일 클라이언트를 사용하여 작업할 수 있습니다.

## 방법: SDK 다운로드 및 초기화

SDK는 [NuGet.org]에서 사용할 수 있습니다. 이 패키지는 SDK를 사용하여 시작하는 데 필요한 기본 기능을 포함합니다. SDK를 초기화하려면 **HttpConfiguration** 개체에서 작업을 수행해야 합니다.

###SDK 설치

SDK를 설치하려면 마우스 오른쪽 단추로 Visual Studio에서 서버 프로젝트를 클릭하고 **NuGet 패키지 관리**를 선택합니다. [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 패키지를 검색한 다음 **설치**를 클릭합니다.

###서버 프로젝트 초기화

A .NET 백 엔드 서버 프로젝트는 OWIN 시작 클래스를 포함하여 다른 ASP.NET 프로젝트와 유사하게 초기화됩니다. Visual Studio에 이 클래스를 추가하려면 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** -> **새 항목** -> **웹** -> **일반 ** -> **OWIN 시작 클래스**를 차례로 선택합니다.

그러면 다음과 같은 특성의 클래스가 생성됩니다.

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

OWIN 시작 클래스의 `Configuration()` 메서드에서 서비스에 대한 구성 옵션을 나타내는 **HttpConfiguration** 개체를 사용하여 서버 프로젝트를 설정합니다. 다음 예제는 추가된 기능이 없는 서버 프로젝트를 초기화합니다.

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

개별 기능을 사용하려면 **ApplyTo**를 호출하기 전에**MobileAppConfiguration** 개체에서 확장 메서드를 호출해야 합니다. 예를 들어 다음 코드는 초기화하는 동안 모든 API 컨트롤러에 기본 경로를 추가합니다.

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

기능 확장 메서드 대부분을 포함할 수 있는 추가 NuGet 패키지를 통해 사용할 수 있으며 이를 아래 섹션에서 설명합니다. Azure 포털의 빠른 시작 서버에서 **UseDefaultConfiguration()**을 호출합니다. 이것은 다음 설정과 같습니다.
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### SDK 확장

다음 NuGet 기반 확장 패키지는 응용 프로그램에서 사용할 수 있는 다양한 모바일 기능을 제공합니다. **MobileAppConfiguration** 개체를 사용하여 초기화하는 동안 확장을 사용할 수 있습니다.

- [Microsoft.Azure.Mobile.Server.Quickstart]는 기본 모바일 앱 설정을 지원합니다. 초기화하는 동안 **UseDefaultConfiguration** 확장 메서드를 호출하여 구성에 추가했습니다. 이 확장은 알림, 인증, 엔터티, 테이블, Crossdomain 및 홈 패키지와 같은 확장을 포함합니다. Azure 포털에서 다운로드하는 빠른 시작 서버 프로젝트와 같습니다.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/)은 웹 사이트 루트에 대해 기본 *이 모바일 앱이 실행 중인 페이지*를 구현합니다. **AddMobileAppHomeController** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/)은 데이터로 작업하기 위한 클래스를 포함하고 데이터 파이프라인을 설정합니다. **AddTables** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) SQL 데이터베이스에서 데이터를 액세스하는 Entity Framework를 사용할 수 있도록 합니다. **AddTablesWithEntityFramework** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Authentication] 인증을 사용할 수 있도록 하고 토큰의 유효성을 검사하는 데 사용되는 OWIN 미들웨어를 설정합니다. **AddAppServiceAuthentication** 및 **IAppBuilder**.**UseMobileAppAuthentication** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Notifications] 푸시 알림을 사용할 수 있도록 하며 푸시 등록 끝점을 정의합니다. **AddPushNotifications** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 모바일 앱에서 레거시 웹 브라우저에 데이터를 제공하는 컨트롤러를 만듭니다. **MapLegacyCrossDomainController** 확장 메서드를 호출하여 구성에 추가합니다.

## 방법: 사용자 지정 API 컨트롤러 정의

사용자 지정 API 컨트롤러는 끝점을 노출하여 모바일 앱 백 엔드에서 가장 기본적인 기능을 제공합니다. 사용자 지정 API 컨트롤러

1. Visual Studio에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** > **컨트롤러**를 클릭하고 **웹 API 2 Controller&mdash;Empty**를 선택하며 **추가**를 클릭합니다.

2. `CustomController`와 같은 **컨트롤러 이름**을 제공하고 **추가**를 클릭합니다. **ApiController**에서 상속한 새 **CustomController** 클래스를 만듭니다.

3. 새로운 컨트롤러 클래스 파일에서 다음 using 문을 추가합니다.

		using Microsoft.Azure.Mobile.Server.Config;

4. 다음 예제와 같이 **MobileAppControllerAttribute**를 API 컨트롤러 클래스 정의에 적용합니다.

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. App\_Start/Startup.MobileApp.cs 파일에서 다음 예제와 같이 **MapApiControllers** 확장 메서드에 호출을 추가합니다.

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	**UseDefaultConfiguration**을 대신 호출하는 경우 **MapApiControllers**를 호출하지 않아도 되며 이는 모든 기능을 초기화합니다.

**MobileAppControllerAttribute**가 적용되지 않는 모든 컨트롤러는 클라이언트에서 여전히 액세스할 수 있지만 모든 모바일 앱 클라이언트 SDK로 올바르게 사용되지 않습니다.

## 방법: 테이블 컨트롤러 정의

테이블 컨트롤러는 SQL 데이터베이스 또는 Azure 테이블 저장소와 같은 테이블 기반 데이터 저장소에 엔터티 데이터에 대한 액세스를 제공합니다. 테이블 컨트롤러는 **TableController** 일반 클래스에서 상속하는데 여기서 제네릭 형식은 다음과 같이 테이블 스키마를 나타내는 모델의 엔터티입니다.

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

테이블 컨트롤러가 **AddTables** 확장 메서드를 사용하여 초기화됩니다. 다음 예제는 데이터 액세스를 위한 Entity Framework를 사용하는 테이블 컨트롤러를 초기화합니다.

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Entity Framework를 사용하여 Azure SQL 데이터베이스에서 데이터에 액세스하는 테이블 컨트롤러의 예는 Azure 포털의 빠른 시작 서버 프로젝트 다운로드에서 **TodoItemController** 클래스를 참조하세요.

## 방법: 서버 프로젝트에 인증 추가

**MobileAppConfiguration** 개체를 확장하고 OWIN 미들웨어를 구성하여 서버 프로젝트에 인증을 추가할 수 있습니다. [Microsoft.Azure.Mobile.Server.Quickstart] 패키지를 설치하고 **UseDefaultConfiguration** 확장 메서드를 호출하는 경우 3단계로 건너뛸 수 있습니다.

1. Visual Studio에서 [Microsoft.Azure.Mobile.Server.Authentication] 패키지를 설치합니다. 

2. Startup.cs 프로젝트 파일에서 **Configuration** 메서드의 시작 부분에 다음 코드 줄을 추가합니다.

		app.UseMobileAppAuthentication(config);

	Azure 모바일 앱을 사용하는 OWIN 미들웨어 구성 요소를 추가하여 관련된 앱 서비스 게이트웨이에서 발급된 토큰의 유효성을 검사합니다.

3. `[Authorize]` 특성을 인증을 요구하는 모든 컨트롤러 또는 메서드에 추가합니다. 이제 해당 끝점 또는 특정 API에 액세스하려면 사용자를 인증해야 합니다.

모바일 앱 백 엔드에 클라이언트를 인증하는 방법에 대해 알아보려면 [앱에 인증 추가](app-service-mobile-dotnet-backend-ios-get-started-users.md)를 참조하세요.

## 방법: 서버 프로젝트에 푸시 알림 추가

**MobileAppConfiguration** 개체를 확장하고 알림 허브 클라이언트를 만들어 서버 프로젝트에 푸시 알림을 추가할 수 있습니다. [Microsoft.Azure.Mobile.Server.Quickstart] 패키지를 설치하고 **UseDefaultConfiguration** 확장 메서드를 호출하는 경우 3단계로 건너뛸 수 있습니다.

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. Microsoft.Azure.Mobile.Server.Notifications`를 검색한 다음 **설치**를 클릭합니다. 그러면 [Microsoft.Azure.Mobile.Server.Notifications] 패키지가 설치됩니다.
 
3. 이 단계를 반복하여 알림 허브 클라이언트 라이브러리를 포함하는 `Microsoft.Azure.NotificationHubs` 패키지를 설치합니다.

2. App\_Start/Startup.MobileApp.cs에서 초기화하는 동안 다음과 같이 **AddPushNotifications** 확장 메서드에 호출을 추가합니다.

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	서버 프로젝트에 푸시 알림 등록 끝점을 만듭니다. 이 끝점을 연결된 알림 허브와 함께 등록하려면 클라이언트에서 사용합니다. 이제 알림을 보내는데 사용되는 알림 허브 클라이언트를 추가해야 합니다.

3. 푸시 알림을 보내려는 컨트롤러에 다음 using 문을 추가합니다.

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. 알림 허브 클라이언트를 만드는 다음 코드를 추가합니다.

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

이제 등록된 장치에 푸시 알림을 보내는 데 알림 허브 클라이언트를 사용할 수 있습니다. 자세한 내용은 [앱에 푸시 알림 추가](app-service-mobile-ios-get-started-push.md)를 참조하세요. 알림 허브를 통해 수행할 수 있는 모든 것에 대한 자세한 내용은 [알림 허브 개요](../notification-hubs/notification-hubs-overview.md)를 참조하세요.

## 방법: 서버 프로젝트 게시

서버 프로젝트를 Azure에 게시하려면 다음 단계를 사용합니다.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO1-->