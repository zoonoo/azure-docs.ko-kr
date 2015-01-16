<properties pageTitle="iOS 앱에 대해 Azure 모바일 서비스 시작" metaKeywords="Azure iOS application, mobile service iOS, getting started Azure iOS" description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 iOS 개발을 시작할 수 있습니다. " metaCanonical="" services="" documentationCenter="Mobile" title="Get started with Mobile Services" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="hero-article" ms.date="10/10/2014" ms.author="krisragh" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>이 자습서는 Azure 모바일 서비스를 사용하여 iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다.</p>
<p>동영상을 시청하려는 경우 이 자습서와 동일한 단계를 설명하는 오른쪽에 있는 동영상 클립을 확인하세요.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="label">자습서 보기</a> <a style="background-image: url('/media/devcenter/mobile/videos/get-started-ios-180x120.png') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/iOS-Creating-your-first-app-using-the-Windows-Azure-Mobile-Services-Quickstart" target="_blank" class="dev-onpage-video"><span class="icon">동영상 재생</span></a> <span class="time">09:38:00</span></div>
</div>

이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 _할 일 모음_ 앱을 둘 다 만듭니다. 만드는 모바일 서비스는 서버 쪽 비즈니스 논리에 JavaScript를 사용합니다. Visual Studio를 사용하여 지원되는 .NET 언어로 서버 쪽 비즈니스 논리를 작성할 수 있게 해 주는 모바일 서비스를 만들려면 이 토픽의 [.NET 백 엔드 버전]을 참조하세요.

완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 XCode 4.5 및 iOS 5.0 이상 버전이 필요합니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-ios%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>새 iOS 앱 만들기</h2>

모바일 서비스를 만든 후 관리 포털에서 쉬운 빠른 시작을 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다.

이 섹션에서는 모바일 서비스에 연결된 새 iOS 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 빠른 시작 탭에서 **플랫폼 선택** 아래의 **iOS**를 클릭하고 **새 iOS 앱 만들기**를 확장합니다.

   	![][6]

   	This displays the three easy steps to create an iOS app connected to your mobile service.

  	![][7]

3. 아직 하지 않은 경우 [Xcode] v4.4 이상 버전을 다운로드하여 설치합니다.

4. **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **앱 다운로드 및 실행** 아래에서 **다운로드**를 클릭합니다.

  	모바일 서비스 iOS SDK와 함께 모바일 서비스에 연결된 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

## 새 iOS 앱 실행

[WACOM.INCLUDE [mobile-services-ios-run-app](../includes/mobile-services-ios-run-app.md)]

<ol start="4">
<li><p>관리 포털로 돌아가서 <strong>데이터</strong> 탭을 클릭한 후 <strong>TodoItem</strong> 테이블을 클릭합니다.<p></li></ol>

![](./media/mobile-services-ios-get-started/mobile-data-tab.png)

이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.</p>

![](./media/mobile-services-ios-get-started/mobile-data-browse.png)


## <a name="next-steps"> </a>다음 단계
이제 빠른 시작을 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다.

* [데이터 작업 시작]
  <br/>모바일 서비스를 사용하여 데이터를 저장하고 쿼리하는 방법을 알아봅니다.

* [오프라인 데이터 동기화 시작]
  <br/>오프라인 데이터 동기화를 사용하여 앱의 응답성 및 성능을 강화합니다.

* [인증 시작]
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [푸시 알림 시작]
  <br/>매우 기본적인 푸시 알림을 앱에 보내는 방법을 알아봅니다.


<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[모바일 서비스 인스턴스 정의]:#define-mobile-service-instance
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-ios-get-started/mobile-quickstart-completed-ios.png

[6]: ./media/mobile-services-ios-get-started/mobile-portal-quickstart-ios.png
[7]: ./media/mobile-services-ios-get-started/mobile-quickstart-steps-ios.png
[8]: ./media/mobile-services-ios-get-started/mobile-xcode-project.png

[10]: ./media/mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[데이터 작업 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-data/
[인증 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-users/
[오프라인 데이터 동기화 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-offline-data
[푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/

[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[관리 포털]: https://manage.windowsazure.com/
[XCode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[.NET 백 엔드 버전]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
