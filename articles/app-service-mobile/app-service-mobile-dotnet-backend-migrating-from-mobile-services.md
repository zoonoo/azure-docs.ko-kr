<properties 
	pageTitle="모바일 서비스에서 앱 서비스 모바일 앱으로 마이그레이션" 
	description="모바일 서비스 응용 프로그램을 앱 서비스 모바일 앱으로 쉽게 마이그레이션하는 방법을 알아봅니다." 
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
	ms.date="11/04/2015" 
	ms.author="mahender"/>

# 기존 Azure 모바일 서비스를 앱 서비스로 마이그레이션 

이 항목에서는 기존 응용 프로그램을 Azure 모바일 서비스에서 새로운 앱 서비스 모바일 앱으로 마이그레이션하는 방법을 보여 줍니다. 기존의 모든 모바일 서비스 앱을 새로운 앱 서비스 모바일 앱으로 쉽게 마이그레이션할 수 있습니다. 마이그레이션 중에 기존 모바일 서비스 응용 프로그램이 계속 작동할 수 있습니다.

>[AZURE.NOTE]현재 수동 마이그레이션을 사용하여 .NET 백 엔드 모바일 서비스를 앱 서비스로 마이그레이션할 수 있습니다. Node.js와 .NET에 대한 완성 인도 방식의 마이그레이션 환경이 곧 제공됩니다. 그러나 수동 마이그레이션을 수행하는 경우 기존의 **service.azure-mobile.net** URL을 보존할 수 *없습니다*.

앱이 Azure 앱 서비스로 마이그레이션되면 모든 앱 서비스 기능에 액세스할 수 있고 모바일 서비스 가격 책정이 아닌 [앱 서비스 가격 책정]에 따라 요금이 청구됩니다.

### <a name="why-host"></a>왜 앱 서비스에서 호스트하나요?

앱 서비스는 응용 프로그램에 대해 더 풍부한 기능의 호스팅 환경을 제공합니다. 앱 서비스에서 호스팅하여 서비스 슬롯, 사용자 지정 도메인, 트래픽 관리자 지원 및 기타에 대해 액세스할 수 있습니다. [앱 서비스의 모바일 앱에 모바일 서비스를 마이그레이션]하는 동안 일부 고객은 SDK 업데이트를 수행하지 않고 이러한 기능을 즉시 활용하려고 할 수 있습니다.

앱 서비스에서 호스팅되는 주요 제한 사항은 모바일 서비스가 예약된 작업이 통합되지 않고 Azure 스케줄러가 사용자 지정 API을 적중하도록 설정되거나 웹 작업 기능이 사용되도록 설정되어야 합니다.

앱 서비스의 이점에 대한 자세한 내용은 [모바일 서비스 vs. 앱 서비스] 항목을 참조하세요.

