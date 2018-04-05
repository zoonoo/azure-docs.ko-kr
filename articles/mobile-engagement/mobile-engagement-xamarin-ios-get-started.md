---
title: Xamarin.iOS용 Azure Mobile Engagement 시작
description: Xamarin.iOS 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: xamarin
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 0448209e-fff6-47bd-985c-2cf074bac12f
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 78ba376eb4552468810a1de72d53ceb59ec9d395
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Xamarin.iOS 앱용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 토픽에서는 Azure Mobile Engagement를 사용하여 앱 사용법을 이해하고 Xamarin.iOS 응용 프로그램의 분할된 사용자에게 푸시 알림을 보내는 방법을 보여 줍니다.
이 자습서에서는 기본 데이터를 수집하고 APNS(Apple 푸시 알림 시스템)를 사용하여 푸시 알림을 받는 빈 Xamarin.iOS 앱을 만듭니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)를 참조하세요.

이 자습서를 사용하려면 다음이 필요합니다.

* [Xamarin Studio](http://xamarin.com/studio). Xamarin이 포함된 Visual Studio를 사용할 수도 있지만 이 자습서에서는 Xamarin Studio를 사용합니다. 또한 설치 지침은 [Visual Studio 및 Xamarin을 위한 설정 및 설치](https://msdn.microsoft.com/library/mt613162.aspx)를 참조하세요. 
* [Mobile Engagement Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [!NOTE]
> 이 자습서를 완료하려면 활성 Azure 계정이 있어야 합니다. 계정이 없는 경우 몇 분 만에 평가판 계정을 만들 수 있습니다. 자세한 내용은 [Azure 무료 체험](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started)을 참조하세요.
> 
> 

## <a id="setup-azme"></a>iOS 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다.

여기서는 통합을 시연하기 위해 Xamarin을 사용하여 기본적인 앱을 만듭니다.

### <a name="create-a-new-xamarinios-project"></a>새 Xamarin.iOS 프로젝트 만들기
1. Xamarin Studio를 시작합니다. **파일** -> **새로 만들기** -> **솔루션**으로 이동합니다. 
   
    ![][1]
2. **단일 보기 앱**을 선택한 후 선택한 언어가 **C#**인지 확인하고 **다음**을 클릭합니다.
   
    ![][2]
3. **앱 이름** 및 **조직 식별자**를 입력하고 **다음**을 클릭합니다. 
   
    ![][3]
   
   > [!IMPORTANT]
   > iOS 앱을 배포하는 데 궁극적으로 사용할 게시 프로필에서 여기에 있는 번들 식별자와 정확히 일치하는 앱 ID를 사용하는지 확인합니다. 
   > 
   > 
4. 필요한 경우 **프로젝트 이름**, **솔루션 이름** 및 **위치**를 업데이트하고 **만들기**를 클릭합니다.
   
    ![][4]

Xamarin Studio가 Mobile Engagement를 통합할 데모 앱을 만듭니다. 

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
1. 솔루션 창에서 **패키지** 폴더를 마우스 오른쪽 단추로 클릭하고 **패키지 추가...**를 선택합니다.
   
    ![][5]
2. **Microsoft Azure Mobile Engagement Xamarin SDK**를 검색하고 이를 솔루션에 추가합니다.  
   
    ![][6]
3. **AppDelegate.cs**를 열고 다음 using 문을 추가합니다.
   
        using Microsoft.Azure.Engagement.Xamarin;
4. **FinishedLaunching** 메서드에서 다음을 추가하여 Mobile Engagement 백 엔드 연결을 초기화합니다. **ConnectionString**을 추가해야 합니다. 이 코드는 또한 대체하려는 Mobile Engagement SDK로 추가되는 더미 **NotificationIcon**을 사용합니다. 
   
        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

## <a id="monitor"></a>실시간 모니터링 사용
데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면을 하나 이상 보내야 합니다.

1. **ViewController.cs**를 열고 다음 using 문을 추가합니다.
   
        using Microsoft.Azure.Engagement.Xamarin;
2. `ViewController`이 `UIViewController`에서 `EngagementViewController`로 상속하는 클래스를 대체합니다. 

## <a id="monitor"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용
Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자 및 도달률과 상호 작용할 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

### <a name="modify-your-application-delegate"></a>응용 프로그램 대리자 수정
1. **AppDelegate.cs**를 열고 다음 using 문을 추가합니다.
   
        using System; 
2. 이제 `FinishedLaunching` 메서드 내에서 다음을 추가하여 `EngagementAgent.init(...)` 뒤에 푸시 메시지를 등록합니다.
   
        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }
3. 마지막으로 다음 메서드를 업데이트 또는 추가합니다.
   
        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }
   
        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }
   
        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }
4. 솔루션의 **Info.plist** 파일에서 **번들 식별자**가 Apple 개발자 센터의 프로비전 프로필에 있는 **앱 ID**와 일치하는지 확인합니다. 
   
    ![][7]
5. 동일한 **Info.plist** 파일에서 **백그라운드 모드 사용** 및 **원격 알림**을 선택했는지 확인합니다. 
   
     ![][8]
6. 이 게시 프로필과 연결한 장치에서 앱을 실행합니다. 

[!INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
