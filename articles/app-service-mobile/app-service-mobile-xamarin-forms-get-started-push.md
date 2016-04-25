<properties
	pageTitle="Azure 앱 서비스를 사용하여 Xamarin.Forms 앱에 푸시 알림 추가"
	description="Azure 앱 서비스를 사용하여Xamarin.Forms 앱에 푸시 알림을 전송하는 방법을 알아봅니다."
	services="app-service\mobile"
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="wesmc"/>

# Xamarin.Forms 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##개요

이 자습서는 먼저 완료해야 하는 [Xamarin.Forms 빠른 시작 자습서](app-service-mobile-xamarin-forms-get-started.md)를 기반으로 합니다. 지원하려는 각 프로젝트의 푸시 알림 지원을 Xamarin.Forms 빠른 시작 프로젝트에 추가합니다. 레코드가 삽입될 때마다 푸시 알림이 전송됩니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

[iOS 시뮬레이터는 푸시 알림을 지원하지 않으므로](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html) 실제 iOS 장치를 사용해야 합니다. [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/)에 등록해야 합니다.

##필수 조건

* 다른 필수 조건을 나열하는 [Xamarin.Forms 앱 만들기](app-service-mobile-xamarin-forms-get-started.md) 자습서를 완료합니다. 이 문서에서는 해당 자습서에서 완성된 앱을 사용합니다.

* 실제 iOS 장치. 푸시 알림은 iOS 시뮬레이터에서 지원되지 않습니다.

##모바일 앱에 대한 알림 허브 만들기

알림을 보내도록 앱을 구성하려면 새 알림 허브를 만들어 사용하려는 플랫폼 알림 서비스로 구성합니다.

이 단계에서는 새 알림 허브를 만드는 과정을 안내합니다. 이미 만든 경우에는 기존 알림 허브를 선택할 수 있습니다.

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **찾아보기** > **모바일 앱** > 백 엔드 > **설정** > **모바일** > **푸시** > **알림 허브** > **+ 알림 허브**를 차례로 클릭하고 새 알림 허브에 대한 이름 및 네임스페이스를 제공한 다음 **확인** 단추를 클릭합니다.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-configure-notification-hub.png)

2. 알림 허브 만들기 블레이드에서 **만들기**를 클릭합니다.


##푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(선택 사항) Android 프로젝트 구성 및 실행

이 섹션에서는 Android용 Xamarin Android 프로젝트를 실행합니다. Android 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.


####GCM(Google Cloud Messaging) 사용


