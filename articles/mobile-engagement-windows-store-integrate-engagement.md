<properties 
	pageTitle="Azure Mobile Engagement Windows 스토어 SDK 개요" 
	description="Azure Mobile Engagement용 Windows 스토어 SDK의 최신 업데이트 및 절차" 					
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="lalathie" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-integrate-engagement/" title="Windows Store" class="current">Windows 스토어</a><a href="/documentation/articles/mobile-engagement-windows-phone-integrate-engagement/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-integrate-engagement/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-integrate-engagement/" title="Android" >Android</a></div>

#Windows에서 Engagement를 통합하는 방법

이 절차에서는 Windows 응용 프로그램에서 Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

다음 단계만 수행하면 사용자, 세션, 활동, 작동 중단 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그 보고를 활성화할 수 있습니다. 이벤트, 오류, 작업 등의 기타 통계는 응용 프로그램별로 다르므로, 해당 통계를 계산하는 데 필요한 로그 보고는 Engagement API를 사용하여 수동으로 수행해야 합니다. 관련 설명은 [Windows 스토어 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](../mobile-engagement-windows-store-use-engagement-api/)을 참조하세요..

##지원되는 버전

이 Windows SDK는 다음 버전만 지원합니다.

-   Windows 8 C\# 스토어 응용 프로그램
-   Windows 8.1 C\# 스토어 응용 프로그램

##Windows 프로젝트에 Engagement SDK 통합

Microsoft 개발 플랫폼용 NuGet 패키지 관리자를 사용하여 Engagement SDK를 포함합니다.

Mobile Engagement용 NuGet 패키지는 아직 온라인에서 제공되지 않으므로 SDK 보관 파일을 다운로드하여 압축을 푼 다음 Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 "NuGet 패키지 관리"->설정으로 이동한 후에 lib 폴더를 새 패키지 원본으로 추가합니다.

> [AZURE.NOTE] NuGet은 .NET 프로젝트의 주 패키지 관리자이며, Engagement SDK를 사용하는 데 필요한 모든 콘텐츠를 다운로드/추가/복사합니다. Engagement Windows 8 C\# Metro 응용 프로그램은 플랫폼 간 호환성을 위해 타사 라이브러리(Microsoft.Bcl.Build v1.0.10 및 Microsoft.Bcl portability pack v1.1.3)를 사용합니다. NuGet은 이러한 타사 라이브러리를 설치하라는 메시지도 자동으로 표시합니다. 자세한 내용은 [NuGet 웹 사이트]에서 확인할 수 있습니다.

##기능 추가

Engagement SDK가 올바르게 작동하려면 Windows SDK의 일부 기능이 필요합니다.

`Package.appxmanifest`  파일을 열고 다음 기능이 선언되었는지 확인합니다.

-   `Internet (Client)`

Package.appxmanifest 파일의 `Declarations` 패널로 이동합니다.

`사용 가능한 선언` 에서 `파일 형식 연결` 을 선택하여 추가합니다. 오른쪽 화면의 `이름` 필드에 `engagement_log_file`을 입력하고 파일 형식 필드에 `.set`를 입력합니다.

그런 다음 `사용 가능한 선언` 에서 `캐시된 파일 업데이트 프로그램` 을 선택하여 추가합니다.

##Engagement SDK 초기화

### Engagement 구성

Engagement 구성은 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 중앙 집중식으로 관리됩니다.

이 파일을 편집하여 다음을 지정합니다.

-   `<connectionString>` 및 `<\connectionString>` 태그 사이의 응용 프로그램 연결 문자열

이 문자열을 런타임에 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Initialize Engagement angent with above configuration. */
			EngagementAgent.Instance.Init(args, engagementConfiguration);

응용 프로그램의 연결 문자열은 Azure 포털에 표시됩니다.

### Engagement 초기화

