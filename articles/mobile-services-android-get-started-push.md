<properties linkid="develop-mobile-tutorials-get-started-with-push-android" urlDisplayName="Get Started with Push (Android)" pageTitle="푸시 알림 시작(Android) | 모바일 개발자 센터" metaKeywords="" description="Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법에 대해 알아봅니다." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# 모바일 서비스에서 푸시 알림 시작(기존 푸시)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-dotnet" title="Windows Store C#">Windows 스토어 C#</a>
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-js" title="Windows Store JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a>
    <a href="/ko-kr/develop/mobile/tutorials/get-started-with-push-android" title="Android" class="current">Android</a>
<!--    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
	<a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/ko-kr/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend">.NET 백 엔드</a> | <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-push/"  title="JavaScript backend" class="current">JavaScript 백 엔드</a></div>

이 항목에서는 Azure 모바일 서비스를 사용하여 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 GCM(Google Cloud Messaging) 서비스를 사용하여 빠른 시작 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 보냅니다.

>[WACOM.NOTE]이 항목에서는 아직 알림 허브 통합을 사용하도록 <em>업그레이드되지 않은</em> <em>기존</em> 모바일 서비스를 지원합니다. <em>새</em> 모바일 서비스를 만드는 경우 이 통합 기능이 자동으로 사용하도록 설정됩니다. 새 모바일 서비스의 경우 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)을 참조하세요.
>
>모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. <em>가능한 경우 알림 허브를 사용하도록 기존 모바일 서비스를 업그레이드해야 합니다</em>. 업그레이드한 후에는 [푸시 알림 시작](/ko-kr/documentation/articles/mobile-services-javascript-backend-android-get-started-push/)을 참조하세요.

이 자습서에서는 푸시 알림을 사용하도록 설정하는 다음 기본 단계를 단계별로 안내합니다.

