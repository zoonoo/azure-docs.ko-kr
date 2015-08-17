<properties
	pageTitle="Cordova/Phonegap용 Azure Mobile Engagement 시작"
	description="Cordova/Phonegap 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-phonegap"
	ms.devlang="js"
	ms.topic="article" 
	ms.date="07/02/2015"
	ms.author="piyushjo" />

# Cordova/Phonegap용 Azure Mobile Engagement 시작

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

이 항목에서는 Azure Mobile Engagement를 사용하여 Cordova에서 개발된 모바일 응용 프로그램의 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다.

이 자습서에서는 Mac을 사용하여 빈 Cordova 앱을 만들고 Mobile Engagement SDK를 통합합니다. 기본 분석 데이터를 수집하고 iOS용 APNS(Apple 푸시 알림 시스템) 및 Android용 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받습니다. 테스트를 위해 iOS 또는 Android 장치에 이 앱을 배포합니다.

> [AZURE.IMPORTANT]이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

이 자습서를 사용하려면 다음이 필요합니다.

+ Mac 앱 스토어에서 설치할 수 있는 Xcode(iOS 배포용)
+ [Android SDK 및 에뮬레이터](http://developer.android.com/sdk/installing/index.html)(Android 배포용)
+ APNS을 위해 Apple 개발자 센터에서 가져올 수 있는 푸시 알림 인증서(.p12)
+ GCM을 위해 Google 개발자 콘솔에서 가져올 수 있는 GCM 프로젝트 번호
+ [Mobile Engagement Cordova 플러그 인](https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-engagement)

> [AZURE.NOTE][Github](https://github.com/Azure/azure-mobile-engagement-cordova)에서 Cordova 플러그 인에 대한 소스 코드와 추가 정보를 찾을 수 있습니다.

##<a id="setup-azme"></a>앱용 Mobile Engagement 설정

1. Azure 관리 포털에 로그온한 다음 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][1]

3. 팝업이 표시되면 다음 정보를 입력합니다.

   	![][2]

	- **응용 프로그램 이름**: 응용 프로그램의 이름입니다. 
	- **플랫폼**: 앱의 대상 플랫폼입니다(Cordova 앱을 배포하는 위치에 따라 **iOS** 또는 **Android** 선택).
	- **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 
	- **위치**: 이 앱과 앱 컬렉션을 호스트할 데이터 센터입니다.
	- **컬렉션**: 이전에 만든 컬렉션을 선택하거나 ‘새 컬렉션’을 선택합니다.
	- **컬렉션 이름**: 응용 프로그램 그룹을 나타냅니다. 또한 그룹을 사용하는 경우에는 모든 앱이 그룹에 포함되므로 메트릭의 집계 계산이 가능합니다. 해당하는 경우 여기에 회사 이름이나 부서를 사용해야 합니다.

4. **응용 프로그램** 탭에서 방금 만든 앱을 선택합니다.

5. **연결 정보**를 클릭하여 모바일 앱의 SDK 통합에 적용할 연결 설정을 표시합니다.

   	![][3]

6. **연결 문자열** 복사 - 응용 프로그램 코드에서 이 앱을 식별하고 Phone 앱에서 Mobile Engagement와 연결하는 데 필요합니다.

   	![][4]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다.

여기서는 통합을 시연하기 위해 Cordova를 사용하여 기본 앱을 만듭니다.

###새 Cordova 프로젝트 만들기

1. Mac 컴퓨터에서 *Terminal* 창을 시작하고 기본 템플릿에서 새 Cordova 프로젝트를 만들 다음을 입력합니다.

		$ cordova create azme-cordova com.mycompany.myapp
		$ cd azme-cordova

> [AZURE.IMPORTANT]iOS 앱을 배포하는 데 사용할 최종 게시 프로필이 앱 ID로 'com.mycompany.myapp'을 사용하는지 확인합니다.

2. 다음을 실행하여 **iOS**에 대한 프로젝트를 구성하고 iOS 시뮬레이터에서 실행합니다.

		$ cordova platform add ios 
		$ cordova run ios

3. 다음을 실행하여 **Android**에 대한 프로젝트를 구성하고 Android 시뮬레이터에서 실행합니다.

		$ cordova platform add android
		$ cordova run android

4. 	Cordova 콘솔 플러그 인을 추가합니다.

		$ cordova plugin add cordova-plugin-console 

###Mobile Engagement 백 엔드에 앱 연결

1. 변수 값을 제공하여 플러그 인을 구성하는 동안 Azure Mobile Engagement Cordova 플러그 인을 설치합니다.

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement    
			--variable AZME_IOS_COLLECTION=<iOSAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=... 
	        --variable AZME_IOS_APPID=... 
	        --variable AZME_IOS_REACH_ICON=... (icon name WITH extension) 
	        --variable AZME_ANDROID_COLLECTION=<AndroidAppCollectionName>.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=...
	        --variable AZME_ANDROID_APPID=...
			--variable AZME_ANDROID_REACH_ICON=... (icon name WITHOUT extension)       
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=... (From your Google Cloud console for sending push notifications) 
	        --variable AZME_REDIRECT_URL=... (URL scheme which triggers the app for deep linking)
	        --variable AZME_ENABLE_LOG=true|false

	예:

		cordova plugin add cordova-plugin-ms-azure-mobile-engagement   
			--variable AZME_IOS_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_IOS_SDKKEY=26dxxxxxxxxxxxxb794 
	        --variable AZME_IOS_APPID=piyxxxxxx
	        --variable AZME_IOS_REACH_ICON=icon.png 
			--variable AZME_ANDROID_COLLECTION=apps-Collection.device.mobileengagement.windows.net
	        --variable AZME_ANDROID_SDKKEY=c3d296xxxxxxxxxxc6540
	        --variable AZME_ANDROID_APPID=piyxxxxxxx
			--variable AZME_ANDROID_REACH_ICON=icon   
	        --variable AZME_ANDROID_GOOGLE_PROJECT_NUMBER=393xxxxxxx718
			--variable AZME_REDIRECT_URL=myapp 
			--variable AZME_ENABLE_LOG=true

> [AZURE.TIP]AppId, SDKKey 및 Collection은 연결 문자열 **Endpoint={YourAppCollection.Domain};SdkKey={YourSDKKey};AppId={YourAppId}**에서 검색할 수 있습니다.

##<a id="monitor"></a>실시간 모니터링 사용

1. Cordova 프로젝트에서 **www/js/index.js**를 편집하여 Mobile Engagement에 대한 호출을 추가하고 *deviceReady* 이벤트가 수신된 후 새 활동을 선언합니다.

		 onDeviceReady: function() {
		        app.receivedEvent('deviceready');
		        AzureEngagement.startActivity("myPage",{});
		    }

2. 응용 프로그램을 실행합니다.
		
	- **iOS의 경우**
	
		`Terminal` 창에서는 다음을 실행하여 앱을 새 시뮬레이터 인스턴스에서 시작합니다.

			cordova run ios

	- **Android의 경우**
		
		`Terminal` 창에서는 다음을 실행하여 앱을 새 시뮬레이터 인스턴스에서 시작합니다.

			cordova run android

3. 콘솔 로그에서 다음을 확인할 수 있습니다.

		[Engagement] Agent: Session started
		[Engagement] Agent: Activity 'myPage' started
		[Engagement] Connection: Established
		[Engagement] Connection: Sent: appInfo
		[Engagement] Connection: Sent: startSession
		[Engagement] Connection: Sent: activity name='myPage'

###앱이 실시간 모니터링과 연결되어 있는지 확인

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 설명합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트용으로 사용 중인 앱이 열려 있는지 확인하고 아래쪽에서 **참여**를 클릭합니다.

	![][6]

2. 그러면 앱에 대한 **Engagement 포털**의 설정 페이지가 표시됩니다. 이 페이지에서 아래와 같이 **모니터** 탭을 클릭합니다.

	![][7]

3. 에뮬레이터에서 실행 중인 앱이 올바르게 구성되어 있으면 앱이 실행되는 동안 실시간 로그인 세션이 표시됩니다.

	![][8]

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용할 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

###Mobile Engagement에 대한 푸시 자격 증명 구성

사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Apple iOS 인증서 또는 GCM 서버 API 키에 대한 액세스 권한을 Mobile Engagement에 부여해야 합니다.
	
1. Mobile Engagement 포털로 이동합니다. 이 프로젝트용으로 사용 중인 앱이 열려 있는지 확인하고 맨 아래에서 **참여** 단추를 클릭합니다.
	
	![][10]
	
2. 그러면 Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 다음과 같이 **네이티브 푸시** 섹션을 클릭합니다.
	
	![][11]

3. iOS 인증서/GCM 서버 API 키 구성

	**[iOS]**

	a. 다음과 같이 .p12를 선택하여 업로드하고 암호를 입력합니다.
	
	![][12]

	**[Android]**

	a. 아래와 같이 GCM 설정 섹션의 **API 키** 앞에 있는 편집 아이콘을 클릭합니다.
		
	![][20]
	
	b. 팝업에서 GCM 서버 키를 붙여넣고 **확인**을 클릭합니다.
	
	![][21]

###Cordova 앱에서 푸시 알림을 사용하도록 설정

**www/js/index.js**를 편집하여 푸시 알림을 요청하고 처리기를 선언하기 위한 Mobile Engagement에 대한 호출을 추가합니다.

	 onDeviceReady: function() {
	        app.receivedEvent('deviceready');
	        AzureEngagement.registerForPushNotification();
	        AzureEngagement.onOpenURL(function(_url) { alert(_url); });
	        AzureEngagement.startActivity("myPage",{});
	    }

###앱 실행

**[iOS]**

1. iOS에서는 실제 장치에 대한 푸시 알림만 허용하므로 XCode를 사용하여 푸시 알림을 테스트할 장치에서 앱을 빌드하고 배포합니다. Cordova 프로젝트가 생성된 위치로 이동하고 **...\\platforms\\ios** 위치로 이동합니다. XCode에서 네이티브 .xcodeproj 파일을 엽니다. 
	
2. 방금 Mobile Engagement 포털에 업로드한 인증서가 포함된 프로비저닝 프로필이 있는 계정과 Cordova 앱을 만들 때 제공한 것과 일치하는 앱 ID를 사용하여 Cordova 앱을 빌드하고 iOS 장치에 배포합니다. XCode의 **Resources*-info.plist** 파일에서 *번들 식별자*가 일치하는지 확인할 수 있습니다.

3. 앱에서 알림을 보낼 권한을 요청함을 나타내는 표준 iOS 팝업이 장치에 표시됩니다. 권한을 부여합니다.

**[Android]**

GCM 알림은 Android 에뮬레이터에서 지원되므로 에뮬레이터를 사용하여 Android 앱을 실행할 수 있습니다.

	cordova run android

##<a id="send"></a>앱에 알림 보내기

이제 장치에서 실행 중인 앱에 푸시를 보낼 간단한 푸시 알림 캠페인을 만듭니다.

1. Mobile Engagement 포털에서 도달률 탭으로 이동합니다.

2. **새 알림**을 클릭하여 푸시 캠페인을 만듭니다.

	![][13]

3. 입력을 제공하여 캠페인을 만듭니다.

	![][14]

	- 	캠페인 이름을 제공합니다. 
	- 	**[Android]** **배달 유형**을 *시스템 알림* - *단순*으로 선택합니다.
	- 	배달 시간을 다음으로 선택합니다. 
		- 	**iOS**: *"앱 외부에서만"*
		- 	**Android**: *"항상"*
		
		이는 일부 텍스트를 제공하는 간단한 푸시 알림 유형입니다.
	- 	알림 텍스트에서 먼저 푸시의 첫 줄에 표시될 제목을 입력합니다.
	- 	그런 다음 두 번째 줄이 될 메시지를 입력합니다.

4. 아래로 스크롤하여 콘텐츠 섹션에서 **알림만**을 선택합니다.

	![][15]

5. [선택 사항] 작업 URL을 제공할 수도 있습니다. 플러그 인의 **AZME REDIRECT URL** 변수를 구성하는 동안 제공된 URL 체계가 사용되는지 확인합니다(예: **myapp://test*).

5. 이제 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 다시 아래쪽으로 스크롤하여 캠페인을 **만든** 다음 저장해 보세요.
	
	![][16]

6. 마지막으로 캠페인을 **활성화**합니다.
	
	![][17]

7. 이제 이 캠페인 부분으로 장치나 에뮬레이터에 푸시 알림이 표시되어야 합니다.

##<a id="next-steps"></a>다음 단계
[Cordova Mobile Engagement SDK에서 사용 가능한 모든 방법의 개요](https://github.com/Azure/azure-mobile-engagement-cordova)

<!-- URLs. -->
[Mobile Engagement iOS SDK]: http://go.microsoft.com/?linkid=9864553

<!-- Images. -->
[1]: ./media/mobile-engagement-cordova-get-started/create-mobile-engagement-app.png
[2]: ./media/mobile-engagement-cordova-get-started/create-azme-popup.png
[3]: ./media/mobile-engagement-cordova-get-started/app-main-page-select-connection-info.png
[4]: ./media/mobile-engagement-cordova-get-started/app-connection-info-page.png
[6]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[7]: ./media/mobile-engagement-cordova-get-started/clic-monitor-tab.png
[8]: ./media/mobile-engagement-cordova-get-started/monitor.png
[10]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[11]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[12]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[13]: ./media/mobile-engagement-cordova-get-started/new-announcement.png
[14]: ./media/mobile-engagement-cordova-get-started/campaign-first-params.png
[15]: ./media/mobile-engagement-cordova-get-started/campaign-content.png
[16]: ./media/mobile-engagement-cordova-get-started/campaign-create.png
[17]: ./media/mobile-engagement-cordova-get-started/campaign-activate.png
[18]: ./media/mobile-engagement-cordova-get-started/engage-button.png
[19]: ./media/mobile-engagement-cordova-get-started/engagement-portal.png
[20]: ./media/mobile-engagement-cordova-get-started/native-push-settings.png
[21]: ./media/mobile-engagement-cordova-get-started/api-key.png

<!---HONumber=August15_HO6-->