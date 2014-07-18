모바일 서비스에서 푸시 알림 시작
================================

[Windows 스토어 C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows 스토어 C#")[Windows 스토어 JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows 스토어 JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

이 토픽은 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging) 서비스를 사용하여 빠른 시작 프로젝트에 푸시 알림을 추가합니다. 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 보냅니다.

> [WACOM.NOTE] 이 토픽은 모바일 서비스에서 제공하는 레거시 지원을 통해 푸시 알림을 사용하도록 설정하는 방법을 보여 줍니다. Azure 알림 허브가 모바일 서비스와 통합되어 템플릿 기반 및 플랫폼 간 푸시 알림을 수백만 개의 장치로 보낼 수 있게 해 줍니다. 기본적으로 알림 허브를 사용한 푸시 알림은 사용되지 않으며, Android용 알림 허브 지원은 현재 모바일 서비스 라이브러리에 없습니다. 그러나 알림 허브 라이브러리를 사용하여 모바일 서비스에서 푸시 알림을 보낼 수 있습니다. 자세한 내용은 [알림 허브 시작](/en-us/documentation/articles/notification-hubs-android-get-started/)을 참조하십시오.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

-   [Google Cloud Messaging 사용](#register)
-   [모바일 서비스 구성](#configure)
-   [앱에 푸시 알림 추가](#add-push)
-   [푸시 알림을 보내도록 스크립트 업데이트](#update-scripts)
-   [알림을 받기 위해 데이터 삽입](#test)

이 자습서에는 다음이 필요합니다.

-   [모바일 서비스 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)
-   활성 Google 계정

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작](/en-us/develop/mobile/tutorials/get-started-android)을 완료해야 합니다.

Google Cloud Messaging 사용
---------------------------

[WACOM.INCLUDE [GCM 사용](../includes/mobile-services-enable-Google-cloud-messaging.md)]

다음에는 이 API 키를 사용하여 모바일 서비스에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

푸시 요청을 보내도록 모바일 서비스 구성
---------------------------------------

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

        ![][18]

2.  **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

        ![][19]

이제 모바일 서비스가 GCM과 함께 작동하여 푸시 알림을 보내도록 구성되었습니다.

앱에 푸시 알림 추가
-------------------

### 프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play Services 추가](../includes/mobile-services-add-Google-play-services.md)]

### 코드 추가

1.  프로젝트 파일 **AndroidManifest.xml**을 엽니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다. 이전 장치를 사용 중이어서 16 미만 값으로 설정해야 하는 경우 이 값을 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](http://go.microsoft.com/fwlink/?LinkId=389801)(영문)을 참조하십시오. 속성을 적절하게 설정합니다.

2.  uses-sdk 요소에서 **targetSdkVersion**이 설치된 SDK 플랫폼 번호(1단계)로 설정되었는지 확인합니다. 사용 가능한 최신 버전으로 설정하는 것이 좋습니다.

3.  이전 단계에서 선택한 항목에 따라 **uses-sdk** 태그가 다음과 같이 표시될 수 있습니다.

         <uses data-morhtml="true"-sdk
             android:minSdkVersion="17"
             android:targetSdkVersion="19" />

4.  다음 두 단계의 코드에서 *\*\*my\_app\_package\*\**를 프로젝트용 앱 패키지의 이름으로 바꿉니다. 이 이름은 manifest 태그의 package 특성 값입니다.

5.  기존 uses-permission 요소 뒤에 다음과 같은 새 사용 권한을 추가합니다.

         <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
             android:protectionLevel="signature" />
         <uses data-morhtml="true"-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
         <uses data-morhtml="true"-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
         <uses data-morhtml="true"-permission android:name="android.permission.GET_ACCOUNTS" />
         <uses data-morhtml="true"-permission android:name="android.permission.WAKE_LOCK" />

6.  application 여는 태그 뒤에 다음 코드를 추가합니다.

         <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
             android:permission="com.google.android.c2dm.permission.SEND">
             <intent data-morhtml="true"-filter>
                 <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                 <category android:name="**my_app_package**" />
             </intent-filter>
         </receiver>

7.  ToDoItem.java 파일을 열고 **TodoItem** 클래스에 다음 코드를 추가합니다.

             @com.google.gson.annotations.SerializedName("handle")
             private String mHandle;
            
             public String getHandle() {
                 return mHandle;
             }
            
             public final void setHandle(String handle) {
                 mHandle = handle;
             }

    이 코드는 등록 ID를 포함하는 새 속성을 만듭니다.

    **참고**

    모바일 서비스에서 동적 스키마가 사용하도록 설정된 경우 이 속성이 포함된 새 항목이 삽입되면 **handle** 열이 **TodoItem** 테이블에 자동으로 추가됩니다.

8.  [모바일 서비스 Android SDK](https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409)를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    **참고**

    후속 SDK 릴리스에서는 파일 이름 끝에 있는 숫자가 변경될 수도 있습니다.

9.  *ToDoItemActivity.java* 파일을 열고 다음 import 문을 추가합니다.

        import com.microsoft.windowsazure.notifications.NotificationsManager;

10. 클래스에 다음 private 변수를 추가합니다. 여기서 *&lt;PROJECT data-morhtml="true"\_NUMBER\>*는 이전 절차에서 Google이 앱에 할당한 프로젝트 번호입니다.

        public static final String SENDER_ID = "<PROJECT data-morhtml="true"_NUMBER>";

11. **onCreate** 메서드에서 MobileServiceClient가 인스턴스화되기 전에 장치에 알림 처리기를 등록하는 다음 코드를 추가합니다.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

12. 항목이 테이블에 삽입되기 전에 **addItem** 메서드에 다음 코드 줄을 추가합니다.

        item.setHandle(MyHandler.getHandle());

    이 코드는 항목의 handle 속성을 장치의 등록 ID로 설정합니다.

13. Package Explorer에서 src 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **New**를 클릭한 후 **Class**를 클릭합니다.

14. **Name**에 MyHandler를 입력하고 **Superclass**에 com.microsoft.windowsazure.notifications.NotificationsHandler를 입력한 후 **Finish**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png)

    새 MyHandler 클래스가 만들어집니다.

15. 다음 import 문을 추가합니다.

        import android.content.Context;
            
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

16. 다음 코드를 추가합니다.

        @com.google.gson.annotations.SerializedName("handle")
        private static String mHandle;

        public static String getHandle() {
            return mHandle;
        }

        public static final void setHandle(String handle) {
            mHandle = handle;
        }

17. 기존 **onRegistered** 메서드 재정의를 다음 코드로 바꿉니다.

        @Override
        public void onRegistered(Context context, String gcmRegistrationId) {
            super.onRegistered(context, gcmRegistrationId);
                
            setHandle(gcmRegistrationId);
        }

이제 앱이 푸시 알림을 지원하도록 업데이트되었습니다.

관리 포털에서 등록된 삽입 스크립트 업데이트
-------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

        ![][21]

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png) **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  insert 함수를 다음 코드로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Write to the response and then send the notification in the background
                     request.respond();
                     push.gcm.send(item.handle, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error) {
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

        This registers a new insert script, which uses the [gcm object] to send a push notification (the inserted text) to the device provided in the insert request. 

앱에서 푸시 알림 테스트
-----------------------

**참고**

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

1.  Eclipse를 다시 시작하고 Package Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **Properties**, **Android**를 차례로 클릭합니다. **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

프로젝트 대상이 Google API로 설정됩니다.

1.  **Window**에서 **Android Virtual Device Manager**를 선택하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2.  **Target**에서 **Google APIs**를 선택하고 OK를 클릭합니다.

        ![][25]

    AVD 대상이 Google API를 사용하도록 설정됩니다.

3.  **Run** 메뉴에서 **Run**을 클릭하여 앱을 시작합니다.

4.  앱에서 *A new Mobile Services task* 등의 의미 있는 텍스트를 입력하고 **Add** 단추를 클릭합니다.

![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

1.  화면 아래쪽에 검은색 알림 상자가 잠시 나타납니다.

![](./media/mobile-services-android-get-started-push/mobile-push-icon.png)

이 자습서를 완료했습니다.

다음 단계
---------

이 간단한 예제에서는 사용자가 방금 삽입한 데이터가 포함된 푸시 알림을 받습니다. 클라이언트가 요청을 통해 GCM에서 사용하는 장치 토큰을 모바일 서비스에 제공합니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림](/en-us/develop/mobile/tutorials/push-notifications-to-users-android)에서는 장치 토큰을 저장할 별도의 Devices 테이블을 만들고 삽입 시 저장된 모든 채널에 푸시 알림을 보냅니다.

