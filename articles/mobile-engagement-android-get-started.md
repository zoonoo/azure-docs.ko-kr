<properties 
	pageTitle="Azure Mobile Engagement 시작" 
	description="분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다." 					services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Mobile Engagement 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store">Windows 스토어 </a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone </a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS </a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android" class="current">Android</a></div>

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 Android 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 
이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 해당 시나리오에서 기본 데이터를 수집하고 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다. 이 시나리오를 완료하면 모든 장치 또는 해당 장치 속성에 따라 특정 대상 사용자에게 푸시 알림을 브로드캐스트할 수 있습니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.


이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ Android SDK(Android Studio를 사용한다고 가정). [여기]에서 다운로드할 수 있습니다.(http://go.microsoft.com/fwlink/?LinkId=389797)
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT] Android 앱의 다른 모든 Mobile Engagement 자습서를 사용하기 전에 이 자습서를 완료해야 하며, 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

<!--
##<a id="register"></a>Enable Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

You will use your GCM API key later when setting up your app for Mobile Engagement.
-->

##<a id="setup-azme"></a>앱에 대해 Mobile Engagement 설정

1. [Azure 관리 포털]에 로그온하여 화면 하단의 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][7]

3. 표시되는 팝업에서 다음 정보를 입력합니다.
 
   	![][8]

	1. **응용 프로그램 이름**: 응용 프로그램의 이름을 입력할 수 있습니다. 어떤 문자라도 사용할 수 있습니다.
	2. **플랫폼**: 해당 앱의 대상 플랫폼을 선택합니다. 앱이 여러 플랫폼을 대상으로 하는 경우 각 플랫폼에 대해 이 자습서를 반복합니다.
	3. **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기준 URL 문자를 사용하는 것이 좋으며, 자동 생성된 이름을 기본으로 사용하는 것이 좋습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가하는 것도 좋습니다.
	4. **위치**: 이 앱(및 해당 컬렉션 - 아래 참조)을 호스트할 데이터 센터를 선택합니다.
	5. **컬렉션**: 응용 프로그램을 이미 만든 경우 이전에 만든 컬렉션을 선택하고, 그렇지 않은 경우 새 컬렉션을 선택합니다.
	6. **컬렉션 이름**: 응용 프로그램의 그룹을 나타냅니다. 또한 모든 앱이 집계된 계산을 허용하는 그룹 내에 있다는 것을 보장합니다. 이 이름으로 회사 이름 또는 부서를 사용하는 것이 좋습니다.


	완료하면 확인 단추를 클릭하여 앱 생성을 완료합니다.

4. 이제 **응용 프로그램** 탭에서 방금 만든 앱을 클릭/선택합니다.
 
   	![][9]

5. 그런 다음 연결 설정을 표시하여 SDK 통합에 넣으려면 **연결 정보**를 클릭합니다.
 
   	![][10]

6. 마지막으로 **연결 문자열**을 기록해 둡니다. 이 문자열은 응용 프로그램 코드에서 이 앱을 식별하는 데 필요합니다.

   	![][11]

	>[AZURE.TIP] 연결 문자열의 오른쪽에 있는 "복사" 아이콘을 사용하면 편리하게 클립보드에 복사할 수 있습니다.

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 전송하는 데 필요한 최소 설정인 "기본 통합"을 보여 줍니다. 완벽한 통합 설명서는 [Mobile Engagement Android SDK 설명서]에서 찾아볼 수 있습니다.

이제 Android Studio로 기본 앱을 만들어 통합을 보여 주겠습니다.

###새 Android 프로젝트 만들기

이미 앱이 있으며 Android 개발에 익숙한 경우 이 단계를 건너뛸 수 있습니다.

1. Android Studio를 시작하고, 팝업에서 **Start a new Android Studio project**를 선택합니다.

   	![][12]

2. 앱 이름 및 회사 도메인을 입력합니다. 나중에 필요하므로 이 정보를 기록해둔 후 **Next**를 클릭합니다.

   	![][13]

