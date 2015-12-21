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
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/25/2015"
	ms.author="mahender"/>

# 기존 Azure 모바일 서비스를 앱 서비스로 마이그레이션

이 항목에서는 기존 응용 프로그램을 Azure 모바일 서비스에서 새로운 앱 서비스 모바일 앱으로 마이그레이션하는 방법을 보여 줍니다. 기존의 모든 모바일 서비스를 새로운 모바일 앱 백 엔드로 쉽게 마이그레이션할 수 있습니다. 마이그레이션하는 동안 기존 모바일 서비스는 계속 작동합니다.

모바일 서비스가 앱 서비스로 마이그레이션되면 모든 앱 서비스 기능에 액세스할 수 있고 모바일 서비스 가격 책정이 아닌 [앱 서비스 가격 책정]에 따라 요금이 청구됩니다.

예약된 작업이 Microsoft가 관리하는 Azure 스케줄러 계획에서 고유의 구독 및 제어에 있는 Azure 스케줄러 계획으로 이동한다는 사실을 참고합니다. 앱 서비스에 마이그레이션하는 이점과 마찬가지로 Azure 스케줄러의 모든 기능을 활용할 수 있습니다.

### <a name="why-host"></a>왜 앱 서비스에서 호스팅합니까?

앱 서비스는 응용 프로그램에 대해 더 풍부한 기능의 호스팅 환경을 제공합니다. 앱 서비스에서 호스팅하여 서비스 슬롯, 사용자 지정 도메인, 트래픽 관리자 지원 및 기타에 대해 액세스할 수 있습니다. 모바일 앱 백 엔드에 모바일 서비스를 업그레이드하는 동안 앱 서비스에 마이그레이션하면 일부 고객은 SDK 업데이트를 수행하지 않고 이러한 기능을 즉시 활용하려고 할 수 있습니다.

앱 서비스의 이점에 대한 자세한 내용은 [모바일 서비스 vs. 앱 서비스]를 참조하세요.

## 마이그레이션과 업그레이드 비교

[AZURE.INCLUDE [app-service-mobile-migrate-vs-upgrade](../../includes/app-service-mobile-migrate-vs-upgrade.md)]

