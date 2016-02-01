<properties
	pageTitle="모바일 앱용 .NET 백 엔드 서버 SDK를 사용하는 방법 | Azure 앱 서비스"
	description="Azure 앱 서비스 모바일 앱용 .NET 백엔드 서버 SDK를 사용하는 방법에 대해 알아봅니다."
	keywords="앱 서비스, Azure 앱 서비스, 모바일 앱, 모바일 서비스, 규모, 확장성, 앱 배포, Azure 앱 배포"
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
	ms.date="01/09/2016"
	ms.author="glenga"/>

# Azure 모바일 앱용 .NET 백엔드 서버 SDK 사용

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

이 항목은 주요 Azure 앱 서비스 모바일 앱 시나리오에서 .NET 백 엔드 서버 SDK를 사용하는 방법을 보여줍니다. Azure 모바일 앱 SDK를 사용하면 ASP.NET 응용 프로그램에서 모바일 클라이언트를 사용하여 작업할 수 있습니다.

>[AZURE.TIP] [Azure 모바일 앱용 .NET 서버 SDK](https://github.com/Azure/azure-mobile-apps-net-server)는 GitHub의 오픈 소스입니다. 리포지토리에는 전체 서버 SDK 단위 테스트 도구 모음과 일부 샘플 프로젝트가 포함되어 있습니다.

## 참조 설명서

서버 SDK에 대한 참조 설명서는 [Azure 모바일 앱 .NET 참조](https://msdn.microsoft.com/library/azure/dn961176.aspx)를 참조하세요.

## <a name="create-app"></a>방법: 모바일 응용 프로그램에 대한 .NET 백 엔드 만들기

새 프로젝트를 시작하는 경우 [Azure 포털]과 Visual Studio 중 하나를 사용하여 앱 서비스 응용 프로그램을 만들 수 있습니다. 이 섹션에서 다음 중 하나를 사용하여 간단한 ToDo List API를 호스팅하는 새 모바일 응용 프로그램 백 엔드를 만들 수 있습니다. 이 작업을 로컬로 실행하거나 클라우드 기반 앱 서비스 모바일 앱에 프로젝트를 게시할 수 있습니다.

기존 프로젝트에 모바일 기능을 추가하는 경우 아래 [SDK 다운로드 및 초기화](#install-sdk) 섹션을 참조하세요.

### Azure 포털을 사용하여 .NET 백 엔드 만들기

바로 [Azure 포털]에서 새 모바일 응용 프로그램을 만들 수 있습니다. 아래 단계를 수행하거나 [모바일 앱 만들기](app-service-mobile-ios-get-started.md) 자습서에 따라 새 클라이언트 및 서버를 함께 만들 수 있습니다.

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. 시작 블레이드로 돌아가서 **테이블 API 만들기** 아래에서 **백 엔드 언어**로 **C#**을 선택합니다.

&nbsp;&nbsp;10. 다운로드를 클릭하고 로컬 컴퓨터에 압축된 프로젝트 파일을 풀고 Visual Studio에서 솔루션을 엽니다.

### Visual Studio 2013 및 Visual Studio 2015를 사용하여 .NET 백 엔드 만들기

Visual Studio에서 모바일 앱 프로젝트를 만들려면 [Azure SDK for .NET](https://azure.microsoft.com/downloads/) 2.8.1 이상의 버전을 설치해야 합니다. SDK를 설치한 후 새 ASP.NET 응용 프로그램을 만듭니다.

1. **새 프로젝트** 대화를 엽니다.(파일 > **새로 만들기** > **프로젝트...**에서)

2. **템플릿** > **Visual C#**를 확장하고 **웹**을 선택합니다.

3. **ASP.NET 웹 응용 프로그램**을 선택합니다.

4. 프로젝트 이름을 입력합니다. 그런 후 **OK**를 클릭합니다.

5. ASP.NET 4.5.2 템플릿 아래에서 **Azure 모바일 앱**을 선택합니다. **클라우드에 호스트**를 선택하여 클라우드에 이 프로젝트를 게시할 수 있는 새 모바일 앱을 만듭니다.

6. **확인**을 클릭합니다. 응용 프로그램이 만들어지고 솔루션 탐색기에 나타납니다.

## <a name="install-sdk"></a>방법: SDK 다운로드 및 설치

SDK는 [NuGet.org]에서 사용할 수 있습니다. 이 패키지는 SDK를 사용하여 시작하는 데 필요한 기본 기능을 포함합니다. SDK를 초기화하려면 **HttpConfiguration** 개체에서 작업을 수행해야 합니다.

###SDK 설치

SDK를 설치하려면 Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 선택합니다. [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 패키지를 검색한 다음 **설치**를 클릭합니다.

###<a name="server-project-setup"></a> 서버 프로젝트 초기화

A .NET 백 엔드 서버 프로젝트는 OWIN 시작 클래스를 포함하여 다른 ASP.NET 프로젝트와 유사하게 초기화됩니다. NuGet 패키지 `Microsoft.Owin.Host.SystemWeb`을 참조하도록 확인합니다. Visual Studio에서 이 클래스를 추가하려면 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** > **웹** > **일반** > **OWIN 시작 클래스**를 차례로 선택합니다.

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

개별 기능을 사용하려면 **ApplyTo**를 호출하기 전에 **MobileAppConfiguration** 개체에서 확장 메서드를 호출해야 합니다. 예를 들어 다음 코드는 초기화하는 동안 `[MobileAppController]` 특성을 포함하는 모든 API 컨트롤러에 기본 경로를 추가합니다.

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

`MapApiControllers`는 `[MobileAppController]` 특성을 포함하는 컨트롤러만 매핑합니다.

기능 확장 메서드 대부분을 포함할 수 있는 추가 NuGet 패키지를 통해 사용할 수 있으며 이를 아래 섹션에서 설명합니다.

Azure 포털의 빠른 시작 서버에서 **UseDefaultConfiguration()**을 호출합니다. 이것은 다음 설정과 같습니다.
    
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

다음 NuGet 기반 확장 패키지는 응용 프로그램에서 사용할 수 있는 다양한 모바일 기능을 제공합니다. **MobileAppConfiguration** 개체를 사용하여 초기화하는 동안 확장을 사용하도록 설정합니다.

- [Microsoft.Azure.Mobile.Server.Quickstart] 기본 모바일 앱 설정을 지원합니다. 초기화하는 동안 **UseDefaultConfiguration** 확장 메서드를 호출하여 구성에 추가했습니다. 이 확장은 알림, 인증, 엔터티, 테이블, Crossdomain 및 홈 패키지와 같은 확장을 포함합니다. Azure 포털에서 다운로드하는 빠른 시작 서버 프로젝트와 같습니다.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) 웹 사이트 루트에 대해 기본 이 모바일 앱이 실행 중인 페이지를 구현합니다. **AddMobileAppHomeController** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) 데이터로 작업하기 위한 클래스를 포함하고 데이터 파이프라인을 설정합니다. **AddTables** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) SQL 데이터베이스에서 데이터를 액세스하는 Entity Framework를 사용할 수 있도록 합니다. **AddTablesWithEntityFramework** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Authentication] 인증을 사용할 수 있도록 하고 토큰의 유효성을 검사하는 데 사용되는 OWIN 미들웨어를 설정합니다. **AddAppServiceAuthentication** 및 **IAppBuilder**.**UseAppServiceAuthentication** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Notifications] 푸시 알림을 사용할 수 있도록 하며 푸시 등록 끝점을 정의합니다. **AddPushNotifications** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) 모바일 앱에서 레거시 웹 브라우저에 데이터를 제공하는 컨트롤러를 만듭니다. **MapLegacyCrossDomainController** 확장 메서드를 호출하여 구성에 추가합니다.