3. 그런 다음 대상 폼 팩터 및 API 수준을 선택한 후 **Next**를 클릭합니다. 

	>[AZURE.NOTE] Mobile Engagement를 사용하려면 API 수준 10 이상(Android 2.3.3)이 필요합니다.

   	![][14]

4. 이제 간단한 앱에 주 화면이자 유일한 화면이 될 작업을 추가하겠습니다. **Blank Activity**를 선택하고 **Next**를 클릭합니다.

   	![][15]

5. 이 자습서에서는 마법사의 마지막 화면에서 모든 항목을 그대로 둘 수 있으며, **Finish**를 클릭합니다.

   	![][16]

그러면 Android Studio가 Mobile Engagement를 통합할 데모 앱을 만듭니다.

###프로젝트에 SDK 라이브러리 포함

SDK 라이브러리 다운로드 및 통합

1. [Mobile Engagement Android SDK]를 다운로드합니다.
2. 보관 파일을 컴퓨터의 폴더로 추출합니다.
3. 이 SDK의 최신 버전에 대한 .jar 라이브러리를 확인하고(이 설명서는 3.0.0 버전용으로 작성되었음) 클립보드로 복사합니다.

	![][17]

4. 프로젝트 섹션으로 이동하여(1) libs 폴더에 .jar을 붙여넣습니다(2).

	![][18]

5. 프로젝트를 동기화하여 라이브러리를 로드합니다.

	![][19]


###연결 문자열로 Mobile Engagement 백 엔드에 앱 연결

1. 작업 생성에 다음 코드 줄을 복사합니다(응용 프로그램의 한 위치에서만 수행해야 하며, 일반적으로 기본 작업에서 수행함).

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Azure 포털로 이동하여 앱의 **연결 정보** 페이지로 돌아가 **연결 문자열**을 복사합니다.

	![][11]

3. `setConnectionString` 매개 변수에서 해당 문자열을 붙여넣어 아래와 같이 제공된 예제를 바꿉니다(아래에서 AppId 및 Sdkkey는 숨겨짐).

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

4. EngagementConfiguration 및 EngagementAgent는 확인되지 않음으로 표시될 가능성이 높습니다(코드에서 빨간색으로 표시됨). 확인되지 않은 각 클래스를 클릭하고 Alt + Enter를 누르면 자동으로 확인됩니다.

	![][20]

###권한 및 서비스 선언 추가

1. 프로젝트의 Manifest.xml에서 `<application>` 태그 바로 앞에 다음 권한을 추가합니다.
	
		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>


2. < application > 태그와 </application > 태그 사이에 다음을 추가하여 에이전트 서비스를 선언합니다.

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>Service"
 			android:process=":Engagement"/>

3. 방금 붙여넣은 코드에서 label의 "<Your application name>"을 바꿉니다. 예를 들면 다음과 같습니다.

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="MySuperAppService"
 			android:process=":Engagement"/>

###Mobile Engagement에 화면 보내기

데이터를 보내고 사용자가 활성인지 확인하려면 Mobile Engagement 백 엔드에 하나 이상의 화면(작업)을 보내야 합니다. SDK에서 제공하는 EngagementActivity와 함께 작업의 하위 클래스를 지정하여 이 작업을 수행할 수 있습니다.
그렇게 하려면 아래와 같이 ActionBarActivity 앞에 있는 MainActivity의 상위 클래스를 EngagementActivity로 바꿉니다.

![][22]

>[AZURE.NOTE] 클래스가 빨간색으로 표시되는 경우 잊지 말고 해당 클래스를 클릭하고 Alt + Enter를 눌러 확인합니다.

##<a id="monitor"></a>앱이 실시간 모니터링과 연결되어 있는지 확인하는 방법

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 보여 줍니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 **Engage** 단추를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Mobile Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 아래와 같이 **모니터** 탭을 클릭합니다.
	![][30]

3. 모니터는 앱을 시작할 장치를 실시간으로 표시할 준비가 되어 있습니다.
	![][31]

4. Android Studio로 돌아가 녹색 삼각형을 클릭한 후 장치를 선택하여 모니터 또는 연결된 장치에서 앱을 시작합니다.
	![][32]