- Node.js 기반 서버 프로젝트의 경우 새로운 [모바일 앱 Node.js SDK](https://github.com/Azure/azure-mobile-apps-node)에서 다양한 새로운 기능을 제공합니다. 예를 들어 이제 로컬 개발 및 디버깅을 수행하고 0.10 이후의 모든 Node.js 버전을 사용할 수 있으며 원하는 Express.js 미들웨어로 사용자 지정할 수 있습니다.

- .NET 기반 서버 프로젝트의 경우 새로운 [모바일 앱 SDK NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/)는 NuGet 종속성에 대한 유연성을 더 많이 제공하고, 새로운 앱 서비스 인증 기능을 지원하며, MVC를 비롯한 모든 ASP.NET 프로젝트를 사용하여 작성합니다. 업그레이드에 대한 자세한 내용은 [기존 .NET 모바일 서비스를 앱 서비스로 업그레이드](app-service-mobile-net-upgrading-from-mobile-services.md)를 참조하세요.


## <a name="understand"></a>앱 서비스 모바일 앱 이해

앱 서비스의 모바일 앱은 Azure를 사용하여 모바일 응용 프로그램을 작성하는 새로운 방법입니다. [모바일 앱 정의]에서 모바일 앱에 대한 자세한 내용을 알아볼 수 있습니다.

모바일 앱으로 마이그레이션하는 경우 비즈니스 논리를 포함하는 모든 기존 앱 기능을 보존할 수 있습니다. 또한 응용 프로그램에서 새로운 기능을 사용할 수 있습니다. 모바일 앱 모델에서 코드는 실제로 앱 서비스의 웹앱에서 실행되며 이는 새 버전의 Azure 웹 사이트입니다. 웹앱과 웹앱 작동 방식을 완전히 제어할 수 있습니다. 뿐만 아니라 이전에 모바일 서비스 고객이 사용할 수 없었던 웹앱 기능(예: 트래픽 관리자 및 개발 슬롯)을 이제 사용할 수 있습니다.

업그레이드의 주요 제한 사항은 모바일 서비스가 예약된 작업이 통합되지 않고 Azure 스케줄러가 사용자 지정 API을 호출하도록 설정되거나 웹 작업 기능이 사용되도록 설정되어야 합니다.

## 앱 서비스 마법사에 마이그레이션을 통해 모바일 서비스 마이그레이션

앱 서비스 마법사에 마이그레이션을 사용하여 모바일 서비스를 앱 서비스로 마이그레이션하는 간단하고 권장되는 방법이며 이는 Azure 클래식 포털에서 사용할 수 있습니다. [Azure 클래식 포털]로 이동하고 모바일 서비스를 찾아서 마이그레이션할 모바일 서비스를 선택합니다. 화면 맨 아래에서 모바일 서비스를 마이그레이션하는 과정을 안내하는 **앱 서비스 마이그레이션** 단추가 표시됩니다.

### <a name="what-gets-migrated"></a>마이그레이션되는 항목은 무엇입니까?

마이그레이션 마법사는 모바일 서비스에서 관리되다가 앱 서비스에서 관리되는 모바일 서비스를 호스팅하는 서버 팜을 변경합니다. 또한 모바일 서비스가 Microsoft가 관리하는 구독에서 구독으로 관리되는 스케줄러 계획을 이동합니다. 서버 팜이 앱 서비스의 관리 제어를 통해 전송될 때 해당 서버 팜에 연결된 모든 모바일 서비스는 동시에 이동됩니다.

모바일 서비스는 하위 지역 및 SKU에 따라 서버 팜으로 모바일 서비스를 구성합니다.(무료, 기본, 표준) 하위 지역의 모든 무료 서비스는 동일한 서버 팜에 위치하며 함께 마이그레이션합니다. 하위 지역에서 특정 수의 기본 서비스는 동일한 팜에 함께 호스팅되지만 많은 수의 기본 서비스가 있는 경우 별도의 계획에 위치합니다. 각 표준 서비스는 자체 서버 팜에 위치합니다. 단일 서비스를 마이그레이션하거나 서비스가 다른 사이트를 통해 이동하는 것을 방지하려면 기본으로 업그레이드할 수 있고 고유의 독립적인 계획에 둡니다.

또한 예약된 작업을 사용하는 경우 Azure 스케줄러 계획을 구독에 추가해야 합니다. 구독별로 허용된 무료 예약된 작업의 수가 제한되므로 게시 구독을 검토하려는 것과 연관된 비용이 있을 수 있습니다.

###  마이그레이션 마법사 사용

1. [Azure 클래식 포털]로 이동하여 모바일 서비스를 클릭합니다. 

2. 모바일 서비스를 마이그레이션하도록 선택한 다음 아래쪽 메뉴 모음에서 "앱 서비스 마이그레이션"을 클릭합니다. 마이그레이션되는 모바일 서비스의 목록을 팝업 창으로 나타냅니다. 표시되는 서비스의 종류 및 원인에 대한 자세한 내용은 [이전 섹션](#what-gets-migrated)을 참조하세요.

3. 모바일 서비스의 이름을 입력하여 마이그레이션을 확인한 다음 계속하려면 확인 표시를 클릭합니다. 여러 모바일 서비스가 마이그레이션에 포함되는 경우 선택된 원래 서비스의 이름을 입력해야 합니다.

4. 마이그레이션이 시작된 후에 [Azure 클래식 포털]의 모바일 서비스 목록에서 현재 상태를 볼 수 있습니다. 현재 마이그레이션되는 모든 서비스는 "마이그레이션"으로 표시됩니다. 모든 마이그레이션이 완료되었다고 표시되면 모바일 앱의 [Azure 포털]에서 볼 수 있습니다. 마이그레이션하는 동안 및 이후에 모바일 서비스는 계속 작동하고 URL은 변경되지 않습니다.

## 모바일 서비스 .NET 백 엔드에 대한 수동 마이그레이션

>[AZURE.NOTE]모바일 서비스를 마이그레이션하는 권장되는 방법은 이전 섹션에 설명된 앱 서비스 마법사에 마이그레이션을 통한 것입니다. 수동 마이그레이션은 마법사를 사용할 수 없는 상황에만 사용되어야 합니다.

이 섹션에서는 Azure 앱 서비스 내에서 .NET 모바일 서비스 프로젝트를 호스트하는 방법을 보여 줍니다. 앱 서비스 인스턴스의 도메인과 azure-mobile.net 도메인을 사용하여 URL을 대체하는데 필요하지만 이러한 방식으로 호스팅되는 앱은 모든 *모바일 서비스* .NET 런타임 자습서를 사용할 수 있습니다.

또한 모바일 서비스 프로젝트는 [앱 서비스 환경]에서 호스팅될 수 있습니다. 이 항목의 내용은 모두 적용되지만 사이트는 `contoso.azurewebsites.net` 대신 `contoso.contosoase.p.azurewebsites.net` 양식을 가집니다.

>[AZURE.NOTE]수동 마이그레이션을 수행하는 경우 기존의 **service.azure-mobile.net** URL을 보존할 수 *없습니다*. 이 URL에 연결하는 모바일 클라이언트가 있는 경우 자동 마이그레이션 옵션을 사용하거나 모든 모바일 클라이언트가 새 URL로 업그레이드될 때까지 모바일 서비스를 계속 실행해야 합니다.


### <a name="app-settings"></a>응용 프로그램 설정
모바일 서비스에는 환경에서 사용 가능한 여러 응용 프로그램 설정이 필요합니다. 이에 대해서는 이 섹션에 설명되어 있습니다.

모바일 앱 백 엔드의 응용 프로그램을 설정하기 위해 먼저 [Azure 포털]에 로그인합니다. 모바일 앱 백 엔드로 사용하려는 앱 서비스 앱으로 이동하고 **설정** > **응용 프로그램 설정**을 클릭한 다음 **앱 설정**까지 아래로 스크롤합니다. 여기서 다음과 같은 필수 키-값 쌍을 설정할 수 있습니다.

+ **MS\_MobileServiceName**는 앱의 이름으로 설정해야 합니다. 예를 들어 백 엔드의 URL가 `contoso.azurewebsites.net`인 경우 *contoso*은 올바른 값입니다.

+ **MS\_MobileServiceDomainSuffix**는 웹앱의 이름으로 설정해야 합니다. 예를 들어 백 엔드의 URL가 `contoso.azurewebsites.net`인 경우 *azurewebsites.net*은 올바른 값입니다.

+ **MS\_ApplicationKey**는 임의의 값으로 설정할 수 있지만 클라이언트 SDK에서 사용되는 동일한 값이어야 합니다. GUID는 사용하는 것이 좋습니다.

+ **MS\_MasterKey**는 임의의 값으로 설정할 수 있지만 API/클라이언트에서 사용되는 동일한 값이어야 합니다. GUID는 사용하는 것이 좋습니다.

모바일 서비스를 마이그레이션할 때 마스터 키와 응용 프로그램 키는 모두 [Azure 클래식 포털]의 모바일 서비스 섹션에 있는 **구성** 탭에서 가져올 수 있습니다. 맨 아래에서 **키 관리**를 클릭하고 키를 복사합니다.


### <a name="client-sdk"></a>방법: 클라이언트 SDK 수정

클라이언트 앱 프로젝트에서 모바일 서비스 클라이언트 개체의 생성자를 수정하여 새로운 앱 URL(예: `https://contoso.azurewebsites.net`) 및 이전에 구성한 응용 프로그램 키를 적용합니다. 클라이언트 SDK 버전은 **모바일 서비스** 버전이어야 하고 업그레이드되지 **않아야** 합니다. iOS 및 Android 클라이언트의 경우 2.x 버전을 사용하고 Windows/Xamarin의 경우 1.3.2를 사용합니다. Javascript 클라이언트는 1.2.7을 사용해야 합니다.

### <a name="data"></a>방법: 데이터 기능 활성화

모바일 서비스에서 기본 Entity Framework 클래스로 작업하려면 두 가지 추가 설정이 필요합니다.

연결 문자열은 **앱 설정** 섹션 바로 아래에서 응용 프로그램 설정 블레이드의 **연결 문자열** 섹션에 저장됩니다. 데이터베이스에 대한 연결 문자열은 **MS\_TableConnectionString** 키에서 설정되어야 합니다. 기존 모바일 서비스를 앱 서비스에 마이그레이션할 경우 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 모바일 서비스 **구성** 탭의 **연결 문자열** 섹션으로 이동합니다. **연결 문자열 표시**를 클릭하고 값을 복사합니다.

기본적으로 사용할 스키마는 **MS\_MobileServiceName**이지만 **MS\_TableSchema** 설정을 사용하여 덮어쓰여질 수 있습니다. 다시 **앱 설정**에서 **MS\_TableSchema**을 사용할 스키마의 이름으로 설정합니다. 기존 모바일 서비스 응용 프로그램을 통해 이동하는 경우 스키마는 Entity Framework를 사용하여 만들어집니다. 이것은 코드를 호스팅하는 앱 서비스 인스턴스가 아니라 모바일 서비스의 이름입니다.

### <a name="push"></a>방법: 푸시 기능 활성화

또한 작동할 푸시의 경우 웹앱은 알림 허브에 대한 정보를 알고 있어야 합니다.

**연결 문자열**에서 **MS\_NotificationHubConnectionString**을 알림 허브의 DefaultFullSharedAccessSignature 연결 문자열을 사용하여 설정합니다. 기존 모바일 서비스를 마이그레이션하려면 [Azure 클래식 포털](https://manage.windowsazure.com/)에서 모바일 서비스 **구성** 탭의 **연결 문자열** 섹션으로 이동합니다. **연결 문자열 표시**를 클릭하고 값을 복사합니다.

**MS\_NotificationHubName** 앱 설정은 허브의 이름으로 설정되어야 합니다. 기존 모바일 서비스를 이동할 때 [Azure 클래식 포털](https://manage.windowsazure.com/)에 있는 모바일 서비스의 **푸시** 탭에서 이 값을 가져올 수 있습니다. 이 탭에서 다른 필드는 자체 허브에 연결되고 어디에도 복사할 필요가 없습니다.

### <a name="auth"></a>방법: 인증 기능 활성화

또한 ID 기능에는 개별 공급자에 필요한 앱 설정을 요구 사항이 있습니다. 기존 모바일 서비스로 이동하는 경우 Azure 클래식 포털의 **ID** 탭에 있는 각각의 필드에는 해당하는 앱 설정이 있습니다.

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

* **MS\_AadTenants** - 참고: **MS\_AadTenants**는 테넌트 도메인의 쉼표로 구분된 목록으로 저장됩니다.(Azure 클래식 포털에서 **허용된 테넌트** 필드)

### <a name="publish"></a>방법: 모바일 서비스 프로젝트 게시

1. [Azure 포털]에서 앱으로 이동하고 명령 모음에서 **게시 프로필 가져오기**를 클릭하며 로컬 컴퓨터에 다운로드한 프로필을 저장합니다.
2. Visual Studio에서 모바일 서비스 서버 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **게시**를 클릭합니다. 
3. 프로필 탭에서 **가져오기**를 선택하고 다운로드한 프로필을 찾습니다.
3. **게시**를 클릭하여 앱 서비스에 코드를 배포합니다.

로그는 표준 앱 서비스 로깅 기능을 통해 처리됩니다. 로깅에 대해 자세히 알아보려면 [Azure 앱 서비스에서 진단 로깅을 사용]을 참조하세요.



<!-- URLs. -->

[Azure 포털]: https://portal.azure.com/
[Azure 클래식 포털]: https://manage.windowsazure.com/
[모바일 앱 정의]: app-service-mobile-value-prop.md
[I already use web sites and mobile services – how does App Service help me?]: /ko-KR/documentation/articles/app-service-mobile-value-prop-migration-from-mobile-services
[Mobile App Server SDK]: http://www.nuget.org/packages/microsoft.azure.mobile.server
[Create a Mobile App]: app-service-mobile-xamarin-ios-get-started.md
[Add push notifications to your mobile app]: app-service-mobile-xamarin-ios-get-started-push.md
[Add authentication to your mobile app]: app-service-mobile-xamarin-ios-get-started-users.md
[Azure Scheduler]: /ko-KR/documentation/services/scheduler/
[Web Job]: ../app-service-web/websites-webjobs-resources.md
[How to use the .NET server SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md


[Azure 앱 서비스에서 진단 로깅을 사용]: web-sites-enable-diagnostic-log.md
[앱 서비스 가격 책정]: https://azure.microsoft.com/ko-KR/pricing/details/app-service/
[앱 서비스 환경]: app-service-app-service-environment-intro.md
[모바일 서비스 vs. 앱 서비스]: app-service-mobile-value-prop-migration-from-mobile-services-preview.md
[migrate a mobile service to a mobile app on App Service]: app-service-mobile-migrating-from-mobile-services.md

<!---HONumber=AcomDC_1210_2015-->