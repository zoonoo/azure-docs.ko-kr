 <properties
	pageTitle="모바일 서비스 앱에 푸시 알림 추가(Xamarin.Forms) - 모바일 서비스"
	description="Azure 모바일 서비스와 함께 Xamarin.Forms 앱에서 푸시 알림을 사용하는 방법에 대해 알아봅니다."
	documentationCenter="xamarin"
	authors="wesmc7777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-xamarin"
	ms.workload="mobile"
	ms.date="01/22/2016"
	ms.author="wesmc"/>

# Xamarin.Forms 앱에 푸시 알림 추가

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##개요

이 섹션에서는 Azure 모바일 서비스를 사용하여 Xamarin.Forms 솔루션의 iOS, Android 및 Windows Phone 앱에 푸시 알림을 보내는 방법을 보여 줍니다. 먼저 모바일 서비스를 만듭니다. 그런 다음 시작 샘플을 다운로드하고, 해당 푸시 알림 서비스에 등록하고, 솔루션에 코드를 추가하여 해당 서비스에서 알림을 받습니다.

이 자습서를 완료하면 모바일 서비스는 사용자가 앱 중 하나에 작업을 추가할 때마다 푸시 알림을 보냅니다. 여기서 전체 샘플을 확인할 수 있습니다. [전체 Xamarin.Forms Azure 푸시 알림 샘플](영문)

이 자습서를 사용하려면 다음이 필요합니다.

+ iOS 8 장치(iOS 시뮬레이터에서에서 푸시 알림을 테스트할 수 없음)
+ iOS 개발자 프로그램 멤버 자격
+ [Xamarin.iOS Studio]
+ [Azure 모바일 서비스 구성 요소]
+ 활성 Google 계정
+ [Google Cloud Messaging 클라이언트 구성 요소]. 자습서에 이 구성 요소가 추가됩니다.

항목 내용