5. 앱이 작동하는 경우 이제 모니터에서 한 세션을 볼 수 있습니다! 
	![][33]

**축하합니다.** 이 자습서의 첫 번째 단계를 완료하였습니다. 앱을 Mobile Engagement 백 엔드에 연결하고 이미 데이터를 보냈습니다.


##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement를 통해 캠페인의 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 도달할 수 있습니다. 이 모듈을 Mobile Engagement 포털에서 도달률이라고 합니다.
다음 섹션에서는 알림 또는 메시지를 수신하도록 앱을 설정합니다.

### 앱 내 메시징 사용

1. 아래의 앱 내 메시징 리소스를 Manifest.xml 파일의 < application > 태그와 </application > 태그 사이에 복사합니다.

		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementTextAnnouncementActivity" android:theme="@android:style/Theme.Light">
  			<intent-filter>
    			<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
    			<category android:name="android.intent.category.DEFAULT" />
    			<data android:mimeType="text/plain" />
  			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementWebAnnouncementActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ANNOUNCEMENT"/>
				<category android:name="android.intent.category.DEFAULT" />
				<data android:mimeType="text/html" />
			</intent-filter>
		</activity>
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementPollActivity" android:theme="@android:style/Theme.Light">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.POLL"/>
				<category android:name="android.intent.category.DEFAULT" />
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>

2. 다음 단계에 따라 리소스를 프로젝트에 복사합니다.
	1. SDK 다운로드 콘텐츠로 돌아가  'res' 폴더를 엽니다.
	2. 2개의 폴더를 선택하고 클립보드에 복사합니다.

		![][23]

	4. Android Studio로 돌아가 프로젝트의  'res' 부분을 선택하고 프로젝트에 리소스를 붙여넣어 추가합니다.

		![][24]

###알림의 기본 아이콘 지정
다음 코드는 알림과 함께 표시될 기본 아이콘을 정의합니다. 여기에서는 Android Studio에서 생성한 프로젝트와 함께 제공되는 아이콘을 사용했습니다. 이 xml 조각을 Manifest.xml 파일의 < application > 태그와 </application > 태그 사이에 붙여넣을 수 있습니다.

		<meta-data android:name="engagement:reach:notification:icon" android:value="ic_launcher" />

###앱이 GCM 푸시 알림을 받을 수 있도록 설정

1. 다음을 복사하여 Manifest.xml 파일의 < application > 태그와 </application > 태그 사이에 붙여넣어 gcm:sender 메타데이터를 입력합니다. 아래에서 별표로 숨겨진 값은 Google Play 콘솔에서 가져온  `project number`입니다.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 아래 코드를 Manifest.xml 파일의 < application > 태그와 </application > 태그 사이에 붙여넣습니다. `<category android:name="com.mycompany.mysuperapp" />`에서는 프로젝트의 패키지 이름을 사용했습니다. 고유한 프로덕션 프로젝트에서는 달라질 수 있습니다.

		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMEnabler"
		android:exported="false">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.intent.action.APPID_GOT" />
			</intent-filter>
		</receiver>
		
		<receiver android:name="com.microsoft.azure.engagement.gcm.EngagementGCMReceiver" android:permission="com.google.android.c2dm.permission.SEND">
			<intent-filter>
				<action android:name="com.google.android.c2dm.intent.REGISTRATION" />
				<action android:name="com.google.android.c2dm.intent.RECEIVE" />
				<category android:name="com.mycompany.mysuperapp" />
			</intent-filter>
		</receiver>

3. 아래에 강조 표시된 마지막 권한 집합을 < application> 태그 앞에 추가합니다. 여기에서 사용한 이 프로젝트 패키지 이름도 고유한 프로덕션 앱에서는 바꾸어야 합니다.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" />
		<permission android:name="com.mycompany.mysuperapp.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Mobile Engagement에 GCM API 키에 대한 액세스 권한 부여

사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Mobile Engagement에 API 키에 대한 액세스 권한을 부여해야 합니다. 키를 구성하고 Mobile Engagement 포털에 입력하여 이를 수행합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 **Engage** 단추를 클릭합니다.

	![][26]

