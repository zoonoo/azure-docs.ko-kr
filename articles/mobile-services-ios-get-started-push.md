<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app (legacy push)." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" solutions="" manager="dwrede" editor="" authors="krisragh" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh"></tags>

# 모바일 서비스에서 푸시 알림 시작(기존 푸시)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 스토어 C#">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
    <a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET 백 엔드">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript 백 엔드" class="current">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE]이 항목에서는 알림 허브 통합을 사용하도록 *아직 업그레이드되지 않은* *기존* 모바일 서비스를 지원합니다. *새* 모바일 서비스를 만드는 경우 이 통합 기능이 자동으로 사용하도록 설정됩니다. 새 모바일 서비스의 경우 [푸시 알림 시작][푸시 알림 시작]을 참조하십시오.
>
> 모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. *가능한 경우 알림 허브를 사용하도록 기존 모바일 서비스를 업그레이드해야 합니다*. 업그레이드한 후에는 [푸시 알림 시작][푸시 알림 시작]을 참조하십시오.

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
-   [Xcode 4.5][Xcode 4.5]
-   iOS 5.0(이상) 지원 장치
-   iOS 개발자 프로그램 멤버 자격

> [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기]를 완료해야 합니다.

[WACOM.INCLUDE [Apple Push Notification 사용][Apple Push Notification 사용]]

## 푸시 요청을 전송하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-apns-configure-push][mobile-services-apns-configure-push]]

## 앱에 푸시 알림 추가

1.  Xcode에서 QSAppDelegate.h 파일을 열고 \***window** 속성 아래에 다음 속성을 추가합니다.

        @property (strong, nonatomic) NSString *deviceToken;

    > [WACOM.NOTE] 모바일 서비스에서 동적 스키마를 사용하는 경우, 이 속성을 포함하는 새 항목이 삽입되면 **TodoItem** 테이블에 'deviceToken' 열이 자동으로 추가됩니다.

2.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 교체합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5.  QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Because toast alerts don't work when the app is running, the app handles them.
        // This uses the userInfo in the payload to display a UIAlertView.
        - (void)application:(UIApplication *)application didReceiveRemoteNotification:
        (NSDictionary *)userInfo {
            NSLog(@"%@", userInfo);
            UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
            [userInfo objectForKey:@"inAppMessage"] delegate:nil cancelButtonTitle:
            @"OK" otherButtonTitles:nil, nil];
            [alert show];
        }

6.  대리자(delegate)를 사용하여 장치 토큰을 가져올 수 있도록 QSTodoListViewController.m에서 QSAppDelegate.h 파일을 가져옵니다.

        #import "QSAppDelegate.h"

7.  QSTodoListViewController.m에서 다음 행을 찾아 **(IBAction)onAdd** 동작을 수정합니다.

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) }; 

이것을 다음 코드로 교체합니다.

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

    This adds a reference to the **QSAppDelegate** to obtain the device token and then modifies the request payload to include that device token.

    > [WACOM.NOTE] You must add this code before to the call to the <strong>addItem</strong> method.

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## 관리 포털에서 등록된 삽입 스크립트 업데이트

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][]

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][1]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

        function insert(item, user, request) {
            request.execute();
            // Set timeout to delay the notification, to provide time for the 
            // app to be closed on the device to demonstrate toast notifications
            setTimeout(function() {
                push.apns.send(item.deviceToken, {
                    alert: "Toast: " + item.text,
                    payload: {
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'"
                    }
                });
            }, 2500);
        }

    새 삽입 스크립트가 등록되며, 이 스크립트는 [apns 개체][apns 개체](영문)를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

    > [WACOM.NOTE] 이 스크립트는 앱을 닫고 알림 메시지를 수신할 수 있는 시간을 주기 위해 알림 전송을 지연시킵니다.

## 앱에서 푸시 알림 테스트

1.  **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

    ![][2]

    > [WACOM.NOTE] 앱에서 명시적으로 푸시 알림을 허용해야 합니다. This request only occurs the first time that the app runs.

2.  앱에서 *새 모바일 서비스 작업*과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

    ![][3]

3.  알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

    ![][4]

4.  2단계를 반복하여 앱을 즉시 닫은 후 다음 알림이 표시되는지 확인합니다.

    ![][5]

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이 간단한 예제에서는 사용자가 방금 삽입한 데이터로 푸시 알림을 받습니다. APNS에서 사용되는 장치 토큰이 요청에서 클라이언트에 의해 모바일 서비스에 제공됩니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림][앱 사용자에 대한 푸시 알림]에서는, 장치 토큰을 저장하고 삽입이 발생할 경우 저장된 모든 채널로 푸시 알림을 보내는 별도의 Devices 테이블을 만듭니다.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows 스토어 C#]: /ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows 스토어 C#"
  [Windows 스토어 JavaScript]: /ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows 스토어 JavaScript"
  [Windows Phone]: /ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /ko-kr/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /ko-kr/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [.NET 백 엔드]: /ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ ".NET 백 엔드"
  [JavaScript 백 엔드]: /ko-kr/documentation/articles/mobile-services-ios-get-started-push/ "JavaScript 백 엔드"
  [푸시 알림 시작]: /ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [인증서 서명 요청 생성]: #certificates
  [앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
  [앱용 프로비저닝 프로필 만들기]: #profile
  [모바일 서비스 구성]: #configure
  [앱에 푸시 알림 추가]: #add-push
  [푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
  [알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
  [Xcode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [모바일 서비스 시작하기]: /ko-kr/develop/mobile/tutorials/get-started-ios
  [Apple Push Notification 사용]: ../includes/enable-apple-push-notifications.md
  [mobile-services-apns-configure-push]: ../includes/mobile-services-apns-configure-push.md
  []: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
  [1]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
  [apns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=272333
  [2]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
  [3]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
  [4]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
  [5]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
  [앱 사용자에 대한 푸시 알림]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios
