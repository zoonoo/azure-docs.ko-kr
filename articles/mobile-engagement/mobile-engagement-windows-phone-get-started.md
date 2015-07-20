<properties
	pageTitle="Windows Phone Silverlight 앱용 Azure Mobile Engagement 시작"
	description="Windows Phone Silverlight 앱에서 분석 및 푸시 알림 기능이 포함된 Azure Mobile Engagement를 사용하는 방법에 대해 알아봅니다."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Windows Phone Silverlight 앱용 Azure Mobile Engagement 시작

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

이 항목에서는 Azure Mobile Engagement를 사용하여 Windows Phone Silverlight 응용 프로그램에서 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다. 이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 여기서는 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 받고 기본적인 데이터를 수집하는 빈 Windows Phone Silverlight 앱을 만듭니다. 이 과정을 완료하면 장치 속성(MPNS 사용)에 따라 특정 사용자를 대상으로 지정하거나 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.

> [AZURE.NOTE]Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 [Windows 범용](mobile-engagement-windows-store-dotnet-get-started.md) 자습서를 참조하세요.

이 자습서를 사용하려면 다음이 필요합니다.

+ Visual Studio 2013
+ [Mobile Engagement Windows Phone SDK]

> [AZURE.IMPORTANT]Windows Phone Silverlight 앱용 모든 기타 Mobile Engagement 자습서를 완료하려면 이 자습서를 먼저 완료해야 합니다. 그러려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a id="setup-azme"></a>Windows Phone Silverlight 앱용 Mobile Engagement 설정

1. Azure 관리 포털에 로그온한 다음 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][7]

3. 팝업이 표시되면 다음 정보를 입력합니다.

   	![][8]

	- **응용 프로그램 이름**: 응용 프로그램의 이름을 입력합니다. 원하는 모든 문자를 사용할 수 있습니다.
	- **플랫폼**: 앱의 대상 플랫폼(**Windows Phone Silverlight**)을 선택합니다. 앱의 대상 플랫폼이 여러 개이면 각 플랫폼에 대해 이 자습서의 작업을 반복합니다.
	- **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기본 URL 문자만 사용해야 합니다. 자동으로 생성되는 이름을 기준으로 사용할 수 있습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가해야 합니다.
	- **위치**: 이 앱과 해당 컬렉션을 호스트할 데이터 센터를 선택합니다.
	- **컬렉션**: 응용 프로그램을 이미 만든 경우 이전에 만든 컬렉션을 선택하고 그렇지 않으면 새 컬렉션을 선택합니다.
	- **컬렉션 이름**: 응용 프로그램 그룹을 나타냅니다. 또한 그룹을 사용하는 경우에는 모든 앱이 그룹에 포함되므로 메트릭의 집계 계산이 가능합니다. 해당하는 경우 여기에 회사 이름이나 부서를 사용해야 합니다.

4. **응용 프로그램** 탭에서 방금 만든 앱을 선택합니다.

5. **연결 정보**를 클릭하여 모바일 앱의 SDK 통합에 적용할 연결 설정을 표시합니다.

   	![][10]

6. **연결 문자열** 복사 - 응용 프로그램 코드에서 이 앱을 식별하고 Phone 앱에서 Mobile Engagement와 연결하는 데 필요합니다.

   	![][11]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement Windows Phone SDK 설명서]에서 확인할 수 있습니다.

여기서는 통합을 시연하기 위해 Visual Studio를 사용하여 기본적인 앱을 만듭니다.

###새 Windows Phone Silverlight 프로젝트 만들기

1. Visual Studio를 시작하고 홈 화면에서 **새 프로젝트...**를 선택합니다.

2. 팝업에서 `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`을(를) 선택합니다. 앱에서 `Name` 및 `Solution name`을(를) 채우고 **확인**을 클릭합니다.

   	![][13]

3. **Windows Phone 8.0** 또는 **Windows Phone 8.1** 버전을 모두 대상으로 선택할 수 있습니다.

이제 Azure Mobile Engagement SDK를 통합할 새 Windows Phone Silverlight 앱이 만들어졌습니다.

###Mobile Engagement 백 엔드에 앱 연결

1. 프로젝트에서 [Mobile Engagement Windows Phone SDK] NuGet 패키지를 설치합니다.

