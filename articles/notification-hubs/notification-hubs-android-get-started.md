<properties
	pageTitle="Android 앱에 대한 Azure 알림 허브 시작 | Microsoft Azure"
	description="이 자습서에서 Azure 알림 허브를 사용하여 Android 장치로 푸시 알림을 보내는 방법을 알아봅니다."
	services="notification-hubs"
	documentationCenter="android"
	authors="wesmc7777"
	manager="dwrede"
	editor=""/>
<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="hero-article"
	ms.date="11/25/2015"
	ms.author="wesmc"/>

# Android 앱에 대한 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##개요

이 자습서에서는 Azure 알림 허브를 사용하여 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.


## 시작하기 전에

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

이 자습서에 대해 완료된 코드는 GitHub의 [여기](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted)서 찾을 수 있습니다.


##필수 조건

이 자습서를 사용하려면 다음이 필요합니다.

+ Android Studio - <a href="http://go.microsoft.com/fwlink/?LinkId=389797">사이트에서 다운로드</a>에서 다운로드 가능
+ 활성 Azure 계정. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F)을 참조하세요.


이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다.


##Google Cloud Messaging을 지원하는 프로젝트 만들기

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##새 알림 허브 구성


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;7. 맨 위에 있는 **구성** 탭을 클릭하고 이전 섹션에서 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)

이제 알림 허브가 GCM과 작동하도록 구성되었으며 알림을 받고 푸시 알림을 보내도록 앱을 등록하기 위한 연결 문자열이 있습니다.

##<a id="connecting-app"></a>알림 허브에 앱 연결

###새 Android 프로젝트 만들기

1. Android Studio에서 새 Android Studio 프로젝트를 시작합니다.

   	![][13]

2. **휴대폰 및 태블릿** 폼 팩터와 지원할 **최소 SDK**를 선택합니다. 그런 후 **Next**를 클릭합니다.

   	![][14]

