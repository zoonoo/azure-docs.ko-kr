<properties
	pageTitle="Windows 유니버설 앱용 Azure Mobile Engagement 시작"
	description="Windows 유니버설 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Windows 유니버설 앱용 Azure Mobile Engagement 시작

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

이 항목에서는 Azure Mobile Engagement를 사용하여 Windows 유니버설 응용 프로그램에서 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다. 이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. WNS(Windows 알림 서비스)를 사용하여 푸시 알림을 받고 기본적인 앱 사용 데이터를 수집하는 빈 Windows 유니버설 앱을 만듭니다. 이 과정을 완료하면 장치 속성에 따라 특정 사용자를 대상으로 지정하거나 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.

이 자습서를 사용하려면 다음이 필요합니다.

+ Visual Studio 2013
+ [Mobile Engagement Windows 유니버설 SDK]

> [AZURE.IMPORTANT]이 자습서를 완료해야 다른 모든 Windows 유니버설 앱용 Mobile Engagement 자습서를 진행할 수 있습니다. 완료하려면 - 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a id="setup-azme"></a>Windows 유니버설 앱용 Mobile Engagement 설정

1. Azure 관리 포털에 로그온한 다음 화면 맨 아래에 있는 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][7]

3. 팝업이 표시되면 다음 정보를 입력합니다.

   	![][8]

	- **응용 프로그램 이름**: 응용 프로그램의 이름을 입력합니다. 원하는 모든 문자를 사용할 수 있습니다.
	- **플랫폼**: 앱의 대상 플랫폼(**Windows 유니버설**)을 선택합니다. 앱의 대상 플랫폼이 여러 개이면 각 플랫폼에 대해 이 자습서의 작업을 반복합니다.
	- **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기본 URL 문자만 사용해야 합니다. 자동으로 생성되는 이름을 기준으로 사용할 수 있습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가해야 합니다.
	- **위치**: 이 앱과 해당 컬렉션을 호스트할 데이터 센터를 선택합니다.
	- **컬렉션**: 응용 프로그램을 이미 만든 경우 이전에 만든 컬렉션을 선택하고 그렇지 않으면 새 컬렉션을 선택합니다.
	- **컬렉션 이름**: 응용 프로그램 그룹을 나타냅니다. 또한 그룹을 사용하는 경우에는 모든 앱이 그룹에 포함되므로 메트릭의 집계 계산이 가능합니다. 해당하는 경우 여기에 회사 이름이나 부서를 사용해야 합니다.

	> [AZURE.TIP]유니버설 앱이 Windows와 Windows Phone 플랫폼을 모두 대상으로 하는 경우 여전히 지원하는 각 플랫폼에 하나씩 두 개의 Mobile Engagement 응용 프로그램을 만들어야 합니다. 그러면 대상에 적절한 구분을 생성할 수 있고 각 플랫폼을 대상으로 하는 적절한 알림을 보낼 수 있습니다.

4. **응용 프로그램** 탭에서 방금 만든 앱을 선택합니다.

5. **연결 정보**를 클릭하여 모바일 앱의 SDK 통합에 적용할 연결 설정을 표시합니다.

   	![][10]

6. **연결 문자열** 복사 - 응용 프로그램 코드에서 이 앱을 식별하고 유니버설 앱에서 Mobile Engagement와 연결하는 데 필요합니다.

   	![][11]

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement Windows 유니버설 SDK 설명서]에서 확인할 수 있습니다.

여기서는 통합을 시연하기 위해 Visual Studio를 사용하여 기본적인 앱을 만듭니다.

###새 Windows 유니버설 앱 프로젝트 만들기

이미 앱이 있으며 Windows 유니버설 개발에 대해 잘 알고 있으면 이 단계를 건너뛰어도 됩니다.

1. Visual Studio를 시작하고 홈 화면에서 **새 프로젝트...**를 선택합니다.

2. 팝업에서 `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`을(를) 선택합니다. 앱에서 `Name` 및 `Solution name`을(를) 채우고 **확인**을 클릭합니다.

   	![][13]

이제 Azure Mobile Engagement SDK를 통합할 새 Windows 유니버설 앱 프로젝트가 만들어졌습니다.

###Mobile Engagement 백 엔드에 앱 연결

1. 프로젝트에서 [Mobile Engagement Windows 유니버설 SDK] NuGet 패키지를 설치합니다. Windows와 Windows Phone 플랫폼을 모두 대상으로 하는 경우 두 프로젝트에 대해 이 작업을 수행해야 합니다. 동일한 Nuget 패키지는 각 프로젝트에서 올바른 플랫폼 특정 이진 파일을 배치합니다.

2. `Package.appxmanifest`을(를) 열고 다음이 자동으로 추가되지 않으면 추가합니다.

		Internet (Client)

	![][20]

3. 이제 앞서 Mobile Engagement App에서 복사한 연결 문자열을 `Resources\EngagementConfiguration.xml` 파일의 `<connectionString>` 및 `</connectionString>` 태그 사이에 붙여넣습니다.

	![][22]

	>[AZURE.TIP]앱이 Windows와 Windows Phone 플랫폼을 모두 대상으로 하는 경우 여전히 지원하는 각 플랫폼에 하나씩 두 개의 Mobile Engagement 응용 프로그램을 만들어야 합니다. 그러면 대상에 적절한 구분을 생성할 수 있고 각 플랫폼을 대상으로 하는 적절한 알림을 보낼 수 있습니다.

