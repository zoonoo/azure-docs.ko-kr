<properties pageTitle="푸시 알림 시작(iOS) | 모바일 개발자 센터" description="Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림(레거시 푸시)을 보내는 방법에 대해 알아봅니다." services="mobile-services" documentationCenter="ios" manager="dwrede" editor="" authors="krisragh"/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# 모바일 서비스 앱에 푸시 알림 추가(레거시 푸시)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push" title="iOS" class="current">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
	<a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title=".NET backend">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push/"  title="JavaScript backend" class="current">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple 푸시 알림 서비스)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.


>[AZURE.NOTE]이 항목에서는 알림 허브 통합을 사용하도록 <em>아직 업그레이드되지 않은</em> <em>기존</em> 모바일 서비스를 지원합니다. <em>새</em> 모바일 서비스를 만들 때 이 통합 기능이 자동으로 사용하도록 설정됩니다. 새 모바일 서비스의 경우 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)을 참조하세요.
>
>모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. <em>가능한 경우 알림 허브를 사용하도록 기존 모바일 서비스를 업그레이드해야 합니다</em>. 업그레이드한 후에는 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/)을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1. [인증서 서명 요청 생성]
2. [앱을 등록하고 푸시 알림을 사용하도록 설정]
3. [앱용 프로비저닝 프로필 만들기]
3. [모바일 서비스 구성]
4. [앱에 푸시 알림 추가]
5. [푸시 알림을 전송하도록 스크립트 업데이트]
6. [알림을 받기 위한 데이터 삽입]

이 자습서를 사용하려면 다음이 필요합니다.

+ [모바일 서비스 iOS SDK]
+ [Xcode 4.5][Xcode 설치]
+ iOS 5.0(이상) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   > [AZURE.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작]을 완료해야 합니다.

[AZURE.INCLUDE [Enable Apple Push Notifications](../includes/enable-apple-push-notifications.md)]

## 푸시 요청을 보내도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

## 앱에 푸시 알림 추가

1. Xcode에서 QSAppDelegate.h 파일을 열고 ***window** 속성 아래에 다음 속성을 추가합니다.

        @property (strong, nonatomic) NSString *deviceToken;

    > [AZURE.NOTE] 모바일 서비스에서 동적 스키마가 사용하도록 설정된 경우 이 속성이 포함된 새 항목이 삽입되면  'deviceToken' 열이 **TodoItem** 테이블에 자동으로 추가됩니다.

2. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 교체합니다.

        - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:
        (NSDictionary *)launchOptions
        {
            // Register for remote notifications
            [[UIApplication sharedApplication] registerForRemoteNotificationTypes:
            UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];
            return YES;
        }

3. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // We are registered, so now store the device token (as a string) on the AppDelegate instance
        // taking care to remove the angle brackets first.
        - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:
        (NSData *)deviceToken {
            NSCharacterSet *angleBrackets = [NSCharacterSet characterSetWithCharactersInString:@"<>"];
            self.deviceToken = [[deviceToken description] stringByTrimmingCharactersInSet:angleBrackets];
        }

4. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.

        // Handle any failure to register. In this case we set the deviceToken to an empty
        // string to prevent the insert from failing.
        - (void)application:(UIApplication *)application didFailToRegisterForRemoteNotificationsWithError:
        (NSError *)error {
            NSLog(@"Failed to register for remote notifications: %@", error);
            self.deviceToken = @"";
        }

5. QSAppDelegate.m의 구현 내에서 다음 처리기 메서드를 추가합니다.  

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

5. 대리자(delegate)를 사용하여 장치 토큰을 가져올 수 있도록 QSTodoListViewController.m에서 QSAppDelegate.h 파일을 가져옵니다.

        #import "QSAppDelegate.h"

6. QSTodoListViewController.m에서 다음 행을 찾아 **(IBAction)onAdd** 동작을 수정합니다.

        NSDictionary *item = @{ @"text" : itemText.text, @"complete" : @(NO) };

   Replace this with the following code:

        // Get a reference to the AppDelegate to easily retrieve the deviceToken
        QSAppDelegate *delegate = [[UIApplication sharedApplication] delegate];

        NSDictionary *item = @{
            @"text" : itemText.text,
            @"complete" : @(NO),
            // add the device token property to our todo item payload
            @"deviceToken" : delegate.deviceToken
        };

   	그러면 **QSAppDelegate**에 대한 참조를 추가하여 장치 토큰을 가져온 후 해당 장치 토큰을 포함하도록 요청 페이로드를 수정합니다.

   	> [AZURE.NOTE] <strong>addItem</strong> 메서드를 호출하기 전에 이 코드를 추가해야 합니다.

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## 관리 포털에서 등록된 삽입 스크립트 업데이트

1. 관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][21]

2. **todoitem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

  	![][22]

   	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

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

   	새 삽입 스크립트가 등록되며, 이 스크립트는 [apns 개체]를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.


   	> [AZURE.NOTE] 이 스크립트는 앱을 닫고 알림 메시지를 수신할 수 있는 시간을 주기 위해 알림 전송을 지연시킵니다.

## 앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

  	![][23]

    > [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 수행됩니다.

2. 앱에서 _새 모바일 서비스 작업_과 같은 의미 있는 텍스트를 입력하고 더하기(**+**) 아이콘을 클릭합니다.

  	![][24]

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

  	![][25]

4. 2단계를 반복하여 앱을 즉시 닫은 후 다음 알림이 표시되는지 확인합니다.

  	![][26]

이 자습서를 성공적으로 완료했습니다.

## 다음 단계

이 간단한 예제에서는 사용자가 방금 삽입한 데이터로 푸시 알림을 받습니다. APNS에서 사용되는 장치 토큰이 요청에서 클라이언트에 의해 모바일 서비스에 제공됩니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림]에서는, 장치 토큰을 저장하고 삽입이 발생할 경우 저장된 모든 채널로 푸시 알림을 보내는 별도의 Devices 테이블을 만듭니다.

<!-- Anchors. -->
[인증서 서명 요청 생성]: #certificates
[앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
[앱용 프로비저닝 프로필 만들기]: #profile
[모바일 서비스 구성]: #configure
[푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
[앱에 푸시 알림 추가]: #add-push
[알림을 받기 위한 데이터 삽입]: #test
[다음 단계]:#next-steps

<!-- Images. -->
[5]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step10.png
[17]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/mobile-services-ios-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios.png
[20]: ./media/mobile-services-ios-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/mobile-services-ios-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png

<!-- URLs. -->
[Xcode 설치](영문): https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS 프로비전 포털]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[모바일 서비스 iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple 푸시 알림 서비스]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[모바일 서비스 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-ios
[데이터 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-ios
[인증 시작](영문): /ko-kr/develop/mobile/tutorials/get-started-with-users-ios
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-ios
[앱 사용자에 대한 푸시 알림]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-ios
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-in-scripts-ios
[Azure 관리 포털]: https://manage.windowsazure.com/
[apns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=272333


<!--HONumber=42-->