* [Google Cloud Messaging 사용](#register)
* [모바일 서비스 구성](#configure)
* [앱에 푸시 알림 추가](#add-push)
* [푸시 알림을 전송하도록 스크립트 업데이트](#update-scripts)
* [알림을 받기 위한 데이터 삽입](#test)

이 자습서를 사용하려면 다음이 필요합니다.

+ [모바일 서비스 Android SDK]
+ 활성 Google 계정

이 자습서는 모바일 서비스 빠른 시작을 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작]을 완료해야 합니다. 

##<a id="register"></a>Google Cloud Messaging 사용


[WACOM.INCLUDE [GCM 사용](../includes/mobile-services-enable-Google-cloud-messaging.md)]

다음에는 이 API 키를 사용하여 모바일 서비스에서 GCM에 인증하고 앱 대신 푸시 알림을 보낼 수 있게 합니다.

##<a id="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

1. [Azure 관리 포털]에 로그온하여 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

   	![][18]

2. **푸시** 탭을 클릭하고 이전 절차에서 GCM으로부터 받은 **API 키** 값을 입력한 후 **저장**을 클릭합니다.

   	![][19]

이제 모바일 서비스가 GCM과 함께 작동하여 푸시 알림을 보내도록 구성되었습니다.

##<a id="add-push"></a>앱에 푸시 알림 추가


###프로젝트에 Google Play Services 추가

[WACOM.INCLUDE [Play 서비스 추가](../includes/mobile-services-add-Google-play-services.md)]

###코드 추가

1. 프로젝트 파일 **AndroidManifest.xml**을 엽니다. Google Cloud Messaging에는 매니페스트의 **minSdkVersion** 속성이 준수해야 하는 개발 및 테스트에 대한 최소 API 수준 요구 사항이 있습니다. 이전 장치를 사용 중이어서 16 미만 값으로 설정해야 하는 경우 이 값을 설정할 수 있는 최소값을 확인하려면 [Google Play Services SDK 설정](영문)을 참조하세요. 속성을 적절하게 설정합니다.

2. `uses-sdk` 요소에서 **targetSdkVersion**이 설치된 SDK 플랫폼 번호(1단계)로 설정되었는지 확인합니다. 사용 가능한 최신 버전으로 설정하는 것이 좋습니다. 

3. 이전 단계에서 선택한 항목에 따라 **uses-sdk** 태그가 다음과 같이 표시될 수 있습니다.

	    <uses-sdk
	        android:minSdkVersion="17"
	        android:targetSdkVersion="19" />
	
4. 다음 두 단계의 코드에서 `**my_app_package**`_를 프로젝트용 앱 패키지의 이름으로 바꿉니다. 이 이름은 `manifest` 태그의 `package` 특성 값입니다. 

5. 기존 `uses-permission` 요소 뒤에 다음과 같은 새 사용 권한을 추가합니다.

        <permission android:name="**my_app_package**.permission.C2D_MESSAGE" 
            android:protectionLevel="signature" />
        <uses-permission android:name="**my_app_package**.permission.C2D_MESSAGE" /> 
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
        <uses-permission android:name="android.permission.GET_ACCOUNTS" />
        <uses-permission android:name="android.permission.WAKE_LOCK" />

6. `application` 여는 태그 뒤에 다음 코드를 추가합니다. 

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="**my_app_package**" />
            </intent-filter>
        </receiver>



7. ToDoItem.java 파일을 열고 **TodoItem** 클래스에 다음 코드를 추가합니다.

			@com.google.gson.annotations.SerializedName("handle")
			private String mHandle;
		
			public String getHandle() {
				return mHandle;
			}
		
			public final void setHandle(String handle) {
				mHandle = handle;
			}
		
	This code creates a new property that holds the registration ID.

    <div class="dev-callout"><b>참고</b>
	<p>모바일 서비스에서 동적 스키마가 사용하도록 설정된 경우 이 속성이 포함된 새 항목이 삽입되면 새 <strong>handle</strong> 열이 <strong>TodoItem</strong> 테이블에 자동으로 추가됩니다.</p>
    </div>

8. [모바일 서비스 Android SDK]를 다운로드하여 압축을 풀고 **notifications** 폴더를 연 후 **notifications-1.0.1.jar** 파일을 Eclipse 프로젝트의 *libs* 폴더로 복사하고 *libs* 폴더를 새로 고칩니다.

    <div class="dev-callout"><b>Note</b>
	<p>The numbers at the end of the file name may change in subsequent SDK releases.</p>
    </div>

9.  *ToDoItemActivity.java* 파일을 열고 다음 import 문을 추가합니다.

		import com.microsoft.windowsazure.notifications.NotificationsManager;

10. 클래스에 다음 private 변수를 추가합니다. 여기서 `<PROJECT_NUMBER>`는 이전 절차에서 Google이 앱에 할당한 프로젝트 번호입니다.

		public static final String SENDER_ID = "<PROJECT_NUMBER>";

11. **onCreate** 메서드에서 MobileServiceClient가 인스턴스화되기 전에 장치에 알림 처리기를 등록하는 다음 코드를 추가합니다.

		NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);



12. 항목이 테이블에 삽입되기 전에 **addItem** 메서드에 다음 코드 줄을 추가합니다.

		item.setHandle(MyHandler.getHandle());

	이 코드는 항목의 `handle` 속성을 장치의 등록 ID로 설정합니다.

13. 패키지 탐색기에서 `src` 노드 아래의 패키지를 마우스 오른쪽 단추로 클릭하고 **새로 만들기**를 클릭한 후 **클래스**를 클릭합니다.

14. **Name**에 `MyHandler`을 입력하고 **Superclass**에 `com.microsoft.windowsazure.notifications.NotificationsHandler`를 입력한 다음 **마침**을 클릭합니다.

	![][6]

	This creates the new MyHandler class.

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
		

이제 푸시 알림을 지원하도록 앱이 업데이트됩니다.


##<a id="update-scripts"></a>관리 포털에서 등록된 삽입 스크립트 업데이트

1. 관리 포털로 돌아가서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다. 

   	![][21]

2. **todoitem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.
   
  	![][22]

   	**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

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

   	새 삽입 스크립트가 등록되며, 이 스크립트는 [gcm 개체]를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다. 

##<a id="test"></a>앱에서 푸시 알림 테스트

<div class="dev-callout"><b>참고</b>
	<p>에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.</p>
</div>

1. Eclipse를 다시 시작하고 Package Explorer에서 프로젝트를 마우스 오른쪽 단추로 클릭한 후 **속성**, **Android**를 차례로 클릭합니다. **Google API**를 선택하고 **확인**을 클릭합니다.

	![][23]

  	프로젝트 대상이 Google API로 설정됩니다.

2. **창**에서 **Android Virtual Device Manager**를 선택하고 해당 장치를 선택한 후 **편집**을 클릭합니다.

	![][24]

3. **대상**에서 **Google API**를 선택하고 확인을 클릭합니다.

   	![][25]

	AVD 대상이 Google API를 사용하도록 설정됩니다.

4. **실행** 메뉴에서 **실행**을 클릭하여 앱을 시작합니다.

5. 앱에서 _새 모바일 서비스 작업_ 등의 의미 있는 텍스트를 입력하고 **추가** 단추를 클릭합니다.

  	![][26]

6. 화면 아래쪽에 검은색 알림 상자가 잠시 나타납니다. 

  	![][28]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.

  ![][27]-->

이 자습서를 성공적으로 완료했습니다.

## <a name="next-steps"></a>다음 단계

이 간단한 예제에서는 사용자가 방금 삽입한 데이터로 푸시 알림을 받습니다. 클라이언트가 요청을 통해 GCM에서 사용하는 장치 토큰을 모바일 서비스에 제공합니다. 다음 자습서인 [앱 사용자에 대한 푸시 알림]에서는, 장치 토큰을 저장하고 삽입이 발생할 경우 저장된 모든 채널로 푸시 알림을 보내는 별도의 Devices 테이블을 만듭니다. 


<!-- Images. -->

[1]: ./media/mobile-services-android-get-started-push/mobile-services-google-developers.png
[2]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server.png
[3]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server2.png
[4]: ./media/mobile-services-android-get-started-push/mobile-services-google-create-server3.png
[5]: ./media/mobile-services-android-get-started-push/mobile-services-android-sdk-manager.png
[6]: ./media/mobile-services-android-get-started-push/mobile-services-android-create-class.png
[7]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-new-project.png
[8]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-sms-verify.png
[9]: ./media/mobile-services-android-get-started-push/mobile-google-cloud-project-created.png
[10]: ./media/mobile-services-android-get-started-push/mobile-google-choose-play-api.png
[18]: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
[19]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
[21]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
[22]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
[23]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
[24]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
[25]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
[26]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
[27]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-get-started-push/mobile-push-icon.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

<!-- URLs. -->
[Google 클라우드 콘솔]: https://cloud.google.com/console
[Google api]: http://go.microsoft.com/fwlink/p/?LinkId=268303
[Android 프로비전 포털]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Google Play 서비스 SDK 설정]: http://go.microsoft.com/fwlink/?LinkId=389801
[라이브러리 프로젝트 참조]: http://go.microsoft.com/fwlink/?LinkId=389800
[모바일 서비스 Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[모바일 서비스 시작]: /ko-kr/develop/mobile/tutorials/get-started-android
[데이터 작업 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-data-android
[인증 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-users-android
[푸시 알림 시작]: /ko-kr/develop/mobile/tutorials/get-started-with-push-android
[앱 사용자에 대한 푸시 알림]: /ko-kr/develop/mobile/tutorials/push-notifications-to-users-android
[스크립트를 통해 사용자 권한 부여]: /ko-kr/develop/mobile/tutorials/authorize-users-android

[Azure 관리 포털]: https://manage.windowsazure.com/
[모바일 서비스 서버 스크립트 참조]: http://go.microsoft.com/fwlink/?LinkId=262293
[모바일 서비스 Android 개념]: /ko-kr/develop/mobile/how-to-guides/work-with-android-client-library/
[gcm 개체]: http://go.microsoft.com/fwlink/p/?LinkId=282645