새 프로젝트를 만들 때는 `App.xaml.cs` 파일이 생성됩니다. 이 클래스는 `Application`에서 상속하며, 여러 중요 메서드를 포함합니다. Engagement SDK를 초기화할 때도 이 클래스가 사용됩니다.

`App.xaml.cs`: 파일을 수정합니다.

-   `using` 문에 다음 줄을 추가합니다.

			using Microsoft.Azure.Engagement;

-   Insert `EngagementAgent.Instance.Init` in the `OnLaunched` method:

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

-   사용자 지정 체계, 다른 응용 프로그램 또는 명령줄을 사용하여 응용 프로그램을 시작하면 `OnActivated` 메서드가 호출됩니다. 또한 앱을 활성화할 때 Engagement 에이전트도 초기화해야 합니다. 이렇게 하려면 `OnActivated` 메서드를 재정의합니다.

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			
			  // or
			
			  EngagementAgent.Instance.Init(args, engagementConfiguration);
			}

> [AZURE.IMPORTANT] 응용 프로그램의 다른 위치에는 Engagement 초기화를 추가하지 않는 것이 좋습니다.

##기본 보고

### 권장 방법: `Page` 클래스 오버로드

Engagement에서 사용자, 세션, 활동, 작동 중단 및 기술 통계를 계산하는 데 필요한 모든 로그의 보고를 활성화하려는 경우 모든 `Page` 서브클래스가 `EngagementPage` 클래스에서 상속하도록 지정하면 됩니다.

아래에는 응용 프로그램 페이지에 대해 이 작업을 수행하는 방법의 예제가 나와 있습니다. 응용 프로그램의 모든 페이지에 대해 동일한 작업을 수행할 수 있습니다.

#### C\# 소스 파일

페이지 `.xaml.cs` 파일을 수정합니다.

-   `using` 문에 다음 줄을 추가합니다.

			using Microsoft.Azure.Engagement;

-   `Page` 를 `EngagementPage`로 바꿉니다:

**Engagement 미포함**

			namespace Example
			{
			  public sealed partial class ExamplePage : Page
			  {
			    [...]
			  }
			}

**Engagement 포함**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage 
			  {
			    [...]
			  }
			}

> [AZURE.IMPORTANT] 페이지가 `OnNavigatedTo` 메서드를 재정의하는 경우에는 `base.OnNavigatedTo(e)`를 호출해야 합니다. 그렇지 않으면 활동이 보고되지 않습니다. `EngagementPage`는 해당 `OnNavigatedTo` 메서드 내에서 `StartActivity`를 호출합니다.

#### XAML 파일

페이지 `.xaml` 파일을 수정합니다.

-   다음 줄을 네임스페이스 선언에 추가합니다.

			xmlns:engagement="using:Microsoft.Azure.Engagement"

-   `Page`를 `engagement:EngagementPage`로 바꿉니다.

**Engagement 미포함**

			<Page>
			    <!-- layout -->
			    ...
			</Page>

**Engagement 포함**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <!-- layout -->
			    ...
			</engagement:EngagementPage >

#### 기본 동작 재정의

기본적으로 페이지의 클래스 이름은 extra 없이 활동 이름으로 보고됩니다. 클래스에서 "Page" 접미사를 사용하는 경우에는 해당 접미사도 제거됩니다.

이름의 기본 동작을 재정의하려면 코드에 다음 코드 조각만 추가하면 됩니다.

			// in the .xaml.cs file
			protected override string GetEngagementPageName()
			{
			  /* your code */
			  return "new name";
			}

활동과 함께 추가 정보를 보고하려는 경우에는 코드에 다음 코드 조각을 추가할 수 있습니다.

			// in the .xaml.cs file
			protected override Dictionary<object,object> GetEngagementPageExtra()
			{
			  /* your code */
			  return extra;
			}

추가 정보에 대한 자세한 내용은 \<windows-sdk-extras-parameters\>에서 확인할 수 있습니다

