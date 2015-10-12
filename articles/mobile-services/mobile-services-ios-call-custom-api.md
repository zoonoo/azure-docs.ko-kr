<properties
	pageTitle="iOS 클라이언트에서 사용자 지정 API를 호출하는 방법(JavaScript 백 엔드)"
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
	ms.date="09/28/2015"
	ms.author="krisragh"/>

# iOS 클라이언트에서 사용자 지정 API를 호출하는 방법(JavaScript 백 엔드)

[AZURE.INCLUDE [mobile-services-selector-call-custom-api](../../includes/mobile-services-selector-call-custom-api.md)]

이 항목에서는 iOS 앱에서 사용자 지정 API를 호출하는 방법을 보여 줍니다. 사용자 지정 API를 사용하면 삽입, 업데이트, 삭제 또는 읽기 작업에 매핑되지 않는 서버 기능이 있는 사용자 지정 끝점을 정의할 수 있습니다. 사용자 지정 API를 사용하여 HTTP 헤더 및 본문 형식을 비롯한 메시지를 보다 효과적으로 제어할 수 있습니다.

## <a name="define-custom-api"></a>사용자 지정 API 정의

[AZURE.INCLUDE [mobile-services-create-custom-api](../../includes/mobile-services-create-custom-api.md)]

[AZURE.INCLUDE [mobile-services-ios-call-custom-api](../../includes/mobile-services-ios-call-custom-api.md)]

## 다음 단계

이 항목에서는 **invokeApi** 메서드를 사용하여 iOS 앱에서 매우 간단한 사용자 지정 API를 호출하는 방법을 보여 줍니다. **invokeApi** 메서드 사용에 대한 자세한 내용은 게시물 [Azure 모바일 서비스의 사용자 지정 API](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx)를 참조하세요.

다음 모바일 서비스 항목에 대해서도 자세히 알아보세요.

* [모바일 서비스 서버 스크립트 참조] <br/>사용자 지정 API 만들기에 대해 알아봅니다.

* [원본 제어에 서버 스크립트 저장] <br/> 원본 제어 기능을 사용하여 더 쉽고 안전하게 사용자 지정 API 스크립트 코드를 개발하고 게시하는 방법을 알아봅니다.

<!-- Anchors. -->
[Define the custom API]: #define-custom-api
[Update the app to call the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- URLs. -->
[Windows Push Notifications & Live Connect]: http://go.microsoft.com/fwlink/?LinkID=257677
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[My Apps dashboard]: http://go.microsoft.com/fwlink/?LinkId=262039
[Mobile Services Quick Start]: mobile-services-ios-get-started.md
[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Get started with authentication]: mobile-services-ios-get-started-users.md
[Get started with push notifications]: ../mobile-services-ios-get-started-push.md
[원본 제어에 서버 스크립트 저장]: mobile-services-store-scripts-source-control.md

<!---HONumber=Oct15_HO1-->