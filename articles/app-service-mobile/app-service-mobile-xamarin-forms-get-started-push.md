---
title: "Xamarin.Forms 앱에 푸시 알림 추가 | Microsoft Docs"
description: "Azure 서비스를 사용하여 Xamarin.Forms 앱에 다중 플랫폼 푸시 알림을 전송하는 방법을 알아봅니다."
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: d9b1ba9a-b3f2-4d12-affc-2ee34311538b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: f6989f65f80027be7803a889c0a87f6afbae150d
ms.openlocfilehash: 703ab64e96894df6c974fd441b737568fb905004
ms.lasthandoff: 12/13/2016


---
# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Xamarin.Forms 앱에 푸시 알림 추가
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>개요
이 자습서에서는 [Xamarin.Forms 빠른 시작](app-service-mobile-xamarin-forms-get-started.md)으로 인해 발생한 모든 프로젝트에 푸시 알림을 추가합니다. 즉, 레코드가 삽입될 때마다 모든 플랫폼 간 클라이언트로 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 푸시 알림 확장 패키지가 필요합니다. 자세한 내용은 [Azure Mobile Apps용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

## <a name="prerequisites"></a>필수 조건
iOS의 경우 [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/) 및 실제 iOS 장치가 필요합니다. [iOS 시뮬레이터는 푸시 알림을 지원하지 않습니다](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>알림 허브 구성
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="update-the-server-project-to-send-push-notifications"></a>푸시 알림을 전송하도록 서버 프로젝트 업데이트
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="configure-and-run-the-android-project-optional"></a>Android 프로젝트 구성 및 실행(선택 사항)
이 섹션을 완료하여 Android용 Xamarin.Forms Droid 프로젝트에 대한 푸시 알림을 사용하도록 설정합니다.

### <a name="enable-firebase-cloud-messaging-fcm"></a>FCM(Firebase Cloud Messaging) 사용
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

### <a name="configure-the-mobile-apps-back-end-to-send-push-requests-by-using-fcm"></a>FCM을 사용하여 푸시 요청을 보내도록 Mobile Apps 백 엔드 구성
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

### <a name="add-push-notifications-to-the-android-project"></a>Android 프로젝트에 푸시 알림 추가
FCM를 사용하여 백 엔드를 구성한 경우 FCM에 등록할 클라이언트에 구성 요소 및 코드를 추가할 수 있습니다. 또한 Mobile Apps 백 엔드를 통해 Azure Notification Hubs에 푸시 알림을 등록하고 알림을 수신할 수도 있습니다.

1. **Droid** 프로젝트에서 **구성 요소** 폴더를 마우스 오른쪽 단추로 클릭하고 **추가 구성 요소 가져오기...**를 클릭합니다. 그런 후 **Google Cloud Messaging 클라이언트** 구성 요소를 검색하고 프로젝트에 구성 요소를 추가합니다. 이 구성 요소는 Xamarin Android 프로젝트에 대한 푸시 알림을 지원합니다.
2. MainActivity.cs 프로젝트 파일을 열고 파일의 맨 위에 다음 문을 추가합니다.

        using Gcm.Client;
3. 다음 코드를 **LoadApplication**에 호출한 후에 **OnCreate** 메서드에 추가합니다.

        try
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }
4. 새 **CreateAndShowDialog** 도우미 메서드를 다음과 같이 추가합니다.

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }
5. 다음 코드를 **MainActivity** 클래스에 추가합니다.

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    현재 **MainActivity** 인스턴스를 노출하므로 주 UI 스레드에서 실행할 수 있습니다.
6. 다음과 같이 **OnCreate** 메서드 시작 부분에서 변수 `instance`를 초기화합니다.

        // Set the current instance of MainActivity.
        instance = this;