4. 파일 `App.xaml.cs`에서:

	a. `using` 구문 추가:

			using Microsoft.Azure.Engagement;

	b. SDK를 `OnLaunched` 메서드에서 초기화:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. `OnActivated` 메서드에 다음을 삽입하고 이미 존재하지 않으면 해당 메서드 추가:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>실시간 모니터링 사용

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다. 여기서는 Mobile Engagement SDK에서 제공하는 `EngagementPage`을(를) 사용해서 `MainPage`의 서브클래스를 지정하는 방식으로 이 작업을 수행합니다.

1. 	`using` 구문 추가:

		using Microsoft.Azure.Engagement;

2. `Page` 앞에 있는 `MainPage`의 슈퍼클래스를 아래와 같이 `EngagementPage`(으)로 변경:

	![][23]

3. `MainPage.xml` 파일에서:

	a. 다음 줄을 네임스페이스 선언에 추가합니다.

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. xml 태그 이름의 `Page`을(를) `engagement:EngagementPage`(으)로 바꿉니다.

###앱이 실시간 모니터링과 연결되어 있는지 확인

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 설명합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트에 사용하고 있는 앱으로 이동한 후 하단에 있는 **Engage** 단추를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Engagement 포털의 설정 페이지가 시작됩니다. 이 페이지에서 아래와 같이 **모니터** 탭을 클릭합니다. ![][30]

3. 모니터는 앱을 시작하는 모든 장치를 실시간으로 표시할 수 있습니다.

4. Visual Studio로 돌아가 에뮬레이터 또는 연결된 장치에서 앱을 시작합니다.

5. 앱이 정상적으로 작동하면 모니터에 실시간으로 세션 하나가 표시됩니다. ![][33]

**축하합니다.** 이미 데이터를 전송 중인 Mobile Engagement 백 엔드에 연결하는 앱을 사용하여 이 자습서의 첫 단계를 정상적으로 완료했습니다.

##<a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다. 다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

###WNS 푸시 알림을 받도록 앱 설정

1. `Notifications` 아래의 `Application` 탭의 파일 `Package.appxmanifest`에서 `Toast capable:`에 대해 `Yes` 선택:

	![][35]

###도달률 SDK 초기화

1. `App.xaml.cs`에서 `OnLaunched` 함수의 에이전트 초기화 바로 뒤에서 `EngagementReach.Instance.Init();`을(를) 호출합니다.

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. `App.xaml.cs`에서 `OnActivated` 함수의 에이전트 초기화 바로 뒤에서 `EngagementReach.Instance.Init(e);`을(를) 호출합니다.

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

이제 알림을 보낼 준비가 완료되었으므로 이 기본 통합이 올바르게 수행되었는지 확인하겠습니다.

###알림을 보내도록 Mobile Engagement 액세스 권한 부여

1. 다음으로는 앱을 Windows 스토어 앱에 연결하여 `Package security identifier (SID)` 및 `Secret Key`(클라이언트 암호)을(를) 가져와야 합니다. [Windows 스토어 개발자 센터]에서 앱을 만들 수 있으며, 그런 다음 Visual Studio의 **스토어와 앱 연결**을 해야 합니다.

2. Mobile Engagement 포털의 **설정**으로 이동하여 왼쪽의 `Native Push` 섹션을 클릭합니다.

3. `Edit`을 클릭하여 다음과 같이 `Package security identifier (SID)` 및 `Secret Key`을(를) 입력합니다.

	![][36]

##<a id="send"></a>앱에 알림 보내기

이제 앱에 푸시 알림을 보내는 간단한 푸시 알림 캠페인을 만들겠습니다.

1. Mobile Engagement 포털에서 **도달률** 탭으로 이동합니다.

2. **새 알림**을 클릭하여 푸시 캠페인을 만듭니다. ![][37]

3. 다음 단계를 수행하여 캠페인의 첫 번째 필드를 설정합니다. ![][38]

	a. 캠페인 이름을 지정합니다.

	b. 앱 시작 여부에 관계없이 앱에서 알림을 받을 수 있도록 **Delivery 시간**을 *항상*으로 선택합니다.

	c. 알림 텍스트에서 푸시에 굵게 표시할 **제목**을 입력합니다.

	d. 그런 다음 메시지를 입력합니다.

4. 아래로 스크롤하여 콘텐츠 섹션에서 **Notification only(알림만)**를 선택합니다. ![][39]

5. 이제 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 다시 아래쪽으로 스크롤하여 **만들기**를 눌러 저장해 보세요.

6. 마지막 단계로 **활성화**를 클릭하여 캠페인을 활성화하고 푸시 알림을 보냅니다. ![][41]

이제 장치의 캠페인에서 이 알림 메시지를 볼 수 있으며, 이 알림 메시지를 보려면 앱을 닫아야 합니다. 앱이 실행 중인 경우에는 알림 메시지를 받을 수 있도록 하기 위해 캠페인을 활성화하기 전에 몇 분동안 닫아야 합니다. 앱 내부의 알림을 통합하여 앱을 열었을 때 알림이 표시되도록 하려는 경우에는 [Windows 유니버설 앱 - 오버레이 통합]을 참조하세요.

<!-- URLs. -->
[Mobile Engagement Windows 유니버설 SDK 설명서]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows 유니버설 SDK]: http://go.microsoft.com/?linkid=9864592
[Windows 스토어 개발자 센터]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[Windows 유니버설 앱 - 오버레이 통합]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png
 

<!---HONumber=August15_HO6-->