- [Microsoft.Azure.Mobile.Server.Login] AppServiceLoginHandler.CreateToken() 메서드를 통해 사용자 지정 인증에 대한 미리 보기 지원을 제공합니다. 이는 정적 메서드이며 구성에서 사용하도록 설정할 필요는 없습니다.

## <a name="publish-server-project"></a>방법: 서버 프로젝트 게시

이 섹션에서는 Visual Studio에서 .NET 백 엔드 프로젝트를 게시하는 방법을 보여줍니다. [Azure 앱 서비스 배포 설명서](../app-service-web/web-site-deploy.md)에 나오는 Git 또는 다른 메서드를 사용하여 백 엔드 프로젝트를 배포할 수도 있습니다.

1. Visual Studio에서 NuGet 패키지를 복원하려면 프로젝트를 빌드하십시오.

2. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시**를 클릭합니다. 처음 게시할 때는 게시 프로필을 정의해야 합니다. 이미 정의된 프로필이 있을 때 프로필을 선택하고 **게시**를 클릭합니다.

2. 게시 대상을 선택하도록 요청받으면 **Microsoft Azure 앱 서비스** > **다음**을 클릭한 다음 (필요한 경우) Azure 자격 증명을 사용하여 로그인합니다. Visual Studio가 Azure에서 직접 게시 설정을 안전하게 다운로드하고 저장합니다.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. **구독**을 선택하고 **보기**에서 **리소스 형식**을 선택하며 **모바일 앱**을 확장하고 모바일 앱 백 엔드를 클릭한 다음 **확인**을 클릭합니다.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. 게시 프로필 정보를 확인하고 **게시**를 클릭합니다.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	모바일 앱 백 엔드가 성공적으로 게시되면 성공했다는 것을 나타내는 방문 페이지가 표시됩니다.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## 방법: 테이블 컨트롤러 정의

