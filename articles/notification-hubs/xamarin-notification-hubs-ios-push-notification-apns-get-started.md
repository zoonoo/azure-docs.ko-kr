---
title: "Xamarin.iOS 앱에 대한 Azure Notification Hubs 시작 | Microsoft Docs"
description: "이 자습서에서 Azure Notification Hubs를 사용하여 Xamarin iOS 응용 프로그램에 푸시 알림을 보내는 방법을 알아봅니다."
services: notification-hubs
keywords: "ios 푸시 알림, 푸시 메시지, 푸시 알림, 푸시 메시지"
documentationcenter: xamarin
author: jwhiteDev
manager: kpiteira
editor: 
ms.assetid: 4d4dfd42-c5a5-4360-9d70-7812f96924d2
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 12/22/2017
ms.author: jawh
ms.openlocfilehash: edb48cd8de9b1f7357c40cea73fa4b9a422cb8fa
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2018
---
# <a name="get-started-with-azure-notification-hubs-for-xamarinios-apps"></a>Xamarin.iOS 앱에 대한 Azure Notification Hubs 시작
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>개요
> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started)을 참조하세요.
> 
> 

이 자습서에서는 Azure Notification Hubs를 사용하여 iOS 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. [APNS(Apple Push Notification Service)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html)를 사용하여 푸시 알림을 받는 빈 Xamarin.iOS 앱을 만듭니다. 

완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. 완성된 코드는 [NotificationHubs 앱][GitHub] 샘플에서 사용할 수 있습니다.

이 자습서에서는 Notification Hubs를 사용하는 간단한 푸시 메시지 브로드캐스트 시나리오를 보여 줍니다.

## <a name="prerequisites"></a>필수 조건
이 자습서를 사용하려면 다음이 필요합니다.