##마이그레이션과 업그레이드 비교

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

  - Node.js 기반 서버 프로젝트의 경우 새로운 [모바일 앱 Node.js SDK](https://github.com/Azure/azure-mobile-apps-node)에서 다양한 새로운 기능을 제공합니다. 예를 들어 이제 로컬 개발 및 디버깅을 수행하고 0.10 이후의 모든 Node.js 버전을 사용할 수 있으며 원하는 Express.js 미들웨어로 사용자 지정할 수 있습니다.

  - .NET 기반 서버 프로젝트의 경우 새로운 [모바일 앱 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)는 NuGet 종속성에 대한 유연성을 더 많이 제공하고, 새로운 앱 서비스 인증 기능을 지원하며, MVC를 비롯한 모든 ASP.NET 프로젝트를 사용하여 작성합니다. 업그레이드에 대한 자세한 내용은 [기존 .NET 모바일 서비스를 앱 서비스로 업그레이드](app-service-mobile-net-upgrading-from-mobile-services.md)를 참조하세요.


##<a name="understand"></a>앱 서비스 모바일 이해 

앱 서비스 모바일은 Microsoft Azure를 사용하여 모바일 응용 프로그램을 빌드하는 새로운 방법입니다. 모바일 앱에 대한 자세한 내용은 [모바일 앱 정의] 항목을 참조하세요.

모바일 앱으로 마이그레이션하는 경우 기존 앱 기능(및 코드)을 모두 보존할 수 있습니다. 또한 응용 프로그램에서 새로운 기능을 사용할 수 있습니다. 모바일 앱 모델에서 코드는 실제로 웹앱(새 버전의 Azure 웹 사이트)에서 실행됩니다. 웹앱과 웹앱 작동 방식을 완전히 제어할 수 있습니다. 뿐만 아니라 이전에 모바일 서비스 고객이 사용할 수 없었던 웹앱 기능(예: 트래픽 관리자 및 개발 슬롯)을 이제 사용할 수 있습니다.


##모바일 서비스 .NET 백 엔드에 대한 수동 마이그레이션

이 섹션에서는 Azure 앱 서비스 내에서 .NET 모바일 서비스 프로젝트를 호스트하는 방법을 보여 줍니다. 이러한 방식으로 호스트되는 앱은 모든 *모바일 서비스* .NET 런타임 자습서를 사용할 수 있습니다. 단, azure-mobile.net 도메인을 사용하는 모든 URL을 앱 서비스 인스턴스의 도메인으로 대체해야 합니다.

또한 모바일 서비스 프로젝트는 [앱 서비스 환경]에서 호스트될 수 있습니다. 이 항목의 모든 내용이 여전히 적용되지만 사이트는 contoso.azurewebsites.net 대신 contoso.contosoase.p.azurewebsites.net 형태입니다.

>[AZURE.NOTE]수동 마이그레이션을 수행하는 경우 기존의 **service.azure-mobile.net** URL을 보존할 수 *없습니다*. 이 URL에 연결하는 모바일 클라이언트가 있는 경우 자동 마이그레이션 옵션을 사용하거나 모든 모바일 클라이언트가 새 URL로 업그레이드될 때까지 모바일 서비스를 계속 실행해야 합니다.


### <a name="app-settings"></a>응용 프로그램 설정
모바일 서비스에는 환경에서 사용 가능한 여러 응용 프로그램 설정이 필요합니다. 이에 대해서는 이 섹션에 설명되어 있습니다.

웹앱에서 응용 프로그램 설정을 구성하기 위해 먼저 [Azure 관리 포털 미리 보기]에 로그인합니다. "설정" 및 "응용 프로그램 설정"을 차례로 사용하고 선택하려는 웹, 모바일 또는 API 앱으로 이동합니다. "앱 설정"이라는 섹션까지 아래로 스크롤합니다. 여기에 필요한 키-값 쌍을 설정할 수 있습니다.
 
**MS\_MobileServiceName**은 앱의 이름으로 설정해야 합니다. 예를 들어 URL contoso.azurewebsites.net을 가진 앱에서 실행하는 경우 "contoso"는 올바른 값입니다.
 
**MS\_MobileServiceDomainSuffix**는 웹앱의 이름으로 설정해야 합니다. 예를 들어 URL contoso.azurewebsites.net을 가진 앱에서 실행하는 경우 "azurewebsites.net"는 올바른 값입니다.
 
**MS\_ApplicationKey**는 임의의 값으로 설정할 수 있지만 클라이언트 SDK에서 사용되는 동일한 값이어야 합니다. GUID는 사용하는 것이 좋습니다.
 
**MS\_MasterKey**는 임의의 값으로 설정할 수 있지만, 이 값은 관리자 API/클라이언트에서 사용되는 값과 같아야 합니다. GUID는 사용하는 것이 좋습니다.
 
기존 모바일 서비스 응용 프로그램을 통해 이동하는 경우 마스터 키와 응용 프로그램 키는 모두 [Azure 관리 포털]의 모바일 서비스 섹션에 있는 "구성" 탭에서 가져올 수 있습니다. 아래쪽에 있는 "키 관리" 작업을 선택하고 키를 복사합니다.


### <a name="client-sdk"></a>방법: 클라이언트 SDK 수정

클라이언트 앱 프로젝트에서 모바일 서비스 클라이언트 개체의 생성자를 수정하여 새로운 앱 URL(예: `https://contoso.azurewebsites.net`) 및 이전에 구성한 응용 프로그램 키를 적용합니다. 클라이언트 SDK 버전은 **모바일 서비스** 버전이어야 하고 업그레이드되지 **않아야** 합니다. iOS 및 Android 클라이언트의 경우 2.x 버전을 사용하고 Windows/Xamarin의 경우 1.3.2를 사용합니다. Javascript 클라이언트는 1.2.7을 사용해야 합니다.

### <a name="data"></a>방법: 데이터 기능 활성화

모바일 서비스에서 기본 Entity Framework 클래스를 사용하기 위해 추가로 두 개의 앱 설정이 필요합니다.
 
연결 문자열은 응용 프로그램 설정 블레이드의 **앱 설정** 섹션 바로 아래에 있는 "연결 문자열" 섹션에 저장됩니다. 데이터베이스에 대한 연결 문자열은 **MS\_TableConnectionString** 키에서 설정되어야 합니다. 기존 모바일 서비스 응용 프로그램을 통해 이동하는 경우 모바일 서비스 포털에서 구성 탭의 "연결 문자열" 섹션으로 이동합니다. "연결 문자열 표시"를 클릭하고 값을 복사합니다.
 
기본적으로 사용할 스키마는 **MS\_MobileServiceName**이지만 **MS\_TableSchema** 설정으로 덮어쓸 수 있습니다. 다시 **앱 설정**에서 **MS\_TableSchema**를 사용할 스키마의 이름으로 설정합니다. 기존 모바일 서비스 응용 프로그램을 통해 이동하는 경우 스키마는 Entity Framework를 사용하여 만들어집니다. 이것은 코드를 호스팅하는 앱 서비스 인스턴스가 아니라 모바일 서비스의 이름입니다.

### <a name="push"></a>방법: 푸시 기능 활성화

작동할 푸시의 경우 웹앱은 추가로 알림 허브에 대한 정보를 알고 있어야 합니다.
 
"연결 문자열"에서 **MS\_NotificationHubConnectionString**을 알림 허브에 대한 DefaultFullSharedAccessSignature 연결 문자열을 사용하여 설정합니다. 기존 모바일 서비스 응용 프로그램을 통해 이동하는 경우 모바일 서비스 포털에서 구성 탭의 "연결 문자열" 섹션으로 이동합니다. "연결 문자열 표시"를 클릭하고 값을 복사합니다.

**MS\_NotificationHubName** 앱 설정은 허브의 이름으로 설정되어야 합니다. 기존 모바일 서비스 앱을 이동할 때 모바일 서비스 포털의 푸시 탭에서 이 값을 가져올 수 있습니다. 이 탭에서 다른 필드는 자체 허브에 연결되고 어디에도 복사할 필요가 없습니다.
 
### <a name="auth"></a>방법: 인증 기능 활성화

또한 ID 기능에는 개별 공급자에 필요한 앱 설정을 요구 사항이 있습니다. 기존 모바일 서비스 앱으로 이동하는 경우 각 모바일 서비스 포털의 ID 탭에 있는 각각의 필드에는 해당하는 앱 설정이 있습니다.
 
Microsoft 계정

* **MS\_MicrosoftClientID**

* **MS\_MicrosoftClientSecret**

* **MS\_MicrosoftPackageSID**
 
Facebook

* **MS\_FacebookAppID**

* **MS\_FacebookAppSecret**
 
Twitter

* **MS\_TwitterConsumerKey**

* **MS\_TwitterConsumerSecret**
 
Google

* **MS\_GoogleClientID**

* **MS\_GoogleClientSecret**
 
AAD

* **MS\_AadClientID**

* **MS\_AadTenants** - 참고: **MS\_AadTenants**는 테넌트 도메인의 쉼표로 구분된 목록으로 저장됩니다(모바일 서비스 포털에서 "허용된 테넌트" 필드).

### <a name="publish"></a>방법: 모바일 서비스 프로젝트 게시

1. 미리 보기 포털에서 앱으로 이동하고 명령 모음에서 "Get 게시 프로필"를 선택합니다. 로컬 컴퓨터에 다운로드한 프로필을 저장합니다.
2. Visual Studio에서 모바일 서비스 서버 프로젝트를 마우스 오른쪽 단추로 클릭하고 "게시"를 선택합니다. 프로필 탭에서 "가져오기"를 선택하고 다운로드한 프로필을 찾습니다.
3. 게시를 클릭하여 앱 서비스에 코드를 배포합니다.

로그는 표준 앱 서비스 로깅 기능을 통해 처리됩니다. 로깅에 대해 자세히 알아보려면 [Azure 앱 서비스에서 진단 로깅 사용]을 참조하세요.



<!-- URLs. -->

[Azure 관리 포털 미리 보기]: https://portal.azure.com/
[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 앱 정의]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ko-KR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /ko-KR/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[Send cross-platform push notifications]: app-service-mobile-xamarin-ios-push-notifications-to-user.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Azure 앱 서비스에서 진단 로깅 사용]: web-sites-enable-diagnostic-log.md
[앱 서비스 가격 책정]: https://azure.microsoft.com/ko-KR/pricing/details/app-service/
[앱 서비스 환경]: app-service-app-service-environment-intro.md
[모바일 서비스 vs. 앱 서비스]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[앱 서비스의 모바일 앱에 모바일 서비스를 마이그레이션]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

<!---HONumber=Nov15_HO3-->