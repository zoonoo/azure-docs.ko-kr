<properties pageTitle="Get started with push notifications (Appcelerator) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Appcelerator app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="Appcelerator team;mahender" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-appcelerator" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="Appcelerator="" team;mahender" />

# 모바일 서비스에서 푸시 알림 시작(레거시 푸시)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows 스토어 C#">Windows 스토어 C#</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows 스토어 JavaScript">Windows 스토어 JavaScript</a>
    <a href="/ko-kr/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a>
    <a href="/ko-kr/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/ko-kr/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/ko-kr/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a> -->
    <a href="/ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator" class="current">Appcelerator</a>
</div>

이 항목에서는 Microsoft Azure 모바일 서비스를 사용하여 Appcelerator Titanium Studio를 통해 개발된 iOS 및 Android 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 APNS(Apple Push Notification Service) 및 Google Cloud Messaging을 사용하여 quickstart 프로젝트에 푸시 알림을 추가합니다. 이 작업을 완료하면 레코드가 삽입될 때마다 모바일 서비스에서 푸시 알림을 전송합니다.

> [WACOM.NOTE] 모바일 서비스가 Azure 알림 허브와 통합되어 템플릿, 다중 플랫폼, 향상된 확장 등 추가적인 푸시 알림 기능을 지원합니다. 이 항목에서는 아직 알림 허브 통합을 사용하도록 업그레이드되지 않은 기존 모바일 서비스를 지원합니다. 새 모바일 서비스를 만들 때 이 통합 기능이 자동으로 사용하도록 설정됩니다. 가능하면 알림 허브를 사용하도록 서비스를 업그레이드해야 합니다. **Appcelerator를 사용한 알림 허브 푸시에 대한 자습서가 곧 제공될 예정입니다.**

1.  [인증서 서명 요청 생성][인증서 서명 요청 생성]
2.  [앱을 등록하고 푸시 알림을 사용하도록 설정][앱을 등록하고 푸시 알림을 사용하도록 설정]
3.  [앱용 프로비저닝 프로필 만들기][앱용 프로비저닝 프로필 만들기]
4.  [Google Cloud Messaging 사용][Google Cloud Messaging 사용]
5.  [Titanium에 대한 GCM 모듈 만들기][Titanium에 대한 GCM 모듈 만들기]
6.  [모바일 서비스 구성][모바일 서비스 구성]
7.  [앱에 푸시 알림 추가][앱에 푸시 알림 추가]
8.  [푸시 알림을 전송하도록 스크립트 업데이트][푸시 알림을 전송하도록 스크립트 업데이트]
9.  [알림을 받기 위한 데이터 삽입][알림을 받기 위한 데이터 삽입]

이 자습서를 사용하려면 다음이 필요합니다.

-   Appcelerator Azure 모바일 서비스 모듈
-   Appcelerator Titanium Studio 3.2.1 이상
-   iOS 7.0(이상 버전or ) 및 Android 4.3(이상 버전) 지원 장치
-   iOS 개발자 프로그램 멤버 자격
-   활성 Google 계정

> [WACOM.NOTE] 푸시 알림 구성 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

이 자습서는 모바일 서비스 퀵 스타트를 기반으로 합니다. 이 자습서를 시작하기 전에 먼저 [모바일 서비스 시작하기][모바일 서비스 시작하기]를 완료해야 합니다.

[WACOM.INCLUDE [Apple 푸시 알림 설정](../includes/enable-apple-push-notifications.md)]

## <a name="register-gcm"></a>Google Cloud Messaging 사용

> [WACOM.NOTE] 이 절차를 완료하려면 검증된 메일 주소가 포함된 Google 계정이 있어야 합니다. 새 Google 계정을 만들려면 [accounts.google.com][accounts.google.com]으로 이동하세요.

[WACOM.INCLUDE [GCM 사용](../includes/mobile-services-enable-Google-cloud-messaging.md)]

## <a name="gcm-module"></a>Titanium에 대한 GCM 모듈 만들기

### 모듈 만들기를 위한 Appcelerator Titanium Studio 준비

Android 모듈을 만들려면 Appcelerator Titanium Studio 내부에 Java 지원을 설치해야 합니다. 아직 설치하지 않은 경우 간략한 단계는 Appcelerator의 [Java 개발 도구 설치][Java 개발 도구 설치]를 참조하세요.

