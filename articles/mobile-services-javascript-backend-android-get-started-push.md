<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>모바일 서비스에서 푸시 알림 시작

<div class="dev-center-tutorial-selector sublanding">

[Windows 스토어 C#][Windows 스토어 C#][Windows 스토어 JavaScript][Windows 스토어 JavaScript][Windows Phone][Windows Phone][iOS][iOS][Android][Android] <!-- <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->

</div>

<div class="dev-center-tutorial-subselector">

[.NET 백 엔드][.NET 백 엔드] | [JavaScript 백 엔드][JavaScript 백 엔드]

</div>

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 퀵 스타트 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE]이 자습서에서는 알림 허브와 모바일 서비스를 통합하는 방법을 보여 줍니다. 이를 통해 모바일 서비스에서 푸시 알림을 보낼 수 있습니다. 기존 푸시를 사용하는 이전 버전의 모바일 서비스를 사용하고 있고 아직 알림 허브를 사용하도록 업그레이드하지 않은 경우 이 자습서를 통해 *업그레이드하는 것이 좋습니다*. 지금 업그레이드하지 않으려는 경우 다음 버전의 자습서 내용을 따라야 합니다. [푸시 알림 시작(기존)][푸시 알림 시작(기존)]

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [Google Cloud Messaging 사용][Google Cloud Messaging 사용]
2.  [모바일 서비스 구성][모바일 서비스 구성]
3.  [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
4.  [푸시 알림을 전송하도록 스크립트 업데이트][푸시 알림을 전송하도록 스크립트 업데이트]
5.  [알림을 받기 위한 데이터 삽입][알림을 받기 위한 데이터 삽입]

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기] 또는 [데이터 시작하기][데이터 시작하기]를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다.

## <span id="register"></span></a>Google Cloud Messaging 사용

> [WACOM.NOTE]이 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com][accounts.google.com]으로 이동하십시오.

[WACOM.INCLUDE [GCM 사용][GCM 사용]]

다음에는 이 API 키를 사용하여 모바일 서비스에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

## <span id="configure"></span></a>푸시 요청을 보내도록 모바일 서비스 구성

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][]

2.  **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

    > [WACOM.NOTE]이전 버전의 모바일 서비스를 사용하여 이 자습서를 완료하는 경우 **푸시** 탭 아래쪽에 **향상된 푸시 설정**이라는 링크가 표시될 수 있습니다. 이제 이 링크를 클릭하여 모바일 서비스를 알림 허브와 통합하도록 업그레이드합니다. 이러한 변경은 되돌릴 수 없습니다. 프로덕션 모바일 서비스에서 향상된 푸시 알림을 설정하는 방법에 대한 자세한 내용은 [이 지침][이 지침](영문)을 참조하십시오.

    ![][1]

    <div class="dev-callout">

    **중요**
    포털의 푸시 탭에서 향상된 푸시 알림에 대한 GCM 자격 증명을 설정할 경우 앱에서 알림 허브를 구성하도록 GCM 자격 증명이 알림 허브와 공유됩니다.

    </div>

이제 모바일 서비스와 앱이 GCM 및 알림 허브를 사용하도록 둘 다 구성되었습니다.

## <span id="add-push"></span></a>앱에 푸시 알림 추가

### Android SDK 버전 확인

[WACOM.INCLUDE [Verify SDK][Verify SDK]]

다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정][Google Play Services SDK 설정](영문)을 참조하십시오.

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play Services 추가][Play Services 추가]]

### 코드 추가

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][mobile-services-android-getting-started-with-push]]

## <span id="update-scripts"></span></a>관리 포털에서 등록된 삽입 스크립트 업데이트

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][2]

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][3]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    이 코드는 새 삽입 스크립트를 등록합니다. 그러면 삽입이 성공한 이후에 [gcm 개체][gcm 개체]를 사용하여 모든 등록된 장치에 푸시 알림을 보냅니다.

## <span id="test"></span></a>앱에서 푸시 알림 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

### 테스트를 위해 에뮬레이터 설정

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1.  Eclipse를 다시 시작하고 Package Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **Properties**, **Android**를 차례로 클릭합니다. **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![][4]

    프로젝트 대상이 Google API로 설정됩니다.

2.  **Window**에서 **Android Virtual Device Manager**를 선택하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![][5]

3.  **Target**에서 **Google APIs**를 선택하고 OK를 클릭합니다.

    ![][6]

    AVD 대상이 Google API를 사용하도록 설정됩니다.

### 테스트 실행

1.  Eclipse의 **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

2.  앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

    ![][7]

3.  알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.

이 자습서를 성공적으로 완료했습니다.

## <a name="next-steps"> </a>다음 단계

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

-   [데이터 시작하기][데이터 시작하기]
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

-   [인증 시작][인증 시작]
    모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]
    모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법][모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]
    Android와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]
    모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 자세히 알아봅니다.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript 백 엔드"
  [푸시 알림 시작(기존)]: /ko-kr/documentation/articles/mobile-services-android-get-started-push/
  [Google Cloud Messaging 사용]: #register
  [모바일 서비스 구성]: #configure
  [앱에 푸시 알림 추가]: #add-push
  [푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
  [알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-android-get-started/
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [GCM 사용]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [이 지침]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verify SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [Google Play Services SDK 설정]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Play Services 추가]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [gcm 개체]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-android-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [모바일 서비스에 Android 클라이언트 라이브러리를 사용하는 방법]: /ko-kr/documentation/articles/mobile-services-android-how-to-use-client-library
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