* 최신 버전의 [XCode][Install Xcode]
* iOS 10 이상 호환 장치
* [Apple 개발자 프로그램](https://developer.apple.com/programs/) 멤버 자격
* [Mac용 Visual Studio]
  
  > [!NOTE]
  > iOS 푸시 알림에 대한 구성 요구 사항 때문에 시뮬레이터 대신 실제 iOS 장치(iPhone 또는 iPad)에서 응용 프로그램 예제를 배포 및 테스트해야 합니다.
  > 
  > 

먼저 이 자습서를 완료해야 Xamarin.iOS 앱에 대한 다른 모든 Notification Hubs 자습서를 진행할 수 있습니다.

[!INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]

## <a name="configure-your-notification-hub-for-ios-push-notifications"></a>iOS 푸시 알림에 대해 알림 허브 구성
이 섹션에서는 새 알림 허브를 만들고, APNS와 함께 이전에 만든 **.p12** 푸시 인증서를 사용하여 인증을 구성하는 단계를 안내합니다. 이미 만든 알림 허브를 사용하려는 경우 5단계로 건너뛸 수 있습니다.

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">

<li>

<p><b>알림 서비스</b> 단추를 클릭한 다음 <b>Apple(APNS)</b>을 선택합니다. <b>인증서</b>를 선택하고, 파일 아이콘을 클릭한 다음, 이전에 내보낸 <b>.p12</b> 파일을 선택합니다. 또한 올바른 암호를 지정합니다.</p>

<p>개발용이므로 <b>샌드박스</b> 모드를 선택합니다. 스토어에서 앱을 구매한 사용자에게 푸시 알림을 보내려는 경우에만 <b>프로덕션</b>을 사용합니다.</p>
</li>
</ol>

&emsp;&emsp;&emsp;&emsp;![Azure Portal에서 APNS 구성][6]

&emsp;&emsp;&emsp;&emsp;![Azure Portal에서 APNS 인증 구성][7]

이제 알림 허브가 APNS와 작동하도록 구성되었으며 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.

## <a name="connect-your-app-to-the-notification-hub"></a>알림 허브에 앱 연결
#### <a name="create-a-new-project"></a>새 프로젝트 만들기
1. Visual Studio에서 새 iOS 프로젝트를 만들고, **단일 뷰 앱** 템플릿을 선택하고, **다음**을 클릭합니다.
   
     ![Visual Studio - 응용 프로그램 유형 선택][31]

2. 앱 이름 및 조직 식별자를 입력하고 **다음**, **만들기**를 차례로 누릅니다.

3. [솔루션] 보기에서 *Into.plist*를 두 번 클릭하고, **ID** 아래에서 번들 식별자가 프로비전 프로필을 만들 때 사용한 식별자와 일치하는지 확인합니다. **서명** 아래에서 **팀**에 대해 개발자 계정이 선택되고, "자동으로 서명 관리"가 선택되고, 서명 인증서 및 프로비전 프로필이 자동으로 선택되어 있는지 확인합니다.

    ![Visual Studio - iOS 앱 구성][32]

4. Azure Messaging 패키지를 추가합니다. [솔루션] 보기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가** > **NuGet 패키지 추가**를 차례로 선택합니다. **Xamarin.Azure.NotificationHubs.iOS**를 검색하고 프로젝트에 이 패키지를 추가합니다.

5. 클래스에 새 파일을 추가하고, 이름을 **Constants.cs**로 지정하고, 다음 변수를 추가하고, 문자열 리터럴 자리 표시자를 *허브 이름* 및 앞에서 언급한 *DefaultListenSharedAccessSignature*로 바꿉니다.
   
    ```csharp
        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";
    ```

6. **AppDelegate.cs**에서 다음 using 문을 추가합니다.
   
    ```csharp
        using WindowsAzure.Messaging;
    ```

7. **SBNotificationHub**인스턴스를 선언합니다.
   
    ```csharp
        private SBNotificationHub Hub { get; set; }
    ```

8. **AppDelegate.cs**에서 **FinishedLaunching()**을 다음과 일치하도록 업데이트합니다.
   
    ```csharp
        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());
   
                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }
   
            return true;
        }
    ```

9. **AppDelegate.cs**의 **RegisteredForRemoteNotifications()** 메서드를 재정의합니다.
   
    ```csharp
        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);
   
            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }
   
                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }
    ```

10. **AppDelegate.cs**의 **ReceivedRemoteNotification()** 메서드를 재정의합니다.
   
    ```csharp
        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }
    ```

11. **AppDelegate.cs**에 다음 **ProcessNotification()** 메서드를 만듭니다.
   
    ```csharp
        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;
   
                string alert = string.Empty;
   
                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();
   
                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }
    ```
   > [!NOTE]
   > 네트워크 연결이 없는 경우를 처리하도록 **FailedToRegisterForRemoteNotifications()** 를 재정의할 수 있습니다. 이는 사용자가 응용 프로그램을 오프라인 모드(예: 비행기)에서 시작할 수 있고 앱에 특정한 푸시 메시지 시나리오를 처리하려는 경우에 특히 중요합니다.
  

12. 장치에서 앱을 실행합니다.

## <a name="sending-test-push-notifications"></a>테스트 푸시 알림 보내기
[Azure Portal]에서 *테스트 보내기* 옵션을 사용하여 앱에서 알림 수신을 테스트할 수 있습니다. 이렇게 하면 테스트 푸시 알림을 장치에 보냅니다.

![Azure Portal - 보내기 테스트][30]

푸시 알림은 일반적으로 호환 라이브러리를 사용하는 Mobile Apps 또는 ASP.NET과 같은 백 엔드 서비스에서 전송됩니다. 백 엔드에서 라이브러리를 사용할 수 없는 경우 REST API를 직접 사용하여 알림 메시지를 보낼 수도 있습니다.

ASP.NET 백 엔드에서 알림을 보내기 위한 다음 단계로서 [Notification Hubs를 사용하여 사용자에게 알림 푸시](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) 자습서를 확인하는 것이 좋습니다. 그러나 다음 접근 방식을 사용하여 알림을 보낼 수 있습니다.

알림을 보내기 위해 검토할 수 있는 다른 자습서 목록은 다음과 같습니다.
* REST 인터페이스: [REST 인터페이스](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx)를 사용하여 모든 백 엔드 플랫폼에서 푸시 알림을 지원할 수 있습니다.
* **Microsoft Azure Notification Hubs .NET SDK**: Visual Studio용 Nuget 패키지 관리자에서 [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)를 실행합니다.
* Node.js: [Node.js에서 Notification Hubs를 사용하는 방법](notification-hubs-nodejs-push-notification-tutorial.md)
* Java/PHP: REST API를 사용하여 푸시 알림을 보내는 방법에 대한 예제는 "Java/PHP에서 Notification Hubs를 사용하는 방법"([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md))을 참조하세요.

## <a name="next-steps"></a>다음 단계
이 간단한 예제에서는 모든 iOS 장치로 포시 알림을 브로드캐스트합니다. 특정 사용자를 대상으로 하려면 [Notification Hubs를 사용하여 사용자에게 알림 푸시](영문) 자습서를 참조하세요. 사용자를 관심 그룹별로 분할하려면 [Notification Hubs를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오. Notification Hubs 사용 방법에 대해 자세히 알아보려면 [Notification Hubs 지침] 및 [iOS용 Notification Hubs 방법]을 참조하세요.

<!-- Images. -->
[6]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config.png
[7]: ./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png
[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png

[30]: ./media/notification-hubs-ios-get-started/notification-hubs-test-send.png
[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png
[32]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-app-settings.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Windows Azure Messaging Framework]: http://go.microsoft.com/fwlink/?LinkID=799698&clcid=0x409
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Notification Hubs 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[iOS용 Notification Hubs 방법]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mac용 Visual Studio]: https://www.visualstudio.com/vs/visual-studio-mac/
[Notification Hubs를 사용하여 사용자에게 알림 푸시]: /manage/services/notification-hubs/notify-users-aspnet
[Notification Hubs를 사용하여 뉴스 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet

[Local and Push Notification Programming Guide]:https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/HandlingRemoteNotifications.html#//apple_ref/doc/uid/TP40008194-CH6-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: https://github.com/xamarin/mobile-samples/tree/master/Azure/NotificationHubs
[Azure Portal]: https://portal.azure.com
