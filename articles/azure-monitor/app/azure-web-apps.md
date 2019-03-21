---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 차트 부하 및 응답 시간, 종속성 정보 및 성능에 대한 경고를 설정합니다.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0b2deb30-6ea8-4bc4-8ed0-26765b85149f
ms.service: application-insights
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: mbullwin
ms.openlocfilehash: 92a7c1a45655f8804aa1f81b1a77ebf7cd5197e8
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122168"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service 성능 모니터링
[Azure Portal](https://portal.azure.com)에서 웹앱, 모바일 백 엔드, [Azure App Service](../../app-service/overview.md)의 API 앱에 대한 애플리케이션 성능 모니터링을 설정할 수 있습니다. [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md)는 해당 작업에 대한 원격 분석을 저장하고 분석하는 Application Insights 서비스에 보내는 앱을 계측합니다. 여기서 메트릭 차트 및 검색 도구를 사용하여 문제를 진단하고 성능을 개선하며 사용량 평가할 수 있습니다.

## <a name="runtime-or-build-time"></a>런타임 또는 빌드 시간
다음과 같은 두 가지 방법 중 하나로 앱을 계측하여 모니터링을 구성할 수 있습니다.

* **런타임** -성능 app service이 이미 라이브 상태인 경우 모니터링 확장을 선택할 수 있습니다. 앱을 다시 빌드하거나 설치할 필요가 없습니다. 응답 시간, 성공률, 예외, 종속성 등을 모니터링하는 패키지의 표준 집합을 얻게 됩니다.

* **빌드 시간** - 개발 중인 앱에서 패키지를 설치할 수 있습니다. 이 옵션은 융통성이 뛰어납니다. 동일한 표준 패키지 외에도 원격 분석 데이터를 사용자 지정하거나 고유한 원격 분석을 보내는 코드를 작성할 수 있습니다. 앱 도메인의 의미 체계에 따라 특정 작업 또는 레코드 이벤트를 기록할 수 있습니다. 또한 이렇게 하면 런타임 모니터링은 안정적인 최신 릴리스로로 제한 하는 반면 Sdk 베타를 평가 하도록 선택할 수 있습니다 하는 대로 Application Insights SDK의 최신 버전을 테스트할 수 있습니다.

## <a name="runtime-instrumentation-with-application-insights"></a>Application Insights를 사용 하 여 런타임 계측
Azure에서 현재 app service를 실행 하는 경우 이미 얻게 일부 모니터링: 기본적으로 요청 및 오류 비율입니다. Application Insights을 가져오려면 응답 시간 등 더 많은 호출 종속성 스마트 감지를 모니터링과 강력한 Kusto 쿼리 언어에 대 한 액세스를 추가 합니다. 

1. App Service의 Azure 제어판에서 **Application Insights를 선택**합니다.

    ![설정에서 Application Insights 선택](./media/azure-web-apps/settings-app-insights.png)

   * 이 애플리케이션에 대한 Application Insights 리소스를 설정하지 않은 경우 새 리소스 만들기를 선택합니다. 

     > [!NOTE]
     > **확인**을 클릭하여 새 리소스를 만들 경우 **모니터링 설정을 적용**할지 묻는 메시지가 표시됩니다. **계속**을 선택하면 새 Application Insights 리소스가 App Service에 연결됩니다. 또한 이로 인해 **App Service 다시 시작이 트리거**됩니다. 

     ![웹앱 계측](./media/azure-web-apps/create-resource.png)

2. 사용할 리소스를 지정한 후 Application Insights에서 애플리케이션에 대한 플랫폼별 데이터를 수집하는 방법을 선택할 수 있습니다. 기본적으로 ASP.NET 앱 모니터링은 두 개의 서로 다른 수준의 컬렉션입니다.

    ![플랫폼별 옵션 선택](./media/azure-web-apps/choose-options-new.png)

   * .NET **기본 수집** 수준은 필수적인 단일 인스턴스 APM 기능을 제공합니다.
    
   * .NET **추천 수집** 수준은 다음과 같습니다.
       * CPU, 메모리 및 I/O 사용량 추세를 추가합니다.
       * 요청/종속성 경계 간에 마이크로 서비스를 상호 연결합니다.
       * 사용량 추세를 수집하고, 가용성 결과와 트랜잭션의 상관 관계를 사용하도록 설정합니다.
       * 호스트 프로세스에서 처리되지 않은 예외를 수집합니다.
       * 샘플링을 사용하는 경우 부하 상태에서 APM 메트릭 정확도가 향상됩니다.
    
     .NET core 제공 **컬렉션을 권장** 또는 **비활성** .NET Core 2.0 및 2.1에 대 한 합니다.

3. Application Insights가 설치된 후 **App Service를 계측**합니다.

   페이지 보기 및 사용자 원격 분석에 대해 **클라이언트 쪽 모니터링 사용**을 설정합니다.

    (이 경우 'APPINSIGHTS_JAVASCRIPT_ENABLED' 앱 설정의 존재 여부에 관계없이 기본적으로 .NET Core 앱에 대해 **추천 수집**을 사용하도록 설정됩니다. 현재 .NET Core에서는 클라이언트 쪽 모니터링을 사용하지 않도록 설정하는 세분화된 UI 기반 지원을 사용할 수 없습니다.)
    
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

   1. 원격 분석이 저장, 분석 및 표시되는 Azure에 Application Insights 리소스를 만듭니다.
   2. Application Insights NuGet 패키지를 사용자 코드에 추가하고(없는 경우) 원격 분석을 Azure 리소스로 전송하도록 구성합니다.
2. 개발 컴퓨터(F5)에서 앱을 실행하여 **원격 분석을 테스트**합니다.
3. 일반적인 방법으로 Azure에 **앱을 게시**합니다. 

*다른 Application Insights 리소스에 보내기로 전환하려면 어떻게 해야 합니까?*

* Visual Studio에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **Application Insights 구성**을 선택한 다음, 원하는 리소스를 선택합니다. 새 리소스를 만드는 옵션이 생깁니다. 다시 빌드하고 다시 배포합니다.

## <a name="automate-monitoring"></a>모니터링 자동화

Application Insights로 원격 분석 수집을 사용 하도록 설정 하려면 응용 프로그램 설정에만 설정 해야 합니다.

   ![사용 가능한 Application Insights 설정 사용 하 여 app Service 응용 프로그램 설정](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>응용 프로그램 설정 정의

|앱 설정 이름 |  정의 | 값 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | 런타임 모니터링을 제어 하는 기본 확장입니다. | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  기본 모드에 필수적인 기능 최적의 성능을 보장 하기 위해 사용 됩니다. | `default` 또는 `recommended`입니다. |
|InstrumentationEngine_EXTENSION_VERSION | 제어 하는 경우 이진 재작성 엔진 `InstrumentationEngine` 켜 집니다. 이 설정은 성능에 영향 및 콜드 시작/시작 시간에 영향을 줍니다. | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL 및 Azure 테이블이 텍스트 컨트롤 종속성 호출이 함께 캡처됩니다. 성능 경고:이 위해서는 `InstrumentationEngine`합니다. | `~1` |

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager를 사용 하 여 앱 서비스 응용 프로그램 설정

App Services에 대 한 응용 프로그램 설정을 관리 하 고 사용 하 여 구성할 수 있습니다 [Azure Resource Manager 템플릿](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)합니다. Azure Resource Manager automation을 사용 하 여 또는 기존 리소스의 설정을 수정 하는 것에 대 한 새 App Service 리소스를 배포 하는 경우이 메서드를 사용할 수 있습니다.

App service의 응용 프로그램 설정 JSON의 기본 구조는 다음과 같습니다.

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]

```

예제는 Azure Resource Manager 템플릿 응용 프로그램 설정 사용 하 여이 옵션을 구성 Application Insights에 대 한 [템플릿을](https://github.com/Andrew-MSFT/BasicImageGallery) 유용할 수 있습니다, 터 섹션을 특히 [238 줄](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)합니다.

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights 리소스 및 새로 만든된 App Service에 링크 생성을 자동화 합니다.

구성 된 모든 기본 Application Insights 설정을 사용 하 여 Azure Resource Manager 템플릿 만들기, 사용 하도록 설정 하는 Application Insights를 사용 하 여 새 웹 앱을 만드는 것 처럼 프로세스를 시작 합니다.

선택 **Automation 옵션**

   ![App Service 웹 앱 만들기 메뉴](./media/azure-web-apps/create-web-app.png)

그러면 최신 Azure Resource Manager 템플릿을 구성 하는 모든 필수 설정을 사용 하 여 생성 됩니다.

  ![App Service 웹 앱 템플릿](./media/azure-web-apps/arm-template.png)

> [!NOTE]
> 서식 파일 "기본" 모드에서 응용 프로그램 설정을 생성 됩니다. 이 모드는 성능 최적화 원하는 어떤 기능을 활성화 하도록 템플릿을 수정할 수 있습니다.

## <a name="more-telemetry"></a>추가 원격 분석

* [웹 페이지 로드 데이터](../../azure-monitor/app/javascript.md)
* [사용자 지정 원격 분석](../../azure-monitor/app/api-custom-events-metrics.md)

## <a name="troubleshooting"></a>문제 해결

### <a name="do-i-still-need-to-go-to-extensions---add---application-insights-extension-for-new-app-service-apps"></a>여전히 확장으로 이동 하려면 추가-새 App Service 앱에 대 한 Application Insights 확장 하나요?

아니요, 더 이상 해야 수동으로 확장을 추가 합니다. 설정 블레이드를 통해 Application Insights를 사용 하도록 설정 하면 모니터링을 사용 하려면 필요한 모든 응용 프로그램 설정을 추가 합니다. 왜냐하면 이제 가능한 확장에 의해 이전에 추가한 파일은 이제 [사전](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions) App Service 이미지의 일부로. 파일에 위치한 `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent`합니다.

### <a name="if-runtime-and-build-time-monitoring-are-both-enabled-do-i-end-up-with-duplicate-data"></a>런타임 및 빌드 시간 모니터링은 둘 다 사용 하는 경우 수행 최종적으로 중복 데이터?

아니요, 기본적으로 빌드 시간 모니터링 검색 되 면 데이터 보내기를 중지는 런타임 확장을 통해 모니터링 및 모니터링 구성 빌드 때만 적용 됩니다. 런타임 모니터링을 사용 하지 않도록 설정 여부를 결정은 이러한 세 파일의 검색을 기반으로 합니다.

* Microsoft.ApplicationInsights dll
* Microsoft.ASP.NET.TelemetryCorrelation dll
* System.Diagnostics.DiagnosticSource dll

Visual Studio의 여러 버전의 일부 또는 모든이 파일은 기본적으로 추가 ASP.NET 및 ASP.NET Core에 대 한 Visual Studio 템플릿 파일을 염두에 두는 것이 반드시 합니다. 프로젝트를 만든 경우 기반 템플릿의 하나으로 Application Insights를 명시적으로 설정 하지 않은 경우에 파일 종속성의 존재 런타임 모니터링 활성화 방지 합니다.

### <a name="appinsightsjavascriptenabled-causes-incomplete-html-response-in-net-core-web-applications"></a>APPINSIGHTS_JAVASCRIPT_ENABLED는.NET CORE 웹 애플리케이션에서 불완전한 HTML 응답을 야기합니다.

App Services를 통해 Javascript를 사용하면 html 응답이 차단될 수 있습니다.

* 해결 방법 1: APPINSIGHTS_JAVASCRIPT_ENABLED 애플리케이션 설정을 false로 설정하거나 완전히 제거한 후 다시 시작
* 해결 방법 2: 코드를 통해 sdk를 추가 하 고 확장 제거 (이 구성을 사용 하 여 Profiler 및 스냅숏 디버거 작동 하지 않음)

이 문제를 추적 하려면로 이동 [불완전 한 HTML 응답을 유발 하는 Azure 확장](https://github.com/Microsoft/ApplicationInsights-Home/issues/277)합니다.

.NET Core에서 현재 **지원되지 않는** 항목은 다음과 같습니다.

* 자체 포함 배포
* .NET Framework를 대상으로 하는 앱
* .NET Core 2.2 애플리케이션

> [!NOTE]
> .NET Core 2.0 및.NET Core 2.1이 지원됩니다. .NET Core 2.2 지원이 추가되면 이 문서가 업데이트됩니다.

## <a name="next-steps"></a>다음 단계
* [라이브 앱에서 프로파일러를 실행합니다](../../azure-monitor/app/profiler.md).
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights로 Azure Functions 모니터링
* [Azure 진단을 사용](../../azure-monitor/platform/diagnostics-extension-to-application-insights.md)하여 Application Insights에 보냅니다.
* [서비스 상태 메트릭을 모니터링](../../azure-monitor/platform/data-collection.md)하여 서비스를 사용 가능하며 응답할 수 있는 상태로 유지합니다.
* 작업 이벤트가 발생하거나 메트릭이 임계값을 초과할 때마다 [경고 알림을 수신](../../azure-monitor/platform/alerts-overview.md)합니다.
* [JavaScript 앱 및 웹 페이지용 Application Insights](../../azure-monitor/app/javascript.md)를 사용하여 웹 페이지로 이동하는 브라우저에서 클라이언트 원격 분석을 가져옵니다.
* [가용성 웹 테스트를 설정](../../azure-monitor/app/monitor-web-app-availability.md) 합니다.