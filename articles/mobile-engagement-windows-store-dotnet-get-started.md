<properties 
	pageTitle="Azure Mobile Engagement 시작" 
	description="분석 및 푸시 알림 기능이 포함된 Azure Mobile Engagement를 사용하는 방법에 대해 알아봅니다."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Mobile Engagement 시작

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store"  class="current">Windows 스토어</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

이 항목에서는 Azure Mobile Engagement를 사용하여 Windows 스토어 응용 프로그램에서 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다. 
이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 여기서는 WNS(Windows 알림 서비스)를 사용하여 푸시 알림을 받고 기본적인 데이터를 수집하는 빈 Windows 스토어 앱을 만듭니다. 이 과정을 완료하면 장치 속성에 따라 특정 사용자를 대상으로 지정하거나 모든 장치로 푸시 알림을 브로드캐스트할 수 있습니다. Mobile Engagement를 사용하여 장치의 특정 사용자 및 그룹을 처리하는 방법을 알아보려면 다음 자습서도 함께 완료해야 합니다.


이 자습서의 작업을 수행하려면 다음이 필요합니다.

+ Visual Studio 2013
+ [Mobile Engagement Windows 스토어 SDK]

> [AZURE.IMPORTANT] Windows 스토어 앱용 모든 기타 Mobile Engagement 자습서를 완료하려면 이 자습서를 먼저 완료해야 합니다. 그러려면 활성 Azure 계정이 필요합니다. 계정이 없는 경우 몇 분 만에 무료 평가판 계정을 만들 수 있습니다. 자세한 내용은 <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fko-kr%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Azure 무료 평가판</a>을 참조하세요.

##<a id="setup-azme"></a>앱용 Mobile Engagement 설정

1. **Azure 관리 포털**에 로그온하여 화면 아래쪽의 **+새로 만들기**를 클릭합니다.

2. **앱 서비스**, **Mobile Engagement**, **만들기**를 차례로 클릭합니다.

   	![][7]

3. 팝업이 표시되면 다음 정보를 입력합니다.
 
   	![][8]

	1. **응용 프로그램 이름**: 응용 프로그램의 이름을 입력할 수 있습니다. 원하는 모든 문자를 사용할 수 있습니다.
	2. **플랫폼**: 앱의 대상 플랫폼을 선택합니다. 앱의 대상 플랫폼이 여러 개이면 각 플랫폼에 대해 이 자습서의 작업을 반복합니다.
	3. **응용 프로그램 리소스 이름**: API 및 URL을 통해 이 응용 프로그램에 액세스하는 데 사용되는 이름입니다. 기본 URL 문자만 사용하는 것이 좋습니다. 자동으로 생성되는 이름을 기준으로 사용할 수 있습니다. 또한 이 이름은 고유해야 하므로 이름 충돌을 방지하기 위해 플랫폼 이름을 추가하는 것이 좋습니다.
	4. **위치**: 이 앱과 해당 컬렉션(아래 설명 참조)을 호스트할 데이터 센터를 선택합니다.
	5. **컬렉션**: 응용 프로그램을 이미 만든 경우 이전에 만든 컬렉션을 선택하고 그렇지 않으면 새 컬렉션을 선택합니다.
	6. **컬렉션 이름**: 응용 프로그램 그룹을 나타냅니다. 또한 그룹을 사용하는 경우에는 모든 앱이 그룹에 포함되므로 집계 계산이 가능합니다. 회사 이름이나 부서를 사용하는 것이 좋습니다.


	작업을 완료한 후 확인 단추를 클릭하여 앱 만들기를 완료합니다.

4. 이제 **응용 프로그램** 탭에서 방금 만든 앱을 클릭/선택합니다.
 
   	![][9]

5. 그런 다음 **연결 정보**를 클릭하여 SDK 통합에 적용할 연결 설정을 표시합니다.
 
   	![][10]

6. 마지막으로 이 앱을 응용 프로그램 코드에서 식별하는 데 필요한 **연결 문자열**을 작성합니다.

   	![][11]

	>[AZURE.TIP] 연결 문자열 오른쪽의 "복사" 아이콘을 사용하여 연결 문자열을 클립보드에 복사하면 편리합니다.

##<a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결

이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement Windows 스토어 SDK 설명서]에서 확인할 수 있습니다.

여기서는 통합을 시연하기 위해 Visual Studio를 사용하여 기본적인 앱을 만듭니다.

###새 Windows 스토어 프로젝트 만들기