1. [새 모바일 서비스 만들기](#create-service)
2. [시작 샘플 다운로드 및 구성](#download-starter-sample)
4. [Xamarin.Forms.iOS 앱에 푸시 알림 추가](#iOS)
5. [Xamarin.Forms.Android 앱에 푸시 알림 추가](#Android)
6. [Xamarin.Forms.Windows 앱에 푸시 알림 추가](#Windows)
7. [모든 앱에 푸시 알림을 보내도록 Azure 테이블 삽입 스크립트 업데이트](#all-apps)

## <a name="create-service"></a>새 모바일 서비스 만들기

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

새 모바일 서비스에서 앱 데이터를 저장하려면 먼저 새 테이블을 만들어야 합니다.

1. Azure 클래식 포털에서 **모바일 서비스**를 클릭한 후 방금 만든 모바일 서비스를 클릭합니다.

2. **데이터** 탭을 클릭한 후 **+만들기**를 클릭합니다.

    ![][123]

   	**Create new table** 대화 상자가 표시됩니다.

3. **Table name**에 _TodoItem_을 입력한 후 확인 단추를 클릭합니다.

    ![][124]

  	기본 사용 권한 집합이 설정된 새 저장소 테이블 **TodoItem**이 생성됩니다. 앱의 모든 사용자가 테이블의 데이터에 액세스하고 변경할 수 있습니다.

    > [AZURE.NOTE] 모바일 서비스 빠른 시작에 같은 테이블 이름이 사용됩니다. 하지만 각 테이블은 주어진 모바일 서비스에 특정된 스키마에 생성됩니다. 이렇게 하면 여러 모바일 서비스가 같은 데이터베이스를 사용할 때 발생하는 데이터 충돌을 예방할 수 있습니다.

4. 새 **TodoItem** 테이블을 클릭하고 데이터 행이 없는 것을 확인합니다.

5. **열** 탭을 클릭하고 자동으로 생성된 **id** 열 하나만 있는지 확인합니다.

  	이 부분이 모바일 서비스의 테이블 최소 요구 사항입니다.

    > [AZURE.NOTE] 모바일 서비스에서 동적 스키마를 사용하면 삽입 또는 업데이트 작업에서 JSON 개체를 모바일 서비스에 보낼 때 새 열이 자동으로 생성됩니다.

이제 새 모바일 서비스를 앱의 데이터 저장소로 사용할 준비가 되었습니다.

## <a name="download-starter-sample"></a>시작 샘플 다운로드 및 구성
기존 샘플에 푸시 알림을 추가할 것입니다.

1. 다음 샘플을 다운로드합니다. [Xamarin.Forms Azure 푸시 알림 시작 샘플](영문)

2. [Azure 클래식 포털]에서 **모바일 서비스**를 클릭한 후 해당 모바일 서비스를 클릭합니다. **대시보드** 탭을 클릭하고 **사이트 URL** 값을 기록해 둡니다. 이제 **키 관리**를 클릭하고 **응용 프로그램 키**를 기록해 둡니다. 앱 코드에서 모바일 서비스에 액세스할 때 이러한 값이 필요합니다.

3. 솔루션의 **ToDoAzure(Portable)** 프로젝트에서 **Constants.cs** 파일을 열고 `ApplicationURL` 및 `ApplicationKey`를 이전 단계에서 얻은 사이트 URL 및 응용 프로그램 키로 바꿉니다.

## <a name="iOS"></a>Xamarin.Forms.iOS 앱에 푸시 알림 추가

APNS(Apple Push Notification Service)를 사용하여 iOS 앱에 푸시 알림을 추가합니다. 활성 Google 계정 및 [Google Cloud Messaging 클라이언트 구성 요소]가 필요합니다.

>[AZURE.IMPORTANT] APNS(Apple Push Notification Service) 요구 사항 때문에 에뮬레이터 대신 iOS 지원 장치(iPhone 또는 iPad)에서 푸시 알림을 배포 및 테스트해야 합니다.

APNS는 인증서를 사용하여 모바일 서비스를 인증합니다. 필요한 인증서를 만들어 모바일 서비스에 업로드하려면 해당 지침을 따르세요. 공식 APNS 기능 설명서는 [Apple Push Notification Service](영문)를 참조하세요.

### <a name="certificates"></a>인증서 서명 요청 파일 생성

먼저, 서명된 인증서를 생성하기 위해 Apple에서 사용하는 CSR(인증서 서명 요청) 파일을 생성해야 합니다.

1. 유틸리티에서 **Keychain Access 도구**를 실행합니다.

2. **Keychain Access**를 클릭하고 **Certificate Assistant**를 확장한 다음 **Request a Certificate from a Certificate Authority...**를 클릭합니다.

    ![][5]

3. **User Email Address**를 입력하고 **Common Name** 값을 입력한 다음 **Saved to disk**가 선택되었는지 확인하고 **Continue**를 클릭합니다.

    ![][6]

4. **Save As**에 CSR(인증서 서명 요청) 파일의 이름을 입력하고, **Where**에서 위치를 선택한 후 **Save**를 클릭합니다.

    ![][7]

    선택한 위치를 기억해 두세요.

이제 Apple에 앱을 등록하고, 푸시 알림을 사용하도록 설정하고, 내보낸 CSR을 업로드하여 푸시 인증서를 만들 것입니다.

### <a name="register"></a>푸시 알림에 대해 앱 등록

모바일 서비스에서 iOS 앱으로 푸시 알림을 보내려면 Apple에 앱을 등록하고 푸시 알림을 등록해야 합니다.

1. 아직 앱을 등록하지 않은 경우 Apple 개발자 센터의 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 이동하여 Apple ID로 로그온하고 **Identifiers**와 **App IDs**를 클릭한 다음에 **+** 기호를 클릭하여 앱의 앱 ID를 만듭니다.

    ![][102]

2. **Description**에 앱의 이름을 입력하고 고유한 **Bundle Identifier**를 입력한 후 기억해 둔 다음 "App Services" 섹션에서 "Push Notifications" 옵션을 선택하고 **Continue**를 클릭합니다. 이 예제에서는 **MobileServices.Quickstart** ID를 사용하지만 실제로 이 ID를 사용해서는 안 됩니다. 모든 사용자는 고유한 앱 ID를 사용해야 하기 때문입니다. 사용자의 전체 이름 또는 이니셜을 앱 이름 뒤에 추가하는 것이 좋습니다.

    ![][103]

    앱 ID가 생성되고 정보를 **제출**하도록 요청됩니다. **Submit**를 클릭합니다.

    ![][104]

    **Submit**를 클릭하면 아래와 같은 **Registration complete** 화면이 표시됩니다. **Done**을 클릭합니다.

    ![][105]

3. 방금 만든 앱 ID를 찾아 해당 행을 클릭합니다.

    ![][106]

    앱 ID를 클릭하면 앱 및 앱 ID에 대한 세부 정보가 표시됩니다. **Settings** 단추를 클릭합니다.

    ![][107]

4. 화면 아래로 스크롤하여 **Development Push SSL Certificate** 섹션에서 **Create Certificate...** 단추를 클릭합니다.

    ![][108]

    "Add iOS Certificate" assistant가 표시됩니다.

    참고: 이 자습서에서는 개발 인증서를 사용합니다. 프로덕션 인증서를 등록할 때에도 동일한 프로세스가 사용됩니다. 모바일 서비스로 인증서를 업로드할 때 동일한 인증서 유형을 설정해야 합니다.

5. **Choose File**을 클릭하고 이전에 CSR 파일을 저장한 위치로 이동하여 **Generate**를 클릭합니다.

    ![][110]

6. 포털에서 인증서가 생성되면 **Download** 단추를 클릭하고 **Done**을 클릭합니다.

    ![][111]

    서명 인증서가 다운로드되어 컴퓨터의 다운로드 폴더에 저장됩니다.

    ![][9]

    참고: 기본적으로 다운로드된 개발 증명서 파일은 이름이 <strong>aps\_development.cer</strong>로 지정됩니다.

7. 다운로드한 푸시 인증서 **aps\_development.cer**을 두 번 클릭합니다.

    아래와 같이 새 인증서가 Keychain에 설치됩니다.

    ![][10]

    인증서의 이름은 다를 수 있지만 <strong>Apple Development iOS Push Notification Services:</strong>가 앞에 옵니다.

나중에 이 인증서를 사용하여 .p12 파일을 생성하고 모바일 서비스에 업로드하여 APNS에서의 인증에 사용하게 됩니다.

### <a name="profile"></a>앱용 프로비저닝 프로필 만들기

1. <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS Provisioning Portal</a>로 돌아가서 **Provisioning Profiles**와 **All**을 차례로 선택하고 **+** 단추를 클릭하여 새 프로필을 만듭니다. **Add iOS Provisioning Profile** 마법사가 실행됩니다.

    ![][112]

2. **Development**에서 프로비저닝 프로필 유형으로 **iOS App Development**를 선택하고 **Continue**를 클릭합니다.

3. **App ID** 드롭다운 목록에서 모바일 서비스 Quickstart 앱의 앱 ID를 선택하고 **Continue**를 클릭합니다.

    ![][113]

4. **Select certificates** 화면에서 앞서 만든 인증서를 선택하고 **Continue**를 클릭합니다.

    ![][114]

5. 테스트에 사용할 **Devices**를 선택하고 **Continue**를 클릭합니다.

    ![][115]

6. 끝으로, **Profile Name**에서 프로필의 이름을 선택하고 **Generate**와 **Done**을 차례로 클릭합니다.

    ![][116]

    새 프로비저닝 프로필이 생성됩니다.

    ![][117]

7. Xcode에서 Organizer를 열고 Devices view를 선택하고, 왼쪽 창의 **Library** 섹션에서 **Provisioning Profiles**를 선택하고, 가운데 창의 아래쪽에서 **Refresh** 단추를 클릭합니다.

### <a name="configure-mobileServices"></a>푸시 요청을 전송하도록 모바일 서비스 구성

APNS에 앱을 등록하고 프로젝트를 구성했으면 APNS와 통합되도록 모바일 서비스를 구성해야 합니다.

1. Keychain Access에서 새 인증서를 마우스 오른쪽 단추로 클릭하여 **Export**를 클릭하고, 파일의 이름을 지정한 다음, **.p12** 형식을 선택하고, **Save**를 클릭합니다.

    ![][28]

    내보낸 인증서의 파일 이름과 위치를 적어둡니다.

2. [Azure 클래식 포털]에 로그온하고 **모바일 서비스**를 클릭한 후 앱을 클릭합니다.

    ![][18]

3. **푸시** 탭을 클릭하고 **apple 푸시 알림 설정**에서 **업로드**를 클릭합니다.

    ![][19]

    인증서 업로드 대화 상자가 표시됩니다.

4. **파일**을 클릭하고, 내보낸 인증서 .p12 파일을 선택한 다음, **암호**를 입력하고, 올바른 **모드**가 선택되었는지 확인합니다. 그런 다음 확인 아이콘을 클릭한 후 **저장**을 클릭합니다.

    ![][20]

모바일 서비스가 이제 APNS와 작동하도록 구성됩니다.

### <a name="configure-app"></a>Xamarin.iOS 응용 프로그램 구성

1. Xamarin.Studio 또는 Visual Studio에서 **Info.plist**를 열고 앞에서 만든 ID를 사용하여 **번들 식별자**를 업데이트합니다.

    ![][121]

2. 아래의 **Background Modes**로 스크롤하여 **Enable Background Modes** 상자와 **Remote notifications** 상자를 선택합니다.

    ![][122]

3. Solution Panel에서 프로젝트를 두 번 클릭하여 **Project Options**를 엽니다.

4.  **Build**에서 **iOS Bundle Signing**을 선택하고 방금 이 프로젝트에 대해 설정한 해당 **ID** 및 **프로비저닝 프로필**을 선택합니다.

    ![][120]

    이제 Xamarin 프로젝트에서 코드 서명에 새 프로필을 사용하게 됩니다. 공식 Xamarin 장치 프로비저닝 설명서를 보려면 [Xamarin 장치 프로비저닝]을 참조하세요.

### <a name="add-push"></a>앱에 푸시 알림 추가

1. Xamarin.Studio 또는 Visual Studio에서 **ToDoAzure.iOS** 프로젝트를 확장하고 확장하고 **AppDelegate** 클래스를 연 다음 **FinishedLaunching** 이벤트를 다음 코드로 바꿉니다.

        public override bool FinishedLaunching(UIApplication app, NSDictionary options)
        {
             // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            global::Xamarin.Forms.Forms.Init();
            instance = this;
            CurrentPlatform.Init();

            todoItemManager = new ToDoItemManager();
            App.SetTodoItemManager(todoItemManager);


            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            LoadApplication(new App());
            return base.FinishedLaunching(app, options);
        }

6. **AppDelegate**에서 **RegisteredForRemoteNotifications** 이벤트를 재정의합니다.

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            // Modify device token
            string _deviceToken = deviceToken.Description;
            _deviceToken = _deviceToken.Trim('<', '>').Replace(" ", "");

            // Get Mobile Services client
            MobileServiceClient client = todoItemManager.GetClient();

            // Register for push with Mobile Services
            IEnumerable<string> tag = new List<string>() { "uniqueTag" };

            const string template = "{"aps":{"alert":"$(message)"}}";

            var expiryDate = DateTime.Now.AddDays(90).ToString
                (System.Globalization.CultureInfo.CreateSpecificCulture("ko-KR"));

            var push = client.GetPush();

            push.RegisterTemplateAsync(_deviceToken, template, expiryDate, "myTemplate", tag);
        }

7. **AppDelegate**에서 **ReceivedRemoteNotification** 이벤트를 재정의합니다.

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            NSObject inAppMessage;

            bool success = userInfo.TryGetValue(new NSString("inAppMessage"), out inAppMessage);

            if (success)
            {
                var alert = new UIAlertView("Got push notification", inAppMessage.ToString(), null, "OK", null);
                alert.Show();
            }
        }

이제 앱이 푸시 알림을 지원하도록 업데이트됩니다.

### <a name="update-scripts"></a>Azure 클래식 포털에서 등록된 삽입 스크립트 업데이트

1. Azure 클래식 포털에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][21]

2. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][22]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }

    새 삽입 스크립트가 등록되며, 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

   >[AZURE.NOTE] 이 스크립트는 앱을 닫고 알림 메시지를 수신할 수 있는 시간을 주기 위해 알림 전송을 지연시킵니다.

### <a name="test"></a>앱에서 푸시 알림 테스트

1. **실행** 단추를 눌러 프로젝트를 빌드하고 iOS 지원 장치에서 앱을 시작한 다음, **확인**을 클릭하여 푸시 알림을 수락합니다.

   >[AZURE.NOTE] 앱에서 푸시 알림을 명시적으로 수락해야 합니다. 이 요청은 앱이 처음 실행될 때만 발생합니다.

2. 앱에서 **추가** 단추를 클릭하고 작업 제목을 추가한 후 **저장** 단추를 클릭합니다.

3. 알림이 수신되는지 확인하고, **확인**을 클릭하여 알림을 해제합니다.


이 자습서를 성공적으로 완료했습니다.

## <a name="Android"></a>Xamarin.Forms.Android 앱에 푸시 알림 추가

GCM(Google Cloud Messaging) 서비스를 사용하여 Android 앱에 푸시 알림을 추가합니다. 활성 Google 계정 및 [Google Cloud Messaging 클라이언트 구성 요소]가 필요합니다.

###<a id="register"></a>Google Cloud Messaging 사용

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

###<a id="configure"></a>푸시 요청을 전송하도록 모바일 서비스 구성

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

###<a id="update-scripts"></a>알림을 보내도록 등록된 삽입 스크립트 업데이트

>[AZURE.NOTE] 다음 단계에서는 Azure 클래식 포털의 TodoItem 테이블에서 삽입 작업에 등록된 스크립트를 업데이트하는 방법을 보여 줍니다. 또한 서버 탐색기의 Azure 노드의 Visual Studio에서 이 모바일 서비스 스크립트에 직접 액세스하여 편집할 수도 있습니다.

[Azure 클래식 포털]에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

   ![][21]

2. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

   ![][22]

**TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    새 삽입 스크립트가 등록되며, 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

   >[AZURE.NOTE] 이 스크립트는 앱을 닫고 알림 메시지를 수신할 수 있는 시간을 주기 위해 알림 전송을 지연시킵니다.


###<a id="configure-app"></a>푸시 알림에 대한 기존 프로젝트 구성

1. 솔루션 보기에서 Xamarin.Android 앱의 **Components** 폴더를 확장하여 Azure 모바일 서비스 패키지가 설치되었는지 확인합니다.

2. **Components** 폴더를 마우스 오른쪽 단추로 클릭하고 **Get More Components...**를 클릭하고 **Google Cloud Messaging Client** 구성 요소를 검색하여 이를 프로젝트에 추가합니다.

1. MainActivity.cs 프로젝트 파일을 열고 클래스에 다음 using 문을 추가합니다.

		using Gcm.Client;


4.	**MainActivity** 클래스에서 **LoadApplication** 메서드를 호출한 후 **OnCreate** 메서드에 다음 코드를 추가합니다.

            try
            {
                // Check to ensure everything's setup right
                GcmClient.CheckDevice(this);
                GcmClient.CheckManifest(this);

                // Register for push notifications
                System.Diagnostics.Debug.WriteLine("Registering...");
                GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
            }
            catch (Java.Net.MalformedURLException)
            {
                CreateAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
            }
            catch (Exception e)
            {
                CreateAndShowDialog(e, "Error");
            }

이제 **MainActivity**가 푸시 알림 추가를 위해 준비됩니다.

###<a id="add-push"></a>앱에 푸시 알림 코드 추가

5. ToDoAzure.Droid 프로젝트에서 `GcmService` 프로젝트에 새 클래스를 만듭니다.

5. **GcmService** 클래스에 다음 using 문을 추가합니다.

		using Gcm.Client;
		using Microsoft.WindowsAzure.MobileServices;

6. **using** 문과 **namespace** 선언 사이에 다음 사용 권한 요청을 추가합니다.

		[assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

7. **GcmService.cs** 프로젝트 파일에 다음 클래스를 추가합니다.

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]

        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {

            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };

        }

	위의 코드에서 Google 개발자 포털에서 앱을 프로비전할 때 Google에서 할당한 프로젝트 번호로 _`<PROJECT_NUMBER>`_을(를) 바꿉니다.

