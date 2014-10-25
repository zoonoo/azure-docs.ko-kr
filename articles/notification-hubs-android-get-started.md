<properties linkid="develop-notificationhubs-tutorials-get-started-android" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# 알림 허브 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows 범용">Windows 범용</a><a href="/ko-KR/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/ko-KR/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/ko-KR/documentation/articles/notification-hubs-android-get-started/" title="Android" class="current">Android</a><a href="/ko-KR/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/ko-KR/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

이 항목에서는 Azure 알림 허브를 사용하여 Android 응용 프로그램에 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 완료하면 알림 허브를 사용하여 앱을 실행하는 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 차례로 안내합니다.

-   [Google Cloud Messaging 사용][]
-   [알림 허브 구성][]
-   [알림 허브에 앱 연결][]
-   [앱에 알림을 보내는 방법][]
-   [앱 테스트][]

이 자습서에서는 알림 허브를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 알림 허브를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.

이 자습서를 사용하려면 다음이 필요합니다.

-   Android SDK(Eclipse를 사용하는 것으로 가정) - [여기][]에서 다운로드 가능
-   [모바일 서비스 Android SDK][]

이 자습서를 완료해야 다른 모든 Android 앱용 알림 허브 자습서를 진행할 수 있습니다.

<div class="dev-callout"><strong>참고</strong> <p>이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/ko-KR/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.</p></div>

## <span id="register"></span></a>Google Cloud Messaging 사용

[WACOM.INCLUDE [GCM 사용][]]

다음에는 이 API 키 값을 사용하여 알림 허브에서 GCM의 인증을 받고 응용 프로그램 대신 푸시 알림을 보낼 수 있게 합니다.

## <span id="configure-hub"></span></a>알림 허브 구성

1.  [Azure 관리 포털][]에 로그온하고 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2.  **앱 서비스**, **Service Bus**, **알림 허브**, **빠른 생성**을 차례로 클릭합니다.

    ![][]

3.  알림 허브의 이름을 입력하고 원하는 지역을 선택한 후 **새 알림 허브 만들기**를 클릭합니다.

    ![][1]

4.  방금 만든 네임스페이스(일반적으로 ***알림 허브 이름*-ns**)를 클릭한 후 맨 위에 있는 **구성**을 클릭합니다.

    ![][2]

5.  맨 위에 있는 **알림 허브** 탭을 클릭한 후 방금 만든 알림 허브를 클릭합니다.

    ![][3]

6.  맨 위에 있는 **구성** 탭을 클릭하고 이전 섹션에서 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

    ![][4]

7.  맨 위에 있는 **대시보드** 탭을 선택한 후 **연결 문자열 보기**를 클릭합니다. 두 연결 문자열을 기록해 둡니다.

이제 알림 허브가 GCM과 작동하도록 구성되었으며 앱을 등록하고 푸시 알림을 보내기 위한 연결 문자열이 있습니다.

## <span id="connecting-app"></span></a>알림 허브에 앱 연결

### 새로운 Android 프로젝트 만들기

1.  Eclipse ADT에서 새로운 Android 프로젝트를 만듭니다(File, New, Android Application).

    ![][5]

2.  **Minimum Required SDK**가 *API 8: Android 2.2 (Froyo)*로 설정되어 있으며 다음 두 개의 SDK 항목이 사용 가능한 최신 버전으로 설정되어 있는지 확인합니다. Next를 선택하고 마법사의 지침을 따르며, 빈 활동을 만들 수 있도록 **Create activity**를 선택했는지 확인합니다. 다음 상자에 있는 기본 시작 관리자 아이콘을 그대로 사용하고, 마지막 상자에서 **Finish**를 클릭합니다.

    ![][6]

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play Services 추가][]]

### 코드 추가