이미 앱이 있으며 Windows 개발에 대해 잘 알고 있으면 이 단계를 건너뛰어도 됩니다.

1. Visual Studio를 시작하고 홈 화면에서 **새 프로젝트...**를 선택합니다.

   	![][12]

2. 팝업에서 (1) `Windows 스토어`을 선택한 다음 (2) `새 앱(XAML)`을 선택합니다. 또한 (3) 앱 `이름`과 (4) `솔루션 이름`도 입력하고 **확인**을 클릭합니다.

   	![][13]

이제 데모 앱이 포함된 프로젝트가 작성되었습니다. 여기에 Mobile Engagement를 통합합니다.

###프로젝트에 SDK 라이브러리 포함

SDK 라이브러리를 다운로드하여 통합합니다.

1. [Mobile Engagement Windows 스토어 SDK]를 다운로드합니다.
2. 컴퓨터의 폴더에 .zip 파일의 압축을 풉니다.
3.  `PROJECT`->'NuGet 패키지 관리...'로 이동합니다.
4. 팝업에서 설정을 클릭합니다.
5. 그런 다음 '+' 단추를 눌러 새 원본을 만듭니다.

	![][17]

6. 아래쪽에서 `...` 단추를 클릭하여 원본을 선택하고 압축을 푼 SDK 다운로드의 lib 폴더로 이동한 후에(2단계 참조) `선택`을 클릭합니다.

	![][18]

7. 그러면 SDK가 원본으로 추가됩니다.  `확인`을 클릭합니다. "온라인" 패키지 목록에서 패키지를 선택하고 설치를 클릭합니다.

	![][19]


###연결 문자열을 사용하여 Mobile Engagement 백 엔드에 앱 연결

1.  `Package.appxmanifest`를 열어 다음 항목이 선언되었는지 확인한 후에 선언되어 있지 않으면 추가합니다.
		
		Internet (Client)

	![][20]

2. 같은 파일에서 `선언` 탭을 엽니다.
	1.  `사용 가능한 선언`에서 `파일 형식 연결`을 추가합니다.
	2. 그런 다음 오른쪽의 `속성` 부분에서 `이름`을 `engagement_log_file`로 설정합니다.
	3. 계속해서 오른쪽의 `속성` 부분에서 `파일 형식`을 `.set`로 설정합니다.
	4. 마지막으로 `사용 가능한 선언`에서 `캐시된 파일 업데이트 프로그램`을 추가합니다.

		![][21]
 
3. Azure 포털의 앱 **연결 정보** 페이지로 돌아가서 **연결 문자열**을 복사합니다.

	![][11]

4. 연결 문자열을 `Resources\EngagementConfiguration.xml` 파일의 `<connectionString>` 및 `</connectionString>`  태그 사이에 붙여 넣습니다.

	![][22]

5. 이제 `App.xaml.cs` 파일에서 다음을 수행합니다.
	1. 다음 `using` 문을 추가합니다.

			using Microsoft.Azure.Engagement;

	2. SDK를 `OnLaunched` 메서드에서 초기화합니다.
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

	3. 다음 코드를 `Application_Activated`에 삽입합니다. 이 작업이 아직 없으면 추가해야 합니다.

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  //... rest of the code
			}

###Mobile Engagement에 화면 보내기

데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다. 여기서는 Mobile Engagement SDK에서 제공하는 `EngagementPage`를 사용해 `MainPage` 서브클래스를 지정하는 방식으로 이 작업을 수행합니다.

1. 이렇게 하려면 `Page` 앞에 있는 `MainPage`의 슈퍼클래스를 아래와 같이 EngagementPage로 바꿉니다.

	![][23]

	>[AZURE.NOTE] 클래스에 빨간색 밑줄이 표시되면 `using Microsoft.Azure.Engagement;`를 `using` 절에 추가하여 클래스를 확인해야 합니다.

2. 다음으로는 `MainPage.xaml` 파일에서 아래 단계를 수행합니다.
	1. 다음 줄을 네임스페이스 선언에 추가합니다.

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. xml 태그 이름의 `Page`를 `engagement:EngagementPage`로 바꿉니다.

##<a id="monitor"></a>앱이 실시간 모니터링 기능과 연결되어 있는지 확인하는 방법

이 섹션에서는 Mobile Engagement의 실시간 모니터링 기능을 사용하여 앱이 Mobile Engagement 백 엔드에 연결되어 있는지 확인하는 방법을 설명합니다.

