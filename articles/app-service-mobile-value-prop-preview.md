<properties
	pageTitle="모바일 앱 정의"
	description="앱 서비스가 엔터프라이즈 모바일 앱에 제공하는 이점을 알아봅니다."
	services="app-service\mobile"
	documentationCenter=""
	authors="kirillg"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="02/20/2015"
	ms.author="kirillg"/>

# <a name="getting-started"> </a>모바일 앱 정의

>[AZURE.NOTE]**모바일 서비스 설명서는 [여기](http://azure.microsoft.com/documentation/services/mobile-services/)를 참조하세요.**

Azure 앱 서비스는 전문적 개발자를 위한 완전히 관리되는 PaaS(Platform as a Service) 제품으로, 웹, 모바일 및 통합 시나리오에 풍부한 기능 집합을 제공합니다. *Azure 앱 서비스*의 *모바일 앱*은 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 응용 프로그램 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 앱에 풍부한 기능 집합을 제공합니다.

![모바일 앱](./media/app-service-mobile-value-prop-preview/overview.png)

## 모바일 앱 사용 이유
*Azure 앱 서비스*의 *모바일 앱*은 엔터프라이즈 개발자 및 시스템 통합자를 위해 확장성이 크고 전 세계에서 사용 가능한 모바일 응용 프로그램 개발 플랫폼을 제공합니다. 이 플랫폼은 모바일 앱에 풍부한 기능 집합을 제공합니다. 모바일 앱을 통해 다음을 수행할 수 있습니다.

- **네이티브 앱 및 크로스 플랫폼 앱 빌드** - 빌드하는 앱이 네이티브 iOS, Android 및 Windows 앱인지 또는 크로스 플랫폼 Xamarin 앱이나 Cordova(Phonegap) 앱인지에 관계없이 네이티브 SDK를 통해 앱 서비스를 이용할 수 있습니다.  
- **엔터프라이즈 시스템에 연결** - 모바일 앱을 사용하면 몇 분 내에 회사 로그온을 추가하고 엔터프라이즈 온-프레미스 또는 클라우드 리소스에 연결할 수 있습니다.
- **쉽게 SaaS API에 연결** - 40개 이상의 SaaS API 커넥터를 통해 현재 엔터프라이즈에서 사용하는 SaaS API와 앱을 쉽게 통합할 수 있습니다. CRM과 청구 시스템 둘 다에서 계정 상태를 업데이트하고 싶으세요? 모바일 앱을 통해 엔터프라이즈 SaaS API를 원하는 대로 제어할 수 있습니다.
- **동기화를 사용하여 오프라인 준비 앱 빌드** - 오프라인에서 작동하며 연결이 있을 경우 백그라운드에서 엔터프라이즈 데이터 원본이나 SasS API와 데이터를 동기화하는 앱을 빌드하여 모바일 직원의 생산성을 높입니다.
- **몇 초 내에 수백만 명에게 푸시 알림 전송** - 요구에 맞게 개인 설정되고 적절한 시간에 전송되는 인스턴트 푸시 알림을 모든 장치에서 지원하여 고객을 관리합니다.

## 모바일 앱 개념
- **Single Sign On** - Azure Active Directory, Facebook, Google, Twitter 및 Microsoft 계정을 포함하여 점점 증가하는 ID 공급자 목록에서 선택한 다음 모바일 앱을 활용하여 몇 분 내에 인증을 앱에 추가합니다.
- **오프라인 동기화** - 모바일 앱을 사용하면 연결을 사용할 수 없는 경우 직원들이 오프라인에서 작업하고 장치가 다시 온라인 상태가 되면 엔터프라이즈 백 엔드와 동기화할 수 있게 해주는 강력하고 응답성이 우수한 앱을 쉽게 빌드할 수 있습니다. 오프라인 동기화 기능은 모든 클라이언트 플랫폼에서 지원되며 SQL, 테이블 저장소, Mongo 또는 문서 DB를 비롯한 모든 데이터 원본과 Office 365, Salesforce, Dynamics 또는 온-프레미스 데이터베이스를 비롯한 모든 SaaS API에서 작동합니다. 
- **푸시 알림** - 모바일 앱은 iOS, Android, Windows 또는 Kindle 장치를 사용하는 동적 고객층에게 개인 설정된 수백만 개의 푸시 알림을 몇 초 내에 전송할 수 있는 고확장성 모바일 푸시 알림 엔진인 알림 허브를 제공합니다. 백 엔드가 온-프레미스에서 호스트되는지 또는 클라우드에서 호스트되는지에 관계없이 기존의 모든 앱 백 엔드에 알림 허브를 쉽게 연결할 수 있습니다.
- **자동 크기 조정** - 앱 서비스를 사용하면 들어오는 고객 부하를 처리하기 위해 신속하게 확장하거나 축소할 수 있습니다. VM 수와 크기를 수동으로 선택하거나 부하 또는 일정에 따라 모바일 앱 백 엔드의 크기를 조정하도록 자동 크기 조정을 설정합니다.

## 시작하기 ##
모바일 앱을 시작하려면 [시작] 자습서를 따르세요.

Azure 앱 서비스 플랫폼에 대한 자세한 내용은 [Azure 앱 서비스]를 참조하세요.


<!-- URLs. -->
[Migrate your Mobile Service to App Service]: app-service-mobile-dotnet-backend-migrating-from-mobile-services-preview.md

[Azure 앱 서비스]: app-service-value-prop-what-is.md
[시작]: app-service-mobile-ios-get-started-offline-data-preview.md

[Management Portal]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript backend version]: mobile-services-ios-get-started.md

<!--HONumber=52-->
