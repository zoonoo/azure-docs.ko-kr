---
title: Azure Application Insights Profiler 설정 창 사용 | Microsoft Docs
description: Profiler 상태 보기 및 프로파일링 세션 시작
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: f66a23c0562ec9f1987bd119a45b7b767f2dfe46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671633"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler 구성

## <a name="updated-profiler-agent"></a>업데이트된 프로파일러 에이전트
트리거 기능은 프로파일러 에이전트의 버전 2.6 이상에서만 작동합니다. Azure 앱 서비스를 실행 중인 경우 에이전트가 자동으로 업데이트됩니다. 웹 사이트의 Kudu URL로 이동하여 다음과 같이 \DiagnosticServices를 끝에 추가할 경우 실행 중인 에이전트 버전을 확인할 https://yourwebsite.scm.azurewebsites.net/diagnosticservices수 있습니다. 응용 프로그램 인사이트 프로파일러 웹작업은 2.6 이상 버전이 있어야 합니다. 웹 앱을 다시 시작하여 업그레이드를 강제할 수 있습니다. 

VM 또는 클라우드 서비스에서 프로파일러를 실행하는 경우 Windows AZURE 진단(WAD) 확장 버전 16.0.4 이상이 설치되어 있어야 합니다. VM에 로그온하고 이 디렉토리를 찾아WAD 버전을 확인할 수 있습니다: C:\패키지\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4. 디렉터리 이름은 설치된 WAD 버전입니다. Azure VM 에이전트는 새 버전을 사용할 수 있을 때 WAD를 자동으로 업데이트합니다.

## <a name="profiler-settings-page"></a>프로파일러 설정 페이지

Azure 응용 프로그램 인사이트 프로파일러 설정 창을 열려면 응용 프로그램 인사이트 성능 창으로 이동한 다음 **프로파일러 구성** 단추를 선택합니다.

![프로파일러 설정 열기로 연결되는 링크 페이지][configure-profiler-entry]

이렇게 하면 다음과 같은 페이지가 열립니다.

![프로파일러 설정 페이지][configure-profiler-page]

**응용 프로그램 인사이트 프로파일러 구성** 페이지에는 다음과 같은 기능이 있습니다.

| | |
|-|-|
지금 프로필 | 이 Application Insights 인스턴스에 연결된 모든 앱에 대해 프로파일링 세션이 시작됩니다.
트리거 | 프로파일러를 실행하도록 하는 트리거를 구성할 수 있습니다. 
최근 프로파일링 세션 | 이전 프로파일링 세션에 대한 정보가 표시됩니다.

## <a name="profile-now"></a>지금 프로필
이 옵션을 사용하면 필요에 따라 프로파일링 세션을 시작할 수 있습니다. 이 링크를 클릭하면 이 Application Insights 인스턴스로 데이터를 보내는 모든 프로파일러 에이전트가 프로필을 캡처하기 시작합니다. 5~10분 후에 프로필 세션이 아래 목록에 표시됩니다.