1.  알림 허브 Android SDK를 [여기][모바일 서비스 Android SDK]에서 다운로드합니다. .zip 파일의 압축을 풀고 Package Explorer에서 notificationhubs\\notification-hubs-0.1.jar 파일을 프로젝트의 \\libs 디렉터리로 복사합니다.

2.  [모바일 서비스 Android SDK][]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    <div class="dev-callout"><b>참고</b>
<p>후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.</p>
</div>

    GCM에서 *registrationId*를 가져오도록 응용 프로그램을 설정하고, 이를 사용해 앱 인스턴스를 알림 허브에 등록합니다.

3.  AndroidManifest.xml 파일에서 <uses-sdk></uses-sdk> 요소 바로 아래에 다음 줄을 추가합니다. `<your package>`를 1단계에서 앱에 대해 선택한 패키지(이 예의 경우 `com.yourCompany.wams_notificationhubs`)로 바꿔야 합니다.

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

    <div class="dev-callout"><b>참고</b>
<p>SENDER_ID를 앞서 얻은 프로젝트 번호로 설정하십시오.</p>
</div>

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

    <div class="dev-callout"><b>참고</b>
<p>자리 표시자를 패키지 이름으로 교체하십시오.</p>
</div>

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>

9.  Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**와 **Class**를 차례로 클릭합니다.

