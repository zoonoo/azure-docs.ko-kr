<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>


# 모바일 서비스에서 푸시 알림 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS" class="current">iOS</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android">Android</a>
<a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET 백 엔드" class="current">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/"  title="JavaScript 백 엔드">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.


이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증서 서명 요청 생성][인증서 서명 요청 생성]
2. [앱을 등록하고 푸시 알림을 사용하도록 설정][앱을 등록하고 푸시 알림을 사용하도록 설정]
3. [앱용 프로비저닝 프로필 만들기][앱용 프로비저닝 프로필 만들기]
4. [로컬로 서비스 다운로드][로컬로 서비스 다운로드]
5. [모바일 서비스 테스트][모바일 서비스 테스트]
6. [푸시 알림을 전송하도록 서버 업데이트][푸시 알림을 전송하도록 서버 업데이트]
7. [Azure에 모바일 서비스 게시][Azure에 모바일 서비스 게시]
8. [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
9. [푸시 알림을 전송하도록 스크립트 업데이트][푸시 알림을 전송하도록 스크립트 업데이트]
10. [로컬 테스트에 푸시 알림 사용][로컬 테스트에 푸시 알림 사용]
11. [게시된 모바일 서비스에 대해 앱 테스트][게시된 모바일 서비스에 대해 앱 테스트]

이 자습서를 사용하려면 다음이 필요합니다.

+ [모바일 서비스 iOS SDK][모바일 서비스 iOS SDK]
+ [XCode 4.5][XCode 4.5]
+ iOS 6.0(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   > [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기]를 완료해야 합니다.


[WACOM.INCLUDE [Apple 푸시 알림 활성화][Apple 푸시 알림 활성화]]


## 푸시 요청을 전송하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

<h2><a name="download-the-service"></a><span class="short-header">서비스 다운로드</span>로컬 컴퓨터로 서비스 다운로드</h2>

[WACOM.INCLUDE [mobile-services-ios-download-service-locally][mobile-services-ios-download-service-locally]]

<h2><a name="test-the-service"></a><span class="short-header">서비스 테스트</span>모바일 서비스 테스트</h2>

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][mobile-services-dotnet-backend-test-local-service]]

## <span id="update-server"></span></a> 푸시 알림을 전송하도록 서버 업데이트

1. Visual Studio 솔루션 탐색기에서 모바일 서비스 프로젝트의 **컨트롤러** 폴더를 확장합니다. TodoItemController.cs를 엽니다. 파일 맨 위에 `using` 문을 추가합니다.


		using System;
		using System.Collections.Generic;

2. `PostTodoItem` 메서드를 다음 코드로 업데이트합니다.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            ApplePushMessage message = new ApplePushMessage(item.Text, TimeSpan.FromHours(1));

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

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][mobile-services-dotnet-backend-publish-service]]

## 앱에 푸시 알림 추가

1. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 교체합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
			client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Because alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

5.  대리자(delegate)를 사용하여 장치 토큰을 가져올 수 있도록 QSTodoListViewController.m에서 QSAppDelegate.h 파일을 가져옵니다.

        #import "QSAppDelegate.h"

6. QSTodoListViewController.m에서 다음 행을 찾아 **(IBAction)onAdd** 동작을 수정합니다.

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 
 
   이것을 다음 코드로 교체합니다.

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];
    
        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : [[NSString alloc] initWithData:delegate.deviceToken encoding:NSUTF8StringEncoding]
        };

   그러면 **QSAppDelegate**에 대한 참조를 추가하여 장치 토큰을 가져온 후 해당 장치 토큰을 포함하도록 요청 페이로드를 수정합니다.

   > [WACOM.NOTE] **addItem** 메서드를 호출하기 전에 이 코드를 추가해야 합니다.

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## <span id="local-testing"></span></a> 로컬 테스트에 푸시 알림 사용

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## 앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

    ![][0]

    > [WACOM.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. This request only occurs the first time that the app runs.

2. 앱에서 *새 모바일 서비스 작업*과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

    ![][1]

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

    ![][2]

4. 2단계를 반복하여 앱을 즉시 닫은 후 다음 푸시가 표시되는지 확인합니다.

    ![][3]

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이 자습서에서는 모바일 서비스 및 알림 허브를 사용해서 푸시 알림을 보낼 수 있도록 iOS 앱을 설정하는 작업에 대한 기본 사항을 설명했습니다. 다음에는 [인증된 사용자에게 푸시 알림 보내기][인증된 사용자에게 푸시 알림 보내기] 자습서를 완료하는 것이 좋습니다. 이 자습서에서는 태그를 사용해서 모바일 서비스의 푸시 알림을 인증된 사용자에게만 보내는 방법을 보여 줍니다.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.  

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.

다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아보세요.

* [데이터 시작하기][데이터 시작하기]
  <br/>모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

* [인증 시작][인증 시작]
  <br/>모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

* [알림 허브 정의][알림 허브 정의]
  <br/>모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.


<!-- Anchors.  --> 
<!-- Images. --> 
<!-- URLs. -->

[Windows 스토어 C\#]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows 스토어 C#"
[Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows 스토어 JavaScript"
[Windows Phone]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
[iOS]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
[Android]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
[.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET 백 엔드"
[JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/ "JavaScript 백 엔드"
[인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[로컬로 서비스 다운로드]: #download-the-service-locally
[모바일 서비스 테스트]: #test-the-service
[푸시 알림을 전송하도록 서버 업데이트]: #update-server
[Azure에 모바일 서비스 게시]: #publish-mobile-service
[앱에 푸시 알림 추가]: #add-push
[푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
[로컬 테스트에 푸시 알림 사용]: #local-testing
[게시된 모바일 서비스에 대해 앱 테스트]: #test-app
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started
[Apple 푸시 알림 활성화]: ../includes/enable-apple-push-notifications.md
[mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
[mobile-services-ios-download-service-locally]: ../includes/mobile-services-ios-download-service-locally.md
[mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
[mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
[mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
[0]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[1]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/
[데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data
[인증 시작]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users
[알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
