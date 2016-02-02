<properties
	pageTitle="iOS 앱용 Azure 모바일 서비스 시작 | JavaScript 백 엔드"
	description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 iOS 개발을 시작할 수 있습니다."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="hero-article"
	ms.date="11/04/2015"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

[AZURE.INCLUDE [mobile-services-hero-slug](../../includes/mobile-services-hero-slug.md)]


이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다.

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. .NET에서 서버 쪽 비즈니스 논리로 모바일 서비스를 만들려면 이 항목의 [.NET 백 엔드 버전]을 참조하세요.

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 [평가판 사용 기간이 끝난 후에도 계속 사용할 수 있는 무료 모바일 서비스](https://azure.microsoft.com/pricing/details/mobile-services/)를 사용할 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20)을 참조하세요.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## 새 iOS 앱 만들기

Azure 클래식 포털에서 쉬운 빠른 시작을 수행하여 모바일 서비스에 연결된 새 앱을 만들 수 있습니다.

1. [Azure 클래식 포털]에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다. 모바일 서비스에 연결된 iOS 앱을 만드는 단계가 표시됩니다.

3. **TodoItem 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

4. **Download and run your app** 아래에서 **다운로드**를 클릭합니다. 모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 iOS 앱 실행

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]

<ol start="4"> <li><p>[Azure 클래식 포털]에 돌아와서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다. 이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.<p></li></ol></p>

## <a name="next-steps"> </a>다음 단계
모바일 서비스에서 중요한 추가 작업을 수행하는 방법에 대해 알아봅니다.

* [오프라인 데이터 동기화 시작] <br/>오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 알아봅니다.

* [기존 앱에 인증 추가] <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [기존 앱에 푸시 알림 추가] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]


<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[오프라인 데이터 동기화 시작]: mobile-services-ios-get-started-offline-data.md
[기존 앱에 인증 추가]: mobile-services-dotnet-backend-ios-get-started-users.md
[기존 앱에 푸시 알림 추가]: mobile-services-dotnet-backend-ios-get-started-push.md


[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Azure 클래식 포털]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET 백 엔드 버전]: mobile-services-dotnet-backend-ios-get-started.md

<!---HONumber=AcomDC_0128_2016-->