페이지의 `OnNavigatedTo` 메서드 내에서 이러한 메서드를 호출합니다.

### 대체 메서드: 수동으로 `StartActivity()` 호출

 `Page` 클래스를 오버로드할 수 없거나 오버로드하지 않으려는 경우 `EngagementAgent` 메서드를 직접 호출하여 활동을 시작할 수 있습니다.

페이지의 `OnNavigatedTo` 메서드 내에서 `StartActivity`를 호출하는 것이 좋습니다.

			protected override void OnNavigatedTo(NavigationEventArgs e)
			{
			  base.OnNavigatedTo(e);
			  EngagementAgent.Instance.StartActivity("MyPage");
			}

> [AZURE.IMPORTANT] 응용 프로그램을 닫을 때 Windows SDK는 `EndActivity` 메서드를 자동으로 호출합니다. 따라서 사용자 활동이 변경될 때마다 `StartActivity` 메서드를 호출하는 것이 좋으며 `EndActivity` 메서드는 호출하지 않는 것이 좋습니다. EndActivity 메서드를 호출하면 현재 세션이 강제로 종료되기 때문입니다.

##고급 보고

필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고할 수 있습니다. 이렇게 하려면 `EngagementAgent` 클래스에 포함된 다른 메서드를 사용합니다. Engagement API에서는 Engagement의 모든 고급 기능을 사용할 수 있습니다.

자세한 내용은 Windows에서 Engagement API를 사용하는 방법을 참조하세요.

##고급 구성

### 자동 작동 중단 보고를 사용하지 않도록 설정

Engagement의 자동 작동 중단 보고를 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 처리되지 않은 예외 발생 시 Engagement에서 아무런 작업도 수행하지 않습니다.

> [AZURE.WARNING] 이 기능을 사용하지 않도록 설정하려는 경우 앱에서 처리되지 않은 작동 중단이 발생해도 Engagement에서 작동 중단을 전송하지 **않으며** 세션 및 작업도 닫지 않습니다.

자동 작동 중단 보고를 사용하지 않도록 설정하려면 구성을 선언한 방식에 따라 사용자 지정합니다.

####  `EngagementConfiguration.xml` 파일에서

`<reportCrash>` 및 `</reportCrash>` 태그 간의 작동 중단 보고를 `false`로 설정합니다.

#### 런타임에 `EngagementConfiguration` 개체에서

EngagementConfiguration 개체를 사용하여 작동 중단 보고를 false로 설정합니다.

			/* Engagement configuration. */
			EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
			engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";
			
			/* Disable Engagement crash reporting. */
			engagementConfiguration.Agent.ReportCrash = false;

### 버스트 모드 [베타]

기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 매우 자주 보고하는 경우에는 로그를 버퍼링한 다음 정기적으로 한꺼번에 보고하는 것이 보다 효율적입니다. 이러한 방식을 "버스트 모드"라고 합니다.

이 방식을 사용하려면 다음 메서드를 호출합니다.

			EngagementAgent.Instance.SetBurstThreshold(int everyMs);

인수는 **밀리초** 단위의 값입니다. 언제든지 실시간 로깅을 다시 활성화하려면 매개 변수를 포함하지 않거나 값을 0으로 설정하여 메서드를 호출하면 됩니다.

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 30000(30초) 이하의 버스트 임계값을 사용하는 것이 좋습니다.

> [AZURE.WARNING] 1초보다 짧은 기간으로 버스트 임계값을 구성할 수는 없습니다. 버스트 임계값을 1초보다 짧게 구성하면 SDK에는 오류가 포함된 추적이 표시되며, 값은 자동으로 기본값인 0초로 다시 설정됩니다. 그러면 SDK에서 실시간 로그 보고가 트리거됩니다.

[여기]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet 웹 사이트]:http://docs.nuget.org/docs/start-here/overview

<!--HONumber=47-->
