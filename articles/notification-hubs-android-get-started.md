<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

알림 허브 시작
==============

[Windows 스토어 C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows 스토어 C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

이 항목에서는 Azure 알림 허브를 사용하여 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 안내합니다.

-   [Google Cloud Messaging 사용](#register)
-   [알림 허브 구성](#configure-hub)
-   [알림 허브에 앱 연결](#connecting-app)
-   [앱에 알림을 보내는 방법](#send)
-   [앱 테스트](#run-app)

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.

이 자습서를 사용하려면 다음이 필요합니다.

-   Android SDK(Eclipse를 사용하는 것으로 가정) - [여기](http://go.microsoft.com/fwlink/?LinkId=389797)에서 다운로드 가능
-   [모바일 서비스 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)

이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다.

**참고**

이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 평가판](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F)을 참조하십시오.

Google Cloud Messaging 사용
---------------------------

[WACOM.INCLUDE [GCM 사용](../includes/mobile-services-enable-Google-cloud-messaging.md)]

다음에는 이 API 키 값을 사용하여 알림 허브에서 GCM의 인증을 받고 응용 프로그램 대신 푸시 알림을 보낼 수 있게 합니다.

알림 허브 구성
--------------

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

7.  맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 문자열 보기**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

이제 알림 허브가 GCM과 작동하도록 구성되었으며 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.

알림 허브에 앱 연결
-------------------

### 새로운 Android 프로젝트 만들기

1.  Eclipse ADT에서 새로운 Android 프로젝트를 만듭니다(File, New, Android Application).

    ![][13]

2.  **Minimum Required SDK**가 *API 8: Android 2.2 (Froyo)*로 설정되어 있으며 다음 두 개의 SDK 항목이 사용 가능한 최신 버전으로 설정되어 있는지 확인합니다. Next를 선택하고 마법사의 지침을 따르며, 빈 활동을 만들 수 있도록 **Create activity**를 선택했는지 확인합니다. 다음 상자에 있는 기본 시작 관리자 아이콘을 그대로 사용하고, 마지막 상자에서 **Finish**를 클릭합니다.

    ![][14]

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play Services 추가](../includes/mobile-services-add-Google-play-services.md)]

### 코드 추가

1.  알림 허브 Android SDK를 [여기](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)에서 다운로드합니다. .zip 파일의 압축을 풀고 Package Explorer에서 notificationhubs\\notification-hubs-0.1.jar 파일을 프로젝트의 \\libs 디렉터리로 복사합니다.

2.  [모바일 서비스 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    **참고**

    후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

    GCM에서 *registrationId*를 가져오도록 응용 프로그램을 설정하고, 이를 사용해 앱 인스턴스를 알림 허브에 등록합니다.

3.  AndroidManifest.xml 파일에서 &lt;uses-sdk\> 요소 바로 아래에 다음 줄을 추가합니다. `<your package>`를 1단계에서 앱에 대해 선택한 패키지(이 경우 `com.yourCompany.wams_notificationhubs`)로 교체합니다.

         <uses-permission android:name="android.permission.INTERNET"/>
         <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
         <uses-permission android:name="android.permission.WAKE_LOCK"/>
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

         <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
         <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>

4.  **MainActivity** 클래스에 다음 문을 추가합니다.

         import android.os.AsyncTask;    
         import com.google.android.gms.gcm.*;
         import com.microsoft.windowsazure.messaging.*;
         import com.microsoft.windowsazure.notifications.NotificationsManager;

5.  클래스의 맨 위에 다음과 같은 private 멤버를 추가합니다.

    **참고**

    SENDER\_ID를 앞서 얻은 프로젝트 번호로 설정하십시오.

         private String SENDER_ID = "<your project number>";
         private GoogleCloudMessaging gcm;
         private NotificationHub hub;

6.  **OnCreate** 메서드에 다음 코드를 추가하고, 자리 표시자를 이전 섹션에서 얻은 수신 대기 권한이 포함된 연결 문자열 및 허브용 Azure의 페이지 맨 위에 나타나는 알림 허브의 이름(전체 URL **아님**)으로 교체합니다.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

         gcm = GoogleCloudMessaging.getInstance(this);
            
         String connectionString = "<your listen access connection string>";
         hub = new NotificationHub("<your notification hub name>", connectionString, this);
            
         registerWithNotificationHubs();

7.  MainActivity.java에서 다음 메서드를 만듭니다.

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

8.  Android는 알림을 표시하지 않기 때문에 수신기를 직접 작성해야 합니다. **AndroidManifest.xml**에서 `<application/>` 요소 내에 다음 요소를 추가합니다.

    **참고**

    자리 표시자를 패키지 이름으로 교체하십시오.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>


9.  Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **Class**를 클릭합니다.

10. **Name**에 `MyHandler`를 입력하고 **Superclass**에 `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 후 **Finish**를 클릭합니다.

    ![](./media/notification-hubs-android-get-started/notification-hub-android-new-class.png)

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

앱에 알림을 보내는 방법
-----------------------

[REST 인터페이스](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx)를 사용하여 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 알림을 보내는 두 가지 방법, 즉 .NET 콘솔 앱 및 노드 스크립트를 사용하는 모바일 서비스를 통해 알림을 보냅니다.

### .NET 콘솔 앱을 사용하여 알림을 보내려면

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

### 모바일 서비스를 사용하여 알림을 보내려면

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하고 모바일 서비스를 선택합니다. 아직 모바일 서비스가 없다면 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started/#create-new-service)을 수행하십시오.

2.  맨 위에 있는 **스케줄러** 탭을 선택합니다.

    ![][22]

3.  새 예약된 작업을 만들고 이름을 삽입한 후 **요청 시**를 선택합니다.

    ![][23]

4.  작업이 만들어졌으면 작업 이름을 클릭합니다. 그런 다음 위쪽 막대에서 **스크립트** 탭을 클릭합니다.

5.  스케줄러 함수 내에 다음 스크립트를 삽입합니다. 자리 표시자를 알림 허브 이름과 앞에서 얻은 *DefaultFullSharedAccessSignature*의 연결 문자열로 바꿔야 합니다. **저장**을 클릭합니다.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
         notificationHubService.gcm.send(null,'{"data":{"msg" : "Hello from Mobile Services!"}}',
			function(error)
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

앱 테스트
---------

실제 휴대폰에서 이 앱을 테스트하려면 USB 케이블로 컴퓨터에 연결하면 됩니다.

에뮬레이터로 이 앱을 테스트하려면:

1.  Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

2.  **Window**에서 **Android Virtual Device Manager**를 클릭하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![][18]

3.  **Target**에서 **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![][19]

4.  푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다(에뮬레이터에서 **Settings**로 이동하고 **Add Account** 클릭). 에뮬레이터가 인터넷에 연결되어 있어야 합니다.



어떤 장치를 선택했든, 이제 다음을 수행합니다.

1.  Eclipse 상단 도구 모음에서 **Run**을 클릭하고 테스트할 앱을 선택합니다. 앱이 연결된 휴대폰으로 로드되거나, 에뮬레이터가 시작된 후 앱이 로드되고 실행됩니다.

2.  앱이 GCM에서 *registrationId*를 검색하고 알림 허브에 등록됩니다.

3.  이제 이전 섹션의 방법 중 하나를 사용해 앱에 알림을 보냅니다.

    -   .Net 콘솔 응용 프로그램을 사용 중인 경우 Visual Studio에서 F5 키를 눌러 앱을 실행합니다. 그러면 알림이 전송됩니다.
    -   모바일 서비스 스크립트를 사용 중인 경우 모바일 서비스 화면의 아래쪽 막대에서 **Run Once**를 클릭합니다. 그러면 스크립트에서 알림이 전송됩니다.

4.  알림 영역(왼쪽 위)에 아이콘이 나타납니다. 알림 서랍을 내리면 알림을 볼 수 있습니다.

    ![][21]

다음 단계
---------

이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시](/en-us/manage/services/notification-hubs/notify-users-aspnet) 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet)를 참조하십시오. 알림 허브 사용 방법에 대해 자세히 알아보려면 [알림 허브 지침](http://msdn.microsoft.com/en-us/library/jj927170.aspx) 및 [Android용 알림 허브 방법](http://msdn.microsoft.com/en-us/library/dn282661.aspx)을 참조하십시오.


<!-- Images. -->
[1]: ./media/notification-hubs-android-get-started/mobile-services-google-new-project.png
[2]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png
[3]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png
[4]: ./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png
[5]: ./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
[7]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
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
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/#create-new-service
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-android
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/en-us/library/jj927170.aspx
[Notification Hubs How-To for Android]: http://msdn.microsoft.com/en-us/library/dn282661.aspx

[Use Notification Hubs to push notifications to users]: /en-us/manage/services/notification-hubs/notify-users-aspnet
[Use Notification Hubs to send breaking news]: /en-us/manage/services/notification-hubs/breaking-news-dotnet