7. 새 클래스 파일을 `GcmService.cs`로 명명된 **Droid** 프로젝트에 추가하고 다음 **using** 문이 파일 맨 위에 있는지 확인합니다.

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;
8. 파일의 맨 위에서 **using** 문 뒤의 **네임스페이스** 선언 앞에 다음과 같은 사용 권한 요청을 추가합니다.

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
9. 네임스페이스에 다음과 같은 클래스 정의를 추가합니다.

       [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
       [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
       public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
       {
           public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
       }

   > [!NOTE]
   > **<PROJECT_NUMBER>**를 앞에서 설명한 프로젝트 번호로 바꿉니다.    
   >
   >
10. 빈 **GcmService** 클래스를 새 브로드캐스트 수신기를 사용하는 다음 코드로 바꿉니다.

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }
11. **GcmService** 클래스에 다음 코드를 추가합니다. 이렇게 하면 **OnRegistered** 이벤트 처리기가 재정의되고 **Register** 메서드가 구현됩니다.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration.
12. **OnMessage**를 구현하는 다음 코드를 추가합니다.

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    이 코드는 들어오는 알림을 처리하고 표시될 알림 관리자로 보냅니다.
13. **GcmServiceBase**를 사용하려면 **OnUnRegistered** 및 **OnError** 처리기 메서드를 구현해야 하며 다음과 같이 수행할 수 있습니다.

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

이제 Android 장치 또는 에뮬레이터에서 실행 중인 앱에서 푸시 알림을 테스트할 준비가 되었습니다.

### <a name="test-push-notifications-in-your-android-app"></a>Android 앱에서 푸시 알림 테스트
처음 두 단계는 에뮬레이터에서 테스트할 때만 필요합니다.

1. 아래와 같이 Android 가상 장치 관리자에서 대상으로 설정된 Google API가 있는 가상 장치에 배포하거나 해당 가상 장치에서 디버그해야 합니다.
2. **앱** > **설정** > **계정 추가**를 클릭하여 Android 장치에 Google 계정을 추가합니다. 그런 후 화면 지시에 따라 장치에 기존 Google 계정을 추가하거나 새 계정을 만듭니다.
3. Visual Studio 또는 Xamarin Studio에서 **Droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.
4. **실행**을 클릭하여 프로젝트를 빌드하고 Android 장치 또는 에뮬레이터에서 앱을 시작합니다.
5. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.
6. 항목이 추가될 때 알림을 받았는지 확인합니다.

## <a name="configure-and-run-the-ios-project-optional"></a>iOS 프로젝트 구성 및 실행(선택 사항)
이 섹션에서는 iOS 장치용 Xamarin iOS 프로젝트를 실행합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

[!INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

#### <a name="configure-the-notification-hub-for-apns"></a>APNS에 대한 알림 허브 구성
[!INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

다음에는 Xamarin Studio 또는 Visual Studio에서 iOS 프로젝트 설정을 구성합니다.

[!INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

#### <a name="add-push-notifications-to-your-ios-app"></a>iOS 앱에 푸시 알림 추가
1. **iOS** 프로젝트에서 AppDelegate.cs를 열고 다음 문을 코드 파일의 맨 위에 추가합니다.

        using Newtonsoft.Json.Linq;
2. 또한 **AppDelegate** 클래스에서 **RegisteredForRemoteNotifications** 이벤트 재정의를 추가하여 알림을 등록합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application,
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }
3. **AppDelegate**에서 **DidReceiveRemoteNotification** 이벤트 처리기에 대한 다음 재정의도 추가합니다.

        public override void DidReceiveRemoteNotification(UIApplication application,
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    이 메서드는 앱을 실행하는 동안 들어오는 알림을 처리합니다.
4. **AppDelegate** 클래스에서 다음 코드를 **FinishedLaunching** 메서드에 추가합니다.

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    이를 통해 원격 알림을 지원하고 푸시 등록을 요청합니다.

이제 앱이 푸시 알림을 지원하도록 업데이트됩니다.

#### <a name="test-push-notifications-in-your-ios-app"></a>iOS 앱에서 푸시 알림 테스트
1. iOS 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 눌러 프로젝트를 빌드하고 iOS 장치에서 앱을 시작합니다. 그런 다음 **확인**을 클릭하여 푸시 알림을 허용하도록 합니다.

   > [!NOTE]
   > 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.
   >
   >
3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.
4. 알림이 수신되는지 확인한 다음 **확인**을 클릭하여 알림을 해제합니다.

## <a name="configure-and-run-windows-projects-optional"></a>Windows 프로젝트 구성 및 실행(선택 사항)
이 섹션에서는 Windows 장치용 Xamarin.Forms WinApp 및 WinPhone81 프로젝트를 실행합니다. 이 단계에서는 UWP(범용 Windows 플랫폼) 프로젝트도 지원합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

#### <a name="register-your-windows-app-for-push-notifications-with-windows-notification-service-wns"></a>WNS(Windows 알림 서비스)를 통한 푸시 알림에 Windows 앱 등록
[!INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

#### <a name="configure-the-notification-hub-for-wns"></a>WNS에 대한 알림 허브 구성
[!INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

#### <a name="add-push-notifications-to-your-windows-app"></a>Windows 앱에 푸시 알림 추가
1. Visual Studio의 Windows 프로젝트에서 **App.xaml.cs**를 열고 다음 문을 추가합니다.

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    `<your_TodoItemManager_portable_class_namespace>`를 `TodoItemManager` 클래스가 들어 있는 이식 가능 프로젝트의 네임스페이스로 바꿉니다.
2. App.xaml.cs에서 다음 **InitNotificationsAsync** 메서드를 추가합니다.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS =
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    이 메서드는 푸시 알림 채널을 가져오고 알림 허브에서 템플릿 알림을 수신하기 위해 템플릿을 등록합니다. *messageParam* 을 지원하는 템플릿 알림이 이 클라이언트에 전달됩니다.
3. App.xaml.cs에서 `async` 한정자를 추가하여 **OnLaunched** 이벤트 처리기 메서드 정의를 업데이트합니다. 그런 후 다음 코드 줄을 메서드의 끝에 추가합니다.

        await InitNotificationsAsync();

    이렇게 하면 앱이 시작될 때마다 푸시 알림 등록이 생성되거나 새로 고쳐집니다. WNS 푸시 채널이 항상 활성화되도록 이 작업을 수행하는 것이 중요합니다.  
4. Visual Studio용 솔루션 탐색기에서 **Package.appxmanifest** 파일을 열고 **알림**에서 **알림 가능**을 **예**로 설정합니다.
5. 앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 Mobile Apps 백 엔드에서 템플릿 알림을 등록해야 합니다. 솔루션의 모든 Windows 프로젝트에 대해 이 섹션을 반복합니다.

#### <a name="test-push-notifications-in-your-windows-app"></a>Windows 앱에서 푸시 알림 테스트
1. Visual Studio에서 Windows 프로젝트를 마우스 오른쪽 단추로 클릭한 다음 **시작 프로젝트로 설정**을 클릭합니다.
2. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다.
3. 앱에서 새 todoitem에 대한 이름을 입력한 다음 더하기(**+**) 아이콘을 클릭하여 추가합니다.
4. 항목이 추가될 때 알림을 받았는지 확인합니다.

## <a name="next-steps"></a>다음 단계
푸시 알림에 대해 자세히 알아봅니다.

* [푸시 알림 문제 진단](../notification-hubs/notification-hubs-push-notification-fixer.md)  
  장치에서 알림이 삭제되거나 끝나지 않는 다양한 이유가 있습니다. 이 항목에서는 푸시 알림 실패의 근본 원인을 분석 및 파악하는 방법을 보여 줍니다.

다음 자습서 중 하나를 계속 진행할 수도 있습니다.

* [앱에 인증 추가 ](app-service-mobile-xamarin-forms-get-started-users.md)  
  ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.
* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Mobile Apps 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱&mdash;데이터 보기, 추가 또는 수정&mdash;과 같은 상호 작용을 수행할 수 있습니다.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

