<properties pageTitle="iOS 앱에 대해 Azure 모바일 서비스 시작" description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 iOS 개발을 시작할 수 있습니다." services="" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# <a name="getting-started"> </a>모바일 서비스 시작

[AZURE.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 생성되는 모바일 서비스에서는 Visual Studio에서 지원되는 .NET 언어를 서버 쪽 비즈니스 논리와 모바일 서비스 관리에 사용합니다. JavaScript에서 서버 쪽 비즈니스 논리를 작성하는 데 사용할 수 있는 모바일 서비스를 만들려면 이 항목의 [JavaScript 백 엔드 버전]을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 XCode 4.5 및 iOS 5.0 이상 버전이 필요합니다.

> [AZURE.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F%20target="_blank")을 참조하세요.

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## 로컬 컴퓨터에 모바일 서비스 다운로드

모바일 서비스를 만들었으므로 이제 로컬 컴퓨터 또는 가상 컴퓨터에서 실행할 수 있는 개인 설정된 모바일 서비스 프로젝트를 다운로드합니다.

1. 방금 만든 모바일 서비스를 클릭하고 퀵 스타트 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

	![][1]

2. 아직 하지 않은 경우 Visual Studio Professional 2013 이상 버전을 다운로드하여 설치합니다.

3. **서비스를 다운로드하고 클라우드에 게시** 아래의 **다운로드**를 클릭합니다.

	모바일 서비스를 구현하는 Visual Studio 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

4. 또한 게시 프로필을 다운로드하고, 다운로드한 파일을 로컬 컴퓨터에 저장한 다음 저장 위치를 기록해 둡니다.

## 모바일 서비스 테스트

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## 모바일 서비스 게시

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## 새 iOS 앱 만들기

이 섹션에서는 모바일 서비스에 연결된 새 iOS 앱을 만듭니다.

1. 관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 퀵 스타트 탭에서 **플랫폼 선택** 아래의**iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

3. 아직 하지 않은 경우 [Xcode] v4.4 이상 버전을 다운로드하여 설치합니다.

4. **앱 다운로드 및 실행** 아래에서 **다운로드**를 클릭합니다.

  	모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 _To do list_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 iOS 앱 실행

[AZURE.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

Azure에서 실행 중인 모바일 서비스에 대해 새 클라이언트 앱을 실행하는 방법을 보여 줍니다. 로컬 컴퓨터에서 실행 중인 모바일 서비스로 iOS 앱을 테스트하려면 먼저 iOS 개발 컴퓨터에서 액세스할 수 있도록 웹 서버와 방화벽을 구성해야 합니다. 자세한 내용은 [로컬 모바일 서비스에 연결할 수 있도록 로컬 웹 서버 구성](/ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-configure-iis-express).을 참조하세요.

## <a name="next-steps"> </a>다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* [데이터 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [오프라인 데이터 동기화 시작]
  <br/>오프라인 데이터 동기화를 활용하여 앱의 응답성과 견고성을 향상하는 방법을 알아봅니다.

* [인증 시작 (영문)]
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작]
  <br/>앱에 기본적인 푸시 알림을 보내는 방법을 알아봅니다.

* [모바일 서비스 .NET 백 엔드 문제 해결]
  <br/> 모바일 서비스 .NET 백 엔드에서 발생할 수 있는 문제를 진단하고 해결하는 방법에 알아봅니다.

<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[모바일 서비스 인스턴스 정의]:#define-mobile-service-instance
[다음 단계]:#next-steps

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
[데이터 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[오프라인 데이터 동기화 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-offline-data
[인증 시작 (영문)]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push
[모바일 서비스 .NET 백 엔드 문제 해결]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-how-to-troubleshoot/

[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[관리 포털]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[JavaScript 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-ios-get-started


<!--HONumber=42-->
