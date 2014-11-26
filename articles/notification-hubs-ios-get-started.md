<properties linkid="develop-notificationhubs-tutorials-get-started-ios" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="krisragh" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="09/24/2014" ms.author="krisragh" />

# 알림 허브 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows 범용">Windows 범용</a><a href="/ko-KR/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-get-started/" title="iOS" class="current">iOS</a><a href="/ko-KR/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/ko-KR/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

이 항목에서는 Azure 알림 허브를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 APN(Apple 푸시 알림) 서비스를 사용하여 푸시 알림을 받는 빈 iOS 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [인증서 서명 요청 생성][인증서 서명 요청 생성]
2.  [앱을 등록하고 푸시 알림을 사용하도록 설정][앱을 등록하고 푸시 알림을 사용하도록 설정]
3.  [앱용 프로비저닝 프로필 만들기][앱용 프로비저닝 프로필 만들기]
4.  [알림 허브 구성][알림 허브 구성]
5.  [알림 허브에 앱 연결][알림 허브에 앱 연결]
6.  [백 엔드에서 알림 보내기][백 엔드에서 알림 보내기]

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 이 자습서를 사용하려면 다음 필수 조건이 필요합니다.

-   [모바일 서비스 iOS SDK][모바일 서비스 iOS SDK]
-   [XCode 4.5][XCode 4.5]
-   iOS 5.0(이상) 지원 장치
-   iOS 개발자 프로그램 멤버 자격
<div class="dev-callout"><b>참고</b><br /> <p>푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.</p><br /> </div>

</p>
이 자습서를 완료해야 다른 모든 iOS 앱용 알림 허브 자습서를 진행할 수 있습니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-KR/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

[WACOM.INCLUDE [Apple 푸시 알림 사용](../includes/enable-apple-push-notifications.md)]

## <a name="configure-hub"></a>알림 허브 구성

1.  Keychain Access에서 quickstart 앱의 새 인증서 **My Certificates**를 마우스 오른쪽 단추로 클릭합니다. **Export**를 클릭하고 파일의 이름을 지정한 후 **.p12** 형식을 선택하고 **Save**를 클릭합니다.

    ![][0]

  내보낸 인증서의 파일 이름과 위치를 적어둡니다.

> [WACOM.NOTE] 이 자습서에서는 QuickStart.p12 파일을 만듭니다. 파일 이름과 위치가 다를 수 있습니다.

1.  [Azure 관리 포털][Azure 관리 포털]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

    ![][1]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

    ![][2]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성** 탭을 클릭합니다.

    ![][3]

5.  맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

    ![][4]

6.  맨 위에 있는 **구성** 탭을 선택한 다음 Apple 알림 설정에 대해 **업로드**를 클릭합니다. 그런 다음 앞서 내보낸 **.p12** 인증서 및 인증서의 암호를 선택합니다. **프로덕션**(스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우) 또는 **샌드박스**(개발 중에) 중 어떤 푸시 서비스를 사용할지를 선택해야 합니다.

    ![][5]

7.  맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

    ![][6]

이제 알림 허브가 APN과 작동하도록 구성되었으며 앱을 등록하고 알림을 보내기 위한 연결 문자열이 있습니다.

## <a name="connecting-app"></a>알림 허브에 앱 연결

1.  XCode에서 새 iOS 프로젝트를 만들고 **Single View Application** 템플릿을 선택합니다.

    ![][7]

2.  **Targets**에서 프로젝트 이름을 클릭하고, **Code Signing Identity**를 확장한 다음, **Debug**에서 코드 서명 ID 프로필을 선택합니다. 추가로, XCode의 최신 버전을 사용하는 경우 **Levels**를 **Basic**에서 **All**로 전환하고 **Provisioning Profile** 라인 항목을 프로비저닝 프로필로 설정합니다.

    ![][8]

