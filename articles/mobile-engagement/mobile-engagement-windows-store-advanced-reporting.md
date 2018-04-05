---
title: MobileApps Engagement를 사용한 Windows 유니버설 고급 보고
description: Windows 유니버설 앱과 Azure 모바일 Engagement를 통합하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: ea5030bf-73ac-49b7-bc3e-c25fc10e945a
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: 88d28bbc32179698147ab7516cc414fd23ed4bd6
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-reporting-with-the-windows-universal-apps-engagement-sdk"></a>Windows 유니버설 앱 Engagement SDK의 고급 보고
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-advanced-reporting.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-reporting.md)
> 
> 

이 항목에서는 Windows 유니버설 응용 프로그램에서 추가 보고 시나리오를 설명합니다. 이러한 시나리오에서는 [시작](mobile-engagement-windows-store-dotnet-get-started.md) 자습서에서 만든 앱에 적용하도록 선택할 수 있습니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

이 자습서를 시작하기 전에 먼저 직접적이고 간단한 [시작](mobile-engagement-windows-store-dotnet-get-started.md) 자습서를 완료해야 합니다. 이 자습서에서는 선택할 수 있는 추가 옵션에 관해 설명합니다.

## <a name="specifying-engagement-configuration-at-runtime"></a>런타임에 Engagement 구성 지정
Engagement 구성은 [시작](mobile-engagement-windows-store-dotnet-get-started.md) 항목에서 지정한 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 중앙 집중식으로 관리됩니다.

그렇지만 런타임에 구성을 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);



## <a name="recommended-method-overload-your-page-classes"></a>권장 방법: `Page` 클래스 오버로드
Engagement에서 사용자, 세션, 활동, 작동 중단 및 기술 통계를 계산하는 데 필요한 모든 로그의 보고를 활성화하려는 경우 모든 `Page` 서브클래스가 `EngagementPage` 클래스에서 상속하도록 지정합니다.

아래에는 응용 프로그램 페이지에 대한 예제가 나와 있습니다. 응용 프로그램의 모든 페이지에 대해 동일한 작업을 수행할 수 있습니다.

### <a name="c-source-file"></a>C# 소스 파일
페이지 `.xaml.cs` 파일 수정:

* `using` 구문에 추가:
  
      using Microsoft.Azure.Engagement;
* `Page` 및 `EngagementPage` 교체:

**Engagement 사용 안 함:**

        namespace Example
        {
          public sealed partial class ExamplePage : Page
          {
            [...]
          }
        }

**Engagement 사용:**

        using Microsoft.Azure.Engagement;

        namespace Example
        {
          public sealed partial class ExamplePage : EngagementPage
          {
            [...]
          }
        }

> [!IMPORTANT]
> 페이지가 `OnNavigatedTo` 메서드를 재정의하는 경우에는 `base.OnNavigatedTo(e)`을(를) 호출해야 합니다. 그렇지 않으면 활동이 보고되지 않습니다. `EngagementPage`는 `OnNavigatedTo` 메서드 내에서 `StartActivity`를 호출합니다.
> 
> 

### <a name="xaml-file"></a>XAML 파일
페이지 `.xaml` 파일 수정:

* 다음 줄을 네임스페이스 선언에 추가합니다.
  
      xmlns:engagement="using:Microsoft.Azure.Engagement"
* `Page` 및 `engagement:EngagementPage` 교체:

**Engagement 사용 안 함:**

        <Page>
            <!-- layout -->
            ...
        </Page>

**Engagement 사용:**

        <engagement:EngagementPage
            xmlns:engagement="using:Microsoft.Azure.Engagement">
            <!-- layout -->
            ...
        </engagement:EngagementPage >

### <a name="override-the-default-behaviour"></a>기본 동작 재정의
기본적으로 페이지의 클래스 이름은 extra 없이 활동 이름으로 보고됩니다. 클래스에서 "Page" 접미사를 사용하는 경우에는 해당 접미사가 제거됩니다.

이름에 대한 기본 동작을 재정의하려면 다음 코드를 추가합니다.

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

사용자 활동에 대한 추가 정보를 보고하려면 다음 코드를 추가합니다.

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

페이지의 `OnNavigatedTo` 메서드 내에서 이러한 메서드를 호출합니다.

### <a name="alternate-method-call-startactivity-manually"></a>대체 메서드: 수동으로 `StartActivity()` 호출
`Page` 클래스를 오버로드할 수 없거나 오버로드하지 않으려는 경우 `EngagementAgent` 메서드를 직접 호출하여 활동을 시작할 수 있습니다.

페이지의 `OnNavigatedTo` 메서드 내에서 `StartActivity`를 호출하는 것이 좋습니다.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> 세션을 올바르게 종료해야 합니다.
> 
> 응용 프로그램을 닫을 때 Windows 유니버설 SDK는 `EndActivity` 메서드를 자동으로 호출합니다. 따라서 사용자 활동이 변경될 때마다 `StartActivity` 메서드를 호출하는 것이 **상당히** 좋으며 `EndActivity` 메서드는 호출하지 **않는** 것이 좋습니다. 이 메서드는 현재 사용자가 응용 프로그램을 떠났음을 Engagement 서버에 알립니다. 이는 모든 응용 프로그램 로그에 영향을 미칩니다.
> 
> 

## <a name="advanced-reporting"></a>고급 보고
필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고할 수 있습니다. 이렇게 하려면 `EngagementAgent` 클래스에 포함된 다른 메서드를 사용합니다. Engagement API에서는 Engagement의 모든 고급 기능을 사용할 수 있습니다.

자세한 내용은 [Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)을 참조하세요