8. GcmService.cs 프로젝트 파일에서 **GcmService** 클래스를 정의하는 다음 코드를 추가합니다.

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


	이 클래스는 **GcmServiceBase**에서 파생되며 **서비스** 특성이 이 클래스에 적용되어야 합니다.

	>[AZURE.NOTE]**GcmServiceBase** 클래스는 **OnRegistered()**, **OnUnRegistered()**, **OnMessage()** 및 **OnError()** 메서드를 구현합니다. **GcmService** 클래스에서 이러한 메서드를 재정의해야 합니다.

5. **OnRegistered** 이벤트 처리기를 재정의하는 **GcmService** 클래스에 다음 코드를 추가합니다.

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(PushHandlerBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("GcmService Registered...", "The device has been Registered, Tap to View!");

            MobileServiceClient client =  MainActivity.DefaultService.todoItemManager.GetClient;

            var push = client.GetPush();

            MainActivity.DefaultService.RunOnUiThread(() => Register(push, null));

        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string template = "{"data":{"message":"$(message)"}}";

                await push.RegisterTemplateAsync(RegistrationID, template, "mytemplate", tags);
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

	이 메서드는 반환된 GCM 등록 ID를 사용하여 푸시 알림을 위해 Azure에 등록합니다.

10. **GcmService**의 **OnMessage** 메서드를 다음 코드로 재정의합니다.

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(PushHandlerBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
        }

12. 컴파일할 프로젝트에 필요한 **OnUnRegistered()** 및 **OnError()**에 대해 재정의하는 다음 메서드를 추가합니다.

		protected override void OnUnRegistered(Context context, string registrationId)
		{
			Log.Error("GcmService", "Unregistered RegisterationId : " + registrationId);
		}

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(PushHandlerBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }

###<a id="test"></a>앱에서 푸시 알림 테스트

USB 케이블로 Android 휴대폰을 직접 연결하거나 에뮬레이터에서 가상 장치를 사용하여 앱을 테스트할 수 있습니다.

에뮬레이터에서 이 앱을 실행하는 경우 Google API를 지원하는 AVD(Android Virtual Device)를 사용해야 합니다.

> [AZURE.IMPORTANT] 푸시 알림을 받으려면 Android Virtual Device에서 Google 계정을 설정해야 합니다(에뮬레이터에서 **Settings**로 이동하고 **Add Account** 클릭). 에뮬레이터가 인터넷에 연결되어 있어야 합니다.

1. **Tools**에서 **Open Android Emulator Manager**를 클릭하고 해당 장치를 선택한 후 **Edit**를 클릭합니다.

    ![][125]

2. **Target**에서 **Google APIs**를 선택하고 **OK**를 클릭합니다.

    ![][126]

3. 위쪽 도구 모음에서 **Run**을 클릭하고 앱을 선택합니다. 에뮬레이터가 시작되고 앱이 실행됩니다.

  앱이 GCM에서 *registrationId*를 검색하고 알림 허브에 등록됩니다.

1. 앱에서 새 작업을 추가합니다.

2. 알림을 보려면 화면의 위쪽에서 아래쪽으로 살짝 밀어서 장치의 알림 센터를 엽니다.

	![][127]

## <a name="Windows"></a>Xamarin.Forms.Windows 앱에 푸시 알림 추가

이 섹션에서는 Azure 모바일 서비스를 사용하여 Xamarin.Forms 솔루션에 포함된 Windows Phone Silverlight 앱에 푸시 알림을 보내는 방법을 보여 줍니다.

###<a id="update-app"></a> 알림 등록을 위해 앱 업데이트

앱에서 푸시 알림을 받을 수 있으려면 알림 채널을 등록해야 합니다.

1. Visual Studio에서 App.xaml.cs 파일을 열고 다음 `using` 문을 추가합니다.

        using Microsoft.Phone.Notification;

3. App.xaml.cs에 다음을 추가합니다.

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                   // Register for notifications using the new channel
                    const string template =
                    "<?xml version="1.0" encoding="utf-8"?><wp:Notification " +
                    "xmlns:wp="WPNotification"><wp:Toast><wp:Text1>$(message)</wp:Text1></wp:Toast></wp:Notification>";

                    await client.GetPush()
                        .RegisterTemplateAsync(CurrentChannel.ChannelUri.ToString(), template, "mytemplate");
                });
        }

    이 코드는 Windows Phone 8.x "Silverlight"에서 사용되는 MPNS(Microsoft 푸시 알림 서비스)에서 앱의 ChannelURI를 검색한 후 해당 ChannelURI를 푸시 알림에 대해 등록합니다.

	>[AZURE.NOTE]이 자습서에서 모바일 서비스는 알림 메시지를 장치에 보냅니다. 타일 알림을 보내는 경우 채널에서 **BindToShellTile** 메서드를 대신 호출해야 합니다.

