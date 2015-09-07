<properties
	pageTitle="Azure 알림 허브 시작 | Microsoft Azure"
	description="이 자습서에서 Azure 알림 허브를 사용하여 Kindle 응용 프로그램에 푸시 알림을 보내는 방법을 알아봅니다."
	services="notification-hubs"
	documentationCenter=""
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-kindle"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="06/16/2015"
	ms.author="wesmc"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##개요

이 자습서에서는 Azure 알림 허브를 사용하여 Kindle 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. ADM(Amazon 장치 메시징)을 사용하여 푸시 알림을 받는 빈 Kindle 앱을 만듭니다.

##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

+ <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android 사이트</a>에서 Android SDK(Eclipse를 사용한다고 가정)를 가져옵니다.
+ <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">개발 환경 설정</a>의 단계에 따라 Kindle에 대한 개발 환경을 설정합니다.

##개발자 포털에 새 앱 추가

1. 먼저 [Amazon 개발자 포털]에서 앱을 만듭니다.

	![][0]

2. **응용 프로그램 키**를 복사합니다.

	![][1]

3. 포털에서 앱의 이름을 클릭한 다음 **Device Messaging** 탭을 클릭합니다.

	![][2]

4. **Create a New Security Profile**를 클릭한 다음 새 보안 프로필(예: **TestAdm 보안 프로필**)을 만듭니다. 그런 다음 **Save**를 클릭합니다.

	![][3]

5. **Security Profiles**를 클릭하여 방금 만든 보안 프로필을 확인합니다. 나중에 사용할 수 있도록 **클라이언트 ID** 및 **클라이언트 암호** 값을 복사해 둡니다.

	![][4]

## API 키 만들기

1. 관리자 권한으로 명령 프롬프트를 엽니다.
2. Android SDK 폴더로 이동합니다.
3. 다음 명령을 입력합니다.

    	keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

	![][5]

4.  **keystore** 암호에 **android**를 입력합니다.

5.  **MD5** 지문을 복사합니다.
6.  개발자 포털로 돌아가서 **메시징** 탭에서 **Android/Kindle**을 클릭하고 앱에 대한 패키지 이름(예: **com.sample.notificationhubtest**)과 **MD5** 값을 입력한 다음 **API 키 생성**을 클릭합니다.

## 허브에 자격 증명 추가

포털에서 알림 허브의 **구성** 탭에 클라이언트 암호와 클라이언트 ID를 추가합니다.

## 응용 프로그램 설정

> [AZURE.NOTE]응용 프로그램을 만들 때 API Level 17 이상을 사용합니다.

Eclipse 프로젝트에 ADM 라이브러리를 추가합니다.

1. ADM 라이브러리를 가져오려면 [SDK를 다운로드합니다]. SDK zip 파일의 압축을 풉니다.
2. Eclipse에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **Properties**를 클릭합니다. 왼쪽에서 **Java Build Path**를 선택한 다음 맨 위에 있는 **Libraries** 탭을 선택합니다. **Add External Jar**을 클릭하고, Amazon SDK의 압축을 푼 디렉터리에서 `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` 파일을 선택합니다.
3. NotificationHubs Android SDK(링크)를 다운로드합니다.
4. 패키지의 압축을 푼 후 `notification-hubs-sdk.jar` 파일을 Eclipse의 `libs` 폴더로 끌어옵니다.

ADM을 지원하도록 앱 매니페스트를 편집합니다.

1. 루트 매니페스트 요소에 Amazon 네임스페이스를 추가합니다.


		xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. 사용 권한을 매니페스트 요소 아래에 첫 번째 요소로 추가합니다. **[YOUR PACKAGE NAME]**을 앱을 만드는 데 사용한 패키지로 바꿉니다.

		<permission
	     android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
	     android:protectionLevel="signature" />

		<uses-permission android:name="android.permission.INTERNET"/>

		<uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

		<!-- This permission allows your app access to receive push notifications
		from ADM. -->
		<uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

		<!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
		<uses-permission android:name="android.permission.WAKE_LOCK" />

