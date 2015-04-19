<properties 
	pageTitle="Azure Mobile Engagement Windows 스토어 SDK 도달률 통합" 
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
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="kapiteir" />

#Windows에서 Engagement 도달률을 통합하는 방법

이 가이드의 작업을 수행하기 전에 [Windows에서 Engagement를 통합하는 방법] 문서에서 설명하는 통합 절차를(mobile-engagement-windows-store-integrate-engagement.md) 수행해야 합니다.

##Windows 프로젝트에 Engagement 도달률 SDK 포함

별도로 추가할 항목은 없습니다.`EngagementReach`  참조 및 리소스가 이미 프로젝트에 포함되어 있습니다.

> [AZURE.TIP] 프로젝트의 `Resources` 폴더에 있는 이미지, 특히 기본적으로 Engagement 아이콘을 사용하는 브랜드 아이콘을 사용자 지정할 수 있습니다.

##기능 추가

Engagement 도달률 SDK에는 몇 가지 추가 기능이 필요합니다.

먼저 `Package.appxmanifest` 파일을 열고 `선언` 패널로 이동합니다. `사용 가능한 선언` 스크롤 상자에서  `파일 형식 연결` 을 선택하여 추가합니다. `engagement_reach_content`에 이름을 설정하고 `.txt`에 파일 형식을 설정합니다.

##Windows 알림 서비스를 사용하도록 설정

`Application UI`에서 `Package.appxmanifest` 파일의 **Windows 알림 서비스** (WNS으로 불림)를 사용하려면 왼쪽 bot 상자에서 `모든 이미지 자산`을 클릭합니다. 상자 오른쪽의 `알림`에서 `알림 가능`을 `(설정되지 않음)` 에서 `예`로 변경합니다.

또한 Microsoft 계정 및 Engagement 플랫폼에 앱을 동기화해야 합니다. Engagement 프런트 엔드에서 앱 설정의 '네이티브 푸시'로 이동하여 자격 증명을 붙여 넣습니다. 그런 다음 프로젝트를 마우스 오른쪽 단추로 클릭하고 `스토어` , `Associate App with the Store...`.을 차례로 선택합니다.

##Engagement 도달률 SDK 초기화

먼저 `App.xaml.cs` 파일을 수정합니다.

-   `using` 문에 추가합니다.

			using Microsoft.Azure.Engagement;

-   그런 다음 `OnLaunched` 에서 `EngagementAgent.Instance.Init` 바로 뒤에 `EngagementReach.Instance.Init`를 삽입합니다.

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			}

> [AZURE.NOTE] `EngagementReach.Instance.Init` 는 전용 스레드에서 실행되므로 직접 실행할 필요가 없습니다.

-   앱을 활성화할 때 Engagement 도달률을 시작하려면 `OnActivated` 메서드를 재정의합니다.

			protected override void OnActivated(IActivatedEventArgs args)
			{
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			}

> [AZURE.TIP] 프로젝트의 `Resources\EngagementConfiguration.xml` 파일 내 `<channelName></channelName>`에서 응용 프로그램의 WNS 푸시 채널 이름을 지정할 수 있습니다. 기본적으로는 appId를 기준으로 이름이 생성됩니다. Engagement 외부에서 푸시 채널을 사용하려는 경우를 제외하면 이름을 직접 지정할 필요가 없습니다.

##통합

Engagement에서는 도달률 알림(notification 및 announcement)을 구현하는 두 가지 방식인 오버레이 통합과 웹 보기 통합을 제공합니다.