4. App.xaml.cs에서 **Application\_Launching** 이벤트 처리기의 맨 위에 다음과 같은 새 **AcquirePushChannel** 메서드 호출을 추가합니다.

        AcquirePushChannel();

	이제 페이지가 로드될 때마다 등록이 요청됩니다. 등록 상태가 유지되도록 앱에서 주기적으로 등록할 수도 있습니다.

5. **F5** 키를 눌러 앱을 실행합니다. 등록 키가 포함된 팝업 대화 상자가 표시됩니다.

6.	솔루션 탐색기에서 **속성**을 확장하고 WMAppManifest.xml 파일을 연 후 **기능** 탭을 클릭하고 **ID\_CAP\_PUSH\_NOTIFICATION** 기능이 선택되었는지 확인합니다.

   	![VS에서 알림 사용](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-app-enable-push-wp8.png)

   	이제 앱에서 알림 메시지를 표시할 수 있습니다.

###<a id="update-scripts"></a> 푸시 알림을 전송하도록 서버 스크립트 업데이트

마지막으로 TodoItem 테이블에서 삽입 작업에 등록된 스크립트를 업데이트하여 알림을 보내야 합니다.

1. [Azure 클래식 포털]에서 **데이터** 탭을 클릭한 후 **TodoItem** 테이블을 클릭합니다.

    ![][21]

