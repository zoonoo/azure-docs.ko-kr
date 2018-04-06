---
title: Windows 유니버설 앱 도달률 SDK 통합
description: Windows 유니버설 앱과 Azure 모바일 Engagement 도달률을 통합하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: a31ca1d6-856f-4aec-898a-07969ae5f7ec
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8472af2ac72a1976871970963851be775b19859b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-reach-sdk-integration"></a>Windows 유니버설 앱 도달률 SDK 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 가이드의 작업을 수행하기 전에 [Windows 유니버설 Engagement SDK 통합](mobile-engagement-windows-store-integrate-engagement.md) 에 설명된 통합 절차를 수행해야 합니다.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-universal-project"></a>Windows 유니버설 프로젝트에 Engagement 도달률 SDK 포함
별도로 추가할 항목은 없습니다. `EngagementReach` 참조 및 리소스가 이미 프로젝트에 포함되어 있습니다.

> [!TIP]
> 프로젝트의 `Resources` 폴더에 있는 이미지, 특히 기본적으로 Engagement 아이콘을 사용하는 브랜드 아이콘을 사용자 지정할 수 있습니다. 유니버설 앱에서 공유 프로젝트의 `Resources` 폴더를 이동하여 앱 사이의 콘텐츠를 공유할 수도 있지만 플랫폼에 종속된 기본 위치에 `Resources\EngagementConfiguration.xml` 파일을 유지해야 합니다.
> 
> 

## <a name="enable-the-windows-notification-service"></a>Windows 알림 서비스를 사용하도록 설정
### <a name="windows-8x-and-windows-phone-81-only"></a>Windows 8.x 및 Windows Phone 8.1만 해당
`Application UI`의 `Package.appxmanifest` 파일에서 **Windows 알림 서비스**(WNS라고 함)를 사용하려면 왼쪽 아래 박스에서 `All Image Assets`을(를) 클릭합니다. `Notifications`의 오른쪽 상자에서 `toast capable`을(를) `(not set)`에서 `(Yes)`(으)로 변경합니다.

### <a name="all-platforms"></a>모든 플랫폼
Microsoft 계정 및 Engagement 플랫폼에 앱을 동기화해야 합니다. 이를 위해 계정을 만들거나 [Windows 개발자 센터](https://dev.windows.com)에 로그온해야 합니다. 그다음에 새 응용 프로그램을 만들고 SID 및 암호 키를 찾습니다. Engagement 프런트 엔드에서 `native push` 의 앱 설정으로 이동하여 자격 증명을 붙여넣습니다. 그런 다음 프로젝트를 마우스 오른쪽 단추로 클릭하고 `store` 및 `Associate App with the Store...`을(를) 선택합니다. 동기화하기 전에 만든 응용 프로그램을 선택하면 됩니다.

## <a name="initialize-the-engagement-reach-sdk"></a>Engagement 도달률 SDK 초기화
`App.xaml.cs` 수정:

* `InitEngagement` 메서드에서 `EngagementAgent.Instance.Init` 바로 다음에 `EngagementReach.Instance.Init` 삽입:
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
        EngagementReach.Instance.Init(e);
      }
  
  `EngagementReach.Instance.Init`은(는) 전용 스레드에서 실행됩니다. 직접 실행할 필요가 없습니다.

