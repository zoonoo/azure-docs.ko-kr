<properties 
	pageTitle="Windows 유니버설 앱 도달률 SDK 통합" 
	description="Windows 유니버설 앱과 Azure 모바일 Engagement 도달률을 통합하는 방법"
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
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="piyushjo" />

# Windows 유니버설 앱 도달률 SDK 통합

이 가이드의 작업을 수행하기 전에 [Windows 유니버설 Engagement SDK 통합](mobile-engagement-windows-store-integrate-engagement.md)에 설명된 통합 절차를 수행해야 합니다.

## Windows 유니버설 프로젝트에 Engagement 도달률 SDK 포함

별도로 추가할 항목은 없습니다. `EngagementReach` 참조 및 리소스가 이미 프로젝트에 포함되어 있습니다.

> [AZURE.TIP] 프로젝트의 `Resources` 폴더에 있는 이미지, 특히 기본적으로 Engagement 아이콘을 사용하는 브랜드 아이콘을 사용자 지정할 수 있습니다. 유니버설 앱에서 공유 프로젝트의 `Resources` 폴더를 이동하여 앱 사이의 콘텐츠를 공유할 수도 있지만 플랫폼에 종속된 기본 위치에 `Resources\EngagementConfiguration.xml` 파일을 유지해야 합니다.

## Windows 알림 서비스를 사용하도록 설정

### Windows 8.x 및 Windows Phone 8.1만 해당

`Application UI`의 `Package.appxmanifest` 파일에서 **Windows 알림 서비스**(WNS라고 함)를 사용하려면 왼쪽 bot 박스에서 `All Image Assets`을(를) 클릭합니다. `Notifications`의 오른쪽 상자에서 `toast capable`을(를) `(not set)`에서 `(Yes)`(으)로 변경합니다.

### 모든 플랫폼

