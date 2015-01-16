<properties pageTitle="Xamarin iOS 앱에 대해 모바일 서비스 시작" metaKeywords="" description="이 자습서에 따라 Azure 모바일 서비스를 사용하여 Xamarin iOS 개발을 시작할 수 있습니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with Mobile Services" authors="craigd" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="hero-article" ms.date="11/22/2014" ms.author="craig.dunn@xamarin.com" />

# <a name="getting-started"> </a>모바일 서비스 시작

[WACOM.INCLUDE [mobile-services-selector-get-started](../includes/mobile-services-selector-get-started.md)]

이 자습서에서는 Azure 모바일 서비스를 사용하여 Xamarin.iOS 앱에 클라우드 기반 백 엔드 서비스를 추가하는 방법을 보여 줍니다. 이 자습서에서는 새 모바일 서비스와 새 모바일 서비스에 앱 데이터를 저장하는 간단한 <em>To do list</em> 앱을 만듭니다.

동영상을 시청하려는 경우 아래쪽의 클립은 이 자습서와 동일한 단계를 따릅니다.

비디오: "Xamarin 및 Azure 모바일 서비스 시작". 진행: Craig Dunn, Xamarin 개발자(시간: 10:05분)

> [AZURE.VIDEO getting-started-with-xamarin-and-mobile-services]



완성된 앱의 스크린샷은 다음과 같습니다.

![][0]

이 자습서를 완료하려면 OS X용 XCode와 [Xamarin Studio] 또는 Windows의 Visual Studio용 Xamarin Visual Studio 플러그 인이 필요합니다. 샘플은 iOS 5.0 이상에서 실행됩니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 Azure 평가판을 등록하고 최대 10개의 무료 모바일 서비스를 사용할 수 있습니다. 이러한 서비스는 평가판 사용 기간이 끝난 후에도 계속 사용할 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-xamarin-ios%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <a name="create-new-service"> </a>새 모바일 서비스 만들기

[WACOM.INCLUDE [mobile-services-create-new-service](../includes/mobile-services-create-new-service.md)]

<h2>새 Xamarin.iOS 앱 만들기</h2>

모바일 서비스를 만든 후 관리 포털에서 쉬운 퀵 스타트를 따라 모바일 서비스에 연결할 새 앱을 만들거나 기존 앱을 수정할 수 있습니다. 

이 섹션에서는 모바일 서비스에 연결된 새 Xamarin.iOS 앱을 만듭니다.

1.  관리 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. 퀵 스타트 탭의 **플랫폼 선택** 아래에서 **Xamarin.iOS**를 클릭하고 **새 Xamarin.iOS 앱 만들기**를 확장합니다.

	![][6]

	그러면 모바일 서비스에 연결된 Xamarin.iOS 앱을 만들기 위한 쉬운 3단계가 표시됩니다.

  	![][7]

3. 아직 하지 않은 경우 Xcode(최신 버전인 Xcode 6.0 이상 권장) 및 [Xamarin Studio]를 다운로드하여 설치합니다.

4. **TodoItems 테이블 만들기**를 클릭하여 앱 데이터를 저장할 테이블을 만듭니다.

5. **앱 다운로드 및 실행**에서 **다운로드**를 클릭합니다. 

	그러면 모바일 서비스에 연결되고 Xamarin.iOS에 대한 Azure 모바일 서비스 구성 요소를 참조하는 샘플 _할 일 모음_ 응용 프로그램에 대한 프로젝트가 다운로드됩니다. 압축된 프로젝트 파일을 로컬 컴퓨터에 저장하고 저장 위치를 기록해 둡니다.

<h2>새 Xamarin.iOS 앱 실행</h2>

이 자습서의 최종 단계는 새 앱을 빌드하고 실행하는 것입니다.

1. 압축된 프로젝트 파일을 저장한 위치로 이동한 후 컴퓨터에서 파일 압축을 풀고 Xamarin Studio 또는 Visual Studio를 사용하여 **XamarinTodoQuickStart.iOS.sln** 솔루션 파일을 엽니다.

	![][8]

	![][9]

2. **실행** 단추를 눌러 프로젝트를 빌드하고 이 프로젝트의 기본값인 iPhone 에뮬레이터에서 앱을 시작합니다.

3. 앱에서 _Complete the tutorial_ 등의 의미 있는 텍스트를 입력한 후 더하기(**+**) 아이콘을 클릭합니다.

	![][10]

	Azure에 호스트된 새 모바일 서비스에 POST 요청이 전송됩니다. 요청에서 데이터가 TodoItem 테이블에 삽입됩니다. TodoItem 테이블에 저장된 항목이 모바일 서비스에서 반환된 후 데이터가 목록에 표시됩니다.

	<div class="dev-callout"> 
	<b>참고</b> 
   	<p>모바일 서비스에 액세스하여 데이터를 쿼리 및 삽입하는 코드를 검토할 수 있습니다. 이 코드는 TodoService.cs C# 파일에 있습니다.</p> 
 	</div>

4. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItems** 테이블을 클릭합니다.

	![][11]

	이를 통해 앱에서 테이블로 삽입된 데이터를 찾을 수 있습니다.

	![][12]

## <a name="next-steps"> </a>다음 단계
이제 퀵 스타트를 완료했으며 모바일 서비스에서 중요한 추가 작업을 수행하는 방법을 알아보겠습니다. 

* [데이터 시작](영문)
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보세요.

* [오프라인 데이터 동기화 시작]
  <br/>오프라인 데이터 동기화를 활용하여 앱의 응답성과 견고성을 향상하는 방법을 알아봅니다.

* [인증 시작](영문)
  <br/>ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [푸시 알림 시작] 
  <br/>기본적인 푸시 알림을 앱에 보내는 방법을 알아보세요.


<!-- Anchors. -->
[모바일 서비스 시작]:#getting-started
[새 모바일 서비스 만들기]:#create-new-service
[모바일 서비스 인스턴스 정의]:#define-mobile-service-instance
[다음 단계]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[데이터 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[오프라인 데이터 동기화 시작]: /ko-kr/develop/mobile/tutorials/mobile-services-xamarin-ios-get-started-offline-data
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Xamarin Studio](영문): http://xamarin.com/download
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[관리 포털]: https://manage.windowsazure.com/

<!--HONumber=35.1-->