2. **TodoItem**에서 **스크립트** 탭을 클릭하고 **삽입**을 선택합니다.

    ![][22]

    **TodoItem** 테이블에 삽입 시 호출되는 함수가 표시됩니다.

3. 삽입 함수를 다음의 코드로 바꾼 후 **저장**을 클릭합니다.

          function insert(item, user, request) {
          // Execute the request and send notifications.
             request.execute({
             success: function() {
              // Create a template-based payload.
              var payload = '{ "message" : "New item added: ' + item.text + '" }';

              // Write the default response and send a notification
              // to all platforms.
              push.send(null, payload, {
                  success: function(pushResponse){
                  console.log("Sent push:", pushResponse);
                  // Send the default response.
                  request.respond();
                  },
                  error: function (pushResponse) {
                      console.log("Error Sending push:", pushResponse);
                       // Send the an error response.
                      request.respond(500, { error: pushResponse });
                      }
               });
              }
           });
          }


    새 삽입 스크립트가 등록되며, 삽입 요청에 제공된 장치에 푸시 알림(삽입된 텍스트)을 보냅니다.

3. **푸시** 탭을 클릭하고 **인증되지 않은 푸시 알림을 사용하도록 설정합니다.**를 선택한 다음 **저장**을 클릭합니다.

	이렇게 하면 푸시 알림을 보낼 수 있도록 모바일 서비스가 인증되지 않은 모드에서 MPNS에 연결됩니다.

	>[AZURE.NOTE]이 자습서에서는 인증되지 않은 모드로 MPNS를 사용합니다. 이 모드에서 MPNS는 장치 채널로 전송할 수 있는 알림 수를 제한합니다. 이 제한을 제거하려면 **업로드**를 클릭하고 인증서를 선택하여 인증서를 생성 및 업로드해야 합니다. 인증서 생성에 대한 자세한 내용은 Windows Phone의 푸시 알림을 보내도록 인증된 웹 서비스 설정을 참조하세요.

