---
title: Windows 유니버설 앱 Engagement SDK 통합
description: Windows 유니버설 앱과 Azure 모바일 Engagement를 통합하는 방법
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 71236b68-5ebd-44aa-8c82-c7ca8098ea05
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: dc075ffe6565b4ef60e77f74ba4f065629c99952
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-universal-apps-engagement-sdk-integration"></a>Windows 유니버설 앱 Engagement SDK 통합
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-integrate-engagement.md) 
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md) 
> * [iOS](mobile-engagement-ios-integrate-engagement.md) 
> * [Android](mobile-engagement-android-integrate-engagement.md) 
> 
> 

이 절차에서는 Windows 유니버설 응용 프로그램에서 Engagement의 분석 및 모니터링 기능을 활성화하는 가장 간단한 방법을 설명합니다.

다음 단계만 수행하면 사용자, 세션, 활동, 작동 중단 및 기술과 관련된 모든 통계를 계산하는 데 필요한 로그 보고를 활성화할 수 있습니다. 이벤트, 오류, 작업 등의 기타 통계는 응용 프로그램별로 다르므로, 해당 통계를 계산하는 데 필요한 로그 보고는 Engagement API를 사용하여 수동으로 수행해야 합니다. 관련 설명은 [Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)을 참조하세요.

## <a name="supported-versions"></a>지원되는 버전
Windows 유니버설 앱용 Mobile Engagement SDK는 다음을 대상으로 하는 Windows 런타임 및 유니버설 Windows 플랫폼 응용 프로그램에만 통합할 수 있습니다.

* Windows 8
* Windows 8.1
* Windows Phone 8.1
* Windows 10(데스크톱 및 모바일 제품군)

> [!NOTE]
> Windows Phone Silverlight를 대상으로 하는 경우 [Windows Phone Silverlight 통합 절차](mobile-engagement-windows-phone-integrate-engagement.md)를 참조하세요.
> 
> 

## <a name="install-the-mobile-engagement-universal-apps-sdk"></a>Mobile Engagement 유니버설 Apps SDK 설치
### <a name="all-platforms"></a>모든 플랫폼
Windows 유니버설 앱용 Mobile Engagement SDK는 *MicrosoftAzure.MobileEngagement*로 불리는 Nuget 패키지로 사용할 수 있습니다. Visual Studio Nuget 패키지 관리자에서 설치할 수 있습니다.

### <a name="windows-8x-and-windows-phone-81"></a>Windows 8.x 및 Windows Phone 8.1
NuGet은 응용 프로그램 프로젝트의 루트에 있는 `Resources` 폴더에서 SDK 리소스를 자동으로 배포합니다.

### <a name="windows-10-universal-windows-platform-applications"></a>Windows 10 유니버설 Windows 플랫폼 응용 프로그램
NuGet이 아직 UWP 응용 프로그램에서 SDK 리소스를 자동으로 배포하지 않습니다. NuGet에서 리소스 배포가 다시 도입될 때까지 수동으로 배포해야 합니다.

1. 파일 탐색기를 엽니다.
2. *%USERPROFILE%\\.nuget\packages\MicrosoftAzure.MobileEngagement\\**x.x.x**\\content\win81* 위치로 이동합니다(**x.x.x**는 설치하는 Engagement 버전)
3. 파일 탐색기에서 **Resources** 폴더를 Visual Studio의 프로젝트 루트에 끌어다 놓습니다.
4. Visual Studio에서 프로젝트를 선택하고 **솔루션 탐색기** 맨 위에서 **모든 파일 표시** 아이콘을 활성화합니다.
5. 일부 파일이 프로젝트에 포함되지 않았습니다. 한 번에 가져오려면 **Resources** 폴더를 마우스 오른쪽 단추로 클릭하고 **프로젝트에서 제외**를 클릭한 다음 **Resources** 폴더를 다시 한 번 마우스 오른쪽 단추로 클릭하고 **프로젝트에 포함**을 다시 클릭하여 전체 폴더를 다시 포함합니다. 이제 **Resources** 폴더의 모든 파일이 프로젝트에 포함되었습니다.

## <a name="add-the-capabilities"></a>기능 추가
Engagement SDK가 올바르게 작동하려면 Windows SDK의 일부 기능이 필요합니다.

`Package.appxmanifest` 파일을 열고 다음 기능이 선언되었는지 확인합니다.

* `Internet (Client)`