테이블 컨트롤러는 SQL 데이터베이스 또는 Azure 테이블 저장소와 같은 테이블 기반 데이터 저장소에 엔터티 데이터에 대한 액세스를 제공합니다. 테이블 컨트롤러는 **TableController** 제네릭 클래스에서 상속하는데 여기서 제네릭 형식은 다음과 같이 테이블 스키마를 나타내는 모델의 엔터티입니다.

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

테이블 컨트롤러가 **AddTables** 확장 메서드를 사용하여 초기화됩니다. 이것은 `TableController`의 모든 하위 클래스에 대해 `/tables/` 하위 경로를 추가합니다.

다음 예제는 데이터 액세스를 위한 Entity Framework를 사용하는 테이블 컨트롤러를 초기화합니다.

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Entity Framework를 사용하여 Azure SQL 데이터베이스의 데이터를 액세스하는 테이블 컨트롤러의 예제는 Azure 포털의 빠른 시작 서버 프로젝트 다운로드에서 **TodoItemController** 클래스를 참조하세요.

## 방법: 사용자 지정 API 컨트롤러 정의

사용자 지정 API 컨트롤러는 끝점을 노출하여 모바일 앱 백 엔드에서 가장 기본적인 기능을 제공합니다. `MobileAppControllerAttribute` 특성을 사용하여 모바일 전용 API 컨트롤러를 등록할 수 있습니다. 이 특성은 경로를 등록하고 모바일 앱 JSON 직렬 변환기를 설정합니다.

1. Visual Studio에서 컨트롤러 폴더를 마우스 오른쪽 단추로 클릭한 다음 **추가** > **컨트롤러**를 클릭하고 **웹 API 2 컨트롤러&mdash;비어 있음**을 선택한 후 **추가**를 클릭합니다.

2. `CustomController`와 같은 **컨트롤러 이름**을 제공하고 **추가**를 클릭합니다. 이렇게 하면 **ApiController**에서 상속한 새 **CustomController** 클래스가 만들어집니다.

3. 새로운 컨트롤러 클래스 파일에서 다음 using 문을 추가합니다.

		using Microsoft.Azure.Mobile.Server.Config;

4. 다음 예제와 같이 **MobileAppControllerAttribute**를 API 컨트롤러 클래스 정의에 적용합니다.

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. App\_Start/Startup.MobileApp.cs 파일에서 다음 예제와 같이 **MapApiControllers** 확장 메서드에 대한 호출을 추가합니다.

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	**UseDefaultConfiguration**을 대신 호출하는 경우 **MapApiControllers**를 호출하지 않아도 되며 이는 모든 기능을 초기화합니다.

**MobileAppControllerAttribute**가 적용되지 않는 모든 컨트롤러는 클라이언트에서 여전히 액세스할 수 있지만 모든 모바일 앱 클라이언트 SDK로 올바르게 사용되지 않을 수 있습니다.

## 방법: 인증으로 작업

모바일 앱은 앱 서비스 인증 및 ASP.NET의 기능을 앱에 인증을 사용하도록 설정하는 과정에 사용합니다. 이 섹션에서는 .NET 백 엔드 서버 프로젝트에서 다음과 같은 인증 관련 작업을 수행하는 방법을 보여줍니다.