[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


####GCM에 대한 알림 허브 구성

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **찾아보기** > **모바일 앱** > 사용 중인 모바일 앱 > **설정** > **푸시** > **Google(GCM)**을 차례로 클릭합니다. 이전에 만든 서버 API 키를 붙여넣고 **저장**을 클릭합니다. 이제 Android용 푸시 알림과 작동하도록 서비스가 구성되었습니다.

	![](./media/app-service-mobile-xamarin-forms-get-started-push/mobile-app-save-gcm-api-key.png)


####droid 프로젝트에 푸시 알림 추가

1. Components 폴더를 마우스 오른쪽 단추로 클릭하고 Get More Components...를 클릭하고 **Google Cloud Messaging Client** 구성 요소를 검색하여 이를 프로젝트에 추가합니다. 이 구성 요소는 Xamarrin Android 프로젝트를 사용하여 푸시 알림 작동을 단순화하는 데 도움이 됩니다.

2. MainActivity.cs 프로젝트 파일을 열고 파일의 맨 위에 있는 문을 사용하여 다음을 추가합니다.

		using Gcm.Client;

3. `LoadApplication` 호출 뒤의 `OnCreate` 메서드에 다음 코드를 추가합니다.

	    try
	    {
	        // Check to ensure everything's setup right
	        GcmClient.CheckDevice(this);
	        GcmClient.CheckManifest(this);

	        // Register for push notifications
	        System.Diagnostics.Debug.WriteLine("Registering...");
	        GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
	    }
	    catch (Java.Net.MalformedURLException)
	    {
	        CreateAndShowDialog("There was an error creating the Mobile Service. Verify the URL", "Error");
	    }
	    catch (Exception e)
	    {
	        CreateAndShowDialog(e.Message, "Error");
	    }


4. `CreateAndShowDialog` 도우미 메서드에 대해 다음 코드를 추가합니다.

		private void CreateAndShowDialog(String message, String title)
		{
			AlertDialog.Builder builder = new AlertDialog.Builder(this);

			builder.SetMessage (message);
			builder.SetTitle (title);
			builder.Create().Show ();
		}


5. `MainActivity` 클래스에서 주 UI 스레드에 대해 몇몇 클래스를 실행할 수 있도록 다음 코드를 추가하여 현재 `MainActivity`를 노출합니다.

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

6. `MainActivity.OnCreate` 메서드 시작 부분의 변수 `instance`을 초기화합니다.

		// Set the current instance of MainActivity.
		instance = this;

7. 새 클래스 파일을 **Droid** 프로젝트에 추가합니다. 새 클래스 파일의 이름을 **GcmService**로 지정합니다.

8. 파일의 맨 위에 다음과 같은 `using` 문이 포함되었는지 확인합니다.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;
		using Android.App;
		using Android.Content;
		using System.Collections.Generic;
		using System.Diagnostics;
		using Android.Util;
		using Newtonsoft.Json.Linq;
		using System.Text;
		using System.Linq;

9. 파일의 맨 위에서 `using` 문 뒤의 `namespace` 선언 앞에 다음과 같은 사용 권한 요청을 추가합니다.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

10. 네임스페이스에 다음과 같은 클래스 정의를 추가합니다. **<PROJECT_NUMBER>**을 앞에서 설명한 프로젝트 번호로 바꿉니다.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

11. 새 브로드캐스트 수신기를 사용하도록 `GcmService` 클래스를 업데이트합니다.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. OnRegistered 이벤트 처리기를 재정의하고 `Register` 메서드를 구현하는 다음 코드를 GcmService 클래스에 추가합니다.

	이 코드는 `messageParam` 매개 변수를 사용하여 템플릿 알림을 수신하기 위해 템플릿 본문을 등록합니다. 템플릿 알림을 사용하여 플랫폼 간에 알림을 보낼 수 있습니다. 자세한 내용은 [템플릿](https://msdn.microsoft.com/library/azure/dn530748.aspx)을 사용하세요.

		protected override void OnRegistered(Context context, string registrationId)
		{
		    Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
		    RegistrationID = registrationId;

		    createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

		    MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));

		}

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{"data":{"message":"$(messageParam)"}}";

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

13. 들어오는 푸시 알림을 처리하려면 `OnMessage`를 구현해야 합니다. 이 코드에서는 알림을 처리하고 이를 알림 관리자에게 전송합니다.

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

		    //Create the notification
		    var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

		    //Auto cancel will remove the notification once the user touches it
		    notification.Flags = NotificationFlags.AutoCancel;

		    //Set the notification info
		    //we use the pending intent, passing our ui intent over which will get called
		    //when the notification is tapped.
		    notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

		    //Show the notification
		    notificationManager.Notify(1, notification);
		}

14. 또한 수신기에 대해 `OnUnRegistered` 및 `OnError` 처리기를 구현해야 합니다.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}



####Android 앱에서 푸시 알림 테스트

1. Visual Studio 또는 Xamarin Studio에서 **droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

2. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

	> [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

2. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.





##(선택 사항) iOS 프로젝트 구성 및 실행

이 섹션에서는 iOS 장치용 Xamarin iOS 프로젝트를 실행합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

[AZURE.INCLUDE [알림 허브 Xamarin이 Apple 푸시 알림 사용](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####APNS에 대한 알림 허브 구성

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **찾아보기** > **모바일 앱** > 사용 중인 모바일 앱 > **설정** > **푸시** > **Apple(APNS)** > **인증서 업로드**를 차례로 클릭합니다. 앞에서 내보낸 .p12 푸시 인증서 파일을 업로드합니다. 개발 및 테스트에 대한 개발 푸시 인증서를 만든 경우 **샌드박스**를 선택합니다. 그렇지 않은 경우 **프로덕션**을 선택합니다. 이제 iOS의 푸시 알림과 작동하도록 서비스가 구성되었습니다.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	다음에는 Xamarin Studio 또는 Visual Studio에서 iOS 프로젝트 설정을 구성합니다.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####iOS 앱에 푸시 알림 추가

1. 다음 `using` 문을 **AppDelegate.cs** 파일의 맨 위에 추가합니다.

        using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;


2. iOS 프로젝트에서 AppDelegate.cs를 열고 다음과 같이 원격 알림을 지원하도록 `FinishedLaunching`을 업데이트합니다.

		public override bool FinishedLaunching (UIApplication app, NSDictionary options)
		{
			global::Xamarin.Forms.Forms.Init ();

			Microsoft.WindowsAzure.MobileServices.CurrentPlatform.Init();

            // IMPORTANT: uncomment this code to enable sync on Xamarin.iOS
            // For more information, see: http://go.microsoft.com/fwlink/?LinkId=620342
            //SQLitePCL.CurrentPlatform.Init();

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

			LoadApplication (new App ());

			return base.FinishedLaunching (app, options);
		}


4. 또한 AppDelegate.cs에서 알림을 등록하기 위한 **RegisteredForRemoteNotifications** 이벤트 재정의를 추가합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            const string templateBodyAPNS = "{"aps":{"alert":"$(messageParam)"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. 또한 AppDelegate.cs에서 앱이 실행하는 동안 들어오는 알림을 처리하기 위한 **DidReceivedRemoteNotification** 이벤트 재정의를 추가합니다.

        public override void DidReceiveRemoteNotification(UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
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

이제 앱이 푸시 알림을 지원하도록 업데이트됩니다.

####iOS 앱에서 푸시 알림 테스트

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.

2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

	> [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

4. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.




##(선택 사항) Windows 프로젝트 구성 및 실행

이 섹션에서는 Windows 장치용 Xamarin WinApp 프로젝트를 실행합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.


####WNS를 사용하여 푸시 알림에 대해 Windows 앱 등록

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####WNS에 대한 알림 허브 구성

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Windows 앱에 푸시 알림 추가

1. Visual Studio에서 **WinApp** 프로젝트의 **App.xaml.cs**를 엽니다. 다음 `using` 문을 추가합니다.

		using System.Threading.Tasks;
		using Windows.Networking.PushNotifications;
		using WesmcMobileAppGaTest;
		using Microsoft.WindowsAzure.MobileServices;
		using Newtonsoft.Json.Linq;

2. App.xaml.cs에서 다음 `InitNotificationsAsync` 메서드를 추가합니다. 이 메서드는 푸시 알림 채널을 가져오고 알림 허브에서 템플릿 알림을 수신하기 위해 템플릿을 등록합니다. `messageParam`를 지원하는 템플릿 알림이 이 클라이언트에 전달됩니다.

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = "<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyWNS},
                  {"headers", headers} // Only needed for WNS & MPNS
                };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush().RegisterAsync(channel.Uri, templates);
        }

3. App.xaml.cs에서 `async` 특성으로 `OnLaunched` 이벤트 처리기를 업데이트하고 `InitNotificationsAsync`을 호출합니다.

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            Frame rootFrame = Window.Current.Content as Frame;

            // Do not repeat app initialization when the Window already has content,
            // just ensure that the window is active
            if (rootFrame == null)
            {
                // Create a Frame to act as the navigation context and navigate to the first page
                rootFrame = new Frame();
                // Set the default language
                rootFrame.Language = Windows.Globalization.ApplicationLanguages.Languages[0];
                rootFrame.NavigationFailed += OnNavigationFailed;
                Xamarin.Forms.Forms.Init(e);
                if (e.PreviousExecutionState == ApplicationExecutionState.Terminated)
                {
                    //TODO: Load state from previously suspended application
                }
                // Place the frame in the current Window
                Window.Current.Content = rootFrame;
            }

            if (rootFrame.Content == null)
            {
                // When the navigation stack isn't restored navigate to the first page,
                // configuring the new page by passing required information as a navigation
                // parameter
                rootFrame.Navigate(typeof(MainPage), e.Arguments);
            }
            // Ensure the current window is active
            Window.Current.Activate();

            await InitNotificationsAsync();
        }

4. Visual Studio용 솔루션 탐색기에서 **Package.appxmanifest** 파일을 열고 **알림** 아래에서 **알림 가능**을 **예**로 설정합니다.

5. 앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 모바일 앱 백 엔드에서 템플릿 알림을 등록해야 합니다.


####Windows 앱에서 푸시 알림 테스트

1. Visual Studio에서 **WinApp** 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.


2. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

	> [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

4. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.



<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0413_2016-->