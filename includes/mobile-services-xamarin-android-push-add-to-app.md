
4. `ToDoBroadcastReceiver`(이)라는 프로젝트에서 새 클래스를 만듭니다.

5. **ToDoBroadcastReceiver** 클래스에 다음 using 문을 추가합니다.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. **using** 문과 **namespace** 선언 사이에 다음 사용 권한 요청을 추가합니다.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
		[assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

		//GET_ACCOUNTS is only needed for android versions 4.0.3 and below
		[assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
		[assembly: UsesPermission(Name = "android.permission.INTERNET")]
		[assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. 기존 **ToDoBroadcastReceiver** 클래스 정의를 다음으로 바꿉니다.
 
	    [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, 
	        Categories = new string[] { "@PACKAGE_NAME@" })]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, 
	        Categories = new string[] { "@PACKAGE_NAME@" })]
	    [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, 
        Categories = new string[] { "@PACKAGE_NAME@" })]
        public class ToDoBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
	        // Set the Google app ID.
	        public static string[] senderIDs = new string[] { "<PROJECT_NUMBER>" };
        }

	위의 코드에서 Google 개발자 포털에서 앱을 프로비전할 때 Google에서 할당한 프로젝트 번호로 _`<PROJECT_NUMBER>`_을(를) 바꿉니다.

8. ToDoBroadcastReceiver.cs 프로젝트 파일에서 **PushHandlerService** 클래스를 정의하는 다음 코드를 추가합니다.
 
		// The ServiceAttribute must be applied to the class.
    	[Service] 
    	public class PushHandlerService : GcmServiceBase
    	{
        	public static string RegistrationID { get; private set; }
 
        	public PushHandlerService() : base(ToDoBroadcastReceiver.senderIDs) { }
    	}

	이 클래스는 **GcmServiceBase**에서 파생되며 **서비스** 특성이 이 클래스에 적용되어야 합니다.

	>[AZURE.NOTE]**GcmServiceBase** 클래스는 **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** 및 **OnError()** 메서드를 구현합니다. **PushHandlerService** 클래스에서 이러한 메서드를 재정의해야 합니다.

5. **OnRegistered** 이벤트 처리기를 재정의하는 **ToDoBroadcastReceiver** 클래스에 다음 코드를 추가합니다.

        protected override void OnRegistered(Context context, string registrationId)
        {
            System.Diagnostics.Debug.WriteLine("The device has been registered with GCM.", "Success!");
            
            // Get the MobileServiceClient from the current activity instance.
            MobileServiceClient client = ToDoActivity.CurrentActivity.CurrentClient;           
            var push = client.GetPush();

            List<string> tags = null;

            //// (Optional) Uncomment to add tags to the registration.
            //var tags = new List<string>() { "myTag" }; // create tags if you want

            try
            {
                // Make sure we run the registration on the same thread as the activity, 
                // to avoid threading errors.
                ToDoActivity.CurrentActivity.RunOnUiThread(
                    async () => await push.RegisterNativeAsync(registrationId, tags));
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(
                    string.Format("Error with Azure push registration: {0}", ex.Message));                
            }
        }

	이 메서드는 반환된 GCM 등록 ID를 사용하여 푸시 알림을 위해 Azure에 등록합니다.

10. **PushHandlerService**의 **OnMessage** 메서드를 다음 코드로 재정의합니다.

        protected override void OnMessage(Context context, Intent intent)
        {          
            string message = string.Empty;

            // Extract the push notification message from the intent.
            if (intent.Extras.ContainsKey("message"))
            {
                message = intent.Extras.Get("message").ToString();
                var title = "New item added:";

                // Create a notification manager to send the notification.
                var notificationManager = 
                    GetSystemService(Context.NotificationService) as NotificationManager;

                // Create a new intent to show the notification in the UI. 
                PendingIntent contentIntent = 
					PendingIntent.GetActivity(context, 0, 
					new Intent(this, typeof(ToDoActivity)), 0);	          

                // Create the notification using the builder.
                var builder = new Notification.Builder(context);
                builder.SetAutoCancel(true);
                builder.SetContentTitle(title);
                builder.SetContentText(message);
                builder.SetSmallIcon(Resource.Drawable.ic_launcher);
                builder.SetContentIntent(contentIntent);
                var notification = builder.Build();

                // Display the notification in the Notifications Area.
                notificationManager.Notify(1, notification);

            }
        }

12. 컴파일할 프로젝트에 필요한 **OnUnRegistered()** 및 **OnError()**에 대해 재정의하는 다음 메서드를 추가합니다.

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            throw new NotImplementedException();
        }

        protected override void OnError(Context context, string errorId)
        {
            System.Diagnostics.Debug.WriteLine(
                string.Format("Error occurred in the notification: {0}.", errorId));
        }

<!---HONumber=July15_HO2-->