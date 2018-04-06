---
title: Windows Phone 도달률 Engagement SDK 통합
description: Windows Phone Silverlight 앱에서 Azure Mobile Engagement 도달률을 통합하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: d3516a6b-db9f-4cdb-a475-4148edf81af1
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 34e5ce414ebf72fbecef6c921e57128e2658c921
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-reach-sdk-integration"></a>Windows Phone 도달률 Engagement SDK 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이 가이드의 작업을 수행하기 전에 [Windows Phone Silverlight Engagement SDK 통합](mobile-engagement-windows-phone-integrate-engagement.md) 에 설명된 통합 절차를 수행해야 합니다.

## <a name="embed-the-engagement-reach-sdk-into-your-windows-phone-silverlight-project"></a>Windows Phone Silverlight 프로젝트에 Engagement 도달률 SDK 통합
별도로 추가할 항목은 없습니다. `EngagementReach` 참조 및 리소스가 이미 프로젝트에 포함되어 있습니다.

> [!TIP]
> 프로젝트의 `Resources` 폴더에 있는 이미지, 특히 기본적으로 Engagement 아이콘을 사용하는 브랜드 아이콘을 사용자 지정할 수 있습니다.
> 
> 

## <a name="add-the-capabilities"></a>기능 추가
Engagement 도달률 SDK에는 몇 가지 추가 기능이 필요합니다.

`WMAppManifest.xml` 파일을 열고 다음 기능이 선언되었는지 확인합니다.

* `ID_CAP_PUSH_NOTIFICATION`
* `ID_CAP_WEBBROWSERCOMPONENT`

첫 번째는 MPNS 서비스에 의해 사용되어 알림 메시지의 표시를 허용합니다. 두 번째는 SDK에 브라우저 작업을 포함하는 데 사용됩니다.

`WMAppManifest.xml` 파일을 편집하여 `<Capabilities />` 태그 내에 추가:

    <Capability Name="ID_CAP_PUSH_NOTIFICATION" />
    <Capability Name="ID_CAP_WEBBROWSERCOMPONENT" />

## <a name="enable-the-microsoft-push-notification-service"></a>Microsoft 푸시 알림 서비스를 사용하도록 설정
MPNS, 즉 **Microsoft 푸시 알림 서비스**를 사용하려면 `WMAppManifest.xml` 파일에 `Publisher` 특성이 프로젝트 이름으로 설정된 `<App />` 태그가 있어야 합니다.

## <a name="initialize-the-engagement-reach-sdk"></a>Engagement 도달률 SDK 초기화
### <a name="engagement-configuration"></a>Engagement 구성
Engagement 구성은 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 중앙 집중식으로 관리됩니다.

이 파일을 편집하여 도달률 구성을 지정합니다.

* *선택 사항으로*, 네이티브 푸시(MPNS)가 `<enableNativePush>` 및 `</enableNativePush>` 태그 간에 활성화되는지 여부를 나타냅니다(기본적으로 `true`).
* *선택 사항으로*, `<channelName>` 및 `</channelName>` 태그 간의 푸시 채널 이름을 나타냅니다. 응용 프로그램이 현재 사용 중일 수 있는 동일 항목을 제공하거나 비워 둡니다.

대신 런타임에 구성을 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

    /* Engagement configuration. */
    EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

    engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

    engagementConfiguration.Reach.EnableNativePush = true;                  
    /* [Optional] whether the native push (MPNS) is activated or not. */

    engagementConfiguration.Reach.ChannelName = "YOUR_PUSH_CHANNEL_NAME";   
    /* [Optional] Provide the same channel name that your application may currently use. */

    /* Initialize Engagement agent with above configuration. */
    EngagementAgent.Instance.Init(engagementConfiguration);

> [!TIP]
> 응용 프로그램의 MPNS 푸시 채널 이름을 지정할 수 있습니다. 기본적으로는 appId를 기준으로 이름이 생성됩니다. Engagement 외부에서 푸시 채널을 사용하려는 경우를 제외하면 이름을 직접 지정할 필요가 없습니다.
> 
> 

### <a name="engagement-initialization"></a>Engagement 초기화
`App.xaml.cs`수정:

* `using` 구문에 추가:
  
      using Microsoft.Azure.Engagement;
* `Application_Launching`에서 `EngagementAgent.Instance.Init` 바로 다음에 `EngagementReach.Instance.Init` 삽입:
  
      private void Application_Launching(object sender, LaunchingEventArgs e)
      {
         EngagementAgent.Instance.Init();
         EngagementReach.Instance.Init();
      }