[오버레이 통합] -(#overlay-integration) 응용 프로그램에 대해 많은 코드를 작성할 필요가 없습니다. EngagementPageOverlay를 사용하여 페이지, xaml, cs 파일에 태그만 지정하면 됩니다. 또한 Engagement 기본 보기를 사용자 지정하는 경우에는 사용자 지정 내용이 한 번만 정의되어 태그가 지정된 모든 페이지에서 공유됩니다. 그러나 페이지가 EngagementPageOverlay 이외의 다른 개체에서 상속해야 하는 경우에는 웹 보기 통합을 사용해야 합니다.

[웹 보기 통합] -(#web-view-integration) 구현하기가 더 복잡합니다. 그러나 앱 페이지가 "Page" 이외의 다른 개체에서 상속해야 하는 경우에는 웹 보기와 해당 동작을 통합해야 합니다.

Windows 8.1 스토어 응용 프로그램의 모범 사례에 따르면, 첫 번째 수준 `<Grid></Grid>` 요소를 추가하여 모든 페이지 콘텐츠를 둘러싸야 합니다. 웹 보기 통합에서는 웹 보기를 이 Grid 요소의 자식으로 추가하기만 하면 됩니다. 다른 위치에 Engagement 구성 요소를 설정해야 하는 경우 표시 크기를 직접 관리해야 합니다.

### 오버레이 통합

Engagement에서는 알림(notification 및 announcement) 표시를 위한 오버레이를 제공합니다.

오버레이를 사용하려는 경우 [웹 보기 통합]을 사용하지 마세요.(#web-view-integration).

.xaml 파일에서 EngagementPage 참조를 EngagementPageOverlay로 변경합니다.

-   다음 줄을 네임스페이스 선언에 추가합니다.

			xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay"

-   `engagement:EngagementPage`를 `engagement:EngagementPageOverlay`로 바꿉니다.

**EngagementPage를 사용하는 경우**

			<engagement:EngagementPage 
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			
			    <!-- layout -->
			</engagement:EngagementPage>

**EngagementPageOverlay를 사용하는 경우**

			<engagement:EngagementPageOverlay 
			    xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			
			    <!-- layout -->
			</engagement:EngagementPageOverlay>

> **8.1용 EngagementPageOverlay를 사용하는 경우**

			<engagement:EngagementPageOverlay 
			    xmlns:engagement="using:using:Microsoft.Azure.Engagement.Overlay">
			    <Grid>
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPageOverlay>

그런 다음 .cs 파일에서 "EngagementPage 대신 "EngagementPageOverlay"로 페이지에 태그를 지정하고 "Microsoft.Azure.Engagement.Overlay"를 가져옵니다.

			using Microsoft.Azure.Engagement.Overlay;

-   `EngagementPage` 를 `EngagementPageOverlay`로 바꿉니다.

**EngagementPage를 사용하는 경우**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**EngagementPageOverlay를 사용하는 경우**

			using Microsoft.Azure.Engagement.Overlay;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPageOverlay 
			  {
			    [...]
			  }
			}

이제 이 페이지는 Engagement 오버레이 메커니즘을 사용하므로 웹 보기를 삽입하지 않아도 됩니다.

Engagement 오버레이는 xaml 파일에서 발견되는 첫 번째 "Grid" 요소를 사용하여 페이지에 웹 보기 2개를 추가합니다. 웹 보기를 설정할 위치를 찾으려는 경우 "EngagementGrid"라는 그리드를 다음과 같이 정의할 수 있습니다.

			<Grid x:Name="EngagementGrid"></Grid>

오버레이 알림(notification 및 announcement)은 해당 xaml 및 cs 파일에서 직접 사용자 지정할 수 있습니다.

-   `EngagementAnnouncement.html` : `알림(Announcement)` 웹 보기 html 디자인입니다.
-   `EngagementOverlayAnnouncement.xaml` : `알림(Announcement)` xaml 디자인입니다.
-   `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` 연결된 코드입니다.
-   `EngagementNotification.html` : `알림(Notification)` 웹 보기 html 디자인입니다.
-   `EngagementOverlayNotification.xaml` : `알림(Notification)` xaml 디자인입니다.
-   `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` 연결된 코드입니다.
-   `EngagementPageOverlay.cs` :  `오버레이` 알림(announcement 및 notification) 표시 코드입니다.

### 웹 보기 통합

웹 보기를 사용하려는 경우 [을 사용하지 마세요Overlay integration](#overlay-integration).

Engagement 콘텐츠를 표시하려면 알림(notification 및 announcement)을 표시해야 하는 각 페이지에 xaml 웹 보기 2개를 통합해야 합니다. 다음 코드를 xaml 파일에 추가합니다.

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **8.1 통합**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" ScriptNotify="scriptEvent" Height="64" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed" ScriptNotify="scriptEvent" HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			      <!-- layout -->
			    </Grid>
			</engagement:EngagementPage>

연결된 .cs 파일은 다음과 같이 표시됩니다.

			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			   /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			
			    Window.Current.SizeChanged += DisplayProperties_OrientationChanged;
			  }
			
			  #region to implement
			  /* Allow webview script to notify system */
			  private void scriptEvent(object sender, NotifyEventArgs e)
			  {
			  }
			
			  /* When page is left ensure to detach SizeChanged handler */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
			    base.OnNavigatedFrom(e);
			  }
			
			  /* "width" is the current width of your application display */
			  double width = Window.Current.Bounds.Width;
			
			  /* "height" is the current height of your application display */
			  double height = Window.Current.Bounds.Height;
			
			  /// <summary>
			  /// Set your webview elements to the correct size
			  /// </summary>
			  /// <param name="width">The width of your current display</param>
			  /// <param name="height">The height of your current display</param>
			  private void SetWebView(double width, double height)
			  {
			    #pragma warning disable 4014
			    CoreApplication.MainView.CoreWindow.Dispatcher.RunAsync(Windows.UI.Core.CoreDispatcherPriority.Normal,
			            () =>
			            {
			              this.engagement_notification_content.Width = width;
			              this.engagement_announcement_content.Width = width;
			              this.engagement_announcement_content.Height = height;
			            });
			  }
			
			  /// <summary>
			  /// Handler that take the Windows.Current.SizeChanged and indicate that webview have to be resized
			  /// </summary>
			  /// <param name="sender">Original event trigger</param>
			  /// <param name="e">Window Size Changed Event argument</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size */
			    SetWebView(width, height);
			  }
			  #endregion
			}