Android NDK를 설치해야 합니다. [][]<http://developer.android.com/sdk/ndk/index.html></a>에서 해당 .zip 파일을 다운로드하고 디스크의 특정 위치에 추출합니다. 이 위치를 기억해두세요.

### 새 모듈 만들기

1.  Appcelerator Titanium Studio를 엽니다.

2.  File -\> New -\> Mobile Module Project를 클릭합니다.

    ![][0]

3.  다음 창에서 프로젝트 설정 데이터를 입력합니다.

    -   **Project name:** 이 경우 "notificationhub"를 사용합니다(같을 수 있음).

    -   **Module Id:** 이 경우 "com.winwire.notificationhub"를 사용합니다. 이 값은 "application Id"와도 일치해야 합니다.

    -   **Deployment Targets:** 이 경우 Android를 선택합니다.

    > [WACOM.NOTE] 작업 영역 이름에 공백이 포함되지 않아야 합니다. 그렇지 않으면 컴파일된 모듈을 만들 때 문제가 발생합니다.

    ![][1]

4.  Next를 누르고 모듈에 대한 정보를 입력합니다.

    ![][2]

5.  마지막으로 Finish를 누릅니다.

6.  timodule.xml 파일을 엽니다. android 태그에 다음 변경 내용을 추가합니다.

        <manifest package="com.winwire.notificationhub" android:versionCode="1" android:versionName="1.0">
        <supports-screens android:anyDensity="true"/>
        <uses-sdk android:minSdkVersion="8"/>
        <uses-permission android:name= "com.google.android.c2dm.permission.RECEIVE"/>
        <permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE" android:protectionLevel="signature"/>
        <uses-permission android:name= "${tiapp.properties['id']}.permission.C2D_MESSAGE"/>
        <uses-permission android:name= "android.permission.WAKE_LOCK"/>
        <uses-permission android:name= "android.permission.VIBRATE"/>
        <uses-permission android:name= "android.permission.INTERNET"/>
        <uses-permission android:name= "android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name= "android.permission.USE_CREDENTIALS"/>
        <uses-permission android:name= "android.permission.ACCESS_NETWORK_STATE"/>
        <application>
        <service android:name= "com.winwire.notificationhub.GCMIntentService" />
        <receiver android:name= "com.google.android.gcm.GCMBroadcastReceiver" android:permission= "com.google.android.c2dm.permission.SEND">
        <!-- Start receiver on boot -->
        <intent-filter>
        <action android:name= "android.intent.action.BOOT_COMPLETED"/>
        <category android:name= "android.intent.category.HOME"/>
        </intent-filter>
        <!-- Receive the actual message -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.RECEIVE" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        <!-- Receive the registration id -->
        <intent-filter>
        <action android:name= "com.google.android.c2dm.intent.REGISTRATION" />
        <category android:name= "${tiapp.properties['id']}" />
        </intent-filter>
        </receiver>
        </application>
        </manifest>

> [WACOM.NOTE] 위의 코드에서 텍스트 *com.winwire.notificationhub*의 모든 인스턴스를 응용 프로그램 패키지 이름(모듈 ID)으로 바꿔야 합니다.

1.  알림 허브 모듈에서 "src" 폴더를 마우스 오른쪽 단추로 클릭하고 "New"로 이동하여 "folder"를 선택합니다. 폴더 이름을 com.google.android.gcm으로 지정합니다.

> [WACOM.NOTE] "New" 옵션에 "folder"가 표시되지 않으면 "Other"를 선택하고 General을 확장하고 나서 "Folder"를 선택합니다.

1.  이제 여기에서 ".java" 파일(gcm.zip)을 다운로드하고 해당 파일을 새로 만든 폴더(com.google.android.gcm)로 복사합니다.

2.  이제 모듈 ID로 이름이 지정된 폴더를 찾아서 확장합니다. 해당 폴더에서 ".java" 파일 목록을 확인할 수 있습니다. 해당 파일 가운데 이름이 프로젝트 이름+module.java(예를 들어 프로젝트 이름이 notificationhub이면 "NotificationhubModule.java"로 표시됨)인 파일을 열고 맨 위에 아래 코드 줄을 추가합니다.

        private static NotificationhubModule _THIS;
        private KrollFunction successCallback;
        private KrollFunction errorCallback;
        private KrollFunction messageCallback;

