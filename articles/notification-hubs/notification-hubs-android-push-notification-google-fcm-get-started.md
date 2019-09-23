---
title: Azure Notification Hubs 및 Firebase Cloud Messaging을 사용하여 Android 앱에 알림 푸시 | Microsoft Docs
description: 이 자습서에서 Azure Notification Hubs 및 Google Firebase Cloud Messaging을 사용하여 Android 디바이스로 푸시 알림을 보내는 방법을 알아봅니다.
services: notification-hubs
documentationcenter: android
keywords: 푸시 알림, 푸시알림, Android 푸시 알림, FCM, Firebase Cloud Messaging
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 02298560-da61-4bbb-b07c-e79bd520e420
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/11/2019
ms.author: jowargo
ms.openlocfilehash: f1a6980efd7614ce245c45852b6ce08eb71d1cfd
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/12/2019
ms.locfileid: "70935128"
---
# <a name="tutorial-push-notifications-to-android-devices-by-using-azure-notification-hubs-and-google-firebase-cloud-messaging"></a>자습서: Azure Notification Hubs 및 Google Firebase Cloud Messaging을 사용하여 Android 디바이스에 알림 푸시

[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

이 자습서에서는 Azure Notification Hubs 및 FCM(Firebase Cloud Messaging)을 사용하여 Android 애플리케이션에 알림을 푸시하는 방법을 보여줍니다. 이 자습서에서는 FCM(Firebase Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.

이 자습서의 완성된 코드는 [GitHub에서](https://github.com/Azure/azure-notificationhubs-android/tree/master/samples/FCMTutorialApp) 다운로드할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * Android Studio 프로젝트를 만듭니다.
> * Firebase Cloud Messaging을 지원하는 Firebase 프로젝트를 만듭니다.
> * 허브를 만듭니다.
> * 허브에 앱을 연결합니다.
> * 앱을 테스트합니다.

## <a name="prerequisites"></a>필수 조건

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/free/)을 참조하세요. 

또한 다음 항목이 필요합니다. 

* 최신 버전의 [Android Studio](https://go.microsoft.com/fwlink/?LinkId=389797)
* Android 2.3 이상(Firebase Cloud Messaging에 필요)
* Google Repository 버전 27 이상(Firebase Cloud Messaging에 필요)
* Google Play Services 9.0.2 이상(Firebase Cloud Messaging에 필요)

이 자습서를 완료해야 다른 모든 Android 앱용 Notification Hubs 자습서를 진행할 수 있습니다.

## <a name="create-an-android-studio-project"></a>Android Studio 프로젝트 만들기

1. Android Studio를 시작합니다.
2. **File**(파일)을 선택하고, **New**(신규)를 가리킨 후, **New Project**(새 프로젝트)를 선택합니다. 
2. **Choose your project** 페이지에서 **Empty Activity**를 선택한 후 **Next**를 선택합니다. 
3. **Configure your project** 페이지에서 다음 단계를 수행합니다. 
    1. 애플리케이션의 이름을 입력합니다.
    2. 프로젝트 파일을 저장할 위치를 지정합니다. 
    3. **마침**을 선택합니다. 

        ![프로젝트 구성](./media/notification-hubs-android-push-notification-google-fcm-get-started/configure-project.png)

## <a name="create-a-firebase-project-that-supports-fcm"></a>FCM을 지원하는 Firebase 프로젝트 만들기

[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-a-hub"></a>허브 구성

[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

### <a name="configure-firebase-cloud-messaging-settings-for-the-hub"></a>허브에 대한 Firebase Cloud Messaging 설정 구성

1. **설정** 아래의 왼쪽 창에서 **Google(GCM/FCM)** 을 선택합니다. 
2. 앞에서 저장한 FCM 프로젝트의 **서버 키**를 입력합니다. 
3. 도구 모음에서 **저장**을 선택합니다. 

    ![Azure 알림 허브 - Google(FCM)](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)
4. Azure Portal에서 허브가 업데이트되었다는 경고 메시지를 표시합니다. **Save** 단추가 비활성화됩니다. 

이제 허브가 Firebase Cloud Messaging과 함께 작동하도록 구성되었습니다. 디바이스에 알림을 보내고, 앱을 등록하여 알림을 수신하는 데 필요한 연결 문자열도 있습니다.

## <a id="connecting-app"></a>알림 허브에 앱 연결

### <a name="add-google-play-services-to-the-project"></a>프로젝트에 Google Play Services 추가

1. Android Studio의 메뉴에서 **도구**를 선택하고, **SDK Manager**를 선택합니다. 
2. 프로젝트에서 사용되는 Android SDK의 대상 버전을 선택합니다. 그런 다음 **패키지 세부 정보 표시**를 선택합니다. 

    ![Android SDK Manager - 대상 버전 선택](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. **Google API**를 선택합니다(아직 설치되지 않은 경우).

    ![Android SDK Manager - Google API 선택](./media/notification-hubs-android-studio-add-google-play-services/googole-apis-selected.png)
4. **SDK 도구** 탭으로 전환합니다. Google Play 서비스를 아직 설치하지 않은 경우 다음 이미지처럼 **Google Play 서비스**를 선택합니다. 그런 다음 **적용**을 선택하여 설치합니다. 이후 단계에서 사용할 수 있도록 SDK 경로를 기록해 둡니다.

    ![Android SDK Manager - Google Play 서비스 선택](./media/notification-hubs-android-studio-add-google-play-services/google-play-services-selected.png)
3. **변경 확인** 대화 상자가 나타나면 **확인**을 선택합니다. 구성 요소 설치 관리자가 요청된 구성 요소를 설치합니다. 구성 요소가 설치되면 **완료**를 선택합니다.
4. **확인**을 선택하여 **새 프로젝트 설정** 대화 상자를 닫습니다.  
1. AndroidManifest.xml 파일을 연 다음 *application* 태그에 다음 태그를 추가합니다.

    ```xml
    <meta-data android:name="com.google.android.gms.version"
         android:value="@integer/google_play_services_version" />
    ```


### <a name="add-azure-notification-hubs-libraries"></a>Azure Notification Hubs 라이브러리 추가

1. 앱의 Build.Gradle 파일에서 dependencies 섹션에 다음 줄을 추가합니다.

    ```gradle
    implementation 'com.microsoft.azure:notification-hubs-android-sdk:0.6@aar'
    ```

2. 종속성 섹션 뒤에 다음 리포지토리를 추가합니다.

    ```gradle
    repositories {
        maven {
            url "https://dl.bintray.com/microsoftazuremobile/SDK"
        }
    }
    ```

### <a name="add-google-firebase-support"></a>Google Firebase 지원 추가

1. 앱의 Build.Gradle 파일에서 **dependencies** 섹션에 다음 줄이 아직 없는 경우 추가합니다. 

    ```gradle
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:17.3.4'
    ```

2. 파일 끝에 다음 플러그 인이 아직 없는 경우 추가합니다. 

    ```gradle
    apply plugin: 'com.google.gms.google-services'
    ```
3. 도구 모음에서 **지금 동기화**를 선택합니다.

### <a name="update-the-androidmanifestxml-file"></a>AndroidManifest.xml 파일 업데이트

1. FCM 등록 토큰을 받으면 해당 토큰을 사용하여 [Azure Notification Hubs에 등록](notification-hubs-push-notification-registration-management.md)합니다. `RegistrationIntentService`라는 `IntentService`를 사용하여 백그라운드에서 이 등록을 지원합니다. 또한 이 서비스는 FCM 등록 토큰을 새로 고칩니다. 또한 `FirebaseService`라는 클래스를 `FirebaseMessagingService`의 하위 클래스로 만들고 알림을 수신 및 처리하도록 `onMessageReceived` 메서드를 재정의합니다. 

    AndroidManifest.xml 파일의 `<application>` 태그 내부에 다음 서비스 정의를 추가합니다.

    ```xml
    <service
        android:name=".RegistrationIntentService"
        android:exported="false">
    </service>
    <service
        android:name=".FirebaseService"
        android:exported="false">
        <intent-filter>
            <action android:name="com.google.firebase.MESSAGING_EVENT" />
        </intent-filter>
    </service>
    ```
3. `</application>` 태그 아래에 다음과 같은 FCM 관련 필수 권한을 추가합니다.

    ```xml
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
    ```

### <a name="add-code"></a>코드 추가

1. 프로젝트 뷰에서 **앱** > **src** > **기본** > **java**를 확장합니다. **java** 아래의 패키지 폴더를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**, **Java 클래스**를 차례로 선택합니다. 이름에 **NotificationSettings**를 입력한 후 **확인**을 선택합니다.

    아래의 `NotificationSettings` 클래스에 대한 코드에서 다음 세 개의 자리 표시자를 업데이트합니다.

   * **HubListenConnectionString**: 허브의 **DefaultListenAccessSignature** 연결 문자열입니다. [Azure Portal]의 허브에서 **액세스 정책**을 클릭하여 해당 연결 문자열을 복사할 수 있습니다.
   * **HubName**: [Azure Portal]의 허브 페이지에 표시되는 허브 이름을 사용합니다.

     `NotificationSettings` 코드:

        ```java
        public class NotificationSettings {
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Enter your DefaultListenSharedAccessSignature connection string>";
        }
        ```

     > [!IMPORTANT]
     > 허브의 **이름**과 **DefaultListenSharedAccessSignature**를 입력하고 계속 진행합니다. 

2. `RegistrationIntentService`라는 프로젝트에 또 다른 새 클래스를 추가합니다. 이 클래스는 `IntentService` 인터페이스를 구현합니다. 또한 [FCM 토큰 새로 고침](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) 및 [알림 허브에 등록](notification-hubs-push-notification-registration-management.md)을 처리합니다.

    이 클래스에 대해 다음 코드를 사용합니다.

    ```java
    import android.app.IntentService;
    import android.content.Intent;
    import android.content.SharedPreferences;
    import android.preference.PreferenceManager;
    import android.util.Log;
    import com.google.android.gms.tasks.OnSuccessListener;
    import com.google.firebase.iid.FirebaseInstanceId;
    import com.google.firebase.iid.InstanceIdResult;
    import com.microsoft.windowsazure.messaging.NotificationHub;
    import java.util.concurrent.TimeUnit;

    public class RegistrationIntentService extends IntentService {

        private static final String TAG = "RegIntentService";
        String FCM_token = null;

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
                FirebaseInstanceId.getInstance().getInstanceId().addOnSuccessListener(new OnSuccessListener<InstanceIdResult>() { 
                    @Override 
                    public void onSuccess(InstanceIdResult instanceIdResult) { 
                        FCM_token = instanceIdResult.getToken(); 
                        Log.d(TAG, "FCM Registration Token: " + FCM_token); 
                    } 
                }); 
                TimeUnit.SECONDS.sleep(1);

                // Storing the registration ID that indicates whether the generated token has been
                // sent to your server. If it is not stored, send the token to your server.
                // Otherwise, your server should have already received the token.
                if (((regID=sharedPreferences.getString("registrationID", null)) == null)){

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "Attempting a new registration with NH using FCM token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
                    // regID = hub.register(token, "tag1,tag2").getRegistrationId();

                    resultString = "New NH Registration Successfully - RegId : " + regID;
                    Log.d(TAG, resultString);

                    sharedPreferences.edit().putString("registrationID", regID ).apply();
                    sharedPreferences.edit().putString("FCMtoken", FCM_token ).apply();
                }

                // Check to see if the token has been compromised and needs refreshing.
                else if ((storedToken=sharedPreferences.getString("FCMtoken", "")) != FCM_token) {

                    NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                            NotificationSettings.HubListenConnectionString, this);
                    Log.d(TAG, "NH Registration refreshing with token : " + FCM_token);
                    regID = hub.register(FCM_token).getRegistrationId();

                    // If you want to use tags...
                    // Refer to : https://azure.microsoft.com/documentation/articles/notification-hubs-routing-tag-expressions/
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
                // If an exception happens while fetching the new token or updating registration data
                // on a third-party server, this ensures that we'll attempt the update at a later time.
            }

            // Notify UI that registration has completed.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(resultString);
            }
        }
    }
    ```

3. `MainActivity` 클래스에서 다음 `import` 문을 클래스 선언 위에 추가합니다.

    ```java
    import com.google.android.gms.common.ConnectionResult;
    import com.google.android.gms.common.GoogleApiAvailability;
    import android.content.Intent;
    import android.util.Log;
    import android.widget.TextView;
    import android.widget.Toast;
    ```

4. 클래스 위에 다음 멤버를 추가합니다. 이러한 필드를 사용하여 [Google에서 권장한 대로 Google Play 서비스의 가용성을 확인합니다](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

    ```java
    public static MainActivity mainActivity;
    public static Boolean isVisible = false;
    private static final String TAG = "MainActivity";
    private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;
    ```

5. `MainActivity` 클래스에서 Google Play 서비스의 가용성을 확인하는 다음 메서드를 추가합니다.

    ```java
    /**
    * Check the device to make sure it has the Google Play Services APK. If
    * it doesn't, display a dialog box that enables  users to download the APK from
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
    ```

6. `MainActivity` 클래스에서 `IntentService`를 호출하기 전에 Google Play 서비스를 확인하는 다음 코드를 추가하여 FCM 등록 토큰을 가져오고 허브에 등록합니다.

    ```java
    public void registerWithNotificationHubs()
    {
        if (checkPlayServices()) {
            // Start IntentService to register this application with FCM.
            Intent intent = new Intent(this, RegistrationIntentService.class);
            startService(intent);
        }
    }
    ```

7. `MainActivity` 클래스의 `OnCreate` 메서드에서 활동이 생성되면 등록 프로세스를 시작하는 다음 코드를 추가합니다.

    ```java
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        mainActivity = this;
        registerWithNotificationHubs();
        FirebaseService.createChannelAndHandleNotifications(getApplicationContext());
    }
    ```

8. 앱 상태를 확인하고 앱에서 상태를 보고하려면 `MainActivity`에 이러한 추가 메서드를 추가합니다.

    ```java
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
    ```

9. `ToastNotify` 메서드는 *"Hello World"* `TextView` 컨트롤을 사용하여 앱에서 영구적으로 상태 및 알림을 보고합니다. **res** > **layout** > **activity_main.xml** 레이아웃에서 해당 컨트롤에 대한 다음 ID를 추가합니다.

    ```java
    android:id="@+id/text_hello"
    ```

    ![Azure Notification Hubs - 전송 테스트](./media/notification-hubs-android-push-notification-google-fcm-get-started/activity-main-xml.png)

10. 다음으로 AndroidManifest.xml에서 정의된 수신기에 대한 하위 클래스를 추가합니다. `FirebaseService`라는 프로젝트에 또 다른 새 클래스를 추가합니다.

11. 그런 다음 `FirebaseService.java`의 맨 위에 다음 import 문을 추가합니다.

    ```java
    import com.google.firebase.messaging.FirebaseMessagingService;
    import com.google.firebase.messaging.RemoteMessage;
    import android.util.Log;
    import android.app.NotificationChannel;
    import android.app.NotificationManager;
    import android.app.PendingIntent;
    import android.content.Context;
    import android.content.Intent;
    import android.media.RingtoneManager;
    import android.net.Uri;
    import android.os.Build;
    import android.os.Bundle;
    import androidx.core.app.NotificationCompat;
    ```

12. `FirebaseService` 클래스에 다음 코드를 추가하여 `FirebaseMessagingService`의 하위 클래스로 만듭니다.

    이 코드는 `onMessageReceived` 메서드를 재정의하고 받은 알림을 보고합니다. 또한 `sendNotification()` 메서드를 사용하여 Android 알림 관리자에 푸시 알림을 보냅니다. `sendNotification()` 메서드는 앱이 실행되지 않을 때 알림이 수신되면 호출됩니다.

    ```java
    public class FirebaseService extends FirebaseMessagingService
    {
        private String TAG = "FirebaseService";
    
        public static final String NOTIFICATION_CHANNEL_ID = "nh-demo-channel-id";
        public static final String NOTIFICATION_CHANNEL_NAME = "Notification Hubs Demo Channel";
        public static final String NOTIFICATION_CHANNEL_DESCRIPTION = "Notification Hubs Demo Channel";
    
        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        static Context ctx;
    
        @Override
        public void onMessageReceived(RemoteMessage remoteMessage) {
            // ...
    
            // TODO(developer): Handle FCM messages here.
            // Not getting messages here? See why this may be: https://goo.gl/39bRNJ
            Log.d(TAG, "From: " + remoteMessage.getFrom());
    
            String nhMessage;
            // Check if message contains a notification payload.
            if (remoteMessage.getNotification() != null) {
                Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
    
                nhMessage = remoteMessage.getNotification().getBody();
            }
            else {
                nhMessage = remoteMessage.getData().values().iterator().next();
            }
    
            // Also if you intend on generating your own notifications as a result of a received FCM
            // message, here is where that should be initiated. See sendNotification method below.
            if (MainActivity.isVisible) {
                MainActivity.mainActivity.ToastNotify(nhMessage);
            }
            sendNotification(nhMessage);
        }
    
        private void sendNotification(String msg) {
    
            Intent intent = new Intent(ctx, MainActivity.class);
            intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
    
            mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);
    
            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                    intent, PendingIntent.FLAG_ONE_SHOT);
    
            Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
            NotificationCompat.Builder notificationBuilder = new NotificationCompat.Builder(
                    ctx,
                    NOTIFICATION_CHANNEL_ID)
                    .setContentText(msg)
                    .setPriority(NotificationCompat.PRIORITY_HIGH)
                    .setSmallIcon(android.R.drawable.ic_popup_reminder)
                    .setBadgeIconType(NotificationCompat.BADGE_ICON_SMALL);
    
            notificationBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, notificationBuilder.build());
        }
    
        public static void createChannelAndHandleNotifications(Context context) {
            ctx = context;
    
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
                NotificationChannel channel = new NotificationChannel(
                        NOTIFICATION_CHANNEL_ID,
                        NOTIFICATION_CHANNEL_NAME,
                        NotificationManager.IMPORTANCE_HIGH);
                channel.setDescription(NOTIFICATION_CHANNEL_DESCRIPTION);
                channel.setShowBadge(true);
    
                NotificationManager notificationManager = context.getSystemService(NotificationManager.class);
                notificationManager.createNotificationChannel(channel);
             }
        }
    }
    ```

13. Android Studio의 메뉴 모음에서 **빌드** > **프로젝트 다시 빌드**를 선택하여 코드에 오류가 없는지 확인합니다. `ic_launcher` 아이콘에 대한 오류가 발생하는 경우 AndroidManifest.xml 파일에서 다음 명령문을 제거합니다. 

    ```
        android:icon="@mipmap/ic_launcher"
    ```
14. 앱을 실행하기 위한 가상 디바이스가 있는지 확인합니다. 없으면 다음과 같이 추가합니다.
    1. ![디바이스 관리자 열기](./media/notification-hubs-android-push-notification-google-fcm-get-started/open-device-manager.png)
    2. ![가상 디바이스 만들기](./media/notification-hubs-android-push-notification-google-fcm-get-started/your-virtual-devices.PNG)

15. 선택한 디바이스에서 앱을 실행하고 허브를 사용하여 성공적으로 등록되는지 확인합니다.

    > [!NOTE]
    > 인스턴스 ID 서비스의 `onTokenRefresh()` 메서드가 호출될 때까지 초기 시작 시 등록이 실패할 수 있습니다. 새로 고침은 알림 허브를 사용하여 등록을 성공적으로 시작해야 합니다.

    ![디바이스 등록 성공](./media/notification-hubs-android-push-notification-google-fcm-get-started/device-registration.png)

## <a name="test-send-notification-from-the-notification-hub"></a>알림 허브에서 알림 테스트 보내기

다음 단계를 수행하여 [Azure Portal]에서 푸시 알림을 보낼 수 있습니다.

1. Azure Portal에서 허브에 대한 알림 허브 페이지의 **문제 해결** 섹션에서 **테스트 보내기**를 선택합니다.
3. **플랫폼**에 대해 **Android**를 선택합니다.
4. **보내기**를 선택합니다.  Android 디바이스에서 알림이 아직 보이지 않으면 모바일 앱을 실행하지 않았기 때문입니다. 모바일 앱을 실행한 후 **보내기** 단추를 다시 선택하여 알림 메시지를 표시합니다.
5. 맨 아래에 있는 목록에 작업의 결과가 표시됩니다.

    ![Azure Notification Hubs - 전송 테스트](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-hubs-test-send.png)
6. 디바이스에 알림 메시지가 표시됩니다. 

    ![디바이스의 알림 메시지](./media/notification-hubs-android-push-notification-google-fcm-get-started/notification-on-device.png)
    

[!INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

### <a name="run-the-mobile-app-on-emulator"></a>에뮬레이터에서 모바일 앱 실행
에뮬레이터 내부에서 푸시 알림을 테스트하기 전에 에뮬레이터 이미지가 앱에 대해 선택한 Google API 수준을 지원하는지 확인합니다. 이미지에서 네이티브 Google API를 지원하지 않으면 **SERVICE\_NOT\_AVAILABLE** 예외가 발생할 수 있습니다.

또한 **설정** > **계정**에서 실행 중인 에뮬레이터에 Google 계정을 추가했는지 확인합니다. 그렇지 않으면 FCM 등록 시 **AUTHENTICATION\_FAILED** 예외가 발생할 수 있습니다.

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Firebase Cloud Messaging을 사용하여 서비스에 등록된 모든 Android 디바이스로 알림을 브로드캐스트했습니다. 특정 디바이스로 알림을 푸시하는 방법을 알아보려면 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
>[자습서: 특정 Android 디바이스에 푸시 알림](push-notifications-android-specific-devices-firebase-cloud-messaging.md)

<!-- Images. -->

<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: https://go.microsoft.com/fwlink/?LinkId=389800
[Notification Hubs Guidance]: notification-hubs-push-notification-overview.md
[Azure Portal]: https://portal.azure.com