3. **빈 활동**를 기본 활동으로 선택하고 **다음**, **마침**를 차례로 클릭합니다.

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Play 서비스 추가](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###코드 추가

1. [Bintray의 Notification-Hubs-Android-SDK](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4)의 **파일** 탭에서 notification-hubs-0.4.jar 파일을 다운로드합니다. 또한 [notifications-1.0.1.jar](https://bintray.com/microsoftazuremobile/SDK/Notifications-Handler/view)를 프로젝트의 **app\\libs** 디렉터리에 다운로드합니다. Android Studio의 Project View 창에서 **libs** 폴더로 파일을 직접 끌어올 수 있습니다. **libs** 폴더를 새로 고칩니다.


    >[AZURE.NOTE]후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

2. GCM에서 등록 ID를 가져오도록 응용 프로그램을 설정하고, 이를 사용해 앱 인스턴스를 알림 허브에 등록합니다.

	AndroidManifest.xml 파일에서 `</application>` 태그 아래에 다음 사용 권한을 추가합니다. `<your package>`를 AndroidManifest.xml 파일의 위쪽에 표시된 패키지 이름(이 예제의 경우 `com.example.testnotificationhubs`)으로 바꿔야 합니다.

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

3. **MainActivity** 클래스에서 다음 `import` 문을 클래스 선언 위에 추가합니다.

		import android.app.AlertDialog;
		import android.content.DialogInterface;
		import android.os.AsyncTask;
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


4. 클래스의 맨 위에 다음과 같은 private 멤버를 추가합니다.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;
    	private String HubName = "<Enter Your Hub Name>";
		private String HubListenConnectionString = "<Your default listen connection string>";
	    private static Boolean isVisible = false;


	세 개의 자리 표시자를 업데이트해야 합니다. * **SENDER\_ID**: `SENDER_ID`를 이전에 [Google 클라우드 콘솔](http://cloud.google.com/console)에서 만든 프로젝트에서 얻은 프로젝트 번호로 설정합니다. * **HubListenConnectionString**: `HubListenConnectionString`을 허브의 **DefaultListenAccessSignature** 연결 문자열로 설정합니다. [Azure 클래식 포털]에서 허브의 **대시보드** 탭에 있는 **연결 문자열 보기**를 클릭하여 이 연결 문자열을 복사할 수 있습니다. * **HubName**: Azure의 허브 페이지 위쪽에 표시된 알림 허브의 이름(전체 URL이 **아님**)입니다. 예를 들면 `"myhub"`를 사용합니다.



5. **MainActivity** 클래스의 **OnCreate** 메서드에 다음 코드를 추가하여 활동을 만들 때 알림 허브로 등록합니다.

        MyHandler.mainActivity = this;
        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        gcm = GoogleCloudMessaging.getInstance(this);
        hub = new NotificationHub(HubName, HubListenConnectionString, this);
        registerWithNotificationHubs();

6. **MainActivity.java**에서 **registerWithNotificationHubs()** 메서드에 대해 아래 코드를 추가합니다. 이 메서드는 Google 클라우드 메시징 및 알림 허브에 등록한 후 성공을 보고합니다.

    	@SuppressWarnings("unchecked")
    	private void registerWithNotificationHubs() {
        	new AsyncTask() {
            	@Override
            	protected Object doInBackground(Object... params) {
                	try {
                    	String regid = gcm.register(SENDER_ID);
                    DialogNotify("Registered Successfully","RegId : " +
						hub.register(regid).getRegistrationId());
                	} catch (Exception e) {
                    	DialogNotify("Exception",e.getMessage());
                    	return e;
                	}
                	return null;
            	}
        	}.execute(null, null, null);
    	}


7. 앱이 실행 중이며 표시되는 경우 알림을 표시하려면 `DialogNotify` 메서드를 활동에 추가합니다. 또한 대화 상자를 표시하기 위해 활동이 표시되는지 확인하려면 `onStart`, `onPause`, `onResume` 및 `onStop`을 재정의합니다.

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

		/**
		  * A modal AlertDialog for displaying a message on the UI thread
		  * when there's an exception or message to report.
		  *
		  * @param title   Title for the AlertDialog box.
		  * @param message The message displayed for the AlertDialog box.
		  */
    	public void DialogNotify(final String title,final String message)
    	{
	        if (isVisible == false)
	            return;

        	final AlertDialog.Builder dlg;
        	dlg = new AlertDialog.Builder(this);

        	runOnUiThread(new Runnable() {
            	@Override
            	public void run() {
                	AlertDialog dlgAlert = dlg.create();
                	dlgAlert.setTitle(title);
                	dlgAlert.setButton(DialogInterface.BUTTON_POSITIVE,
						(CharSequence) "OK",
						new DialogInterface.OnClickListener() {
                            public void onClick(DialogInterface dialog, int which) {
                                dialog.dismiss();
                            }
                        });
                	dlgAlert.setMessage(message);
                	dlgAlert.setCancelable(false);
                	dlgAlert.show();
            	}
        	});
    	}

8. Android는 알림을 표시하지 않기 때문에 수신기를 직접 작성해야 합니다. **AndroidManifest.xml**에서 `<application>` 요소 내에 다음 요소를 추가합니다.

	> [AZURE.NOTE]자리 표시자를 패키지 이름으로 교체하세요.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>


9. 프로젝트 뷰에서 **앱** > **원본** > **기본** > **java**를 확장합니다. **java** 아래의 패키지 폴더를 마우스 오른쪽 단추로 클릭하고 **New**, **Java Class**를 차례로 클릭합니다.

	![][6]

10. 새 클래스의 **이름** 필드에 **MyHandler**를 입력하고 **확인**을 클릭합니다.


11. 다음 가져오기 문을 **MyHandler.java**의 맨 위에 추가합니다.

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		import com.microsoft.windowsazure.notifications.NotificationsHandler;


12. 다음과 같이 클래스 선언을 업데이트하여 `MyHandler`를 `com.microsoft.windowsazure.notifications.NotificationsHandler`의 하위 클래스로 설정합니다.

		public class MyHandler extends NotificationsHandler {


13. `MyHandler` 클래스에 대해 다음 코드를 추가합니다.

	이 코드는 처리기가 받은 알림을 보여 주는 팝업 `AlertDialog`를 표시할 수 있도록 `OnReceive` 메서드를 재정의합니다. 또한 처리기는 `sendNotification()` 메서드를 사용하여 Android Notification Manager에 알림을 보냅니다.

    	public static final int NOTIFICATION_ID = 1;
    	private NotificationManager mNotificationManager;
    	NotificationCompat.Builder builder;
    	Context ctx;

    	static public MainActivity mainActivity;

    	@Override
    	public void onReceive(Context context, Bundle bundle) {
        	ctx = context;
        	String nhMessage = bundle.getString("message");

        	sendNotification(nhMessage);
        	mainActivity.DialogNotify("Received Notification",nhMessage);
    	}

    	private void sendNotification(String msg) {
        	mNotificationManager = (NotificationManager)
                ctx.getSystemService(Context.NOTIFICATION_SERVICE);

        	PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

			NotificationCompat.Builder mBuilder =
				new NotificationCompat.Builder(ctx)
					.setSmallIcon(R.mipmap.ic_launcher)
					.setContentTitle("Notification Hub Demo")
					.setStyle(new NotificationCompat.BigTextStyle()
					.bigText(msg))
					.setContentText(msg);

			mBuilder.setContentIntent(contentIntent);
			mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
		}

14. Android Studio의 메뉴 모음에서 **빌드** > **프로젝트 다시 빌드**를 클릭하여 발견된 오류가 없는지 확인합니다.

##알림 보내기



아래 화면과 같이 알림 허브의 디버그 탭을 통해 [Azure 클래식 포털]에서 알림을 보내서 앱의 알림 수신을 테스트할 수 있습니다.

![][30]

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]


## (선택 사항) 앱에서 알림 보내기


1. Android Studio 프로젝트 뷰에서 **앱** > **원본** > **기본** > **자원** > **레이아웃**을 확장합니다. **activity\_main.xml** 레이아웃 파일을 열고 **Text** 탭을 클릭하여 파일의 텍스트 내용을 업데이트합니다. 아래 코드로 업데이트하여 알림 허브에 알림 메시지를 보내는 새 `Button` 및 `EditText` 컨트롤을 추가합니다. 이 코드를 맨 아래의 `</RelativeLayout>` 바로 앞에 추가합니다.

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

2. Android Studio 프로젝트 뷰에서 **앱** > **원본** > **기본** > **자원** > **값**을 확장합니다. **strings.xml** 파일을 열고 `Button` 및 `EditText` 컨트롤에서 참조하는 문자열 값을 추가합니다. 파일 맨 아래의 `</resources>` 바로 앞에 이를 추가합니다.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. **MainActivity.java** 파일에서 다음 `import` 문을 `MainActivity` 클래스 위에 추가합니다.

		import java.net.URLEncoder;
		import javax.crypto.Mac;
		import javax.crypto.spec.SecretKeySpec;

		import android.util.Base64;
		import android.view.View;
		import android.widget.EditText;

		import org.apache.http.HttpResponse;
		import org.apache.http.client.HttpClient;
		import org.apache.http.client.methods.HttpPost;
		import org.apache.http.entity.StringEntity;
		import org.apache.http.impl.client.DefaultHttpClient;


3. **MainActivity.java** 파일에서 다음 멤버를 `MainActivity` 클래스 위에 추가합니다.

	허브에 **DefaultFullSharedAccessSignature** 연결 문자열을 사용하여 `HubFullAccess`을 업데이트합니다. 알림 허브에 대한 **대시보드** 탭에서 **연결 문자열 보기**를 클릭하여 [Azure 클래식 포털]에서 이 연결 문자열을 복사할 수 있습니다.

	    private String HubEndpoint = null;
	    private String HubSasKeyName = null;
	    private String HubSasKeyValue = null;
		private String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. 활동에는 허브 이름 및 허브에 대한 전체 공유 액세스 연결 문자열이 유지됩니다. 알림 허브로 메시지를 보낼 POST 요청을 인증하기 위해 SaS(Software Access Signature) 토큰을 만들어야 합니다. 연결 문자열에서 키 데이터를 구문 분석한 다음 [일반적인 개념](http://msdn.microsoft.com/library/azure/dn495627.aspx) REST API 참조에 설명된 대로 SaS 토큰을 만들면 됩니다.

	**MainActivity.java**에서 `MainActivity` 클래스에 다음 메서드를 추가하여 연결 문자열의 구문을 분석합니다.

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

5. **MainActivity.java**에서 `MainActivity` 클래스에 다음 메서드를 추가하여 SaS 인증 토큰을 만듭니다.

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
                String token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
                return token;
            } catch (Exception e) {
                DialogNotify("Exception Generating SaS",e.getMessage().toString());
            }

            return null;
        }


6. **MainActivity.java**에서 `MainActivity` 클래스에 다음 메서드를 추가하여 **알림 보내기** 단추 클릭을 처리하고 REST API를 사용하여 허브에 알림 메시지를 보냅니다.

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
                        HttpClient client = new DefaultHttpClient();

                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(HubFullAccess);
                        String url = HubEndpoint + HubName + "/messages/?api-version=2015-01";
                        HttpPost post = new HttpPost(url);

                        // Authenticate the POST request with the SaS token
                        post.setHeader("Authorization", generateSasToken(url));

                        // JSON content for GCM
                        post.setHeader("Content-Type", "application/json;charset=utf-8");

                        // Notification format should be GCM
                        post.setHeader("ServiceBusNotification-Format", "gcm");
                        post.setEntity(new StringEntity(json));

                        HttpResponse response = client.execute(post);
                    }
                    catch(Exception e)
                    {
                        DialogNotify("Exception",e.getMessage().toString());
                    }
                }
            }.start();
        }



