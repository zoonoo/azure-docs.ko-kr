<properties 
	pageTitle="Azure 알림 허브 시작" 
	description="Azure 알림 허브를 사용하여 알림을 푸시하는 방법에 대해 알아봅니다." 
	services="notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>
<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="03/15/2014" 
	ms.author="ricksal"/>

# 알림 허브 시작

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

이 항목에서는 Azure 알림 허브를 사용하여 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 
이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

* [Google Cloud Messaging 사용](#register)
* [알림 허브 구성](#configure-hub)
* [알림 허브에 앱 연결](#connecting-app)
* [앱에 알림을 보내는 방법](#send)
* [앱 테스트](#run-app)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다. 

이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ Android SDK(Eclipse를 사용하는 것으로 가정) - <a href="http://go.microsoft.com/fwlink/?LinkId=389797">여기</a>에서 다운로드 가능
+ [모바일 서비스 Android SDK]

이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다. 

> [AZURE.NOTE] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-kr%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started%2F)을 참조하세요.

##<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../includes/mobile-services-enable-Google-cloud-messaging.md)]


##<a id="configure-hub"></a>알림 허브 구성

[AZURE.INCLUDE [notification-hubs-android-configure-push](../includes/notification-hubs-android-configure-push.md)]

##<a id="connecting-app"></a>알림 허브에 앱 연결

###새로운 Android 프로젝트 만들기

1. Eclipse ADT에서 새로운 Android 프로젝트를 만듭니다(File, New, Android Application).

   	![][13]

2. **Minimum Required SDK**가  *API 8: Android 2.2 (Froyo)*로 설정되어 있으며 다음 두 개의 SDK 항목이 사용 가능한 최신 버전으로 설정되어 있는지 확인합니다. 다음을 선택하고 마법사의 지침을 따릅니다. 빈 활동을 만들 수 있도록 **활동 만들기**가 선택되어 있는지 확인합니다. 다음 상자에 있는 기본 시작 관리자 아이콘을 그대로 적용하고 마지막 상자에서 **마침**을 클릭합니다.

   	![][14]

###프로젝트에 Google Play Services 추가

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###코드 추가

1. <a href="https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409">여기</a>서 알림 허브 Android SDK를 다운로드합니다. .zip 파일의 압축을 풀고 Package Explorer에서 notificationhubs\notification-hubs-0.1.jar 파일을 프로젝트의 \libs 디렉터리로 복사합니다.

2. [모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 프로젝트에 복사한 다음 *libs* 폴더를 새로 고칩니다.

    > [AZURE.NOTE] 후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

	GCM에서  *registrationId*를 가져오도록 응용 프로그램을 설정하고, 이를 사용해 앱 인스턴스를 알림 허브에 등록합니다.

3. AndroidManifest.xml 파일에서 <uses-sdk/> 요소 바로 아래에 다음 줄을 추가합니다. `<your package>`를 1단계에서 앱에 대해 선택한 패키지(이 경우 `com.yourCompany.wams_notificationhubs`)로 교체합니다.

        <uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.GET_ACCOUNTS"/>
		<uses-permission android:name="android.permission.WAKE_LOCK"/>
		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

		<permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
		<uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4. **MainActivity** 클래스에 다음 문을 추가합니다.

		import android.os.AsyncTask;	
		import com.google.android.gms.gcm.*;
		import com.microsoft.windowsazure.messaging.*;
		import com.microsoft.windowsazure.notifications.NotificationsManager;


5. 클래스의 맨 위에 다음과 같은 private 멤버를 추가합니다.

	> [AZURE.NOTE] SENDER_ID를 앞서 얻은 프로젝트 번호로 설정하세요.

		private String SENDER_ID = "<your project number>";
		private GoogleCloudMessaging gcm;
		private NotificationHub hub;

6. **OnCreate** 메서드에 다음 코드를 추가하고, 자리 표시자를 이전 섹션에서 얻은 수신 대기 권한이 포함된 연결 문자열 및 허브용 Azure의 페이지 맨 위에 나타나는 알림 허브의 이름(전체 URL **아님**)으로 교체합니다.

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

		gcm = GoogleCloudMessaging.getInstance(this);
        
		String connectionString = "<your listen access connection string>";
		hub = new NotificationHub("<your notification hub name>", connectionString, this);
		
		registerWithNotificationHubs();

7. MainActivity.java에서 다음 메서드를 만듭니다.

		@SuppressWarnings("unchecked")
		private void registerWithNotificationHubs() {
		   new AsyncTask() {
		      @Override
		      protected Object doInBackground(Object... params) {
		         try {
		            String regid = gcm.register(SENDER_ID);
		            hub.register(regid);
		         } catch (Exception e) {
		            return e;
		         }
		         return null;
		     }
		   }.execute(null, null, null);
		}

8. Android는 알림을 표시하지 않기 때문에 수신기를 직접 작성해야 합니다. **AndroidManifest.xml**에서 `<application/>` 요소 내에 다음 요소를 추가합니다.

	> [AZURE.NOTE] 자리 표시자를 패키지 이름으로 교체하세요.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>


9. 패키지 탐색기에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**, **클래스**를 차례로 클릭합니다.

10. **이름**에  `MyHandler`를 입력하고 **슈퍼클래스**에  `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 후 **마침**을 클릭합니다.

	![][6]

	새 MyHandler 클래스가 만들어집니다.

11. 다음 import 문을 추가합니다.

		import android.app.NotificationManager;
		import android.app.PendingIntent;
		import android.content.Context;
		import android.content.Intent;
		import android.os.Bundle;
		import android.support.v4.app.NotificationCompat;
		

12. 클래스에 다음 코드를 추가합니다.

		public static final int NOTIFICATION_ID = 1;
		private NotificationManager mNotificationManager;
		NotificationCompat.Builder builder;
		Context ctx;
	
		
		@Override
		public void onReceive(Context context, Bundle bundle) {
		    ctx = context;
		    String nhMessage = bundle.getString("msg");
	
		    sendNotification(nhMessage);
		}
	
		private void sendNotification(String msg) {
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
	

##<a name="send"></a>앱에 알림을 보내는 방법

>[AZURE.NOTE]<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST 인터페이스</a>를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예제는 [모바일 서비스에서 푸시 알림 시작](mobile-services-javascript-backend-android-get-started-push.md)을 참조하세요.  REST API를 사용하여 알림을 보내는 방법의 예제는 [Java에서 알림 허브를 사용하는 방법](notification-hubs-java-backend-how-to.md) 또는 [PHP에서 알림 허브를 사용하는 방법](notification-hubs-php-backend-how-to.md)을 참조하세요.

1. Visual Studio의 **파일** 메뉴에서 **새로 만들기**, **프로젝트...**를 차례로 선택하고 **Visual C#** 아래에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.  

   	![][20]

	그러면 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.

2. **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다. 

	그러면 패키지 관리자 콘솔이 표시됩니다.

3. 콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus
    
	그러면 <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet 패키지</a>가 포함된 Azure 서비스 버스 SDK에 대한 참조가 추가됩니다. 

4. Program.cs 파일을 열고 다음  `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

5. **Program** 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

   	"허브 이름" 자리 표시자는 포털의 **알림 허브** 탭에 표시되는 알림 허브의 이름으로 바꿉니다. 또한 연결 문자열 자리 표시자는 "알림 허브 구성" 섹션에서 확인한 **DefaultFullSharedAccessSignature** 연결 문자열로 바꿉니다. 

	>[AZURE.NOTE]**수신 대기** 권한이 아닌 **모든 권한**이 있는 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 권한이 없습니다.

5. 그런 다음 **Main** 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
		 Console.ReadLine();

##<a name="run-app"></a>앱 테스트

에뮬레이터에서 앱을 테스트하기 전에 다음과 같은 에뮬레이터 구성 단계를 완료해야 합니다(실제 장치에서 테스트하는 경우에는 건너뜀).

1. Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

2. **창**에서 **Android 가상 장치 관리자**를 클릭하고 장치를 선택한 후에 **편집**을 클릭합니다.

   	![][18]

3. **대상**에서 **Google API**를 선택하고 **확인**을 클릭합니다.

   	![][19]

4. 	푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다. 이렇게 하려면 에뮬레이터에서 <strong>설정</strong>으로 이동하여 <strong>계정 추가</strong>를 클릭합니다. 에뮬레이터가 인터넷에 연결되어 있어야 합니다.


다음 단계를 사용하여 장치 또는 에뮬레이터에서 앱을 실행합니다.

1. Eclipse 위쪽 도구 모음에서 **실행**을 클릭하고 테스트할 앱을 선택합니다. 
 
	그러면 에뮬레이터가 시작되고(에뮬레이터를 사용하는 경우) 앱이 로드되어 실행됩니다. 앱이 GCM에서  *registrationId*를 검색하고 알림 허브에 등록됩니다.

3. Visual Studio에서 F5 키를 눌러 콘솔 앱을 실행합니다. 

	앱에 알림이 전송됩니다.  
 
5. 알림 영역(왼쪽 위)에 아이콘이 나타나면 알림 서랍을 내려 알림을 확인합니다.  

   	![][21]

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 푸시 알림 보내기] 자습서를 참조하고, 사용자를 관심 그룹별로 분할하려면 [알림 허브를 통해 속보 보내기]를 참조하세요. 알림 허브 사용 방법에 대해 자세히 알아보려면 [알림 허브 지침]을 참조하세요.


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
[14]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[모바일 서비스 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[라이브러리 프로젝트 참조]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure 관리 포털]: https://manage.windowsazure.com/
[알림 허브 지침]: http://msdn.microsoft.com/library/jj927170.aspx

[알림 허브를 사용하여 사용자에게 푸시 알림 보내기]: notification-hubs-aspnet-backend-android-notify-users.md
[알림 허브를 사용하여 속보 보내기]: notification-hubs-aspnet-backend-android-breaking-news.md
[알림 허브를 통해 속보 보내기]: notification-hubs-aspnet-backend-android-breaking-news.md


<!--HONumber=49-->