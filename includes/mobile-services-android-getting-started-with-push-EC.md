1. 앱 프로젝트에서 `AndroidManifest.xml` 파일을 엽니다. 다음 두 단계의 코드에서 _`**my_app_package**`_을(를) 프로젝트의 앱 패키지 이름(`manifest` 태그의 `package` 특성 값)으로 바꿉니다. 

2. 기존 `uses-permission` 요소 뒤에 다음과 같은 새 사용 권한을 추가합니다.

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. 다음 코드를 `application` 시작 태그 뒤에 추가합니다.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            						 	android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


4. [모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    > [AZURE.NOTE]후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

5.  *ToDoItemActivity.java* 파일을 열고 다음 import 문을 추가합니다.

		import com.microsoft.windowsazure.notifications.NotificationsManager;


6. 클래스에 다음 개인 변수를 추가합니다. 여기서 _`<PROJECT_NUMBER>`_은(는) Google이 이전 절차에서 앱에 할당한 프로젝트 번호로 바꿉니다.

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

7. *MobileServiceClient*의 정의를 **private**에서 **public static**으로 변경하면 이제 다음과 같이 표시됩니다.

		public static MobileServiceClient mClient;



9. 다음으로 알림을 처리하는 새 클래스를 추가해야 합니다. 패키지 탐색기에서 패키지(`src` 노드 아래)를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**, **클래스**를 차례로 클릭합니다.

10. **이름**에서 `MyHandler`을(를) 입력하고, **Superclass**에서 `com.microsoft.windowsazure.notifications.NotificationsHandler`을(를) 입력한 다음 **마침**을 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

	새 MyHandler 클래스가 만들어집니다.

11. `MyHandler` 클래스에 대한 다음 import 문을 추가합니다.

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.AsyncTask;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;

	
12. 그리고 `MyHandler` 클래스에 대한 다음 멤버를 추가합니다.

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;


13. `MyHandler` 클래스에서 다음 코드를 추가하여 장치를 모바일 서비스 알림 허브에 등록하는 **onRegistered** 메서드를 재정의합니다.

		@Override
		public void onRegistered(Context context,  final String gcmRegistrationId) {
		    super.onRegistered(context, gcmRegistrationId);
	
		    new AsyncTask<Void, Void, Void>() {
		    		    	
		    	protected Void doInBackground(Void... params) {
		    		try {
		    		    ToDoActivity.mClient.getPush().register(gcmRegistrationId, null);
		    		    return null;
	    		    }
	    		    catch(Exception e) { 
			    		// handle error    		    
	    		    }
					return null;  		    
	    		}
		    }.execute();
		}



14. `MyHandler` 클래스에서 다음 코드를 추가하여 **onReceive** 메서드를 재정의하면 알림 수신 시 표시됩니다.

		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("message");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
			mNotificationManager = (NotificationManager)
		              ctx.getSystemService(Context.NOTIFICATION_SERVICE);
	
		    PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		          new Intent(ctx, ToDoActivity.class), 0);
	
		    NotificationCompat.Builder mBuilder =
		          new NotificationCompat.Builder(ctx)
		          .setSmallIcon(R.drawable.ic_launcher)
		          .setContentTitle("Notification Hub Demo")
		          .setStyle(new NotificationCompat.BigTextStyle()
		                     .bigText(msg))
		          .setContentText(msg);
	
		     mBuilder.setContentIntent(contentIntent);
		     mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}


15. 다시 TodoActivity.java 파일에서 *ToDoActivity* 클래스의 **onCreate** 메서드를 업데이트하여 알림 처리기 클래스를 등록합니다. *MobileServiceClient*가 인스턴스화된 후에 이 코드를 추가해야 합니다.


		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

<!-- URLs. -->
[모바일 서비스 Android SDK]: http://aka.ms/Iajk6q

<!---HONumber=July15_HO3-->