3.  iOS용 Azure Mobile SDK를 다운로드합니다. .zip 파일을 열고 WindowsAzureMessaging.framework 폴더를 XCode 프로젝트의 Framework 폴더로 끌어옵니다. **Copy items in destination group's folder**를 선택하고 **OK**를 클릭합니다.

    ![][9]

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

## <a name="send"></a>백 엔드에서 알림 보내기

[REST 인터페이스][REST 인터페이스]를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예는 **모바일 서비스에서 푸시 알림 시작**([.NET 백 엔드][.NET 백 엔드] | [JavaScript 백 엔드][.NET 백 엔드])을 참조하세요. REST API를 사용하여 알림을 보내는 방법에 대한 예는 **Java/PHP에서 알림 허브를 사용하는 방법**([Java][Java] | [PHP][PHP])을 참조하세요.

1.  Visual Studio의 **파일** 메뉴에서 **새로 만들기**, **프로젝트...**를 차례로 선택하고 **Visual C#** 아래에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.

    [][20]

    그러면 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.

2.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

    그러면 패키지 관리자 콘솔이 표시됩니다.

3.  콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus

    이 명령은 [WindowsAzure.ServiceBus NuGet 패키지][WindowsAzure.ServiceBus NuGet 패키지]를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다.

4.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

5.  **Program** 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

    "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다. 또한 연결 문자열 자리 표시자를 "알림 허브 구성" 섹션에서 얻은 **DefaultFullSharedAccessSignature**라는 연결 문자열로 바꿉니다.

    > [WACOM.NOTE]**수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 권한이 없습니다.

6.  그런 다음 **Main** 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
         Console.ReadLine();

7.  F5 키를 눌러 콘솔 응용 프로그램을 실행합니다.

    장치에 대한 경고를 받게 됩니다. Wi-Fi를 사용하는 경우 연결에 문제가 없는지 확인하십시오.

Apple [Local and Push Notification Programming Guide][Local and Push Notification Programming Guide]에서 가능한 모든 페이로드를 찾아볼 수 있습니다.

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 iOS 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시][알림 허브를 사용하여 사용자에게 알림 푸시] 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기][알림 허브를 사용하여 뉴스 속보 보내기]를 참조하십시오. [알림 허브 지침][알림 허브 지침]에서 알림 허브 사용 방법을 자세히 알아보십시오.

 
 


  [인증서 서명 요청 생성]: #certificates
  [앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
  [앱용 프로비저닝 프로필 만들기]: #profile
  [알림 허브 구성]: #configure-hub
  [알림 허브에 앱 연결]: #connecting-app
  [백 엔드에서 알림 보내기]: #send
  [모바일 서비스 iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
  [XCode 4.5]: https://go.microsoft.com/fwLink/p/?LinkID=266532
  [0]: ./media/notification-hubs-ios-get-started/mobile-services-ios-push-27.png
  [Azure 관리 포털]: https://manage.windowsazure.com/
  [1]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-ios-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-ios-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-ios-get-started/notification-hub-configure-ios.png
  [6]: ./media/notification-hubs-ios-get-started/notification-hub-connection-strings.png
  [7]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app.png
  [8]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app2.png
  [9]: ./media/notification-hubs-ios-get-started/notification-hub-create-ios-app3.png
  [REST 인터페이스]: http://msdn.microsoft.com/ko-KR/library/windowsazure/dn223264.aspx
  [.NET 백 엔드]: /ko-KR/documentation/articles/mobile-services-javascript-backend-ios-get-started-push/
  [Java]: /ko-KR/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ko-KR/documentation/articles/notification-hubs-php-backend-how-to/
  [WindowsAzure.ServiceBus NuGet 패키지]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [Local and Push Notification Programming Guide]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
  [알림 허브를 사용하여 사용자에게 알림 푸시]: /ko-KR/manage/services/notification-hubs/notify-users-aspnet
  [알림 허브를 사용하여 뉴스 속보 보내기]: /ko-KR/manage/services/notification-hubs/breaking-news-dotnet
  [알림 허브 지침]: http://msdn.microsoft.com/ko-KR/library/jj927170.aspx