3.  같은 파일에서 생성자를 찾고 아래 코드로 바꿉니다.

        public NotificationhubModule() {
            super();
            _THIS = this;
        }

4.  같은 파일에서 맨 아래에 아래 코드 줄을 추가합니다.

        @Kroll.method
        public void registerC2dm(HashMap options) {
            successCallback = (KrollFunction) options.get("success");
            errorCallback = (KrollFunction) options.get("error");
            messageCallback = (KrollFunction) options.get("callback");
            String registrationId = getRegistrationId();
            if (registrationId != null && registrationId.length() > 0) {
                sendSuccess(registrationId);
            } else {
                GCMRegistrar.register(TiApplication.getInstance(), getSenderId());
            }
        }
        @Kroll.method
        public void unregister() {
            GCMRegistrar.unregister(TiApplication.getInstance());
        }
        @Kroll.method
        public String getRegistrationId() {
            return GCMRegistrar.getRegistrationId(TiApplication.getInstance());
        }
        @Kroll.method
        public String getSenderId() {
            return TiApplication.getInstance().getAppProperties()
            .getString("com.winwire.notificationhub.sender_id", "");
        }

        public void sendSuccess(String registrationId) {
            if (successCallback != null) {
                HashMap data = new HashMap();
                data.put("registrationId", registrationId);
                successCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendError(String error) {
            if (errorCallback != null) {
                HashMap data = new HashMap();
                data.put("error", error);
                errorCallback.callAsync(getKrollObject(), data);
            }
        }

        public void sendMessage(HashMap messageData) {
            if (messageCallback != null) {
                HashMap data = new HashMap();
                data.put("data", messageData);
                messageCallback.call(getKrollObject(), data);
            }
        }
        public static NotificationhubModule getInstance() {
            return _THIS;
        }

5.  이제 module.zip을 다운로드하고 파일을 모듈 ID를 이름으로 사용한 폴더로 복사합니다.

> [WACOM.NOTE] 위의 파일에서 텍스트 *com.winwire.notificationhub*의 모든 인스턴스를 응용 프로그램 패키지 이름(모듈 ID)으로 바꿔야 합니다. 또한 "NotificationhubModule"을 프로젝트 이름+모듈(예: "NotificationhubModule")로 바꿔야 합니다.

### 모듈 빌드/패키징

**Deploy \> Package - Android Module**을 선택합니다. Studio를 사용하여 BlackBerry 모듈을 빌드할 수는 없습니다. BlackBerry NDK CLI 도구나 Momentics IDE를 사용하세요.

![][3]

모든 프로젝트 또는 특정 프로젝트에 대해 모듈을 배포하도록 선택할 수 있습니다. 이 작업은 [Titanium 모듈 사용][Titanium 모듈 사용]에 설명된 설치 규칙을 따르세요. 요약하면 다음과 같습니다.

-   모든 프로젝트: 모듈 .zip 파일이 Titanium SDK 설치 위치의 루트에서 삭제됩니다.

-   특정 프로젝트: 모듈 .zip 파일이 프로젝트의 루트에서 삭제됩니다.

## <a name="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

[WACOM.INCLUDE [mobile-services-apns-configure-push](../includes/mobile-services-apns-configure-push.md)]

1.  이전 절차에서 GCM으로부터 받은 API 키 값을 입력한 후 저장을 클릭합니다.

    ![][4]

이제 모바일 서비스가 APNS 및 GCM과 함께 작동하도록 구성되었습니다.

## <a name="add-push"></a>앱에 푸시 알림 추가

1.  tiapp.xml에서 tiapp.xml 탭을 선택하고("Overview" 탭 옆의 아래쪽에 있음) `<android>` 태그를 찾습니다. 태그 아래에 다음 코드를 추가합니다.

        <modules>
            <module platform="android">ModuleId</module>
        </modules>
        <property name="ApplicationId.sender_id" type="string">Provide your GCM sender ID</property>
        <property name="ApplicationId.icon type="int">2130837504</property>
        <property name="ApplicationId.component" type="string">ApplicationId/ApplicationId.AppNameActivity</property>

> [WACOM.NOTE] 위의 코드에서 **ModuleId** 및 **ApplicationId**를 GCM 모듈을 만들 때 입력한 모듈 ID 및 프로젝트를 만들 때 입력한 응용 프로그램 ID로 바꿔야 합니다. **ModuleId** 및 **ApplicationId**는 둘 다 같아야 합니다. 또한 **ApplicationId.AppNameActivity**를 변경해야 합니다. com.winwire.notificationhub/ com.winwire.notificationhub.NotificationhubActivity 같이 표시되어야 합니다.

1.  앞에서 만든 GCM 모듈을 복사하고 아래 위치에 저장합니다.

    <table>
    <tr>
    <td>
    OSX

    </td>
    <td>
    /Library/Application Support/Titanium 또는 ~/Library/Application Support/Titanium

    </td>
    </tr>
    <td>
    Windows 7

    </td>
    <td>
    C:\\Users\\username\\AppData\\Roaming(또는 Titanium Studio 1.0.1 이상의 C:\\ProgramData\\Titanium)

    </td>
    </tr>
    <td>
    Windows XP

    </td>
    <td>
    C:\\Documents and Settings\\username\\Application Data(또는 Titanium Studio 1.0.1 이하의 C:\\Documents and Settings\\All Users\\Application Data\\Titanium)

    </td>
    </tr>
    <td>
    Linux

    </td>
    <td>
    ~/.titanium

    </td>
    </tr>
    </tr>
    </table>

> [WACOM.NOTE] Mac OS에서 라이브러리는 숨겨진 폴더입니다. 이 폴더를 표시하려면 다음 명령을 실행하고 파인더를 다시 시작합니다. `**defaults write com.apple.finder AppleShowAllFiles TRUE**`

1.  Appcelerator Titanium Studio에서 index.js 파일을 열고 맨 아래에 다음 코드를 추가합니다. 이 코드는 푸시 알림에 대한 장치를 등록합니다.

            Alloy.Globals.tempRegId = '';
                if (OS_ANDROID) {
                var gcm = require('com.winwire.notificationhub');
            gcm.registerC2dm({
            success : function(e) {
            var regId = e.registrationId;
            Alloy.Globals.tempRegId = regId;
            },
            error : function(e) {
            alert("Error during registration : " + e.error);
            var message;
            if (e.error == "ACCOUNT_MISSING") {
            message = "No Google account found; you will need to add on in order to activate notifications";
            }
            Titanium.UI.createAlertDialog({
            title : 'Push Notification Setup',
            message : message,
            buttonNames : ['OK']
            }).show();
            },
            callback : function(e)// called when a push notification is received
            {
            var data = JSON.stringify(e.data);
            var intent = Ti.Android.createIntent({
            action : Ti.Android.ACTION_MAIN,
            className : 'com.winwire.notificationhub. WindowsAzureActivity',
            flags : Ti.Android.FLAG_ACTIVITY_RESET_TASK_IF_NEEDED | Ti.Android.FLAG_ACTIVITY_SINGLE_TOP
            });
            intent.addCategory(Titanium.Android.CATEGORY_LAUNCHER);
            var NotificationClickAction = Ti.Android.createPendingIntent({
            activity : Ti.Android.currentActivity,
            intent : intent,
            flags : Ti.Android.FLAG_UPDATE_CURRENT,
            type : Ti.Android.PENDING_INTENT_FOR_ACTIVITY
            });
            var NotificationMembers = {
            contentTitle : 'Notification Hub Demo',
            contentText : e.data.message,
            defaults : Titanium.Android.NotificationManager.DEFAULT_SOUND,
            tickerText : 'Notification Hub Demo',
            icon : Ti.App.Android.R.drawable.appicon,
            when : new Date(),
            flags : Ti.Android.FLAG_AUTO_CANCEL,
            contentIntent : NotificationClickAction
            };
            Ti.Android.NotificationManager.notify(1, .Android.createNotification(NotificationMembers));
            var toast = Titanium.UI.createNotification({
            duration : 2000,
            message : e.data.message
            });
            toast.show();
            alert(e.data.message);
            }
            });
            } else  if (OS_IOS) {
            Titanium.Network.registerForPushNotifications({
            types : [Titanium.Network.NOTIFICATION_TYPE_BADGE, .Network.NOTIFICATION_TYPE_ALERT, .Network.NOTIFICATION_TYPE_SOUND],
            success : function(e) {
            deviceToken = e.deviceToken;
            Alloy.Globals.tempRegId = deviceToken;
            },
            error : function(e) {
            Ti.API.info("Error during registration: " + e.error);
            },
            callback : function(e) {
            var data = e.data.inAppMessage;
            if (data != null && data != '') {
            alert(data);
            }
            }
            });
            }

2.  이제 TableData.js 파일을 열고 **addOrUpdateDataFromAndroid** 함수에서 다음 줄을 찾습니다.

        var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    새 항목을 삽입하기 위해 else 조건에서만 위의 코드를 바꾸세요.

3.  Android의 경우 위의 코드를 다음 코드로 바꿉니다.

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'handle' : Alloy.Globals.tempRegId
    };

4.  이제 **updateOrAddData** 함수에서 다음 줄을 찾습니다.

           var request = {
            'text' : alertTextField.getValue(),
            'complete' : false
        };

    새 항목을 삽입하기 위해 else 조건에서만 위의 코드를 바꾸세요.

5.  iOS의 경우 위의 코드를 다음 코드로 바꿉니다.

    var request = {
     'text' : alertTextField.getValue(),
     'complete' : false,
     'deviceToken' : Alloy.Globals.tempRegId
    };

이제 iOS 및 Android 플랫폼에서 푸시 알림을 지원하도록 앱이 업데이트됩니다.

## <a name="update-scripts"></a>관리 포털에서 등록된 삽입 스크립트 업데이트

1.  관리 포털에서 데이터 탭을 클릭한 후 TodoItem 테이블을 클릭합니다.

    ![][5]

2.  TodoItem에서 스크립트 탭을 클릭하고 삽입을 선택합니다.

    ![][6]

    TodoItem 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3.  삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

**iOS:**

        function insert(item, user, request) { 
            request.execute();
            // Set timeout to delay the notification, to provide time for the  
            // app to be closed on the device to demonstrate toast notifications 
            setTimeout(function() { 
                push.apns.send(item.deviceToken, { 
                    alert: "Toast: " + item.text, 
                    payload: { 
                        inAppMessage: "Hey, a new item arrived: '" + item.text + "'" 
                    }
                 }); 
            }, 2500);
        }

    > [WACOM.NOTE] This script delays sending the notification to give you time to close the app to receive a push notification.  

**Android:**

          function insert(item, user, request) {
            request.execute({ 
                success: function() {  
                    // Write to the response and then send the notification in the background 
                    request.respond();  
                    push.gcm.send(item.handle, item.text, {
                        success: function(response) { 
                            console.log('Push notification sent: ', response);
                        }, error: function(error)   { 
                            console.log('Error sending push notification: ', error);      
                            }      
                   }); 
                }  
          });  
        }

새 삽입 스크립트가 등록되며, 이 스크립트는 [모바일 서비스 push 개체][모바일 서비스 push 개체]를 사용하여 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.



  [인증서 서명 요청 생성]: #certificates
  [앱을 등록하고 푸시 알림을 사용하도록 설정]: #register
  [앱용 프로비저닝 프로필 만들기]: #profile
  [Google Cloud Messaging 사용]: #register-gcm
  [Titanium에 대한 GCM 모듈 만들기]: #gcm-module
  [모바일 서비스 구성]: #configure
  [앱에 푸시 알림 추가]: #add-push
  [푸시 알림을 전송하도록 스크립트 업데이트]: #update-scripts
  [알림을 받기 위한 데이터 삽입]: #test
  [모바일 서비스 시작하기]: /ko-kr/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Java 개발 도구 설치]: http://docs.appcelerator.com/titanium/latest/#!/guide/Installing_the_Java_Development_Tools
  [0]: http://developer.android.com/sdk/ndk/index.html
  [0]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0011.png
  [1]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0031.png
  [2]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0041.png
  [3]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image0061.png
  [Titanium 모듈 사용]: http://docs.appcelerator.com/titanium/latest/#!/guide/Using_Titanium_Modules
  [4]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image062.png
  [5]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image064.png
  [6]: ./media/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push/image066.png
  [모바일 서비스 push 개체]: http://go.microsoft.com/fwlink/p/?linkid=272333&clcid=0x409
