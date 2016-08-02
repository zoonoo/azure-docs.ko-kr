<properties
	pageTitle="Azure 알림 허브 및 Firebase Cloud Messaging을 사용하여 Android에 푸시 알림 보내기 | Microsoft Azure"
	description="이 자습서에서 Azure 알림 허브 및 Firebase Cloud Messaging을 사용하여 Android 장치로 푸시 알림을 보내는 방법을 알아봅니다."
	services="notification-hubs"
	documentationCenter="android"
	keywords="푸시 알림, 푸시알림, Android 푸시 알림, FCM, Firebase Cloud Messaging"
	authors="wesmc7777"
	manager="erikre"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="07/14/2016"
	ms.author="wesmc"/>

# Azure 알림 허브를 사용하여 Android에 푸시 알림 보내기

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##개요

> [AZURE.IMPORTANT] 이 항목에서는 FCM(Google Firebase Cloud Messaging)을 사용한 푸시 알림을 보여 줍니다. GCM(Google Cloud Messaging)을 사용하는 경우 [Azure 알림 허브 및 GCM을 사용하여 Android에 푸시 알림 보내기](notification-hubs-android-push-notification-google-gcm-get-started.md)를 참조하세요.

이 자습서에서는 Azure 알림 허브 및 Firebase Cloud Messaging을 사용하여 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.



[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

이 자습서에 대해 완료된 코드는 GitHub의 [여기](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStartedFirebase)서 다운로드할 수 있습니다.


##필수 조건

> [AZURE.IMPORTANT] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started)을 참조하세요.

- 이 자습서에서는 위에서 언급한 활성 Azure 계정 외에도 [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797) 최신 버전이 필요합니다.
- Firebase Cloud Messaging에 Android 2.3 이상이 필요합니다.
- Firebase Cloud Messaging에 Google 리포지토리 수정 27 이상이 필요합니다.
- Firebase Cloud Messaging에 Google Play Services 9.0.2 이상이 필요합니다.
- 이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다.


##새 Android Studio 프로젝트 만들기

1. Android Studio에서 새 Android Studio 프로젝트를 시작합니다.

   	![Android Studio - 새 프로젝트](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-new-project.png)

2. **Phone and Tablet** 폼 팩터와 지원할 **Minimum SDK**를 선택합니다. 그런 후 **Next**를 클릭합니다.

   	![Android Studio - 프로젝트 만들기 워크플로](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-choose-form-factor.png)

3. **빈 활동**를 기본 활동으로 선택하고 **다음**, **마침**를 차례로 클릭합니다.


##Firebase Cloud Messaging을 지원하는 프로젝트 만들기

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]


##새 알림 허브 구성

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

