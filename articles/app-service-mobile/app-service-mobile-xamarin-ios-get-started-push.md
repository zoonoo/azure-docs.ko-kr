<properties 
	pageTitle="Azure 앱 서비스를 사용하여 Xamarin.iOS 앱에 푸시 알림 추가" 
	description="Azure 앱 서비스를 사용하여Xamarin.iOS 앱에 푸시 알림을 전송하는 방법을 알아봅니다." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="12/18/2015" 
	ms.author="wesmc"/>

# Xamarin.iOS 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##개요

이 자습서는 먼저 완료해야 하는 [Xamarin.iOS 빠른 시작 자습서](app-service-mobile-xamarin-ios-get-started.md)를 기반으로 합니다. 푸시 알림을 Xamarin.iOS 빠른 시작 프로젝트에 추가하여 레코드가 삽입될 때마다 푸시 알림이 전송됩니다. 다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

##필수 조건

이 자습서를 완료하려면 다음이 필요합니다.

* 활성 Azure 계정. 계정이 아직 없으면 Azure 평가판에 등록하고 최대 10개의 무료 모바일 앱 백 엔드를 가져옵니다. 평가판이 종료된 후에도 계속 사용할 수 있습니다. [Azure 무료 평가판](https://azure.microsoft.com/pricing/free-trial/)을 참조하세요.

* [Xamarin Studio] 및 [Xcode] v4.4 이상이 설치되어 있는 Mac. 원하는 경우 Windows 컴퓨터에서 Visual Studio를 사용하여 Xamarin.iOS 앱을 실행할 수 있지만 Xamarin.iOS 빌드 호스트를 실행하는 네트워크에 연결된 Mac에 연결해야 하기 때문에 좀 더 복잡합니다. 실행에 대해 더 자세히 보려면 [Windows에서 Xamarin.iOS 설치]를 참조하세요.

* 실제 iOS 장치. 푸시 알림은 iOS 시뮬레이터에서 지원되지 않습니다.

* [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/)에는 APNS(Apple 푸시 알림 서비스)의 등록이 필요합니다.

* [Xamarin.iOS 빠른 시작 자습서](app-service-mobile-xamarin-ios-get-started.md)를 완료합니다.


##Apple 개발자 포털의 푸시 알림에 대한 앱 등록

[AZURE.INCLUDE [알림 허브 Xamarin이 Apple 푸시 알림 사용](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]

##푸시 알림을 전송하도록 모바일 앱 구성

알림을 보내도록 앱을 구성하려면 새 허브를 만들어 사용하려는 플랫폼 알림 서비스로 구성합니다.

1. [Azure 포털](https://portal.azure.com/)에서 **찾아보기** > **모바일 앱** > 모바일 앱 > **설정** > **모바일** > **푸시** > **알림 허브** > **+ 알림 허브**를 클릭하고, 새 알림 허브에 대한 이름 및 네임스페이스를 입력한 다음 **확인** 단추를 클릭합니다.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. 알림 허브 만들기 블레이드에서 **만들기**를 클릭합니다.

3. **푸시** > **Apple(APNS)** > **인증서 업로드**를 클릭합니다. 앞에서 내보낸 .p12 푸시 인증서 파일을 업로드합니다. 개발 및 테스트에 대한 개발 푸시 인증서를 만든 경우 **샌드박스**를 선택합니다. 그렇지 않은 경우 **프로덕션**을 선택합니다.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)

이제 iOS의 푸시 알림과 작동하도록 서비스가 구성되었습니다.

##푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##Xamarin.iOS 프로젝트 구성

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##앱에 푸시 알림 추가

1. **QSTodoService**에서 **AppDelegate**가 모바일 클라이언트를 가져올 수 있도록 다음 속성을 추가합니다.
        
            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. 다음 `using` 문을 **AppDelegate.cs** 파일의 맨 위에 추가합니다.

		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. **AppDelegate**에서 **FinishedLaunching** 이벤트를 재정의합니다.

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert 
                | UIUserNotificationType.Badge 
                | UIUserNotificationType.Sound, 
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. 동일한 파일에서 **RegisteredForRemoteNotifications** 이벤트를 재정의합니다. 이 코드에서는 서버에서 지원하는 모든 플랫폼에서 전송되는 간단한 템플릿 알림을 등록하게 됩니다.
 
	알림 허브를 사용하는 템플릿에 대한 자세한 내용은 [템플릿](../notification-hubs/notification-hubs-templates.md)을 참조하세요.


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. 그런 다음 **DidReceivedRemoteNotification** 이벤트를 재정의합니다.

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## <a name="test"></a>앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.
	
	> [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

2. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.

4. 2단계를 반복하여 앱을 즉시 닫은 후 알림이 표시되는지 확인합니다.

이 자습서를 성공적으로 완료했습니다.

<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Windows에서 Xamarin.iOS 설치]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/


 

<!---HONumber=AcomDC_0128_2016-->