> 이 경우 장치 화면을 회전하면 포함된 웹 보기 크기 조정이 구현됩니다.

##datapush 처리(선택 사항)

응용 프로그램이 도달률 데이터 푸시를 수신할 수 있도록 하려면 EngagementReach 클래스의 두 이벤트를 구현해야 합니다.

App.xaml.cs의 "Public App(){}"에 다음 코드를 추가합니다.

			EngagementReach.Instance.DataPushStringReceived += (body) =>
			{
			  Debug.WriteLine("String data push message received: " + body);
			  return true;
			};
			
			EngagementReach.Instance.DataPushBase64Received += (decodedBody, encodedBody) =>
			{
			  Debug.WriteLine("Base64 data push message received: " + encodedBody);
			  // Do something useful with decodedBody like updating an image view
			  return true;
			};

각 메서드의 콜백에서는 부울이 반환됩니다. Engagement에서는 데이터 푸시를 디스패치한 후 해당 백 엔드로 피드백을 전송합니다. 콜백에서 false를 반환하면 `exit` 피드백이 전송됩니다. 그렇지 않으면 `action`이 전송됩니다. 이벤트에 대해 콜백이 설정되어 있지 않으면 `drop` 피드백이 Engagement에 반환됩니다.

> [AZURE.WARNING] Engagement는 데이터 푸시에 대해 여러 피드백을 수신할 수 없습니다. 이벤트에 대해 여러 처리기를 설정하려는 경우 피드백은 마지막으로 전송된 항목에 해당합니다. 이 경우에는 프런트 엔드에서 피드백을 혼동하지 않도록 항상 같은 값을 반환하는 것이 좋습니다.