* `Application_Activated` 메서드에 `EngagementReach.Instance.OnActivated` 삽입:
  
      private void Application_Activated(object sender, ActivatedEventArgs e)
      {
         EngagementAgent.Instance.OnActivated(e);
         EngagementReach.Instance.OnActivated(e);
      }

> [!IMPORTANT]
> `EngagementReach.Instance.Init`은(는) 전용 스레드에서 실행됩니다. 직접 실행할 필요가 없습니다.
> 
> 

## <a name="app-store-submission-considerations"></a>앱 스토어 제출 고려 사항
푸시 알림 사용 시에는 다음 규칙이 적용됩니다.

Microsoft [응용 프로그램 정책] 설명서 2.9항에는 다음과 같은 규칙이 나와 있습니다.

1) 사용자에게 푸시 알림 수신을 수락하도록 요청해야 합니다. 그런 다음 설정에서 푸시 알림을 사용하지 않도록 설정할 방법을 추가합니다.

EngagementReach 개체는 옵트인(opt in) 및 옵트아웃(opt out)을 관리하기 위한 두 메서드인 `EnableNativePush()` 및 `DisableNativePush()`을(를) 제공합니다. 예를 들어 MPNS를 사용하거나 사용하지 않는 토글을 사용하여 설정에서 옵션을 만들 수 있습니다.

또한 Engagement 구성\<windows-phone-sdk-reach-configuration\>을 통해 MPNS를 비활성화할 수도 있습니다.

> 2.9.1) 응용 프로그램은 먼저 제공할 알림을 설명하고 **사용자의 명시적 권한, 즉 옵트인(opt in)을 받아야 합니다**. 또한 **사용자가 푸시 알림 수신을 옵트아웃(opt out)할 수 있는 메커니즘도 제공해야 합니다**. Microsoft 푸시 알림 서비스를 사용하여 제공하는 모든 알림은 사용자에게 제공되는 설명과 일치해야 하며 해당하는 모든 [응용 프로그램 정책][Content Policies] 및 [특정 응용 프로그램 유형에 대한 추가 요구 사항]을 따라야 합니다.
> 
> 

2) 푸시 알림을 너무 많이 사용해서는 안 됩니다. Engagement는 사용자에 대한 알림을 처리합니다.

> 2.9.2) 응용 프로그램은, 그리고 해당 응용 프로그램이 Microsoft 푸시 알림 서비스를 사용할 때는 Microsoft 푸시 알림 서비스의 네트워크 용량이나 대역폭을 과도하게 사용해서는 안 되며, Microsoft의 합리적인 결정에 따라 과다한 푸시 알림으로 Windows Phone 또는 기타 Microsoft 장치나 서비스에 지나친 부담을 주어서는 안 됩니다. 또한 Microsoft 네트워크 또는 서버나 Microsoft 푸시 알림 서비스에 연결된 타사 서버 또는 네트워크를 손상시키거나 작동을 방해해서도 안 됩니다.
> 
> 

3) MPNS를 사용하여 중요한 정보를 보내지 마세요. Engagement에서는 MPNS를 사용하므로 이 규칙은 Engagement 프런트 엔드 내에서 만든 캠페인에도 적용됩니다.

> 2.9.3) Microsoft 푸시 알림 서비스를 사용하여 중요 업무용 알림 또는 생사에 영향을 줄 수 있는 알림을 보내서는 안 됩니다. 여기에는 의료 장치나 상태와 관련된 중요 알림을 비롯한 다수의 알림이 포함됩니다. Microsoft는 Microsoft 푸시 알림 서비스 또는 Microsoft 푸시 알림 서비스 알림 배달 기능을 중단/오류 없이 사용할 수 있거나 해당 기능이 실시간으로 제공된다는 어떤 보증도 거부합니다.
> 
> 

**이러한 권장 사항을 지키지 않는 경우에는 응용 프로그램이 유효성 검사 프로세스를 통과한다고 보장할 수 없습니다.**

## <a name="handle-data-push-optional"></a>데이터 푸시 처리(선택 사항)
응용 프로그램이 도달률 데이터 푸시를 수신할 수 있도록 하려면 EngagementReach 클래스의 두 이벤트를 구현해야 합니다.

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

그런 다음 `Application_Launching` 메서드 내에서 `App.xaml.cs` 클래스의 사용자 지정 개체를 사용하여 `EngagementReach.Instance.Handler` 필드의 콘텐츠를 설정합니다.