+ [방법: 서버 프로젝트에 인증 추가](#add-auth) 
+ [방법: 응용 프로그램에 사용자 지정 인증 사용](#custom-auth) 
+ [방법: 인증된 사용자 정보 검색](#user-info)

### <a name="add-auth"></a>방법: 서버 프로젝트에 인증 추가

**MobileAppConfiguration** 개체를 확장하고 OWIN 미들웨어를 구성하여 서버 프로젝트에 인증을 추가할 수 있습니다. [Microsoft.Azure.Mobile.Server.Quickstart] 패키지를 설치하고 **UseDefaultConfiguration** 확장 메서드를 호출하는 경우 3단계로 건너뛸 수 있습니다.

1. Visual Studio에서 [Microsoft.Azure.Mobile.Server.Authentication] 패키지를 설치합니다. 

2. Startup.cs 프로젝트 파일에서 **Configuration** 메서드의 시작 부분에 다음 코드 줄을 추가합니다.

		app.UseAppServiceAuthentication(config);

	Azure 모바일 앱을 사용하는 OWIN 미들웨어 구성 요소를 추가하여 관련된 앱 서비스 게이트웨이에서 발급된 토큰의 유효성을 검사합니다.

3. 인증을 요구하는 모든 컨트롤러 또는 메서드에 `[Authorize]` 특성을 추가합니다. 이제 해당 끝점 또는 특정 API에 액세스하려면 사용자를 인증해야 합니다.

모바일 앱 백 엔드에 클라이언트를 인증하는 방법에 대해 알아보려면 [앱에 인증 추가](app-service-mobile-ios-get-started-users.md)를 참조하세요.

### <a name="custom-auth"></a>방법: 응용 프로그램에 사용자 지정 인증 사용

앱 서비스 인증/권한 부여 공급자 중 하나를 사용하지 않으려면 본인의 고유한 로그인 시스템을 제공할 수 있습니다. 이렇게 하려면 [Microsoft.Azure.Mobile.Server.Login] 패키지를 설치합니다.

사용자가 로그인해야 하는지 여부를 결정하려면 본인의 고유한 논리를 제공해야 합니다. 예를 들어 데이터베이스의 솔트되고 해시된 암호를 기준으로 검사할 수 있습니다. 다음 예제에서 `isValidAssertion()` 메서드는 이러한 검사를 담당하며 다른 곳에 정의됩니다.

사용자 지정 인증은 새 ApiController를 만들고 아래와 같은 등록 및 로그인 작업을 노출하면 노출됩니다. 클라이언트는 사용자로부터 관련 정보를 수집하고 HTTPS POST 본문에 사용자 정보를 포함시켜 API에 제출하여 로그인을 시도할 수 있습니다. 서버가 어설션의 유효성을 검사하면 `AppServiceLoginHandler.CreateToken()` 메서드를 사용하여 토큰을 발급할 수 있습니다.

로그인 작업 예제는 다음과 같습니다.

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

위에서 LoginResult와 LoginResultUser는 표시된 속성을 노출하는 단순한 개체일 뿐입니다. 클라이언트는 로그인 응답을 통해 다음과 같은 형식의 JSON 개체로 돌아와야 합니다.

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

`MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()` 메서드는 audience 및 issuer 매개 변수를 포함합니다. 일반적으로 두 매개 변수 모두 HTTPS 체계를 사용하여 응용 프로그램 루트의 URL로 설정됩니다. 마찬가지로 secretKey를 응용 프로그램의 서명 키의 값으로 설정해야 합니다. 이 값은 클라이언트에서 공유되거나 포함되지 않아야 하는 중요한 값입니다. 앱 서비스에서 호스팅하는 동안 WEBSITE\_AUTH\_SIGNING\_KEY 환경 변수를 참조하여 이 값을 가져올 수 있습니다. 로컬 디버깅 컨텍스트에서 필요한 경우 [인증을 사용하여 로컬 디버깅](#local-debug) 섹션의 지침에 따라 키를 검색하고 이 키를 응용 프로그램 설정으로 저장합니다.

발급된 토큰 및 포함하고자 하는 클레임에 대한 수명도 제공해야 합니다. 예제 코드에 나오는 것처럼 제목 클레임을 제공해야 합니다.

수동 HTTP POST 대신 `loginAsync()` 메서드(이름은 플랫폼에 따라 달라질 수 있음)를 사용하도록 클라이언트 코드를 단순화할 수도 있습니다. 추가 토큰 매개 변수를 사용하고 POST할 어설션 개체를 상호 연결하는 오버로드를 사용합니다. 이 경우 공급자는 원하는 사용자 지정 이름이어야 합니다. 서버에서 로그인 작업은 이 사용자 지정 이름을 포함하는 /.auth/login/{customProviderName} 경로에 있어야 합니다. 이 경로에 컨트롤러를 배치하려면 MobileAppConfiguration을 적용하기 전에 HttpConfiguration에 경로를 추가합니다.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" }); 
		
위의 "CustomAuth" 문자열을 로그인 작업을 호스트하는 컨트롤러 이름으로 바꿉니다.

>[AZURE.TIP]loginAsync() 방식을 사용하여 인증 토큰이 서비스에 대한 모든 후속 호출에 연결되어 있는지 확인합니다.

###<a name="user-info"></a>방법: 인증된 사용자 정보 검색

사용자가 앱 서비스에서 인증을 하는 경우 .NET 백 엔드 코드에서 할당된 사용자 ID와 기타 정보에 액세스할 수 있습니다. 특정 사용자가 테이블 행 또는 다른 리소스에 액세스할 수 있는지 여부와 같이 백 엔드에서 지정된 사용자에 대한 권한 부여 결정에 유용합니다. 다음 코드에서는 로그인한 사용자에 대한 사용자 ID를 가져오는 방법을 보여줍니다.

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

SID는 공급자 특정 사용자 ID에서 파생되고 지정된 사용자 및 로그인 공급자에 대해 정적입니다.

또한 앱 서비스는 로그인 공급자에서 특정 클레임을 요청할 수 있습니다. 이렇게 하면 Facebook Graph API를 사용하여 공급자에서 추가 정보를 요청할 수 있습니다. 포털의 공급자 블레이드에서 클레임을 지정할 수 있습니다. 일부 클레임은 공급자를 사용하여 추가 구성이 필요합니다.

다음 코드는 **GetAppServiceIdentityAsync** 확장 메서드를 호출하여 로그인 자격 증명을 가져오며 이는 Facebook Graph API에 대한 요청에 필요한 액세스 토큰을 포함합니다.

    // Get the credentials for the logged-in user.
    var credentials = 
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token=" 
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

**GetAppServiceIdentityAsync** 확장 메서드 작업을 만드는 `System.Security.Principal`에 문을 사용하여 추가해야 합니다.


## 방법: 서버 프로젝트에 푸시 알림 추가

**MobileAppConfiguration** 개체를 확장하고 알림 허브 클라이언트를 만들어 서버 프로젝트에 푸시 알림을 추가할 수 있습니다. [Microsoft.Azure.Mobile.Server.Quickstart] 패키지를 설치하고 **UseDefaultConfiguration** 확장 메서드를 호출하는 경우 3단계로 건너뛸 수 있습니다.

1. Visual Studio에서 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 **NuGet 패키지 관리**를 클릭합니다. Microsoft.Azure.Mobile.Server.Notifications를 검색한 다음 **설치**를 클릭합니다. 그러면 [Microsoft.Azure.Mobile.Server.Notifications] 패키지가 설치됩니다.
 
3. 이 단계를 반복하여 알림 허브 클라이언트 라이브러리를 포함하는 `Microsoft.Azure.NotificationHubs` 패키지를 설치합니다.

2. App\_Start/Startup.MobileApp.cs에서 초기화하는 동안 다음과 같이 **AddPushNotifications** 확장 메서드에 대한 호출을 추가합니다.

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

##<a name="tags"></a>방법: 태그에 푸시를 사용하도록 설정하기 위해 장치 설치에 태그 추가

위의 **방법: 사용자 지정 API 컨트롤러 정의**를 따라 특정 장치 설치에 태그를 추가하려면 알림 허브를 사용하여 작업하도록 백 엔드에 사용자 지정 API를 설정하는 것이 좋습니다. 클라이언트 로컬 저장소에 저장된 설치 ID와 추가하려는 태그(백 엔드에서 직접 태그를 지정할 수 있으므로 선택적임)를 전달해야 합니다. 태그를 장치 설치 ID에 태그를 추가하려면 알림 허브를 사용하여 작업하도록 다음 코드 조각을 컨트롤러에 추가해야 합니다.

[Azure 알림 허브 NuGet](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) 사용([참조](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

이러한 태그에 푸시하려면 [알림 허브 API](https://msdn.microsoft.com/library/azure/dn495101.aspx)를 사용하여 작업합니다.

또한 백 엔드에서 직접 알림 허브를 사용하여 장치 설치를 등록하기 위한 사용자 지정 API를 구축할 수 있습니다.

## 방법: .NET 서버 SDK 디버그 및 문제 해결

Azure 앱 서비스는 ASP.NET 응용 프로그램에 대한 여러 디버깅 및 문제 해결 기술을 제공합니다.

- [Azure 앱 서비스 모니터링](../app-service-web/web-sites-monitor.md)
- [Azure 앱 서비스에 진단 로그 사용](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Visual Studio에서 Azure 앱 서비스 문제 해결](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### 로깅

표준 ASP.NET 추적 작성을 사용하여 앱 서비스 진단 로그에 쓸 수 있습니다. 로그를 작성하려면 먼저 모바일 앱 백 엔드에서 진단을 활성화해야 합니다.

진단을 사용하도록 설정하고 로그에 쓰려면:

1. [진단을 사용하는 방법](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag)에서 단계를 수행합니다.

2. 코드 파일에 다음 using 문을 추가합니다.

		using System.Web.Http.Tracing;

3. .NET 백 엔드에서 진단 로그에 작성하려면 다음과 같이 추적 작성기를 만듭니다.

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

4. 서버 프로젝트를 다시 게시하고 모바일 앱 백 엔드에 액세스하여 로깅을 통해 코드 경로를 실행합니다.

5. [방법: 로그 다운로드](../app-service-web/web-sites-enable-diagnostic-log.md#download)에 설명된 대로 로그를 다운로드하고 평가합니다.

### <a name="local-debug"></a>인증을 사용하여 로컬 디버깅

응용 프로그램을 로컬로 실행하여 변경 내용을 클라우드에 게시하기 전에 테스트할 수 있습니다. 앱이 많은 경우에도 Visual Studio에 있는 동안 F5를 누르기만 하면 됩니다. 그러나 인증을 사용할 때 몇 가지 추가 고려 사항이 있습니다.

클라우드 기반 모바일 앱에서 앱 서비스 인증/권한 부여를 구성해야 하며 클라이언트가 클라우드 끝점을 대체 로그인 호스트로 지정해야 합니다. 필요한 구체적인 단계는 선택한 클라이언트 플랫폼([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md))에 대한 설명서를 참조하세요.

응용 프로그램에 [Microsoft.Azure.Mobile.Server.Authentication]을 설치했는지 확인합니다. 그런 다음 `MobileAppConfiguration`를 `HttpConfiguration`에 적용한 후 응용 프로그램의 OWIN 시작 클래스에 다음을 추가합니다.
		
		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

위의 예제에서는 HTTPS 체계를 사용하여 Web.config 파일 내에서 authAudience 및 authIssuer 응용 프로그램 설정을 응용 프로그램 루트의 URL로 구성해야 합니다. 마찬가지로 authSigningKey를 응용 프로그램의 서명 키의 값으로 설정해야 합니다. 이 값은 클라이언트에서 공유되거나 포함되지 않아야 하는 중요한 값입니다. 이 값을 가져오려면 [Azure 포털] 내에서 앱으로 이동하고 **도구**를 클릭합니다. 그런 다음 **Kudu**, **이동**을 차례로 클릭합니다. 그러면 사이트에 대한 Kudu 관리 끝점으로 이동됩니다. **환경**을 클릭하고 WEBSITE\_AUTH\_SIGNING\_KEY 아래에서 값을 찾습니다. 이 값은 로컬 앱 구성에서 authSigningKey에 대해 사용해야 하는 값입니다.

로컬로 실행 중인 서버가 이제 클라이언트가 클라우드 기반 끝점에서 가져오는 토큰을 확인하게 됩니다.


[Azure 포털]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0121_2016-->