3. 응용 프로그램 요소의 첫 번째 자식으로 다음 요소를 삽입합니다. **[YOUR SERVICE NAME]**을 다음 섹션에서 만드는 ADM 메시지 처리기의 이름(패키지 포함)으로 바꾸고, **[YOUR PACKAGE NAME]**을 앱을 만들 때 사용한 패키지 이름으로 바꿉니다.

		<amazon:enable-feature
		      android:name="com.amazon.device.messaging"
		             android:required="true"/>
		<service
		    android:name="[YOUR SERVICE NAME]"
		    android:exported="false" />

		<receiver
		    android:name="[YOUR SERVICE NAME]$Receiver"

		    <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
		    android:permission="com.amazon.device.messaging.permission.SEND" >

		    <!-- To interact with ADM, your app must listen for the following intents. -->
		    <intent-filter>
		  <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
		  <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

		  <!-- Replace the name in the category tag with your app's package name. -->
		  <category android:name="[YOUR PACKAGE NAME]" />
		    </intent-filter>
		</receiver>

## ADM 메시지 처리기 만들기

1. 다음 그림에 표시된 것처럼 `com.amazon.device.messaging.ADMMessageHandlerBase`에서 상속되는 새 클래스를 만들고 이름을 `MyADMMessageHandler`로 지정합니다.

	![][6]

2. 다음 `import` 문을 추가합니다.

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.support.v4.app.NotificationCompat;
		import com.amazon.device.messaging.ADMMessageReceiver;
		import com.microsoft.windowsazure.messaging.NotificationHub

3. 만든 클래스에 다음 코드를 추가합니다. 허브 이름 및 연결 문자열(수신)을 바꿉니다.

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
      	private static NotificationHub hub;
		public static NotificationHub getNotificationHub(Context context) {
			Log.v("com.wa.hellokindlefire", "getNotificationHub");
			if (hub == null) {
				hub = new NotificationHub("[hub name]", "[listen connection string]", context);
			}
			return hub;
		}

		public MyADMMessageHandler() {
				super("MyADMMessageHandler");
			}

			public static class Receiver extends ADMMessageReceiver
    		{
        		public Receiver()
        		{
            		super(MyADMMessageHandler.class);
        		}
    		}

			private void sendNotification(String msg) {
				Context ctx = getApplicationContext();

	   		 mNotificationManager = (NotificationManager)
	    			ctx.getSystemService(Context.NOTIFICATION_SERVICE);

	    	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
	          	new Intent(ctx, MainActivity.class), 0);

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

4. `OnMessage()` 메서드에 다음 코드를 추가합니다.

		String nhMessage = intent.getExtras().getString("msg");
		sendNotification(nhMessage);

5. `OnRegistered` 메서드에 다음 코드를 추가합니다.

			try {
		getNotificationHub(getApplicationContext()).register(registrationId);
			} catch (Exception e) {
		Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
			}

6.	`OnUnregistered` 메서드에 다음 코드를 추가합니다.

			try {
				getNotificationHub(getApplicationContext()).unregister();
			} catch (Exception e) {
				Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
			}

7. `MainActivity` 메서드에 다음 import 문을 추가합니다.

		import com.amazon.device.messaging.ADM;

8. `OnCreate` 메서드의 끝에 다음 코드를 추가합니다.

		final ADM adm = new ADM(this);
		if (adm.getRegistrationId() == null)
		{
		   adm.startRegister();
		} else {
			new AsyncTask() {
			      @Override
			      protected Object doInBackground(Object... params) {
			         try {			        	 MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
			         } catch (Exception e) {
			        	 Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
			        	 return e;
			         }
			         return null;
			     }
			   }.execute(null, null, null);
		}

## 앱에 API 키 추가

1. Eclipse에서 프로젝트의 디렉터리 자산에 **api\_key.txt**라는 새 파일을 만듭니다.
2. 파일을 열고 Amazon 개발자 포털에서 생성한 API 키를 복사합니다.

## 앱 실행

1. 에뮬레이터를 시작합니다.
2. 에뮬레이터에서 위에서 아래로 살짝 밀고 **설정**, **내 계정**을 차례로 클릭한 다음 유효한 Amazon 계정을 등록합니다.
3. Eclipse에서 앱을 실행합니다.

> [AZURE.NOTE]문제가 발생하면 에뮬레이터 또는 장치의 시간을 확인합니다. 시간 값이 정확해야 합니다. Kindle 에뮬레이터의 시간을 변경하려면 Android SDK 플랫폼 도구 디렉터리에서 다음 명령을 실행할 수 있습니다.

		adb shell  date -s "yyyymmdd.hhmmss"

## 메시지 보내기

.NET을 사용하여 메시지를 보내려면

		static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{"data":{"msg" : "Hello from .NET!"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Amazon 개발자 포털]: https://developer.amazon.com/home.html
[SDK를 다운로드합니다]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png

<!---HONumber=August15_HO9-->