## <a name="initialize-the-engagement-sdk"></a>Engagement SDK 초기화
### <a name="engagement-configuration"></a>Engagement 구성
Engagement 구성은 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 중앙 집중식으로 관리됩니다.

이 파일을 편집하여 다음을 지정합니다.

* `<connectionString>` 및 `<\connectionString>` 태그 사이의 응용 프로그램 연결 문자열

이 문자열을 런타임에 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

          /* Engagement configuration. */
          EngagementConfiguration engagementConfiguration = new EngagementConfiguration();

          /* Set the Engagement connection string. */
          engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

          /* Initialize Engagement angent with above configuration. */
          EngagementAgent.Instance.Init(e, engagementConfiguration);

응용 프로그램의 연결 문자열은 Azure Portal에 표시됩니다.

### <a name="engagement-initialization"></a>Engagement 초기화
새 프로젝트를 만들 때는 `App.xaml.cs` 파일이 생성됩니다. 이 클래스는 `Application` 에서 상속하며, 여러 중요 메서드를 포함합니다. Engagement SDK를 초기화할 때도 이 클래스가 사용됩니다.

`App.xaml.cs`파일을 수정합니다.

* `using` 구문에 추가:
  
      using Microsoft.Azure.Engagement;
* 모든 호출에 대해 한 번씩 Engagement 초기화를 공유하도록 메서드를 정의합니다.
  
      private void InitEngagement(IActivatedEventArgs e)
      {
        EngagementAgent.Instance.Init(e);
  
        // or
  
        EngagementAgent.Instance.Init(e, engagementConfiguration);
      }
* `OnLaunched` 메서드에서 `InitEngagement`를 호출합니다.
  
      protected override void OnLaunched(LaunchActivatedEventArgs e)
      {
        InitEngagement(e);
      }
* 사용자 지정 체계, 다른 응용 프로그램 또는 명령줄을 사용하여 응용 프로그램을 시작하면 `OnActivated` 메서드가 호출됩니다. 또한 앱을 활성화할 때 Engagement SDK를 시작해야 합니다. 이렇게 하려면 `OnActivated` 메서드를 재정의합니다.
  
      protected override void OnActivated(IActivatedEventArgs args)
      {
        InitEngagement(args);
      }

> [!IMPORTANT]
> 응용 프로그램의 다른 위치에는 Engagement 초기화를 추가하지 않는 것이 좋습니다.
> 
> 

## <a name="basic-reporting"></a>기본 보고
### <a name="recommended-method-overload-your-page-classes"></a>권장 방법: `Page` 클래스 오버로드
Engagement에서 사용자, 세션, 활동, 작동 중단 및 기술 통계를 계산하는 데 필요한 모든 로그의 보고를 활성화하려는 경우 모든 `Page` 서브클래스가 `EngagementPage` 클래스에서 상속하도록 지정하면 됩니다.

아래에는 응용 프로그램 페이지에 대해 이 작업을 수행하는 방법의 예제가 나와 있습니다. 응용 프로그램의 모든 페이지에 대해 동일한 작업을 수행할 수 있습니다.

#### <a name="c-source-file"></a>C# 소스 파일
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
> 페이지가 `OnNavigatedTo` 메서드를 재정의하는 경우에는 `base.OnNavigatedTo(e)`을(를) 호출해야 합니다. 그렇지 않으면 활동이 보고되지 않습니다. `EngagementPage`은(는) `OnNavigatedTo` 메서드 내에서 `StartActivity`을(를) 호출합니다.
> 
> 

#### <a name="xaml-file"></a>XAML 파일
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

#### <a name="override-the-default-behaviour"></a>기본 동작 재정의
기본적으로 페이지의 클래스 이름은 extra 없이 활동 이름으로 보고됩니다. 클래스에서 "Page" 접미사를 사용하는 경우에는 해당 접미사도 제거됩니다.

이름의 기본 동작을 재정의하려면 코드에 다음 코드 조각만 추가하면 됩니다.

        // in the .xaml.cs file
        protected override string GetEngagementPageName()
        {
          /* your code */
          return "new name";
        }

활동과 함께 추가 정보를 보고하려는 경우에는 코드에 다음 코드 조각을 추가할 수 있습니다.

        // in the .xaml.cs file
        protected override Dictionary<object,object> GetEngagementPageExtra()
        {
          /* your code */
          return extra;
        }

페이지의 `OnNavigatedTo` 메서드 내에서 이러한 메서드를 호출합니다.

