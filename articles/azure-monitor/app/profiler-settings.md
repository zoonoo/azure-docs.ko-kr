---
title: Azure Application Insights Profiler 설정 블레이드 | Microsoft Docs
description: Profiler 상태 보기 및 프로파일링 세션 시작
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: b656fb521ad72256e91de63e96aa261f1e94bd13
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/08/2019
ms.locfileid: "54082428"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler 구성

## <a name="profiler-settings-page"></a>Profiler 설정 페이지

Profiler 설정 페이지는 **Profiler** 단추를 눌러 Application Insights 성능 페이지에서 열 수 있습니다.

![프로파일러 구성 창 항목][configure-profiler-entry]

Application Insights Profiler 구성 페이지는 다음 4가지 기능을 제공합니다. 
1. **지금 프로파일링** - 이 단추를 클릭하면 이 Application Insights 인스턴스에 연결된 모든 앱에 대해 프로파일링 세션이 시작됩니다.
1. **연결된 앱** - 이 Application Insights 리소스로 Profiler를 보내는 애플리케이션의 목록입니다.
1. **진행 중인 세션** - **지금 프로파일링**을 누르면 세션의 상태가 여기에 표시됩니다.
1. **최근 프로파일링 세션** - 이전 프로파일링 세션에 대한 정보가 표시됩니다.

![주문형 프로파일러][profiler-on-demand]

## <a name="app-service-environments-ase"></a>ASE(App Service 환경)
ASE가 구성된 방식에 따라 에이전트 상태를 확인하기 위한 호출이 차단될 수 있습니다. 이 페이지에는 실제로는 실행 중인 에이전트가 실행되고 있지 않은 것으로 표시됩니다. 애플리케이션의 웹 작업을 통해 확인할 수 있습니다. 하지만 모든 앱 설정이 올바르게 설정되고 App Insights 사이트 확장이 애플리케이션에 설치되어 있으면 프로파일러가 실행되게 되며, 최근 프로파일링 세션에서 애플리케이션에 대한 적절한 트래픽이 있는지 확인할 수 있습니다.

## <a id="profileondemand"></a> 수동으로 Profiler 트리거

Profiler는 하나의 단추를 클릭하여 수동으로 트리거될 수 있습니다. 웹 성능 테스트를 실행한다고 가정합니다. 웹앱이 부하 상태에서 수행하는 방법을 이해할 수 있는 추적이 필요합니다. 부하 테스트를 실행하는 시점을 알고 있으므로 추적이 캡펴되는 경우를 제어하는 것이 중요하지만 무작위 샘플링 간격은 누락할 수 있습니다.
다음 단계에서는 이 시나리오가 작동되는 방식을 보여줍니다.

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(선택 사항) 1단계: 웹 성능 테스트를 시작하여 웹앱에 트래픽 생성

웹앱에 들어오는 트래픽을 이미 있거나 수동으로 트래픽을 생성하려는 경우 이 섹션을 건너뛰고 2단계를 계속 진행합니다.

Application Insights 포털, **구성 > 성능 테스트**로 이동합니다. 새 성능 테스트를 시작하려면 새로 만들기 단추를 클릭합니다.

![새 성능 테스트 만들기][create-performance-test]

**새 성능 테스트** 창에서 테스트 대상 URL을 구성합니다. 모든 기본 설정을 수락하고 부하 테스트를 실행하기 시작합니다.

![부하 테스트 구성][configure-performance-test]

새 테스트가 먼저 큐에 대기되었다고 표시되고 ‘진행 중’ 상태가 따라옵니다.

![부하 테스트가 제출되고 큐에 대기됨][load-test-queued]

![진행 중인 부하 테스트가 실행됨][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>2단계: 요청 시 프로파일러 시작

부하 테스트가 실행되면 프로파일러를 시작하여 부하를 수신하는 동안 웹앱에서 추적을 캡처할 수 있습니다.
프로파일러 구성 창으로 이동합니다.


### <a name="step-3-view-traces"></a>3단계: 추적 보기

프로파일러 실행이 완료되면 알림에 대한 지침을 따라 성능 페이지 및 추적 보기로 이동합니다.

## <a name="troubleshooting-on-demand-profiler"></a>주문형 프로파일러 문제 해결

경우에 따라 주문형 세션 이후에 프로파일러 시간 제한 오류 메시지가 발생할 수 있습니다.

![프로파일러 시간 제한 오류][profiler-timeout]

이 오류를 표시하는 이유에는 두 가지 이유가 있을 수 있습니다.

1. 주문형 프로파일러 세션에 성공했지만 Application Insights가 수집된 데이터를 처리하는 데 시간이 오래 걸렸습니다. 데이터 처리가 15분 내에 완료되지 않은 경우 포털에는 시간 제한 메시지가 표시됩니다. 잠시 후에 Profiler 추적이 표시됩니다. 지금은 이 경우에 오류 메시지를 무시하세요. 해결하기 위해 적극적으로 노력하고 있습니다.

1. 웹앱에는 주문형 기능이 없는 이전 버전의 Profiler 에이전트가 있습니다. Application Insights 프로필을 이전에 사용하도록 설정한 경우 Profiler 에이전트를 업데이트하여 주문형 기능 사용하기 시작해야 하는 경우가 있습니다.
  
다음 단계를 수행하여 최신 Profiler를 확인하고 설치합니다.

1. App Services 앱 설정으로 이동하고 다음이 설정되었는지를 확인합니다.
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights에 대한 적절한 계측 키로 바꿉니다.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0. 이러한 항목을 설정하지 않은 경우 Application Insights 사용 창으로 이동하여 최신 사이트 확장을 설치합니다.

1. App Services 포털에서 Application Insights 창으로 이동합니다.

    ![App Services 포털에서 Application Insights 사용][enable-app-insights]

1. 다음 페이지에서 '업데이트' 단추가 표시되는 경우 클릭하여 최신 Profiler 에이전트를 설치하는 Application Insights 사이트 확장을 업데이트합니다.

    ![사이트 확장 업데이트][update-site-extension]

1. 그런 다음, **변경**을 클릭하여 Profiler가 켜져 있는지 확인하고 **확인**을 선택하여 변경 내용을 저장합니다.

    ![앱 인사이트 변경 및 저장][change-and-save-appinsights]

1. App Service에 대한 **앱 설정** 탭으로 돌아가서 다음 앱 설정 항목이 설정되었는지 다시 확인합니다.
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: Application Insights에 대한 적절한 계측 키로 바꿉니다.
    * **APPINSIGHTS_PORTALINFO**: ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![프로파일러에 대한 앱 설정][app-settings-for-profiler]

1. 필요에 따라 확장 버전을 확인하고 사용할 수 있는 업데이트가 없는지 확인합니다.

    ![확장 업데이트 확인][check-for-extension-update]

## <a name="next-steps"></a>다음 단계
[Profiler 사용 및 추적 보기](profiler-overview.md ?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png