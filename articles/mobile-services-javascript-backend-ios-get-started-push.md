<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# 모바일 서비스에서 푸시 알림 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows 스토어 C#">Windows 스토어 C#</a><a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a><a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS" class="current">iOS</a><a href="/ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android">Android</a><!---<a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>--></div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET 백 엔드" >.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="JavaScript 백 엔드" class="current">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE] 이 자습서에서는 모바일 서비스에서 푸시 알림을 전송하는 방법인 알림 허브와 모바일 서비스의 통합에 대해 설명합니다. 레거시 푸시를 사용하는 이전 모바일 서비스를 사용 중이고 아직 알림 허브로 업그레이드하지 않은 경우 이 자습서 중에 *업그레이드를 수행하는 것이 좋습니다*. 지금 업그레이드하지 않도록 선택할 경우, [푸시 알림 시작(레거시)][푸시 알림 시작(레거시)] 자습서를 따라야 합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증서 서명 요청 생성][인증서 서명 요청 생성]
2.  [앱을 등록하고 푸시 알림을 사용하도록 설정][앱을 등록하고 푸시 알림을 사용하도록 설정]
3.  [앱용 프로비저닝 프로필 만들기][앱용 프로비저닝 프로필 만들기]
4.  [모바일 서비스 구성][모바일 서비스 구성]
5.  [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
6.  [푸시 알림을 전송하도록 스크립트 업데이트][푸시 알림을 전송하도록 스크립트 업데이트]
7.  [알림을 받기 위한 데이터 삽입][알림을 받기 위한 데이터 삽입]

이 자습서를 사용하려면 다음이 필요합니다.

-   [모바일 서비스 iOS SDK][모바일 서비스 iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 6.0(이상) 지원 장치
-   iOS 개발자 프로그램 멤버 자격

   > [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기]를 완료해야 합니다.

[WACOM.INCLUDE [Apple 푸시 알림 설정](../includes/enable-apple-push-notifications.md)]

## 푸시 요청을 전송하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## 앱에 푸시 알림 추가

1.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 교체합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

2.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            client.push.registerNative(deviceToken, @”uniqueTag”);
        }

3.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Handle any failure to register. 
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
        }

4.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

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

6.  QSTodoListViewController.m에서 다음 행을 찾아 **(IBAction)onAdd** 동작을 수정합니다.

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

## 관리 포털에서 등록된 삽입 스크립트 업데이트

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][0]

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][1]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate push notifications
            setTimeout(function() {
                push.apns.send(null, {
                    alert: "Alert: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    새 삽입 스크립트가 등록되며, 이 스크립트는 [apns 개체][apns 개체]를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

    > [WACOM.NOTE] 이 스크립트는 푸시 알림을 수신하기 위해 앱을 닫을 시간을 제공하도록 알림 전송을 지연시킵니다.

## 앱에서 푸시 알림 테스트

1.  **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

    ![][2]

    > [WACOM.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. This request only occurs the first time that the app runs.

2.  앱에서 *새 모바일 서비스 작업*과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

    ![][3]

3.  알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

    ![][4]

4.  2단계를 반복하여 앱을 즉시 닫은 후 다음 푸시가 표시되는지 확인합니다.

    ![][5]

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이 자습서에서는 모바일 서비스 및 알림 허브를 사용하여 푸시 알림을 보낼 수 있도록 iOS 앱을 설정하는 작업에 대한 기본 사항을 설명했습니다. 그런 후 다음 자습서 중 하나를 완료해야 합니다.

-   [인증된 사용자에게 푸시 알림 보내기][인증된 사용자에게 푸시 알림 보내기]<br/>
    태그를 사용하여 모바일 서비스의 푸시 알림을 인증된 사용자에게만 보내는 방법에 대해 알아봅니다.

-   [구독자에게 브로드캐스트 알림 보내기][구독자에게 브로드캐스트 알림 보내기]<br/>
    사용자가 관심 있어 하는 범주에 대해 푸시 알림을 등록하고 수신하는 방법을 설명합니다.
    <!--- + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->
    다음 항목에서 모바일 서비스 및 알림 허브에 대해 알아봅니다.

-   [데이터 시작하기][데이터 시작하기]<br/>
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아봅니다.

-   [인증 시작][인증 시작]<br/>
    모바일 서비스를 사용하여 서로 다른 계정 유형의 앱 사용자를 인증하는 방법에 대해 알아봅니다.

-   [알림 허브 정의][알림 허브 정의]<br/>
    모든 주요 클라이언트 플랫폼에 걸쳐 알림 허브가 앱에 알림을 전달하는 방법에 대해 알아봅니다.

-   [모바일 서비스 Objective-C 방법 개념 참조][모바일 서비스 Objective-C 방법 개념 참조]<br/>
    Objective-C 및 iOS에서 모바일 서비스를 사용하는 방법에 대해 알아봅니다.

-   [모바일 서비스 서버 스크립트 참조][모바일 서비스 서버 스크립트 참조]<br/>
    모바일 서비스에서 비즈니스 논리를 구현하는 방법에 대해 알아봅니다.



  [인증서 서명 요청 생성]: #certificates
  [앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
  [앱용 프로비저닝 프로필 만들기]: #profile
  [모바일 서비스 구성]: #configure
  [앱에 푸시 알림 추가]: #add-push
  [푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
  [알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/mobile-services-ios-get-started
  [0]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [인증된 사용자에게 푸시 알림 보내기]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-push-notifications-app-users/
  [구독자에게 브로드캐스트 알림 보내기]: /ko-kr/documentation/articles/notification-hubs-ios-send-breaking-news/
  [데이터 시작하기]: /ko-kr/documentation/articles/mobile-services-ios-get-started-data
  [인증 시작]: /ko-kr/documentation/articles/mobile-services-ios-get-started-users
  [알림 허브 정의]: /ko-kr/documentation/articles/notification-hubs-overview/
  [모바일 서비스 Objective-C 방법 개념 참조]: /ko-kr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
