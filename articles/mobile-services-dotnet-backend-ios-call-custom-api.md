<properties
	pageTitle="iOS 클라이언트에서 사용자 지정 API를 호출하는 방법"
	description="사용자 지정 API를 정의한 다음 Azure 모바일 서비스를 사용하는 iOS 앱에서 이를 호출하는 방법에 대해 알아봅니다."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	writer="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/03/2015"
	ms.author="krisragh"/>


# iOS 클라이언트에서 사용자 지정 API를 호출하는 방법(.NET 백 엔드)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../includes/mobile-services-selector-call-custom-api.md)]

이 항목에서는 iOS 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API를 사용하면 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능이 있는 사용자 지정 끝점을 정의할 수 있습니다. 사용자 지정 API를 사용하여 HTTP 헤더 및 본문 형식을 비롯한 메시지를 보다 효과적으로 제어할 수 있습니다.

## <a name="define-custom-api"></a>사용자 지정 API 정의

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../includes/mobile-services-ios-call-custom-api.md)]

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Get started with Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Mobile Services Quick Start]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with data]: mobile-services-dotnet-backend-ios-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Store server scripts in source control]: mobile-services-store-scripts-source-control.md

<!--HONumber=54-->