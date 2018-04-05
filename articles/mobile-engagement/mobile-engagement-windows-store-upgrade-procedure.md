---
title: Windows 유니버설 앱 SDK 업그레이드 절차
description: Azure Mobile Engagement용 Windows 유니버설 앱 SDK 업그레이드 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 4c898175-2cd6-43db-b350-bb408332f24d
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: a9d6cbcdf353f7eea991c344c3efe65378abe336
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-sdk-upgrade-procedures"></a>Windows 유니버설 앱 SDK 업그레이드 절차
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이전 버전의 Engagement를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

## <a name="from-330-to-340"></a>3.3.0에서 3.4.0으로
### <a name="test-logs"></a>테스트 로그
이제 SDK에서 생성된 콘솔 로그를 사용/사용 안 함/필터링할 수 있습니다. 이를 사용자 지정하려면 속성 `EngagementAgent.Instance.TestLogEnabled`를 `EngagementTestLogLevel` 열거형에서 사용 가능한 값 중 하나로 업데이트합니다. 예를 들어 다음과 같습니다.

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

### <a name="resources"></a>리소스
도달률 오버레이가 향상되었습니다. SDK NuGet 패키지 리소스의 일부입니다.

새 버전의 SDK로 업그레이드하는 동안 리소스의 오버레이 폴더에서 기존 파일을 보관할 것인지 보관하지 않을 것인지 여부를 선택할 수 있습니다.

* 이전 오버레이가 작동 중이거나 `WebView` 요소를 수동으로 통합하는 경우 기존 파일을 보관하도록 결정하여 계속 작동하도록 할 수 있습니다. 
* 새 오버레이로 업데이트하려는 경우 리소스의 전체 `overlay` 폴더를 SDK 패키지의 새 폴더로 바꿉니다(UWP 앱: 업그레이드 후 새 오버레이 폴더를 %USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\3.4.0\content\win81\Resources에서 가져올 수 있음).

> [!WARNING]
> 새 오버레이를 사용하면 이전 버전에서 설정한 모든 사용자 지정을 덮어씁니다.
> 
> 

## <a name="from-320-to-330"></a>3.2.0에서 3.3.0으로
### <a name="resources"></a>리소스
이 단계는 사용자 지정된 리소스에만 관련됩니다. SDK(html, 이미지, 오버레이)에서 제공되는 리소스를 사용자 지정한 경우 업그레이드된 리소스에서 사용자 지정한 내용을 업그레이드 및 다시 적용하기 전에 백업해야 합니다.

## <a name="from-310-to-320"></a>3.1.0에서 3.2.0으로
### <a name="resources"></a>리소스
이 단계는 사용자 지정된 리소스에만 관련됩니다. SDK(html, 이미지, 오버레이)에서 제공되는 리소스를 사용자 지정한 경우 업그레이드된 리소스에서 사용자 지정한 내용을 업그레이드 및 다시 적용하기 전에 백업해야 합니다.

### <a name="webview-integration"></a>웹 보기 통합을 사용하지 마세요.
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

## <a name="from-200-to-300"></a>2.0.0에서 3.0.0으로
### <a name="resources"></a>리소스
이 단계는 사용자 지정된 리소스에만 관련됩니다. SDK(html, 이미지, 오버레이)에서 제공되는 리소스를 사용자 지정한 경우 업그레이드된 리소스에서 사용자 지정한 내용을 업그레이드 및 다시 적용하기 전에 백업해야 합니다.

## <a name="from-111-to-200"></a>1.1.1에서 2.0.0으로 마이그레이션
아래에서는 SDK 통합을 Capptain SAS 제공 Capptain 서비스에서 Azure Mobile Engagement 구동 앱으로 마이그레이션하는 방법을 설명합니다. 

> [!IMPORTANT]
> Capptain과 Mobile Engagement는 같은 서비스가 아니며, 아래에서 제공하는 절차에서는 클라이언트 앱을 마이그레이션하는 방법만 중점적으로 설명합니다. 앱에서 SDK를 마이그레이션해도 데이터가 Capptain 서버에서 Mobile Engagement 서버로 마이그레이션되지는 않습니다.
> 
> 

이전 버전에서 마이그레이션하는 경우에는 Capptain 웹 사이트를 참조하여 1.1.1로 먼저 마이그레이션한 후에 다음 절차를 따르세요.

### <a name="nuget-package"></a>NuGet 패키지
**Capptain.WindowsPhone**을 **MicrosoftAzure.MobileEngagement** Nuget 패키지로 대체합니다.

### <a name="applying-mobile-engagement"></a>Mobile Engagement 적용
SDK에서는 `Engagement`(이)라는 용어를 사용합니다. 이 변경 내용에 맞게 프로젝트를 업데이트해야 합니다.

현재 Capptain NuGet 패키지는 제거해야 합니다. Capptain 리소스 폴더의 모든 변경 내용도 제거됩니다. 해당 폴더의 파일을 보존하려면 복사본을 만드세요.