&emsp;&emsp;6. 알림 허브의 **설정** 블레이드에서 **알림 서비스**를 선택한 다음 **Google(GCM)**을 선택합니다. [Firebase 콘솔](https://firebase.google.com/console/)에서 이전에 복사한 FCM 서버 키를 입력하고 **저장**을 클릭합니다.

&emsp;&emsp;![Azure 알림 허브 - Google(GCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-gcm-api.png)

이제 알림 허브가 Firebase Cloud Messaging과 작동하도록 구성되었으며, 푸시 알림을 받고 보내도록 앱을 등록하기 위한 연결 문자열이 있습니다.

##<a id="connecting-app"></a>알림 허브에 앱 연결

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Play 서비스 추가](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###Azure 알림 허브 라이브러리 추가


1. **앱**의 `Build.Gradle` 파일에서 **종속성** 섹션에 다음 줄을 추가합니다.

		compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
		compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. **종속성** 섹션 다음에 다음 리포지트리를 추가합니다.

		repositories {
		    maven {
		        url "http://dl.bintray.com/microsoftazuremobile/SDK"
		    }
		}

### AndroidManifest.xml을 업데이트합니다.


1. FCM을 지원하기 위해, 코드에 [Google FirebaseInstanceId API](https://firebase.google.com/docs/reference/android/com/google/firebase/iid/FirebaseInstanceId)를 사용하여 [등록 토큰 가져오기](https://firebase.google.com/docs/cloud-messaging/android/client#sample-register)에 사용되는 인스턴스 ID 수신기 서비스를 구현해야 합니다. 이 자습서에서는 클래스의 이름을 `MyInstanceIDService`라고 하겠습니다.
 
	AndroidManifest.xml 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다.

        <service android:name=".MyInstanceIDService">
            <intent-filter>
                <action android:name="com.google.firebase.INSTANCE_ID_EVENT"/>
            </intent-filter>
        </service>



2. FirebaseInstanceId API에서 FCM 등록 토큰을 수신하면 해당 토큰을 사용하여 [Azure 알림 허브에 등록](notification-hubs-push-notification-registration-management.md)합니다. 이 등록은 `RegistrationIntentService`라는 `IntentService`를 사용하여 백그라운드에서 지원합니다. 이 서비스에는 FCM 등록 토큰 새로 고침에 대한 책임이 있습니다.
 
	AndroidManifest.xml 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다.

        <service
            android:name=".RegistrationIntentService"
            android:exported="false">
        </service>



3. 또한 알림을 수신하도록 수신기를 정의할 것입니다. 다음 수신기 정의를 `<application>` 태그 안의 AndroidManifest.xml 파일에 추가합니다. `<your package>` 자리 표시자를 `AndroidManifest.xml` 파일 맨 위에 표시된 실제 패키지 이름으로 바꿉니다.

		<receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
		    android:permission="com.google.android.c2dm.permission.SEND">
		    <intent-filter>
		        <action android:name="com.google.android.c2dm.intent.RECEIVE" />
		        <category android:name="<your package name>" />
		    </intent-filter>
		</receiver>



4. `</application>` 태그 아래에 다음 필수 FCM 관련 권한을 추가합니다. 반드시 `<your package>`을(를) `AndroidManifest.xml` 파일 맨 위에 있는 패키지 이름으로 바꿉니다.

	이러한 사용 권한에 대한 자세한 내용은 [Android용 GCM 클라이언트 앱 설치](https://developers.google.com/cloud-messaging/android/client#manifest) 및 [Firebase Cloud Messaging에 Android용 GCM 클라이언트 앱 마이그레이션](https://developers.google.com/cloud-messaging/android/android-migrate-fcm#remove_the_permissions_required_by_gcm)을 참조하세요.

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />


### 코드 추가


1. 프로젝트 뷰에서 **앱** > **원본** > **기본** > **java**를 확장합니다. **java** 아래의 패키지 폴더를 마우스 오른쪽 단추로 클릭하고 **New**, **Java Class**를 차례로 클릭합니다. `NotificationSettings`(이)라는 새 클래스를 추가합니다.

	![Android Studio - 새 Java 프로젝트](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hub-android-new-class.png)

	`NotificationSettings` 클래스에 대한 다음 코드에서 아래 세 개의 자리 표시자를 업데이트합니다.
	* **SenderId**: [Firebase 콘솔](https://firebase.google.com/console/)의 프로젝트 설정에 있는 **클라우드 메시징** 탭에서 이전에 가져온 보낸 사람 ID입니다.
	* **HubListenConnectionString**: 허브의 **DefaultListenAccessSignature** 연결 문자열입니다. **Azure 포털**에 있는 허브의 **설정** 블레이드에서 [액세스 정책]을 클릭하여 이 연결 문자열을 복사할 수 있습니다.
	* **HubName**: [Azure 포털]의 허브 블레이드에 표시되는 알림 허브 이름을 사용합니다.

	`NotificationSettings` 코드:

		public class NotificationSettings {
		    public static String SenderId = "<Your project number>";
		    public static String HubName = "<Your HubName>";
		    public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
		}

2. 위의 단계에 따라 `MyInstanceIDService`라는 또 다른 새 클래스를 추가합니다. 이것으로 인스턴스 ID 수신기 서비스가 구현될 것입니다.

	이 클래스의 코드는 `IntentService`를 호출하여 백그라운드에서 [FCM 토큰을 새로 고칩니다](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).

		import android.content.Intent;
		import android.util.Log;
		import com.google.firebase.iid.FirebaseInstanceIdService;
		
		
		public class MyInstanceIDService extends FirebaseInstanceIdService {
		
		    private static final String TAG = "MyInstanceIDService";
		
		    @Override
		    public void onTokenRefresh() {
		
		        Log.d(TAG, "Refreshing GCM Registration Token");
		
		        Intent intent = new Intent(this, RegistrationIntentService.class);
		        startService(intent);
		    }
		};


3. `RegistrationIntentService`라는 프로젝트에 또 다른 새 클래스를 추가합니다. 그러면 [FCM 토큰 새로 고침](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) 및 [알림 허브 등록](notification-hubs-push-notification-registration-management.md)을 처리하는 `IntentService`에 대한 솔루션이 구현됩니다.

	이 클래스에 대해 다음 코드를 사용합니다.

		import android.app.IntentService;
		import android.content.Intent;
		import android.content.SharedPreferences;
		import android.preference.PreferenceManager;
		import android.util.Log;		
		import com.google.firebase.iid.FirebaseInstanceId;
		import com.microsoft.windowsazure.messaging.NotificationHub;
		
		public class RegistrationIntentService extends IntentService {
		
		    private static final String TAG = "RegIntentService";
		
		    private NotificationHub hub;
		
		    public RegistrationIntentService() {
		        super(TAG);
		    }
		
		    @Override
		    protected void onHandleIntent(Intent intent) {
		
		        SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
		        String resultString = null;
		        String regID = null;
		        String storedToken = null;
		
		        try {
		            String FCM_token = FirebaseInstanceId.getInstance().getToken();
		            Log.d(TAG, "FCM Registration Token: " + FCM_token);
		
		            // Storing the registration id that indicates whether the generated token has been
		            // sent to your server. If it is not stored, send the token to your server,
		            // otherwise your server should have already received the token.
		            if (((regID=sharedPreferences.getString("registrationID", null)) == null)){
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/ko-KR/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            // Check if the token may have been compromised and needs refreshing.
		            else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {
		
		                NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
		                        NotificationSettings.HubListenConnectionString, this);
		                Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
		                regID = hub.register(FCM_token).getRegistrationId();
		
		                // If you want to use tags...
		                // Refer to : https://azure.microsoft.com/ko-KR/documentation/articles/notification-hubs-routing-tag-expressions/
		                // regID = hub.register(token, "tag1,tag2").getRegistrationId();
		
		                resultString = "New NH Registration Successfully - RegId : " + regID;
		                Log.d(TAG, resultString);
		
		                sharedPreferences.edit().putString("registrationID", regID ).apply();
		                sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
		            }
		
		            else {
		                resultString = "Previously Registered Successfully - RegId : " + regID;
		            }
		        } catch (Exception e) {
		            Log.e(TAG, resultString="Failed to complete registration", e);
		            // If an exception happens while fetching the new token or updating our registration data
		            // on a third-party server, this ensures that we'll attempt the update at a later time.
		        }
		
		        // Notify UI that registration has completed.
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(resultString);
		        }
		    }
		}


		
4. `MainActivity` 클래스에서 다음 `import` 문을 클래스 선언 위에 추가합니다.

		import com.google.android.gms.common.ConnectionResult;
		import com.google.android.gms.common.GoogleApiAvailability;
		import com.microsoft.windowsazure.notifications.NotificationsManager;
		import android.content.Intent;
		import android.util.Log;
		import android.widget.TextView;
		import android.widget.Toast;

5. 클래스의 맨 위에 다음과 같은 private 멤버를 추가합니다. [Google 권장 사항에 따라 Google Play Services의 가용성을 확인](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk)합니다.

	    public static MainActivity mainActivity;
    	public static Boolean isVisible = false;	
	    private static final String TAG = "MainActivity";
	    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. `MainActivity` 클래스에서 Google Play Services 가용성에 다음 메서드를 추가합니다.

	    /**
	     * Check the device to make sure it has the Google Play Services APK. If
	     * it doesn't, display a dialog that allows users to download the APK from
	     * the Google Play Store or enable it in the device's system settings.
	     */
	    private boolean checkPlayServices() {
	        GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
	        int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
	        if (resultCode != ConnectionResult.SUCCESS) {
	            if (apiAvailability.isUserResolvableError(resultCode)) {
	                apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
	                        .show();
	            } else {
	                Log.i(TAG, "This device is not supported by Google Play Services.");
	                ToastNotify("This device is not supported by Google Play Services.");
	                finish();
	            }
	            return false;
	        }
	        return true;
	    }


7. `MainActivity` 클래스에서 Google Play Services를 검사한 후 `IntentService`를 호출하는 다음 코드를 추가하여 FCM 등록 토큰을 가져와서 알림 허브에 등록합니다.

	    public void registerWithNotificationHubs()
	    {
	        if (checkPlayServices()) {
	            // Start IntentService to register this application with FCM.
	            Intent intent = new Intent(this, RegistrationIntentService.class);
	            startService(intent);
	        }
	    }


8. `MainActivity` 클래스의 `OnCreate` 메서드에서 활동이 생성되면 등록 프로세스를 시작하는 다음 코드를 추가합니다.

	    @Override
	    protected void onCreate(Bundle savedInstanceState) {
	        super.onCreate(savedInstanceState);
	        setContentView(R.layout.activity_main);
	
	        mainActivity = this;
	        NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
	        registerWithNotificationHubs();
	    }


9. `MainActivity`에 이러한 추가 메서드를 추가하여 앱 상태를 확인하고 앱에서 상태를 보고합니다.

	    @Override
	    protected void onStart() {
	        super.onStart();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onPause() {
	        super.onPause();
	        isVisible = false;
	    }
	
	    @Override
	    protected void onResume() {
	        super.onResume();
	        isVisible = true;
	    }
	
	    @Override
	    protected void onStop() {
	        super.onStop();
	        isVisible = false;
	    }
	
	    public void ToastNotify(final String notificationMessage) {
	        runOnUiThread(new Runnable() {
	            @Override
	            public void run() {
	                Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
	                TextView helloText = (TextView) findViewById(R.id.text_hello);
	                helloText.setText(notificationMessage);
	            }
	        });
	    }


10. `ToastNotify` 메서드는 *"Hello World"* `TextView` 컨트롤을 사용하여 앱에서 영구적으로 상태 및 알림을 보고합니다. activity\_main.xml 레이아웃에서 해당 컨트롤에 대한 다음 ID를 추가합니다.

        android:id="@+id/text_hello"

11. 다음으로 AndroidManifest.xml에서 정의한 수신기에 대한 하위 클래스를 추가합니다. `MyHandler`라는 프로젝트에 또 다른 새 클래스를 추가합니다.

12. 그런 다음 `MyHandler.java`의 맨 위에 다음 import 문을 추가합니다.

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.media.RingtoneManager;
		import android.net.Uri;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. `MyHandler` 클래스에 대해 다음 코드를 추가하여 `com.microsoft.windowsazure.notifications.NotificationsHandler`의 하위 클래스로 만듭니다.

	이 코드는 처리기가 받은 알림을 보고할 수 있도록 `OnReceive` 메서드를 재정의합니다. 또한 처리기는 `sendNotification()` 메서드를 사용하여 Android Notification Manager에 푸시 알림을 보냅니다. `sendNotification()` 메서드는 앱이 실행되지 않을 때, 알림이 수신될 때 실행해야 합니다.

		public class MyHandler extends NotificationsHandler {
		    public static final int NOTIFICATION_ID = 1;
		    private NotificationManager mNotificationManager;
		    NotificationCompat.Builder builder;
		    Context ctx;
		
		    @Override
		    public void onReceive(Context context, Bundle bundle) {
		        ctx = context;
		        String nhMessage = bundle.getString("message");
		        sendNotification(nhMessage);
		        if (MainActivity.isVisible) {
		            MainActivity.mainActivity.ToastNotify(nhMessage);
		        }
		    }
		
		    private void sendNotification(String msg) {
		
		        Intent intent = new Intent(ctx, MainActivity.class);
		        intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
		
		        mNotificationManager = (NotificationManager)
		                ctx.getSystemService(Context.NOTIFICATION_SERVICE);
		
		        PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
		                intent, PendingIntent.FLAG_ONE_SHOT);
		
		        Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
		        NotificationCompat.Builder mBuilder =
		                new NotificationCompat.Builder(ctx)
		                        .setSmallIcon(R.mipmap.ic_launcher)
		                        .setContentTitle("Notification Hub Demo")
		                        .setStyle(new NotificationCompat.BigTextStyle()
		                                .bigText(msg))
		                        .setSound(defaultSoundUri)
		                        .setContentText(msg);
		
		        mBuilder.setContentIntent(contentIntent);
		        mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		    }
		}


14. Android Studio의 메뉴 모음에서 **빌드** > **프로젝트 다시 빌드**를 클릭하여 코드에 오류가 없는지 확인합니다.
15. 장치에서 앱을 실행하고 알림 허브를 사용하여 성공적으로 등록되는지 확인합니다.

	> [AZURE.NOTE] 등록은 인스턴스 ID 서비스의 `onTokenRefresh()` 메서드를 호출할 때까지 초기 시작 시 실패할 수 있습니다. 새로 고침은 알림 허브를 사용하여 성공적으로 등록하기 시작해야 합니다.

##푸시 알림 보내기

[Azure 포털]을 통해 푸시 알림을 보내서 앱에서 푸시 알림 수신을 테스트할 수 있습니다. 아래와 같이 허브 블레이드의 **문제 해결** 섹션을 살펴보세요.

![Azure 알림 허브 - 전송 테스트](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## (선택 사항) 앱에서 바로 푸시 알림 보내기

>[AZURE.IMPORTANT] 클라이언트 앱에서 알림을 보내는 이 예제는 학습 목적으로만 제공됩니다. 이를 위해서 `DefaultFullSharedAccessSignature`이 클라이언트 응용 프로그램에 표시되어야 하므로 사용자가 클라이언트에 무단으로 알림을 보내는 액세스 권한을 가질 수 있는 위험에 알림 허브가 노출됩니다.

일반적으로, 백 엔드 서버를 사용하여 알림을 보냅니다. 경우에 따라, 클라이언트 응용 프로그램에서 직접 푸시 알림을 보낼 수 있기를 원하기도 합니다. 이 섹션은 [Azure 알림 허브 REST API](https://msdn.microsoft.com/library/azure/dn223264.aspx)를 사용하여 클라이언트에서 알림을 보내는 방법을 설명합니다.

1. Android Studio 프로젝트 뷰에서 **앱** > **원본** > **기본** > **자원** > **레이아웃**을 확장합니다. `activity_main.xml` 레이아웃 파일을 열고 **텍스트** 탭을 클릭하여 파일의 텍스트 내용을 업데이트합니다. 아래 코드로 업데이트하여 알림 허브에 푸시 알림 메시지를 보내는 새 `Button` 및 `EditText` 컨트롤을 추가합니다. 이 코드를 맨 아래의 `</RelativeLayout>` 바로 앞에 추가합니다.

	    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

	    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Android Studio 프로젝트 뷰에서 **앱** > **원본** > **기본** > **자원** > **값**을 확장합니다. `strings.xml` 파일을 열고 새 `Button` 및 `EditText` 컨트롤에서 참조하는 문자열 값을 추가합니다. 파일 맨 아래의 `</resources>` 바로 앞에 이를 추가합니다.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. `NotificationSetting.java` 파일에서 `NotificationSettings` 클래스에 다음 설정을 추가합니다.

	허브에 **DefaultFullSharedAccessSignature** 연결 문자열을 사용하여 `HubFullAccess`을 업데이트합니다. 알림 허브에 대한 **설정** 블레이드에서 **액세스 정책**을 클릭하여 [Azure 포털]에서 이 연결 문자열을 복사할 수 있습니다.

		public static String HubFullAccess = "<Enter Your DefaultFullSharedAccessSignature Connection string>";

4. `MainActivity.java` 파일에서 `MainActivity` 클래스 위에 다음 `import` 문을 추가합니다.

		import java.io.BufferedOutputStream;
		import java.io.BufferedReader;
		import java.io.InputStreamReader;
		import java.io.OutputStream;
		import java.net.HttpURLConnection;
		import java.net.URL;
		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;
		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

6. `MainActivity.java` 파일에서 다음 멤버를 `MainActivity` 클래스 위에 추가합니다.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;

6. 알림 허브로 메시지를 보낼 POST 요청을 인증하기 위해 SaS(Software Access Signature) 토큰을 만들어야 합니다. 연결 문자열에서 키 데이터를 구문 분석한 다음 [일반적인 개념](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API 참조에 설명된 대로 SaS 토큰을 만들면 됩니다. 다음 코드는 구현 예제입니다.

	`MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 연결 문자열의 구문을 분석합니다.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
	     * to parse the connection string so a SaS authentication token can be
	     * constructed.
	     *
	     * @param connectionString This must be the DefaultFullSharedAccess connection
	     *                         string for this example.
	     */
	    private void ParseConnectionString(String connectionString)
	    {
	        String[] parts = connectionString.split(";");
	        if (parts.length != 3)
	            throw new RuntimeException("Error parsing connection string: "
	                    + connectionString);
	
	        for (int i = 0; i < parts.length; i++) {
	            if (parts[i].startsWith("Endpoint")) {
	                this.HubEndpoint = "https" + parts[i].substring(11);
	            } else if (parts[i].startsWith("SharedAccessKeyName")) {
	                this.HubSasKeyName = parts[i].substring(20);
	            } else if (parts[i].startsWith("SharedAccessKey")) {
	                this.HubSasKeyValue = parts[i].substring(16);
	            }
	        }
	    }


7. `MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 SaS 인증 토큰을 만듭니다.

	    /**
	     * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
	     * construct a SaS token from the access key to authenticate a request.
	     *
	     * @param uri The unencoded resource URI string for this operation. The resource
	     *            URI is the full URI of the Service Bus resource to which access is
	     *            claimed. For example,
	     *            "http://<namespace>.servicebus.windows.net/<hubName>"
	     */
	    private String generateSasToken(String uri) {
	
	        String targetUri;
	        String token = null;
	        try {
	            targetUri = URLEncoder
	                    .encode(uri.toString().toLowerCase(), "UTF-8")
	                    .toLowerCase();
	
	            long expiresOnDate = System.currentTimeMillis();
	            int expiresInMins = 60; // 1 hour
	            expiresOnDate += expiresInMins * 60 * 1000;
	            long expires = expiresOnDate / 1000;
	            String toSign = targetUri + "\n" + expires;
	
	            // Get an hmac_sha1 key from the raw key bytes
	            byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
	            SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
	
	            // Get an hmac_sha1 Mac instance and initialize with the signing key
	            Mac mac = Mac.getInstance("HmacSHA256");
	            mac.init(signingKey);
	
	            // Compute the hmac on input data bytes
	            byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
	
	            // Using android.util.Base64 for Android Studio instead of
	            // Apache commons codec
	            String signature = URLEncoder.encode(
	                    Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
	
	            // Construct authorization string
	            token = "SharedAccessSignature sr=" + targetUri + "&sig="
	                    + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
	        } catch (Exception e) {
	            if (isVisible) {
	                ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
	            }
	        }
	
	        return token;
	    }



8. `MainActivity.java`에서 `MainActivity` 클래스에 다음 메서드를 추가하여 **알림 보내기** 단추 클릭을 처리하고 내장된 REST API를 사용하여 허브에 푸시 알림 메시지를 보냅니다.

	    /**
	     * Send Notification button click handler. This method parses the
	     * DefaultFullSharedAccess connection string and generates a SaS token. The
	     * token is added to the Authorization header on the POST request to the
	     * notification hub. The text in the editTextNotificationMessage control
	     * is added as the JSON body for the request to add a GCM message to the hub.
	     *
	     * @param v
	     */
	    public void sendNotificationButtonOnClick(View v) {
	        EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
	        final String json = "{"data":{"message":"" + notificationText.getText().toString() + ""}}";
	
	        new Thread()
	        {
	            public void run()
	            {
	                try
	                {
	                    // Based on reference documentation...
	                    // http://msdn.microsoft.com/library/azure/dn223273.aspx
	                    ParseConnectionString(NotificationSettings.HubFullAccess);
	                    URL url = new URL(HubEndpoint + NotificationSettings.HubName +
	                            "/messages/?api-version=2015-01");
	
	                    HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
	
	                    try {
	                        // POST request
	                        urlConnection.setDoOutput(true);
	
	                        // Authenticate the POST request with the SaS token
	                        urlConnection.setRequestProperty("Authorization", 
								generateSasToken(url.toString()));
	
	                        // Notification format should be GCM
	                        urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
	
	                        // Include any tags
	                        // Example below targets 3 specific tags
	                        // Refer to : https://azure.microsoft.com/ko-KR/documentation/articles/notification-hubs-routing-tag-expressions/
	                        // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
							//		"tag1 || tag2 || tag3");
	
	                        // Send notification message
	                        urlConnection.setFixedLengthStreamingMode(json.length());
	                        OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
	                        bodyStream.write(json.getBytes());
	                        bodyStream.close();
	
	                        // Get reponse
	                        urlConnection.connect();
	                        int responseCode = urlConnection.getResponseCode();
	                        if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
	                        }
	                    } finally {
	                        urlConnection.disconnect();
	                    }
	                }
	                catch(Exception e)
	                {
	                    if (isVisible) {
	                        ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
	                    }
	                }
	            }
	        }.start();
	    }



##앱 테스트

####에뮬레이터의 푸시 알림

에뮬레이터 내부에서 푸시 알림을 테스트하려는 경우 에뮬레이터 이미지가 앱에 대해 선택한 Google API 수준을 지원하는지 확인합니다. 이미지가 네이티브 Google API를 지원하지 않으면 **SERVICE\_NOT\_AVAILABLE** 예외가 발생합니다.

또한 **설정** > **계정**에서 실행 중인 에뮬레이터에 Google 계정을 추가해야 합니다. 그렇지 않으면 GCM 등록 시 **AUTHENTICATION\_FAILED** 예외가 발생할 수 있습니다.

####응용 프로그램 실행

1. 앱을 실행하고 등록에 성공한 경우 등록 ID가 보고되는지 확인합니다.

   	![Android에서 테스트 - 채널 등록](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-registered.png)

2. 허브에 등록된 모든 Android 장치로 보낼 알림 메시지를 입력합니다.

   	![Android에서 테스트 - 메시지 보내기](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-set-message.png)

3. **알림 보내기**를 누릅니다. 앱을 실행 중인 장치에 푸시 알림 메시지가 포함된 `AlertDialog` 인스턴스가 표시됩니다. 앱이 실행되고 있지는 않지만 이전에 푸시 알림을 등록한 장치는 Android 알림 관리자에서 알림을 받습니다. 왼쪽 위 모서리에서 아래로 쓸어 넘겨 알림을 볼 수 있습니다.

   	![Android에서 테스트 - 알림](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-android-studio-received-message.png)

##다음 단계

다음 단계로 [알림 허브를 사용하여 사용자에게 알림 푸시] 자습서를 수행하는 것이 좋습니다. 특정 사용자를 대상으로 하는 태그를 사용하여 ASP.NET 백엔드에서 알림을 보내는 방법을 보여 줍니다.

사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기] 자습서를 확인하세요.

알림 허브에 대한 일반적인 정보를 알아보려면 [알림 허브 지침]을 참조하세요.

<!-- Images. -->



<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[알림 허브 지침]: notification-hubs-push-notification-overview.md
[알림 허브를 사용하여 사용자에게 알림 푸시]: notification-hubs-aspnet-backend-android-notify-users.md
[알림 허브를 사용하여 뉴스 속보 보내기]: notification-hubs-aspnet-backend-android-breaking-news.md
[Azure 포털]: https://portal.azure.com
[액세스 정책]: https://portal.azure.com

<!---HONumber=AcomDC_0720_2016-->