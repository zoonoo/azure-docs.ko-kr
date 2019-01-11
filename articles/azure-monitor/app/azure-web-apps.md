---
title: Azure App Service 성능 모니터링 | Microsoft Docs
description: Azure 웹앱에 대한 애플리케이션 성능 모니터링입니다. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: mbullwin
ms.openlocfilehash: a5aea15c2563a81f4e915baa1b332beda64a1a70
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/02/2019
ms.locfileid: "53970970"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service 성능 모니터링
[Azure Portal](https://portal.azure.com)에서 웹앱, 모바일 백 엔드, [Azure App Service](../../app-service/overview.md)의 API 앱에 대한 애플리케이션 성능 모니터링을 설정할 수 있습니다. [Azure Application Insights](../../application-insights/app-insights-overview.md)는 해당 작업에 대한 원격 분석을 저장하고 분석하는 Application Insights 서비스에 보내는 앱을 계측합니다. 여기서 메트릭 차트 및 검색 도구를 사용하여 문제를 진단하고 성능을 개선하며 사용량 평가할 수 있습니다.

## <a name="run-time-or-build-time"></a>실행 시간 또는 빌드 시간
다음과 같은 두 가지 방법 중 하나로 앱을 계측하여 모니터링을 구성할 수 있습니다.

* **런타임** - 웹앱이 이미 라이브 상태인 경우 성능 모니터링 확장을 선택할 수 있습니다. 앱을 다시 빌드하거나 설치할 필요는 없습니다. 응답 시간, 성공률, 예외, 종속성 등을 모니터링하는 패키지의 표준 집합을 얻게 됩니다. 
* **빌드 시간** - 개발 중인 앱에서 패키지를 설치할 수 있습니다. 이 옵션은 융통성이 뛰어납니다. 동일한 표준 패키지 외에도 원격 분석 데이터를 사용자 지정하거나 고유한 원격 분석을 보내는 코드를 작성할 수 있습니다. 앱 도메인의 의미 체계에 따라 특정 작업 또는 레코드 이벤트를 기록할 수 있습니다. 

## <a name="run-time-instrumentation-with-application-insights"></a>Application Insights를 사용하여 시간 계측 실행
Azure에서 웹앱을 이미 실행 중인 경우 이미 일부 요청 및 오류 비율을 모니터링하고 있습니다. Application Insights를 추가하여 응답 시간, 종속성 호출 모니터링, 스마트 검색, 강력한 Log Analytics 쿼리 언어 등 더 많은 것을 얻을 수 있습니다. 

1. 웹앱의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

    > [!NOTE]
    > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 웹앱에 연결됩니다. 또한 이로 인해 **웹앱 다시 시작이 트리거**됩니다. 

    ![웹앱 계측](./media/azure-web-apps/create-resource.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options.png)

3. Application Insights를 설치한 후 **웹앱을 계측**합니다.

   페이지 보기 및 사용자 원격 분석을 위해 **클라이언트 쪽 모니터링을 사용하도록 설정**합니다.

   * 설정 &gt; 애플리케이션 설정 선택
   * 앱 설정 아래에서 새로운 키 값 쌍을 추가합니다.

    키: `APPINSIGHTS_JAVASCRIPT_ENABLED`

    값: `true`
   * 설정을 **저장**하고 앱을 **다시 시작**합니다.
4. **설정** > **Application Insights** > **Application Insights에서 자세히 보기**를 선택하여 앱 모니터링 데이터를 살펴봅니다.

나중에 원하는 경우 Application Insights로 앱을 빌드할 수 있습니다.

*Application Insights를 제거하거나 다른 리소스에 보내기로 전환하려면 어떻게 해야 합니까?*

* Azure에서 웹앱 제어 블레이드를 열고 설정 아래에서 **Application Insights**를 엽니다. 맨 위에 있는 **사용 안 함**을 클릭하거나 **리소스 변경** 섹션에서 새 리소스를 선택하여 Application Insights를 해제할 수 있습니다.

## <a name="build-the-app-with-application-insights"></a>Application Insights로 앱 빌드
Application Insights는 앱에 SDK를 설치하여 더 자세한 원격 분석을 제공할 수 있습니다. 특히 추적 로그를 수집하고 [사용자 지정 원격 분석을 작성](../../azure-monitor/app/api-custom-events-metrics.md)하고 보다 자세한 예외 보고서를 가져올 수 있습니다.

1. **Visual Studio**(2013 업데이트 2 이상)에서 프로젝트를 위한 Application Insights를 구성합니다.

    웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 **추가 > Application Insights** 또는 **프로젝트** > **Application Insights** > **Application Insights 구성**을 선택합니다.

    ![웹 프로젝트를 마우스 오른쪽 단추로 클릭하고 Application Insights 추가 또는 구성 선택](./media/azure-web-apps/03-add.png)

    로그인이 요청되면 자신의 Azure 계정에 대한 자격 증명을 사용합니다.

    작업에는 두 가지 효과가 있습니다.

   1. 원격 분석을 저장하고, 분석하고 표시할 수 있는 Azure에서 Application Insights 리소스를 만듭니다.
   2. Application Insights NuGet 패키지를 사용자 코드에 추가하고(없는 경우) 원격 분석을 Azure 리소스로 전송하도록 구성합니다.
2. 개발 컴퓨터(F5)에서 앱을 실행하여 **원격 분석을 테스트**합니다.
3. 일반적인 방법으로 Azure에 **앱을 게시**합니다. 

*다른 Application Insights 리소스에 보내기로 전환하려면 어떻게 해야 합니까?*

* Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **Application Insights 구성**을 선택한 다음, 원하는 리소스를 선택합니다. 새 리소스를 만드는 옵션이 생깁니다. 다시 빌드하고 다시 배포합니다.

## <a name="more-telemetry"></a>추가 원격 분석

* [웹 페이지 로드 데이터](../../azure-monitor/app/javascript.md)
* [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="video"></a>비디오

> [!VIDEO https://channel9.msdn.com/events/Connect/2016/100/player]

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](../../application-insights/app-insights-profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../../azure-monitor/platform/data-collection.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../../azure-monitor/platform/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](../../azure-monitor/app/javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](../../azure-monitor/app/monitor-web-app-availability.md) 합니다.

