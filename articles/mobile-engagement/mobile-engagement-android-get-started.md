<properties
	pageTitle="Azure Mobile Engagement 시작"
	description="Android 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="android"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="Java"
	ms.topic="hero-article"
	ms.date="12/02/2015"
	ms.author="piyushjo" />

# Android 앱용 Azure Mobile Engagement 시작

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

이 항목에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 Android 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 해당 시나리오에서 기본 데이터를 수집하고 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Android 앱을 만듭니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ Android SDK(Android Studio를 사용한다고 가정). [여기](http://go.microsoft.com/fwlink/?LinkId=389797)에서 다운로드할 수 있습니다.
+ [Mobile Engagement Android SDK]

> [AZURE.IMPORTANT]Android 앱의 다른 모든 Mobile Engagement 자습서를 사용하기 전에 이 자습서를 완료해야 하며, 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-KR%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a id="setup-azme"></a>Android 앱용 Mobile Engagement 설정

[AZURE.INCLUDE [포털에서 Mobile Engagement 앱 만들기](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 완벽한 통합 설명서는[ Mobile Engagement Android SDK 통합](../mobile-engagement-android-sdk-overview/)에서 찾아볼 수 있습니다.

이제 Android Studio로 기본 앱을 만들어 통합을 보여 주겠습니다.

###새 Android 프로젝트 만들기

1. **Android Studio**를 시작하고 팝업에서 **Start a new Android Studio project**를 선택합니다.

    ![][1]

2. 앱 이름 및 회사 도메인을 제공합니다. 나중에 사용하므로 입력한 내용을 적어둡니다. **다음**을 클릭합니다.

    ![][2]

3. 대상 폼 팩터 및 API 수준을 선택하고 **다음**을 클릭합니다.

	>[AZURE.NOTE]Mobile Engagement를 사용하려면 API 수준 10 이상(Android 2.3.3)이 필요합니다.

    ![][3]

4. 이 앱에 대한 유일한 화면인 **Blank Activity**를 여기서 선택하고 **다음**을 클릭합니다.

    ![][4]

5. 마지막으로 기본값을 그대로 두고 **마침**을 클릭합니다.

    ![][5]

그러면 이제 Android Studio가 Mobile Engagement를 통합할 데모 앱을 만듭니다.

###프로젝트에 SDK 라이브러리 포함

SDK 라이브러리를 다운로드하여 통합합니다.

1. [Mobile Engagement Android SDK]를 다운로드합니다.
2. 보관 파일을 컴퓨터의 폴더로 추출합니다.
3. 이 SDK의 최신 버전에 대한 .jar 라이브러리를 확인하고 클립보드로 복사합니다.

	  ![][6]

4. **Project** 섹션으로 이동하여(1) libs 폴더에 .jar을 붙여넣습니다(2).

	  ![][7]

5. 프로젝트를 동기화하여 라이브러리를 로드합니다.

	  ![][8]

###연결 문자열을 사용하여 Mobile Engagement 백 엔드에 앱 연결

1. 작업 생성에 다음 코드 줄을 복사합니다(응용 프로그램의 한 위치에서만 수행해야 하며, 일반적으로 기본 작업에서 수행함). 이 샘플 앱의 경우 src -> 기본 -> java 폴더에서 MainActivity를 열고 다음을 추가합니다.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.setConnectionString("Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}");
		EngagementAgent.getInstance(this).init(engagementConfiguration);

2. Alt + Enter를 누르거나 다음 import 문을 추가하여 참조를 확인합니다.

		import com.microsoft.azure.engagement.EngagementAgent;
		import com.microsoft.azure.engagement.EngagementConfiguration;

3. Azure 클래식 포털의 앱 **연결 정보** 페이지로 돌아가서 **연결 문자열**을 복사합니다.

	  ![][9]

4. `setConnectionString` 매개 변수에서 해당 문자열을 붙여넣어 아래와 같이 제공된 예제를 바꿉니다.

		engagementConfiguration.setConnectionString("Endpoint=my-company-name.device.mobileengagement.windows.net;SdkKey=********************;AppId=*********");

###권한 및 서비스 선언 추가

1. 프로젝트의 Manifest.xml에서 `<application>` 태그 바로 앞에 다음 권한을 추가합니다.

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

2. `<application>` 태그와 `</application>` 태그 사이에 다음을 추가하여 에이전트 서비스를 선언합니다.

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

3. 방금 붙여넣은 코드의 레이블에서 `"<Your application name>"`을 바꿉니다. 이는 사용자가 장치에서 실행 중인 서비스를 확인할 수 있는 **설정** 메뉴에 표시됩니다. 예를 들어 해당 레이블에 "서비스"라는 단어를 추가할 수 있습니다.

###Mobile Engagement에 화면 보내기

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

**MainActivity.java**로 이동하고 다음을 추가하여 **MainActivity**의 기본 클래스를 **EngagementActivity**로 바꿉니다.

	public class MainActivity extends EngagementActivity {

간단한 샘플 시나리오에 대해 다음 줄을 주석 처리해야 합니다.(제외)

    // setSupportActionBar(toolbar);

이를 유지하려면 [고급 Android 통합]의 "기본 보고" 시나리오를 확인해야 합니다.

##<a id="monitor"></a>실시간 모니터링과 앱 연결

[AZURE.INCLUDE [실시간 모니터링과 앱 연결](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

### 앱 내 메시징 사용

1. 아래의 앱 내 메시징 리소스를 Manifest.xml의 `<application>` 태그와 `</application>` 태그 사이에 복사합니다.

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
		<activity android:name="com.microsoft.azure.engagement.reach.activity.EngagementLoadingActivity" android:theme="@android:style/Theme.Dialog">
			<intent-filter>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.LOADING"/>
				<category android:name="android.intent.category.DEFAULT"/>
			</intent-filter>
		</activity>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachReceiver" android:exported="false">
			<intent-filter>
				<action android:name="android.intent.action.BOOT_COMPLETED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.AGENT_CREATED"/>
				<action android:name="com.microsoft.azure.engagement.intent.action.MESSAGE"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.ACTION_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.EXIT_NOTIFICATION"/>
				<action android:name="com.microsoft.azure.engagement.reach.intent.action.DOWNLOAD_TIMEOUT"/>
			</intent-filter>
		</receiver>
		<receiver android:name="com.microsoft.azure.engagement.reach.EngagementReachDownloadReceiver">
			<intent-filter>
				<action android:name="android.intent.action.DOWNLOAD_COMPLETE"/>
			</intent-filter>
		</receiver>

2. 다음 단계에 따라 리소스를 프로젝트에 복사합니다.
	1. 다시 SDK 다운로드 콘텐츠로 돌아가서 'res' 폴더를 복사합니다.

		 ![][13]

	2. Android Studio로 돌아가서 프로젝트 파일의 'main' 디렉터리를 선택하고 붙여넣어 프로젝트에 리소스를 추가합니다.

		 ![][14]

###알림 아이콘 지정

다음 Manifest.xml의 XML 코드 조각을 `<application>` 태그와 `</application>` 태그 사이에 붙여넣습니다.

		<meta-data android:name="engagement:reach:notification:icon" android:value="engagement_close"/>

시스템 및 앱 내 알림 모두에서 표시되는 아이콘을 정의합니다. 이 아이콘은 앱 내 알림에는 옵션이지만 시스템 알림에는 필수입니다. Android에서는 아이콘이 잘못된 시스템 알림을 거부합니다.

**drawable** 폴더(예: ``engagement_close.png``) 중 하나에 있는 아이콘을 사용하고 있는지 확인하세요. **mipmap** 폴더는 지원되지 않습니다.

>[AZURE.NOTE]**시작 관리자** 아이콘은 사용하면 안 됩니다. 시작 관리자는 해상도가 다르며 일반적으로 지원되지 않는 mipmap 폴더에 있습니다.

실제 앱의 경우 [Android 디자인 지침](http://developer.android.com/design/patterns/notifications.html)에 따라 알림에 적합한 아이콘을 사용할 수 있습니다.

>[AZURE.TIP]올바른 아이콘 해상도를 사용하는지 확인하려면 [해당 예제](https://www.google.com/design/icons)를 확인하세요. **알림** 섹션이 나올 때까지 아래로 스크롤하고 아이콘을 클릭한 다음 `PNGS`를 클릭하여 아이콘 drawable 집합을 다운로드합니다. 각 아이콘 버전에 사용할 drawable 폴더와 해상도를 확인할 수 있습니다.

##API 키를 가진 Google Cloud Messaging 프로젝트 만들기

[AZURE.INCLUDE [mobile-engagement-enable-Google-cloud-messaging](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

###앱이 GCM 푸시 알림을 받을 수 있도록 설정

1. Google Play 콘솔에서 가져온 `project number`을 교체한 후에 다음을 `<application>` 및 `</application>` 사이의 Manifest.xml에 붙여넣습니다. \\n은 프로젝트 번호가 \\n으로 끝나도록 의도적으로 사용되었습니다.

		<meta-data android:name="engagement:gcm:sender" android:value="************\n" />

2. 아래 코드를 Manifest.xml 파일의 `<application>` 태그와 `</application>` 태그 사이에 붙여넣습니다. 패키지 이름 <Your package name>을 바꿉니다.

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
				<category android:name="<Your package name>" />
			</intent-filter>
		</receiver>

3. `<application>` 태그 앞에 강조 표시된 마지막 권한 집합을 추가합니다. `<Your package name>`을 응용 프로그램의 실제 패키지 이름으로 바꿉니다.

		<uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
		<uses-permission android:name="<Your package name>.permission.C2D_MESSAGE" />
		<permission android:name="<Your package name>.permission.C2D_MESSAGE" android:protectionLevel="signature" />

###Mobile Engagement에 GCM API 키에 대한 액세스 권한 부여

사용자를 대신하여 Mobile Engagement에서 푸시 알림을 보내도록 허용하려면 Mobile Engagement에 API 키에 대한 액세스 권한을 부여해야 합니다. 키를 구성하고 Mobile Engagement 포털에 입력하여 이를 수행합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 클래식 포털에서 현재 이 프로젝트에 사용하고 있는 앱에 있는지 확인한 다음 아래쪽에서 **연결** 단추를 클릭합니다.

	![][15]

2. **설정** -> **네이티브 푸시** 섹션을 클릭하여 GCM 키를 입력합니다.

	![][16]

3. 아래와 같이 **GCM 설정** 섹션의 **API 키** 앞에 있는 **편집** 아이콘을 클릭합니다.

	![][17]

4. 팝업에서 전에 가져온 GCM 서버 키를 붙여넣은 다음 **확인**을 클릭합니다.

	![][18]

##<a id="send"></a>앱에 알림 보내기

이제 앱에 푸시 알림을 보내는 간단한 푸시 알림 캠페인을 만들겠습니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.

2. **새 공지**를 클릭하여 푸시 알림 캠페인을 만듭니다.

	![][20]

3. 다음 단계를 수행하여 캠페인의 첫 번째 필드를 설정합니다.

	![][21]

	a. 캠페인 이름을 지정합니다.

	b. **Delivery 형식**을 *시스템 알림 -> 심플* 로 선택: 이 알림은 제목 및 몇 줄의 텍스트를 특징으로 하는 간단한 Android 푸시 알림 유형입니다.

	c. 앱 시작 여부와 관계없이 앱에서 알림을 받을 수 있도록 **배달 시간**을 *항상* 으로 선택합니다.

	d. 알림 텍스트에서 푸시에 굵게 표시할 **제목**을 입력합니다.

	e. 그런 다음 **메시지**를 입력합니다.

4. 아래로 스크롤하여 **콘텐츠** 섹션에서 **알림만**을 선택합니다.

	![][22]

5. 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 이제 다시 아래로 스크롤하고 **만들기** 단추를 클릭하여 캠페인을 저장합니다.

6. 마지막 단계: **활성화**를 클릭하여 푸시 알림을 보내기 위해 캠페인을 활성화합니다.

	![][24]

<!-- URLs. -->
[Mobile Engagement Android SDK]: https://aka.ms/vq9mfn
[Mobile Engagement Android SDK documentation]: https://aka.ms/tujlkm
[고급 Android 통합]: https://azure.microsoft.com/ko-KR/documentation/articles/mobile-engagement-android-integrate-engagement/#basic-reporting

<!-- Images. -->
[1]: ./media/mobile-engagement-android-get-started/android-studio-new-project.png
[2]: ./media/mobile-engagement-android-get-started/android-studio-project-props.png
[3]: ./media/mobile-engagement-android-get-started/android-studio-project-props2.png
[4]: ./media/mobile-engagement-android-get-started/android-studio-add-activity.png
[5]: ./media/mobile-engagement-android-get-started/android-studio-activity-name.png
[6]: ./media/mobile-engagement-android-get-started/sdk-content.png
[7]: ./media/mobile-engagement-android-get-started/paste-jar.png
[8]: ./media/mobile-engagement-android-get-started/sync-project.png
[9]: ./media/mobile-engagement-android-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-android-get-started/copy-resources.png
[14]: ./media/mobile-engagement-android-get-started/paste-resources.png
[15]: ./media/mobile-engagement-android-get-started/engage-button.png
[16]: ./media/mobile-engagement-android-get-started/engagement-portal.png
[17]: ./media/mobile-engagement-android-get-started/native-push-settings.png
[18]: ./media/mobile-engagement-android-get-started/api-key.png
[20]: ./media/mobile-engagement-android-get-started/new-announcement.png
[21]: ./media/mobile-engagement-android-get-started/campaign-first-params.png
[22]: ./media/mobile-engagement-android-get-started/campaign-content.png
[24]: ./media/mobile-engagement-android-get-started/campaign-activate.png

<!---HONumber=AcomDC_0114_2016-->