2. 속성 폴더 아래에 있는 `WMAppManifest.xml`을(를) `<Capabilities />` 태그에서 다음이 선언되도록 합니다(없으면 추가합니다).

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. 이제 앞서 Mobile Engagement App에서 복사한 연결 문자열을 `Resources\EngagementConfiguration.xml` 파일의 `<connectionString>` 및 `</connectionString>` 태그 사이에 붙여넣습니다.

	![][21]

4. 파일 `App.xaml.cs`에서:

	a. `using` 구문을 추가합니다.

			using Microsoft.Azure.Engagement;

	b. SDK를 `Application_Launching` 메서드에서 초기화:

			private void Application_Launching(object sender, LaunchingEventArgs e)
			{
			  EngagementAgent.Instance.Init();
			}

	c. 다음 코드를 `Application_Activated`에 삽입합니다.

			private void Application_Activated(object sender, ActivatedEventArgs e)
			{
			   EngagementAgent.Instance.OnActivated(e);
			}

##<a id="monitor"></a>실시간 모니터링 사용

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다. 여기서는 Mobile Engagement SDK에서 제공하는 `EngagementPage`을(를) 사용해서 `MainPage`의 서브클래스를 지정하는 방식으로 이 작업을 수행합니다.

1. `using` 구문 추가:

		using Microsoft.Azure.Engagement;

2. `PhoneApplicationPage` 앞에 있는 `MainPage`의 슈퍼클래스를 아래와 같이 `EngagementPage`(으)로 바꿉니다.

	![][22]

3. `MainPage.xml` 파일에서: a. 다음 줄을 네임스페이스 선언에 추가합니다.

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. xml 태그 이름의 `phone:PhoneApplicationPage`을(를) `engagement:EngagementPage`(으)로 바꿉니다.

###앱이 실시간 모니터링과 연결되어 있는지 확인

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 설명합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 **Engage** 단추를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Engagement 포털의 설정 페이지가 시작됩니다. 이 페이지에서 아래와 같이 **모니터** 탭을 클릭합니다. ![][30]

3. 모니터는 앱을 시작하는 모든 장치를 실시간으로 표시할 수 있습니다.

4. Visual Studio로 돌아가 에뮬레이터 또는 연결된 장치에서 앱을 시작합니다.

5. 앱이 정상적으로 작동하면 모니터에 세션 하나가 표시됩니다. ![][33]

**축하합니다.** 이미 데이터를 전송 중인 Mobile Engagement 백 엔드에 연결하는 앱을 사용하여 이 자습서의 첫 단계를 정상적으로 완료했습니다.

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

###MPNS 푸시 알림을 받도록 앱 설정

새 기능을 `WMAppManifest.xml` 파일에 추가합니다.

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###도달률 SDK 초기화

1. `App.xaml.cs`에서 `Application_Launching` 함수의 에이전트 초기화 바로 뒤에서 `EngagementReach.Instance.Init();`을(를) 호출합니다.

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. `App.xaml.cs`에서 `Application_Activated` 함수의 에이전트 초기화 바로 뒤에서 `EngagementReach.Instance.OnActivated(e);`을(를) 호출합니다.

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

이제 준비가 완료되었으므로 이 기본 통합이 올바르게 수행되었는지 확인하겠습니다.

##<a id="send"></a>앱에 알림 보내기

이제 앱에 푸시 알림을 보내는 간단한 푸시 알림 캠페인을 만들겠습니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.

2. **새 알림**을 클릭하여 푸시 캠페인을 만듭니다. ![][35]

3. 다음 단계를 수행하여 캠페인의 첫 번째 필드를 설정합니다. ![][36]

	1. 캠페인 이름을 원하는 이름으로 지정합니다.
	2. 앱 시작 여부에 관계없이 앱에서 알림을 받을 수 있도록 **Delivery 시간**을 *항상*으로 선택합니다.
	3. 알림 텍스트에서 푸시에 굵게 표시할 제목을 입력합니다.
	4. 그런 다음 메시지를 입력합니다.

4. 아래로 스크롤하여 콘텐츠 섹션에서 **Notification only(알림만)**를 선택합니다. ![][37]

5. 이제 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 다시 아래쪽으로 스크롤하여 **만들기**를 눌러 저장해 보세요.

6. 마지막 단계로 **활성화**를 클릭하여 캠페인을 활성화하고 푸시 알림을 보냅니다. ![][39]

7. 이제 장치에 알림이 표시됩니다. **축하합니다!**: ![][40]

<!-- URLs. -->
[Mobile Engagement Windows Phone SDK]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK 설명서]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=July15_HO2-->