사용자가 프로파일러 세션을 수동으로 트리거하려면 애플리케이션 인사이트 구성 요소에 대한 역할에 대한 최소한의 "쓰기" 액세스가 필요합니다. 대부분의 경우 이 액세스 권한을 자동으로 얻을 수 있으며 추가 작업이 필요하지 않습니다. 문제가 있는 경우 추가할 구독 범위 역할은 "응용 프로그램 인사이트 구성 요소 참여자" 역할입니다. [Azure 모니터링을 통해 역할 액세스 제어에 대해 자세히 알아보십시오.](https://docs.microsoft.com/azure/azure-monitor/app/resources-roles-access-control)

## <a name="trigger-settings"></a>트리거 설정
![트리거 설정 플라이아웃][trigger-settings-flyout]

메뉴 모음에서 트리거 버튼을 클릭하면 트리거 설정 상자가 열립니다. CPU 또는 메모리 사용 비율이 설정한 수준에 도달하면 프로파일링을 시작하도록 트리거를 설정할 수 있습니다.

| | |
|-|-|
켜기/끄기 버튼 | On: 이 트리거로 프로파일러를 시작할 수 있습니다. 꺼기: 이 트리거에 의해 프로파일러가 시작되지 않습니다.
메모리 임계값 | 이 메모리 백분율이 사용 중이면 프로파일러가 시작됩니다.
Duration | 프로파일러가 트리거될 때 실행되는 시간을 설정합니다.
Cooldown | 프로파일러가 트리거된 후 메모리 또는 CPU 사용량을 다시 확인하기 전에 프로파일러가 기다릴 시간을 설정합니다.

## <a name="recent-profiling-sessions"></a>최근 프로파일링 세션
페이지의 이 섹션에서는 최근 프로파일링 세션에 대한 정보를 보여 줍니다. 프로파일링 세션은 프로파일러 에이전트가 응용 프로그램을 호스팅하는 컴퓨터 중 하나에서 프로필을 촬영한 기간을 나타냅니다. 행 중 하나를 클릭하여 세션에서 프로필을 열 수 있습니다. 각 세션에 대해 다음을 보여 드립니다.

| | |
|-|-|
에 의해 트리거 | 트리거, 지금 프로필 또는 기본 샘플링을 통해 세션이 시작된 방법. 
앱 이름 | 프로파일이 지정된 응용 프로그램의 이름입니다.
컴퓨터 인스턴스 | 프로파일러 에이전트가 실행한 컴퓨터의 이름입니다.
타임스탬프 | 프로필이 캡처된 시간입니다.
트레이스 (동음이의) | 개별 요청에 첨부된 추적 수입니다.
CPU % | 프로파일러가 실행되는 동안 사용 중인 CPU의 백분율입니다.
메모리 % | 프로파일러가 실행되는 동안 사용 중인 메모리의 백분율입니다.

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a>웹 성능 테스트를 사용하여 응용 프로그램에 대한 트래픽 생성

클릭 한 번으로 Profiler를 수동 트리거할 수 있습니다. 웹 성능 테스트를 실행 중이라고 가정해 보겠습니다. 웹 앱이 부하를 받고 실행되는 방식을 이해하는 데 도움이 되는 추적이 필요합니다. 부하 테스트를 실행할 시점은 알고 있으므로 추적이 캡처되는 시기를 제어할 수 있어야 합니다. 하지만 샘플링 간격이 무작위이면 추적이 캡처되는 시기를 확인하지 못할 수도 있습니다.

다음 섹션에서는 이 시나리오의 작동 방식에 대해 설명합니다.

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>1단계: 웹 성능 테스트를 시작하여 웹 앱으로 트래픽 생성

웹앱에 들어오는 트래픽을 이미 있거나 수동으로 트래픽을 생성하려는 경우 이 섹션을 건너뛰고 2단계를 계속 진행합니다.

1. 응용 프로그램 인사이트 포털에서**성능 테스트** **구성을** > 선택합니다. 

1. 새 성능 테스트를 시작하려면 **새로 만들기** 단추를 선택합니다.

   ![새 성능 테스트 만들기][create-performance-test]

1. **새 성능 테스트** 창에서 테스트 대상 URL을 구성합니다. 모든 기본 설정을 그대로 적용하고 **테스트 실행**을 선택하여 부하 테스트 실행을 시작합니다.

    ![부하 테스트 구성][configure-performance-test]

    새 테스트는 먼저 큐에 대기되었다가 *진행 중* 상태로 전환됩니다.

    ![부하 테스트가 제출되고 큐에 대기됨][load-test-queued]

    ![부하 테스트 진행이 진행 중임][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>2단계: 주문형 프로파일러 세션 시작

1. 부하 테스트가 실행되면 Profiler를 시작하여 부하를 수신하는 동안 웹앱에서 추적을 캡처합니다.

1. **Profiler 구성** 창으로 이동합니다.


### <a name="step-3-view-traces"></a>3단계: 추적 보기

Profiler 실행이 완료되면 알림의 지침에 따라 성능 창으로 이동하여 추적을 확인합니다.

## <a name="next-steps"></a>다음 단계
[Profiler 사용 및 추적 보기](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
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
