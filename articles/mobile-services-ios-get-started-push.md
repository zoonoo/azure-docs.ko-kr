<properties linkid="develop-mobile-tutorials-get-started-with-push-ios" urlDisplayName="Get Started with Push (iOS)" pageTitle="Get started with push notifications (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your iOS app." metaCanonical="http://www.windowsazure.com/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="" solutions="" manager="" editor="" />

모바일 서비스에서 푸시 알림 시작하기
====================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

이 항목에서는 Azure 모바일 서비스를 사용하여 iOS 앱에 푸시 알림을 보내는 방법에 대해 설명합니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

오른쪽에 있는 클립을 클릭하여 이 자습서의 동영상 버전을 볼 수 있습니다.

[자습서 보기](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) [동영상 재생](http://channel9.msdn.com/posts/iOS-Support-in-Windows-Azure-Mobile-Services) 10:37

> [WACOM.NOTE] 이 항목에서는 모바일 서비스에서 제공하는 기존 지원을 통해 푸시 알림을 사용하도록 설정하는 방법에 대해 설명합니다. 템플릿 기반, 플랫폼 간 푸시 알림을 수많은 장치에 보낼 수 있도록 Azure 알림 허브가 모바일 서비스와 통합됩니다. 알림 허브를 사용하는 푸시 알림은 기본적으로 사용되지 않으며, 현재 모바일 서비스 라이브러리에서 iOS에 대한 알림 허브 지원이 제공되지 않습니다. 그러나 알림 허브 라이브러리를 사용하여 모바일 서비스에서 푸시 알림을 전송할 수 있습니다. 자세한 내용은 [알림 허브 시작하기](/ko-kr/documentation/articles/notification-hubs-ios-get-started/)를 참조하십시오.

이 자습서에서는 푸시 알림을 사용하기 위한 다음과 같은 기본 단계를 안내합니다.

1.  [인증서 서명 요청 생성](#certificates)
2.  [앱을 등록하고 푸시 알림을 사용하도록 설정](#register)
3.  [앱용 프로비저닝 프로필 만들기](#profile)
4.  [모바일 서비스 구성](#configure)
5.  [앱에 푸시 알림 추가](#add-push)
6.  [푸시 알림을 전송하도록 스크립트 업데이트](#update-scripts)
7.  [알림을 받기 위한 데이터 삽입](#test)

이 자습서를 사용하려면 다음이 필요합니다.

-   [모바일 서비스 iOS SDK](https://go.microsoft.com/fwLink/p/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   iOS 5.0(이상) 지원 장치
-   iOS 개발자 프로그램 멤버 자격

    > [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/en-us/develop/mobile/tutorials/get-started-ios)를 완료해야 합니다.

APNS(Apple Push Notification Service)는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르십시오. 공식 APNS 기능 설명서는 [Apple Push Notification Service](http://go.microsoft.com/fwlink/p/?LinkId=272584)(영문)를 참조하십시오.

인증서 서명 요청 파일 생성
--------------------------

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1.  Utilities 폴더에서 Keychain Access 도구를 실행합니다.

2.  **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step5.png)

1.  **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step6.png)

1.  **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step7.png) 

  	CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두십시오.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만들 것입니다.

푸시 알림을 위해 앱 등록
------------------------

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림도 등록해야 합니다.

1.  아직 앱을 등록하지 않은 경우 Apple Developer Center의 [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 새 앱을 등록합니다.

   	![][102] 

2.  **Description**에 앱의 이름을 입력하고, **Bundle Identifier**에 *MobileServices.Quickstart* 값을 입력하고, "App Services" 섹션에서 "Push Notifications" 옵션을 선택하고, **Continue**를 클릭합니다. 이 예제에서는 **MobileServices.Quickstart** ID를 사용하지만 실제로 이 ID를 사용해서는 안 됩니다. 모든 사용자는 고유한 앱 ID를 사용해야 하기 때문입니다. 사용자의 전체 이름 또는 이니셜을 앱 이름 뒤에 추가하는 것이 좋습니다.

   	![][103]
           
   	This generates your app ID and requests you to **Submit** the information. Click **Submit**
           
   	![][104] 
           
   	Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.
           
   	![][105]

    > [WACOM.NOTE] *MobileServices.Quickstart* 외의 **Bundle Identifier** 값을 제공하려는 경우 Xcode 프로젝트에서도 Bundle Identifier(번들 식별자) 값을 업데이트해야 합니다.

3.  방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

   	![][106]
           
   	Clicking on the app ID will display details on the app and app ID. Click the **Settings** button.
           
   	![][107] 

4.  화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

   	![][108] 

   	This displays the "Add iOS Certificate" assistant.
           
   	![][108] 

    > [WACOM.NOTE] 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5.  **Choose File**을 클릭하고, 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 파일을 선택하고, **Generate**를 클릭합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-10.png)

1.  포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-11.png)

 서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] By default, the downloaded file a development certificate is named **aps_development.cer**.

1.  다운로드한 푸시 인증서 **aps\_development.cer**을 두 번 클릭합니다.

   	This installs the new certificate in the Keychain, as shown below:

   	![][10]

    > [WACOM.NOTE] 인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Notification Services:**가 앞에 옵니다.

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 서비스에 업로드하여 APNS에서의 인증에 사용하게 됩니다.

앱용 프로비저닝 프로필 만들기
-----------------------------

1.  [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 돌아가, **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.

   	![][112]

2.  **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

   	![][113]

3.  **App ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

   	![][114]

4.  **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

   	![][115]

5.  테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

   	![][116]

6.  마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

   	![][117]

   	새 프로비저닝 프로필이 생성됩니다.

1.  Xcode에서 Organizer를 열고 Devices view를 선택하고, 왼쪽 창의 **Library** 섹션에서 **Provisioning Profiles**를 선택하고, 가운데 창의 아래쪽에서 **Refresh** 단추를 클릭합니다.

   	![][101]

2.  **Targets** 아래에서 **Quickstart**를 클릭하고 **Code Signing Identity**를 확장한 다음, **Debug** 아래에서 새 프로필을 선택합니다.

   	![][17]

이제 Xcode 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 다음 단계에서는 인증서를 모바일 서비스에 업로드해야 합니다.

푸시 요청을 전송하도록 모바일 서비스 구성
-----------------------------------------

APNS에 앱을 등록하고 프로젝트를 구성했으면 APNS와 통합되도록 모바일 서비스를 구성해야 합니다.

1.  Keychain Access에서 새 인증서를 마우스 오른쪽 단추로 클릭하여 **Export**를 클릭하고, QuickstartPusher 파일의 이름을 지정하고, **.p12** 형식을 선택하고, **Save**를 클릭합니다.

   	![][28]

  내보낸 인증서의 파일 이름과 위치를 적어둡니다.

    > [WACOM.NOTE] This tutorial creates a QuickstartPusher.p12 file. Your file name and location might be different.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][18]

2.  **푸시** 탭을 클릭한 후 **업로드**를 클릭합니다.

   	![][19]

    인증서 업로드 대화 상자가 표시됩니다.

3.  **파일**을 클릭하고, 내보낸 인증서 QuickstartPusher.p12 파일을 선택하고, **암호**를 입력하고, 올바른 **모드**가 선택되었는지 확인하고, 확인 아이콘을 클릭한 후 **저장**을 클릭합니다.

   	![][20] 

    > [WACOM.NOTE] 이 자습서에서는 개발 인증서를 사용합니다.

모바일 서비스가 이제 APNS와 작동하도록 구성됩니다.

앱에 푸시 알림 추가
-------------------

1.  Xcode에서 QSAppDelegate.h 파일을 열고 ***window** 속성 아래에 다음 속성을 추가합니다.

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

관리 포털에서 등록된 삽입 스크립트 업데이트
-------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   	![][21]

2.  **todoitem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-insert-script-push2.png) 

   	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

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

   	This registers a new insert script, which uses the [apns object] to send a push notification (the inserted text) to the device provided in the insert request. 


   	> [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a toast notification.

앱에서 푸시 알림 테스트
-----------------------

1.  **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push1-ios.png)

    > [WACOM.NOTE] You must explicitly accept push notifications from your app. This request only occurs the first time that the app runs.

1.  앱에서 *새 모바일 서비스 작업*과 같은 의미 있는 텍스트를 입력하고 (**+**) 아이콘을 클릭합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push2-ios.png)

1.  알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push3-ios.png)

1.  2단계를 반복하여 앱을 즉시 닫은 후 다음 알림이 표시되는지 확인합니다.

  	![](./media/mobile-services-ios-get-started-push/mobile-quickstart-push4-ios.png)

이 자습서를 성공적으로 완료했습니다.

다음 단계
---------

이 간단한 예제에서는 사용자가 방금 삽입한 데이터로 푸시 알림을 받습니다. APNS에서 사용되는 장치 토큰이 요청에서 클라이언트에 의해 모바일 서비스에 제공됩니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios)에서는, 장치 토큰을 저장하고 삽입이 발생할 경우 저장된 모든 채널로 푸시 알림을 보내는 별도의 Devices 테이블을 만듭니다.

<!-- Images. -->
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
[112]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/mobile-services-ios-get-started-push/mobile-services-ios-push-17.png
