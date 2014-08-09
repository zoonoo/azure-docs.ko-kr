<properties linkid="" urlDisplayName="" pageTitle="Get started with Notification Hubs for Xamarin.Android apps" metaKeywords="" description="Learn how to use Azure Notification Hubs to send push notifications to a Xamarin Android application." metaCanonical="" authors="elioda" solutions="" manager="" editor="" services="mobile-services,notification-hubs" documentationCenter="" title="Get started with Notification Hubs" />

알림 허브 시작
==============

[Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 스토어 C#")[Windows Phone](/ko-kr/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/ko-kr/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/ko-kr/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/ko-kr/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/ko-kr/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/ko-kr/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

이 항목에서는 Azure 알림 허브를 사용하여 Xamarin.Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. 완성된 코드는 [NotificationHubs 앱](http://go.microsoft.com/fwlink/p/?LinkId=331329) 샘플에서 확인할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

1.  [Google Cloud Messaging 사용](#register)
2.  [알림 허브 구성](#configure-hub)
3.  [알림 허브에 앱 연결](#connecting-app)
4.  [에뮬레이터를 사용하여 앱 실행](#run-app)
5.  [백 엔드에서 알림 보내기](#send)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 이 자습서를 사용하려면 다음이 필요합니다.

-   Xamarin.Android
-   활성 Google 계정

이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다.

**참고**

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/ko-kr/pricing/free-trial/?WT.mc_id=A9C9624B5&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fmanage%2Fservices%2Fnotification-hubs%2Fgetting-started-xamarin-android%2F)을 참조하십시오.

Google Cloud Messaging 사용Google Cloud Messaging 사용
------------------------------------------------------

**참고**

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

1.  [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **Create project...**를 클릭합니다.

   	![][1]   

    **참고**

    기존 프로젝트가 이미 있는 경우 로그인하면 **Dashboard** 페이지로 이동됩니다. Dashboard에서 새 프로젝트를 만들려면 **API Project**를 확장하고 **Other projects** 아래의 **Create...**를 클릭한 후 프로젝트 이름을 입력하고 **Create project**를 클릭합니다.

2.  왼쪽 열에서 **Overview**를 클릭하고 **Dashboard** 섹션에 있는 프로젝트 번호를 기록해 둡니다.

    이 자습서 뒷부분에서 이 값을 클라이언트의 PROJECT\_ID 변수로 설정합니다.

3.  [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 페이지에서 **Services**를 클릭한 후 토글을 클릭하여 **Google Cloud Messaging for Android**를 사용하도록 설정하고 서비스 약관에 동의합니다.

4.  **API Access**를 클릭한 후 **Create new Server key...**를 클릭합니다.

   	![][2]

5.  **Configure Server Key for API Project**에서 **Create**를 클릭합니다.

   	![][3]

6.  **API key** 값을 기록해 둡니다.

   	![][4] 

다음에는 이 API 키 값을 사용하여 알림 허브에서 GCM의 인증을 받고 응용 프로그램 대신 푸시 알림을 보낼 수 있게 합니다.

알림 허브 구성알림 허브 구성
----------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

   	![][7]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

   	![][8]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성**을 클릭합니다.

   	![][9]

5.  맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

   	![][10]

6.  맨 위에 있는 **구성** 탭을 클릭하고 이전 섹션에서 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

   	![][11]

7.  맨 위에 있는 **대시보드** 탭을 선택하고 **연결 정보**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

   	![][12]

이제 알림 허브가 GCM과 작동하도록 구성되었으며 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.

앱 연결알림 허브에 앱 연결
--------------------------

### 새 프로젝트 만들기

1.  Xamarin Studio(또는 Visual Studio)에서 새로운 Android 프로젝트를 만듭니다(File, New, Solution, Android Application).

   	![][13]   
   	![][14]

2.  솔루션 보기에서 새 프로젝트를 마우스 오른쪽 단추로 클릭하고 **Options**를 선택하여 프로젝트 속성을 엽니다. **Build** 섹션에서 **Android Application** 항목을 선택합니다.

   	![][15]

3.  **Minimum Android version**을 API Level 8로 설정합니다.

4.  **Target Android version**을 대상으로 지정할 API 버전으로 설정합니다(API Level 8 이상이어야 함).

5.  **Package name**의 첫 문자는 소문자여야 합니다.

    **참고**

    패키지 이름의 첫 문자는 소문자여야 합니다. 그렇지 않으면 아래에서 푸시 알림에 대한 **BroadcastReceiver** 및 **IntentFilter**를 등록할 때 응용 프로그램 매니페스트 오류가 표시됩니다.

### 프로젝트에 PushSharp 추가

1.  이제 프로젝트에서 참조로 **PushSharp**를 추가합니다. 이렇게 하려면 최신 버전의 PushSharp를 컴파일하고 컴파일된 DLL을 Xamarin.Android 프로젝트에 대한 참조로 추가해야 합니다.

2.  [PushSharp Github 페이지](https://github.com/Redth/PushSharp)(영문)를 방문하여 최신 릴리스를 다운로드합니다. 파일 컬렉션의 압축을 풀고 나면 다음 샘플 프로젝트 폴더로 이동합니다.

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. Xamarin Studio(또는 Visual Studio)에서 다음 프로젝트 파일을 엽니다.

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  MonoForAndroid PushSharp 클라이언트 샘플을 **Release** 모드에서 빌드합니다.

4.  Xamarin.Android 프로젝트 폴더에 **\_external** 폴더를 만듭니다.

5.  MonoForAndroid PushSharp 클라이언트 샘플에서 다음 파일을 복사하여 Xamarin.Android 프로젝트 폴더에서 새로 만든 **\_external** 폴더에 넣습니다.

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Xamarin Studio(또는 Visual Studio)에서 Xamarin.Android 프로젝트를 엽니다.

7.  프로젝트 **References** 폴더를 마우스 오른쪽 단추로 클릭하고 **Edit References...**를 선택합니다.

8.  **.Net Assembly** 탭으로 이동하여 프로젝트의 **\_external** 폴더를 찾은 후 앞에서 빌드한 **PushSharp.Client.MonoForAndroid.dll**을 선택하고 **Add**를 클릭합니다. OK를 클릭하여 대화 상자를 닫습니다.

### 프로젝트에 Xamarin.NotificationHub 추가

1.  이제 NotificationHub 클래스를 지원할 라이브러리를 추가해야 합니다. [Xamarin.NotificationHub Github 페이지](https://github.com/SaschaDittmann/Xamarin.NotificationHub)(영문)를 방문하여 원본 폴더를 다운로드하고 빌드합니다.

2.  컴파일된 **ByteSmith.WindowsAzure.Messaging.Android.dll**을 Xamarin.Android 프로젝트 폴더의 **\_external** 폴더에 복사합니다.

3.  Xamarin Studio(또는 Visual Studio)에서 Xamarin.Android 프로젝트를 엽니다.

4.  프로젝트 **References** 폴더를 마우스 오른쪽 단추로 클릭하고 **Edit References...**를 선택합니다.

5.  **.Net Assembly** 탭으로 이동하여 프로젝트의 **\_external** 폴더를 찾은 후 앞에서 빌드한 **ByteSmith.WindowsAzure.Messaging.Android.dll**을 선택하고 **Add**를 클릭합니다. OK를 클릭하여 대화 상자를 닫습니다.

### 프로젝트에서 알림 허브 설정

1.  **Constants.cs** 클래스를 만들고 다음 상수 값을 정의합니다(자리 표시자를 값으로 대체).

         public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number

         // Azure app specific connection string and hub path
         public const string ConnectionString = "<Azure connection string>"";
         public const string NotificationHubPath = "<hub path>";

2.  **MainActivity.cs**에 다음 using 문을 추가합니다.

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

3.  **MainActivity** 클래스에 다음 메서드를 만듭니다.

         private void RegisterWithGCM()
         {
             // Check to ensure everything's setup right
             PushClient.CheckDevice(this);
             PushClient.CheckManifest(this);

             // Register for push notifications
             System.Diagnostics.Debug.WriteLine("Registering...");
             PushClient.Register(this, Constants.SenderID);
         }

4.  새 클래스 **MyBroadcastReceiver**를 만듭니다.

    **참고**

    처음부터 **BroadcastReceiver**를 만드는 과정을 단계별로 안내합니다. 그러나 아래에서 수동으로 **MyBroadcastReceiver.cs**를 만드는 대신 빠른 대안으로 GitHub의 PushSharp 샘플 Xamarin.Android 프로젝트에 있는 **PushService.cs** 파일을 참조할 수 있습니다. **PushService.cs**를 복제하고 클래스 이름을 변경하면 빨리 시작할 수 있습니다.

5.  **MyBroadcastReceiver.cs**에 다음 using 문을 추가합니다.

         using ByteSmith.WindowsAzure.Messaging;
         using PushSharp.Client;

6.  **using** 문과 **namespace** 선언 사이에 다음 사용 권한 요청을 추가합니다.

         [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
         [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

         //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
         [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
         [assembly: UsesPermission(Name = "android.permission.INTERNET")]
         [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7.  **MyBroadcastReceiver.cs**에서 **MyBroadcastReceiver** 클래스를 다음과 일치하도록 변경합니다.

    [BroadcastReceiver(Permission=GCMConstants.PERMISSION\_GCM\_INTENTS)]

         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
         [IntentFilter(new string[] { GCMConstants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
         public class MyBroadcastReceiver : PushHandlerBroadcastReceiverBase<PushHandlerService>
         {
             public static string[] SENDER_IDS = new string[] { Constants.SenderID };

             public const string TAG = "MyBroadcastReceiver-GCM";
         }

8.  **MyBroadcastReceiver.cs**에서 **PushHandlerServiceBase**에서 파생된 **PushHandlerService** 클래스를 추가합니다. 클래스에 **Service** 지시문을 사용해야 합니다.

    [Service] //Must use the service tag public class PushHandlerService : PushHandlerServiceBase {

             public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID) 
                {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor"); 
             }
         }

9.  **PushHandlerServiceBase**는 **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()** 및 **OnError()** 메서드를 구현합니다. 구현 클래스 **PushHandlerService**는 이러한 메서드를 재정의해야 하며, 해당 메서드는 알림 허브와 상호 작용에 대한 응답으로 발생합니다.

10. **PushHandlerService**의 **OnRegistered()** 메서드를 다음 코드로 재정의합니다.

         protected override async void OnRegistered(Context context, string registrationId)
         {
             Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
             RegistrationID = registrationId;

             createNotification("PushHandlerService-GCM Registered...", "The device has been Registered, Tap to View!");

             Hub = new NotificationHub(Constants.NotificationHubPath, Constants.ConnectionString);
             try
             {
                 await Hub.UnregisterAllAsync(registrationId);
             }
             catch (Exception ex)
             {
                 Debug.WriteLine(ex.Message);
                 Debugger.Break();
             }

             var tags = new List<string>() { "falcons" }; // create tags if you want

             try
             {
                 var hubRegistration = await Hub.RegisterNativeAsync(registrationId, tags);
             }
             catch (Exception ex)
             {
                 Debug.WriteLine(ex.Message); 
                 Debugger.Break();
             }
         }

    **참고**

    위의 **OnRegistered()** 코드에서 태그를 지정하여 특정 메시징 채널에 등록하는 기능을 확인해야 합니다.

11. **PushHandlerService**의 **OnMessage** 메서드를 다음 코드로 재정의합니다.

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

12. 위와 같이 사용자에게 알리기 위해 **PushHandlerService**에 **createNotification** 메서드를 추가합니다.

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

13. 코드가 컴파일되도록 추상 멤버 **OnUnRegistered()**, **OnRecoverableError()** 및 **OnError()**를 재정의합니다.

앱 실행에뮬레이터에서 앱 실행
-----------------------------

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1.  **Tools**에서 **Open Android Emulator Manager**를 클릭하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

   	![][18]

2.  **Target**에서 **Google APIs**를 선택하고 **OK**를 클릭합니다.

   	![][19]

3.  위쪽 도구 모음에서 **Run**을 클릭하고 앱을 선택합니다. 에뮬레이터가 시작되고 앱이 실행됩니다.

4.  앱이 GCM에서 *registrationId*를 검색하고 알림 허브에 등록됩니다.

    **참고**

    푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다(에뮬레이터에서 **Settings**로 이동한 후 **Add Account** 클릭). 에뮬레이터가 인터넷에 연결되어 있어야 합니다.

알림 보내기백 엔드에서 알림 보내기
----------------------------------

[REST 인터페이스](http://msdn.microsoft.com/ko-kr/library/windowsazure/dn223264.aspx)를 사용하여 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 앱 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

.NET 앱을 사용하여 알림을 보내려면

1.  새 Visual C\# 콘솔 응용 프로그램을 만듭니다.

   	![][20]

2.  [WindowsAzure.ServiceBus NuGet 패키지](http://nuget.org/packages/WindowsAzure.ServiceBus/)를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다. Visual Studio 주 메뉴에서 **도구**, **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 그런 다음 콘솔 창에서 다음을 입력하고

         Install-Package WindowsAzure.ServiceBus

    Enter 키를 누릅니다.

3.  Program.cs 파일을 열고 다음 using 문을 추가합니다.

         using Microsoft.ServiceBus.Notifications;

4.  `Program` 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

5.  그런 다음 Main 메서드에 다음 줄을 추가합니다.

          SendNotificationAsync();
          Console.ReadLine();

6.  F5 키를 눌러 앱을 실행합니다. 그러면 알림 메시지가 수신되어야 합니다.

   	![][21]

모바일 서비스를 사용하여 알림을 보내려면 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-xamarin-android/#create-new-service)(영문)을 따른 후 다음을 수행하십시오.

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 모바일 서비스를 선택합니다.

2.  맨 위에 있는 **스케줄러** 탭을 선택합니다.

   	![][22]

3.  새 예약된 작업을 만들고 이름을 삽입한 후 **요청 시**를 선택합니다.

   	![][23]

4.  작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5.  스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. **저장**을 클릭합니다.

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

6.  아래쪽 막대에서 **한 번 실행**을 클릭합니다. 그러면 알림 메시지가 수신되어야 합니다.

다음 단계
---------

이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시](/en-us/manage/services/notification-hubs/notify-users-aspnet) 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet)를 참조하십시오. 알림 허브 사용 방법에 대해 자세히 알아보려면 [알림 허브 지침](http://msdn.microsoft.com/ko-kr/library/jj927170.aspx) 및 [Android용 알림 허브 방법](http://msdn.microsoft.com/ko-kr/library/dn282661.aspx)을 참조하십시오.

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next Steps]:#next-steps

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
