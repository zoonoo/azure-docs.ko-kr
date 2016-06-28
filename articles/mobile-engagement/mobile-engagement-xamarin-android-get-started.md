<properties
	pageTitle="Xamarin.Android용 Azure Mobile Engagement 시작"
	description="Xamarin.Android 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="xamarin"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-android"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="06/16/2016"
	ms.author="piyushjo" />

# Xamarin.Android 앱용 Azure Mobile Engagement 시작

[AZURE.INCLUDE [영웅 자습서 전환기](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 토픽에서는 Azure Mobile Engagement를 사용하여 앱 사용을 이해하고 Xamarin.Android 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다. 이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 해당 시나리오에서 기본 데이터를 수집하고 GCM(Google Cloud Messaging)을 사용하여 푸시 알림을 받는 빈 Xamarin.Android 앱을 만듭니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ [Xamarin Studio](http://xamarin.com/studio). Xamarin이 포함된 Visual Studio를 사용할 수도 있지만 이 자습서에서는 Xamarin Studio를 사용합니다. 또한 설치 지침은 [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)를 참조하세요.
+ [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fko-KR%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-android-get-started)을 참조하세요.

##<a id="setup-azme"></a>Android 앱용 Mobile Engagement 설정

[AZURE.INCLUDE [포털에서 Mobile Engagement 앱 만들기](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다.

여기서는 통합을 시연하기 위해 Xamarin Studio를 사용하여 기본적인 앱을 만듭니다.

###새 Xamarin.Android 프로젝트 만들기

1. **Xamarin Studio**를 시작하고 **File** -> **New** -> **Solution**으로 이동합니다. 

    ![][1]

2. **Android 앱**을 선택한 후 선택한 언어가 **C#**인지 확인하고 **다음**을 클릭합니다.

    ![][2]

3. **App Name** 및 **Organization Identifier**를 입력합니다. **Google Play Services**를 확인 표시한 후 **다음**을 클릭합니다.

    ![][3]
	
4. 필요한 경우 **Project Name**, **Solution Name** 및 **Location**을 업데이트하고 **Create**를 클릭합니다.

    ![][4]
 
Xamarin Studio가 Mobile Engagement를 통합할 앱을 만듭니다.

###Mobile Engagement 백 엔드에 앱 연결

1. 솔루션 창에서 **Packages** 폴더를 마우스 오른쪽 단추로 클릭하고 **Add Packages...**를 선택합니다.

    ![][5]

2. **Microsoft Azure Mobile Engagement Xamarin SDK**를 검색하고 이를 솔루션에 추가합니다.

    ![][6]
   
3. **MainActivity.cs**를 열고 다음 using 문을 추가합니다.

		using Microsoft.Azure.Engagement;
		using Microsoft.Azure.Engagement.Activity;

4. `OnCreate` 메서드에서 다음을 추가하여 Mobile Engagement 백 엔드 연결을 초기화합니다. **ConnectionString**을 추가해야 합니다.

		EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
		engagementConfiguration.ConnectionString = "YourConnectionStringFromAzurePortal";
		EngagementAgent.Init(engagementConfiguration);

###권한 및 서비스 선언 추가

1. 속성 폴더 아래에서 **Manifest.xml** 파일을 엽니다. XML 소스를 직접 업데이트할 수 있도록 Source 탭을 선택합니다.
 
2. 프로젝트의 Manifest.xml(**Properties** 폴더 아래에서 찾을 수 있음)에서 `<application>` 태그 바로 앞 또는 뒤에 다음 권한을 추가합니다.

		<uses-permission android:name="android.permission.INTERNET"/>
		<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"/>
		<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
		<uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
		<uses-permission android:name="android.permission.VIBRATE" />
		<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION"/>

3. `<application>` 태그와 `</application>` 태그 사이에 다음을 추가하여 에이전트 서비스를 선언합니다.

		<service
 			android:name="com.microsoft.azure.engagement.service.EngagementService"
 			android:exported="false"
 			android:label="<Your application name>"
 			android:process=":Engagement"/>

4. 방금 붙여넣은 코드의 레이블에서 `"<Your application name>"`을 바꿉니다. 이는 사용자가 장치에서 실행 중인 서비스를 확인할 수 있는 **설정** 메뉴에 표시됩니다. 예를 들어 해당 레이블에 "서비스"라는 단어를 추가할 수 있습니다.

###Mobile Engagement에 화면 보내기

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면을 하나 이상 보내야 합니다. 이 작업을 수행하려면 `MainActivity`가 `Activity` 대신 `EngagementActivity`에서 상속되어야 합니다.

	public class MainActivity : EngagementActivity
	
또는 `EngagementActivity`에서 상속할 수 없는 경우 `OnResume` 및 `OnPause`에 각각 `.StartActivity` 및 `.EndActivity` 메서드를 추가해야 합니다.

		protected override void OnResume()
	        {
	            EngagementAgent.StartActivity(EngagementAgentUtils.BuildEngagementActivityName(Java.Lang.Class.FromType(this.GetType())), null);
	            base.OnResume();             
	        }
	
	        protected override void OnPause()
	        {
	            EngagementAgent.EndActivity();
	            base.OnPause();            
	        }

##<a id="monitor"></a>실시간 모니터링과 앱 연결

[AZURE.INCLUDE [실시간 모니터링과 앱 연결](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

[AZURE.INCLUDE [Google Cloud Messaging 사용](../../includes/mobile-engagement-enable-google-cloud-messaging.md)]

[AZURE.INCLUDE [앱 내 메시징 사용](../../includes/mobile-engagement-android-send-push.md)]

[AZURE.INCLUDE [포털에서 알림 보내기](../../includes/mobile-engagement-android-send-push-from-portal.md)]

<!-- Images -->
[1]: ./media/mobile-engagement-xamarin-android-get-started/1.png
[2]: ./media/mobile-engagement-xamarin-android-get-started/2.png
[3]: ./media/mobile-engagement-xamarin-android-get-started/3.png
[4]: ./media/mobile-engagement-xamarin-android-get-started/4.png
[5]: ./media/mobile-engagement-xamarin-android-get-started/5.png
[6]: ./media/mobile-engagement-xamarin-android-get-started/6.png

<!---HONumber=AcomDC_0622_2016-->