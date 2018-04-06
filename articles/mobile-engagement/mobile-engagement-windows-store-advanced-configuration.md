---
title: Windows 유니버설 앱 Engagement SDK에 대한 고급 구성
description: Windows 유니버설 앱과 Azure Mobile Engagement 사용에 대한 고급 구성 옵션
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 6d85dd5d-ac07-43ba-bbe4-e91c3a17690b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 10/04/2016
ms.author: piyushjo;ricksal
ms.openlocfilehash: c0f666a0dd3441dfe3de0e82953b78c139091672
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/30/2018
---
# <a name="advanced-configuration-for-windows-universal-apps-engagement-sdk"></a>Windows 유니버설 앱 Engagement SDK에 대한 고급 구성
> [!IMPORTANT]
> Azure Mobile Engagement는 2018/3/31에 사용이 중지됩니다. 이 페이지는 이후에 삭제됩니다.
> 

> [!div class="op_single_selector"]
> * [유니버설 Windows](mobile-engagement-windows-store-advanced-configuration.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
> * [iOS](mobile-engagement-ios-integrate-engagement.md)
> * [Android](mobile-engagement-android-advanced-configuration.md)
> 
> 

이 절차에서는 Azure Mobile Engagement Android 앱에 대한 다양한 고급 구성 옵션을 구성하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 조건
[!INCLUDE [Prereqs](../../includes/mobile-engagement-windows-store-prereqs.md)]

## <a name="advanced-configuration"></a>고급 구성
### <a name="disable-automatic-crash-reporting"></a>자동 작동 중단 보고를 사용하지 않도록 설정
Engagement의 자동 작동 중단 보고를 사용하지 않도록 설정할 수 있습니다. 그러면 처리되지 않은 예외가 발생할 때 Engagement에서 아무 작업도 수행하지 않습니다.

> [!WARNING]
> 이 기능을 사용하지 않도록 설정하면 앱에서 처리되지 않은 작동 중단이 발생해도 Engagement에서 작동 중단을 전송하지 않으며 **또한** 세션 및 작업도 닫지 않습니다.
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

### <a name="disable-real-time-reporting"></a>실시간 보고 사용 안 함
기본적으로 Engagement 서비스는 로그를 실시간으로 보고합니다. 응용 프로그램이 로그를 자주 보고하는 경우 로그를 버퍼링한 후 정기적으로 한 번에 모두 보고하는 것이 좋습니다. 이를 "버스트 모드"라고 합니다.

이 방식을 사용하려면 다음 메서드를 호출합니다.

        EngagementAgent.Instance.SetBurstThreshold(int everyMs);

인수는 **밀리초**단위의 값입니다. 언제든지 실시간 로깅을 다시 활성화하려면 매개 변수를 포함하지 않거나 값을 0으로 설정하여 메서드를 호출합니다.

버스트 모드를 사용하는 경우 배터리 수명은 약간 길어지지만 Engagement 모니터에 영향을 주게 됩니다. 모든 세션 및 작업 기간이 버스트 임계값으로 반올림되므로 버스트 임계값보다 짧은 세션과 작업은 표시되지 않을 수도 있습니다. 30000(30초) 이하의 버스트 임계값을 사용하는 것이 좋습니다. 저장된 로그는 300개 항목으로 제한됩니다. 너무 길게 보내면 일부 로그가 손실될 수 있습니다.

> [!WARNING]
> 1초보다 짧은 기간으로 버스트 임계값을 구성할 수는 없습니다. 버스트 임계값을 1초보다 짧게 구성하면 SDK에는 오류가 포함된 추적이 표시되며, 값은 자동으로 기본값인 0초로 다시 설정됩니다. 그러면 SDK에서 실시간 로그 보고가 트리거됩니다.
> 
> 

[here]:http://www.nuget.org/packages/Capptain.WindowsCS
[NuGet website]:http://docs.nuget.org/docs/start-here/overview
