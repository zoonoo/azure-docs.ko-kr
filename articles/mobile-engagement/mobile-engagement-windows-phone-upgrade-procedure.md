---
title: Windows Phone Silverlight SDK 업그레이드 절차
description: Azure Mobile Engagement용 Windows Phone Silverlight SDK 업그레이드 절차
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 87130026-9759-4659-9184-788a3627a165
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: na
ms.topic: article
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 489b005c37ddb842a2501e89c07fb34b091346e5
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="windows-phone-silverlight-sdk-upgrade-procedures"></a>Windows Phone Silverlight SDK 업그레이드 절차
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

이전 버전의 SDK를 응용 프로그램에 이미 통합한 경우에는 SDK를 업그레이드할 때 다음 사항을 고려해야 합니다.

여러 SDK 버전을 건너뛴 경우에는 여러 절차를 수행해야 할 수 있습니다. 예를 들어 0.10.1에서 0.11.0으로 마이그레이션하는 경우에는 먼저 "0.9.0에서 0.10.1로 마이그레이션" 절차를 수행한 후에 "0.10.1에서 0.11.0으로 마이그레이션" 절차를 수행해야 합니다.

## <a name="from-200-to-330"></a>2.0.0에서 3.3.0으로
### <a name="test-logs"></a>테스트 로그
이제 SDK에서 생성된 콘솔 로그를 사용/사용 안 함/필터링할 수 있습니다. 이를 사용자 지정하려면 속성 `EngagementAgent.Instance.TestLogEnabled`를 `EngagementTestLogLevel` 열거형에서 사용 가능한 값 중 하나로 업데이트합니다. 예를 들어 다음과 같습니다.

            EngagementAgent.Instance.TestLogLevel = EngagementTestLogLevel.Verbose;
            EngagementAgent.Instance.Init();

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

그런 다음 새 Microsoft Azure Engagement NuGet 패키지를 프로젝트에 설치합니다. 해당 패키지는 [Nuget](http://www.nuget.org/packages/MicrosoftAzure.MobileEngagement)에서 직접 찾을 수 있습니다. 이 작업을 수행하면 Engagement에서 사용하는 모든 리소스 파일이 바뀌며 프로젝트 참조에 새 Engagement DLL이 추가됩니다.

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
4. Capptain 그림과 같은 기타 리소스도 "Engagement"를 사용하도록 이름이 바뀌었습니다.

### <a name="application-id--sdk-key"></a>응용 프로그램 ID/SDK 키
Engagement에서는 연결 문자열을 사용합니다. 따라서 Mobile Engagement에서는 응용 프로그램 ID와 SDK 키를 지정할 필요가 없으며 연결 문자열만 지정하면 됩니다. EngagementConfiguration 파일에서 연결 문자열을 설정할 수 있습니다.

Engagement 구성은 프로젝트의 `Resources\EngagementConfiguration.xml` 파일에서 설정할 수 있습니다.

이 파일을 편집하여 다음을 지정합니다.

* `<connectionString>` 및 `<\connectionString>` 태그 사이의 응용 프로그램 연결 문자열

이 문자열을 런타임에 지정하려는 경우에는 Engagement 에이전트 초기화 전에 다음 메서드를 호출하면 됩니다.

        /* Engagement configuration. */
        EngagementConfiguration engagementConfiguration = new EngagementConfiguration();
        engagementConfiguration.Agent.ConnectionString = "Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}";

        /* Initialize Engagement angent with above configuration. */
        EngagementAgent.Instance.Init(engagementConfiguration);

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