Microsoft 계정 및 Engagement 플랫폼에 앱을 동기화해야 합니다. 이를 위해 계정을 만들거나 [Windows 개발자 센터](https://dev.windows.com)에 로그온해야 합니다. 그다음에 새 응용 프로그램을 만들고 SID 및 암호 키를 찾습니다. Engagement 프런트 엔드에서 `native push`의 앱 설정으로 이동하여 자격 증명을 붙여넣습니다. 그런 다음 프로젝트를 마우스 오른쪽 단추로 클릭하고 `store` 및 `Associate App with the Store...`을(를) 선택합니다. 동기화하기 전에 만든 응용 프로그램을 선택하면 됩니다.

## Engagement 도달률 SDK 초기화

`App.xaml.cs` 수정:

-   `InitEngagement` 메서드에서 `EngagementAgent.Instance.Init` 바로 다음에 `EngagementReach.Instance.Init` 삽입:

		private void InitEngagement(IActivatedEventArgs e)
		{
		  EngagementAgent.Instance.Init(e);
		  EngagementReach.Instance.Init(e);
		}

	`EngagementReach.Instance.Init`은(는) 전용 스레드에서 실행됩니다. 직접 실행할 필요가 없습니다.

> [AZURE.NOTE] 응용 프로그램의 다른 곳에서 푸시 알림을 사용 중인 경우 Engagement 도달률에 [푸시 채널을 공유](#push-channel-sharing)해야 합니다.

## 통합

Engagement는 도달률 알림 및 공지를 구현하는 두 가지 방법(오버레이 및 웹 보기 통합)을 제공합니다.

오버레이 통합을 위해 응용 프로그램에 많은 코드를 작성할 필요는 없습니다. EngagementPageOverlay를 사용하여 페이지, xaml, cs 파일에 태그만 지정하면 됩니다. 또한 Engagement 기본 보기를 사용자 지정하는 경우에는 사용자 지정 내용이 한 번만 정의되어 태그가 지정된 모든 페이지에서 공유됩니다. 그러나 페이지가 EngagementPageOverlay 이외의 다른 개체에서 상속해야 하는 경우에는 웹 보기 통합을 사용하도록 중단 및 강제 적용됩니다.

웹 보기 통합은 구현이 더 복잡합니다. 하지만 앱 페이지가 "Page" 이외의 다른 개체에서 상속해야 하는 경우에는 웹 보기와 해당 동작을 통합해야 합니다.

> [AZURE.TIP] 모든 페이지 내용을 포함하려면 루트 수준 `<Grid></Grid>` 요소 추가를 고려해야 합니다. 웹 보기 통합에서는 웹 보기를 이 Grid 요소의 자식으로 추가하기만 하면 됩니다. 다른 위치에 Engagement 구성 요소를 설정해야 하는 경우 표시 크기를 직접 관리해야 합니다.

### 오버레이 통합

Engagement에서는 알림(notification 및 announcement) 표시를 위한 오버레이를 제공합니다.

이를 사용하려면 웹 보기 통합을 사용하지 마세요.

.xaml 파일에서 EngagementPage 참조를 EngagementPageOverlay로 변경합니다.

-   다음 줄을 네임스페이스 선언에 추가합니다.

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

-   `engagement:EngagementPage` 및 `engagement:EngagementPageOverlay` 교체:

**EngagementPage를 사용하는 경우:**

		<engagement:EngagementPage 
		    xmlns:engagement="using:Microsoft.Azure.Engagement">
		
		    <!-- layout -->
		</engagement:EngagementPage>

**EngagementPageOverlay를 사용하는 경우:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		
		    <!-- layout -->
		</engagement:EngagementPageOverlay>

> **8.1용 EngagementPageOverlay를 사용하는 경우:**

		<engagement:EngagementPageOverlay 
		    xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">
		    <Grid>
		      <!-- layout -->
		    </Grid>
		</engagement:EngagementPageOverlay>

그런 다음 .cs 파일에서 "EngagementPage 대신 "EngagementPageOverlay"로 페이지에 태그를 지정하고 "Microsoft.Azure.Engagement.Overlay"를 가져옵니다.

			using Microsoft.Azure.Engagement.Overlay;

-   `EngagementPage` 및 `EngagementPageOverlay` 교체:

**EngagementPage를 사용하는 경우:**

			using Microsoft.Azure.Engagement;
			
			namespace Example
			{
			  public sealed partial class ExamplePage : EngagementPage
			  {
			    [...]
			  }
			}

**EngagementPageOverlay를 사용하는 경우:**

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

-   `EngagementAnnouncement.html` : `Announcement` 웹 보기 html 디자인.
-   `EngagementOverlayAnnouncement.xaml` : `Announcement` xaml 디자인.
-   `EngagementOverlayAnnouncement.xaml.cs` : `EngagementOverlayAnnouncement.xaml` 연결된 코드.
-   `EngagementNotification.html` : `Notification` 웹 보기 html 디자인.
-   `EngagementOverlayNotification.xaml` : `Notification` xaml 디자인.
-   `EngagementOverlayNotification.xaml.cs` : `EngagementOverlayNotification.xaml` 연결된 코드.
-   `EngagementPageOverlay.cs` : `Overlay` 공지 및 알림 표시 코드.

### 웹 보기 통합

이를 사용하려면 오버레이 통합을 사용하지 마세요.

Engagement 콘텐츠를 표시하려면 각 페이지에 xaml WebView 2개를 통합하고 공지 및 알림을 표시해야 합니다. 다음 코드를 xaml 파일에 추가합니다.

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

> **8.1 통합:**

			<engagement:EngagementPage
			    xmlns:engagement="using:Microsoft.Azure.Engagement">
			    <Grid>
			      <!-- Your layout -->
			      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Right" VerticalAlignment="Top"/> 
			    </Grid>
			</engagement:EngagementPage>

연결된 .cs 파일은 다음과 같이 표시됩니다.

    using Microsoft.Azure.Engagement;
    using System;
    using Windows.ApplicationModel.Core;
    using Windows.UI.ViewManagement;
    using Windows.UI.Xaml;
    using Windows.UI.Xaml.Navigation;

    namespace My.Namespace.Example
    {
			/// <summary>
			/// An empty page that can be used on its own or navigated to within a Frame.
			/// </summary>
			public sealed partial class ExampleEngagementReachPage : EngagementPage
			{
			  public ExampleEngagementReachPage()
			  {
			    this.InitializeComponent();
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
			
			  #region to implement
              /* Attach events when page is navigated. */
              protected override void OnNavigatedTo(NavigationEventArgs e)
              {
                /* Update the webview when the app window is resized. */
                Window.Current.SizeChanged += DisplayProperties_OrientationChanged;

                /* Update the webview when the app/status bar is resized. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged += DisplayProperties_VisibleBoundsChanged; 
    #endif
                base.OnNavigatedTo(e);
              }

			  /* When page is left ensure to detach SizeChanged handler. */
			  protected override void OnNavigatedFrom(NavigationEventArgs e)
			  {
			    Window.Current.SizeChanged -= DisplayProperties_OrientationChanged;
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
                ApplicationView.GetForCurrentView().VisibleBoundsChanged -= DisplayProperties_VisibleBoundsChanged;
    #endif
			    base.OnNavigatedFrom(e);
			  }
			  
			  /* "width" and "height" are the current size of your application display. */
    #if WINDOWS_PHONE_APP || WINDOWS_UWP
			  double width = ApplicationView.GetForCurrentView().VisibleBounds.Width;
			  double height = ApplicationView.GetForCurrentView().VisibleBounds.Height;
    #else
			  double width =  Window.Current.Bounds.Width;
			  double height =  Window.Current.Bounds.Height;
    #endif
			
			  /// <summary>
			  /// Set your webview elements to the correct size.
			  /// </summary>
			  /// <param name="width">The width of your current display.</param>
			  /// <param name="height">The height of your current display.</param>
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
			  /// Handler that takes the Windows.Current.SizeChanged and indicates that webviews have to be resized.
			  /// </summary>
			  /// <param name="sender">Original event trigger.</param>
			  /// <param name="e">Window Size Changed Event arguments.</param>
			  private void DisplayProperties_OrientationChanged(object sender, Windows.UI.Core.WindowSizeChangedEventArgs e)
			  {
			    double width = e.Size.Width;
			    double height = e.Size.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }

    #if WINDOWS_PHONE_APP || WINDOWS_UWP			  
			  /// <summary>
			  /// Handler that takes the ApplicationView.VisibleBoundsChanged and indicates that webviews have to be resized
			  /// </summary>
			  /// <param name="sender">The related application view.</param>
			  /// <param name="e">Related event arguments.</param>
			  private void DisplayProperties_VisibleBoundsChanged(ApplicationView sender, Object e)
			  {
			    double width = sender.VisibleBounds.Width;
			    double height = sender.VisibleBounds.Height;
			
			    /* Set your webview elements to the correct size. */
			    SetWebView(width, height);
			  }
    #endif
			  #endregion
			}
    }

> 이 경우 장치 화면을 회전하면 포함된 웹 보기 크기 조정이 구현됩니다.

## datapush 처리(선택 사항)

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

각 메서드의 콜백에서는 부울이 반환됩니다. Engagement에서는 데이터 푸시를 디스패치한 후 해당 백 엔드로 피드백을 전송합니다. 콜백에서 false를 반환하면 `exit` 피드백이 전송됩니다. 그렇지 않으면 `action`이(가) 반환됩니다. 이벤트에 대해 콜백이 설정되어 있지 않으면 `drop` 피드백이 Engagement에 반환됩니다.

> [AZURE.WARNING] Engagement는 데이터 푸시에 대해 여러 피드백을 수신할 수 없습니다. 이벤트에 대해 여러 처리기를 설정하려는 경우 피드백은 마지막으로 전송된 항목에 해당합니다. 이 경우에는 프런트 엔드에서 피드백을 혼동하지 않도록 항상 같은 값을 반환하는 것이 좋습니다.

## UI 사용자 지정(선택 사항)

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

> [AZURE.NOTE] Engagement는 기본적으로 자체 `EngagementReachHandler` 구현을 사용합니다. 따라서 구현을 직접 작성할 필요는 없으며 직접 작성하더라도 모든 메서드를 재정의하지는 않아도 됩니다. 기본 동작에서는 Engagement 기준 개체가 선택됩니다.

### 웹 보기

도달률에서는 기본적으로 DLL의 포함된 리소스를 사용하여 알림과 페이지를 표시합니다.

전체 사용자 지정 기능을 제공하기 위해 웹 보기만 사용됩니다. 레이아웃을 사용자 지정하려면 리소스 파일 `EngagementAnnouncement.html` 및 `EngagementNotification.html`을(를) 직접 재정의합니다. Engagement가 제대로 실행하려면 `<body></body>`에 모든 코드가 필요합니다. 그러나 `engagement_webview_area` 외부에 태그를 추가할 수 있습니다.

원하는 리소스를 사용할 수도 있습니다.

서브클래스에서 `EngagementReachHandler` 메서드를 재정의하여 Engagement에서 레이아웃을 사용하도록 명령할 수 있습니다. 단, 포함된 Engagement 메커니즘을 사용할 때는 주의해야 합니다.

**샘플 코드 :**
			
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


기본적으로 AnnouncementHTML은 `ms-appx-web:///Resources/EngagementAnnouncement.html`입니다. 푸시 메시지(텍스트 알림, 웹 알림, 설문 조사 알림)의 내용을 디자인하는 html 파일을 나타냅니다. AnnouncementName은 `engagement_announcement_content`입니다. xaml 페이지의 웹 보기 디자인 이름입니다.

NotfificationHTML은 `ms-appx-web:///Resources/EngagementNotification.html`입니다. 푸시 메시지의 알림을 디자인하는 html 파일입니다. NotfificationName은 `engagement_notification_content`입니다. xaml 페이지의 웹 보기 디자인 이름입니다.

### 사용자 지정

Engagement 개체를 보존하는 경우 원하는 알림 및 공지 웹 보기를 사용자 지정할 수 있습니다. 세 번에 걸쳐 설명되는 webview에 주의합니다. 첫 번째는 xaml에서, 두 번째는 "setwebview()" 메서드의 .cs 파일에서, 세 번째는 html 파일에서 설명합니다.

-   xaml에 현재 그래픽 레이아웃 webview 구성 요소를 설명합니다.
-   .cs 파일에는 알림(notification 및 announcement)의 두 웹 보기 크기를 설정하는 "setwebview()"를 정의할 수 있습니다. 응용 프로그램의 크기를 조정할 때 매우 효율적입니다.
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

`App.xaml.cs` 파일의 "Public App(){}" 메서드에서 콜백을 설정할 수 있으며 `EngagementReach.Instance.Init()` 호출 앞에 설정하는 것이 좋습니다.

> [AZURE.TIP] UI 스레드에서 각 처리기를 호출합니다. 따라서 MessageBox 또는 UI 관련 항목을 사용할 때는 별도의 작업을 수행할 필요가 없습니다.

##<a id="push-channel-sharing"></a> 푸시 채널 공유

응용 프로그램에서 푸시 알림을 다른 용도로 사용 하는 경우 Engagement SDK의 푸시 채널 공유 기능을 사용해야 합니다. 이는 푸시 누락을 방지하기 위함입니다.

- Engagement 도달률 초기화에 푸시 채널을 제공할 수 있습니다. SDK는 새 항목을 요청하는 대신 이것을 사용합니다.

`App.xaml.cs` 파일의 `InitEngagement` 메서드에서 푸시 채널을 사용한 Engagement 도달률 초기화를 업데이트합니다.
    
    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
    
    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
	EngagementReach.Instance.Init(e,pushChannel);

- 또한, 도달률 초기화 후 푸시 채널을 사용하려는 경우 Engagement 도달률에 콜백을 설정하여 푸시 채널을 SDK에 의해 생성하여 만들 수 있습니다.

도달률 초기화 **후** 모든 위치에서 콜백을 설정합니다.

    /* Set action on the SDK push channel. */
    EngagementReach.Instance.SetActionOnPushChannel((PushNotificationChannel channel) => 
    {
      /* The forwarded channel can be null if its creation fails for any reason. */
      if (channel != null)
      {
		/* Your own push channel logic... */
      });
	}

## 사용자 지정 체계 팁

사용자 지정 체계 사용법이 제공됩니다. Engagement 응용 프로그램에서 사용할 여러 URI 유형을 Engagement 프런트 엔드에서 보낼 수 있습니다. `http, ftp, ...`와(과) 같은 기본 체계는 Windows에서 관리되며 장치에 기본 응용 프로그램이 설치되어 있지 않으면 해당 메시지가 포함된 창이 표시됩니다. 또한 응용 프로그램에 대해 사용자 지정 체계를 사용할 수도 있습니다.

응용 프로그램에서 사용자 지정 체계를 간편하게 설정하는 방법은 `Package.appxmanifest`을(를) 열고 `Declarations` 패널로 이동합니다. 사용 가능한 선언 스크롤 상자에서 `Protocol`을(를) 선택하여 추가합니다. 새 프로토콜의 원하는 이름을 입력하여 `Name` 필드를 편집합니다.

이제 이 프로토콜을 사용하려면 `App.xaml.cs`을(를) `OnActivated` 메서드로 편집하고 여기서 Engagement를 초기화해야 합니다.

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
 

<!---HONumber=AcomDC_0302_2016-->