1. Mobile Engagement 포털로 이동합니다.

	Azure 포털에서 이 프로젝트용으로 사용 중인 앱이 열려 있는지 확인하고 아래쪽에서 **참여**를 클릭합니다.

	![][26]

2. 그러면 앱에 대한 Engagement 포털의 설정 페이지가 시작됩니다. 이 페이지에서 아래와 같이 **모니터** 탭을 클릭합니다.
![][30]

3. 모니터는 앱을 시작하는 모든 장치를 실시간으로 표시할 수 있습니다.
![][31]

4. Visual Studio로 돌아와서 녹색 삼각형을 클릭한 다음 장치를 선택하여 에뮬레이터나 연결된 장치에서 앱을 시작합니다.

5. 앱이 정상적으로 작동하면 모니터에 세션 하나가 표시됩니다. 
![][33]

**축하합니다!** 이미 데이터를 전송 중인 Mobile Engagement 백 엔드에 연결하는 앱을 사용하여 이 자습서의 첫 단계를 정상적으로 완료했습니다.


##<a id="integrate-push"></a>푸시 알림을 사용하도록 설정

Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 해당 알림과 메시지를 받도록 앱을 설정합니다.

###WNS 푸시 알림을 받도록 앱 설정

1. 선언 패널에서 새 `파일 형식 연결`을 `Package.appxmanifest` 파일에 추가합니다.

		Name: engagement_reach_content
		File type: .txt

	![][34]

2. 계속해서 `Package.appxmanifest` 파일의 `Application` 탭 내 `알림`에서 `알림 가능`에 대해 `예`를 선택합니다.

	![][35]

###도달률 SDK 초기화

1.  `App.xaml.cs`에서  `OnLaunched` 함수의 에이전트 초기화 바로 뒤에서  `EngagementReach.Instance.Init(args);` 를 호출합니다.

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		   EngagementAgent.Instance.Init(args);
		   EngagementReach.Instance.Init(args);
		}

2.  `App.xaml.cs`에서  `Application_Activated` 함수의 에이전트 초기화 바로 뒤에서  `EngagementReach.Instance.OnActivated(args);` 를 호출합니다.

		protected override void OnActivated(IActivatedEventArgs args)
		{
		   EngagementAgent.Instance.OnActivated(args);
		   EngagementReach.Instance.OnActivated(args);
		}

3. 응용 프로그램을 실행합니다. 
이제 알림을 보낼 준비가 완료되었으므로 이 기본 통합이 올바르게 수행되었는지 확인하겠습니다.

###알림을 보내도록 Mobile Engagement 액세스 권한 부여

1. 다음으로는 앱을 Windows 스토어에 연결하여 `Package security identifier (SID)` 및 `비밀 키`(*클라이언트 암호*)를 가져와야 합니다.

2. 그런 다음 Mobile Engagement 포털의 설정으로 이동하여 왼쪽의 `네이티브 푸시` 섹션을 클릭합니다.

3. 그런 후에 `편집` 단추를 클릭하여 아래에 나와 있는 것처럼 `패키지 SID(보안 식별자)` 및 `비밀 키`를 입력합니다.

	![][36]

##<a id="send"></a>앱에 알림을 보내는 방법

이제 앱에 푸시 알림을 보내는 간단한 푸시 알림 캠페인을 만들겠습니다.

1. Mobile Engagement의 **도달률** 탭으로 이동합니다.
2. **새 알림**을 클릭하여 푸시 캠페인을 만듭니다.
![][37]

3. 다음 단계를 수행하여 캠페인의 첫 번째 필드를 설정합니다.
![][38]

	1. 캠페인 이름을 원하는 이름으로 지정합니다.
	2. 앱 시작 여부에 관계없이 앱에서 알림을 받을 수 있도록 **배달 시간**을 *항상*으로 선택합니다.
	3. 알림 텍스트에서 푸시에 굵게 표시할 제목을 입력합니다.
	4. 그런 다음 메시지를 입력합니다.

4. 아래쪽으로 스크롤한 다음 콘텐츠 섹션에서 **알림만**을 선택합니다.
![][39]

5. 이제 가능한 가장 기본적인 캠페인 설정을 완료했습니다. 다시 아래쪽으로 스크롤하여 캠페인을 만든 다음 저장해 보세요.
![][40]

6. 마지막 단계로 캠페인을 활성화합니다.
![][41]

이제 장치에 알림이 표시됩니다. **축하합니다!**:



<!-- URLs. -->
[Mobile Engagement Windows 스토어 SDK 설명서]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows 스토어 SDK]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
