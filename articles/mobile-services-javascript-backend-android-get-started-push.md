<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Windows Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-push-dotnet/" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" writer="ricksal" manager="" editor="" />

모바일 서비스에서 푸시 알림 시작
================================

[Windows 스토어 C\#](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows 스토어 C#") [Windows 스토어 JavaScript](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows 스토어 JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone") [iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS") [Android](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging)을 사용하여 퀵 스타트 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

[WACOM.NOTE]이 자습서는 현재 Preview에 있는 알림 허브와 모바일 서비스를 통합하는 방법을 보여 줍니다. 기본적으로 JavaScript 백 엔드에서는 알림 허브를 사용하여 푸시 알림을 보낼 수 없습니다. 새 알림 허브가 생성된 이후에는 통합 프로세스를 되돌릴 수 없습니다. 오늘만 [이 항목 버전](/en-us/documentation/articles/mobile-services-android-get-started-push/)에 설명된 기본 푸시 지원을 사용하여 iOS에 대한 푸시 알림을 사용할 수 있습니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [Google Cloud Messaging 사용](#register)
2.  [모바일 서비스 구성](#configure)
3.  [앱에 푸시 알림 추가](#add-push)
4.  [푸시 알림을 전송하도록 스크립트 업데이트](#update-scripts)
5.  [알림을 받기 위한 데이터 삽입](#test)

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/en-us/develop/mobile/tutorials/get-started/) 또는 [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-js/)를 완료하여 프로젝트를 모바일 서비스에 연결해야 합니다.

Google Cloud Messaging 사용
---------------------------

[WACOM.INCLUDE [GCM 사용](../includes/mobile-services-enable-Google-cloud-messaging.md)]

다음에는 이 API 키를 사용하여 모바일 서비스에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

푸시 요청을 보내도록 모바일 서비스 구성
---------------------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2.  **푸시** 탭, **향상된 푸시 설정**, **예**를 차례로 클릭하여 구성 변경을 적용합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-enable-enhanced-push.png)

    ![](../includes/media/mobile-services-javascript-backend-register-windows-store-app/mobile-enable-enhanced-push.png)

    알림 허브에서 제공되는 향상된 푸시 알림 기능을 사용하도록 모바일 서비스의 구성이 업데이트됩니다. 유료 모바일 서비스에서 일부 알림 허브 사용량이 무료로 제공됩니다. 자세한 내용은 [모바일 서비스 가격 정보](http://go.microsoft.com/fwlink/p/?LinkID=311786)를 참조하십시오.

    **중요**

    이 작업은 푸시 자격 증명을 다시 설정하고 스크립트에서 푸시 메서드의 동작을 변경합니다. 이러한 변경은 되돌릴 수 없습니다. 이 방법을 사용하여 프로덕션 모바일 서비스에 알림 허브를 추가하지 마십시오. 프로덕션 모바일 서비스에서 향상된 푸시 알림을 설정하는 방법에 대한 자세한 내용은 [이 지침](http://go.microsoft.com/fwlink/p/?LinkId=391951)(영문)을 참조하십시오.

3.  이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    **중요**

    포털의 푸시 탭에서 향상된 푸시 알림에 대한 GCM 자격 증명을 설정할 경우 앱에서 알림 허브를 구성하도록 GCM 자격 증명이 알림 허브와 공유됩니다.

이제 모바일 서비스와 앱이 GCM 및 알림 허브를 사용하도록 둘 다 구성되었습니다.

앱에 푸시 알림 추가
-------------------

### Android SDK 버전 확인

[WACOM.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

다음 단계에서는 Google Play Services를 설치합니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다.

이전 장치로 테스트할 경우 이 값을 적절하게 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](http://go.microsoft.com/fwlink/?LinkId=389801)(영문)을 참조하십시오.

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play Services 추가](../includes/mobile-services-add-Google-play-services.md)]

### 코드 추가

1.  `AndroidManifest.xml` 파일을 엽니다. 다음 두 단계의 코드에서 _`**my_app_package**`_를 프로젝트용 앱 패키지의 이름으로 바꿉니다. 이 이름은 `manifest` 태그의 `package` 특성 값입니다.

2.  기존 `uses-permission` 요소 뒤에 다음과 같은 새 사용 권한을 추가합니다.

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses-permission android:name="android.permission.WAKE_LOCK" />

3.  `application` 여는 태그 뒤에 다음 코드를 추가합니다.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
                                        android:permission="com.google.android.c2dm.permission.SEND">
             <intent-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

4.  [모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    **참고**

    후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

5.  *ToDoItemActivity.java* 파일을 열고 다음 import 문을 추가합니다.

        import com.microsoft.windowsazure.notifications.NotificationsManager;

6.  클래스에 다음 private 변수를 추가합니다. 여기서 *`<PROJECT_NUMBER>`*는 이전 절차에서 Google이 앱에 할당한 프로젝트 번호입니다.

        public static final String SENDER_ID = "<PROJECT_NUMBER>";

7.  **onCreate** 메서드에서 MobileServiceClient가 인스턴스화되기 전에 장치에 알림 처리기를 등록하는 다음 코드를 추가합니다.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

    나중에 이 코드에서 참조되는 MyHandler.class를 정의합니다.

8.  Package Explorer에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **Class**를 클릭합니다.

9.  **Name**에 `MyHandler`를 입력하고 **Superclass**에 `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 후 **Finish**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    새 MyHandler 클래스가 만들어집니다.

10. 다음 import 문을 추가합니다.

        import android.content.Context;

11. 이제 이 코드를 클래스에 추가합니다.

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        Context ctx;

12. 다음 코드를 추가하여 **onRegistered** 메서드를 재정의합니다. 이 코드는 모바일 알림 허브에 장치를 등록합니다.

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

13. 다음 코드를 추가하여 **onReceive** 메서드를 재정의합니다. 그러면 알림이 수신되면 표시됩니다.

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

관리 포털에서 등록된 삽입 스크립트 업데이트
-------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) 

	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
         // Define a payload for the Google Cloud Messaging toast notification.
         var payload = 
             '{"data":{"message" : "Hello from Mobile Services!"}}';
            
         request.execute({
             success: function() {
                 // If the insert succeeds, send a notification.
                 push.gcm.send(null, payload, {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse, payload);
                         request.respond();
                         },              
                     error: function(pushResponse) {
                         console.log("Error Sending push:", pushResponse);
                         request.respond(500, { error: pushResponse });
                         }
                     });
                 },
             error: function(err) {
                 console.log("request.execute error", err)
                 request.respond();
             }
           });
         }

        이 코드는 새 삽입 스크립트를 등록합니다. 그러면 삽입이 성공한 이후에 [gcm object]를 사용하여 모든 등록된 장치에 푸시 알림을 보냅니다. 

앱에서 푸시 알림 테스트
-----------------------

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

### 테스트를 위해 에뮬레이터 설정

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1.  Eclipse를 다시 시작하고 Package Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **Properties**, **Android**를 차례로 클릭합니다. **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

	프로젝트 대상이 Google API로 설정됩니다.

1.  **Window**에서 **Android Virtual Device Manager**를 선택하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  **Target**에서 **Google APIs**를 선택하고 OK를 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

    AVD 대상이 Google API를 사용하도록 설정됩니다.

### 테스트 실행

1.  Eclipse의 **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

2.  앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.

이 자습서를 성공적으로 완료했습니다.

다음 단계
---------

이 자습서는 모바일 서비스에서 제공하는 기본 푸시 알림 기능을 보여 줍니다. 플랫폼 간 알림 보내기, 구독 기반 라우팅, 대량 볼륨 등의 고급 기능이 앱에 필요한 경우 모바일 서비스와 함께 Azure 알림 허브를 사용하는 것이 좋습니다. 자세한 내용은 다음과 같은 알림 허브 토픽 중 하나를 참조하십시오.

-   [알림 허브 시작하기](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Android 앱에서 알림 허브를 활용하는 방법에 대해 알아보십시오.

-   [구독자에게 알림 보내기](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    관심이 있는 범주에 대해 푸시 알림을 등록하고 받아보는 방법에 대해 알아보십시오.

-   [사용자에게 알림 보내기](/en-us/manage/services/notification-hubs/notify-users/)
    모바일 서비스에서 장치와 상관없이 특정 사용자에게 푸시 알림을 보내는 방법에 대해 알아보십시오.

-   [사용자에게 플랫폼 간 알림 보내기](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    백 엔드에 플랫폼 전용 페이로드를 마련할 필요 없이 템플릿을 사용하여 모바일 서비스에서 푸시 알림을 보내는 방법에 대해 알아보십시오.

다음의 모바일 서비스 항목에 대해서도 자세히 알아보십시오.

-   [데이터 시작하기](/en-us/develop/mobile/tutorials/get-started-with-data-js/)
    모바일 서비스를 사용하여 데이터를 저장 및 쿼리하는 방법에 대해 자세히 알아보십시오.

-   [인증 시작하기](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    Windows 계정으로 앱의 사용자를 인증하는 방법에 대해 알아보십시오.

-   [모바일 서비스 서버 스크립트 참조](http://go.microsoft.com/fwlink/?LinkId=262293)
    서버 스크립트의 등록 및 사용에 대해 자세히 알아보십시오.

-   [모바일 서비스 HTML/JavaScript 방법 개념 참조](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    HTML 및 JavaScript와 함께 모바일 서비스를 사용하는 방법을 자세히 알아봅니다.


