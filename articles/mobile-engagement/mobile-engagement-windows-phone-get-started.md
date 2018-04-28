---
title: Windows Phone Silverlight 앱용 Azure Mobile Engagement 시작
description: Windows Phone Silverlight 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: aa34692f-87f7-47c6-a20c-a1972750bc25
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 9bf51bf6a4e2b84771edb9841e1b0ae18953826c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Windows Phone Silverlight 앱용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 Windows Phone Silverlight 응용 프로그램에서 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다.
이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. 여기서는 MPNS(Microsoft 푸시 알림 서비스)를 사용하여 푸시 알림을 받고 기본적인 데이터를 수집하는 빈 Windows Phone Silverlight 앱을 만듭니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/services/mobile-engagement/)를 참조하세요.

> [!NOTE]
> Windows Phone 8.1 및 이전 버전 프로젝트는 Visual Studio 2017에서 지원되지 않습니다.  자세한 내용은 [Visual Studio 2017 플랫폼 대상 지정 및 호환성](https://www.visualstudio.com/en-us/productinfo/vs2017-compatibility-vs)을 참조하세요.

> [!NOTE]
> Windows Phone 8.1(비 Silverlight)을 대상으로 하는 경우 [Windows 유니버설 자습서](mobile-engagement-windows-store-dotnet-get-started.md)를 참조하세요.
> 
> 

이 자습서를 사용하려면 다음이 필요합니다.

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] Nuget 패키지

> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started)을 참조하세요.
> 
> 

## <a id="setup-azme"></a>Windows Phone 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement Windows Phone SDK 통합](mobile-engagement-windows-phone-sdk-overview.md)

여기서는 통합을 시연하기 위해 Visual Studio를 사용하여 기본적인 앱을 만듭니다.

### <a name="create-a-new-windows-phone-silverlight-project"></a>새 Windows Phone Silverlight 프로젝트 만들기
다음 단계에서는 Visual Studio 2015를 사용한다고 가정하지만 이전 버전의 Visual Studio에서도 단계는 유사합니다. 

1. Visual Studio를 시작하고 **홈** 화면에서 **새 프로젝트**를 선택합니다.
2. 팝업에서 **Windows 8** -> **Windows Phone** -> **비어 있는 앱(Windows Phone Silverlight)** 을 선택합니다. 앱 **이름** 및 **솔루션 이름**을 입력하고 **확인**을 클릭합니다.
   
    ![][1]
3. **Windows Phone 8.0** 또는 **Windows Phone 8.1**을 대상으로 선택할 수 있습니다.

이제 Azure Mobile Engagement SDK를 통합할 새 Windows Phone Silverlight 앱을 만들었습니다.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
1. 프로젝트에서 [MicrosoftAzure.MobileEngagement] Nuget 패키지를 설치합니다.
2. Properties 폴더 아래에 있는 `WMAppManifest.xml`을 열고 `<Capabilities />` 태그에 다음이 선언되어 있는지 확인합니다(선언되어 있지 않은 경우 추가).
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. 이제 앞서 Mobile Engagement 앱에 대해 복사한 연결 문자열을 `Resources\EngagementConfiguration.xml` 파일의 `<connectionString>` 태그와 `</connectionString>` 태그 사이에 붙여넣습니다.
   
    ![][3]
4. 파일 `App.xaml.cs`에서:
   
    a. `using` 구문 추가:
   
            using Microsoft.Azure.Engagement;
   
    나. SDK를 `Application_Launching` 메서드에서 초기화:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    다. 다음 코드를 `Application_Activated`에 삽입합니다.
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a id="monitor"></a>실시간 모니터링 사용
데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

1. MainPage.xaml.cs에서 `using` 문을 추가합니다.
   
        using Microsoft.Azure.Engagement;
2. **PhoneApplicationPage** 앞에 있는 **MainPage**의 기본 클래스를 **EngagementPage**로 바꿉니다.
   
        class MainPage : EngagementPage 
3. `MainPage.xml` 파일에서:
   
    a. 다음 줄을 네임스페이스 선언에 추가합니다.
   
            xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    나. XML 태그 이름의 `phone:PhoneApplicationPage`를 `engagement:EngagementPage`로 바꿉니다.

## <a id="monitor"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용
Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>MPNS 푸시 알림을 받도록 앱 설정
새 기능을 `WMAppManifest.xml` 파일에 추가합니다.

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>도달률 SDK 초기화
1. 다음과 같이 `App.xaml.cs`에서 에이전트 초기화 직후 **Application_Launching** 함수의 `EngagementReach.Instance.Init();`를 호출합니다.
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. 다음과 같이 `App.xaml.cs`에서 에이전트 초기화 직후 **Application_Activated** 함수의 `EngagementReach.Instance.OnActivated(e);`를 호출합니다.
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

모든 설정을 완료했습니다. 이제 이 기본 통합을 제대로 수행했는지 확인합니다.

## <a id="send"></a>앱에 알림 보내기
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

이제 앱이 열려 있는 경우 앱 내 알림으로 표시되고 그렇지 않을 경우 알림 메시지로 표시되는 알림이 장치에 다음과 같이 나타납니다. 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
