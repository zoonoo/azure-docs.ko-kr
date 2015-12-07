<properties 
	pageTitle="Windows 유니버설 SDK 개요" 
	description="Azure Mobile Engagement의 Windows 유니버설 SDK 개요" 									
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
	ms.date="08/10/2015" 
	ms.author="piyushjo" />

#Azure Mobile Engagement의 Windows 유니버설 SDK 개요

이 문서에서는 Windows 유니버설 앱에 Azure Mobile Engagement를 통합하는 방법에 대한 세부 정보를 확인할 수 있습니다. 먼저 통합을 연습해 보려면 [15분 자습서](mobile-engagement-windows-store-dotnet-get-started.md)를 완료합니다.

[SDK 콘텐츠](mobile-engagement-windows-store-sdk-content.md)를 보려면 클릭하세요.

##통합 절차

1. 시작: [Windows 유니버설 앱에서 Mobile Engagement를 통합하는 방법](mobile-engagement-windows-store-integrate-engagement.md)

2. 알림: [Windows 유니버설 앱에서 도달률(알림)을 통합하는 방법](mobile-engagement-windows-store-integrate-engagement-reach.md)

3. 태그 계획 구현: [Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)

##릴리스 정보

###3\.2.0(11/20/2015)

-   Windows 10 유니버설 Windows 플랫폼 응용 프로그램에 대한 지원이 추가되었습니다.
-   채널 충돌(이제 Azure 알림 허브와 호환됨)을 해결하는 푸시 채널 공유 기능이 추가되었습니다.
-   초기화 바로 다음에 장치 ID를 요청하는 동안 크래시를 수정하였습니다.
-   콘솔은 향상된 기능을 기록합니다.
-   일부 처리되지 않은 예외를 구문 분석하는 동안 크래시를 수정하였습니다.

이전 버전에 대한 내용은 [전체 릴리스 정보](mobile-engagement-windows-store-release-notes.md)를 참조하세요.

##업그레이드 절차

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 하는 경우, 전체 [업그레이드 절차](mobile-engagement-windows-store-upgrade-procedure.md)를 참조하세요. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

###3\.1.0에서 3.2.0으로

#### 리소스
이 단계는 사용자 지정된 리소스에만 관련됩니다. SDK(html, 이미지, 오버레이)에서 제공되는 리소스를 사용자 지정한 경우 업그레이드된 리소스에서 사용자 지정한 내용을 업그레이드 및 다시 적용하기 전에 백업해야 합니다.

#### 웹 보기 통합을 사용하지 마세요.
다른 장치 형식 요인과 일치하는 일부 개선 사항이 이 버전에 도입되었습니다. Webview 통합이 다음과 일치하는지 확인합니다.

XAML 페이지 ()에서:

			<WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Right" VerticalAlignment="Top"/>
			<WebView x:Name="engagement_announcement_content" Visibility="Collapsed" HorizontalAlignment="Right" VerticalAlignment="Top"/> 

그리고 연결된 .cs파일에서:

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

### 이전 버전에서 업그레이드

[업그레이드 절차](mobile-engagement-windows-store-upgrade-procedure/)를 참조하세요.

<!---HONumber=AcomDC_1125_2015-->