### <a name="alternate-method-call-startactivity-manually"></a>대체 메서드: 수동으로 `StartActivity()` 호출
`Page` 클래스를 오버로드할 수 없거나 오버로드하지 않으려는 경우 `EngagementAgent` 메서드를 직접 호출하여 활동을 시작할 수 있습니다.

페이지의 `OnNavigatedTo` 메서드 내에서 `StartActivity`을(를) 호출하는 것이 좋습니다.

            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
              base.OnNavigatedTo(e);
              EngagementAgent.Instance.StartActivity("MyPage");
            }

> [!IMPORTANT]
> 세션을 올바르게 종료해야 합니다.
> 
> 응용 프로그램을 닫을 때 Windows 유니버설 SDK는 `EndActivity` 메서드를 자동으로 호출합니다. 따라서 사용자 활동이 변경될 때마다 `StartActivity` 메서드를 호출하는 것이 **상당히** 좋으며 `EndActivity` 메서드는 호출하지 **않는** 것이 좋습니다. 이 메서드는 현재 사용자가 응용 프로그램을 떠난 Engagement 서버에 보내며 이는 모든 응용 프로그램 로그에 영향을 미칩니다.
> 
> 

## <a name="advanced-reporting"></a>고급 보고
필요에 따라 응용 프로그램 관련 이벤트, 오류 및 작업을 보고할 수 있습니다. 이렇게 하려면 `EngagementAgent` 클래스에 포함된 다른 메서드를 사용합니다. Engagement API에서는 Engagement의 모든 고급 기능을 사용할 수 있습니다.

자세한 내용은 [Windows 유니버설 앱에서 고급 Mobile Engagement 태깅 API를 사용하는 방법](mobile-engagement-windows-store-use-engagement-api.md)을 참조하세요

## <a name="advanced-configuration"></a>고급 구성
### <a name="disable-automatic-crash-reporting"></a>자동 작동 중단 보고를 사용하지 않도록 설정
Engagement의 자동 작동 중단 보고를 사용하지 않도록 설정할 수 있습니다. 이렇게 하면 처리되지 않은 예외 발생 시 Engagement에서 아무런 작업도 수행하지 않습니다.

> [!WARNING]
> 이 기능을 사용하지 않도록 설정하려는 경우 앱에서 처리되지 않은 작동 중단이 발생해도 Engagement에서 작동 중단을 전송하지 않으며 **또한** 세션 및 작업도 닫지 않습니다.
> 
> 

자동 작동 중단 보고를 사용하지 않도록 설정하려면 구성을 선언한 방식에 따라 사용자 지정합니다.

#### <a name="from-engagementconfigurationxml-file"></a>`EngagementConfiguration.xml` 파일에서
`<reportCrash>` 및 `</reportCrash>` 태그 간의 작동 중단 보고를 `false`(으)로 설정합니다.

#### <a name="from-engagementconfiguration-object-at-run-time"></a>런타임에 `EngagementConfiguration` 개체에서
EngagementConfiguration 개체를 사용하여 작동 중단 보고를 false로 설정합니다.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Disable Engagement crash reporting. */
        engagementConfiguration.Agent.ReportCrash = false;

### <a name="burst-mode"></a>버스트 모드
기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 매우 자주 보고하는 경우에는 로그를 버퍼링한 다음 정기적으로 한꺼번에 보고하는 것이 보다 효율적입니다. 이러한 방식을 "버스트 모드"라고 합니다.

이 방식을 사용하려면 다음 메서드를 호출합니다.

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

인수는 **밀리초**단위의 값입니다. 언제든지 실시간 로깅을 다시 활성화하려면 매개 변수를 포함하지 않거나 값을 0으로 설정하여 메서드를 호출하면 됩니다.

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 30000(30초) 이하의 버스트 임계값을 사용하는 것이 좋습니다. 저장된 로드는 300개 항목으로 제한됩니다. 보내는 로그가 너무 길면 일부 로그가 손실될 수 있습니다.

> [!WARNING]
> 1초보다 짧은 기간으로 버스트 임계값을 구성할 수는 없습니다. 버스트 임계값을 1초보다 짧게 구성하면 SDK에는 오류가 포함된 추적이 표시되며, 값은 자동으로 기본값인 0초로 다시 설정됩니다. 그러면 SDK에서 실시간 로그 보고가 트리거됩니다.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview

