<properties 
	pageTitle="Azure 알림 허브 시작" 
	description="Azure 알림 허브를 사용하여 알림을 푸시하는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="ios" 
	authors="wesmc" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##개요

이 항목에서는 Azure 알림 허브를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 APNS\(Apple Push Notification Service\)를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다.

##필수 조건

이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

+ [모바일 서비스 iOS SDK]
+ [XCode 4.5][Install Xcode]
+ iOS 5.0\(이상\) 지원 장치
+ iOS 개발자 프로그램 멤버 자격

   \>[AZURE.NOTE]푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치\(iPhone 또는 iPad\)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서를 완료해야 다른 모든 iOS 앱용 알림 허브 자습서를 진행할 수 있습니다.

> [AZURE.NOTE]이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fnotification-hubs-ios-get-started)을 참조하세요.

[AZURE.INCLUDE [Apple 푸시 알림 사용](../includes/enable-apple-push-notifications.md)]

##알림 허브 구성

1. Keychain Access에서 quickstart 앱의 새 인증서 **My Certificates**를 마우스 오른쪽 단추로 클릭합니다. **Export**를 클릭하고 파일의 이름을 지정한 후 **.p12** 형식을 선택하고 **Save**를 클릭합니다.

    ![][26]

  내보낸 인증서의 파일 이름과 위치를 적어둡니다.

>[AZURE.NOTE]이 자습서에서는 QuickStart.p12 파일을 만듭니다. 파일 이름과 위치가 다를 수 있습니다.

2. [Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

3. **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

   ![][27]

4. 알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

   ![][28]

5. 방금 만든 네임스페이스\(일반적으로 ***알림 허브 이름*-ns**\)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

   ![][29]

6. 맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

   ![][210]

7. 맨 위에 있는 **구성** 탭을 선택한 다음 Apple 알림 설정에 대해 **업로드**를 클릭합니다. 그런 다음 앞서 내보낸 **.p12** 인증서 및 인증서의 암호를 선택합니다. **프로덕션**\(스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우\) 또는 **샌드박스**\(개발 중에\) 중 어떤 푸시 서비스를 사용할지를 선택해야 합니다.

   ![][211]

8. 맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   ![][212]

이제 알림 허브가 APN과 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

##알림 허브에 앱 연결

1. XCode에서 새 iOS 프로젝트를 만들고 **Single View Application** 템플릿을 선택합니다.

   ![][31]

2. **Targets**에서 프로젝트 이름을 클릭하고, **Code Signing Identity**를 확장한 다음, **Debug**에서 코드 서명 ID 프로필을 선택합니다. 추가로, XCode의 최신 버전을 사용하는 경우 **Levels**를 **Basic**에서 **All**로 전환하고 **Provisioning Profile** 라인 항목을 프로비저닝 프로필로 설정합니다.

   ![][32]

3. iOS용 Azure Mobile SDK를 다운로드합니다. .zip 파일을 열고 WindowsAzureMessaging.framework 폴더를 XCode 프로젝트의 Framework 폴더로 끌어옵니다. **Copy items in destination group's folder**를 선택하고 **OK**를 클릭합니다.

   ![][33]

4. AppDelegate.h 파일에서 다음 import 지시문을 추가합니다.

         #import <WindowsAzureMessaging/WindowsAzureMessaging.h>

5. AppDelegate.m 파일의 `didFinishLaunchingWithOptions` 메서드에 다음 코드를 추가합니다.

         [[UIApplication sharedApplication] registerForRemoteNotificationTypes: UIRemoteNotificationTypeAlert | UIRemoteNotificationTypeBadge | UIRemoteNotificationTypeSound];

	iOS 8의 경우
   
		UIUserNotificationSettings *settings = [UIUserNotificationSettings settingsForTypes:UIUserNotificationTypeSound 
												 UIUserNotificationTypeAlert |
												 UIUserNotificationTypeBadge 
												categories:nil];
 
    	[[UIApplication sharedApplication] registerUserNotificationSettings:settings];
    	[[UIApplication sharedApplication] registerForRemoteNotifications];

6. 같은 파일에 다음 메서드를 추가합니다.

	    - (void)application:(UIApplication *)application didRegisterForRemoteNotificationsWithDeviceToken:(NSData *) deviceToken {
		    SBNotificationHub* hub = [[SBNotificationHub alloc] initWithConnectionString:
		                              @"<connection string>" notificationHubPath:@"mynh"];

		    [hub registerNativeWithDeviceToken:deviceToken tags:nil completion:^(NSError* error) {
		        if (error != nil) {
		            NSLog(@"Error registering for notifications: %@", error);
		        }
	    	}];
		}

7. *\(옵션\)* 역시 같은 파일에서, 앱이 활성 상태일 때 알림이 수신되는 경우 **UIAlert**을 표시하려면 다음 메서드를 추가합니다.


        - (void)application:(UIApplication *)application didReceiveRemoteNotification: (NSDictionary *)userInfo {
		    NSLog(@"%@", userInfo);
		    UIAlertView *alert = [[UIAlertView alloc] initWithTitle:@"Notification" message:
		    [[userInfo objectForKey:@"aps"] valueForKey:@"alert"] delegate:nil cancelButtonTitle:
		    @"OK" otherButtonTitles:nil, nil];
		    [alert show];
		}

8. 장치에서 앱을 실행합니다.

##백 엔드에서 알림 보내기

[REST 인터페이스](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예제는 **모바일 서비스에서 푸시 알림 시작**\([.NET 백 엔드](mobile-services-javascript-backend-ios-get-started-push.md) \| [JavaScript 백 엔드](mobile-services-javascript-backend-ios-get-started-push.md)\)을 참조하세요. REST API를 사용하여 알림을 보내는 방법에 대한 예제는 **Java/PHP에서 알림 허브를 사용하는 방법**\([Java](notification-hubs-java-backend-how-to.md) \| [PHP](notification-hubs-php-backend-how-to.md)\)을 참조하세요.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**, **프로젝트...**를 차례로 선택하고 **Visual C\#** 아래에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.  

   ![][20]

	그러면 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.

2. **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

	그러면 패키지 관리자 콘솔이 표시됩니다.

6. 콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus

	이 명령은 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다.

5. Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

6. **Program** 클래스에서 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

	"hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다. 또한 연결 문자열 자리 표시자를 "알림 허브 구성" 섹션에서 가져온 **DefaultFullSharedAccessSignature**라는 연결 문자열로 바꿉니다.

	>[AZURE.NOTE]**수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 수 있는 권한이 없습니다.

5. 그런 다음 **Main** 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 키를 눌러 콘솔 응용 프로그램을 실행합니다.

	장치에 대한 경고를 받게 됩니다. Wi-Fi를 사용하는 경우 연결에 문제가 없는지 확인하세요.

Apple [Local and Push Notification Programming Guide]에서 가능한 모든 페이로드를 찾아볼 수 있습니다.

##다음 단계

이 간단한 예제에서는 모든 iOS 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시] 자습서를 참조하세요. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기]를 참조하세요. [알림 허브 지침]에서 알림 허브 사용 방법을 자세히 알아보세요.



<!-- Images. -->
[5]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step5.png
[6]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step6.png
[7]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step7.png

[9]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step9.png
[10]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step10.png


[18]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-step18.png
[26]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png


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
[모바일 서비스 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[알림 허브를 사용하여 사용자에게 알림 푸시]: notification-hubs-ios-mobile-services-register-user-push-notifications.md
[알림 허브를 사용하여 뉴스 속보 보내기]: notification-hubs-ios-send-breaking-news.md

[Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1


<!--HONumber=52-->