##UI 사용자 지정(선택 사항)

### 첫 번째 단계

도달률 UI는 사용자 지정할 수 있습니다.

UI를 사용자 지정하려면 `EngagementReachHandler` 클래스의 서브클래스를 만들어야 합니다.

**샘플 코드:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  internal class ExampleReachHandler : EngagementReachHandler
			  {
			   // Override EngagementReachHandler methods depending on your needs
			  }
			}

그런 다음 `App()` 메서드 내에서 `App.xaml.cs` 클래스의 사용자 지정 개체를 사용하여 `EngagementReach.Instance.Handler` 필드의 콘텐츠를 설정합니다.

**샘플 코드:**

			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			  // your app initialization 
			  EngagementReach.Instance.Handler = new ExampleReachHandler();
			  // Engagement Agent and Reach initialization
			}

> [AZURE.NOTE] Engagement는 기본적으로 자체 `EngagementReachHandler` 구현을 사용합니다.
> 따라서 구현을 직접 작성할 필요는 없으며 직접 작성하더라도 모든 메서드를 재정의하지는 않아도 됩니다. 기본 동작에서는 Engagement 기준 개체가 선택됩니다.

### 웹 보기

도달률에서는 기본적으로 DLL의 포함된 리소스를 사용하여 알림과 페이지를 표시합니다.

전체 사용자 지정 기능을 제공하기 위해 웹 보기만 사용됩니다. 레이아웃을 사용자 지정하려면 리소스 파일 `EngagementAnnouncement.html` 및 `EngagementNotification.html`을 직접 재정의하세요. Engagement가 정상적으로 작동하려면 '<body></body>' 내의 모든 코드가 필요합니다. 그러나 `engagement_webview_area` 외부에 태그를 추가할 수 있습니다.

원하는 리소스를 사용할 수도 있습니다.

서브클래스에서 `EngagementReachHandler` 메서드를 재정의하여 Engagement에서 특정 레이아웃을 사용하도록 명령할 수 있습니다. 단, 포함된 Engagement 메커니즘을 사용할 때는 주의해야 합니다.

**샘플 코드:**
			
			// In your subclass of EngagementReachHandler
			
			public override string GetAnnouncementHTML()
			{
			  return base.GetAnnouncementHTML();
			}
			public override string GetAnnouncementName()
			{
			  return base.GetAnnouncementName();
			}
			public override string GetNotfificationHTML()
			{
			  return base.GetNotfificationHTML();
			}
			public override string GetNotfificationName()
			{
			  return base.GetNotfificationName();
			}


기본적으로 AnnouncementHTML은 `ms-appx-web:///Resources/EngagementAnnouncement.html`이며, 푸시 메시지(텍스트 알림, 웹 알림, 설문 조사 알림)의 내용을 디자인하는 html 파일을 나타냅니다. AnnouncementName은 `engagement_announcement_content`이며, xaml 페이지의 웹 보기 디자인 이름입니다.

NotfificationHTML은 `ms-appx-web:///Resources/EngagementNotification.html`이며, 푸시 메시지의 알림을 디자인하는 html 파일입니다. NotfificationName은 `engagement_notification_content`이며, xaml 페이지의 웹 보기 디자인 이름입니다.

### 사용자 지정

Engagement 개체를 보존하는 경우 원하는 알림(notification 및 announcement) 웹 보기를 사용자 지정할 수 있습니다. webview 개체는 3번에 걸쳐 설명되도록 합니다. 첫 번째는 xaml에서, 두 번째는 .cs 파일의 "setwebview()" 메서드에서 그리고 세 번째는 html 파일에서 설명합니다.

-   xaml에 현재 그래픽 레이아웃 webview 구성 요소를 설명합니다.
-   .cs 파일에는 알림(notification 및 announcement)의 두 웹 보기 크기를 설정하는 "setwebview()"를 정의할 수 있습니다. 따라서 응용 프로그램의 크기를 조정할 때 매우 효율적입니다.
-   Engagement html 파일에는 웹 보기 내용, 디자인 및 웹 보기 간의 요소 위치에 대한 설명을 포함합니다.

### 시작 메시지

사용자가 시스템 알림(알림)을 클릭하면 응용 프로그램이 시작되고 푸시 메시지의 내용이 로드되며 해당 캠페인의 페이지가 표시됩니다.

응용 프로그램을 시작한 후부터 페이지가 표시될 때까지 네트워크 속도에 따라 지연이 발생합니다.

콘텐츠가 로드 중임을 사용자에게 알리려면 진행률 표시줄이나 진행률 표시기와 같은 시각적 정보를 제공해야 합니다. Engagement에서 이 과정을 직접 처리할 수는 없으며, 처리에 사용할 수 있는 몇 가지 처리기를 제공합니다.

콜백을 구현하려면 App.xaml.cs의 "Public App(){}"에 다음 코드를 추가합니다.

			/* The application has launched and the content is loading.
			 * You should display an indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageStarted += () => { [...] };
			
			/* The application has finished loading the content and the page
			 * is about to be displayed.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageCompleted += () => { [...] };
			
			/* The content has been loaded, but an error has occurred.
			 * You can provide an information to the user.
			 * You should hide the indicator here.
			 */
			EngagementReach.Instance.RetrieveLaunchMessageFailed += () => { [...] };

콜백은 `App.xaml.cs` 파일의 "Public App(){}" 메서드에서 설정할 수 있으며 `EngagementReach.Instance.Init()` 호출 앞에 설정하는 것이 좋습니다.

> [AZURE.TIP] UI 스레드에서 각 처리기를 호출합니다. 따라서 MessageBox 또는 UI 관련 항목을 사용할 때는 별도의 작업을 수행할 필요가 없습니다.

##사용자 지정 체계 팁

사용자 지정 체계 사용법이 제공됩니다. Engagement 응용 프로그램에서 사용할 여러 URI 유형을 Engagement 프런트 엔드에서 보낼 수 있습니다. 기본 체계인 `http, ftp, ...`등은 Windows에서 관리되며 장치에 기본 응용 프로그램이 설치되어 있지 않으면 해당 메시지가 포함된 창이 표시됩니다. 응용 프로그램 체계 등의 기타 체계를 사용할 수도 있습니다. 또한 응용 프로그램에 대해 사용자 체계를 사용할 수도 있습니다.

응용 프로그램에서 사용자 지정 체계를 간편하게 설정하려면 `Package.appxmanifest`를 열고 `선언` 패널로 이동합니다. 사용 가능한 선언 스크롤 상자에서 `프로토콜`을 선택하여 추가합니다. 새 프로토콜의 원하는 이름을 입력하여 `이름` 필드를 편집합니다.

이제 이러한 프로토콜을 사용하려면 `OnActivated` 메서드로 `App.xaml.cs`를 편집합니다. 여기서도 Engagement를 초기화해야 합니다.

			/// <summary>
			/// Enter point when app his called by another way than user click
			/// </summary>
			/// <param name="args">Activation args</param>
			protected override void OnActivated(IActivatedEventArgs args)
			{
			  /* Init engagement like it was launch by a custom uri scheme */
			  EngagementAgent.Instance.Init(args);
			  EngagementReach.Instance.Init(args);
			
			  //TODO design action to do when app is launch
			
			  #region Custom scheme use
			  if (args.Kind == ActivationKind.Protocol)
			  {
			    ProtocolActivatedEventArgs myProtocol = (ProtocolActivatedEventArgs)args;
			
			    if (myProtocol.Uri.Scheme.Equals("protocolName"))
			    {
			      string path = myProtocol.Uri.AbsolutePath;
			    }
			  }
			  #endregion

<!--HONumber=47-->
