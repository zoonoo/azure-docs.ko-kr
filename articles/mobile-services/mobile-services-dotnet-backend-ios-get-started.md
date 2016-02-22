<properties
	pageTitle="iOS 앱에 대해 Azure 모바일 서비스 시작"
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
	ms.topic="article"
	ms.date="02/07/2016"
	ms.author="krisragh"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;


[AZURE.INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]&nbsp;

>[AZURE.TIP] Microsoft Azure를 사용하는 모바일 개발이 처음이라면 Azure 모바일 서비스 대신 [Azure 모바일 앱부터 시작](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)하세요. 모바일 앱이 [추가적인 이점](../app-service-mobile/app-service-mobile-value-prop-migration-from-mobile-services.md)을 제공합니다.

이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 모바일 서비스는 서버 쪽 비즈니스 논리에 .NET 및 Visual Studio를 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리로 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전]을 참조하세요.

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 [평가판 사용 기간이 끝난 후에도 계속 사용할 수 있는 무료 모바일 서비스](https://azure.microsoft.com/pricing/details/mobile-services/)를 사용할 수 있습니다. 자세한 내용은 [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-ios-get-started%2F)을 참조하세요.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 로컬 컴퓨터에 모바일 서비스 및 앱 다운로드

이제 모바일 서비스를 만들었습니다. 로컬로 실행할 수 있는 프로젝트를 다운로드합니다.

1. 방금 만든 모바일 서비스를 클릭하고 빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

2. Windows PC에서 **서비스를 다운로드하고 클라우드에 게시** 아래의 **다운로드**를 클릭합니다. 모바일 서비스를 구현하는 Visual Studio 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

3. Mac에서 **앱 다운로드 및 실행** 아래의 **다운로드**를 클릭합니다. 모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 모바일 서비스 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]


## 새 iOS 앱 실행

[AZURE.INCLUDE [mobile-services-ios-run-app](../../includes/mobile-services-ios-run-app.md)]


## <a name="next-steps"> </a>다음 단계

Azure에서 실행 중인 모바일 서비스에 대해 새 클라이언트 앱을 실행하는 방법을 보여 줍니다. 로컬 컴퓨터에서 실행 중인 모바일 서비스로 iOS 앱을 테스트하려면 먼저 iOS 개발 컴퓨터에서 액세스할 수 있도록 웹 서버와 방화벽을 구성해야 합니다. 자세한 내용은 [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성](mobile-services-dotnet-backend-how-to-configure-iis-express.md)을 참조하십시오.

모바일 서비스에서 중요한 추가 작업을 수행하는 방법에 대해 알아봅니다.

* [오프라인 데이터 동기화 시작] <br/>오프라인 데이터 동기화를 활용하여 응답성과 견고성이 뛰어난 앱을 제작하는 방법을 알아봅니다.

* [기존 앱에 인증 추가] <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [기존 앱에 푸시 알림 추가] <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 백 엔드 문제 해결] <br/> 모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단 및 해결하는 방법을 알아봅니다.

[AZURE.INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-completed-ios.png
[1]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-vs.png

[6]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-dotnet-backend-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[오프라인 데이터 동기화 시작]: mobile-services-ios-get-started-offline-data.md
[기존 앱에 인증 추가]: mobile-services-dotnet-backend-ios-get-started-users.md
[기존 앱에 푸시 알림 추가]: mobile-services-dotnet-backend-ios-get-started-push.md
[모바일 서비스 .NET 백 엔드 문제 해결]: mobile-services-dotnet-backend-how-to-troubleshoot.md
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript 백 엔드 버전]: mobile-services-ios-get-started.md

<!---HONumber=AcomDC_0211_2016-->