<properties
	pageTitle="Xamarin.Android 앱에 대한 알림 허브 시작"
	description="Azure 알림 허브를 사용하여 Xamarin Android 응용 프로그램에 푸시 알림을 보내는 방법에 대해 알아봅니다."
	authors="yuaxu"
	manager="dwrede"
	editor=""
	services="notification-hubs"
	documentationCenter="xamarin"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="11/11/2014"
	ms.author="donnam"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##개요

이 항목에서는 Azure 알림 허브를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. 완성된 코드는 [NotificationHubs 앱][GitHub] 샘플에서 확인할 수 있습니다.

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 

##필수 조건

이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ [Xamarin.Android]
+ 활성 Google 계정
+ [Azure 모바일 서비스 구성 요소]
+ [Azure 메시징 구성 요소]
+ [Google Cloud Messaging 클라이언트 구성 요소]

이 자습서를 완료해야 다른 모든 Xamarin.Android 앱용 알림 허브 자습서를 진행할 수 있습니다.

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F)을 참조하세요.

##<a name="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a name="configure-hub"></a>알림 허브 구성

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a name="connecting-app"></a>알림 허브에 앱 연결

### 새 프로젝트 만들기

1. Xamarin Studio(또는 Visual Studio)에서 **파일**, **새로 만들기**를 차례로 클릭한 다음 **새 솔루션** 대화 상자에서 **Android 응용 프로그램**을 클릭하고 **확인**을 클릭합니다.

	![][14]

	그러면 새 Android 프로젝트가 만들어집니다.

2. 솔루션 보기에서 새 프로젝트를 마우스 오른쪽 단추로 클릭하고 **옵션**을 선택하여 프로젝트 속성을 엽니다. **빌드** 섹션에서 **Android 응용 프로그램** 항목을 선택합니다.

	![][15]

3. **최소 Android 버전**을 API 수준 8로 설정합니다.

4. **대상 Android 버전**을 대상으로 지정할 API 버전으로 설정합니다(API Level 8 이상이어야 함).

5. **패키지 이름**의 첫 문자는 소문자여야 합니다.

	> [AZURE.IMPORTANT] 패키지 이름의 첫 문자는 소문자여야 합니다. 그렇지 않으면 아래 푸시 알림에 대한 **BroadcastReceiver** 및 **IntentFilter**를 등록할 때 응용 프로그램 매니페스트 오류가 표시됩니다.

### 프로젝트에 필요한 구성 요소 추가

Xamarin Component Store에서 제공되는 Google Cloud Messaging 클라이언트는 Xamarin.Android에서 푸시 알림을 지원하는 프로세스를 간소화합니다.

1. Xamarin.Android 앱에서 Components 폴더를 마우스 오른쪽 단추로 클릭하고 **Get More Components...**를 선택합니다.

2. **Azure 모바일 서비스** 구성 요소를 검색하고 프로젝트에 구성 요소를 추가합니다.

3. **Azure 메시징** 구성 요소를 검색하고 프로젝트에 구성 요소를 추가합니다.

4. **Google Cloud Messaging 클라이언트** 구성 요소를 검색하고 프로젝트에 구성 요소를 추가합니다.


### 프로젝트에서 알림 허브 설정

1. **Constants.cs** 클래스를 만들고 다음 상수 값을 정의합니다. 자리 표시자는 값으로 바꿉니다.

        public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

        // Azure app specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<hub path>";

2. **MainActivity.cs**에 다음 using 문을 추가합니다.

		using Microsoft.WindowsAzure.MobileServices;
		using Gcm.Client;

3. **MainActivity** 클래스에 다음 메서드를 만듭니다.

		private void RegisterWithGCM()
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. 새 클래스 **MyBroadcastReceiver**를 만듭니다.

	> [AZURE.NOTE] 아래에서는 **BroadcastReceiver**를 처음부터 새로 만드는 과정을 단계별로 안내합니다. 그러나 수동으로 **MyBroadcastReceiver.cs**를 만드는 대신 GitHub의 샘플 Xamarin.Android 프로젝트에 있는 **GcmService.cs** 파일을 참조하면 해당 파일을 더 빠르게 만들 수 있습니다. **GcmService.cs**를 복제하고 클래스 이름을 변경하는 방법으로도 파일을 빠르게 만들 수 있습니다.

5. 앞에서 추가한 구성 요소 및 어셈블리를 참조하여 다음 using 문을 **MyBroadcastReceiver.cs**에 추가합니다.

		using WindowsAzure.Messaging;
		using Gcm.Client;

5. **using** 문과 **namespace** 선언 사이에 다음 사용 권한 요청을 추가합니다.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. **MyBroadcastReceiver.cs**에서 **MyBroadcastReceiver** 클래스를 다음과 일치하도록 변경합니다.

    	[BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
			Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. **MyBroadcastReceiver.cs**에서 **GcmServiceBase**에서 파생된 **PushHandlerService** 클래스를 추가합니다. 클래스에 **Service** 특성을 적용해야 합니다.

    	[Service] // Must use the service tag
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
        	private NotificationHub Hub { get; set; }

        	public PushHandlerService() : base(Constants.SenderID)
       		{
            	Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
        	}
    	}


