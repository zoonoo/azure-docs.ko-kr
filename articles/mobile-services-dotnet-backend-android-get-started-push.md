<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# 모바일 서비스에서 푸시 알림 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 퀵 스타트 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

이 자습서에서는 다음 단계를 안내합니다.

1.  [Google Cloud Messaging 사용][Google Cloud Messaging 사용]
2.  [푸시 요청을 전송하도록 모바일 서비스 구성][푸시 요청을 전송하도록 모바일 서비스 구성]
3.  [푸시 알림을 전송하도록 서버 업데이트][푸시 알림을 전송하도록 서버 업데이트]
4.  [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
5.  [로컬 테스트에 푸시 알림 사용][로컬 테스트에 푸시 알림 사용]
6.  [게시된 모바일 서비스에 대해 앱 테스트][게시된 모바일 서비스에 대해 앱 테스트]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기] 또는 [데이터 시작하기][데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다. 따라서 이 자습서를 사용하려면 Visual Studio 2013이 있어야 합니다.

> [WACOM.NOTE] 이 자습서를 완료하려면 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판][Azure 무료 평가판]을 참조하세요.

## <span id="register"></span></a>Google Cloud Messaging 사용

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <span id="configure"></span></a>푸시 요청을 보내도록 모바일 서비스 구성

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][0]

2.  **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

    ![][1]

    <div class="dev-callout"><b>중요</b><p>포털의 푸시 탭에서 향상된 푸시 알림에 대한 GCM 자격 증명을 설정할 경우 앱에서 알림 허브를 구성하도록 GCM 자격 증명이 알림 허브와 공유됩니다.</p></div>

이제 모바일 서비스가 GCM 및 알림 허브와 함께 작동하도록 구성되었습니다.

## <a name="download-the-service"></a><span class="short-header">서비스 다운로드</span>로컬 컴퓨터로 서비스 다운로드

[WACOM.INCLUDE [mobile-services-download-service-locally](../includes/mobile-services-download-service-locally.md)]

## <a name="test-the-service"></a><span class="short-header">서비스 테스트</span>모바일 서비스 테스트

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service](../includes/mobile-services-dotnet-backend-test-local-service.md)]

## <span id="update-server"></span></a> 푸시 알림을 전송하도록 서버 업데이트

1.  Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 `using` 문을 추가합니다.

        using System;
        using System.Collections.Generic;

2.  `PostTodoItem` 메서드를 다음 코드로 업데이트합니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    이 코드는 할 일 항목을 삽입한 후 삽입한 항목의 텍스트가 포함된 푸시 알림을 보냅니다. 오류 이벤트에서 코드는 관리 포털에서 모바일 서비스의 **로그** 탭에서 볼 수 있는 오류 로그 항목을 추가합니다.

## <a name="publish-the-service"></a><span class="short-header">서비스 게시</span>Azure에 모바일 서비스 게시

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="update-app"></a>앱에 푸시 알림 추가

### Android SDK 버전 확인

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version](../includes/mobile-services-verify-android-sdk-version.md)]

다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](영문)을 참조하세요.

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

### 코드 추가

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]

## <a name="test-app"></a><span class="short-header">앱 테스트</span>게시된 모바일 서비스에 대해 앱 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

### 테스트에 에뮬레이터를 사용하는 경우...

Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1.  **Window**에서 **Android Virtual Device Manager**를 선택하고, 장치를 선택하고, **Edit**(장치가 없는 경우에는 **New**)를 클릭합니다.

    ![][2]

2.  **Target**에서 **Google APIs**(또는 **Google APIs x86**)를 선택한 다음 OK를 클릭합니다.

    ![][3]

    AVD 대상이 Google API를 사용하도록 설정됩니다. 몇 가지 버전의 Android SDK가 설치되어 있는 경우 API 수준이 앞서 프로젝트 속성에서 설정한 것과 일치하는지 확인합니다.

### <span id="local-testing"></span></a> 로컬 테스트에 푸시 알림 사용

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

### 테스트 실행

1.  Eclipse의 **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

2.  앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

    ![][4]

3.  알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.

이 자습서를 성공적으로 완료했습니다.

## <a name="next-steps"> </a>다음 단계

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

-   [데이터 시작하기][데이터 시작하기]
    <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

-   [인증 시작][인증 시작]
    <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]
    <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법][모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]
    <br/>Android와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript 백 엔드"
  [Google Cloud Messaging 사용]: #register
  [푸시 요청을 전송하도록 모바일 서비스 구성]: #configure
  [푸시 알림을 전송하도록 서버 업데이트]: #update-server
  [앱에 푸시 알림 추가]: #update
  [로컬 테스트에 푸시 알림 사용]: #local-testing
  [게시된 모바일 서비스에 대해 앱 테스트]: #test-app
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [GCM 사용]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Play Services 추가]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]: /ko-kr/documentation/articles/mobile-services-android-how-to-use-client-library
