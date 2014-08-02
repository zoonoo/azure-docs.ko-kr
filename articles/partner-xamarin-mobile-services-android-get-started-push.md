<properties linkid="develop-mobile-tutorials-get-started-with-push-xamarin-android" urlDisplayName="Get Started with Push Notifications" pageTitle="Get started with push notifications - Mobile Services" metaKeywords="" description="Learn how to use push notifications in Xamarin.Android apps with Azure Mobile Services." metaCanonical="" disqusComments="0" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services" documentationCenter="Mobile" authors="" />

모바일 서비스에서 푸시 알림 시작하기
====================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/get-started-with-push-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/get-started-with-push-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/get-started-with-push-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/get-started-with-push-ios "iOS")[Android](/en-us/develop/mobile/tutorials/get-started-with-push-android "Android")[Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android "Xamarin.Android")

이 항목에서는 Azure 모바일 서비스를 사용하여 Xamarin.Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging) 서비스를 사용하여 빠른 시작 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

1.  [푸시 알림에 대해 앱 등록](#register)
2.  [모바일 서비스 구성](#configure)
3.  [앱에 푸시 알림 추가](#add-push)
4.  [푸시 알림을 전송하도록 스크립트 업데이트](#update-scripts)
5.  [알림을 받기 위한 데이터 삽입](#test)

이 자습서를 사용하려면 다음이 필요합니다.

-   활성 Google 계정

이 자습서는 모바일 서비스 quickstart를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기](/en-us/develop/mobile/tutorials/get-started-xamarin-android)를 완료해야 합니다.

앱 등록푸시 알림을 위한 앱 등록
-------------------------------

**참고**

이 항목의 절차를 완료하려면 검증된 전자 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302)으로 이동하십시오.

1.  [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 웹 사이트로 이동하고 Google 계정 자격 증명을 사용하여 로그인한 후 **Create project...**를 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png)

    > [WACOM.NOTE] 
    > 이미 기존 프로젝트가 있는 경우에는 로그인하면 **Dashboard** 페이지로 연결됩니다. Dashboard에서 새 프로젝트를 만들려면 **API Project&lt;**를 확장하고 **Other projects**에서 **Create...**를 클릭한 후 프로젝트 이름을 입력하고 **Create project**를 클릭합니다.

2.  왼쪽 열에서 Overview 단추를 클릭하고 Dashboard 섹션에 있는 Project Number를 기록해 둡니다.

    이 자습서 뒷부분에서 이 값을 클라이언트의 **PROJECT\_ID** 변수로 설정합니다.

3.  [Google apis](http://go.microsoft.com/fwlink/p/?LinkId=268303) 페이지에서 **Services**를 클릭한 후 토글을 클릭하여 **Google Cloud Messaging for Android**를 설정하고 서비스 약관에 동의합니다.

4.  **API Access**를 클릭한 후 **Create new Server key...**를 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png)

5.  **Configure Server Key for API Project**에서 **Create**를 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png)

6.  **API key** 값을 기록해 둡니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png)

	다음에는 이 API 키를 사용하여 모바일 서비스에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

<h2>서비스 구성푸시 요청을 보내도록 모바일 서비스 구성</h2>

1.  [Azure 관리 포털](https://manage.windowsazure.com/)에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png)

2.  **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png)

    이제 모바일 서비스가 GCM과 함께 작동하여 푸시 알림을 보내도록 구성되었습니다.

<h2>푸시 알림 추가앱에 푸시 알림 추가</h2>

1.  먼저 프로젝트에서 참조로 **PushSharp**를 추가합니다. 이렇게 하려면 최신 버전의 PushSharp를 컴파일하고 컴파일된 DLL을 Xamarin.Android 프로젝트에 대한 참조로 추가해야 합니다.

2.  [PushSharp Github 페이지](https://github.com/Redth/PushSharp)(영문)를 방문하여 최신 릴리스를 다운로드합니다. 파일 컬렉션의 압축을 풀고 나면 다음 샘플 프로젝트 폴더로 이동합니다.

    **/Client.Samples/PushSharp.ClientSample.MonoForAndroid/PushSharp.ClientSample.MonoForAndroid.Gcm/**

    .. 다음 프로젝트 파일을 엽니다.

    ** PushSharp.ClientSample.MonoForAndroid.Gcm.csproj **

3.  MonoForAndroid PushSharp 클라이언트 샘플을 **Release** 모드에서 빌드합니다.

4.  Xamarin.Android 프로젝트 폴더에 **\_external** 폴더를 만듭니다.

5.  MonoForAndroid PushSharp 클라이언트 샘플에서 다음 파일을 복사하여 Xamarin.Android 프로젝트 폴더에서 새로 만든 **\_external** 폴더에 넣습니다.

    **\\bin\\Release\\PushSharp.Client.MonoForAndroid.dll**

6.  Xamarin Studio(또는 Visual Studio)에서 Xamarin.Android 프로젝트를 엽니다.

7.  프로젝트 **References** 폴더를 마우스 오른쪽 단추로 클릭하고 **Edit References...**를 선택합니다.

8.  **.Net Assembly** 탭으로 이동하여 프로젝트의 **\_external** 폴더를 찾은 후 앞에서 빌드한 **PushSharp.Client.MonoForAndroid.dll**을 선택하고 **Add**를 클릭합니다. OK를 클릭하여 대화 상자를 닫습니다.

9.  **Constants.cs**를 열고 다음 줄을 추가하여 **PROJECT\_ID**를 앞에서 보았던 Google Project\_ID로 바꿉니다.

         public const string SenderID = "PROJECT_ID"; // Google API Project Number

10. MonoForAndroid PushSharp 클라이언트 샘플에서 Xamarin.Android 프로젝트 폴더로 **PushService.cs** 파일을 복사하여 프로젝트에 추가합니다.

11. **PushService.cs**에서 사용된 네임스페이스를 프로젝트의 네임스페이스와 일치하도록 변경합니다(예: XamarinTodoQuickStart).

12. **PushService.cs**의 **SENDER\_IDS** 배열이 위에서 만든 **SenderID** 상수를 참조하도록 변경합니다.

        public static string[] SENDER_IDS = new string[] { Constants.SenderID };

13. 장치 등록 ID를 추적하도록 **PushService.cs**의 **PushHandlerService**에 새 정적 속성을 추가합니다.

        public static string RegistrationID { get; private set; }

14. 수신한 등록 ID를 로컬 정적 변수에 저장하도록 **PushService.cs**의 **OnRegistered** 메서드를 업데이트합니다.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;
        }

15. 알림의 일부로 수신된 푸시 메시지를 표시하도록 **PushService.cs**의 **OnMessage** 메서드를 업데이트합니다(기존 **createNotification** 호출을 바꿈).

        string message = intent.Extras.GetString("message");
        createNotification("New todo item!", "Todo item: " + message);

16. **OnMessage** 메서드에 마지막으로 수신된 푸시 메시지를 저장하는 다음 코드가 기본적으로 있습니다.

        //Store the message
        var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
        var edit = prefs.Edit();
        edit.PutString("last_msg", msg.ToString());
        edit.Commit();

17. **DefaultActivity** 대신 **TodoActivity**를 참조하도록 **PushService.cs**의 **createNotification** 메서드를 업데이트합니다.

18. **TodoActivity.cs**를 열고 다음 using 문을 추가합니다.

        using PushSharp.Client;

19. **TodoActivity.cs**에서 **MobileServiceClient**가 만들어진 위치 바로 위에 다음 코드 줄을 삽입합니다.

        // Check to ensure everything's setup right
        PushClient.CheckDevice(this);
        PushClient.CheckManifest(this);

        // Register for push notifications
        System.Diagnostics.Debug.WriteLine("Registering...");
        PushClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);

20. **TodoItem.cs**를 열고 TodoItem을 추가한 사용자의 등록된 장치 ID를 추적하는 새 필드를 추가합니다.

        [DataMember(Name = "channel")]
        public string RegistrationId { get; set; }

21. **TodoActivity.cs**에서 **AddItem** 메서드를 업데이트하여 새로 추가된 **TodoItem**의 **RegistrationID**를 등록 도중에 수신한 장치 등록 ID로 설정합니다.

        // Create a new item
        var item = new TodoItem() {
            Text = textNewTodo.Text,
            Complete = false,
            RegistrationId = PushHandlerService.RegistrationID
        };

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.

삽입 스크립트 업데이트관리 포털에서 등록된 삽입 스크립트 업데이트
-----------------------------------------------------------------

1.  관리 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2.  **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

	![](./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png)

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

1.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // Write to the response and then send the notification in the background
                     request.respond();
                     push.gcm.send(item.channel, item.text, {
                         success: function(response) {
                             console.log('Push notification sent: ', response);
                         }, error: function(error) {
                             console.log('Error sending push notification: ', error);
                         }
                     });
                 }
             });
         }

    새 삽입 스크립트가 등록되며, 이 스크립트는 [gcm 개체](http://go.microsoft.com/fwlink/p/?LinkId=282645)(영문)를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

앱 테스트앱에서 푸시 알림 테스트
--------------------------------

1.  앱을 실행하고 새 Todo 항목을 추가합니다. 추가하는 새 Todo 항목에 대한 푸시 알림이 수신되는지 확인합니다.

2.  Azure 관리 포털에서 모바일 앱의 **로그** 탭을 검토하여 위에서 **TodoItem** 테이블에 대한 **Insert** 메서드에 추가한 로깅 메시지를 확인합니다.

3.  Azure 관리 포털에서 **TodoItem** 테이블을 검토하여 새로 추가된 **channel** 열을 확인하고 이 열에 고유한 장치 등록 ID가 포함되어 있는지 확인합니다.

이 자습서를 성공적으로 완료했습니다.

완성된 예 가져오기
------------------

[완성된 예제 프로젝트](http://go.microsoft.com/fwlink/p/?LinkId=331303)를 다운로드합니다. 고유한 Azure 설정으로 **ApplicationURL**, **ApplicationKey** 및 **SenderID** 변수를 업데이트해야 합니다.

다음 단계
---------

이 간단한 예제에서는 사용자가 방금 삽입한 데이터로 푸시 알림을 받습니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림](/en-us/develop/mobile/tutorials/push-notifications-to-users-android)에서는, 장치 토큰을 저장하고 삽입이 발생할 경우 저장된 모든 채널로 푸시 알림을 보내는 별도의 Devices 테이블을 만듭니다.


<!-- Anchors. -->
[Register your app for push notifications]: #register
[Configure Mobile Services]: #configure
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->
[1]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[18]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-android-get-started-push/mobile-insert-script-push2.png







<!-- URLs. TODO:: update 'Download the Android app project' download link, 'GitHub', completed project, etc. -->
[Google apis]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started-xamarin-android
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-android
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services Xamarin conceptual]: /en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library
[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[PushSharp Github page]: https://github.com/Redth/PushSharp

