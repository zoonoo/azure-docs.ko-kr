---
title: Windows 유니버설 앱 Azure Mobile Engagement 시작
description: Windows 유니버설 앱에 대해 분석 및 푸시 알림과 함께 Azure Mobile Engagement를 사용하는 방법을 알아봅니다.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48103867-7f64-4646-b019-42bd797d38e2
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 4f1de1092458bfc12236802d829a2043dcc74540
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="get-started-with-azure-mobile-engagement-for-windows-universal-apps"></a>Windows 유니버설 앱용 Azure Mobile Engagement 시작
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

이 항목에서는 Azure Mobile Engagement를 사용하여 Windows 유니버설 응용 프로그램에서 구분된 사용자에게 푸시 알림을 보내고 앱 사용량을 파악하는 방법을 설명합니다.
이 자습서에서는 Mobile Engagement를 사용하는 간단한 브로드캐스트 시나리오를 보여 줍니다. WNS(Windows 알림 서비스)를 사용하여 푸시 알림을 받고 기본적인 앱 사용 데이터를 수집하는 빈 Windows 유니버설 앱을 만듭니다.

> [!NOTE]
> Azure Mobile Engagement 서비스는 2018년 3월에 사용 중지되며 현재 기존 고객에게만 제공됩니다. 자세한 내용은 [Mobile Engagement](https://azure.microsoft.com/en-us/services/mobile-engagement/)를 참조하세요.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="set-up-mobile-engagement-for-your-windows-universal-app"></a>Windows 유니버설 앱용 Mobile Engagement 설정
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a id="connecting-app"></a>Mobile Engagement 백 엔드에 앱 연결
이 자습서에서는 데이터를 수집하고 푸시 알림을 보내는 데 필요한 최소 집합인 "기본 통합" 방법을 설명합니다. 전체 통합 설명서는 [Mobile Engagement Windows 유니버설 SDK 통합](mobile-engagement-windows-store-sdk-overview.md)에서 확인할 수 있습니다.

여기서는 통합을 시연하기 위해 Visual Studio를 사용하여 기본적인 앱을 만듭니다.

### <a name="create-a-windows-universal-app-project"></a>Windows 유니버설 앱 프로젝트 만들기
다음 단계에서는 Visual Studio 2015를 사용한다고 가정하지만 이전 버전의 Visual Studio에서도 단계는 유사합니다.

1. Visual Studio를 시작하고 **홈** 화면에서 **새 프로젝트**를 선택합니다.
2. 팝업에서 **Windows** -> **유니버설** -> **비어 있는 앱(유니버설 Windows)**을 선택합니다. 앱 **이름** 및 **솔루션 이름**을 입력하고 **확인**을 클릭합니다.

    ![][1]

이제 다음에 Azure Mobile Engagement SDK를 통합할 Windows 유니버설 앱 프로젝트가 만들어졌습니다.

### <a name="connect-your-app-to-mobile-engagement-backend"></a>Mobile Engagement 백 엔드에 앱 연결
1. 프로젝트에서 [MicrosoftAzure.MobileEngagement] Nuget 패키지를 설치합니다. Windows와 Windows Phone 플랫폼을 모두 대상으로 하는 경우 두 프로젝트에 대해 이 작업을 수행해야 합니다. Windows 8.x 및 Windows Phone 8.1에 대해 동일한 Nuget 패키지는 각 프로젝트에 올바른 플랫폼별 이진 파일을 배치합니다.
2. **Package.appxmanifest**를 열고 다음 기능이 추가되어 있는지 확인합니다.

        Internet (Client)

    ![][2]
3. 이제 앞서 Mobile Engagement 앱에 대해 복사한 연결 문자열을 `Resources\EngagementConfiguration.xml` 파일의 `<connectionString>` 태그와 `</connectionString>` 태그 사이에 붙여넣습니다.

    ![][3]

    > [!TIP]
    > 앱이 Windows와 Windows Phone 플랫폼을 모두 대상으로 하는 경우 여전히 지원하는 각 플랫폼에 하나씩 두 개의 Mobile Engagement 응용 프로그램을 만들어야 합니다. 두 개의 앱을 가지면 대상에 올바른 구분을 생성할 수 있고 각 플랫폼을 대상으로 하는 적절한 알림을 보낼 수 있습니다.

    > [!IMPORTANT]
    > NuGet이 아직 Windows 10 UWP 응용 프로그램에서 SDK 리소스를 자동으로 복사하지 않습니다. Nuget 패키지를 설치하는 경우 표시된 단계(readme.txt)를 따라 수동으로 수행해야 합니다.  

1. 파일 `App.xaml.cs`에서:

    a. `using` 구문 추가:

            using Microsoft.Azure.Engagement;

    나. Engagement를 초기화하는 메서드 추가:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

             //... rest of the code
           }

    다. **OnLaunched** 메서드에서 SDK 초기화:

            protected override void OnLaunched(LaunchActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

    다. **OnActivated** 메서드에 다음을 삽입하고 아직 없는 경우 해당 메서드 추가:

            protected override void OnActivated(IActivatedEventArgs e)
            {
              InitEngagement(e);

              //... rest of the code
            }

## <a id="monitor"></a>실시간 모니터링 사용
데이터 보내기를 시작하고 사용자가 활성 상태인지 확인하려면 Mobile Engagement 백 엔드에 화면(활동)을 하나 이상 보내야 합니다.

1. **MainPage.xaml.cs**에서 다음 `using` 문을 추가합니다.

    Microsoft.Azure.Engagement.Overlay; 사용
2. **MainPage**의 기본 클래스를 **Page**에서 **EngagementPageOverlay**로 변경합니다.

        class MainPage : EngagementPageOverlay
3. 파일 `MainPage.xaml`에서:

    a. 다음 줄을 네임스페이스 선언에 추가합니다.

        xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

    나. XML 태그 이름의 **Page**를 **engagement:EngagementPageOverlay**로 바꿉니다.

> [!IMPORTANT]
> 페이지가 `OnNavigatedTo` 메서드를 재정의하는 경우에는 `base.OnNavigatedTo(e)`을(를) 호출해야 합니다. 그렇지 않으면 활동이 보고되지 않습니다. `EngagementPage`은(는) `OnNavigatedTo` 메서드 내에서 `StartActivity`을(를) 호출합니다. 이 작업은 기본 템플릿에 `OnNavigatedTo` 메서드가 있는 Windows Phone 프로젝트에서 특히 중요합니다.
>
> **Windows 10 Universal 앱**의 경우 위에서 설명한 방법보다 [Windows 유니버설 앱 Engagement SDK의 고급 보고](mobile-engagement-windows-store-advanced-reporting.md)의 “권장 방법: Page 클래스 오버로드” 섹션에서 권장하는 방법을 사용합니다.

## <a id="monitor"></a>실시간 모니터링과 앱 연결
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a id="integrate-push"></a>푸시 알림 및 앱 내 메시징 사용
Mobile Engagement에서는 캠페인 컨텍스트에서 푸시 알림 및 앱 내 메시징을 사용하여 사용자와 상호 작용하고 사용자에게 메시지를 보낼 수 있습니다. Mobile Engagement 포털에서는 이 모듈을 도달률이라고 합니다.
다음 섹션에서는 이러한 알림과 메시지를 받도록 앱을 설정합니다.

### <a name="enable-your-app-to-receive-wns-push-notifications"></a>WNS 푸시 알림을 받도록 앱 설정
1. `Package.appxmanifest` 파일의 **응용 프로그램** 탭에서 **알림**에 있는 **알림 가능:**을 **예**로 설정합니다.

    ![][5]

### <a name="initialize-the-reach-sdk"></a>도달률 SDK 초기화
`App.xaml.cs`에서 에이전트 초기화 직후 **InitEngagement** 함수에서 **EngagementReach.Instance.Init(e);**를 호출합니다.

        private void InitEngagement(IActivatedEventArgs e)
        {
           EngagementAgent.Instance.Init(e);
           EngagementReach.Instance.Init(e);
        }

알림을 보낼 준비가 되었습니다. 그런 다음 이 기본 통합을 제대로 수행했는지 확인합니다.

### <a name="grant-access-to-mobile-engagement-to-send-notifications"></a>알림을 보내도록 Mobile Engagement 액세스 권한 부여
1. 웹 브라우저에서 [Windows 스토어 개발자 센터]를 열어 로그인하고 필요한 경우 계정을 만드십시오.
2. 오른쪽 위 모서리에 있는 **대시보드**를 클릭한 다음 왼쪽 패널 메뉴에서 **새 앱 만들기**를 클릭합니다.

    ![][9]
3. 해당 이름을 예약하여 앱을 만듭니다.

    ![][10]
4. 앱이 만들어지면 왼쪽 메뉴에서 **서비스 -> 푸시 알림**으로 이동합니다.

    ![][11]
5. 푸시 알림 섹션에서 **Live 서비스 사이트** 링크를 클릭합니다.

    ![][12]
6. 푸시 자격 증명 섹션으로 이동합니다. **앱 설정** 섹션에 있는지 확인한 다음 **패키지 SID** 및 **클라이언트 암호**를 복사합니다.

    ![][13]
7. Mobile Engagement 포털의 **설정**으로 이동하여 왼쪽의 **네이티브 푸시** 섹션을 클릭합니다. 그런 다음 **편집** 단추를 클릭하여 다음과 같이 **패키지 SID(보안 식별자)** 및 **암호 키**를 입력합니다.

    ![][6]
8. 마지막으로 Visual Studio 앱과 앱 스토어에 만들어진 이 앱이 연결되어 있는지 확인합니다. Visual Studio의 **스토어에 앱 연결**을 클릭합니다.

    ![][7]

## <a id="send"></a>앱에 알림 보내기
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

앱이 실행 중인 경우 앱 내 알림이 표시됩니다. 그렇지 않고 앱이 닫힌 경우 알림 메시지가 표시됩니다.
앱 내 알림이 표시되지만 알림 메시지가 표시되지 않고 앱을 Visual Studio의 디버그 모드로 실행 중인 경우 도구 모음의 **수명 주기 이벤트 -> 일시 중단**을 사용하여 앱이 일시 중단되었는지 확인합니다. Visual Studio에서 응용 프로그램을 디버깅하는 동안 홈 단추를 클릭한 경우 일시 중단되지 않을 수 있으며, 앱 내 알림으로 표시되어 있는 동안에는 알림 메시지가 표시되지 않습니다.  

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows 스토어 개발자 센터]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png