10. **Name**에서 `MyHandler`를 입력하고, **Superclass**에서 `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 다음 **Finish**를 클릭합니다.

    ![][7]

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

## <a name="send"></a>앱에 알림을 보내는 방법

[REST 인터페이스][]를 사용하는 모든 백 엔드에서 알림 허브를 사용하여 알림을 보낼 수 있습니다. 이 자습서에서는 .NET 콘솔 응용 프로그램을 사용하여 알림을 보냅니다. 알림 허브와 통합된 Azure 모바일 서비스 백 엔드에서 알림을 보내는 방법에 대한 예는 **모바일 서비스에서 푸시 알림 시작**([.NET 백 엔드][] | [JavaScript 백 엔드][.NET 백 엔드])을 참조하세요. REST API를 사용하여 알림을 보내는 방법에 대한 예는 **Java/PHP에서 알림 허브를 사용하는 방법**([Java][] | [PHP][])을 참조하세요.

1.  Visual Studio의 **파일** 메뉴에서 **새로 만들기**, **프로젝트...**를 차례로 선택하고 **Visual C#** 아래에서 **Windows**, **콘솔 응용 프로그램**, **확인**을 차례로 클릭합니다.

    ![][8]

    그러면 새 콘솔 응용 프로그램 프로젝트가 만들어집니다.

2.  **도구** 메뉴에서 **라이브러리 패키지 관리자**, **패키지 관리자 콘솔**을 차례로 클릭합니다.

    그러면 패키지 관리자 콘솔이 표시됩니다.

3.  콘솔 창에서 다음 명령을 실행합니다.

        Install-Package WindowsAzure.ServiceBus

    이 명령은 [WindowsAzure.ServiceBus NuGet 패키지][]를 사용하여 Azure 서비스 버스 SDK에 대한 참조를 추가합니다.

4.  Program.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.ServiceBus.Notifications;

5.  **Program** 클래스에 다음 메서드를 추가합니다.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"msg\":\"Hello from Azure!\"}}");
        }

    "hub name" 자리 표시자를 포털의 **알림 허브** 탭에 나타나는 알림 허브의 이름으로 바꿔야 합니다. 또한 연결 문자열 자리 표시자를 "알림 허브 구성" 섹션에서 얻은 **DefaultFullSharedAccessSignature**라는 연결 문자열로 바꿉니다.

    > [WACOM.NOTE]**수신 대기** 권한이 아니라 **모든** 권한을 가진 연결 문자열을 사용해야 합니다. 수신 대기 권한 문자열은 알림을 보낼 권한이 없습니다.

6.  그런 다음 **Main** 메서드에 다음 줄을 추가합니다.

         SendNotificationAsync();
         Console.ReadLine();

## <a name="run-app"></a>앱 테스트

에뮬레이터에서 앱을 테스트하기 전에 다음과 같은 에뮬레이터 구성 단계를 완료해야 합니다(실제 장치에서 테스트하는 경우에는 건너뜀).

1.  Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

2.  **Window**에서 **Android Virtual Device Manager**를 클릭하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![][9]

3.  **Target**에서 **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![][10]

4.  푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다(에뮬레이터에서 **Settings**로 이동하고 **Add Account** 클릭). 에뮬레이터가 인터넷에 연결되어 있어야 합니다.

다음 단계를 사용하여 장치 또는 에뮬레이터에서 앱을 실행합니다.

1.  Eclipse 상단 도구 모음에서 **Run**을 클릭하고 테스트할 앱을 선택합니다.

    그러면 에뮬레이터가 시작되고(에뮬레이터를 사용하는 경우) 앱이 로드되어 실행됩니다. 앱이 GCM에서 *registrationId*를 검색하고 알림 허브에 등록됩니다.

2.  Visual Studio에서 F5 키를 눌러 콘솔 앱을 실행합니다.

    앱에 알림이 전송됩니다.

3.  알림 영역(왼쪽 위)에 아이콘이 나타나면 알림 서랍을 내려 알림을 확인합니다.

    ![][11]

## <a name="next-steps"> </a>다음 단계

이 간단한 예제에서는 모든 Android 장치로 알림을 브로드캐스트합니다. 특정 사용자에게만 알림을 보내려면 [알림 허브를 사용하여 사용자에게 알림 푸시][] 자습서를 참조하십시오. 사용자를 관심 그룹별로 분할하려면 [알림 허브를 사용하여 뉴스 속보 보내기][]를 참조하십시오. [알림 허브 지침][]에서 알림 허브 사용 방법을 자세히 알아보십시오.



  [Windows 범용]: /ko-KR/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows 범용"
  [Windows Phone]: /ko-KR/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /ko-KR/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /ko-KR/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /ko-KR/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /ko-KR/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /ko-KR/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [Google Cloud Messaging 사용]: #register
  [알림 허브 구성]: #configure-hub
  [알림 허브에 앱 연결]: #connecting-app
  [앱에 알림을 보내는 방법]: #send
  [앱 테스트]: #run-app
  [여기]: http://go.microsoft.com/fwlink/?LinkId=389797
  [모바일 서비스 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
  [Azure 무료 평가판]: http://www.windowsazure.com/ko-KR/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [GCM 사용]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [Azure 관리 포털]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal.png
  [1]: ./media/notification-hubs-android-get-started/notification-hub-create-from-portal2.png
  [2]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal.png
  [3]: ./media/notification-hubs-android-get-started/notification-hub-select-from-portal2.png
  [4]: ./media/notification-hubs-android-get-started/notification-hub-configure-android.png
  [5]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app.png
  [6]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app2.png
  [Play Services 추가]: ../includes/mobile-services-add-Google-play-services.md
  [7]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png
  [REST 인터페이스]: http://msdn.microsoft.com/ko-KR/library/windowsazure/dn223264.aspx
  [.NET 백 엔드]: /ko-KR/documentation/articles/mobile-services-javascript-backend-android-get-started-push/
  [Java]: /ko-KR/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /ko-KR/documentation/articles/notification-hubs-php-backend-how-to/
  [8]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet 패키지]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [9]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app7.png
  [10]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app8.png
  [11]: ./media/notification-hubs-android-get-started/notification-hub-android-toast.png
  [알림 허브를 사용하여 사용자에게 알림 푸시]: /ko-KR/manage/services/notification-hubs/notify-users-aspnet
  [알림 허브를 사용하여 뉴스 속보 보내기]: /ko-KR/manage/services/notification-hubs/breaking-news-dotnet
  [알림 허브 지침]: http://msdn.microsoft.com/ko-KR/library/jj927170.aspx