그런 다음 새 Microsoft Azure Engagement NuGet 패키지를 프로젝트에 설치합니다. 해당 패키지는 [NuGet 웹 사이트]에서 직접 찾을 수 있습니다. 또는 여기 인덱스에서 찾을 수도 있습니다. 이 작업을 수행하면 Engagement에서 사용하는 모든 리소스 파일이 바뀌며 프로젝트 참조에 새 Engagement DLL이 추가됩니다.

Capptain DLL 참조를 삭제하여 프로젝트 참조를 정리해야 합니다. 이렇게 하지 않으면 Capptain 버전이 충돌하여 오류가 발생합니다.

Capptain 리소스를 사용자 지정한 경우 이전 파일 콘텐츠를 복사하여 새 Engagement 파일에 붙여 넣습니다. xaml 파일과 cs 파일을 모두 업데이트해야 합니다.

해당 단계를 완료한 후에는 이전 Capptain 참조만 새 Engagement 참조로 바꾸면 됩니다.

1. 모든 Capptain 네임스페이스를 업데이트해야 합니다.
   
    마이그레이션 전:
   
        using Capptain.Agent;
        using Capptain.Reach;
   
    마이그레이션 후:
   
        using Microsoft.Azure.Engagement;
2. "Capptain"을 포함하는 모든 Capptain 클래스는 이제 "Engagement"를 포함해야 합니다.
   
    마이그레이션 전:
   
        public sealed partial class MainPage : CapptainPage
        {
          protected override string GetCapptainPageName()
          {
            return "Capptain Demo";
          }
          ...
        }
   
    마이그레이션 후:
   
        public sealed partial class MainPage : EngagementPage
        {
          protected override string GetEngagementPageName()
          {
            return "Engagement Demo";
          }
          ...
        }
3. xaml 파일에서는 Capptain 네임스페이스와 특성도 변경됩니다.
   
    마이그레이션 전:
   
        <capptain:CapptainPage
        ...
        xmlns:capptain="clr-namespace:Capptain.Agent;assembly=Capptain.Agent.WP"
        ...
        </capptain:CapptainPage>
   
    마이그레이션 후:
   
        <engagement:EngagementPage
        ...
        xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
        ...
        </engagement:EngagementPage>
4. 오버레이 페이지 변경
   
   > [!IMPORTANT]
   > 오버레이도 변경됩니다. 새 네임스페이스는 `Microsoft.Azure.Engagement.Overlay`입니다. xaml 및 cs 파일에서 모두 이 네임스페이스를 사용해야 합니다. 또한 `CapptainGrid` 이름은 `EngagementGrid`(으)로, `capptain_notification_content` 및 `capptain_announcement_content` 이름은 `engagement_notification_content` 및 `engagement_announcement_content`(으)로 지정됩니다.
   > 
   > 
   
    오버레이의 경우 아래 코드는
   
        <capptain:CapptainPageOverlay
          xmlns:capptain="using:Capptain.Overlay"
          ...
        </capptain:CapptainPageOverlay>
   
    아래와 같이 변경됩니다.
   
        <EngagementPageOverlay
          engagement="using:Microsoft.Azure.Engagement.Overlay"
          ...
        </engagement:EngagementPageOverlay>
5. Capptain 그림 및 HTML 파일과 같은 기타 리소스도 "Engagement"를 사용하도록 이름이 바뀌었습니다.

### <a name="project-declaration"></a>프로젝트 선언
Package.appxmanifest에서 `File Type Associations` 이(가) 다음과 같이 업데이트되었습니다.

* capptain\_reach\_content를 engagement\_reach\_content로
* capptain\_log\_file을 engagement\_log\_file로

### <a name="application-id--sdk-key"></a>응용 프로그램 ID/SDK 키
Engagement에서는 연결 문자열을 사용합니다. 따라서 Mobile Engagement에서는 응용 프로그램 ID와 SDK 키를 지정할 필요가 없으며 연결 문자열만 지정하면 됩니다. EngagementConfiguration 파일에서 연결 문자열을 설정할 수 있습니다.

Engagement 구성은 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 설정할 수 있습니다.

이 파일을 편집하여 다음을 지정합니다.

* `<connectionString>` 및 `<\connectionString>` 태그 사이의 응용 프로그램 연결 문자열

이 문자열을 런타임에 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(args, engagementConfiguration);

응용 프로그램의 연결 문자열은 Azure Portal에 표시됩니다.

### <a name="items-name-change"></a>항목 이름 변경
*capptain*이라는 모든 항목은 *engagement*라고 이름을 지정합니다. 마찬가지로 *Capptain*은 *Engagement*로 지정됩니다.

일반적으로 사용되는 Capptain 항목의 예제:

* CapptainConfiguration의 이름은 EngagementConfiguration으로 바뀌었습니다.
* CapptainAgent의 이름은 EngagementAgent로 바뀌었습니다.
* CapptainReach의 이름은 EngagementReach로 바뀌었습니다.
* CapptainHttpConfig의 이름은 EngagementHttpConfig로 바뀌었습니다.
* GetCapptainPageName의 이름은 GetEngagementPageName으로 바뀌었습니다.

이와 같이 바뀐 이름은 재정의되는 메서드에도 영향을 줍니다.