###<a id="test"></a> 앱에서 푸시 알림 테스트

1. Visual Studio에서 F5 키를 눌러 앱을 실행합니다.

    >[AZURE.NOTE] Windows Phone 에뮬레이터에서 테스트하는 경우 401 Unauthorized RegistrationAuthorizationException이 발생할 수 있습니다. 이는 Windows Phone 에뮬레이터가 해당 시계를 호스트 PC와 동기화하는 방법으로 인해 `RegisterNativeAsync()`을(를) 호출하는 동안 발생할 수 있습니다. 이로 인해 보안 토큰이 거부될 수 있습니다. 이 문제를 해결하려면 테스트 전에 에뮬레이터의 시계를 수동으로 설정하면 됩니다.

5. 앱에서 **Hello push**라는 새 작업을 만든 다음 시작 단추 또는 뒤로 단추를 바로 클릭하여 앱을 종료합니다.

  	추가된 항목을 저장하기 위해 삽입 요청이 모바일 서비스로 전송됩니다. 장치가 **hello push**라는 내용의 알림 메시지를 받게 됩니다.

	![수신된 알림 메시지](./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push5-wp8.png)

	>[AZURE.NOTE]계속 앱을 열어 두면 알림이 수신되지 않습니다. 앱이 활성 상태인 동안 알림 메시지를 수신하려면 [ShellToastNotificationReceived](http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx) 이벤트를 처리해야 합니다.