##앱 테스트

####에뮬레이터 테스트

에뮬레이터에서 테스트하려는 경우 에뮬레이터 이미지가 앱에 대해 선택한 Google API 수준을 지원하는지 확인합니다. 이미지가 Google API를 지원하지 않으면 **SERVICE\_NOT\_AVAILABLE** 예외가 발생합니다.

또한 **Settings** > **Accounts**에서 실행 중인 에뮬레이터에 Google 계정을 추가해야 합니다. 그렇지 않으면 GCM 등록 시 **AUTHENTICATION\_FAILED** 예외가 발생할 수 있습니다.

####앱 테스트

1. 앱을 실행하고 등록에 성공한 경우 등록 ID가 보고되는지 확인합니다.

   	![][18]

2. 허브에 등록된 모든 Android 장치로 보낼 알림 메시지를 입력합니다.

   	![][19]

3. **알림 보내기**를 누릅니다. 앱을 실행 중인 장치에 알림 메시지가 포함된 `AlertDialog`가 표시됩니다. 앱을 실행 중이지 않지만 이전에 알림에 등록한 장치에는 알림 관리자에 추가된 알림이 수신됩니다. 왼쪽 위 모서리에서 아래로 쓸어 넘겨 알림을 볼 수 있습니다.

   	![][21]

##다음 단계

이 간단한 예제에서는 포털 또는 콘솔 앱을 사용하여 모든 Windows 장치로 브로드캐스트 알림을 보냈습니다. 다음 단계로 [알림 허브를 사용하여 사용자에게 알림 푸시] 자습서를 수행하는 것이 좋습니다. 특정 사용자를 대상으로 하는 태그를 사용하여 ASP.NET 백엔드에서 알림을 보내는 방법을 보여 줍니다.

사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](영문)를 참조하십시오.

알림 허브에 대한 더 일반적인 정보를 알아보려면 [알림 허브 지침]을 참조하세요.




<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure 클래식 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx
[알림 허브를 사용하여 사용자에게 알림 푸시]: notification-hubs-aspnet-backend-android-notify-users.md
[알림 허브를 사용하여 뉴스 속보 보내기]: notification-hubs-aspnet-backend-android-breaking-news.md

<!---HONumber=AcomDC_1210_2015-->