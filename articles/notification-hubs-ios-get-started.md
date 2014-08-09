<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

알림 허브 시작
==============

[Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 스토어 C#")[Windows Phone](/ko-kr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/ko-kr/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/ko-kr/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/ko-kr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

이 항목에서는 Azure 알림 허브를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 APNS(Apple Push Notification Service)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증서 서명 요청 생성](#certificates)
2.  [앱을 등록하고 푸시 알림을 사용하도록 설정](#register)
3.  [앱용 프로비저닝 프로필 만들기](#profile)
4.  [알림 허브 구성](#configure-hub)
5.  [알림 허브에 앱 연결](#connecting-app)
6.  [백 엔드에서 알림 보내기](#send)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

-   [모바일 서비스 iOS SDK](http://go.microsoft.com/fwLink/?LinkID=266533)
-   [XCode 4.5](https://go.microsoft.com/fwLink/p/?LinkID=266532)
-   iOS 5.0(이상) 지원 장치
-   iOS 개발자 프로그램 멤버 자격

    **참고**

    푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서를 완료해야 다른 모든 iOS 앱용 알림 허브 자습서를 진행할 수 있습니다.

**참고**

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)을 참조하십시오.

APNS(Apple Push Notification Service)는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르십시오. 공식 APNS 기능 설명서는 [Apple Push Notification Service]를 참조하십시오.

CSR 파일 생성인증서 서명 요청 파일 생성
---------------------------------------

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1.  Utilities 폴더에서 Keychain Access 도구를 실행합니다.

2.  **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png)

3.  **User Email Address**와 **Common Name**을 선택하고, **Saved to disk**가 선택되어 있는지 확인한 후 **Continue**를 클릭합니다. **CA Email Address** 필드는 필요하지 않으므로 비워둡니다.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png)

4.  **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.

	![](./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png)

	CSR 파일이 선택한 위치에 저장됩니다. 기본 위치는 바탕 화면입니다. 이 파일을 저장한 위치를 기억해두십시오.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만드십시오.

앱 등록푸시 알림을 위한 앱 등록
-------------------------------

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림도 등록해야 합니다.

1.  아직 앱을 등록하지 않은 경우 Apple Developer Center의 [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 새 앱을 등록합니다.

    ![][B102] 

2.  **Description**에 앱의 이름을 입력하고, **Bundle Identifier**에 *MobileServices.Quickstart* 값을 입력하고, "App Services" 섹션에서 "Push Notifications" 옵션을 선택하고, **Continue**를 클릭합니다. 이 예제에서는 **MobileServices.Quickstart** ID를 사용하지만 실제로 이 ID를 사용해서는 안 됩니다. 모든 사용자는 고유한 앱 ID를 사용해야 하기 때문입니다. 사용자의 전체 이름 또는 이니셜을 앱 이름 뒤에 추가하는 것이 좋습니다.

    ![][B103]
           
    앱 ID가 생성되고 정보를 **제출**하도록 요청됩니다. **Submit**를 클릭하십시오.
       
    ![][B104] 
       
    **Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **Done**을 클릭하십시오.
       
    ![][B105]

    > [WACOM.NOTE] *MobileServices.Quickstart* 외의 **Bundle Identifier** 값을 제공하려는 경우 Xcode 프로젝트에서도 Bundle Identifier(번들 식별자) 값을 업데이트해야 합니다.

3.  방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

    ![][B106]
       
    앱 ID를 클릭하면 앱에 대한 세부 정보 및 앱 ID가 표시됩니다. **Settings** 단추를 클릭하십시오.
       
    ![][B107] 

4.  화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

    ![][B108] 

    "Add iOS Certificate" assistant가 표시됩니다.
       
    ![][B108] 

    > [WACOM.NOTE] 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5.  **Choose File**을 클릭하고, 첫 번째 작업에서 만든 CSR 파일이 저장된 위치로 이동하여 파일을 선택하고, **Generate**를 클릭합니다.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png)

6.  포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png)
 	
	서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

	![](./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png)

    > [WACOM.NOTE] 기본적으로 다운로드된 개발 증명서 파일은 이름이 **aps_development.cer**로 지정됩니다.

1.  다운로드한 푸시 인증서 **aps\_development.cer**을 두 번 클릭합니다.

    아래와 같이 새 인증서가 Keychain에 설치됩니다.

    ![][B10]

    > [WACOM.NOTE] 인증서의 이름은 다를 수 있지만 **Apple Development iOS Push Notification Services:**가 앞에 옵니다.

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 서비스에 업로드하여 APNS에서의 인증에 사용하게 됩니다.

앱 프로비전앱용 프로비저닝 프로필 만들기
----------------------------------------

1.  [iOS Provisioning Portal](http://go.microsoft.com/fwlink/p/?LinkId=272456)로 돌아가, **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 표시됩니다.

    ![][120]

2.  **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

    ![][121]

3.  **App ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

    ![][122]

4.  **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

    ![][123]

5.  테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

    ![][124]

6.  마지막으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

    ![][125]
       
    ![][126]

    새 프로비저닝 프로필이 생성됩니다.

7.  Xcode에서 Organizer를 열고 왼쪽 창의 **Library** 섹션에서 **Provisioning Profiles**를 선택한 다음 방금 만든 프로비저닝 프로필을 가져옵니다.

8.  왼쪽에서 장치를 선택하고 프로비저닝 프로필을 다시 가져옵니다.

9.  Keychain Access에서 새 인증서를 마우스 오른쪽 단추로 클릭하여 **Export**를 클릭하고, **.p12** 형식을 선택하고, **Save**를 클릭합니다.

    ![][18]

    내보낸 인증서의 파일 이름과 위치를 적어둡니다.

알림 허브 구성알림 허브 구성
----------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

    ![][27]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

    ![][28]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

    ![][29]

5.  맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

    ![][210]

6.  맨 위에 있는 **구성** 탭을 클릭한 다음 Apple 알림 설정에 대해 **업로드**를 클릭합니다. 그런 다음 앞서 내보낸 **.p12** 인증서 및 인증서의 암호를 선택합니다. **프로덕션**(스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우) 또는 **샌드박스**(개발 중에) 중 어떤 푸시 서비스를 사용할지를 선택해야 합니다.

    ![][211]

7.  맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

    ![][212]

이제 알림 허브가 APN과 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

앱 연결알림 허브에 앱 연결
--------------------------

1.  XCode에서 새 iOS 프로젝트를 만들고 **Single View Application** 템플릿을 선택합니다.

    ![][31]

2.  **Targets**에서 프로젝트 이름을 클릭하고, **Code Signing Identity**를 확장한 다음, **Debug**에서 코드 서명 ID 프로필을 선택합니다. 추가로, XCode의 최신 버전을 사용하는 경우 **Levels**를 **Basic**에서 **All**로 전환하고 **Provisioning Profile** 라인 항목을 프로비저닝 프로필로 설정합니다.

    ![][32]

3.  iOS용 Azure Mobile SDK를 다운로드합니다. .zip 파일을 열고 WindowsAzureMessaging.framework 폴더를 XCode 프로젝트의 Framework 폴더로 끌어옵니다. **Copy items in destination group's folder**를 선택하고 **OK**를 클릭합니다.

    ![][33]

4.  AppDelegate.h 파일에서 다음 import 지시문을 추가합니다.

          #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5.  AppDelegate.m 파일의 `didFinishLaunchingWithOptions` 메서드에 다음 코드를 추가합니다.

          [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

6.  같은 파일에 다음 메서드를 추가합니다.

         - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {    
             SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
                                       @"<connection string>" notificationHubPath:@"mynh"];
                
             [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
                 if (error != nil) {
                     NSLog(@"Error registering for notifications: %@", error);
                 }
            }];
         }

7.  *(옵션)* 역시 같은 파일에서, 앱이 활성 상태일 때 알림이 수신되는 경우 **UIAlert**을 표시하려면 다음 메서드를 추가합니다.

         - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
             NSLog(@"%@", userInfo);
             UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
             [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
             @"OK" otherButtonTitles:nil, nil];
             [alert show];
         }

8.  장치에서 앱을 실행합니다.

알림 보내기백 엔드에서 알림 보내기
----------------------------------

[REST 인터페이스](http://msdn.microsoft.com/ko-kr/library/windowsazure/dn223264.aspx)를 사용하여 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 앱 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

.NET 앱을 사용하여 알림을 보내려면

1.  새 Visual C\# 콘솔 응용 프로그램을 만듭니다.

    ![](./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png)

2.  [WindowsAzure.ServiceBus NuGet 패키지](http://nuget.org/packages/WindowsAzure.ServiceBus/)를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

         Install-Package WindowsAzure.ServiceBus

    Enter 키를 누릅니다.

3.  Program.cs 파일을 열고 다음 using 문을 추가합니다.

         using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에 다음 메서드를 추가합니다. "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다(예: 이전 예제의 **mynotificationhub2**).

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
             await hub.SendAppleNativeNotificationAsync(alert);
         }

5.  그런 다음 `Main` 메서드에 다음 줄을 추가합니다.

          SendNotificationAsync();
          Console.ReadLine();

6.  F5 키를 눌러 앱을 실행합니다. 장치에 대한 경고를 받게 됩니다. Wi-Fi를 사용하는 경우 연결에 문제가 없는지 확인하십시오.

Apple [Local and Push Notification Programming Guide](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1)에서 가능한 모든 페이로드를 찾아볼 수 있습니다.

모바일 서비스를 사용하여 알림을 보내려면 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-ios)(영문)을 따른 후 다음을 수행하십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 모바일 서비스를 선택합니다.

2.  맨 위에 있는 **스케줄러** 탭을 선택합니다.

    ![][215]

3.  새 예약된 작업을 만들고 이름을 삽입한 후 **요청 시**를 선택합니다.

    ![][216]

4.  작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5.  스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. **저장**을 클릭합니다.

		var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
        	null,
    		{"aps":
				{
                "alert": "Hello from Mobile Services!"
                }
		    },
			function(error)
			{
            	if (!error) {
				    console.warn("Notification successful");
                }
    		}
         );


6.  아래쪽 막대에서 **한 번 실행**을 클릭합니다. 장치에 대한 경고를 받게 됩니다.

다음 단계
---------

이 간단한 예제에서는 모든 iOS 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시](/en-us/manage/services/notification-hubs/notify-users-aspnet) 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet)를 참조하십시오. 알림 허브 사용 방법에 대해 자세히 알아보려면 [알림 허브 지침](http://msdn.microsoft.com/ko-kr/library/jj927170.aspx) 및 [iOS용 알림 허브 방법](http://msdn.microsoft.com/ko-kr/library/jj927168.aspx)을 참조하십시오.


<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png


[105]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-05.png
[106]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-06.png
[107]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-07.png
[108]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-08.png
[109]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-09.png
[110]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-10.png
[111]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-11.png
[112]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-12.png
[113]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-13.png
[114]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-14.png
[115]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-15.png
[116]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-16.png

[118]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-18.png
[119]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-19.png

[120]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-20.png
[121]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-21.png
[122]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-22.png
[123]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-23.png
[124]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-24.png
[125]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-25.png
[126]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-26.png

[27]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
[28]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
[29]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
[210]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
[211]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
[212]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png


[213]: ./media/notification-hubs-ios-get-started/notification-hub-create-console-app.png



[215]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
[33]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png


[B102]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-02.png
[B103]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-03.png
[B104]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-04.png
[B105]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-05.png
[B106]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-06.png
[B107]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-07.png
[B108]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-08.png
[B110]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-10.png
[B111]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-11.png
[B9]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step9.png
[B10]: ./media/notification-hubs-ios-get-started/notification-hub-clone-mobile-services-ios-push-step10.png


<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-ios
[Azure Management Portal]: https://manage.windowsazure.com/
[Notification Hubs Guidance]: http://msdn.microsoft.com/ko-kr/library/jj927170.aspx
[Notification Hubs How-To for iOS]: http://msdn.microsoft.com/ko-kr/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