> [!NOTE]
> 응용 프로그램의 다른 곳에서 푸시 알림을 사용 중인 경우 Engagement 도달률에 [푸시 채널을 공유](#push-channel-sharing) 해야 합니다.
> 
> 

## <a name="integration"></a>통합
Engagement는 도달률 앱 내 배너와 알림 및 설문 조사에 대한 중간 보기를 응용 프로그램에 추가하는 두 가지 방법(오버레이 통합 및 웹 보기 수동 통합)을 제공합니다. 동일한 페이지에서 두 가지 방법을 결합할 수 없습니다.

두 가지 통합 간의 선택은 다음과 같은 방식으로 요약할 수 있습니다.

* 이미 페이지가 에이전트 `EngagementPage`에서 상속된 경우 오버레이 통합을 선택할 수 있으며 이는 페이지에서 `EngagementPage`를 `EngagementPageOverlay`로 바꾸고 `xmlns:engagement="using:Microsoft.Azure.Engagement"`를 `xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"`로 바꾸는 정도의 문제입니다.
* 페이지에서 도달률 UI를 정확히 배치하거나 페이지에 다른 상속 수준을 추가하지 않으려는 경우 웹 보기 수동 통합을 선택할 수 있습니다. 

### <a name="overlay-integration"></a>오버레이 통합
Engagement 오버레이는 페이지에서 도달률 캠페인을 표시하는 데 사용되는 UI 요소를 동적으로 추가합니다. 오버레이가 레이아웃에 적합하지 않은 경우 대신 웹 보기 수동 통합을 고려해야 합니다.

.xaml 파일에서 `EngagementPage` 참조를 `EngagementPageOverlay`로 변경합니다.

* 다음 줄을 네임스페이스 선언에 추가합니다.
  
      xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"
* `engagement:EngagementPage` 및 `engagement:EngagementPageOverlay` 교체:

**EngagementPage를 사용하는 경우:**

        <engagement:EngagementPage 
            xmlns:engagement="using:Microsoft.Azure.Engagement">

            <!-- Your layout -->
        </engagement:EngagementPage>

**EngagementPageOverlay를 사용하는 경우:**

        <engagement:EngagementPageOverlay 
            xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay">

            <!-- Your layout -->
        </engagement:EngagementPageOverlay>

그런 다음 .cs 파일에서 `EngagementPage` 대신 `EngagementPageOverlay`로 페이지에 태그를 지정하고 `Microsoft.Azure.Engagement.Overlay`를 가져옵니다.

            using Microsoft.Azure.Engagement.Overlay;

* `EngagementPage` 및 `EngagementPageOverlay` 교체:

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


Engagement 오버레이는 레이아웃에 구성된 페이지 맨 위에 `Grid` 요소 및 두 개의 `WebView` 요소(하나는 배너용이고 나머지 하나는 중간 보기용)를 추가합니다.

`EngagementPageOverlay.cs` 파일에서 직접 오버레이 요소를 사용자 지정할 수 있습니다.

### <a name="web-views-manual-integration"></a>웹 보기 수동 통합
도달률은 페이지에서 배너 및 중간 보기의 표시를 담당할 두 개의 `WebView` 요소를 검색합니다. 두 개의 `WebView` 요소를 페이지의 임의 위치에 추가하기만 하면 되며 예제는 다음과 같습니다.

    <Grid x:Name="engagementGrid">

      <!-- Your layout -->

      <WebView x:Name="engagement_notification_content" Visibility="Collapsed" Height="80" HorizontalAlignment="Stretch" VerticalAlignment="Top"/>
      <WebView x:Name="engagement_announcement_content" Visibility="Collapsed"  HorizontalAlignment="Stretch"  VerticalAlignment="Stretch"/>
    </Grid>


이 예제에서 `WebView` 요소는 화면 회전 또는 창 크기를 변경할 때 자동으로 크기를 조정하는 컨테이너에 맞게 확장됩니다.

> [!WARNING]
> `WebView` 요소에 동일한 이름 지정 `engagement_notification_content` 및 `engagement_announcement_content`를 유지하는 것이 중요합니다. 도달률은 요소를 이름으로 식별합니다. 
> 
> 

## <a name="handle-datapush-optional"></a>datapush 처리(선택 사항)
응용 프로그램이 도달률 데이터 푸시를 수신할 수 있도록 하려면 EngagementReach 클래스의 두 이벤트를 구현해야 합니다.

App.xaml.cs의 App() 생성자에서 다음을 추가합니다.

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

> [!WARNING]
> Engagement는 데이터 푸시에 대해 여러 피드백을 수신할 수 없습니다. 이벤트에 대해 여러 처리기를 설정하려는 경우 피드백은 마지막으로 전송된 항목에 해당합니다. 이 경우에는 프런트 엔드에서 피드백을 혼동하지 않도록 항상 같은 값을 반환하는 것이 좋습니다.
> 
> 

## <a name="customize-ui-optional"></a>UI 사용자 지정(선택 사항)
### <a name="first-step"></a>첫 번째 단계
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

> [!NOTE]
> Engagement는 기본적으로 자체 `EngagementReachHandler` 구현을 사용합니다.
> 따라서 구현을 직접 작성할 필요는 없으며 직접 작성하더라도 모든 메서드를 재정의하지는 않아도 됩니다. 기본 동작에서는 Engagement 기준 개체가 선택됩니다.
> 
> 

### <a name="web-view"></a>웹 보기
도달률에서는 기본적으로 DLL의 포함된 리소스를 사용하여 알림과 페이지를 표시합니다.

전체 사용자 지정 기능을 제공하기 위해 웹 보기만 사용됩니다. 레이아웃을 사용자 지정하려면 리소스 파일 `EngagementAnnouncement.html` 및 `EngagementNotification.html`을(를) 직접 재정의합니다. Engagement가 제대로 실행하려면 `<body></body>` 에 모든 코드가 필요합니다. 그러나 `engagement_webview_area`외부에 태그를 추가할 수 있습니다.

원하는 리소스를 사용할 수도 있습니다.

서브클래스에서 `EngagementReachHandler` 메서드를 재정의하여 Engagement에서 레이아웃을 사용하도록 명령할 수 있습니다. 단, 포함된 Engagement 메커니즘을 사용할 때는 주의해야 합니다.

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


기본적으로 AnnouncementHTML은 `ms-appx-web:///Resources/EngagementAnnouncement.html`입니다. 푸시 메시지(텍스트 알림, 웹 알림, 설문 조사 알림)의 내용을 디자인하는 html 파일을 나타냅니다. AnnouncementName은 `engagement_announcement_content`입니다. xaml 페이지의 웹 보기 디자인 이름입니다.

NotfificationHTML은 `ms-appx-web:///Resources/EngagementNotification.html`입니다. 푸시 메시지의 알림을 디자인하는 html 파일입니다. NotfificationName은 `engagement_notification_content`입니다. xaml 페이지의 웹 보기 디자인 이름입니다.

### <a name="customization"></a>사용자 지정
Engagement 개체를 보존하는 경우 원하는 알림 및 공지 웹 보기를 사용자 지정할 수 있습니다. 세 번에 걸쳐 설명되는 webview에 주의합니다. 첫 번째는 xaml에서, 두 번째는 "setwebview()" 메서드의 .cs 파일에서, 세 번째는 html 파일에서 설명합니다.

* xaml에 현재 그래픽 레이아웃 webview 구성 요소를 설명합니다.
* .cs 파일에는 알림(notification 및 announcement)의 두 웹 보기 크기를 설정하는 "setwebview()"를 정의할 수 있습니다. 응용 프로그램의 크기를 조정할 때 매우 효율적입니다.
* Engagement html 파일에는 웹 보기 내용, 디자인 및 웹 보기 간의 요소 위치에 대한 설명을 포함합니다.

### <a name="launch-message"></a>시작 메시지
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

> [!TIP]
> UI 스레드에서 각 처리기를 호출합니다. 따라서 MessageBox 또는 UI 관련 항목을 사용할 때는 별도의 작업을 수행할 필요가 없습니다.
> 
> 

## <a id="push-channel-sharing"></a> 푸시 채널 공유
응용 프로그램에서 푸시 알림을 다른 용도로 사용 하는 경우 Engagement SDK의 푸시 채널 공유 기능을 사용해야 합니다. 이는 푸시 누락을 방지하기 위함입니다.

* Engagement 도달률 초기화에 푸시 채널을 제공할 수 있습니다. SDK는 새 항목을 요청하는 대신 이것을 사용합니다.

`App.xaml.cs` 파일의 `InitEngagement` 메서드에서 푸시 채널을 사용한 Engagement 도달률 초기화를 업데이트합니다.

    /* Your own push channel logic... */
    var pushChannel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

    /*...Engagement initialization */
    EngagementAgent.Instance.Init(e);
    EngagementReach.Instance.Init(e,pushChannel);

* 또한, 도달률 초기화 후 푸시 채널을 사용하려는 경우 Engagement 도달률에 콜백을 설정하여 푸시 채널을 SDK에 의해 생성하여 만들 수 있습니다.

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

## <a name="custom-scheme-tip"></a>사용자 지정 체계 팁
사용자 지정 체계 사용법이 제공됩니다. Engagement 응용 프로그램에서 사용할 여러 URI 유형을 Engagement 프런트 엔드에서 보낼 수 있습니다. `http, ftp, ...` 와(과) 같은 기본 체계는 Windows에서 관리되며 장치에 기본 응용 프로그램이 설치되어 있지 않으면 해당 메시지가 포함된 창이 표시됩니다. 또한 응용 프로그램에 대해 사용자 지정 체계를 사용할 수도 있습니다.

응용 프로그램에서 사용자 지정 체계를 간편하게 설정하는 방법은 `Package.appxmanifest`을(를) 열고 `Declarations` 패널로 이동합니다. 사용 가능한 선언 스크롤 상자에서 `Protocol` 을(를) 선택하여 추가합니다. 새 프로토콜의 원하는 이름을 입력하여 `Name` 필드를 편집합니다.

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

