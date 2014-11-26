1.  `AndroidManifest.xml` 파일을 엽니다. 다음 두 단계의 코드에서 _`**my_app_package**`_를 프로젝트의 앱 패키지 이름(`package` 특성의 값, `manifest` 태그)으로 바꿉니다.

2.  기존 `uses-permission` 요소 뒤에 다음과 같은 새 사용 권한을 추가합니다.

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  다음 코드를 `application` 시작 태그 뒤에 추가합니다.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

4.  [모바일 서비스 Android SDK][모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    <div class="dev-callout"><b>참고</b>
<p>후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.</p>
</div>

5.  *ToDoItemActivity.java* 파일을 열고 다음 import 문을 추가합니다.

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  클래스에 다음 개인 변수를 추가합니다. 여기서 *`<PROJECT_NUMBER>`*는 Google이 위의 절차에서 앱에 할당한 프로젝트 번호입니다.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  **onCreate** 메서드에서 MobileServiceClient가 인스턴스화되기 전에 장치에 알림 처리기를 등록하는 다음 코드를 추가합니다.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    나중에 이 코드에서 참조되는 MyHandler.class를 정의합니다.

8.  Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**와 **Class**를 차례로 클릭합니다.

9.  **Name**에서 `MyHandler`를 입력하고, **Superclass**에서 `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 다음 **Finish**를 클릭합니다.

    ![][0]

    새 MyHandler 클래스가 만들어집니다.

10. `MyHandler` 클래스에 대한 다음 import 문을 추가합니다.

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;

        import com.microsoft.windowsazure.mobileservices.Registration;
        import com.microsoft.windowsazure.mobileservices.RegistrationCallback;

11. `MyHandler` 클래스에 대한 다음 구성원을 추가합니다.

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. `MyHandler` 클래스에서 다음 코드를 추가하여 **onRegistered** 메서드를 재정의합니다. 이 코드는 모바일 알림 허브에 장치를 등록합니다.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);

            ToDoActivity.mClient.getPush().register(gcmRegistrationId, null, new RegistrationCallback() {
                @Override
                public void onRegister(Registration registration, Exception exception) {
                      if (exception != null) {
                            // handle error
                      }
                }
            });
        }

13. `MyHandler` 클래스에서 다음 코드를 추가하여 **onReceive** 메서드를 재정의합니다. 그러면 알림이 수신되면 표시됩니다.

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

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.



  [모바일 서비스 Android SDK]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
