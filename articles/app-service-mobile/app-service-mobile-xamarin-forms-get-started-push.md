<properties
	pageTitle="Xamarin.Forms 앱에 푸시 알림 추가 | Microsoft Azure"
	description="Azure 서비스를 사용하여 Xamarin.Forms 앱에 다중 플랫폼 푸시 알림을 전송하는 방법을 알아봅니다."
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
	ms.date="06/20/2016"
	ms.author="wesmc"/>

# Xamarin.Forms 앱에 푸시 알림 추가

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##개요

이 자습서에서는 Azure 서비스를 사용하여 다양한 네이티브 장치 플랫폼, Android, iOS 및 Windows에서 실행 중인 Xamarin.Forms 앱으로 푸시 알림을 보내는 방법을 보여 줍니다. 푸시 알림은 Azure 알림 허브를 사용하여 Azure 모바일 앱 백 엔드에서 전송됩니다. 다양한 푸시 알림 서비스(PNS)를 사용하는 모든 플랫폼에서 실행되는 장치에 동일한 메시지를 전송할 수 있도록 템플릿 등록이 사용됩니다. 크로스 플랫폼 푸시 알림 전송에 대한 자세한 내용은 [Azure 알림 허브](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) 설명서를 참조하세요.

Xamarin.Forms 앱에서 지원하는 모든 프로젝트에 푸시 알림을 추가합니다. 백 엔드에서 레코드가 삽입될 때마다 푸시 알림이 전송됩니다.

##필수 조건

이 자습서를 통한 최상의 결과를 얻기 위해 먼저 [Xamarin.Forms 앱 만들기](app-service-mobile-xamarin-forms-get-started.md) 자습서를 완료하는 것이 좋습니다. 이 자습서를 완료하면 다중 플랫폼 TodoList 앱인 Xamarin.Forms 프로젝트가 생깁니다.

다운로드한 빠른 시작 서버 프로젝트를 사용하지 않는 경우 프로젝트에 푸시 알림 확장 패키지를 추가해야 합니다. 서버 확장 패키지에 대한 자세한 내용은 [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md)을 참조하세요.