**샘플 코드:**

    private void Application_Launching(object sender, LaunchingEventArgs e)
    {
       // your app initialization 
       EngagementReach.Instance.Handler = new ExampleReachHandler();
       // Engagement Agent and Reach initialization
    }

> [!NOTE]
> Engagement는 기본적으로 자체 `EngagementReachHandler` 구현을 사용합니다. 따라서 구현을 직접 작성할 필요는 없으며 직접 작성하더라도 모든 메서드를 재정의하지는 않아도 됩니다. 기본 동작에서는 Engagement 기준 개체가 선택됩니다.
> 
> 

### <a name="layouts"></a>레이아웃
도달률에서는 기본적으로 DLL의 포함된 리소스를 사용하여 알림과 페이지를 표시합니다.

그러나 원하는 리소스를 사용하여 이러한 구성 요소에 브랜드를 반영할 수 있습니다.

서브클래스에서 `EngagementReachHandler` 메서드를 재정의하여 Engagement에서 특정 레이아웃을 사용하도록 명령할 수 있습니다.

**샘플 코드:**

    // In your subclass of EngagementReachHandler

    public override string GetTextViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetWebViewAnnouncementUri()
    {
       // return the path of your own xaml
    }

    public override string GetPollUri()
    {
       // return the path of your own xaml
    }

    public override EngagementNotificationView CreateNotification(EngagementNotificationViewModel viewModel)
    {
       // return a new instance of your own notification
    }

> [!TIP]
> `CreateNotification` 메서드는 null을 반환할 수 있습니다. 그러면 알림이 표시되지 않으며 도달률 캠페인이 삭제됩니다.
> 
> 

레이아웃 구현을 간소화할 수 있도록 코드의 기준으로 사용 가능한 자체 xaml도 제공됩니다. 이 xaml은 Engagement SDK 보관 파일 위치(/src/reach/)에 있습니다.

> [!WARNING]
> 제공되는 원본은 Microsoft에서 사용하는 것과 정확하게 동일합니다. 따라서 해당 원본을 직접 수정하는 경우에는 네임스페이스와 이름을 변경해야 합니다.
> 
> 

### <a name="notification-position"></a>알림 위치
기본적으로 앱 내 알림은 응용 프로그램 왼쪽 아래에 표시됩니다. 이 동작은 `EngagementReachHandler` 개체의 `GetNotificationPosition` 메서드를 재정의하여 변경할 수 있습니다.

    // In your subclass of EngagementReachHandler

    public override EngagementReachHandler.NotificationPosition GetNotificationPosition(EngagementNotificationViewModel viewModel)
    {
       // return a value of the EngagementReachHandler.NotificationPosition enum (TOP or BOTTOM)
    }

현재는 `BOTTOM`(기본값) 및 `TOP` 위치 중에서 선택할 수 있습니다.

### <a name="launch-message"></a>시작 메시지
사용자가 시스템 알림(알림)을 클릭하면 앱이 시작되고 푸시 메시지의 내용이 로드되며 해당 캠페인의 페이지가 표시됩니다.

응용 프로그램을 시작한 후부터 페이지가 표시될 때까지 네트워크 속도에 따라 지연이 발생합니다.

콘텐츠가 로드 중임을 사용자에게 알리려면 진행률 표시줄이나 진행률 표시기와 같은 시각적 정보를 제공해야 합니다. Engagement에서 이 과정을 직접 처리할 수는 없으며, 처리에 사용할 수 있는 몇 가지 처리기를 제공합니다.

콜백을 구현하려면 다음을 실행합니다.

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

콜백은 `App.xaml.cs` 파일의 `Application_Launching` 메서드에서 설정할 수 있으며 `EngagementReach.Instance.Init()` 호출 앞에 설정하는 것이 좋습니다.

> [!TIP]
> UI 스레드에서 각 처리기를 호출합니다. 따라서 MessageBox 또는 UI 관련 항목을 사용할 때는 별도의 작업을 수행할 필요가 없습니다.
> 
> 

[응용 프로그램 정책]:http://msdn.microsoft.com/library/windows/apps/hh184841(v=vs.105).aspx
[Content Policies]:http://msdn.microsoft.com/library/windows/apps/hh184842(v=vs.105).aspx
[특정 응용 프로그램 유형에 대한 추가 요구 사항]:http://msdn.microsoft.com/library/windows/apps/hh184838(v=vs.105).aspx