2. 그러면 Mobile Engagement 포털의 설정 페이지가 표시됩니다. 여기에서 다음과 같이 **네이티브 푸시** 섹션을 클릭하여 GCM 키를 입력합니다.
	![][27]

3. 아래와 같이 GCM 설정 섹션의 **API 키** 앞에 있는 편집 아이콘을 클릭합니다.
	![][28]

4. 팝업에서, [Google Cloud Messaging 사용] 섹션에서 가져온 GCM 서버 키를 붙여넣은 후(#register) **확인**을 클릭합니다.

	![][29]

모든 설정을 완료하였으며 이제 이 기본 통합을 제대로 수행했는지 확인합니다.

> [AZURE.IMPORTANT] 빌드했는지 확인하고 이 새 코드로 시작하고 앱을 종료한 후 다음을 수행하기 전에 약 1분 정도 기다립니다.

##<a id="send"></a>앱에 알림을 보내는 방법

이제 앱에 푸시 알림을 보낼 간단한 푸시 알림 캠페인을 만듭니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.
	![][34]

2. **새 알림**을 클릭하여 푸시 캠페인을 만듭니다.
	![][35]

3. 다음 단계에 따라 캠페인의 첫 번째 필드를 설정합니다.
	![][36]

	1. 원하는 이름으로 캠페인의 이름을 지정합니다.
	2. **배달 유형**을  *System notification / Simple*로 선택합니다. 이 알림은 제목 및 몇 줄의 텍스트를 특징으로 하는 간단한 Android 푸시 알림 유형입니다.
	3. **배달 시간**을  *Any time*으로 선택하여 앱이 시작되었는지 여부와 관계없이 알림을 받을 수 있도록 합니다.
	4. 알림 텍스트에서 제목을 입력합니다. 제목은 푸시에서 굵게 표시됩니다.
	5. 그런 다음 메시지를 입력합니다.

4. 아래로 스크롤하여 콘텐츠 섹션에서 **알림만**을 선택합니다.
	![][37]

5. 가능한 대부분의 기본 캠페인을 설정했습니다. 이제 다시 아래로 스크롤하여 캠페인을 만들어 저장합니다!
![][38]

6. 마지막 단계로 캠페인을 활성화합니다.
![][39]


<!-- URLs. -->
[Mobile Engagement Android SDK]: http://go.microsoft.com/?linkid=9863935
[Mobile Engagement Android SDK 설명서]: http://go.microsoft.com/?linkid=9874682
[Azure 관리 포털]: https://manage.windowsazure.com

<!-- Images. -->
[7]: ./media/mobile-engagement-android-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-android-get-started/create-azme-popup.png
[9]: ./media/mobile-engagement-android-get-started/select-app.png
[10]: ./media/mobile-engagement-android-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[13]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[14]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[15]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[16]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[17]: ./media/mobile-engagement-android-get-started/sdk-content.png
[18]: ./media/mobile-engagement-android-get-started/paste-jar.png
[19]: ./media/mobile-engagement-android-get-started/sync-project.png
[20]: ./media/mobile-engagement-android-get-started/resolve-classes.png
[22]: ./media/mobile-engagement-android-get-started/subclass-activity.png
[23]: ./media/mobile-engagement-android-get-started/copy-resources.png
[24]: ./media/mobile-engagement-android-get-started/paste-resources.png
[26]: ./media/mobile-engagement-android-get-started/engage-button.png
[27]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[28]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-android-get-started/api-key.png
[30]: ./media/mobile-engagement-android-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-android-get-started/monitor.png
[32]: ./media/mobile-engagement-android-get-started/launch.png
[33]: ./media/mobile-engagement-android-get-started/monitor-trafic.png
[34]: ./media/mobile-engagement-android-get-started/reach-tab.png
[35]: ./media/mobile-engagement-android-get-started/new-announcement.png
[36]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-android-get-started/campaign-content.png
[38]: ./media/mobile-engagement-android-get-started/campaign-create.png
[39]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!--HONumber=47-->