iOS 장치에 푸시 알림을 보내려면 [Apple 개발자 프로그램 멤버 자격](https://developer.apple.com/programs/ios/)이 필요합니다. 또한 [iOS 시뮬레이터는 푸시 알림을 지원하지 않으므로](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html) 실제 iOS 장치를 사용해야 합니다.

##<a name="create-hub"></a>알림 허브 만들기

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##푸시 알림을 전송하도록 서버 프로젝트 업데이트

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##(선택 사항) Android 프로젝트 구성 및 실행

이 섹션을 완료하여 Android용 Xamarin.Forms Droid 프로젝트에 대한 푸시 알림을 사용하도록 설정합니다.


###GCM(Google Cloud Messaging) 사용

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###GCM을 사용하여 푸시 요청을 보내도록 모바일 앱 백 엔드 구성

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###Android 프로젝트에 푸시 알림 추가

GCM(Google Cloud Messaging)을 사용하도록 구성된 백 엔드를 통해 구성 요소 및 코드를 클라이언트에 추가하면 모바일 앱 백 엔드를 통해 앱을 GCM에 등록하고 푸시 알림을 Azure 알림 허브에 등록하며 알림을 수신할 수 있습니다.

1. **Droid** 프로젝트에서 **Components** 폴더를 마우스 오른쪽 단추로 클릭하고 **Get More Components...**를 클릭하고 **Google Cloud Messaging Client** 구성 요소를 검색하여 이를 프로젝트에 추가합니다. 이 구성 요소는 Xamarin Android 프로젝트에 대한 푸시 알림을 지원합니다.


2. MainActivity.cs 프로젝트 파일을 열고 파일의 맨 위에 있는 문을 사용하여 다음을 추가합니다.

		using Gcm.Client;

3. **LoadApplication** 호출 후 다음 코드를 **OnCreate** 메서드에 추가합니다.

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

2. 새 클래스 파일을 `GcmService.cs`로 명명된 **Droid** 프로젝트에 추가하고 다음 **using** 문이 파일 맨 위에 있는지 확인합니다.

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


9. 파일의 맨 위에서 **using** 문 뒤의 **namespace** 선언 앞에 다음과 같은 사용 권한 요청을 추가합니다.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. 네임스페이스에 다음과 같은 클래스 정의를 추가합니다.

		[BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
		[IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
		public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
		{
		    public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
		}

	>[AZURE.NOTE]**<PROJECT\_NUMBER>**를 앞에서 설명한 프로젝트 번호로 바꿉니다.

11. 빈 **GcmService** 클래스를 새 브로드캐스트 수신기를 사용하는 다음 코드로 바꿉니다.

		 [Service]
		 public class GcmService : GcmServiceBase
		 {
		     public static string RegistrationID { get; private set; }

		     public GcmService()
		         : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
		 }


12. **OnRegistered** 이벤트 처리기를 재정의하고 **Register** 메서드를 구현하는 다음 코드를 **GcmService** 클래스에 추가합니다.

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

		Note that this code uses the `messageParam` parameter in the template registration. 

13. **OnMessage**를 구현하는 다음 코드를 추가합니다.

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

14. **GcmServiceBase**를 사용하려면 **OnUnRegistered** 및 **OnError** 처리기 메서드를 구현해야 하며 다음과 같이 수행할 수 있습니다.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
		}

		protected override void OnError(Context context, string errorId)
		{
			Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
		}

이제 Android 장치 또는 에뮬레이터에서 실행 중인 앱에서 푸시 알림을 테스트할 준비가 되었습니다.

###Android 앱에서 푸시 알림 테스트

처음 두 단계는 에뮬레이터에서 테스트할 때만 필요합니다.

1. 아래와 같이 AVD(Android 가상 장치) 관리자에서 대상으로 설정된 Google API가 있는 가상 장치에 배포하거나 해당 가상 장치에서 디버그해야 합니다.

2. **앱** > **설정** > **계정 추가**를 클릭하여 Android 장치에 Google 계정을 추가한 다음 표시되는 메시지에 따라 장치에 기존 Google 계정 추가를 사용하여 새로운 계정을 만듭니다.

1. Visual Studio 또는 Xamarin Studio에서 **Droid** 프로젝트를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정**을 클릭합니다.

2. **실행** 단추를 눌러 프로젝트를 빌드하고 Android 장치 또는 에뮬레이터에서 앱을 시작합니다.

3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

4. 항목이 추가될 때 알림을 받았는지 확인합니다.


##(선택 사항) iOS 프로젝트 구성 및 실행

이 섹션에서는 iOS 장치용 Xamarin iOS 프로젝트를 실행합니다. iOS 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.

[AZURE.INCLUDE [notification-hubs-xamarin-enable-apple-push-notifications](../../includes/notification-hubs-xamarin-enable-apple-push-notifications.md)]


####APNS에 대한 알림 허브 구성

1. [Azure 포털](https://portal.azure.com/)에 로그인합니다. **찾아보기** > **모바일 앱 ** > 사용 중인 모바일 앱 > **설정** > **푸시** > **Apple(APNS)** > **인증서 업로드**를 차례로 클릭합니다. 앞에서 내보낸 .p12 푸시 인증서 파일을 업로드합니다. 개발 및 테스트에 대한 개발 푸시 인증서를 만든 경우 **샌드박스**를 선택합니다. 그렇지 않은 경우 **프로덕션**을 선택합니다. 이제 iOS의 푸시 알림과 작동하도록 서비스가 구성되었습니다.

	![](./media/app-service-mobile-xamarin-ios-get-started-push/mobile-app-upload-apns-cert.png)


	다음에는 Xamarin Studio 또는 Visual Studio에서 iOS 프로젝트 설정을 구성합니다.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####iOS 앱에 푸시 알림 추가

1. **iOS** 프로젝트에서 AppDelegate.cs를 열고 다음 **using** 문을 코드 파일의 맨 위에 추가합니다.

        using Newtonsoft.Json.Linq;

4. 또한 **AppDelegate** 클래스에서 **RegisteredForRemoteNotifications** 이벤트 재정의를 추가하여 알림을 등록합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, 
			NSData deviceToken)
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

5. **AppDelegate**에서 **DidReceivedRemoteNotification** 이벤트 처리기에 대한 다음 재정의도 추가합니다.

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

2. **AppDelegate** 클래스에서 다음 코드를 **FinishedLaunching** 메서드에 추가합니다.

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

####iOS 앱에서 푸시 알림 테스트

1. iOS 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.

2. Visual Studio에서 **실행** 단추 또는 **F5** 키를 눌러 프로젝트를 빌드하고 iOS 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

	> [AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

3. 앱에서 작업을 입력한 다음 더하기(**+**) 아이콘을 클릭합니다.

4. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.


##(선택 사항) Windows 프로젝트 구성 및 실행

이 섹션에서는 Windows 장치용 Xamarin.Forms WinApp 및 WinPhone81 프로젝트를 실행합니다. 이 단계에서는 UWP(범용 Windows 플랫폼) 프로젝트도 지원합니다. Windows 장치를 작업하지 않는 경우 이 섹션을 건너뛸 수 있습니다.


####WNS를 사용하여 푸시 알림에 대해 Windows 앱 등록

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####WNS에 대한 알림 허브 구성

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####Windows 앱에 푸시 알림 추가

1. Visual Studio의 Windows 프로젝트에서 **App.xaml.cs**를 열고 다음 **using** 문을 추가합니다.

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
				"<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>";

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

	이 메서드는 푸시 알림 채널을 가져오고 알림 허브에서 템플릿 알림을 수신하기 위해 템플릿을 등록합니다. *messageParam*을 지원하는 템플릿 알림이 이 클라이언트에 전달됩니다.

3. App.xaml.cs에서 `async` 한정자를 추가하여 **OnLaunched** 이벤트 처리기 메서드 정의를 업데이트한 후 다음 코드 줄을 메서드 끝에 추가합니다.

        await InitNotificationsAsync();

	이렇게 하면 앱이 시작될 때마다 푸시 알림 등록이 생성 또는 새로 고침됩니다. WNS 푸시 채널이 항상 활성화되도록 이 작업을 수행하는 것이 중요합니다.

4. Visual Studio용 솔루션 탐색기에서 **Package.appxmanifest** 파일을 열고 **알림** 아래에서 **알림 가능**을 **예**로 설정합니다.

5. 앱을 빌드하고 오류가 없는지 확인합니다. 이제 클라이언트 앱이 모바일 앱 백 엔드에서 템플릿 알림을 등록해야 합니다. 솔루션의 모든 Windows 프로젝트에 대해 이 섹션을 반복합니다.


####Windows 앱에서 푸시 알림 테스트

1. Visual Studio에서 Windows 프로젝트를 마우스 오른쪽 단추로 누른 다음 **시작 프로젝트로 설정**을 클릭합니다.

2. **실행** 단추를 눌러 프로젝트를 빌드하고 앱을 시작합니다.

3. 앱에서 새 todoitem에 대한 이름을 입력한 다음 더하기(**+**) 아이콘을 클릭하여 추가합니다.

4. 항목이 추가될 때 알림을 받았는지 확인합니다.

##다음 단계

푸시 알림에 대해 자세히 알아봅니다.

* [Azure 모바일 앱용 .NET 백 엔드 서버 SDK 사용](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) 태그를 사용하면 푸시를 사용하여 여러 조각으로 나뉜 고객을 대상으로 할 수 있습니다. 장치 설치에 태그를 추가하는 방법에 대해 자세히 알아보세요.

* [푸시 알림 문제 진단](../notification-hubs/notification-hubs-push-notification-fixer.md) 장치에서 알림이 삭제되거나 끝나지 않는 다양한 이유가 있습니다. 이 항목에서는 푸시 알림 실패의 근본 원인을 분석 및 파악하는 방법을 보여 줍니다.

다음 자습서 중 하나를 진행하는 것이 좋습니다.

* [앱에 인증 추가](app-service-mobile-xamarin-forms-get-started-users.md) ID 공급자를 사용하여 앱 사용자를 인증하는 방법을 알아봅니다.

* [앱에 오프라인 동기화 사용](app-service-mobile-xamarin-forms-get-started-offline-data.md) 모바일 앱 백 엔드를 사용하여 앱에 오프라인 지원을 추가하는 방법을 알아봅니다. 오프라인 동기화를 사용하면 최종 사용자는 네트워크에 연결되어 있지 않을 때도 모바일 앱과 데이터 보기, 추가 또는 수정과 같은 상호 작용을 수행할 수 있습니다.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

<!---HONumber=AcomDC_0706_2016-->