8. **GcmServiceBase**는 **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** 및 **OnError()** 메서드를 구현합니다. 구현 클래스 **PushHandlerService**는 이러한 메서드를 재정의해야 하며, 해당 메서드는 알림 허브와 상호 작용에 대한 응답으로 발생합니다.

9. **PushHandlerService**의 **OnRegistered()** 메서드를 다음 코드로 재정의합니다.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

            Hub = new NotificationHub (Constants.NotificationHubPath, Constants.ConnectionString);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }

            var tags = new List<string>() { "falcons" }; // create tags if you want

            try
            {
                var hubRegistration = Hub.RegisterNative (registrationId, tags);
            }
            catch (Exception ex)
            {
                Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	> [AZURE.NOTE] 위의 **OnRegistered()** 코드에서는 특정 메시징 채널에 등록하기 위한 태그를 지정하는 기능을 확인해야 합니다.

10. **PushHandlerService**의 **OnMessage** 메서드를 다음 코드로 재정의합니다.

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(messageText))
            {
                createNotification("New hub message!", messageText);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

11. 위와 같이 사용자에게 알리기 위해 **PushHandlerService**에 **createNotification** 메서드를 추가합니다.

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

12. 코드가 컴파일되도록 추상 멤버 **OnUnRegistered()**, **OnRecoverableError()** 및 **OnError()**를 재정의합니다.


<h2><a name="run-app"></a>에뮬레이터에서 앱 실행</h2>

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

>[AZURE.IMPORTANT] 푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다. 이렇게 하려면 에뮬레이터에서 **설정**으로 이동하여 **계정 추가**를 클릭합니다. 에뮬레이터가 인터넷에 연결되어 있어야 합니다.

1. **도구**에서 **Android Emulator Manager 열기**를 클릭하고 장치를 선택한 후에 **편집**을 클릭합니다.

	![][18]

2. **대상**에서 **Google API**를 선택하고 **확인**을 클릭합니다.

	![][19]

3. 위쪽 도구 모음에서 **실행**을 클릭하고 앱을 선택합니다. 에뮬레이터가 시작되고 앱이 실행됩니다.

  앱이 GCM에서  *registrationId*를 검색하고 알림 허브에 등록됩니다.

<h2><a name="send"></a>백 엔드에서 알림 보내기</h2>

<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 앱 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

.NET 앱을 사용하여 알림을 보내려면

1. 새 Visual C# 콘솔 응용 프로그램을 만듭니다.

	![][20]

2. <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

        Install-Package WindowsAzure.ServiceBus

    Enter 키를 누릅니다.

2. Program.cs 파일을 열고 다음 using 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

3. 다음 메서드를  `Program` 클래스에 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

4. 그런 다음 Main 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

5. F5 키를 눌러 앱을 실행합니다. 그러면 알림 메시지가 수신되어야 합니다.

	![][21]

모바일 서비스를 사용하여 알림을 보내려면 [모바일 서비스 시작]의 지침을 따른 후 다음 단계를 수행합니다.

1. [Azure 관리 포털]에 로그온한 다음 모바일 서비스를 선택합니다.

2. 위쪽의 **스케줄러** 탭을 선택합니다.

	![][22]

3. 새 예약된 작업을 만들고 이름을 입력한 후 **요청 시**를 선택합니다.

	![][23]

4. 작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5. 스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은  *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. **저장**을 클릭합니다.

        var azure = require('azure');
		var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
		notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
    	  function (error)
    	  {
        	if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
	    );

6. 아래쪽 막대에서 **한 번 실행**을 클릭합니다. 그러면 알림 메시지가 수신되어야 합니다.

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 푸시 알림 보내기] 자습서를 참조하고, 사용자를 관심 그룹별로 분할하려면 [알림 허브를 통해 속보 보내기]를 참조하세요. 알림 허브 사용 방법에 대한 자세한 내용은 [알림 허브 지침] 및 [Android용 알림 허브 사용 방법]을 참조하세요.

<!-- Anchors. -->
[Google Cloud Messaging 사용]: #register
[알림 허브 구성]: #configure-hub
[알림 허브에 앱 연결]: #connecting-app
[에뮬레이터를 사용하여 앱 실행]: #run-app
[백 엔드에서 알림 보내기]: #send
[다음 단계]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-notification-hubs-android-get-started/mobile-services-google-create-server3.png

[7]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png
[12]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[14]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app2.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

<!-- URLs. -->
[앱 제출 페이지]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[내 응용 프로그램]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows용 Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[모바일 서비스 시작]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript 및 HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure 관리 포털]: https://manage.windowsazure.com/
[wns 개체]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[Android용 알림 허브 사용 방법]: http://msdn.microsoft.com/library/dn282661.aspx

[알림 허브를 사용하여 사용자에게 푸시 알림 보내기]: /manage/services/notification-hubs/notify-users-aspnet
[알림 허브를 사용하여 속보 보내기]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient 구성 요소 페이지]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub 페이지]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin.Android]: http://xamarin.com/download/
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[Google Cloud Messaging 클라이언트 구성 요소]: http://components.xamarin.com/view/GCMClient/
[Azure 메시징 구성 요소]: http://components.xamarin.com/view/azure-messaging

<!--HONumber=49-->