<!-- Anchors. -->
[Generate the certificate signing request]: #certificates
[Register your app and enable push notifications]: #register
[Create a provisioning profile for the app]: #profile
[Configure Mobile Services]: #configure-mobileServices
[Configure the Xamarin.iOS App]: #configure-app
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test

<!-- Images. -->

[5]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step5.png
[6]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step6.png
[7]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step7.png

[9]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step9.png
[10]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step10.png

[17]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step17.png
[18]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-selection.png
[19]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios.png
[20]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-push-tab-ios-upload.png
[21]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-portal-data-tables.png
[22]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-insert-script-push2.png
[23]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push1-ios.png
[24]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push2-ios.png
[25]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push3-ios.png
[26]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-quickstart-push4-ios.png
[28]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-step18.png

[101]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-01.png
[102]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-02.png
[103]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-03.png
[104]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-04.png
[105]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-05.png
[106]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-06.png
[107]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-07.png
[108]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-08.png

[110]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-10.png
[111]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-11.png
[112]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-12.png
[113]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-13.png
[114]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-14.png
[115]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-15.png
[116]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-16.png
[117]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-17.png

[120]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-20.png
[121]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-21.png
[122]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-services-ios-push-22.png
[123]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-data-tab-empty.png
[124]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/mobile-create-todoitem-table.png
[125]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app7.png
[126]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-hub-create-android-app8.png
[127]: ./media/partner-xamarin-mobile-services-xamarin-forms-get-started-push/notification-area-received.png


[Xamarin.iOS Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584
[Get started with Mobile Services]: mobile-services-ios-get-started.md

[Xamarin 장치 프로비저닝]: http://developer.xamarin.com/guides/ios/getting_started/installation/device_provisioning/


[Azure 클래식 포털]: https://manage.windowsazure.com/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333
[Azure 모바일 서비스 구성 요소]: http://components.xamarin.com/view/azure-mobile-services/
[completed example project]: http://go.microsoft.com/fwlink/p/?LinkId=331303
[Xamarin.iOS]: http://xamarin.com/download
[Google Cloud Messaging 클라이언트 구성 요소]: http://components.xamarin.com/view/GCMClient/
[Xamarin.Forms Azure 푸시 알림 시작 샘플]: https://github.com/Azure/mobile-services-samples/tree/master/TodoListXamarinForms
[전체 Xamarin.Forms Azure 푸시 알림 샘플]: https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithPushXamarinForms

<!---HONumber=AcomDC_0211_2016-->